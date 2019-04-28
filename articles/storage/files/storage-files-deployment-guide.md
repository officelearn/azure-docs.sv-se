---
title: Så här distribuerar du Azure Files | Microsoft Docs
description: Lär dig hur du distribuerar Azure Files från början till slut.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 05/22/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 6afe54d269d273c6a93e6431e9f1c1af7b18cc0e
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766386"
---
# <a name="how-to-deploy-azure-files"></a>Så här distribuerar du Azure Files
[Azure Files](storage-files-introduction.md) erbjuder fullständigt hanterade filresurser i molnet som är tillgängliga via SMB-protokollet som är branschstandard. Den här artikeln visar hur du distribuerar Azure Files praktiskt taget i din organisation.

Vi rekommenderar starkt att du läser [planera för distribution av Azure Files](storage-files-planning.md) innan du följa stegen i den här artikeln.

## <a name="prerequisites"></a>Nödvändiga komponenter
Den här artikeln förutsätter att du redan har slutfört följande steg:

- Skapat ett Azure Storage-konto med önskade återhämtning och kryptering alternativ, i den region som du önskar. Se [skapa ett Lagringskonto](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) för stegvisa instruktioner om hur du skapar ett Lagringskonto.
- Skapa en Azure-filresurs med din önskade kvot i ditt Lagringskonto. Se [skapa en filresurs](storage-how-to-create-file-share.md) för stegvisa instruktioner om hur du skapar en filresurs.

