---
title: Azure Data Box-Disk felsökning dataöverföring problem med loggar | Microsoft Docs
description: Beskriver hur du använder loggar och felsöka problem som kan uppstå när du överför data till Azure Data Box-Disk.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/17/2019
ms.author: alkohli
ms.openlocfilehash: deaa9a220ee4d765650779b40742225e300ffdb7
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807503"
---
# <a name="understand-logs-to-troubleshoot-data-upload-issues-in-azure-data-box-disk"></a>Förstå loggar att felsöka uppladdningsproblem för data i Azure Data Box-Disk

Den här artikeln gäller för Microsoft Azure Data Box-Disk och beskriver de problem som du kan se när du överför data till Azure.

## <a name="about-upload-logs"></a>Om hur du överför loggar

När data överförs till Azure i datacentret, `_error.xml` och `_verbose.xml` filer har genererats för varje lagringskonto. Dessa loggar överförs till samma lagringskonto som används för att ladda upp data. 

Båda loggarna finns i samma format och innehålla XML-beskrivningar av de händelser som inträffade när du kopierar data från disken till Azure Storage-kontot.

Utförliga loggen innehåller fullständig information om status för kopieringsåtgärden för varje blob eller fillagring, felloggen innehåller endast information för BLOB-objekt eller filer som påträffade fel under överföringen.

Felloggen har samma struktur som utförlig loggning för, men filtrerar bort lyckade åtgärder.

## <a name="download-logs"></a>Ladda ned loggar

Vidta följande steg för att hitta ladda upp loggarna.

1. Om det finns några fel när du överför data till Azure, visar portalen en sökväg till den mapp där diagnostikloggar finns.

    ![Länka till loggar i portalen](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs.png)

2. Gå till **waies**.

    ![fel- och utförliga loggar](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs-1.png)

I båda fallen kan se du felloggarna och utförliga loggar. Markera varje logg och hämta en lokal kopia.

## <a name="sample-upload-logs"></a>Exemplet överför loggar

Ett exempel på den `_verbose.xml` visas nedan. I det här fallet har ordningen som slutförts utan fel.

```xml

<?xml version="1.0" encoding="utf-8"?>
<DriveLog Version="2018-10-01">
  <DriveId>184020D95632</DriveId>
  <Blob Status="Completed">
    <BlobPath>$root/botetapageblob.vhd</BlobPath>
    <FilePath>\PageBlob\botetapageblob.vhd</FilePath>
    <Length>1073742336</Length>
    <ImportDisposition Status="Created">rename</ImportDisposition>
    <PageRangeList>
      <PageRange Offset="0" Length="4194304" Status="Completed" />
      <PageRange Offset="4194304" Length="4194304" Status="Completed" />
      <PageRange Offset="8388608" Length="4194304" Status="Completed" />
      --------CUT-------------------------------------------------------
      <PageRange Offset="1061158912" Length="4194304" Status="Completed" />
      <PageRange Offset="1065353216" Length="4194304" Status="Completed" />
      <PageRange Offset="1069547520" Length="4194304" Status="Completed" />
      <PageRange Offset="1073741824" Length="512" Status="Completed" />
    </PageRangeList>
  </Blob>
  <Blob Status="Completed">
    <BlobPath>$root/botetablockblob.txt</BlobPath>
    <FilePath>\BlockBlob\botetablockblob.txt</FilePath>
    <Length>19</Length>
    <ImportDisposition Status="Created">rename</ImportDisposition>
    <BlockList>
      <Block Offset="0" Length="19" Status="Completed" />
    </BlockList>
  </Blob>
  <File Status="Completed">
    <FileStoragePath>botetaazurefilesfolder/botetaazurefiles.txt</FileStoragePath>
    <FilePath>\AzureFile\botetaazurefilesfolder\botetaazurefiles.txt</FilePath>
    <Length>20</Length>
    <ImportDisposition Status="Created">rename</ImportDisposition>
    <FileRangeList>
      <FileRange Offset="0" Length="20" Status="Completed" />
    </FileRangeList>
  </File>
  <Status>Completed</Status>
</DriveLog>
```

För samma ordning, ett exempel på den `_error.xml` visas nedan.

```xml

<?xml version="1.0" encoding="utf-8"?>
<DriveLog Version="2018-10-01">
  <DriveId>184020D95632</DriveId>
  <Summary>
    <ValidationErrors>
      <None Count="3" />
    </ValidationErrors>
    <CopyErrors>
      <None Count="3" Description="No errors encountered" />
    </CopyErrors>
  </Summary>
  <Status>Completed</Status>
</DriveLog>
```

Ett exempel på den `_error.xml` visas nedan var den ordning som slutfördes med fel. 

Fel-filen i det här fallet har en `Summary` och ett annat avsnitt som innehåller alla filen nivå fel. 

Den `Summary` innehåller den `ValidationErrors` och `CopyErrors`. I det här fallet 8 filer eller mappar har överförts till Azure och det fanns inga valideringsfel. När data har kopierats till Azure Storage-konto, 5 filer eller mappar har laddats upp. De återstående 3 filer eller mappar har bytt namn enligt namngivningskonventionerna i Azure container och överförts till Azure.

Statusen för filen finns i `BlobStatus` som beskriver eventuella åtgärder som vidtas för att ladda upp blobar. I det här fallet är tre behållare namn eftersom de mappar där data har kopierats inte överensstämde med namngivningskonventionerna i Azure för behållare. För blobarna som laddades upp i de här behållarna, ingår nya behållarens namn, sökväg för blob i Azure, ursprungliga ogiltig filsökväg och blob-storlek.
    
