---
title: Hur du distribuerar Azure-filer | Microsoft Docs
description: "Lär dig hur du distribuerar Azure-filer från början till slut."
services: storage
documentationcenter: 
author: wmgries
manager: klaasl
editor: jgerend
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2017
ms.author: wgries
ms.openlocfilehash: a594f31c002556f9a5fddaa17fb19273065eed47
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-deploy-azure-files"></a>Så här distribuerar du Azure Files
[Azure Files](storage-files-introduction.md) erbjuder fullständigt hanterade filresurser i molnet som är tillgängliga via SMB standardprotokoll. Den här artikeln visar hur du distribuerar praktiskt taget Azure-filer i din organisation.

Vi rekommenderar starkt att läsa [planera för distribution av en Azure-filer](storage-files-planning.md) innan du följer instruktionerna i den här artikeln.

## <a name="prerequisites"></a>Krav
Den här artikeln förutsätter att du redan har slutfört följande steg:

- Skapa ett Azure Storage-konto med önskade återhämtning och kryptering alternativ, i den region som du önskar. Se [skapa ett Lagringskonto](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) för stegvisa anvisningar om hur du skapar ett Lagringskonto.
- Skapa en Azure-filresurs med din önskade kvot i ditt Lagringskonto. Se [skapa en filresurs](storage-how-to-create-file-share.md) för stegvisa anvisningar om hur du skapar en filresurs.

