---
title: Använda Azure-filer med Linux | Microsoft-dokument
description: Lär dig hur du monterar en Azure-filresurs över SMB på Linux.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 72264755d5f0379f0ffb07852f48885126a36898
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411593"
---
# <a name="use-azure-files-with-linux"></a>Använda Azure Files med Linux
[Azure Files](storage-files-introduction.md) är Microsofts lättanvända filsystem i molnet. Azure-filresurser kan monteras i Linux-distributioner med [SMB-kärnklienten](https://wiki.samba.org/index.php/LinuxCIFS). Den här artikeln visar två sätt att montera en `mount` Azure-filresurs: på begäran `/etc/fstab`med kommandot och vidstart genom att skapa en post i .

Det rekommenderade sättet att montera en Azure-filresurs på Linux är att använda SMB 3.0. Som standard kräver Azure Files kryptering under överföring, vilket endast stöds av SMB 3.0. Azure Files stöder också SMB 2.1, som inte stöder kryptering under överföring, men du får inte montera Azure-filresurser med SMB 2.1 från en annan Azure-region eller lokalt av säkerhetsskäl. Om inte ditt program specifikt kräver SMB 2.1, det finns ingen anledning att använda den eftersom mest populära, nyligen släppt Linux-distributioner stödja SMB 3.0:  

| | SMB 2.1 <br>(Monterar på virtuella datorer inom samma Azure-region) | SMB 3.0 <br>(Fästen från lokal och tvärregion) |
| --- | :---: | :---: |
| Ubuntu | 14.04+ | 16.04+ |
| Red Hat Enterprise Linux (RHEL) | 7+ | 7,5+ |
| CentOS | 7+ |  7,5+ |
| Debian | 8+ | 10+ |
| openSUSE | 13.2+ | 42,3+ |
| SUSE Linux Enterprise Server | 12+ | 12 SP3+ |

Om du använder en Linux-distribution som inte finns med i tabellen ovan kan du kontrollera om din Linux-distribution stöder SMB 3.0 med kryptering genom att kontrollera Linux-kärnversionen. SMB 3.0 med kryptering lades till Linux kernel version 4.11. Kommandot `uname` returnerar den version av Linux-kärnan som används:

```bash
uname -r
```

## <a name="prerequisites"></a>Krav
<a id="smb-client-reqs"></a>

* <a id="install-cifs-utils"></a>**Se till att cifs-utils-paketet är installerat.**  
    Cifs-utils-paketet kan installeras med pakethanteraren på valfri Linux-distribution. 

    På **Ubuntu-** och **Debianbaserade** distributioner använder du `apt` pakethanteraren:

    ```bash
    sudo apt update
    sudo apt install cifs-utils
    ```

    På **Fedora**, **Red Hat Enterprise Linux 8 +** och **CentOS 8 +** använder du `dnf` pakethanteraren:

    ```bash
    sudo dnf install cifs-utils
    ```

    På äldre versioner av **Red Hat Enterprise** `yum` Linux och **CentOS**använder du pakethanteraren:

    ```bash
    sudo yum install cifs-utils 
    ```

    På **openSUSE**använder `zypper` du pakethanteraren:

    ```bash
    sudo zypper install cifs-utils
    ```

    På andra distributioner använder du lämplig pakethanterare eller [kompilerar från](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download)

* **Den senaste versionen av AZURE Command Line Interface (CLI).** Mer information om hur du installerar Azure CLI finns i [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) och välj operativsystem. Om du föredrar att använda Azure PowerShell-modulen i PowerShell 6+ kan du, men instruktionerna nedan presenteras för Azure CLI.

* Se till att **port 445 är öppen:** SMB kommunicerar via TCP-port 445 - kontrollera om brandväggen inte blockerar TCP-portar 445 från klientdatorn.  Ersätt **<>** och **<ditt lagringskonto>**
    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    
    # This command assumes you have logged in with az login
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))
    fileHost=$(echo $smbPath | tr -d "/")

    nc -zvw3 $fileHost 445
    ```

    Om anslutningen lyckades bör du se något som liknar följande utdata:

    ```
    Connection to <your-storage-account> 445 port [tcp/microsoft-ds] succeeded!
    ```

    Om du inte kan öppna port 445 i företagets nätverk eller blockeras från att göra det av en Internet-leverantören kan du använda en VPN-anslutning eller ExpressRoute för att undvika port 445. Mer information finns i [Nätverksöverväganden för direkt Azure-filresursåtkomst](storage-files-networking-overview.md)..

## <a name="mounting-azure-file-share"></a>Montering av Azure-filresurs
Om du vill använda en Azure-filresurs med din Linux-distribution måste du skapa en katalog som ska fungera som monteringspunkt för Azure-filresursen. En monteringspunkt kan skapas var som helst på ditt Linux-system, men det är vanligt att skapa detta under / mnt. Efter monteringspunkten använder `mount` du kommandot för att komma åt Azure-filresursen.

Du kan montera samma Azure-filresurs till flera monteringspunkter om så önskas.

### <a name="mount-the-azure-file-share-on-demand-with-mount"></a>Montera Azure-filresursen på begäran med`mount`
1. **Skapa en mapp för monteringspunkten**: Ersätt `<your-resource-group>`, `<your-storage-account>`och `<your-file-share>` med lämplig information för din miljö:

    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    fileShareName="<your-file-share>"

    mntPath="/mnt/$storageAccountName/$fileShareName"

    sudo mkdir -p $mntPath
    ```

