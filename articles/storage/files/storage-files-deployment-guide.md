---
title: Så här distribuerar du Azure Files | Microsoft Docs
description: Lär dig hur du distribuerar Azure Files från början till slut. Överför data till Azure Files. Montera automatiskt på datorer eller servrar som behövs.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 05/22/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 53111ccd634c516d0db10c0e2dd41768aba43f41
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2020
ms.locfileid: "94629248"
---
# <a name="how-to-deploy-azure-files"></a>Så här distribuerar du Azure Files
[Azure Files](storage-files-introduction.md) erbjuder fullständigt hanterade fil resurser i molnet som är tillgängliga via SMB-protokollet enligt bransch standard. I den här artikeln får du se hur du praktiskt taget distribuerar Azure Files i din organisation.

Vi rekommenderar starkt att du [planerar att läsa en Azure Files distribution](storage-files-planning.md) innan du följer stegen i den här artikeln.

## <a name="prerequisites"></a>Förutsättningar
Den här artikeln förutsätter att du redan har slutfört följande steg:

- Skapade ett Azure Storage-konto med önskade återhämtnings-och krypterings alternativ i den region som du vill använda. Se [skapa ett lagrings konto](../common/storage-account-create.md?toc=%252fazure%252fstorage%252ffiles%252ftoc.json) för steg-för-steg-instruktioner om hur du skapar ett lagrings konto.
- Skapade en Azure-filresurs med önskad kvot i ditt lagrings konto. Se [skapa en fil resurs](storage-how-to-create-file-share.md) för steg-för-steg-instruktioner om hur du skapar en fil resurs.

