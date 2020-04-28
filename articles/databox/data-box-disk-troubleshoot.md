---
title: Felsökning av Azure Data Box-disk | Microsoft Docs
description: Beskriver hur du felsöker problem i Azure Data Box-disk.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/14/2019
ms.author: alkohli
ms.openlocfilehash: f8116ec0836623adf803991017950ddc7f960923
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "67805712"
---
# <a name="use-logs-to-troubleshoot-validation-issues-in-azure-data-box-disk"></a>Använd loggar för att felsöka validerings problem i Azure Data Box Disk

Den här artikeln gäller för Microsoft Azure Data Box Disk. I artikeln beskrivs hur du använder loggarna för att felsöka verifierings problemen som du kan se när du distribuerar den här lösningen.

## <a name="validation-tool-log-files"></a>Loggfiler för validerings verktyg

När du verifierar data på diskarna med hjälp av [verifierings verktyget](data-box-disk-deploy-copy-data.md#validate-data)genereras en *error. XML-fil* för att logga eventuella fel. Logg filen finns i `Drive:\DataBoxDiskImport\logs` mappen på enheten. Du får en länk till fel loggen när du kör verifieringen.

<!--![Validation tool with link to error log](media/data-box-disk-troubleshoot/validation-tool-link-error-log.png)-->

Om du kör flera sessioner för verifiering skapas en fel logg per session.

- Här är ett exempel på fel loggen när data som läses in i `PageBlob` mappen inte är 512-justerade byte. Alla data som laddas upp till PageBlob måste vara 512-byte-justerade, till exempel en VHD eller VHDX. Felen i den här filen finns i- `<Errors>` och-varningar `<Warnings>`i.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
        <ErrorLog Version="2018-10-01">
            <SessionId>session#1</SessionId>
            <ItemType>PageBlob</ItemType>
            <SourceDirectory>D:\Dataset\TestDirectory</SourceDirectory>
            <Errors>
                <Error Code="Not512Aligned">
                    <Description>The file is not 512 bytes aligned.</Description>
                    <List>
                        <File Path="\Practice\myScript.ps1" />
                    </List>
                    <Count>1</Count>
                </Error>
            </Errors>
            <Warnings />
        </ErrorLog>
    ```

- Här är ett exempel på fel loggen när container namnet inte är giltigt. Mappen som du skapar under `BlockBlob`, `PageBlob`eller `AzureFile` mappar på disken blir en behållare i ditt Azure Storage-konto. Namnet på behållaren måste följa [namngivnings konventionerna för Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions).

    ```xml
        <?xml version="1.0" encoding="utf-8"?>
        <ErrorLog Version="2018-10-01">
          <SessionId>bbsession</SessionId>
          <ItemType>BlockBlob</ItemType>
          <SourceDirectory>E:\BlockBlob</SourceDirectory>
          <Errors>
            <Error Code="InvalidShareContainerFormat">
              <List>
                <Container Name="Azu-reFile" />
                <Container Name="bbcont ainer1" />
              </List>
              <Count>2</Count>
            </Error>
          </Errors>
          <Warnings />
    </ErrorLog>
    ```

## <a name="validation-tool-errors"></a>Fel i validerings verktyget

Felen i *error. XML* med motsvarande rekommenderade åtgärder sammanfattas i följande tabell.

| Felkod| Beskrivning                       | Rekommenderade åtgärder               |
|------------|--------------------------|-----------------------------------|
| `None` | Data har verifierats. | Ingen åtgärd krävs. |
| `InvalidXmlCharsInPath` |Det gick inte att skapa en manifest fil eftersom fil Sök vägen innehåller ogiltiga tecken. | Ta bort dessa tecken om du vill fortsätta.  |
| `OpenFileForReadFailed`| Det gick inte att bearbeta filen. Detta kan bero på ett problem med åtkomst problemet eller att fil systemet är skadat.|Det gick inte att läsa filen på grund av ett fel. Fel informationen är i undantaget. |
| `Not512Aligned` | Den här filen har inte ett giltigt format för PageBlob-mappen.| Ladda bara upp data som är 512 byte- `PageBlob` justerade till mappen. Ta bort filen från mappen PageBlob eller flytta den till mappen BlockBlob Gör om verifieringen.|
| `InvalidBlobPath` | Fil Sök vägen mappar inte till en giltig BLOB-sökväg i molnet enligt namngivnings konventionerna för Azure blob.|Följ rikt linjerna för namngivning i Azure för att byta namn på fil Sök vägen. |
| `EnumerationError` | Det gick inte att räkna upp filen för verifiering. |Det kan finnas flera orsaker till det här felet. En trolig orsak är till gång till filen. |
| `ShareSizeExceeded` | Filen gjorde att Azure-filresursens storlek överskrider Azure-gränsen på 5 TB.|Minska storleken på data i resursen så att de överensstämmer med [storleks gränserna för Azure-objekt](data-box-disk-limits.md#azure-object-size-limits). Gör om verifieringen. |
| `AzureFileSizeExceeded` | Fil storleken överskrider Azures fil storleks begränsningar.| Minska storleken på filen eller data så att de överensstämmer med [storleks gränserna för Azure-objekt](data-box-disk-limits.md#azure-object-size-limits). Gör om verifieringen.|
| `BlockBlobSizeExceeded` | Fil storleken överskrider storleks gränsen för Azure Block Blob. | Minska storleken på filen eller data så att de överensstämmer med [storleks gränserna för Azure-objekt](data-box-disk-limits.md#azure-object-size-limits). Gör om verifieringen. |
| `ManagedDiskSizeExceeded` | Fil storleken överskrider storleks gränsen för Azure-hanterade diskar. | Minska storleken på filen eller data så att de överensstämmer med [storleks gränserna för Azure-objekt](data-box-disk-limits.md#azure-object-size-limits). Gör om verifieringen. |
| `PageBlobSizeExceeded` | Fil storleken överskrider storleks gränsen för Azure-hanterade diskar. | Minska storleken på filen eller data så att de överensstämmer med [storleks gränserna för Azure-objekt](data-box-disk-limits.md#azure-object-size-limits). Gör om verifieringen. |
| `InvalidShareContainerFormat`  |Katalog namnen stämmer inte överens med namngivnings konventionerna för behållare eller resurser i Azure.         |Den första mappen som skapas under de befintliga mapparna på disken blir en behållare i ditt lagrings konto. Namnet på den här resursen eller behållaren stämmer inte överens med namngivnings konventionerna för Azure. Byt namn på filen så att den överensstämmer med [namngivnings konventionerna i Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Gör om verifieringen.   |
| `InvalidBlobNameFormat` | Fil Sök vägen mappar inte till en giltig BLOB-sökväg i molnet enligt namngivnings konventionerna för Azure blob.|Byt namn på filen så att den överensstämmer med [namngivnings konventionerna i Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Gör om verifieringen. |
| `InvalidFileNameFormat` | Fil Sök vägen mappar inte till en giltig fil Sök väg i molnet enligt namngivnings konventionerna för Azure-filer. |Byt namn på filen så att den överensstämmer med [namngivnings konventionerna i Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Gör om verifieringen. |
| `InvalidDiskNameFormat` | Fil Sök vägen mappar inte till ett giltigt disk namn i molnet enligt namngivnings konventionerna för Azure Managed disks. |Byt namn på filen så att den överensstämmer med [namngivnings konventionerna i Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Gör om verifieringen.       |
| `NotPartOfFileShare` | Uppladdnings Sök vägen för filer är ogiltig. Överför filerna till en mapp i Azure Files.   | Ta bort filerna i fel och överför filerna till en förskapad mapp. Gör om verifieringen. |
| `NonVhdFileNotSupportedForManagedDisk` | En icke-VHD-fil kan inte överföras som en hanterad disk. |Ta bort filer som inte är VHD `ManagedDisk` -filer från mappen eftersom de inte stöds eller flytta filerna till `PageBlob` en mapp. Gör om verifieringen. |


## <a name="next-steps"></a>Nästa steg

- Felsök [data överförings fel](data-box-disk-troubleshoot-upload.md).
