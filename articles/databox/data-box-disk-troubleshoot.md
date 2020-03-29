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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67805712"
---
# <a name="use-logs-to-troubleshoot-validation-issues-in-azure-data-box-disk"></a>Använda loggar för att felsöka valideringsproblem i Azure Data Box Disk

Den här artikeln gäller Microsoft Azure Data Box Disk. I artikeln beskrivs hur du använder loggarna för att felsöka valideringsproblemen som du kan se när du distribuerar den här lösningen.

## <a name="validation-tool-log-files"></a>Loggfiler för valideringsverktyg

När du validerar data på diskarna med [valideringsverktyget](data-box-disk-deploy-copy-data.md#validate-data)genereras ett *error.xml* för att logga eventuella fel. Loggfilen finns i `Drive:\DataBoxDiskImport\logs` mappen på enheten. En länk till felloggen tillhandahålls när du kör valideringen.

<!--![Validation tool with link to error log](media/data-box-disk-troubleshoot/validation-tool-link-error-log.png)-->

Om du kör flera sessioner för validering genereras en fellogg per session.

- Här är ett exempel på felloggen `PageBlob` när de data som läses in i mappen inte är 512 byte justerat. Alla data som överförs till PageBlob måste vara 512 byte justerade, till exempel en virtuell hårddisk eller VHDX. Felen i den här `<Errors>` filen finns `<Warnings>`i och varningar i .

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

- Här är ett exempel på felloggen när behållarnamnet inte är giltigt. Mappen som du `BlockBlob`skapar `PageBlob`under `AzureFile` , eller mappar på disken blir en behållare i ditt Azure Storage-konto. Namnet på behållaren måste följa [Azure-namngivningskonventionerna](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions).

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

## <a name="validation-tool-errors"></a>Fel i valideringsverktyget

Felen i *error.xml* med motsvarande rekommenderade åtgärder sammanfattas i följande tabell.

| Felkod| Beskrivning                       | Rekommenderade åtgärder               |
|------------|--------------------------|-----------------------------------|
| `None` | Data har validerats. | Ingen åtgärd krävs. |
| `InvalidXmlCharsInPath` |Det gick inte att skapa en manifestfil eftersom filsökvägen har tecken som inte är giltiga. | Ta bort dessa tecken för att fortsätta.  |
| `OpenFileForReadFailed`| Det gick inte att bearbeta filen. Detta kan bero på ett åtkomstproblem eller filsystem skadad.|Det gick inte att läsa filen på grund av ett fel. Felinformationen finns i undantaget. |
| `Not512Aligned` | Den här filen är inte i ett giltigt format för PageBlob-mappen.| Ladda bara upp data som är 512 byte justerade mot `PageBlob` mappen. Ta bort filen från PageBlob-mappen eller flytta den till mappen BlockBlob. Försök igen med valideringen.|
| `InvalidBlobPath` | Filsökvägen mappas inte till en giltig blob-sökväg i molnet enligt azure blob-namngivningskonventionerna.|Följ riktlinjerna för namngivning av Azure för att byta namn på filsökvägen. |
| `EnumerationError` | Det gick inte att räkna upp filen för validering. |Det kan finnas flera orsaker till det här felet. En trolig orsak är åtkomst till filen. |
| `ShareSizeExceeded` | Den här filen orsakade att Azure-filresursstorleken överskred Azure-gränsen på 5 TB.|Minska storleken på data i resursen så att den överensstämmer med [Azure-objektstorleksgränserna](data-box-disk-limits.md#azure-object-size-limits). Försök igen med valideringen. |
| `AzureFileSizeExceeded` | Filstorleken överskrider storleksgränserna för Azure File.| Minska storleken på filen eller data så att den överensstämmer med [Azure-objektstorleksgränserna](data-box-disk-limits.md#azure-object-size-limits). Försök igen med valideringen.|
| `BlockBlobSizeExceeded` | Filstorleken överskrider storleksgränserna för Azure Block Blob. | Minska storleken på filen eller data så att den överensstämmer med [Azure-objektstorleksgränserna](data-box-disk-limits.md#azure-object-size-limits). Försök igen med valideringen. |
| `ManagedDiskSizeExceeded` | Filstorleken överskrider storleksgränserna för Azure Managed Disk. | Minska storleken på filen eller data så att den överensstämmer med [Azure-objektstorleksgränserna](data-box-disk-limits.md#azure-object-size-limits). Försök igen med valideringen. |
| `PageBlobSizeExceeded` | Filstorleken överskrider storleksgränserna för Azure Managed Disk. | Minska storleken på filen eller data så att den överensstämmer med [Azure-objektstorleksgränserna](data-box-disk-limits.md#azure-object-size-limits). Försök igen med valideringen. |
| `InvalidShareContainerFormat`  |Katalognamnen överensstämmer inte med Azure-namngivningskonventioner för behållare eller resurser.         |Den första mappen som skapas under de befintliga mapparna på disken blir en behållare i ditt lagringskonto. Det här resurs- eller behållarnamnet överensstämmer inte med Azure-namngivningskonventionerna. Byt namn på filen så att den överensstämmer med [Azure-namngivningskonventioner](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Försök igen med valideringen.   |
| `InvalidBlobNameFormat` | Filsökvägen mappas inte till en giltig blob-sökväg i molnet enligt azure blob-namngivningskonventionerna.|Byt namn på filen så att den överensstämmer med [Azure-namngivningskonventioner](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Försök igen med valideringen. |
| `InvalidFileNameFormat` | Sökvägen mappas inte till en giltig filsökväg i molnet enligt namngivningskonventionerna för Azure File. |Byt namn på filen så att den överensstämmer med [Azure-namngivningskonventioner](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Försök igen med valideringen. |
| `InvalidDiskNameFormat` | Sökvägen mappas inte till ett giltigt disknamn i molnet enligt namngivningskonventionerna för Azure Managed Disk. |Byt namn på filen så att den överensstämmer med [Azure-namngivningskonventioner](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Försök igen med valideringen.       |
| `NotPartOfFileShare` | Uppladdningssökvägen för filer är ogiltig. Ladda upp filerna till en mapp i Azure Files.   | Ta bort filerna av misstag och ladda upp filerna till en förskapad mapp. Försök igen med valideringen. |
| `NonVhdFileNotSupportedForManagedDisk` | Det går inte att överföra en icke-VHD-fil som en hanterad disk. |Ta bort de filer `ManagedDisk` som inte är vhd-filer från `PageBlob` mappen eftersom de inte stöds eller flytta dessa filer till en mapp. Försök igen med valideringen. |


## <a name="next-steps"></a>Nästa steg

- Felsöka [datauppladdningsfel](data-box-disk-troubleshoot-upload.md).