## <a name="transfer-data-into-azure-files"></a>Överföra data till Azure-filer
Du kanske vill migrera befintliga filresurser, till exempel de lagrade lokalt, till din nya Azure-filresurs. Det här avsnittet visar hur du flyttar data till en Azure-fil dela via flera populära metoderna som beskrivs från den [Planeringsguiden](storage-files-planning.md#data-transfer-method)

### <a name="azure-file-sync-preview"></a>Azure filsynkronisering (förhandsgranskning)
Azure filsynkronisering (förhandsversion) kan du centralisera din organisations filresurser i Azure-filer utan att ge flexibilitet, prestanda och kompatibilitet för en lokal filserver. Detta möjliggörs genom att Windows-servern omvandlas till ett snabbt cacheminne för Azure-filresursen. Du kan använda alla protokoll som är tillgängliga på Windows Server för att komma åt data lokalt (inklusive SMB, NFS och FTPS) och du kan ha så många cacheminnen som du behöver över hela världen.

Azure filsynkronisering kan användas för att migrera data till en Azure-filresurs även eventuellt mekanismen för synkronisering inte för långsiktig användning. Mer information om hur du använder Azure filsynkronisering för att överföra data till Azure-filresurs kan hittas i [planera för distribution av en Azure filsynkronisering](storage-sync-files-planning.md) och [hur du distribuerar Azure filsynkronisering](storage-sync-files-deployment-guide.md).

### <a name="azure-importexport"></a>Import/export i Azure
Tjänsten Azure Import/Export kan du säkert överföra stora mängder data i en Azure-filresurs med leverans hårddiskar till ett Azure-datacenter. Se [använda tjänsten Microsoft Azure Import/Export för att överföra data till Azure storage](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) för en mer detaljerad översikt över tjänsten.

> [!Note]  
> Tjänsten Azure Import/Export stöder inte export av filer från en Azure-filresurs just nu.

Följande steg importera data från en lokal plats till Azure-filresurs.

1. Ange antalet hårddiskar för att e-post som krävs i Azure. Hårddiskar kan vara av valfri diskstorlek, men måste vara antingen en 2,5-tums eller 3,5-tums SSD eller stöder standarden SATA II eller III SATA-Hårddisk. 

2. Anslut och montera varje disk på samma dator eller det server gör dataöverföringen. För optimala prestanda rekommenderar vi körs lokalt exportjobb lokalt på den server som innehåller data. I vissa fall, till exempel när den filserver som hanterar data är en NAS-enhet kan det inte vara möjlig. I så fall är det perfekt acceptabelt att montera varje disk på en dator.

3. Se till att varje enhet är online, initierats och tilldelas en enhetsbeteckning. Ta en enhet online, initiera och tilldela en enhetsbeteckning, öppna snapin-modulen MMC-Diskhantering (diskmgmt.msc).

    - Aktivera en disk online (om den inte är redan online), högerklicka på disken i rutan längst ned i MMC-Diskhantering och välj ”Online”.
    - Högerklicka på disken i nedre fönstret (när disken är online), och välj ”initiera” för att initiera en disk. Se till att markera ”GUID” när du tillfrågas.

        ![En skärmbild av menyn initiera Disk i MMC-Diskhantering](media/storage-files-deployment-guide/transferdata-importexport-1.PNG)

    - Högerklicka på det ”lediga” utrymmet på disken online och initierade om du vill tilldela en enhetsbeteckning till disken och klicka på ”Ny enkel volym”. Detta kan du tilldela enhetsbeteckning. Observera att du inte behöver formatera volymen som detta görs senare.

        ![En skärmbild av guiden Ny enkel volym i MMC-Diskhantering](media/storage-files-deployment-guide/transferdata-importexport-2.png)

4. Skapa dataset CSV-fil. Dataset CSV-fil är en mappning mellan sökvägen till den data lokalt och önskade Azure-filresursen data ska kopieras till. Följande dataset CSV-filen mappas till exempel en lokal resurs (”F:\shares\scratch”) till en Azure-filresurs (”MyAzureFileShare”):
    
    ```
    BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
    "F:\shares\scratch\","MyAzureFileShare/",file,rename,"None",None
    ```

    Du kan ange flera resurser med ett Lagringskonto. Se [förbereda dataset CSV-filen](../common/storage-import-export-tool-preparing-hard-drives-import.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#prepare-the-dataset-csv-file) för mer information.

5. Skapa driveset CSV-fil. CSV-fil driveset visar diskarna som är tillgängliga för export-agenten lokalt. Till exempel följande driveset CSV-filen listor `X:`, `Y:`, och `Z:` enheter som ska användas i lokalt exportera jobb:

    ```
    DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
    X,Format,SilentMode,Encrypt,
    Y,Format,SilentMode,Encrypt,
    Z,Format,SilentMode,Encrypt,
    ```
    
    Se [förbereda driveset CSV-filen](../common/storage-import-export-tool-preparing-hard-drives-import.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#prepare-initialdriveset-or-additionaldriveset-csv-file) för mer information.

6. Använd den [WAImportExport verktyget](http://download.microsoft.com/download/3/6/B/36BFF22A-91C3-4DFC-8717-7567D37D64C5/WAImportExport.zip) att kopiera data till en eller flera hårddiskar.

    ```
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
    ```

    > [!Warning]  
    > Ändra inte data på hårddiskar eller journal-fil när du har slutfört förberedelse av disken.

7. [Skapa ett importjobb](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#create-an-export-job).
    
### <a name="robocopy"></a>ROBOCOPY
ROBOCOPY är ett välkänt kopiera verktyg som levereras med Windows och Windows Server. ROBOCOPY kan användas för att överföra data till Azure-filer genom att montera filresursen lokalt och sedan använda monterade platsen som mål i kommandot Robocopy. Använder Robocopy är ganska enkel:

1. [Montera filresursen Azure](storage-how-to-use-files-windows.md). För optimala prestanda rekommenderar vi att montera filresursen Azure lokalt på den server som innehåller data. I vissa fall, till exempel när den filserver som hanterar data är en NAS-enhet kan det inte vara möjlig. I så fall är det perfekt acceptabelt att montera Azure-filresurs på en dator. I det här exemplet `net use` används på kommandoraden för att montera filresursen:

    ```
    net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
    ```

2. Använd `robocopy` på kommandoraden för att flytta data till Azure-filresursen:

    ```
    robocopy <path-to-local-share> <path-to-azure-file-share> /E /Z /MT:32
    ```
    
    ROBOCOPY har ett stort antal alternativ för att ändra funktionssättet för kopia efter behov. Mer information finns i [Robocopy](https://technet.microsoft.com/library/cc733145.aspx) manuella.

### <a name="azcopy"></a>AzCopy
AzCopy är ett kommandoradsverktyg som utformats för att kopiera data till och från Azure-filer, samt Azure Blob storage med hjälp av enkla kommandon med optimal prestanda. Det är enkelt att använda AzCopy:

1. Hämta den [senaste versionen av AzCopy på Windows](http://aka.ms/downloadazcopy) eller [Linux](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#download-and-install-azcopy).
2. Använd `azcopy` på kommandoraden för att flytta data till Azure-filresursen. Syntaxen i Windows är följande: 

    ```
    azcopy /Source:<path-to-local-share> /Dest:https://<storage-account>.file.core.windows.net/<file-share>/ /DestKey:<storage-account-key> /S
    ```

    Kommandosyntaxen är lite annorlunda på Linux:

    ```
    azcopy --source <path-to-local-share> --destination https://<storage-account>.file.core.windows.net/<file-share>/ --dest-key <storage-account-key> --recursive
    ```

    AzCopy har ett stort antal alternativ för att ändra funktionssättet för kopia efter behov. Mer information [AzCopy på Windows](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) och [AzCopy på Linux](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="automatically-mount-on-needed-pcsservers"></a>Montera automatiskt på nödvändiga datorer /-servrar
Om du vill ersätta en lokal resurs, är det bra att montera före resurser på de datorer som kommer att användas på. Detta kan ske automatiskt på en lista med datorer.

> [!Note]  
> Montera en filresurs på Azure kräver lagringskontots åtkomstnyckel som lösenord, rekommenderar vi därför bara montera i betrodda miljöer. 

### <a name="windows"></a>Windows
PowerShell kan användas för att köra monteringskommandot på flera datorer. I följande exempel `$computers` fylls manuellt, men du kan skapa en lista med datorer att montera automatiskt. Exempelvis kan du fylla i den här variabeln med resultat från Active Directory.

```PowerShell
$computer = "MyComputer1", "MyComputer2", "MyComputer3", "MyComputer4"
$computer | ForEach-Object { Invoke-Command -ComputerName $_ -ScriptBlock { net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name> /PERSISTENT:YES } }
```

### <a name="linux"></a>Linux
Ett enkelt bash-skript som kombineras med SSH kan ge samma resultat i följande exempel. Den `$computer` variabeln på samma sätt som återstår för att fyllas av användaren:

```PowerShell
computer = ("MyComputer1" "MyComputer2" "MyComputer3" "MyComputer4")
for item in "${dur[@]}"
do
    ssh $item "sudo bash -c 'echo \"//<storage-account-name>.file.core.windows.net/<share-name> /mymountpoint cifs vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino\" >> /etc/fstab'", "sudo mount -a"
done
```

## <a name="next-steps"></a>Nästa steg
- [Planera för distribution av en Azure filsynkronisering](storage-sync-files-planning.md)
- [Felsöka Azure filer i Windows](storage-troubleshoot-windows-file-connection-problems.md)
- [Felsöka Azure filer på Linux](storage-troubleshoot-linux-file-connection-problems.md)