1. **Använd kommandot Montera för att montera Azure-filresursen**. I exemplet nedan, den lokala Linux-filen och mappbehörigheter standard 0755, vilket innebär läsa, skriva och köra för ägaren (baserat på filen / katalogen Linux ägare), läsa och köra för användare i ägargruppen, och läsa och köra för andra på systemet. Du kan `uid` använda `gid` alternativen och montera för att ange användar-ID och grupp-ID för fästet. Du kan `dir_mode` också `file_mode` använda och ange anpassade behörigheter som du vill. Mer information om hur du anger behörigheter finns i [UNIX-numerisk notation](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation) på Wikipedia. 

    ```bash
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))$fileShareName

    storageAccountKey=$(az storage account keys list \
        --resource-group $resourceGroupName \
        --account-name $storageAccountName \
        --query "[0].value" | tr -d '"')

    sudo mount -t cifs $smbPath $mntPath -o vers=3.0,username=$storageAccountName,password=$storageAccountKey,serverino
    ```

    > [!Note]  
    > Ovanstående monteringskommando monteras med SMB 3.0. Om din Linux-distribution inte stöder SMB 3.0 med kryptering eller om den bara stöder SMB 2.1, får du bara montera från en Azure VM inom samma region som lagringskontot. Om du vill montera din Azure-filresurs på en Linux-distribution som inte stöder SMB 3.0 med kryptering måste du [inaktivera kryptering under överföringen för lagringskontot](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

När du är klar med Azure-filresursen kan du använda `sudo umount $mntPath` för att avmontera resursen.

### <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>Skapa en beständig monteringspunkt för Azure-filresursen med`/etc/fstab`
1. **Skapa en mapp för monteringspunkten:** En mapp för en monteringspunkt kan skapas var som helst i filsystemet, men det är vanligt att skapa detta under /mnt. Följande kommando skapar till exempel en ny `<your-resource-group>` `<your-storage-account>`katalog, `<your-file-share>` ersätter och med lämplig information för din miljö:

    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    fileShareName="<your-file-share>"

    mntPath="/mnt/$storageAccountName/$fileShareName"

    sudo mkdir -p $mntPath
    ```

1. **Skapa en autentiseringsfil för att lagra användarnamnet (lagringskontonamnet) och lösenordet (lagringskontonyckeln) för filresursen.** 

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

1. **Ändra behörigheterna för autentiseringsfilen så att endast roten kan läsa eller ändra lösenordsfilen.** Eftersom lagringskontonyckeln i huvudsak är ett superadministratörslösenord för lagringskontot är det viktigt att ange behörigheterna för filen så att endast root kan komma åt så att användare med lägre behörighet inte kan hämta lagringskontonyckeln.   

    ```bash
    sudo chmod 600 $smbCredentialFile
    ```

1. **Använd följande kommando för att lägga `/etc/fstab`till följande rad i **: I exemplet nedan, den lokala Linux-filen och mappbehörigheter standard 0755, vilket innebär att läsa, skriva och köra för ägaren (baserat på filen / katalogen Linux ägare), läsa och köra för användare i ägargruppen, och läsa och köra för andra på systemet. Du kan `uid` använda `gid` alternativen och montera för att ange användar-ID och grupp-ID för fästet. Du kan `dir_mode` också `file_mode` använda och ange anpassade behörigheter som du vill. Mer information om hur du anger behörigheter finns i [UNIX-numerisk notation](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation) på Wikipedia.

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
    > Ovanstående monteringskommando monteras med SMB 3.0. Om din Linux-distribution inte stöder SMB 3.0 med kryptering eller om den bara stöder SMB 2.1, får du bara montera från en Azure VM inom samma region som lagringskontot. Om du vill montera din Azure-filresurs på en Linux-distribution som inte stöder SMB 3.0 med kryptering måste du [inaktivera kryptering under överföringen för lagringskontot](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

### <a name="using-autofs-to-automatically-mount-the-azure-file-shares"></a>Använda autofs för att automatiskt montera Azure-filresursen/-aktierna

1. **Kontrollera att autofs-paketet är installerat.**  

    Autofs-paketet kan installeras med pakethanteraren på valfri Linux-distribution. 

    På **Ubuntu-** och **Debianbaserade** distributioner använder du `apt` pakethanteraren:
    ```bash
    sudo apt update
    sudo apt install autofs
    ```
    På **Fedora**, **Red Hat Enterprise Linux 8 +** och **CentOS 8 +** använder du `dnf` pakethanteraren:
    ```bash
    sudo dnf install autofs
    ```
    På äldre versioner av **Red Hat Enterprise** `yum` Linux och **CentOS**använder du pakethanteraren:
    ```bash
    sudo yum install autofs 
    ```
    På **openSUSE**använder `zypper` du pakethanteraren:
    ```bash
    sudo zypper install autofs
    ```
2. **Skapa en monteringspunkt för resursen:**
   ```bash
    sudo mkdir /fileshares
    ```
3. **Skapa en ny anpassad konfigurationsfil för autofs**
    ```bash
    sudo vi /etc/auto.fileshares
    ```
4. **Lägg till följande poster i /etc/auto.fileshares**
   ```bash
   echo "$fileShareName -fstype=cifs,credentials=$smbCredentialFile :$smbPath"" > /etc/auto.fileshares
   ```
5. **Lägg till följande post i /etc/auto.master**
   ```bash
   /fileshares /etc/auto.fileshares --timeout=60
   ```
6. **Starta om autofs**
    ```bash
    sudo systemctl restart autofs
    ```
7.  **Komma åt mappen som är avsedd för resursen**
    ```bash
    cd /fileshares/$filesharename
    ```
## <a name="securing-linux"></a>Säkra Linux
För att kunna montera en Azure-filresurs på Linux måste port 445 vara tillgänglig. Många organisationer blockerar port 445 på grund av säkerhetsrisker med SMB 1. SMB 1, även känd som CIFS (Common Internet File System), är ett äldre filsystemprotokoll som ingår i många Linux-distributioner. SMB 1 är ett inaktuellt, ineffektivt och framför allt oskyddat protokoll. Den goda nyheten är att Azure Files inte stöder SMB 1, och börjar med Linux kernel version 4.18, Linux gör det möjligt att inaktivera SMB 1. Vi [rekommenderar](https://aka.ms/stopusingsmb1) alltid starkt att inaktivera SMB 1 på dina Linux-klienter innan du använder SMB-filresurser i produktion.

Från och med Linux kernel 4.18, `cifs` SMB-kärnmodulen, som kallas av äldre skäl, exponerar en ny `disable_legacy_dialects`modulparameter (ofta kallad *parm* av olika externa dokumentationer), som kallas . Även om det infördes i Linux kernel 4.18, har vissa leverantörer bakåtporterat den här ändringen till äldre kärnor som de stöder. För enkelhetens skull, i följande tabell detaljer tillgängligheten för denna modul parameter på vanliga Linux-distributioner.

| Distribution | Kan inaktivera SMB 1 |
|--------------|-------------------|
| Ubuntu 14,04-16,04 | Inga |
| Ubuntu 18.04 | Ja |
| Ubuntu 19,04+ | Ja |
| Debian 8-9 | Inga |
| Debian 10+ | Ja |
| Fedora 29+ | Ja |
| CentOS 7 | Inga | 
| CentOS 8+ | Ja |
| Red Hat Enterprise Linux 6.x-7.x | Inga |
| Red Hat Enterprise Linux 8+ | Ja |
| openSUSE Leap 15,0 | Inga |
| openSUSE Leap 15.1+ | Ja |
| openSUSE Tumbleweed | Ja |
| SUSE Linux Enterprise 11.x-12.x | Inga |
| SUSE Linux Företag 15 | Inga |
| SUSE Linux Enterprise 15,1 | Inga |

Du kan kontrollera om din Linux-distribution stöder `disable_legacy_dialects` modulparametern via följande kommando.

```bash
sudo modinfo -p cifs | grep disable_legacy_dialects
```

Det här kommandot ska mata ut följande meddelande:

```output
disable_legacy_dialects: To improve security it may be helpful to restrict the ability to override the default dialects (SMB2.1, SMB3 and SMB3.02) on mount with old dialects (CIFS/SMB1 and SMB2) since vers=1.0 (CIFS/SMB1) and vers=2.0 are weaker and less secure. Default: n/N/0 (bool)
```

Innan du inaktiverar SMB 1 måste du kontrollera att SMB-modulen för närvarande inte är laddad på ditt system (detta sker automatiskt om du har monterat en SMB-resurs). Du kan göra detta med följande kommando, som inte bör mata ut något om SMB inte har lästs in:

```bash
lsmod | grep cifs
```

Om du vill ta bort modulen avmonterar du först alla SMB-resurser (med `umount` kommandot som beskrivs ovan). Du kan identifiera alla monterade SMB-resurser på datorn med följande kommando:

```bash
mount | grep cifs
```

När du har avmonterat alla SMB-filresurser är det säkert att lossa modulen. Det kan du göra med kommandot `modprobe`:

```bash
sudo modprobe -r cifs
```

Du kan läsa in modulen manuellt med SMB 1 lossad med `modprobe` kommandot:

```bash
sudo modprobe cifs disable_legacy_dialects=Y
```

Slutligen kan du kontrollera SMB modulen har laddats med `/sys/module/cifs/parameters`parametern genom att titta på de inlästa parametrarna i:

```bash
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

Om du ständigt inaktiverar SMB 1 på Ubuntu- och Debianbaserade distributioner måste du skapa en ny fil `/etc/modprobe.d/local.conf` (om du inte redan har anpassade alternativ för andra moduler) anropad med inställningen. Du kan göra detta med följande kommando:

```bash
echo "options cifs disable_legacy_dialects=Y" | sudo tee -a /etc/modprobe.d/local.conf > /dev/null
```

Du kan kontrollera att detta har fungerat genom att läsa in SMB-modulen:

```bash
sudo modprobe cifs
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

## <a name="feedback"></a>Feedback
Linux-användare, vi vill höra från dig!

Azure Files for Linux-användares grupp är ett forum där du kan dela feedback när du utvärderar och antar fillagring på Linux. E-post [Azure Files Linux-användare](mailto:azurefiles@microsoft.com) att gå med i användarnas grupp.

## <a name="next-steps"></a>Nästa steg
Mer information om Azure Files finns på följande länkar:

* [Planera för distribution av Azure Files](storage-files-planning.md)
* [VANLIGA FRÅGOR OCH SVAR](../storage-files-faq.md)
* [Felsökning](storage-troubleshoot-linux-file-connection-problems.md)
