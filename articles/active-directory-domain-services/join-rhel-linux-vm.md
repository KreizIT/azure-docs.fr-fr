---
title: Joindre une machine virtuelle RHEL à Azure AD Domain Services | Microsoft Docs
description: Découvrez comment configurer une machine virtuelle Red Hat Enterprise Linux et comment la joindre à un domaine managé Azure AD Domain Services.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 16100caa-f209-4cb0-86d3-9e218aeb51c6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/13/2020
ms.author: justinha
ms.openlocfilehash: c883e3e5cb82107b22bf06176092ec9cfae46d23
ms.sourcegitcommit: 096e7972e2a1144348f8d648f7ae66154f0d4b39
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2021
ms.locfileid: "112516690"
---
# <a name="join-a-red-hat-enterprise-linux-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain"></a>Joindre une machine virtuelle Red Hat Enterprise Linux à un domaine managé par Azure Active Directory Domain Services

Pour permettre aux utilisateurs de se connecter à des machines virtuelles dans Azure en utilisant un ensemble unique d’informations d’identification, vous pouvez joindre les machines virtuelles à un domaine managé Azure Active Directory Domain Services (Azure AD DS). Quand vous joignez une machine virtuelle à un domaine managé Azure AD DS, les comptes d’utilisateurs et les informations d’identification du domaine peuvent être utilisés pour se connecter aux serveurs et les gérer. Les appartenances aux groupes du domaine managé sont également appliquées pour vous permettre de contrôler l’accès aux fichiers ou aux services sur la machine virtuelle.

Cet article montre comment joindre une machine virtuelle Red Hat Enterprise Linux (RHEL) à un domaine managé.

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce tutoriel, vous avez besoin des ressources et des privilèges suivants :

