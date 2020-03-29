---
title: Distribuera Azure-filer | Microsoft-dokument
description: Lär dig hur du distribuerar Azure-filer från början till.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 05/22/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 38339defc9d06f3e809bc24f957ebbb30abb46d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598790"
---
# <a name="how-to-deploy-azure-files"></a>Så här distribuerar du Azure Files
[Azure Files](storage-files-introduction.md) erbjuder fullständigt hanterade filresurser i molnet som är tillgängliga via branschstandard SMB-protokollet. Den här artikeln visar hur du praktiskt taget distribuerar Azure-filer inom din organisation.

Vi rekommenderar starkt att läsa [Planering för en Azure Files-distribution](storage-files-planning.md) innan du följer stegen i den här artikeln.

## <a name="prerequisites"></a>Krav
Den här artikeln förutsätter att du redan har slutfört följande steg:

- Skapade ett Azure Storage-konto med önskad återhämtning och krypteringsalternativ, i den region du önskar. Se [Skapa ett lagringskonto](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) för steg-för-steg-anvisningar om hur du skapar ett lagringskonto.
- Skapade en Azure-filresurs med önskad kvot i ditt lagringskonto. Se [Skapa en filresurs](storage-how-to-create-file-share.md) för steg-för-steg-anvisningar om hur du skapar en filresurs.

