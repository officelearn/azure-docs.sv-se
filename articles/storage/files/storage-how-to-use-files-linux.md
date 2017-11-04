---
title: "Använda Azure filer med Linux | Microsoft Docs"
description: "Lär dig mer om att montera en filresurs på Azure över SMB på Linux."
services: storage
documentationcenter: na
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 6edc37ce-698f-4d50-8fc1-591ad456175d
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: renash
ms.openlocfilehash: 192680efe07368666c5a9d037549c7686189d0b0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-files-with-linux"></a>Använda Azure filer med Linux
[Azure Files](storage-files-introduction.md) är Microsofts lättanvända filsystem i molnet. Azure-filresurser kan monteras i Linux-distributioner som använder den [cifs-verktyg för webbplatsuppgradering paketet](https://wiki.samba.org/index.php/LinuxCIFS_utils) från den [Samba projekt](https://www.samba.org/). Den här artikeln beskrivs två sätt att montera en filresurs i Azure: på begäran med den `mount` kommandot och på Start genom att skapa en post i `/etc/fstab`.

> [!NOTE]  
> För att montera en filresurs på Azure utanför Azure stöder region som den är värd för, till exempel lokalt eller i en annan Azure region Operativsystemet kryptering funktionerna i SMB 3.0. Krypteringsfunktionerna för SMB 3.0 för Linux introducerades i 4.11 kernel. Den här funktionen gör det möjligt för montering av Azure-filresursen från lokala eller en annan Azure-region. Vid tidpunkten för publicering har den här funktionen anpassats till Ubuntu från 16.04 och senare.


## <a name="prerequisities-for-mounting-an-azure-file-share-with-linux-and-the-cifs-utils-package"></a>Prerequisities för att montera en Azure-fil dela med Linux- och cifs-verktyg för webbplatsuppgradering-paket
* **Välj en Linux-distributionsplats som kan ha cifs-verktyg för webbplatsuppgradering paketet installeras**: Microsoft rekommenderar följande Linux-distributioner i Azure-avbildning galleriet:

    * Ubuntu Server 14.04 +
    * RHEL 7 +
    * CentOS 7 +
    * Debian 8
    * openSUSE 13.2 +
    * SUSE Linux Enterprise Server 12

* <a id="install-cifs-utils"></a>**Cifs-verktyg för webbplatsuppgradering paketet har installerats**: den cifs-verktyg för webbplatsuppgradering kan installeras med hjälp av package manager på Linux-distribution av ditt val. 

    På **Ubuntu** och **Debian-baserade** distributioner, använda den `apt-get` Pakethanteraren:

    ```
    sudo apt-get update
    sudo apt-get install cifs-utils
    ```

    På **RHEL** och **CentOS**, använda den `yum` Pakethanteraren:

    ```
    sudo yum install samba-client samba-common cifs-utils
    ```

    På **openSUSE**, använda den `zypper` Pakethanteraren:

    ```
    sudo zypper install samba*
    ```

    Använd lämplig package manager på andra distributioner eller [kompilera från källan](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download).

* **Besluta om filen/katalogen behörigheterna för den monterade resursen**: vi att använda 0777 i exemplen nedan, för att ge Läs-, Skriv- och körbehörighet till alla användare. Du kan ersätta den med andra [chmod-behörigheter](https://en.wikipedia.org/wiki/Chmod) enligt önskemål. 

* **Lagringskontonamn**: Om du vill montera en Azure-filresurs behöver du namnet på lagringskontot.

* **Lagringskontonyckel**: Om du vill montera en Azure-filresurs behöver du den primära (eller sekundära) lagringsnyckeln. SAS-nycklar stöds inte för montering.

* **Kontrollera att port 445 är öppen**: SMB kommunicerar via TCP-port 445 - Kontrollera att se om brandväggen inte blockerar TCP-portarna 445 från klientdatorn.

## <a name="mount-the-azure-file-share-on-demand-with-mount"></a>Montera filen Azure dela på begäran med`mount`
1. **[Installera paketet cifs-verktyg för webbplatsuppgradering för Linux-distribution](#install-cifs-utils)**.

2. **Skapa en mapp för monteringspunkten**: Detta kan göras var som helst i filsystemet.

    ```
    mkdir mymountpoint
    ```

3. **Använd mount-kommando för att montera filresursen Azure**: Kom ihåg att ersätta `<storage-account-name>`, `<share-name>`, och `<storage-account-key>` med rätt information.

    ```
    sudo mount -t cifs //<storage-account-name>.file.core.windows.net/<share-name> ./mymountpoint -o vers=2.1,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
    ```

> [!Note]  
> När du är klar använder Azure-filresurs, kan du använda `sudo umount ./mymountpoint` demontera resursen.

## <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>Skapa en beständig monteringspunkt för Azure-filresursen med`/etc/fstab`
1. **[Installera paketet cifs-verktyg för webbplatsuppgradering för Linux-distribution](#install-cifs-utils)**.

2. **Skapa en mapp för monteringspunkten**: Detta kan göras var som helst i filsystemet, men du behöver Observera den absoluta sökvägen till mappen. I följande exempel skapas en mapp under roten.

    ```
    sudo mkdir /mymountpoint
    ```

3. **Använd följande kommando för att lägga till följande rad `/etc/fstab`** : Kom ihåg att ersätta `<storage-account-name>`, `<share-name>`, och `<storage-account-key>` med rätt information.

    ```
    sudo bash -c 'echo "//<storage-account-name>.file.core.windows.net/<share-name> /mymountpoint cifs vers=2.1,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino" >> /etc/fstab'
    ```

> [!Note]  
> Du kan använda `sudo mount -a` att montera filresursen Azure när du har redigerat `/etc/fstab` i stället för att startas om.

## <a name="feedback"></a>Feedback
Linux-användare som vi vill gärna höra av dig!

Azure-filer för Linux användargrupp innehåller ett forum för att dela feedback när du utvärdera och anta File storage i Linux. E-post [Azure filer Linux användare](mailto:azurefileslinuxusers@microsoft.com) att ansluta till den användargruppen.

## <a name="next-steps"></a>Nästa steg
Mer information om Azure Files finns på följande länkar.
* [File Service REST API referens](http://msdn.microsoft.com/library/azure/dn167006.aspx)
* [Använda AzCopy med Microsoft Azure storage](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
* [Använda Azure CLI med Azure storage](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#create-and-manage-file-shares)
* [Vanliga frågor och svar](../storage-files-faq.md)
* [Felsökning](storage-troubleshoot-linux-file-connection-problems.md)