* Un abonnement Azure actif.
    * Si vous n’avez pas d’abonnement Azure, [créez un compte](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un locataire Azure Active Directory associé à votre abonnement, synchronisé avec un annuaire local ou un annuaire cloud uniquement.
    * Si nécessaire, [créez un locataire Azure Active Directory][create-azure-ad-tenant] ou [associez un abonnement Azure à votre compte][associate-azure-ad-tenant].
* Un domaine managé Azure Active Directory Domain Services activé et configuré dans votre locataire Azure AD.
    * Si nécessaire, le premier tutoriel [crée et configure un domaine managé Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Un compte d’utilisateur membre du domaine managé.
* Des noms de machine virtuelle Linux uniques d’un maximum de 15 caractères pour éviter les noms tronqués susceptibles de générer des conflits dans Active Directory.

## <a name="create-and-connect-to-a-rhel-linux-vm"></a>Créer une machine virtuelle Linux RHEL et s’y connecter

Si vous disposez d’une machine virtuelle Linux RHEL dans Azure, connectez-vous-y en utilisant SSH, puis passez à l’étape suivante pour [commencer à configurer la machine virtuelle](#configure-the-hosts-file).

Si vous avez besoin de créer une machine virtuelle Linux RHEL ou si vous souhaitez créer une machine virtuelle de test dans le cadre de cet article, vous pouvez employer l’une des méthodes suivantes :

* [Azure portal](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

Au moment de créer la machine virtuelle, faites attention aux paramètres de réseau virtuel et veillez à ce que la machine virtuelle puisse communiquer avec le domaine managé :

* Déployez la machine virtuelle dans le réseau virtuel où vous avez activé Azure Active Directory Domain Services ou dans un réseau virtuel appairé.
* Déployez la machine virtuelle dans un sous-réseau différent de celui de votre instance Azure Active Directory Domain Services.

Une fois la machine virtuelle déployée, suivez les étapes pour vous connecter à la machine virtuelle avec SSH.

## <a name="configure-the-hosts-file"></a>Configurer le fichier hosts

Pour que le nom d’hôte de la machine virtuelle soit correctement configuré pour le domaine managé, modifiez le fichier */etc/hosts* et définissez le nom d’hôte :

```console
sudo vi /etc/hosts
```

Dans le fichier *hosts*, mettez à jour l’adresse *localhost*. Dans l’exemple suivant :

* *aaddscontoso.com* est le nom de domaine DNS de votre domaine managé.
* *rhel* est le nom d’hôte de votre machine virtuelle RHEL que vous joignez au domaine managé.

Mettez à jour ces noms avec vos propres valeurs :

```console
127.0.0.1 rhel rhel.aaddscontoso.com
```

Quand vous avez terminé, enregistrez et quittez le fichier *hosts* à l’aide de la commande `:wq` de l’éditeur.

## <a name="install-required-packages"></a>Installer les packages nécessaires

La machine virtuelle a besoin de packages supplémentaires pour être jointe au domaine managé. Pour installer et configurer ces packages, mettez à jour et installez les outils de jonction de domaine à l’aide de `yum`. Il existe certaines différences entre RHEL 7.x et RHEL 6.x. Veillez donc à utiliser les commandes appropriées pour votre version de distribution dans les autres sections de cet article.

**RHEL 7**

```console
sudo yum install realmd sssd krb5-workstation krb5-libs oddjob oddjob-mkhomedir samba-common-tools
```

**RHEL 6**

```console
sudo yum install adcli sssd authconfig krb5-workstation
```

## <a name="join-vm-to-the-managed-domain"></a>Joindre la machine virtuelle au domaine managé

Maintenant que les packages nécessaires sont installés sur la machine virtuelle, joignez celle-ci au domaine managé. Là encore, utilisez les étapes appropriées pour votre version de distribution de RHEL.

### <a name="rhel-7"></a>RHEL 7

1. Utilisez la commande `realm discover` pour découvrir le domaine managé. L’exemple suivant découvre le domaine *AADDSCONTOSO.COM*. Spécifiez votre propre nom de domaine managé TOUT EN MAJUSCULES :

    ```console
    sudo realm discover AADDSCONTOSO.COM
    ```

   Si la commande `realm discover` ne trouve pas votre domaine managé, examinez les étapes de dépannage suivantes :

    * Vérifiez que le domaine est accessible à partir de la machine virtuelle. Essayez `ping aaddscontoso.com` pour voir si une réponse positive est retournée.
    * Vérifiez que la machine virtuelle est déployée dans le réseau virtuel où le domaine managé est disponible ou dans un réseau virtuel appairé.
    * Vérifiez que les paramètres de serveur DNS du réseau virtuel ont été mis à jour pour pointer vers les contrôleurs de domaine du domaine managé.

1. À présent, initialisez Kerberos à l’aide de la commande `kinit`. Spécifiez un utilisateur membre du domaine managé. Si nécessaire, [ajoutez un compte d’utilisateur à un groupe dans Azure AD](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md).

    Là encore, le nom de domaine managé doit être entré TOUT EN MAJUSCULES. Dans l’exemple suivant, le compte nommé `contosoadmin@aaddscontoso.com` est utilisé pour initialiser Kerberos. Entrez votre propre compte d’utilisateur qui fait partie du domaine managé :

    ```console
    kinit contosoadmin@AADDSCONTOSO.COM
    ```

1. Enfin, joignez la machine virtuelle au domaine managé à l’aide de la commande `realm join`. Utilisez le même compte d’utilisateur faisant partie du domaine managé et spécifié dans la commande précédente `kinit`, à savoir `contosoadmin@AADDSCONTOSO.COM` :

    ```console
    sudo realm join --verbose AADDSCONTOSO.COM -U 'contosoadmin@AADDSCONTOSO.COM'
    ```

La jonction de la machine virtuelle au domaine managé prend quelques instants. L’exemple de sortie suivant montre que la machine virtuelle a bien été jointe au domaine managé :

```output
Successfully enrolled machine in realm
```

### <a name="rhel-6"></a>RHEL 6

1. Utilisez la commande `adcli info` pour découvrir le domaine managé. L’exemple suivant découvre le domaine *ADDDSCONTOSO.COM*. Spécifiez votre propre nom de domaine managé TOUT EN MAJUSCULES :

    ```console
    sudo adcli info aaddscontoso.com
    ```

   Si la commande `adcli info` ne trouve pas votre domaine managé, examinez les étapes de dépannage suivantes :

    * Vérifiez que le domaine est accessible à partir de la machine virtuelle. Essayez `ping aaddscontoso.com` pour voir si une réponse positive est retournée.
    * Vérifiez que la machine virtuelle est déployée dans le réseau virtuel où le domaine managé est disponible ou dans un réseau virtuel appairé.
    * Vérifiez que les paramètres de serveur DNS du réseau virtuel ont été mis à jour pour pointer vers les contrôleurs de domaine du domaine managé.

1. Commencez par joindre le domaine à l’aide de la commande `adcli join`. Cette commande crée également le fichier Keytab pour l’authentification de la machine. Utilisez un compte d’utilisateur membre du domaine managé.

    ```console
    sudo adcli join aaddscontoso.com -U contosoadmin
    ```

1. Configurez maintenant le fichier `/ect/krb5.conf`, et créez les fichiers `/etc/sssd/sssd.conf` pour utiliser le domaine Active Directory `aaddscontoso.com`.
   Veillez à ce que `AADDSCONTOSO.COM` soit remplacé par votre propre nom de domaine :

    Ouvrez le fichier `/ect/krb5.conf` dans un éditeur :

    ```console
    sudo vi /etc/krb5.conf
    ```

    Mettez à jour le fichier `krb5.conf` pour qu’il corresponde à l’exemple suivant :

    ```console
    [logging]
     default = FILE:/var/log/krb5libs.log
     kdc = FILE:/var/log/krb5kdc.log
     admin_server = FILE:/var/log/kadmind.log
    
    [libdefaults]
     default_realm = AADDSCONTOSO.COM
     dns_lookup_realm = true
     dns_lookup_kdc = true
     ticket_lifetime = 24h
     renew_lifetime = 7d
     forwardable = true
    
    [realms]
     AADDSCONTOSO.COM = {
     kdc = AADDSCONTOSO.COM
     admin_server = AADDSCONTOSO.COM
     }
    
    [domain_realm]
     .AADDSCONTOSO.COM = AADDSCONTOSO.COM
     AADDSCONTOSO.COM = AADDSCONTOSO.COM
    ```
    
   Créer le fichier `/etc/sssd/sssd.conf` :
    
    ```console
    sudo vi /etc/sssd/sssd.conf
    ```

    Mettez à jour le fichier `sssd.conf` pour qu’il corresponde à l’exemple suivant :

    ```console
    [sssd]
     services = nss, pam, ssh, autofs
     config_file_version = 2
     domains = AADDSCONTOSO.COM
    
    [domain/AADDSCONTOSO.COM]
    
     id_provider = ad
    ```

1. Assurez-vous que les autorisations `/etc/sssd/sssd.conf` correspondent à 600 et qu'elles appartiennent à l'utilisateur racine :

    ```console
    sudo chmod 600 /etc/sssd/sssd.conf
    sudo chown root:root /etc/sssd/sssd.conf
    ```

1. Utilisez `authconfig` pour informer la machine virtuelle de l’intégration AD Linux :

    ```console
    sudo authconfig --enablesssd --enablesssdauth --update
    ```

1. Démarrez et activez le service sssd :

    ```console
    sudo service sssd start
    sudo chkconfig sssd on
    ```

Si votre machine virtuelle ne peut pas venir à bout du processus de jonction de domaine, vérifiez que le groupe de sécurité réseau de la machine virtuelle autorise le trafic Kerberos sortant sur le port TCP + UDP 464 vers le sous-réseau du réseau virtuel de votre domaine managé.

Vérifiez maintenant que vous pouvez interroger les informations AD de l'utilisateur via `getent`.

```console
sudo getent passwd contosoadmin
```

## <a name="allow-password-authentication-for-ssh"></a>Autoriser l’authentification par mot de passe pour SSH

Par défaut, les utilisateurs ne peuvent se connecter à une machine virtuelle qu’avec l’authentification par clé publique SSH. L’authentification par mot de passe échoue. Quand vous joignez la machine virtuelle à un domaine managé, ces comptes de domaine doivent utiliser l’authentification par mot de passe. Mettez à jour la configuration SSH pour autoriser l’authentification par mot de passe comme suit.

1. Ouvrez le fichier *sshd_conf* avec un éditeur :

    ```console
    sudo vi /etc/ssh/sshd_config
    ```

1. Mettez à jour la ligne pour *PasswordAuthentication* sur *yes* :

    ```console
    PasswordAuthentication yes
    ```

    Quand vous avez terminé, enregistrez et quittez le fichier *sshd_conf* à l’aide de la commande `:wq` de l’éditeur.

1. Pour appliquer les changements et permettre aux utilisateurs de se connecter avec un mot de passe, redémarrez le service SSH pour votre version de distribution de RHEL :

   **RHEL 7**

    ```console
    sudo systemctl restart sshd
    ```

   **RHEL 6**

    ```console
    sudo service sshd restart
    ```

## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>Accorder les privilèges sudo au groupe « Administrateurs du contrôleur de domaine AAD »

Pour accorder des privilèges d’administration aux membres du groupe *AAD DC Administrators* sur la machine virtuelle RHEL, ajoutez une entrée au fichier */etc/sudoers*. Une fois ajoutés, les membres du groupe *AAD DC Administrators* peuvent utiliser la commande `sudo` sur la machine virtuelle RHEL.

1. Ouvrez le fichier *sudoers* pour le modifier :

    ```console
    sudo visudo
    ```

1. Ajoutez l’entrée suivante à la fin du fichier */etc/sudoers*. Étant donné que le nom de groupe *Administrateurs du contrôleur de domaine AAD* contient un espace, incluez-y une barre oblique inverse en guise de caractère d’échappement. Ajoutez votre propre nom de domaine, par exemple *aaddscontoso.com* :

    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators@aaddscontoso.com ALL=(ALL) NOPASSWD:ALL
    ```

    Quand vous avez terminé, enregistrez et quittez l’éditeur à l’aide de la commande `:wq` de l’éditeur.

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Se connecter à la machine virtuelle à l’aide d’un compte de domaine

Pour vérifier que la machine virtuelle a bien été jointe au domaine managé, démarrez une nouvelle connexion SSH en utilisant un compte d’utilisateur du domaine. Vérifiez qu’un répertoire de base a été créé et que l’appartenance au groupe du domaine est appliquée.

1. Créez une connexion SSH à partir de votre console. Utilisez un compte de domaine qui appartient au domaine managé en utilisant la commande `ssh -l`, comme `contosoadmin@aaddscontoso.com`, puis entrez l’adresse de votre machine virtuelle, par exemple *rhel.aaddscontoso.com*. Si vous utilisez Azure Cloud Shell, utilisez l’adresse IP publique de la machine virtuelle plutôt que le nom DNS interne.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com rhel.aaddscontoso.com
    ```

1. Une fois connecté à la machine virtuelle, vérifiez que le répertoire de base a bien été initialisé :

    ```console
    pwd
    ```

    Vous devez vous trouver dans le répertoire de base */home* et votre propre répertoire doit correspondre au compte d’utilisateur.

1. Vérifiez maintenant que les appartenances aux groupes sont correctement résolues :

    ```console
    id
    ```

    Vos appartenances aux groupes du domaine managé doivent s’afficher.

1. Si vous vous êtes connecté à la machine virtuelle en tant que membre du groupe *Administrateurs AAD DC*, vérifiez que vous pouvez bien utiliser la commande `sudo` :

    ```console
    sudo yum update
    ```

## <a name="next-steps"></a>Étapes suivantes

Si vous avez des difficultés à connecter la machine virtuelle au domaine managé ou à vous connecter avec un compte de domaine, consultez [Résoudre les problèmes de jonction à un domaine](join-windows-vm.md#troubleshoot-domain-join-issues).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