## <a name="transfer-data-into-azure-files"></a>Överföra data till Azure-filer
Du kanske vill migrera befintliga filresurser, till exempel de som lagras lokalt, till din nya Azure-filresurs. Det här avsnittet visar hur du flyttar data till en Azure-filresurs via flera populära metoder som beskrivs från [planeringsguiden](storage-files-planning.md#migration)

### <a name="azure-file-sync"></a>Azure File Sync
Med Azure File Sync kan du centralisera din organisations filresurser i Azure Files med samma flexibilitet, prestanda och kompatibilitet som du får om du använder en lokal filserver. Detta gör detta genom att omvandla dina Windows-servrar till en snabb cache av din Azure-filresurs. Du kan använda alla protokoll som är tillgängliga på Windows Server för att komma åt data lokalt (inklusive SMB, NFS och FTPS) och du kan ha så många cacheminnen som du behöver över hela världen.

Azure File Sync kan användas för att migrera data till en Azure-filresurs, även om synkroniseringsmekanismen inte önskas för långvarig användning. Mer information om hur du använder Azure File Sync för att överföra data till Azure-filresurs finns i [Planering för en Azure File Sync-distribution](storage-sync-files-planning.md) och hur du [distribuerar Azure File Sync](storage-sync-files-deployment-guide.md).

### <a name="azure-importexport"></a>Azure Import/Export
Med Azure Import/Export-tjänsten kan du på ett säkert sätt överföra stora mängder data till en Azure-filresurs genom att skicka hårddiskar till ett Azure-datacenter. Se [Använda Tjänsten Microsoft Azure Import/Export för att överföra data till Azure-lagring](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) för en mer detaljerad översikt över tjänsten.

> [!Note]  
> Azure Import/Export-tjänsten stöder inte export av filer från en Azure-filresurs just nu.

Följande steg importerar data från en lokal plats till din Azure-filresurs.

1. Skaffa det antal hårddiskar som krävs för att skicka till Azure. Hårddiskar kan vara av valfri diskstorlek, men måste vara antingen en 2,5"eller 3,5"SSD eller HDD som stöder SATA II eller SATA III-standarden. 

2. Anslut och montera varje disk på servern/datorn och gör dataöverföringen. För bästa prestanda rekommenderar vi att du kör det lokala exportjobbet lokalt på servern som innehåller data. I vissa fall, till exempel när filservern som betjänar data är en NAS-enhet, kanske detta inte är möjligt. I så fall är det helt acceptabelt att montera varje disk på en dator.

3. Se till att varje enhet är online, initierad och tilldelas en enhetsbeteckning. Om du vill ansluta en enhet, initiera och tilldela en enhetsbeteckning öppnar du MMC-snapin-modulen Diskhantering (diskmgmt.msc).

    - Om du vill ansluta en disk (om den inte redan är online) högerklickar du på disken i den nedre rutan i MMC för diskhantering och väljer "Online".
    - Om du vill initiera en disk högerklickar du på disken i det nedre fönstret (när disken är online) och väljer "Initiera". Var noga med att välja "GPT" när du blir tillfrågad.

        ![En skärmbild av menyn Initiera disk i MMC för diskhantering](media/storage-files-deployment-guide/transferdata-importexport-1.PNG)

    - Om du vill tilldela en enhetsbeteckning till disken högerklickar du på det "oallokerade" utrymmet på den online- och initierade disken och klickar på "Ny enkel volym". På så sätt kan du tilldela enhetsbeteckning. Observera att du inte behöver formatera volymen så här kommer att göras senare.

        ![En skärmbild av guiden Ny enkel volym i MMC för diskhantering](media/storage-files-deployment-guide/transferdata-importexport-2.png)

4. Skapa csv-filen för datauppsättning. Datauppsättningen CSV-fil är en mappning mellan sökvägen till de lokala data och önskad Azure-filresurs som data ska kopieras till. Följande CSV-fil för datauppsättning mappar till exempel en lokal filresurs ("F:\shares\scratch") till en Azure-filresurs ("MyAzureFileShare"):
    
    ```
    BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
    "F:\shares\scratch\","MyAzureFileShare/",file,rename,"None",None
    ```

    Flera resurser med ett lagringskonto kan anges. Mer information [finns i Förbereda datauppsättningen CSV-filen.](../common/storage-import-export-tool-preparing-hard-drives-import.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

5. Skapa driveet CSV-filen. Diskuppsättningen CSV-filen listar de diskar som är tillgängliga för den lokala exportagenten. Följande driveset CSV-fillistor `X:`och `Y:` `Z:` enheter som ska användas i det lokala exportjobbet:

    ```
    DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
    X,Format,SilentMode,Encrypt,
    Y,Format,SilentMode,Encrypt,
    Z,Format,SilentMode,Encrypt,
    ```
    
    Mer information [finns i Förbereda driveet CSV-filen.](../common/storage-import-export-tool-preparing-hard-drives-import.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

6. Använd [WAImportExport-verktyget](https://www.microsoft.com/download/details.aspx?id=55280) för att kopiera data till en eller flera hårddiskar.

    ```
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
    ```

    > [!Warning]  
    > Ändra inte data på hårddiskarna eller journalfilen när diskförberedelserna har slutförts.

7. [Skapa ett importjobb](../common/storage-import-export-data-to-files.md#step-2-create-an-import-job).
    
### <a name="robocopy"></a>Robocopy
Robocopy är ett välkänt kopieringsverktyg som levereras med Windows och Windows Server. Robocopy kan användas för att överföra data till Azure-filer genom att montera filresursen lokalt och sedan använda den monterade platsen som mål i robocopy-kommandot. Använda Robocopy är ganska enkel:

1. [Montera din Azure-filresurs](storage-how-to-use-files-windows.md). För bästa prestanda rekommenderar vi att du monterar Azure-filresursen lokalt på servern som innehåller data. I vissa fall, till exempel när filservern som betjänar data är en NAS-enhet, kanske detta inte är möjligt. I så fall är det helt acceptabelt att montera Azure-filresursen på en dator. I det `net use` här exemplet används på kommandoraden för att montera filresursen:

    ```
    net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
    ```

2. Använd `robocopy` på kommandoraden för att flytta data till Azure-filresursen:

    ```
    robocopy <path-to-local-share> <path-to-azure-file-share> /E /Z /MT:32
    ```
    
    Robocopy har ett stort antal alternativ för att ändra kopieringsbeteendet som du vill. Mer information finns på [sidan Robocopy](https://technet.microsoft.com/library/cc733145.aspx) manual.

### <a name="azcopy"></a>AzCopy
AzCopy är ett kommandoradsverktyg som utformats för att kopiera data till och från Azure-filer, samt Azure Blob-lagring, med hjälp av enkla kommandon med optimal prestanda. Det är enkelt att använda AzCopy:

1. Ladda ner den [senaste versionen av AzCopy på Windows](https://aka.ms/downloadazcopy) eller [Linux](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy-linux#download-and-install-azcopy).
2. Använd `azcopy` på kommandoraden för att flytta data till Azure-filresursen. Syntaxen i Windows är följande: 

    ```
    azcopy /Source:<path-to-local-share> /Dest:https://<storage-account>.file.core.windows.net/<file-share>/ /DestKey:<storage-account-key> /S
    ```

    På Linux är kommandosyntaxen lite annorlunda:

    ```
    azcopy --source <path-to-local-share> --destination https://<storage-account>.file.core.windows.net/<file-share>/ --dest-key <storage-account-key> --recursive
    ```

    AzCopy har ett stort antal alternativ för att ändra kopieringsbeteendet som önskat. Mer information finns i [AzCopy på Windows](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) och [AzCopy på Linux](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="automatically-mount-on-needed-pcsservers"></a>Montera datorer/servrar automatiskt
Om du vill ersätta en lokal filresurs är det bra att förmontera resurserna på de datorer som den ska användas på. Detta kan göras automatiskt på en lista över maskiner.

> [!Note]  
> Montering av en Azure-filresurs kräver att du använder lagringskontonyckeln som lösenord, därför rekommenderar vi endast montering i betrodda miljöer. 

### <a name="windows"></a>Windows
PowerShell kan användas för att köra monteringskommandot på flera datorer. I följande exempel `$computers` fylls den manuellt i, men du kan skapa en lista över datorer som ska monteras automatiskt. Du kan till exempel fylla i den här variabeln med resultat från Active Directory.

```powershell
$computer = "MyComputer1", "MyComputer2", "MyComputer3", "MyComputer4"
$computer | ForEach-Object { Invoke-Command -ComputerName $_ -ScriptBlock { net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name> /PERSISTENT:YES } }
```

### <a name="linux"></a>Linux
Ett enkelt bash-skript i kombination med SSH kan ge samma resultat i följande exempel. Variabeln `$computer` lämnas på samma sätt att fyllas i av användaren:

```
computer = ("MyComputer1" "MyComputer2" "MyComputer3" "MyComputer4")
for item in "${computer[@]}"
do
    ssh $item "sudo bash -c 'echo \"//<storage-account-name>.file.core.windows.net/<share-name> /mymountpoint cifs vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino\" >> /etc/fstab'", "sudo mount -a"
done
```

## <a name="next-steps"></a>Nästa steg
- [Planera för en Azure File Sync-distribution](storage-sync-files-planning.md)
- [Felsöka Azure Files på Windows](storage-troubleshoot-windows-file-connection-problems.md)
- [Felsöka Azure Files på Linux](storage-troubleshoot-linux-file-connection-problems.md)
