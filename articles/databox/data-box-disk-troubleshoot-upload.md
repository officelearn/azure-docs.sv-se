---
title: Felsöka datauppladdningar med hjälp av loggar
titleSuffix: Azure Data Box Disk
description: Beskriver hur du använder loggarna och felsöker problem som visas när du överför data till Azure Data Box Disk.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/17/2019
ms.author: alkohli
ms.openlocfilehash: 7c14988706ef193ef5da868c55f6c4f55e7d98f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260142"
---
# <a name="understand-logs-to-troubleshoot-data-upload-issues-in-azure-data-box-disk"></a>Förstå loggar för att felsöka problem med dataöverföring i Azure Data Box Disk

Den här artikeln gäller Microsoft Azure Data Box Disk och beskriver de problem som visas när du överför data till Azure.

## <a name="about-upload-logs"></a>Om uppladdningsloggar

När data överförs till Azure i `_error.xml` datacentret och `_verbose.xml` filer genereras för varje lagringskonto. Dessa loggar överförs till samma lagringskonto som användes för att ladda upp data. 

Båda loggarna är i samma format och innehåller XML-beskrivningar av de händelser som inträffade när data kopierades från disken till Azure Storage-kontot.

Den utförliga loggen innehåller fullständig information om status för kopieringen för varje blob eller fil, medan felloggen bara innehåller information för blobbar eller filer som påträffade fel under överföringen.

Felloggen har samma struktur som den utförliga loggen, men filtrerar bort lyckade åtgärder.

## <a name="download-logs"></a>Ladda ned loggar

Gör så här för att hitta uppladdningsloggarna.

1. Om det finns några fel när du överför data till Azure visar portalen en sökväg till mappen där diagnostikloggarna finns.

    ![Länk till loggar i portalen](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs.png)

2. Gå till **waies**.

    ![fel och utförliga loggar](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs-1.png)

I varje enskilt fall ser du felloggarna och de utförliga loggarna. Välj varje logg och hämta en lokal kopia.

## <a name="sample-upload-logs"></a>Exempel på uppladdningsloggar

Ett prov `_verbose.xml` av visas nedan. I det här fallet har ordern slutförts utan fel.

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

För samma ordning visas ett `_error.xml` urval av nedan.

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

Ett exempel `_error.xml` på visas nedan där ordern slutfördes med fel. 

Felfilen i det här `Summary` fallet har ett avsnitt och ett annat avsnitt som innehåller alla fel på filnivå. 

Innehåller `Summary` `ValidationErrors` och `CopyErrors`. I det här fallet har 8 filer eller mappar överförts till Azure och det fanns inga valideringsfel. När data kopierades till Azure Storage-konto har 5 filer eller mappar som överförts överförts. De återstående 3 filerna eller mapparna har bytt namn enligt namngivningskonventionerna för Azure-behållaren och sedan överförts till Azure.

Filnivåstatusen finns `BlobStatus` i som beskriver alla åtgärder som vidtas för att ladda upp blobbar. I det här fallet får tre behållare ett nytt namn eftersom mapparna som data kopierades till inte överensstämde med Azure-namngivningskonventionerna för behållare. För blobbar som överförs i dessa behållare inkluderas det nya behållarnamnet, sökvägen till bloben i Azure, den ursprungliga ogiltiga filsökvägen och blob-storleken.
    
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

## <a name="data-upload-errors"></a>Datauppladdningsfel

De fel som genereras när data överförs till Azure sammanfattas i följande tabell.

| Felkod | Beskrivning                   |
|-------------|------------------------------|
|`None` |  Slutförts.           |
|`Renamed` | Bloben har bytt namn.   |
|`CompletedWithErrors` | Uppladdningen har slutförts med fel. Information om filerna av misstag ingår i loggfilen.  |
|`Corrupted`|CRC beräknas under datainmatning matchar inte CRC beräknas under överföringen.  |  
|`StorageRequestFailed` | Azure-lagringsbegäran misslyckades.   |     
|`LeasePresent` | Den här artikeln är uthyrd och används av en annan användare. |
|`StorageRequestForbidden` |Det gick inte att ladda upp på grund av autentiseringsproblem. |
|`ManagedDiskCreationTerminalFailure` | Det gick inte att överföra som hanterade diskar. Filerna är tillgängliga i mellanlagringslagringskontot som sidblobar. Du kan konvertera sidblobar manuellt till hanterade diskar.  |
|`DiskConversionNotStartedTierInfoMissing` | Eftersom VHD-filen kopierades utanför de förskapade nivåmapparna skapades inte en hanterad disk. Filen överförs som sidblobb till mellanlagringslagringskontot som anges när ordern skapades. Du kan konvertera den manuellt till en hanterad disk.|
|`InvalidWorkitem` | Det gick inte att överföra data eftersom de inte överensstämmer med Azure-namngivning och begränsar konventioner.|
|`InvalidPageBlobUploadAsBlockBlob` | Uppladdad som blockblobar i en `databoxdisk-invalid-pb-`behållare med prefix .|
|`InvalidAzureFileUploadAsBlockBlob` | Uppladdad som blockblobar i en `databoxdisk-invalid-af`behållare med prefix -.|
|`InvalidManagedDiskUploadAsBlockBlob` | Uppladdad som blockblobar i en `databoxdisk-invalid-md`behållare med prefix -.|
|`InvalidManagedDiskUploadAsPageBlob` |Uppladdad som sidblobar i en `databoxdisk-invalid-md-`behållare med prefix . |
|`MovedToOverflowShare` |Laddade upp filer till en ny resurs eftersom den ursprungliga resursstorleken överskred den maximala Azure-storleksgränsen. Det nya filresursnamnet har det ursprungliga `-2`namnet som suffixats med .   |
|`MovedToDefaultAzureShare` |Laddade upp filer som inte ingick i någon mapp till en standardresurs. Resursnamnet börjar `databox-`med . |
|`ContainerRenamed` |Behållaren för dessa filer överensstämde inte med Azure-namngivningskonventioner och har bytt namn. Det nya namnet `databox-` börjar med och suffixeras med SHA1-hashen i det ursprungliga namnet |
|`ShareRenamed` |Resursen för dessa filer överensstämde inte med Azure-namngivningskonventioner och har bytt namn. Det nya namnet `databox-` börjar med och suffixeras med SHA1-hashen för det ursprungliga namnet. |
|`BlobRenamed` |Dessa filer överensstämde inte med Azure-namngivningskonventioner och döptes om. Kontrollera `BlobPath` fältet för det nya namnet. |
|`FileRenamed` |Dessa filer överensstämde inte med Azure-namngivningskonventioner och döptes om. Kontrollera `FileStoragePath` fältet för det nya namnet. |
|`DiskRenamed` |Dessa filer överensstämde inte med Azure-namngivningskonventioner och döptes om. Kontrollera `BlobPath` fältet för det nya namnet. |


## <a name="next-steps"></a>Nästa steg

- [Öppna en supportbiljett för problem med databoxdiskar](data-box-disk-contact-microsoft-support.md).
