---
title: Använda Azure filer med Linux | Microsoft Docs
description: Lär dig hur du monterar en Azure-filresursen via SMB på Linux.
services: storage
documentationcenter: na
author: RenaShahMSFT
manager: aungoo
editor: tamram
ms.assetid: 6edc37ce-698f-4d50-8fc1-591ad456175d
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/29/2018
ms.author: renash
ms.openlocfilehash: ec900182e2fe201ee598518076c6a75a7ac057c2
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34839577"
---
# <a name="use-azure-files-with-linux"></a>Använda Azure filer med Linux
[Azure Files](storage-files-introduction.md) är Microsofts lättanvända filsystem i molnet. Azure-filresurser kan monteras i Linux-distributioner som använder den [SMB kernel-klienten](https://wiki.samba.org/index.php/LinuxCIFS). Den här artikeln beskrivs två sätt att montera en Azure-filresursen: på begäran med den `mount` kommandot och på Start genom att skapa en post i `/etc/fstab`.

> [!NOTE]  
> För att montera en Azure-filresursen utanför Azure-regionen finns i, till exempel lokalt eller i en annan Azure-region stöder Operativsystemet kryptering funktionerna i SMB 3.0.

## <a name="prerequisites-for-mounting-an-azure-file-share-with-linux-and-the-cifs-utils-package"></a>Förutsättningar för att montera en Azure-filresursen med Linux- och cifs-verktyg för webbplatsuppgradering-paket
* **Välj en Linux-distributionsplats som kan ha cifs-verktyg för webbplatsuppgradering paketet installeras.**  
    Följande Linux-distributioner är tillgängliga för användning i Azure-galleriet:

    * Ubuntu Server 14.04 +
    * RHEL 7 +
    * CentOS 7 +
    * Debian 8 +
    * openSUSE 13.2 +
    * SUSE Linux Enterprise Server 12

* <a id="install-cifs-utils"></a>**Cifs-verktyg för webbplatsuppgradering paketet har installerats.**  
    Paketets cifs-verktyg för webbplatsuppgradering kan installeras med package manager på Linux-distribution av ditt val. 

    På **Ubuntu** och **Debian-baserade** distributioner, använda den `apt-get` Pakethanteraren:

    ```bash
    sudo apt-get update
    sudo apt-get install cifs-utils
    ```

    På **RHEL** och **CentOS**, använda den `yum` Pakethanteraren:

    ```bash
    sudo yum install cifs-utils
    ```

    På **openSUSE**, använda den `zypper` Pakethanteraren:

    ```bash
    sudo zypper install cifs-utils
    ```

    Använd lämplig package manager på andra distributioner eller [kompilera från källan](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download).

* <a id="smb-client-reqs"></a>**Förstå kraven för SMB-klienten.**  
    Azure Files kan monteras antingen via SMB 2.1 och SMB 3.0. Azure-filer för anslutningar från klienter på lokalt eller i andra Azure-regioner, kommer att avslå SMB 2.1 (eller SMB 3.0 utan kryptering). Om *säker överföring krävs* har aktiverats för ett lagringskonto Azure-filer endast tillåter anslutningar som använder SMB 3.0 med kryptering.
    
    Stöd för SMB 3.0-kryptering har introducerades i Linux kernel-version 4.11 och anpassats till äldre kernel-versioner för populära Linux-distributioner. Vid tidpunkten för det här dokumentet publikationen stöder följande distributioner från Azure-galleriet funktionen:

    - Ubuntu Server 16.04 +
    - openSUSE 42.3 +
    - SUSE Linux Enterprise Server 12 SP3 +
    
    Om Linux-distribution inte visas här, kan du kontrollera kernel-version för Linux med följande kommando:

    ```bash
    uname -r
    ```

* **Besluta om filen/katalogen behörigheterna för den monterade resursen**: I exemplen nedan, behörigheten `0777` är används för att ge Läs-, Skriv- och körbehörighet till alla användare. Du kan ersätta den med andra [chmod-behörigheter](https://en.wikipedia.org/wiki/Chmod) enligt önskemål. 

* **Lagringskontonamnet**: Om du vill montera en Azure-filresurs, måste namnet på lagringskontot.

* **Lagringskontonyckel**: Om du vill montera en Azure-filresurs, måste lagringsnyckeln primära (eller sekundära). SAS-nycklar stöds inte för montering.

* **Kontrollera att port 445 är öppen**: SMB kommunicerar via TCP-port 445 - Kontrollera att se om brandväggen inte blockerar TCP-portarna 445 från klientdatorn.

## <a name="mount-the-azure-file-share-on-demand-with-mount"></a>Montera den Azure file share på begäran med `mount`
1. **[Installera paketet cifs-verktyg för webbplatsuppgradering för Linux-distribution](#install-cifs-utils)**.

2. **Skapa en mapp för monteringspunkten**: en mapp för en monteringspunkt kan skapas var som helst i filsystemet, men det är vanliga konventionen för att skapa det under den `/mnt` mapp. Exempel:

    ```bash
    mkdir /mnt/MyAzureFileShare
    ```

3. **Använd mount-kommando för att montera filresursen Azure**: Kom ihåg att ersätta `<storage-account-name>`, `<share-name>`, `<smb-version>`, `<storage-account-key>`, och `<mount-point>` med lämplig information för din miljö. Om SMB 3.0 har stöd för Linux-distribution med kryptering (se [förstå SMB klientkrav](#smb-client-reqs) för mer information), Använd `3.0` för `<smb-version>`. Linux-distributioner som inte stöder SMB 3.0 med kryptering använder `2.1` för `<smb-version>`. Observera att en Azure-filresurs kan endast monteras utanför en Azure-region (inklusive lokalt eller i en annan Azure-region) med SMB 3.0. 

    ```bash
    sudo mount -t cifs //<storage-account-name>.file.core.windows.net/<share-name> <mount-point> -o vers=<smb-version>,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
    ```

> [!Note]  
> När du är klar med hjälp av Azure-filresursen, kan du använda `sudo umount <mount-point>` demontera resursen.

## <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>Skapa en beständig monteringspunkt för Azure-filresursen med `/etc/fstab`
1. **[Installera paketet cifs-verktyg för webbplatsuppgradering för Linux-distribution](#install-cifs-utils)**.

2. **Skapa en mapp för monteringspunkten**: en mapp för en monteringspunkt kan skapas var som helst i filsystemet, men det är vanliga konventionen för att skapa det under den `/mnt` mapp. Observera den absoluta sökvägen till mappen där du skapar. Till exempel följande kommando skapar en ny mapp under `/mnt` (sökvägen är en absolut sökväg).

    ```bash
    sudo mkdir /mnt/MyAzureFileShare
    ```

3. **Skapa en fil för autentiseringsuppgifter för att lagra användarnamnet (lagringskontots namn) och lösenordet (lagringskontonyckel) för filresursen.** Kom ihåg att ersätta `<storage-account-name>` och `<storage-account-key>` med lämplig information för din miljö. 

    ```bash
    if [ -d "/etc/smbcredentials" ]; then
        sudo mkdir /etc/smbcredentials
    fi

    if [ ! -f "/etc/smbcredentials/<storage-account-name>.cred" ]; then
        sudo bash -c 'echo "username=<storage-account-name>" >> /etc/smbcredentials/<storage-account-name>.cred'
        sudo bash -c 'echo "password=<storage-account-key>" >> /etc/smbcredentials/<storage-account-name>.cred'
    fi
    ```

4. **Ändra behörigheter för filen autentiseringsuppgifter så att endast rot kan läsa eller ändra filen.** Ange behörigheter för filen så att endast rot kan komma åt är viktigt att eftersom lagringskontots åtkomstnyckel är i grunden en överordnad administratörslösenordet för storage-konto, så att användare med lägre behörighet kan inte läsa lagringskontots åtkomstnyckel.   

    ```bash
    sudo chmod 600 /etc/smbcredentials/<storage-account-name>.cred
    ```

5. **Använd följande kommando för att lägga till följande rad `/etc/fstab`** : Kom ihåg att ersätta `<storage-account-name>`, `<share-name>`, `<smb-version>`, och `<mount-point>` med lämplig information för din miljö. Om SMB 3.0 har stöd för Linux-distribution med kryptering (se [förstå SMB klientkrav](#smb-client-reqs) för mer information), Använd `3.0` för `<smb-version>`. Linux-distributioner som inte stöder SMB 3.0 med kryptering använder `2.1` för `<smb-version>`. Observera att en Azure-filresurs kan endast monteras utanför en Azure-region (inklusive lokalt eller i en annan Azure-region) med SMB 3.0. 

    ```bash
    sudo bash -c 'echo "//<storage-account-name>.file.core.windows.net/<share-name> <mount-point> cifs nofail,vers=<smb-version>,credentials=/etc/smbcredentials/<storage-account-name>.cred,dir_mode=0777,file_mode=0777,serverino" >> /etc/fstab'
    ```

> [!Note]  
> Du kan använda `sudo mount -a` att montera filresursen Azure när du har redigerat `/etc/fstab` i stället för att startas om.

## <a name="feedback"></a>Feedback
Linux-användare som vi vill gärna höra av dig!

Azure-filer för Linux användargrupp innehåller ett forum för att dela feedback när du utvärdera och anta File storage i Linux. E-post [Azure filer Linux användare](mailto:azurefileslinuxusers@microsoft.com) att ansluta till den användargruppen.

## <a name="next-steps"></a>Nästa steg
Mer information om Azure Files finns på följande länkar.
* [Introduktion till Azure-filer](storage-files-introduction.md)
* [Planera för en Azure Files-distribution](storage-files-planning.md)
* [Vanliga frågor och svar](../storage-files-faq.md)
* [Felsökning](storage-troubleshoot-linux-file-connection-problems.md)