---
title: Använda Azure Files med Linux | Microsoft Docs
description: Lär dig hur du monterar en Azure-filresurs via SMB på Linux.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 70673dc7d42a0c7d9b60f3c3f877c1985dac3c98
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73097802"
---
# <a name="use-azure-files-with-linux"></a>Använda Azure Files med Linux
[Azure Files](storage-files-introduction.md) är Microsofts lättanvända filsystem i molnet. Azure-filresurser kan monteras i Linux-distributioner med [SMB-kernel-klienten](https://wiki.samba.org/index.php/LinuxCIFS). Den här artikeln visar två sätt att montera en Azure-fil resurs: på begäran med kommandot `mount` och vid start genom att skapa en post i `/etc/fstab`.

Det rekommenderade sättet att montera en Azure-filresurs på Linux är att använda SMB 3,0. Som standard kräver Azure Files kryptering under överföring, som endast stöds av SMB 3,0. Azure Files stöder också SMB 2,1, som inte stöder kryptering under överföring, men du kan inte montera Azure-filresurser med SMB 2,1 från en annan Azure-region eller lokalt av säkerhets skäl. Såvida inte ditt program specifikt kräver SMB 2,1, finns det mycket anledning att använda det eftersom de flesta populära, nyligen utgivna Linux-distributioner har stöd för SMB 3,0:  

| | SMB 2.1 <br>(Monteras på virtuella datorer inom samma Azure-region) | SMB 3.0 <br>(Monteras från lokalt och över flera regioner) |
| --- | :---: | :---: |
| Ubuntu | 14.04 + | 16.04 + |
| Red Hat Enterprise Linux (RHEL) | 7 + | 7.5 + |
| CentOS | 7 + |  7.5 + |
| Debian | 8 + | 10 + |
| openSUSE | 13.2 + | 42.3 + |
| SUSE Linux Enterprise Server | 12 + | 12 SP3 + |

Om du använder en Linux-distribution som inte finns med i tabellen ovan kan du kontrol lera om din Linux-distribution stöder SMB 3,0 med kryptering genom att kontrol lera Linux kernel-versionen. SMB 3,0 med kryptering har lagts till i Linux-kernel-version 4,11. `uname` kommandot returnerar den version av Linux-kärnan som används:

```bash
uname -r
```

## <a name="prerequisites"></a>Krav
<a id="smb-client-reqs"></a>

* <a id="install-cifs-utils"></a>**Se till att CIFS-utils-paketet är installerat.**  
    CIFS-utils-paketet kan installeras med hjälp av paket hanteraren på valfri Linux-distribution. 

    Använd `apt` Package Manager på **Ubuntu** **-och Debian-baserade** distributioner:

    ```bash
    sudo apt update
    sudo apt install cifs-utils
    ```

    På **Fedora**, **Red Hat Enterprise Linux 8 +** och **CentOS 8 +** , använder du `dnf` Package Manager:

    ```bash
    sudo dnf install cifs-utils
    ```

    I äldre versioner av **Red Hat Enterprise Linux** och **CentOS**använder du `dnf` Package Manager:

    ```bash
    sudo yum install cifs-utils 
    ```

    Använd `zypper` Package Manager på **openSUSE**:

    ```bash
    sudo zypper install cifs-utils
    ```

    Använd lämplig paket hanterare eller [kompilera från källa](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download) för andra distributioner

* **Den senaste versionen av kommando rads gränssnittet för Azure (CLI).** Mer information om hur du installerar Azure CLI finns i [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) och välj operativ system. Om du föredrar att använda Azure PowerShell-modulen i PowerShell 6 + kan du se till att anvisningarna nedan visas för Azure CLI.

* **Se till att port 445 är öppen**: SMB kommunicerar via TCP-port 445 – kontrol lera om brand väggen inte blockerar TCP-portarna 445 från klient datorn.  Ersätt **< dina-resurs grupps >** och **< ditt-storage – konto >**
    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    
    # This command assumes you have logged in with az login
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))
    fileHost=$(echo $fileHost | tr -d "/")

    nc -zvw3 $fileHost 445
    ```

    Om anslutningen lyckades ser du något som liknar följande utdata:

    ```
    Connection to <your-storage-account> 445 port [tcp/microsoft-ds] succeeded!
    ```

    Om du inte kan öppna port 445 i företagets nätverk eller om du inte är blockerad av en Internet leverantör kan du använda en VPN-anslutning eller ExpressRoute för att lösa port 445. Mer information finns i [nätverks överväganden för direkt åtkomst till Azure-filresurser](storage-files-networking-overview.md).

## <a name="mounting-azure-file-share"></a>Montera Azure-filresurs
Om du vill använda en Azure-filresurs med din Linux-distribution måste du skapa en katalog som ska fungera som monterings punkt för Azure-filresursen. En monterings punkt kan skapas var som helst på Linux-systemet, men det är en vanlig konvention för att skapa den under/mnt. När monterings punkten används kan du använda kommandot `mount` för att få åtkomst till Azure-filresursen.

Du kan montera samma Azure-filresurs till flera monterings punkter om du vill.

### <a name="mount-the-azure-file-share-on-demand-with-mount"></a>Montera Azure-filresursen på begäran med `mount`
1. **Skapa en mapp för monterings punkten**: Ersätt `<your-resource-group>`, `<your-storage-account>`och `<your-file-share>` med lämplig information för din miljö:

    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    fileShareName="<your-file-share>"

    mntPath="/mnt/$storageAccountName/$fileShareName"

    sudo mkdir -p $mntPath
    ```

