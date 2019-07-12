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
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67805712"
---
# <a name="use-logs-to-troubleshoot-validation-issues-in-azure-data-box-disk"></a>Använd loggarna för att felsöka problem med schemavalidering i Azure Data Box-Disk

Den här artikeln gäller för Microsoft Azure Data Box-Disk. Artikeln beskriver hur du använder loggarna för att felsöka verifieringsproblem kan du få se när du distribuerar den här lösningen.

## <a name="validation-tool-log-files"></a>Loggfiler för verifiering verktyget

När du verifiera att data på diskar med hjälp av den [verifiering verktyget](data-box-disk-deploy-copy-data.md#validate-data), en *error.xml* genereras för att logga in eventuella fel. Loggfilen finns i den `Drive:\DataBoxDiskImport\logs` mapp för enheten. Det finns en länk i felloggen när du kör verifieringen.

<!--![Validation tool with link to error log](media/data-box-disk-troubleshoot/validation-tool-link-error-log.png)-->

Om du kör flera sessioner för verifiering, skapas en felloggen per session.

- Här är ett exempel på felloggen när data läses in i den `PageBlob` mappen är inte 512 byte-justerad. Alla data som överförs till PageBlob måste vara 512-byte justerad, till exempel en VHD eller VHDX. Fel i den här filen finns i den `<Errors>` och varningar i `<Warnings>`.

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

- Här är ett exempel på felloggen när behållarens namn inte är giltig. Mappen som du skapar under `BlockBlob`, `PageBlob`, eller `AzureFile` mappar på disken blir en behållare i Azure Storage-kontot. Namnet på behållaren måste följa den [namngivningskonventionerna Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions).

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

## <a name="validation-tool-errors"></a>Verifieringsfel för verktyget

Fel i den *error.xml* med motsvarande rekommenderade åtgärder sammanfattas i tabellen nedan.

| Felkod| Beskrivning                       | Rekommenderade åtgärder               |
|------------|--------------------------|-----------------------------------|
| `None` | Verifierat data. | Ingen åtgärd krävs. |
| `InvalidXmlCharsInPath` |Kunde inte skapa en manifestfil som filsökvägen innehåller tecken som inte är giltiga. | Ta bort dessa tecken om du vill fortsätta.  |
| `OpenFileForReadFailed`| Det gick inte att bearbeta filen. Detta kan bero på en åtkomst-problem eller filen systemfel.|Det gick inte att läsa filen på grund av ett fel. Felinformation finns i undantaget. |
| `Not512Aligned` | Den här filen är inte ett giltigt format för PageBlob mapp.| Endast ladda uppladdningsdata som är 512 byte justerad `PageBlob` mapp. Ta bort filen från mappen PageBlob eller flytta den till mappen BlockBlob. Gör om verifieringen.|
| `InvalidBlobPath` | Filsökväg mappas inte till en giltig sökväg i molnet enligt den Azure-Blob namngivningskonventioner.|Följ riktlinjerna för Azure namngivning för att byta namn på sökvägen till filen. |
| `EnumerationError` | Det gick inte att räkna upp filen för verifiering. |Det kan finnas flera orsaker till det här felet. En troligaste orsaken är åtkomst till filen. |
| `ShareSizeExceeded` | Den här filen orsakade Azure filresursens storlek överskrider Azure gränsen på 5 TB.|Minska storleken på data i resursen så att den överensstämmer med den [storleksbegränsningar för Azure-objekt](data-box-disk-limits.md#azure-object-size-limits). Gör om verifieringen. |
| `AzureFileSizeExceeded` | Filstorleken överskrider Azure filstorleksbegränsningar.| Minska storleken på filen eller data så att den överensstämmer med den [storleksbegränsningar för Azure-objekt](data-box-disk-limits.md#azure-object-size-limits). Gör om verifieringen.|
| `BlockBlobSizeExceeded` | Filstorleken överskrider storleksgränserna för Azure Block Blob. | Minska storleken på filen eller data så att den överensstämmer med den [storleksbegränsningar för Azure-objekt](data-box-disk-limits.md#azure-object-size-limits). Gör om verifieringen. |
| `ManagedDiskSizeExceeded` | Filstorleken överskrider storleksgränserna Azure Managed Disk. | Minska storleken på filen eller data så att den överensstämmer med den [storleksbegränsningar för Azure-objekt](data-box-disk-limits.md#azure-object-size-limits). Gör om verifieringen. |
| `PageBlobSizeExceeded` | Filstorleken överskrider storleksgränserna Azure Managed Disk. | Minska storleken på filen eller data så att den överensstämmer med den [storleksbegränsningar för Azure-objekt](data-box-disk-limits.md#azure-object-size-limits). Gör om verifieringen. |
| `InvalidShareContainerFormat`  |Directory-namnen överensstämmer inte med Azure regler för namngivning av behållare eller resurser.         |Den första mapp som skapats under de befintliga mapparna på disken blir en behållare i ditt storage-konto. Den här resursen eller behållare namnet följer inte namngivningskonventionerna i Azure. Byt namn på filen så att den överensstämmer [namngivningskonventionerna Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Gör om verifieringen.   |
| `InvalidBlobNameFormat` | Filsökväg mappas inte till en giltig sökväg i molnet enligt den Azure-Blob namngivningskonventioner.|Byt namn på filen så att den överensstämmer [namngivningskonventionerna Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Gör om verifieringen. |
| `InvalidFileNameFormat` | Filsökväg mappas inte till en giltig sökväg i molnet enligt filen Azure namngivningskonventioner. |Byt namn på filen så att den överensstämmer [namngivningskonventionerna Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Gör om verifieringen. |
| `InvalidDiskNameFormat` | Filsökväg mappas inte till ett giltigt disk-namn i molnet enligt namnkonventioner för Azure Managed Disk. |Byt namn på filen så att den överensstämmer [namngivningskonventionerna Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Gör om verifieringen.       |
| `NotPartOfFileShare` | Ladda upp sökvägen för filer är inte giltig. Ladda upp filer till en mapp i Azure Files.   | Ta bort filer i fel och överföra filerna till en införande mapp. Gör om verifieringen. |
| `NonVhdFileNotSupportedForManagedDisk` | Att det går inte att överföra en icke-VHD-filen som en hanterad disk. |Ta bort icke-VHD-filer från `ManagedDisk` mapp som dessa inte stöds eller flytta filerna till en `PageBlob` mapp. Gör om verifieringen. |


## <a name="next-steps"></a>Nästa steg

- Felsöka [data uppladdningsfel](data-box-disk-troubleshoot-upload.md).
