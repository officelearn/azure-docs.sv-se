---
title: Använda Azure Files med Linux | Microsoft Docs
description: Lär dig hur du monterar en Azure-filresurs via SMB på Linux.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 03/29/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 06df5d403ba10489ea9a36a79a94f4b94782e4ef
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/25/2019
ms.locfileid: "68501332"
---
# <a name="use-azure-files-with-linux"></a>Använda Azure Files med Linux

[Azure Files](storage-files-introduction.md) är Microsofts lättanvända filsystem i molnet. Azure-filresurser kan monteras i Linux-distributioner med [SMB-kernel-klienten](https://wiki.samba.org/index.php/LinuxCIFS). Den här artikeln visar två sätt att montera en Azure-fil resurs: på begäran med `mount` kommandot och i start genom att skapa en post i `/etc/fstab`.

> [!NOTE]  
> För att kunna montera en Azure-filresurs utanför Azure-regionen som den finns i, till exempel lokalt eller i en annan Azure-region, måste operativ systemet ha stöd för krypterings funktionen i SMB 3,0.

## <a name="prerequisites-for-mounting-an-azure-file-share-with-linux-and-the-cifs-utils-package"></a>Krav för att montera en Azure-filresurs med Linux och CIFS-utils-paketet
<a id="smb-client-reqs"></a>

* **Ett befintligt Azure Storage-konto och en fil resurs**: Du måste ha ett lagrings konto och en fil resurs för att kunna slutföra den här artikeln. Om du inte redan har skapat en, se en av våra snabb starter på ämnet: [Skapa fil resurser – CLI](storage-how-to-use-files-cli.md).

* **Ditt lagrings konto namn och nyckel** Du behöver lagrings kontots namn och nyckel för att kunna slutföra den här artikeln. Om du har skapat en med hjälp av CLI-snabb starten ska du redan ha dem. i annat fall kan du läsa den CLI-snabb start som var länkad tidigare, för att lära dig hur du hämtar din lagrings konto nyckel.

* **Välj en Linux-distribution som passar dina monterings behov.**  
      Azure Files kan monteras antingen via SMB 2,1 och SMB 3,0. För anslutningar som kommer från klienter lokalt eller i andra Azure-regioner måste du använda SMB 3,0; Azure Files kommer att avvisa SMB 2,1 (eller SMB 3,0 utan kryptering). Om du har åtkomst till Azure-filresursen från en virtuell dator inom samma Azure-region, kan du komma åt din fil resurs med SMB 2,1, om och endast om den *begärda säkra överföringen* är inaktive rad för lagrings kontot som är värd för Azure-filresursen. Vi rekommenderar alltid att kräva säker överföring och enbart använda SMB 3,0 med kryptering.

    Stöd för SMB 3,0-kryptering introducerades i Linux-kernel-version 4,11 och har lästs in till äldre kernel-versioner för populära Linux-distributioner. Vid tiden för det här dokumentet publiceras följande distributioner från det monterings alternativ för Azure-galleriet som anges i tabell rubrikerna. 

### <a name="minimum-recommended-versions-with-corresponding-mount-capabilities-smb-version-21-vs-smb-version-30"></a>Minsta rekommenderade versioner med motsvarande monterings funktioner (SMB version 2,1 vs SMB version 3,0)

|   | SMB 2.1 <br>(Monteras på virtuella datorer inom samma Azure-region) | SMB 3.0 <br>(Monteras från lokalt och över flera regioner) |
| --- | :---: | :---: |
| Ubuntu Server | 14.04 + | 16.04 + |
| RHEL | 7+ | 7.5 + |
| CentOS | 7+ |  7.5 + |
| Debian | 8+ |   |
| openSUSE | 13.2 + | 42.3 + |
| SUSE Linux Enterprise Server | 12 | 12 SP3+ |

Om din Linux-distribution inte visas här kan du kontrol lera att Linux-kernel-versionen visas med följande kommando:

```bash
uname -r
```

* <a id="install-cifs-utils"></a>**CIFS-utils-paketet har installerats.**  
    CIFS-utils-paketet kan installeras med hjälp av paket hanteraren på valfri Linux-distribution. 

    Använd`apt-get` paket hanteraren på **Ubuntu** **-och Debian-baserade** distributioner:

    ```bash
    sudo apt-get update
    sudo apt-get install cifs-utils
    ```

    Använd  Package`yum` Manager på RHEL och CentOS:

    ```bash
    sudo yum install cifs-utils
    ```

    Använd`zypper` Package Manager på **openSUSE**:

    ```bash
    sudo zypper install cifs-utils
    ```

    Använd lämplig paket hanterare eller [kompilera från källa](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download) för andra distributioner

* **Bestäm katalog-/fil behörigheter för den monterade resursen**: I exemplen nedan används behörigheten `0777` för att ge Läs-, skriv-och körnings behörighet till alla användare. Du kan ersätta den med andra [chmod-behörigheter](https://en.wikipedia.org/wiki/Chmod) som du vill, men detta innebär att eventuellt begränsad åtkomst begränsas. Om du använder andra behörigheter bör du även överväga att använda UID och GID för att behålla åtkomsten för lokala användare och grupper som du väljer.

> [!NOTE]
> Om du inte uttryckligen tilldelar katalog-och fil behörighet med dir_mode och file_mode, kommer de att vara standardvärdet 0755.

* **Se till att port 445 är öppen**: SMB kommunicerar via TCP-port 445. Kontrollera om din brandvägg blockerar TCP-port 445 från klientdatorn.

## <a name="mount-the-azure-file-share-on-demand-with-mount"></a>Montera Azure-filresursen på begäran med`mount`

1. **[Installera CIFS-utils-paketet för din Linux-distribution](#install-cifs-utils)** .

1. **Skapa en mapp för monterings punkten**: En mapp för en monterings punkt kan skapas var som helst i fil systemet, men det är en vanlig konvention att skapa den under en ny mapp. Följande kommando skapar till exempel en ny katalog, ersätter **< storage_account_name >** och **< file_share_name >** med lämplig information för din miljö:

    ```bash
    mkdir -p <storage_account_name>/<file_share_name>
    ```

1. **Använd monterings kommandot för att montera Azure**-filresursen: Kom ihåg att ersätta **< storage_account_name >** , **< share_name >** , **< smb_version >** , **< storage_account_key >** och **< mount_point >** med lämplig information för din miljö. Om din Linux-distribution stöder SMB 3,0 med kryptering (se [förstå SMB-klientens krav](#smb-client-reqs) för mer information) använder du **3,0** för **< smb_version >** . För Linux-distributioner som inte stöder SMB 3,0 med kryptering använder du **2,1** för **< smb_version >** . En Azure-filresurs kan bara monteras utanför en Azure-region (inklusive lokalt eller i en annan Azure-region) med SMB 3,0. Om du vill kan du ändra katalog-och fil behörigheter för den monterade resursen, men detta skulle innebära att åtkomsten begränsas.

    ```bash
    sudo mount -t cifs //<storage_account_name>.file.core.windows.net/<share_name> <mount_point> -o vers=<smb_version>,username=<storage_account_name>,password=<storage_account_key>,dir_mode=0777,file_mode=0777,serverino
    ```

> [!Note]  
> När du är färdig med Azure-filresursen kan du använda `sudo umount <mount_point>` för att demontera resursen.

## <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>Skapa en permanent monterings punkt för Azure-filresursen med`/etc/fstab`

1. **[Installera CIFS-utils-paketet för din Linux-distribution](#install-cifs-utils)** .

1. **Skapa en mapp för monterings punkten**: En mapp för en monterings punkt kan skapas var som helst i fil systemet, men det är en vanlig konvention att skapa den under en ny mapp. När du skapar det här noterar du mappens absoluta sökväg. Följande kommando skapar till exempel en ny katalog, ersätter **< storage_account_name >** och **< file_share_name >** med lämplig information för din miljö.

    ```bash
    sudo mkdir -p <storage_account_name>/<file_share_name>
    ```

1. **Skapa en loggfil för att lagra användar namnet (lagrings kontots namn) och lösen ord (lagrings konto nyckeln) för fil resursen.** Ersätt **< storage_account_name >** och **< storage_account_key >** med lämplig information för din miljö.

    ```bash
    if [ ! -d "/etc/smbcredentials" ]; then
    sudo mkdir /etc/smbcredentials
    fi
    if [ ! -f "/etc/smbcredentials/<STORAGE ACCOUNT NAME>.cred" ]; then
    sudo bash -c 'echo "username=<STORAGE ACCOUNT NAME>" >> /etc/smbcredentials/<STORAGE ACCOUNT NAME>.cred'
    sudo bash -c 'echo "password=7wRbLU5ea4mgc<DRIVE LETTER>PIpUCNcuG9gk2W4S2tv7p0cTm62wXTK<DRIVE LETTER>CgJlBJPKYc4VMnwhyQd<DRIVE LETTER>UT<DRIVE LETTER>yR5/RtEHyT/EHtg2Q==" >> /etc/smbcredentials/<STORAGE ACCOUNT NAME>.cred'
    fi
    ```

1. **Ändra behörighet för autentiseringsuppgiften så att endast roten kan läsa eller ändra lösen ords filen.** Eftersom lagrings konto nyckeln i princip är ett superadministratörs lösen ord för lagrings kontot, anger du behörigheterna för filen så att endast roten kan komma åt det, så att lägre privilegier användare inte kan hämta lagrings konto nyckeln.   

    ```bash
    sudo chmod 600 /etc/smbcredentials/<storage_account_name>.cred
    ```

1. **Använd följande kommando för att lägga till följande rad i `/etc/fstab`** : Kom ihåg att ersätta **< storage_account_name >** , **< share_name >** , **< smb_version >** och **< mount_point >** med lämplig information för din miljö. Om din Linux-distribution stöder SMB 3,0 med kryptering (se [förstå SMB-klientens krav](#smb-client-reqs) för mer information) använder du **3,0** för **< smb_version >** . För Linux-distributioner som inte stöder SMB 3,0 med kryptering använder du **2,1** för **< smb_version >** . En Azure-filresurs kan bara monteras utanför en Azure-region (inklusive lokalt eller i en annan Azure-region) med SMB 3,0.

    ```bash
    sudo bash -c 'echo "//<STORAGE ACCOUNT NAME>.file.core.windows.net/<FILE SHARE NAME> /mount/<STORAGE ACCOUNT NAME>/<FILE SHARE NAME> cifs _netdev,nofail,vers=3.0,credentials=/etc/smbcredentials/<STORAGE ACCOUNT NAME>.cred,dir_mode=0777,file_mode=0777,serverino" >> /etc/fstab'

    sudo mount /mount/<STORAGE ACCOUNT NAME>/<FILE SHARE NAME>
    ```

> [!Note]  
> Du kan använda `sudo mount -a` för att montera Azure-filresursen `/etc/fstab` efter redigering i stället för att starta om.

## <a name="feedback"></a>Feedback

Linux-användare vill vi höra från dig!

Gruppen Azure Files för Linux-användare ger ett forum där du kan dela feedback när du utvärderar och inför fil lagring på Linux. E- [Azure Files Linux-användare](mailto:azurefileslinuxusers@microsoft.com) att ansluta till användarnas grupp.

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Files finns på följande länkar:

* [Planera för en Azure Files-distribution](storage-files-planning.md)
* [Vanliga frågor och svar](../storage-files-faq.md)
* [Felsökning](storage-troubleshoot-linux-file-connection-problems.md)
