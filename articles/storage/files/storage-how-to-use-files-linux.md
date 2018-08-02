---
title: Använda Azure Files med Linux | Microsoft Docs
description: Lär dig hur du monterar en Azure-filresurs via SMB på Linux.
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
ms.openlocfilehash: eda06fbb63352dd71cb0183ebdf683dc2f3a4a4c
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39413849"
---
# <a name="use-azure-files-with-linux"></a>Använda Azure Files med Linux
[Azure Files](storage-files-introduction.md) är Microsofts lättanvända filsystem i molnet. Azure-filresurser kan monteras i Linux-distributioner som använder den [SMB kernel-klienten](https://wiki.samba.org/index.php/LinuxCIFS). Den här artikeln visar två sätt att montera en Azure-filresurs: på begäran med den `mount` kommandot och på Start genom att skapa en post i `/etc/fstab`.

> [!NOTE]  
> Operativsystemet måste stödja funktioner för kryptering av SMB 3.0 för att montera en Azure-filresurs utanför Azure-regionen den finns i, till exempel lokalt eller i en annan Azure-region.

## <a name="prerequisites-for-mounting-an-azure-file-share-with-linux-and-the-cifs-utils-package"></a>Krav för att montera en Azure-filresurs med Linux och cifs-utils-paketet
<a id="smb-client-reqs"></a>
* **Välj en Linux-distribution som passar dina behov för montering.**  
      Azure Files kan monteras via SMB 2.1 och SMB 3.0. Azure Files avvisar för anslutningar från klienter på lokala eller i andra Azure-regioner, SMB 2.1 (eller SMB 3.0 utan kryptering). Om *säker överföring krävs* har aktiverats för ett lagringskonto kan endast Azure Files-anslutningar som använder SMB 3.0 med kryptering.
    
    Stöd för SMB 3.0-kryptering har introducerades i Linux-kernel-version 4.11 och anpassats till äldre kernel-versioner för populära Linux-distributioner. Vid tidpunkten för publiceringen av det här dokumentet stöder de följande distributionerna från Azure-galleriet montering alternativ som angetts i tabellrubriker. 

* **Minsta rekommenderade versioner med motsvarande mount-funktioner (SMB-version 2.1 eller SMB-version 3.0)**    
    
    |   | SMB 2.1 <br>(Monterar på virtuella datorer i samma Azure-region) | SMB 3.0 <br>(Monterar från lokalt och över olika regioner) |
    | --- | :---: | :---: |
    | Ubuntu Server | 14.04 + | 16.04 + |
    | RHEL | 7 + | 7.5 + |
    | CentOS | 7 + |  7.5 + |
    | Debian | 8 + |   |
    | openSUSE | 13.2 + | 42.3 + |
    | SUSE Linux Enterprise Server | 12 | 12 SP3 + |
    
    Om din Linux-distribution inte visas här kan kontrollera du den Linux-kernel-versionen med följande kommando:    

   ```bash
   uname -r
   ```    

* <a id="install-cifs-utils"></a>**Cifs-utils-paketet har installerats.**  
    Cifs-utils-paketet kan installeras med Pakethanteraren på valfri annan Linux-distribution. 

    På **Ubuntu** och **Debian-baserad** -distributioner kan använda den `apt-get` Pakethanteraren:

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

    På andra distributioner, använder du lämplig Pakethanteraren eller [kompilera från källan](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download)
    
* **Besluta om katalogfil/behörigheterna för den monterade resursen**: I exemplen nedan behörigheten `0777` är används för att ge Läs, Skriv och körbehörighet till alla användare. Du kan ersätta den med andra [chmod behörigheter](https://en.wikipedia.org/wiki/Chmod) enligt önskemål. 

* **Lagringskontonamn**: Om du vill montera en Azure-filresurs behöver du namnet på lagringskontot.

* **Lagringskontonyckel**: Om du vill montera en Azure-filresurs behöver du den primära (eller sekundära) lagringsnyckeln. SAS-nycklar stöds inte för montering.

* **Kontrollera att port 445 är öppen**: SMB kommunicerar via TCP-port 445 – Kontrollera att se om brandväggen inte blockerar TCP-portarna 445 från klientdatorn.

## <a name="mount-the-azure-file-share-on-demand-with-mount"></a>Montera den Azure-filen resursen på begäran med `mount`
1. **[Installera cifs-utils-paketet för din Linux-distribution](#install-cifs-utils)**.

2. **Skapa en mapp för monteringspunkten**: kan skapa en mapp för en monteringspunkt var som helst i filsystemet, men det är vanliga konventionen att skapa den här under de `/mnt` mapp. Exempel:

    ```bash
    mkdir /mnt/MyAzureFileShare
    ```

3. **Använd mount-kommando för att montera Azure-filresursen**: Kom ihåg att ersätta `<storage-account-name>`, `<share-name>`, `<smb-version>`, `<storage-account-key>`, och `<mount-point>` med rätt information för din miljö. Om din Linux-distribution stöder SMB 3.0 med kryptering (se [förstå SMB klientkrav](#smb-client-reqs) för mer information), Använd `3.0` för `<smb-version>`. Linux-distributioner som inte stöder SMB 3.0 med kryptering kan använda `2.1` för `<smb-version>`. Observera att en Azure-filresurs kan endast monteras utanför en Azure-region (inklusive lokala eller i en annan Azure-region) med SMB 3.0. 

    ```bash
    sudo mount -t cifs //<storage-account-name>.file.core.windows.net/<share-name> <mount-point> -o vers=<smb-version>,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
    ```

> [!Note]  
> När du är klar med Azure-filresursen som du kan använda `sudo umount <mount-point>` att montera resursen.

## <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>Skapa en beständig monteringspunkt för Azure-filresursen med `/etc/fstab`
1. **[Installera cifs-utils-paketet för din Linux-distribution](#install-cifs-utils)**.

2. **Skapa en mapp för monteringspunkten**: kan skapa en mapp för en monteringspunkt var som helst i filsystemet, men det är vanliga konventionen att skapa den här under de `/mnt` mapp. Observera den absoluta sökvägen till mappen där du skapar. Till exempel följande kommando skapar en ny mapp under `/mnt` (sökvägen är en absolut sökväg).

    ```bash
    sudo mkdir /mnt/MyAzureFileShare
    ```

3. **Skapa en fil med autentiseringsuppgifter för att lagra användarnamnet (lagringskontonamnet) och lösenordet (nyckeln till lagringskontot) för filresursen.** Kom ihåg att ersätta `<storage-account-name>` och `<storage-account-key>` med rätt information för din miljö. 

    ```bash
    if [ -d "/etc/smbcredentials" ]; then
        sudo mkdir /etc/smbcredentials
    fi

    if [ ! -f "/etc/smbcredentials/<storage-account-name>.cred" ]; then
        sudo bash -c 'echo "username=<storage-account-name>" >> /etc/smbcredentials/<storage-account-name>.cred'
        sudo bash -c 'echo "password=<storage-account-key>" >> /etc/smbcredentials/<storage-account-name>.cred'
    fi
    ```

4. **Ändra behörigheterna för filen autentiseringsuppgifter så att endast rot kan läsa eller ändra filen.** Ange behörigheter för filen så att endast rot kan komma åt är viktigt att eftersom lagringskontonyckeln är i stort sett ett överordnad administratörslösenord för storage-konto, så att användare med lägre behörighet kan inte läsa nyckeln till lagringskontot.   

    ```bash
    sudo chmod 600 /etc/smbcredentials/<storage-account-name>.cred
    ```

5. **Använd följande kommando för att lägga till följande rad för att `/etc/fstab`** : Kom ihåg att ersätta `<storage-account-name>`, `<share-name>`, `<smb-version>`, och `<mount-point>` med rätt information för din miljö. Om din Linux-distribution stöder SMB 3.0 med kryptering (se [förstå SMB klientkrav](#smb-client-reqs) för mer information), Använd `3.0` för `<smb-version>`. Linux-distributioner som inte stöder SMB 3.0 med kryptering kan använda `2.1` för `<smb-version>`. Observera att en Azure-filresurs kan endast monteras utanför en Azure-region (inklusive lokala eller i en annan Azure-region) med SMB 3.0. 

    ```bash
    sudo bash -c 'echo "//<storage-account-name>.file.core.windows.net/<share-name> <mount-point> cifs nofail,vers=<smb-version>,credentials=/etc/smbcredentials/<storage-account-name>.cred,dir_mode=0777,file_mode=0777,serverino" >> /etc/fstab'
    ```

> [!Note]  
> Du kan använda `sudo mount -a` att montera Azure-filresursen när du har redigerat `/etc/fstab` i stället för att starta om.

## <a name="feedback"></a>Feedback
Linux-användare som vi vill höra från dig!

Azure-filer för Linux användargrupp innehåller ett forum som du kan dela feedback som du utvärderar och börja använda fillagring i Linux. E-post [Azure filer Linux-användare](mailto:azurefileslinuxusers@microsoft.com) att ansluta till den användargruppen.

## <a name="next-steps"></a>Nästa steg
Mer information om Azure Files finns på följande länkar.
* [Introduktion till Azure Files](storage-files-introduction.md)
* [Planera för en Azure Files-distribution](storage-files-planning.md)
* [Vanliga frågor och svar](../storage-files-faq.md)
* [Felsökning](storage-troubleshoot-linux-file-connection-problems.md)