## <a name="transfer-data-into-azure-files"></a>Överför data till Azure Files
Du kanske vill migrera befintliga fil resurser, till exempel de som lagras lokalt, till din nya Azure-filresurs. I det här avsnittet visas hur du flyttar data till en Azure-filresurs via flera populära metoder som beskrivs i [planerings guiden](storage-files-planning.md#migration)

### <a name="azure-file-sync"></a>Azure File Sync
Med Azure File Sync kan du centralisera din organisations filresurser i Azure Files med samma flexibilitet, prestanda och kompatibilitet som du får om du använder en lokal filserver. Det gör du genom att omvandla dina Windows-servrar till ett snabbt cacheminne för Azure-filresursen. Du kan använda alla protokoll som är tillgängliga på Windows Server för att komma åt data lokalt (inklusive SMB, NFS och FTPS) och du kan ha så många cacheminnen som du behöver över hela världen.

Azure File Sync kan användas för att migrera data till en Azure-filresurs, även om metoden för synkronisering inte behövs för långsiktig användning. Mer information om hur du använder Azure File Sync för att överföra data till Azure-filresurs finns i [Planera för en Azure File Sync distribution](storage-sync-files-planning.md) och [hur du distribuerar Azure File Sync](storage-sync-files-deployment-guide.md).

### <a name="azure-importexport"></a>Azure Import/Export
Med tjänsten Azure import/export kan du på ett säkert sätt överföra stora mängder data till en Azure-filresurs genom att leverera hård diskar till ett Azure-datacenter. Se [använda tjänsten Microsoft Azure import/export för att överföra data till Azure Storage](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) för en mer detaljerad översikt över tjänsten.

> [!Note]  
> Tjänsten Azure import/export stöder inte export av filer från en Azure-filresurs just nu.

Följande steg kommer att importera data från en lokal plats till Azure-filresursen.

1. Införskaffa antalet hård diskar som krävs för e-post till Azure. Hård diskar kan vara av valfri disk storlek, men måste vara antingen 2,5 "eller 3,5" SSD eller HDD som stöder SATA II-eller SATA III-standarden. 

2. Anslut och montera varje disk på servern/datorn som utför data överföringen. För optimala prestanda rekommenderar vi att du kör det lokala export jobbet lokalt på den server som innehåller data. I vissa fall, till exempel när den fil server som hanterar data är en NAS-enhet, är det inte säkert att det är möjligt. I så fall är det perfekt att montera varje disk på en dator.

3. Se till att varje enhet är online, initierad och tilldelas en enhets beteckning. Om du vill ta en enhet online, initiera och tilldela en enhets beteckning öppnar du MMC-snapin-modulen disk hantering (diskmgmt. msc).

    - Om du vill ta en disk online (om den inte redan är online) högerklickar du på disken i det nedre fönstret i MMC-modulen disk hantering och väljer "online".
    - Om du vill initiera en disk högerklickar du på disken i det nedre fönstret (när disken är online) och väljer initiera. Se till att välja "GPT" när du uppmanas till detta.

        ![En skärm bild av menyn initiera disk i MMC för disk hantering](media/storage-files-deployment-guide/transferdata-importexport-1.PNG)

    - Om du vill tilldela en enhets beteckning till disken högerklickar du på det "oallokerat" utrymmet på online-sidan och den initierade disken och klickar på ny enkel volym. På så sätt kan du tilldela enhets beteckning. Observera att du inte behöver formatera volymen eftersom detta görs senare.

        ![En skärm bild av guiden Ny enkel volym i MMC för disk hantering](media/storage-files-deployment-guide/transferdata-importexport-2.png)

4. Skapa data uppsättningens CSV-fil. Data uppsättningens CSV-fil är en mappning mellan sökvägen till data lokalt och önskad Azure-filresurs. data ska kopieras till. Till exempel mappar följande CSV-fil en lokal fil resurs ("F:\shares\scratch") till en Azure-fil resurs ("MyAzureFileShare"):
    
    ```
    BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
    "F:\shares\scratch\","MyAzureFileShare/",file,rename,"None",None
    ```

    Du kan ange flera resurser med ett lagrings konto. Mer information finns i [förbereda CSV-filen för data mängden](/previous-versions/azure/storage/common/storage-import-export-tool-preparing-hard-drives-import?toc=%252fazure%252fstorage%252ffiles%252ftoc.json) .

5. Skapa CSV-filen driveset. Driveset CSV-filen listar de diskar som är tillgängliga för den lokala export agenten. Till exempel innehåller följande driveset CSV-fil listor `X:` , `Y:` och `Z:` enheter som ska användas i det lokala export jobbet:

    ```
    DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
    X,Format,SilentMode,Encrypt,
    Y,Format,SilentMode,Encrypt,
    Z,Format,SilentMode,Encrypt,
    ```
    
    Mer information finns i [förbereda CSV-filen för driveset](/previous-versions/azure/storage/common/storage-import-export-tool-preparing-hard-drives-import?toc=%252fazure%252fstorage%252ffiles%252ftoc.json) .

6. Använd [WAImportExport-verktyget](https://www.microsoft.com/download/details.aspx?id=55280) för att kopiera dina data till en eller flera hård diskar.

    ```
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
    ```

    > [!Warning]  
    > Ändra inte data på hård diskarna eller journal filen när du har slutfört disk förberedelsen.

7. [Skapa ett import jobb](../common/storage-import-export-data-to-files.md#step-2-create-an-import-job).
    
### <a name="robocopy"></a>Robocopy
Robocopy är ett välkänt kopieringsverktyg som levereras med Windows och Windows Server. Robocopy kan användas för att överföra data till Azure Files genom att montera fil resursen lokalt och sedan använda den monterade platsen som mål i Robocopy-kommandot. Det är ganska enkelt att använda Robocopy:

1. [Montera Azure-filresursen](storage-how-to-use-files-windows.md). För optimala prestanda rekommenderar vi att du monterar Azure-filresursen lokalt på den server som innehåller data. I vissa fall, till exempel när den fil server som hanterar data är en NAS-enhet, är det inte säkert att det är möjligt. I så fall är det perfekt att montera Azure-filresursen på en dator. I det här exemplet `net use` används på kommando raden för att montera fil resursen:

    ```
    net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
    ```

2. Använd `robocopy` på kommando raden för att flytta data till Azure-filresursen:

    ```
    robocopy <path-to-local-share> <path-to-azure-file-share> /E /Z /MT:32
    ```
    
    Robocopy har ett stort antal alternativ för att ändra kopierings beteendet efter behov. Mer information finns på sidan med manuella [Robocopy](/windows-server/administration/windows-commands/robocopy) .

### <a name="azcopy"></a>AzCopy
AzCopy är ett kommando rads verktyg som utformats för att kopiera data till och från Azure Files, samt Azure Blob Storage med hjälp av enkla kommandon med optimala prestanda. Det är enkelt att använda AzCopy:

1. Ladda ned den [senaste versionen av AzCopy på Windows](https://aka.ms/downloadazcopy) eller [Linux](../common/storage-use-azcopy-v10.md?toc=/azure/storage/files/toc.json#download-azcopy).
2. Använd `azcopy` på kommando raden för att flytta data till Azure-filresursen. Syntaxen i Windows är följande: 

    ```
    azcopy /Source:<path-to-local-share> /Dest:https://<storage-account>.file.core.windows.net/<file-share>/ /DestKey:<storage-account-key> /S
    ```

    På Linux är kommandosyntaxen lite annorlunda:

    ```
    azcopy --source <path-to-local-share> --destination https://<storage-account>.file.core.windows.net/<file-share>/ --dest-key <storage-account-key> --recursive
    ```

    AzCopy har ett stort antal alternativ för att ändra kopierings beteendet efter behov. Mer information finns i [Kom igång med AZCopy](../common/storage-use-azcopy-v10.md?toc=%252fazure%252fstorage%252ffiles%252ftoc.json).

## <a name="automatically-mount-on-needed-pcsservers"></a>Montera automatiskt på nödvändiga datorer/servrar
För att ersätta en lokal fil resurs är det bra att Förmontera resurserna på de datorer som ska användas. Detta kan göras automatiskt på en lista med datorer.

> [!Note]  
> Att montera en Azure-filresurs kräver att du använder lagrings konto nyckeln som lösen ord, och därför rekommenderar vi bara montering i betrodda miljöer. 

### <a name="windows"></a>Windows
PowerShell kan användas för att köra monterings kommandot på flera datorer. I följande exempel `$computers` fylls manuellt i, men du kan skapa en lista med datorer som ska monteras automatiskt. Du kan till exempel fylla i den här variabeln med resultat från Active Directory.

```powershell
$computer = "MyComputer1", "MyComputer2", "MyComputer3", "MyComputer4"
$computer | ForEach-Object { Invoke-Command -ComputerName $_ -ScriptBlock { net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name> /PERSISTENT:YES } }
```

### <a name="linux"></a>Linux
Ett enkelt bash-skript kombinerat med SSH kan ge samma resultat i följande exempel. `$computer`Variabeln lämnas på samma sätt som användaren fyller i:

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
- [Felsöka Azure Files på Linux](storage-troubleshoot-linux-file-connection-problems.md)