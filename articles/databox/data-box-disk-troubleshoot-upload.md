---
title: Azure Data Box-Disk felsökning dataöverföring problem med loggar | Microsoft Docs
description: Beskriver hur du använder loggar och felsöka problem som kan uppstå när du överför data till Azure Data Box-Disk.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/14/2019
ms.author: alkohli
ms.openlocfilehash: 6f3ac38c3eac968bd2f7ec2aada435466d3ff279
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/14/2019
ms.locfileid: "67148314"
---
# <a name="understand-logs-to-troubleshoot-data-upload-issues-in-azure-data-box-disk"></a>Förstå loggar att felsöka uppladdningsproblem för data i Azure Data Box-Disk

Den här artikeln gäller för Microsoft Azure Data Box-Disk och beskriver de problem som du kan se när du överför data till Azure.

## <a name="data-upload-logs"></a>Ladda upp loggar

När data överförs till Azure i datacentret, `_error.xml` och `_verbose.xml` filer genereras. Dessa loggar överförs till samma lagringskonto som används för att ladda upp data. Ett exempel på den `_error.xml` visas nedan.
    
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

## <a name="download-logs"></a>Ladda ned loggar

Det finns två sätt att hitta och hämta diagnostikloggar.

- Om det finns några fel när du överför data till Azure, visar portalen en sökväg till den mapp där diagnostikloggar finns.

    ![Länka till loggar i portalen](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs.png)

- Gå till lagringskontot som associerats med din Data Box-beställning. Gå till **Blob Service > Bläddra efter blobar** och leta efter den blob som motsvarar lagringskontot. Gå till **waies**.

    ![Kopiera loggar 2](./media/data-box-disk-troubleshoot/data-box-disk-copy-logs2.png)

I båda fallen kan se du felloggarna och utförliga loggar. Markera varje logg och hämta en lokal kopia.


## <a name="data-upload-errors"></a>Dataöverföringsfel

Fel som har genererats när du överför data till Azure sammanfattas i tabellen nedan.

| Felkod | Beskrivning                        |
|-------------|------------------------------|
|`None` |  Har slutförts.           |
|`Renamed` | Har bytt namn på blob.  |                                                            |
|`CompletedWithErrors` | Överföringen slutfördes med fel. Information om filerna i fel ingår i loggfilen.  |
|`Corrupted`|CRC beräknas vid datainmatning matchar inte det CRC-värde som beräknats under överföringen.  |  
|`StorageRequestFailed` | Azure storage-begäran misslyckades.   |     |
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
