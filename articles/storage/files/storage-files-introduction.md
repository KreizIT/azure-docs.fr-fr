---
title: Présentation d’Azure Files | Microsoft Docs
description: Vue d’ensemble d’Azure Files, un service qui vous permet de créer et d’utiliser les partages de fichiers réseau dans le cloud à l’aide des protocoles SMB ou NFS.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 07/23/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: c47d68dbaef7cbd154a0ac9af7ad582c1e94b640
ms.sourcegitcommit: 98e126b0948e6971bd1d0ace1b31c3a4d6e71703
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2021
ms.locfileid: "114673952"
---
# <a name="what-is-azure-files"></a>Qu’est-ce qu’Azure Files ?
Azure Files offre des partages de fichiers pleinement managés dans le cloud qui sont accessibles via le [protocole SMB (Server Message Block)](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) standard ou le [protocole NFS (Network File System)](https://en.wikipedia.org/wiki/Network_File_System) standard. Les partages de fichiers Azure Files peuvent être montés simultanément par des déploiements dans le cloud ou en local. Les partages de fichiers Azure via SMB sont accessibles à partir des clients Windows, Linux et macOS. Les partages Azure Files via NFS sont accessibles à partir des clients Linux ou macOS. En outre, les partages de fichiers Azure via SMB peuvent être mis en cache sur les serveurs Windows à l’aide d’[Azure File Sync](../file-sync/file-sync-introduction.md) pour un accès rapide à proximité de l’endroit où les données sont utilisées.

Voici quelques vidéos sur les cas d’utilisation courants d’Azure Files :
* [Remplacer votre serveur de fichiers par un partage de fichiers Azure serverless](https://sec.ch9.ms/ch9/3358/0addac01-3606-4e30-ad7b-f195f3ab3358/ITOpsTalkAzureFiles_high.mp4)
* [Bien démarrer avec les conteneurs de profils FSLogix sur Azure Files dans Windows Virtual Desktop en tirant parti de l’authentification AD](https://www.youtube.com/embed/9S5A1IJqfOQ)

## <a name="why-azure-files-is-useful"></a>Pourquoi Azure Files est-il utile ?
Les partages de fichiers Azure peuvent être utilisés pour :

* **Remplacer ou complémenter les serveurs de fichiers locaux** :  
    Azure Files peut être utilisé pour remplacer complètement ou compléter les serveurs de fichiers locaux traditionnels ou les périphériques NAS. Les systèmes d’exploitation courants tels que Windows, Mac OS et Linux peuvent monter directement des partages Azure Files, n’importe où dans le monde. Les partages de fichiers Azure via SMB peuvent également être répliqués à l’aide d’Azure File Sync vers des serveurs Windows, localement ou dans le cloud, pour une mise en cache performante et distribuée des données là où elles sont utilisées. Avec la mise à disposition récente de l’[authentification AD pour Azure Files](storage-files-active-directory-overview.md), les partages de fichiers Azure via SMB peuvent continuer à fonctionner avec AD, hébergé localement, pour le contrôle d’accès. 

* **Migration « lift-and-shift » des applications** :  
    Azure Files facilite le passage dans le cloud des applications qui exigent un partage de fichiers pour stocker les données utilisateur ou l’application de fichier. Azure Files permet une transition classique, où l’application et ses données sont déplacées vers Azure, ainsi qu’une transition hybride, où les données d’application sont déplacées vers Azure Files et l’application s’exécute toujours en local. 

* **Simplifier le développement cloud** :  
    Azure Files permet aussi de simplifier de plusieurs façons les nouveaux projets de développement cloud. Par exemple :
    * **Paramètres d’application partagés** :  
        Pour les applications distribuées, les fichiers de configuration sont souvent centralisés à un emplacement accessible par différentes instances d’application. Les instances de l’application peuvent charger leur configuration via l’API REST File et l’utilisateur peut y accéder en fonction des besoins en montant le partage SMB localement.

    * **Partage de diagnostic** :  
        Un partage de fichiers Azure est un emplacement pratique où les applications cloud peuvent écrire leurs journaux d’activité, mesures et images mémoire. Les journaux d’activité peuvent être écrits par les instances de l’application via l’API REST File, et les développeurs peuvent y accéder en montant le partage de fichiers sur leur ordinateur local. Cela permet une grande souplesse, car les développeurs peuvent adopter le développement cloud sans abandonner les outils existants qu’ils apprécient.

    * **Développement / test / débogage** :  
        Lorsque les développeurs ou les administrateurs travaillent sur des machines virtuelles situées dans le cloud, ils ont souvent besoin de différents outils ou utilitaires. La copie de ces outils et utilitaires vers chaque machine virtuelle peut prendre beaucoup de temps. En montant un partage de fichiers Azure localement sur les machines virtuelles, un développeur et un administrateur peuvent accéder rapidement à leurs outils et utilitaires, car aucune copie n’est requise.
* **Conteneurisation** :  
    Les partages de fichiers Azure peuvent être utilisés en tant que volumes persistants pour les conteneurs avec état. Les conteneurs fournissent des fonctionnalités à « générer une fois et exécuter partout » qui permettent aux développeurs d’accélérer l’innovation. Pour les conteneurs qui accèdent aux données brutes à chaque démarrage, un système de fichiers partagé est nécessaire pour permettre à ces conteneurs d’accéder au système de fichiers, quelle que soit l’instance sur laquelle ils s’exécutent.

## <a name="key-benefits"></a>Principaux avantages
* **Accès partagé**. Les partages de fichiers Azure prennent en charge les protocoles standard SMB et NFS. Cela signifie que vous pouvez facilement remplacer vos partages de fichiers locaux par des partages de fichiers Azure, sans vous soucier de la compatibilité des applications. Être en mesure de partager un système de fichiers sur plusieurs ordinateurs et applications/instances peut s’avérer très avantageux avec Azure Files pour les applications qui nécessitent des capacités de partage. 
* **Gestion intégrale**. Les partages de fichiers Azure peuvent être créés sans avoir à gérer le matériel ou un système d’exploitation. Cela signifie que vous n’êtes pas obligé de gérer les mises à jour correctives du système d’exploitation du serveur avec des mises à niveau de sécurité critiques ou de remplacer les disques durs défaillants.
* **Écriture de scripts et outils**. Vous pouvez utiliser les applets de commande PowerShell et Azure CLI pour créer, monter et gérer les partages de fichiers Azure dans le cadre de l’administration des applications Azure. Vous pouvez créer et gérer les partages de fichiers Azure à l’aide du portail Azure et de l’Explorateur Stockage Azure. 
* **Résilience**. Azure Files a été entièrement conçu de manière à être toujours disponible. L’utilisation d’Azure Files au lieu des partages de fichiers locaux signifie que vous n’avez plus à vous soucier des pannes de courant ou des problèmes de réseau. 
* **Programmabilité familière**. Les applications exécutées dans Azure peuvent accéder aux données dans le partage via les [API d’E/S du système](/dotnet/api/system.io.file) de fichier. Les développeurs peuvent ainsi tirer profit de leur code et compétences actuels pour migrer les applications existantes. En plus des API d’E/S du système, vous pouvez utiliser les [bibliothèques de client de stockage Azure](/previous-versions/azure/dn261237(v=azure.100)) ou l’[API REST de stockage Azure](/rest/api/storageservices/file-service-rest-api).

## <a name="next-steps"></a>Étapes suivantes
* [Planifier un déploiement Azure Files](storage-files-planning.md)
* [Création d’un partage de fichiers Azure](storage-how-to-create-file-share.md)
* [Connecter et monter un partage SMB sur Windows](storage-how-to-use-files-windows.md)
* [Connecter et monter un partage SMB sur Linux](storage-how-to-use-files-linux.md)
* [Connecter et monter un partage SMB sur macOS](storage-how-to-use-files-mac.md)
* [Connecter et monter un partage NFS sur Linux](storage-files-how-to-mount-nfs-shares.md)