```xml
 <?xml version="1.0" encoding="utf-8"?>
    <DriveLog Version="2018-10-01">
      <DriveId>18041C582D7E</DriveId>
      <Summary>
     <!--Summary for validation and data copy to Azure -->
        <ValidationErrors>
          <None Count="8" />
        </ValidationErrors>
        <CopyErrors>
          <Completed Count="5" Description="No errors encountered" />
          <ContainerRenamed Count="3" Description="Renamed the container as the original container name does not follow Azure conventions." />
        </CopyErrors>
      </Summary>
    <!--List of renamed containers with the new names, new file path in Azure, original invalid file path, and size -->
      <Blob Status="ContainerRenamed">
        <BlobPath>databox-c2073fd1cc379d83e03d6b7acce23a6cf29d1eef/private.vhd</BlobPath>
        <OriginalFilePath>\PageBlob\pageblob test\private.vhd</OriginalFilePath>
        <SizeInBytes>10490880</SizeInBytes>
      </Blob>
      <Blob Status="ContainerRenamed">
        <BlobPath>databox-c2073fd1cc379d83e03d6b7acce23a6cf29d1eef/resource.vhd</BlobPath>
        <OriginalFilePath>\PageBlob\pageblob test\resource.vhd</OriginalFilePath>
        <SizeInBytes>71528448</SizeInBytes>
      </Blob>
      <Blob Status="ContainerRenamed">
        <BlobPath>databox-c2073fd1cc379d83e03d6b7acce23a6cf29d1eef/role.vhd</BlobPath>
        <OriginalFilePath>\PageBlob\pageblob test\role.vhd</OriginalFilePath>
        <SizeInBytes>10490880</SizeInBytes>
      </Blob>
      <Status>CompletedWithErrors</Status>
    </DriveLog>
```

## <a name="data-upload-errors"></a>Dataöverföringsfel

Fel som har genererats när du överför data till Azure sammanfattas i tabellen nedan.

| Felkod | Beskrivning                   |
|-------------|------------------------------|
|`None` |  Har slutförts.           |
|`Renamed` | Har bytt namn på blob.   |
|`CompletedWithErrors` | Överföringen slutfördes med fel. Information om filerna i fel ingår i loggfilen.  |
|`Corrupted`|CRC beräknas vid datainmatning matchar inte det CRC-värde som beräknats under överföringen.  |  
|`StorageRequestFailed` | Azure storage-begäran misslyckades.   |     
|`LeasePresent` | Det här objektet hyrs och används av en annan användare. |
|`StorageRequestForbidden` |Det gick inte att ladda upp på grund av problem med autentisering. |
|`ManagedDiskCreationTerminalFailure` | Det gick inte att ladda upp som hanterade diskar. Filerna är tillgängliga i mellanlagringskontot som sidblobar. Du kan manuellt konvertera sidblobar till hanterade diskar.  |
|`DiskConversionNotStartedTierInfoMissing` | Eftersom VHD-filen har kopierats utanför mapparna införande nivå, inte har en hanterad disk skapats. Filen har överförts som sidblobb till mellanlagringskontot som anges under skapande. Du kan konvertera den manuellt till en hanterad disk.|
|`InvalidWorkitem` | Det gick inte att ladda upp data eftersom den inte följer Azure namngivning och begränsar konventioner.|
|`InvalidPageBlobUploadAsBlockBlob` | Laddas upp som blockblobar i en behållare med prefixet `databoxdisk-invalid-pb-`.|
|`InvalidAzureFileUploadAsBlockBlob` | Laddas upp som blockblobar i en behållare med prefixet `databoxdisk-invalid-af`-.|
|`InvalidManagedDiskUploadAsBlockBlob` | Laddas upp som blockblobar i en behållare med prefixet `databoxdisk-invalid-md`-.|
|`InvalidManagedDiskUploadAsPageBlob` |Laddas upp som sidblobar i en behållare med prefixet `databoxdisk-invalid-md-`. |
|`MovedToOverflowShare` |Uppladdade filer till en ny filresurs som den ursprungliga filresursens storleken överskred den maximala storleksgränsen för Azure. Namn på ny filresurs har det ursprungliga namnet på med `-2`.   |
|`MovedToDefaultAzureShare` |Överförda filer som inte var en del av en mapp till en standardresurs som. Resursnamnet som börjar med `databox-`. |
|`ContainerRenamed` |Behållare för de här filerna överensstämmer med inte namngivningskonventionerna Azure och har bytt namn. Det nya namnet börjar med `databox-` och suffix med SHA1-hashen för det ursprungliga namnet |
|`ShareRenamed` |Resurs för de här filerna överensstämmer med inte namngivningskonventionerna Azure och har bytt namn. Det nya namnet börjar med `databox-` och suffix med SHA1-hashen för det ursprungliga namnet. |
|`BlobRenamed` |De här filerna överensstämmer med inte namngivningskonventionerna Azure och byter namn. Kontrollera den `BlobPath` för det nya namnet. |
|`FileRenamed` |De här filerna överensstämmer med inte namngivningskonventionerna Azure och byter namn. Kontrollera den `FileStoragePath` för det nya namnet. |
|`DiskRenamed` |De här filerna överensstämmer med inte namngivningskonventionerna Azure och byter namn. Kontrollera den `BlobPath` för det nya namnet. |


## <a name="next-steps"></a>Nästa steg

- [Öppna ett supportärende för Data Box-Disk utfärdar](data-box-disk-contact-microsoft-support.md).