1. **Använd monterings kommandot för att montera Azure-filresursen**. I exemplet nedan är den lokala Linux-filen och mappens behörigheter standard 0755, vilket innebär att läsa, skriva och köra för ägaren (baserat på ägaren till filen/katalogen Linux), läsa och köra för användare i ägar gruppen och läsa och köra för andra i systemet. Du kan använda alternativen `uid` och `gid` montering för att ange användar-ID och grupp-ID för monteringen. Du kan också använda `dir_mode` och `file_mode` för att ange anpassade behörigheter som önskade. Mer information om hur du anger behörigheter finns i [UNIX numerisk notation](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation) på wikipedia. 

    ```bash
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))$fileShare

    storageAccountKey=$(az storage account keys list \
        --resource-group $resourceGroupName \
        --account-name $storageAccountName \
        --query "[0].value" | tr -d '"')

    sudo mount -t cifs $smbPath $mntPath -o vers=3.0,username=$storageAccountName,password=$storageAccountKey,serverino
    ```

    > [!Note]  
    > Monterings kommandot ovan monterar med SMB 3,0. Om din Linux-distribution inte stöder SMB 3,0 med kryptering eller om det bara stöder SMB 2,1, kan du bara montera från en virtuell Azure-dator inom samma region som lagrings kontot. Om du vill montera en Azure-filresurs på en Linux-distribution som inte stöder SMB 3,0 med kryptering måste du [inaktivera kryptering under överföring för lagrings kontot](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

När du är färdig med Azure-filresursen kan du använda `sudo umount $mntPath` för att demontera resursen.

### <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>Skapa en permanent monterings punkt för Azure-filresursen med `/etc/fstab`
1. **Skapa en mapp för monterings punkten**: en mapp för en monterings punkt kan skapas var som helst i fil systemet, men det är en vanlig konvention för att skapa den under/mnt. Följande kommando skapar till exempel en ny katalog, ersätter `<your-resource-group>`, `<your-storage-account>`och `<your-file-share>` med lämplig information för din miljö:

    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    fileShareName="<your-file-share>"

    mntPath="/mnt/$storageAccountName/$fileShareName"

    sudo mkdir -p $mntPath
    ```

1. **Skapa en loggfil för att lagra användar namnet (lagrings kontots namn) och lösen ord (lagrings konto nyckeln) för fil resursen.** 

    ```bash
    if [ ! -d "/etc/smbcredentials" ]; then
        sudo mkdir "/etc/smbcredentials"
    fi

    storageAccountKey=$(az storage account keys list \
        --resource-group $resourceGroupName \
        --account-name $storageAccountName \
        --query "[0].value" | tr -d '"')
    
    smbCredentialFile="/etc/smbcredentials/$storageAccountName.cred"
    if [ ! -f $smbCredentialFile ]; then
        echo "username=$storageAccountName" | sudo tee $smbCredentialFile > /dev/null
        echo "password=$storageAccountKey" | sudo tee -a $smbCredentialFile > /dev/null
    else 
        echo "The credential file $smbCredentialFile already exists, and was not modified."
    fi
    ```

1. **Ändra behörighet för autentiseringsuppgiften så att endast roten kan läsa eller ändra lösen ords filen.** Eftersom lagrings konto nyckeln i princip är ett superadministratörs lösen ord för lagrings kontot, anger du behörigheterna för filen så att endast roten kan komma åt det, så att lägre privilegier användare inte kan hämta lagrings konto nyckeln.   

    ```bash
    sudo chmod 600 $smbCredentialFile
    ```

1. **Använd följande kommando för att lägga till följande rad i `/etc/fstab`** : i exemplet nedan är den lokala Linux-filen och mappens behörigheter standard 0755, vilket innebär Läs-, skriv-och körnings behörighet för ägaren (baserat på filen/katalogen Linux-ägaren), läsa och köra för användare i ägar gruppen och läsa och köra andra i systemet. Du kan använda alternativen `uid` och `gid` montering för att ange användar-ID och grupp-ID för monteringen. Du kan också använda `dir_mode` och `file_mode` för att ange anpassade behörigheter som önskade. Mer information om hur du anger behörigheter finns i [UNIX numerisk notation](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation) på wikipedia.

    ```bash
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))$fileShareName

    if [ -z "$(grep $smbPath\ $mntPath /etc/fstab)" ]; then
        echo "$smbPath $mntPath cifs nofail,vers=3.0,credentials=$smbCredentialFile,serverino" | sudo tee -a /etc/fstab > /dev/null
    else
        echo "/etc/fstab was not modified to avoid conflicting entries as this Azure file share was already present. You may want to double check /etc/fstab to ensure the configuration is as desired."
    fi

    sudo mount -a
    ```
    
    > [!Note]  
    > Monterings kommandot ovan monterar med SMB 3,0. Om din Linux-distribution inte stöder SMB 3,0 med kryptering eller om det bara stöder SMB 2,1, kan du bara montera från en virtuell Azure-dator inom samma region som lagrings kontot. Om du vill montera en Azure-filresurs på en Linux-distribution som inte stöder SMB 3,0 med kryptering måste du [inaktivera kryptering under överföring för lagrings kontot](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="securing-linux"></a>Skydda Linux
Port 445 måste vara tillgänglig för att du ska kunna montera en Azure-filresurs på Linux. Många organisationer blockerar port 445 på grund av säkerhetsrisker med SMB 1. SMB 1, som även kallas CIFS (common Internet File System), är ett äldre fil system protokoll som ingår i många Linux-distributioner. SMB 1 är ett inaktuellt, ineffektivt och framför allt oskyddat protokoll. Den goda nyheten är att Azure Files inte stöder SMB 1, och från och med linux kernel version 4,18, gör det möjligt för Linux att inaktivera SMB 1. Vi [rekommenderar alltid starkt](https://aka.ms/stopusingsmb1) att du inaktiverar SMB 1 på dina Linux-klienter innan du använder SMB-filresurser i produktion.

Från och med linux kernel 4,18, anropade SMB-kernel-modulen `cifs` av tidigare skäl, exponerar en ny modul-parameter (kallas ofta *parameter* av olika externa dokumentation), som kallas `disable_legacy_dialects`. Även om vi introducerade i Linux kernel 4,18 har vissa leverantörer förflyttat den här ändringen till äldre kärnor som de stöder. För enkelhetens skull är följande tabell information om tillgängligheten för den här modulen i vanliga Linux-distributioner.

| Distribution | Kan inaktivera SMB 1 |
|--------------|-------------------|
| Ubuntu 14.04-16.04 | Nej |
| Ubuntu 18.04 | Ja |
| Ubuntu 19.04 + | Ja |
| Debian 8-9 | Nej |
| Debian 10 + | Ja |
| Fedora 29 + | Ja |
| CentOS 7 | Nej | 
| CentOS 8 + | Ja |
| Red Hat Enterprise Linux 6. x-7. x | Nej |
| Red Hat Enterprise Linux 8 + | Ja |
| openSUSE skottår 15,0 | Nej |
| openSUSE skottår 15.1 + | Ja |
| openSUSE Tumbleweed | Ja |
| SUSE Linux Enterprise 11. x-12. x | Nej |
| SUSE Linux Enterprise 15 | Nej |
| SUSE Linux Enterprise 15,1 | Nej |

Du kan kontrol lera om din Linux-distribution stöder parametern `disable_legacy_dialects` modul via följande kommando.

```bash
sudo modinfo -p cifs | grep disable_legacy_dialects
```

Följande meddelande ska visas i det här kommandot:

```Output
disable_legacy_dialects: To improve security it may be helpful to restrict the ability to override the default dialects (SMB2.1, SMB3 and SMB3.02) on mount with old dialects (CIFS/SMB1 and SMB2) since vers=1.0 (CIFS/SMB1) and vers=2.0 are weaker and less secure. Default: n/N/0 (bool)
```

Innan du inaktiverar SMB 1 måste du kontrol lera att SMB-modulen inte är inläst i systemet (detta sker automatiskt om du har monterat en SMB-resurs). Du kan göra detta med följande kommando, som ska returnera ingenting om SMB inte har lästs in:

```bash
lsmod | grep cifs
```

Om du vill ta bort modulen avmonterar du först alla SMB-resurser (med kommandot `umount` enligt beskrivningen ovan). Du kan identifiera alla monterade SMB-resurser i systemet med följande kommando:

```bash
mount | grep cifs
```

När du har demonterat alla SMB-filresurser är det säkert att ta bort modulen. Det kan du göra med kommandot `modprobe`:

```bash
sudo modprobe -r cifs
```

Du kan läsa in modulen manuellt med SMB 1 från `modprobe`-kommandot:

```bash
sudo modprobe cifs disable_legacy_dialects=Y
```

Slutligen kan du kontrol lera att SMB-modulen har lästs in med parametern genom att titta på de inlästa parametrarna i `/sys/module/cifs/parameters`:

```bash
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

För att permanent inaktivera SMB 1 på Ubuntu-och Debian-baserade distributioner måste du skapa en ny fil (om du inte redan har anpassade alternativ för andra moduler) som kallas `/etc/modprobe.d/local.conf` med inställningen. Du kan göra detta med följande kommando:

```bash
echo "options cifs disable_legacy_dialects=Y" | sudo tee -a /etc/modprobe.d/local.conf > /dev/null
```

Du kan kontrol lera att detta har fungerat genom att läsa in SMB-modulen:

```bash
sudo modprobe cifs
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

## <a name="feedback"></a>Feedback
Linux-användare vill vi höra från dig!

Gruppen Azure Files för Linux-användare ger ett forum där du kan dela feedback när du utvärderar och inför fil lagring på Linux. E- [Azure Files Linux-användare](mailto:azurefileslinuxusers@microsoft.com) att ansluta till användarnas grupp.

## <a name="next-steps"></a>Nästa steg
Mer information om Azure Files finns på följande länkar:

* [Planera för en Azure Files-distribution](storage-files-planning.md)
* [VANLIGA FRÅGOR OCH SVAR](../storage-files-faq.md)
* [Troubleshooting](storage-troubleshoot-linux-file-connection-problems.md) (Felsökning)