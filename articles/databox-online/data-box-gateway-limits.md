---
title: Azure Data Box Gateway gränser | Microsoft-dokument
description: Beskriver systemgränser och rekommenderade storlekar för Microsoft Azure Data Box Gateway.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/20/2019
ms.author: alkohli
ms.openlocfilehash: e80b03f696a78887676e9f16750055a4dcfac230
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60755258"
---
# <a name="azure-data-box-gateway-limits"></a>Azure Data Box Gateway gränser

Tänk på dessa gränser när du distribuerar och använder din Microsoft Azure Data Box Gateway-lösning. 


## <a name="data-box-gateway-service-limits"></a>Servicegränser för Data Box Gateway

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="data-box-gateway-device-limits"></a>Enhetsgränser för Data Box Gateway

I följande tabell beskrivs gränserna för databoxgatewayenheten.

| Beskrivning | Värde |
|---|---|
|Nej. filer per enhet |100 miljoner <br> Gräns är ~ 25 miljoner filer för varje 2 TB diskutrymme med maximal gräns på 100 miljoner |
|Nej. av aktier per enhet |24 |
|Nej. av resurser per Azure-lagringsbehållare |1 |
|Maximal filstorlek som skrivits till en resurs|För en virtuell enhet på 2 TB är den maximala filstorleken 500 GB. <br> Den maximala filstorleken ökar med datadiskstorleken i föregående förhållande tills den når högst 5 TB. |

## <a name="azure-storage-limits"></a>Azure-lagringsgränser

[!INCLUDE [data-box-edge-gateway-storage-limits](../../includes/data-box-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Varningar för datauppladdning

[!INCLUDE [data-box-edge-gateway-storage-data-upload-caveats](../../includes/data-box-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Storleksbegränsningar för Azure-lagringskonto och objektstorlek

[!INCLUDE [data-box-edge-gateway-storage-acct-limits](../../includes/data-box-edge-gateway-storage-acct-limits.md)]

## <a name="azure-object-size-limits"></a>Storleksbegränsningar för Azure-objekt

[!INCLUDE [data-box-edge-gateway-storage-object-limits](../../includes/data-box-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Nästa steg

- [Förbereda distributionen av Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