## <a name="transfer-data-into-azure-files"></a>Överföra data till Azure Files
Du kanske vill migrera befintliga filresurser, till exempel de lagrade lokalt, till din nya Azure-filresurs. Det här avsnittet visar hur du flyttar data till en Azure-filresurs via flera populära metoderna som beskrivs från den [Planeringsguiden](storage-files-planning.md#data-transfer-method)

### <a name="azure-file-sync"></a>Azure File Sync
Med Azure File Sync kan du centralisera din organisations filresurser i Azure Files med samma flexibilitet, prestanda och kompatibilitet som du får om du använder en lokal filserver. Det gör du genom att omvandla dina Windows-servrar till ett snabbt cacheminne för din Azure-filresurs. Du kan använda alla protokoll som är tillgängliga på Windows Server för att komma åt data lokalt (inklusive SMB, NFS och FTPS) och du kan ha så många cacheminnen som du behöver över hela världen.

Azure File Sync kan användas till att migrera data till en Azure-filresurs, även om mekanismen för synkronisering inte desired för långvarig användning. Mer information om hur du använder Azure File Sync för att överföra data till Azure-filresurs finns i [planera för distribution av Azure File Sync](storage-sync-files-planning.md) och [så här distribuerar du Azure File Sync](storage-sync-files-deployment-guide.md).

### <a name="azure-importexport"></a>Import/export i Azure
Azure Import/Export-tjänsten kan du på ett säkert sätt överföra stora mängder data till en Azure-filresurs via hårddiskar till ett Azure-datacenter. Se [använder Microsoft Azure Import/Export-tjänsten för att överföra data till Azure storage](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) för en mer detaljerad översikt över tjänsten.

> [!Note]  
> Azure Import/Export-tjänsten stöder inte export av filer från en Azure-filresurs just nu.

Följande steg ska importera data från en lokal plats till Azure-filresursen.

1. Skaffa det begärda antalet hårddiskar till e-post till Azure. Hårddiskar används, vilket kan vara av valfri diskstorlek, men måste vara antingen en 2,5-tums eller 3,5-tums SSD och HDD stöder SATA II eller SATA III standarden. 

2. Anslut och montera varje disk på server/datorn gör dataöverföringen. För optimala prestanda rekommenderar vi kör export-jobbet lokala lokalt på den server som innehåller data. I vissa fall, till exempel när den filserver som hanterar data är en NAS-enhet kanske det inte möjligt. I så fall är det perfekt acceptabelt att montera varje disk på en dator.

3. Se till att varje enhet är online, initierats, och har tilldelats en enhetsbeteckning. Ta med en enhet online, initiera och tilldela en enhetsbeteckning, öppna snapin-modulen MMC-Diskhantering (diskmgmt.msc).

    - Att aktivera en disk online (om det inte är redan online), högerklicka på disken i rutan längst ned i MMC-Diskhantering och välja ”Online”.
    - Högerklicka på disken i den nedre (när disken är online) och väljer ”initiera” för att initiera en disk. Var noga med att välja ”GPT” när du tillfrågas.

        ![En skärmbild av menyn initiera Disk i MMC-Diskhantering](media/storage-files-deployment-guide/transferdata-importexport-1.PNG)

    - Om du vill tilldela en enhetsbeteckning till disken, högerklicka på det ”lediga” utrymmet på disken online och initierade och klicka på ”Ny enkel volym”. Detta kan du tilldela enhetsbeteckning. Observera att du inte behöver formatera volymen som detta görs senare.

        ![En skärmbild av guiden Ny enkel volym i MMC-Diskhantering](media/storage-files-deployment-guide/transferdata-importexport-2.png)

4. Skapa datauppsättning CSV-fil. CSV-filen datauppsättning är en mappning mellan sökvägen till de data lokalt och önskade Azure-filresursen data ska kopieras till. Följande CSV-filen för datauppsättningen mappar till exempel en lokal filresurs (”F:\shares\scratch”) till en Azure-filresurs (”MyAzureFileShare”):
    
    ```
    BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
    "F:\shares\scratch\","MyAzureFileShare/",file,rename,"None",None
    ```

    Du kan ange flera filresurser med ett Storage-konto. Se [förbereda CSV-filen datauppsättning](../common/storage-import-export-tool-preparing-hard-drives-import.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#prepare-the-dataset-csv-file) för mer information.

5. Skapa driveset CSV-fil. Driveset CSV-filen visas diskarna som är tillgängliga för export-agenten lokalt. Till exempel följande driveset CSV fil listor `X:`, `Y:`, och `Z:` enheter som ska användas i lokalt exportjobb:

    ```
    DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
    X,Format,SilentMode,Encrypt,
    Y,Format,SilentMode,Encrypt,
    Z,Format,SilentMode,Encrypt,
    ```
    
    Se [förbereda CSV-filen driveset](../common/storage-import-export-tool-preparing-hard-drives-import.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#prepare-initialdriveset-or-additionaldriveset-csv-file) för mer information.

6. Använd den [WAImportExport verktyget](https://www.microsoft.com/download/details.aspx?id=55280) att kopiera data till en eller flera hårddiskar.

    ```
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
    ```

    > [!Warning]  
    > Ändra inte data på hårddiskar eller journal-fil när du har slutfört förberedelse av disk.

7. [Skapa ett importjobb](../common/storage-import-export-data-to-files.md#step-2-create-an-import-job).
    
### <a name="robocopy"></a>ROBOCOPY
ROBOCOPY är ett välkänt kopiera verktyg som levereras med Windows och Windows Server. ROBOCOPY kan användas för att överföra data till Azure Files genom att montera filresursen lokalt och sedan använda den monterade platsen som mål i Robocopy-kommandot. Det är enkelt att använda Robocopy:

1. [Montera Azure-filresursen](storage-how-to-use-files-windows.md). För optimala prestanda rekommenderar vi att montera Azure-filresurs lokalt på den server som innehåller data. I vissa fall, till exempel när den filserver som hanterar data är en NAS-enhet kanske det inte möjligt. I så fall är det perfekt acceptabelt att montera Azure-filresursen på en dator. I det här exemplet `net use` används på kommandoraden för att montera filresursen:

    ```
    net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
    ```

2. Använd `robocopy` på kommandoraden för att flytta data till Azure-filresursen:

    ```
    robocopy <path-to-local-share> <path-to-azure-file-share> /E /Z /MT:32
    ```
    
    ROBOCOPY har ett stort antal alternativ för att ändra kopieringsbeteendet efter behov. Mer information finns i [Robocopy](https://technet.microsoft.com/library/cc733145.aspx) manuell sidan.

### <a name="azcopy"></a>AzCopy
AzCopy är ett kommandoradsverktyg som utformats för att kopiera data till och från Azure Files, samt Azure Blob storage med hjälp av enkla kommandon med optimala prestanda. Det är enkelt att använda AzCopy:

1. Ladda ned den [senaste versionen av AzCopy på Windows](https://aka.ms/downloadazcopy) eller [Linux](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#download-and-install-azcopy).
2. Använd `azcopy` på kommandoraden för att flytta data till Azure-filresursen. Syntaxen på Windows är följande: 

    ```
    azcopy /Source:<path-to-local-share> /Dest:https://<storage-account>.file.core.windows.net/<file-share>/ /DestKey:<storage-account-key> /S
    ```

    Kommandosyntaxen är lite annorlunda på Linux:

    ```
    azcopy --source <path-to-local-share> --destination https://<storage-account>.file.core.windows.net/<file-share>/ --dest-key <storage-account-key> --recursive
    ```

    AzCopy har ett stort antal alternativ för att ändra kopieringsbeteendet efter behov. Mer information finns [AzCopy på Windows](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) och [AzCopy i Linux](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="automatically-mount-on-needed-pcsservers"></a>Automatiskt montera på nödvändiga datorer/servrar
Om du vill ersätta en lokal filresurs, är det bra att förväg montera resurserna på de datorer som den ska användas på. Detta kan göras automatiskt på en lista med datorer.

> [!Note]  
> Montera en Azure-filresurs kräver att du använder lagringskontonyckeln som lösenord, rekommenderar vi därför bara montera i betrodda miljöer. 

### <a name="windows"></a>Windows
Du kan använda PowerShell kör monteringskommandot på flera datorer. I följande exempel `$computers` fylls manuellt, men du kan generera listan över datorer för att montera automatiskt. Exempelvis kan du fylla i den här variabeln med resultat från Active Directory.

```powershell
$computer = "MyComputer1", "MyComputer2", "MyComputer3", "MyComputer4"
$computer | ForEach-Object { Invoke-Command -ComputerName $_ -ScriptBlock { net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name> /PERSISTENT:YES } }
```

### <a name="linux"></a>Linux
En enkel bash-skript som kombineras med SSH kan ge samma resultat i följande exempel. Den `$computer` variabeln på samma sätt som återstår för att fyllas i av användaren:

```
computer = ("MyComputer1" "MyComputer2" "MyComputer3" "MyComputer4")
for item in "${computer[@]}"
do
    ssh $item "sudo bash -c 'echo \"//<storage-account-name>.file.core.windows.net/<share-name> /mymountpoint cifs vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino\" >> /etc/fstab'", "sudo mount -a"
done
```

## <a name="next-steps"></a>Nästa steg
- [Planera för distribution av Azure File Sync](storage-sync-files-planning.md)
- [Felsöka Azure Files på Windows](storage-troubleshoot-windows-file-connection-problems.md)
- [Felsöka Azure Files i Linux](storage-troubleshoot-linux-file-connection-problems.md)
