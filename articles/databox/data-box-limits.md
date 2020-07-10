---
title: Azure Data Box gränser | Microsoft Docs
description: Beskriver system begränsningar och rekommenderade storlekar för Microsoft Azure Data Box-enhet komponenter och anslutningar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/10/2020
ms.author: alkohli
ms.openlocfilehash: 7d699fc47fa0a0cb57d103ff42ff17bdc0f3a82b
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2020
ms.locfileid: "86202672"
---
# <a name="azure-data-box-limits"></a>Azure Data Box gränser

Tänk på följande begränsningar när du distribuerar och använder Microsoft Azure Data Box-enhet. I följande tabell beskrivs de här gränserna för Data Box-enhet.

## <a name="data-box-service-limits"></a>Data Box-enhet tjänst gränser

[!INCLUDE [data-box-service-limits](../../includes/data-box-service-limits.md)]

## <a name="data-box-limits"></a>Data Box-enhet gränser

- Data Box-enhet kan lagra högst 500 000 000 filer för både import och export.
- Data Box-enhet stöder högst 512 behållare eller resurser i molnet. Katalogerna på den översta nivån i användar resursen blir behållare eller Azure-filresurser i molnet. 
- Data Box-enhet användnings kapaciteten kan vara mindre än 80 TB på grund av användningen av ReFS-metadatalagret.

## <a name="azure-storage-limits"></a>Azure Storage-gränser

[!INCLUDE [data-box-storage-limits](../../includes/data-box-storage-limits.md)]

## <a name="data-upload-caveats"></a>Varningar för datauppladdning


### <a name="for-import-order"></a>För import ordning

Data Box-enhet varningar för en import order:

[!INCLUDE [data-box-data-upload-caveats](../../includes/data-box-data-upload-caveats.md)]

## <a name="for-export-order"></a>För export ordning

Data Box-enhets varningar för en export order är:

- Data Box-enhet är en Windows-baserad enhet och stöder inte Skift läges känsliga fil namn. Du kan till exempel ha två olika filer i Azure med namn som bara skiljer sig i skift läge. Använd inte data box för att exportera sådana filer när de skrivs över på enheten.
- Om du har dubbla Taggar i indatafiler eller taggar som refererar till samma data, kan Data Box-enhet export hoppa över eller skriva över filerna. Antalet filer och storleken på de data som visas på Azure Portal kan skilja sig från den faktiska storleken på de data som finns på enheten. 
- Data Box-enhet exporterar data till Windows-baserat system via SMB och begränsas av SMB-begränsningar för filer och mappar. Filer och mappar med namn som inte stöds exporteras inte.
- Det finns en 1:1-mappning från prefixet till container.
- Maximal fil storlek är 1024 tecken, fil namn som överskrider den här längden exporteras inte.
- Dubbla prefix i *XML-* filen (uppladdat vid skapande av order) exporteras. Duplicerade prefix ignoreras inte.
- Sid-blobbar och behållar namn är Skift läges känsliga, så om höljet inte matchar hittas inte blobben och/eller behållaren.
 

## <a name="azure-storage-account-size-limits"></a>Storleks gränser för Azure Storage-kontot

[!INCLUDE [data-box-storage-account-size-limits](../../includes/data-box-storage-account-size-limits.md)]

## <a name="azure-object-size-limits"></a>Storleks gränser för Azure-objekt

[!INCLUDE [data-box-object-size-limits](../../includes/data-box-object-size-limits.md)]

## <a name="azure-block-blob-page-blob-and-file-naming-conventions"></a>Konventioner för Azure Block Blob, Page blob och namngivning av filer

[!INCLUDE [data-box-naming-conventions](../../includes/data-box-naming-conventions.md)]
