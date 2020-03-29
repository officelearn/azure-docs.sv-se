---
title: Azure Data Box Edge-gränser | Microsoft-dokument
description: Beskriver systemgränser och rekommenderade storlekar för Azure Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/22/2019
ms.author: alkohli
ms.openlocfilehash: 9adfc1ef355c31f0bfeeb6c3b194d62426b7b9b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72244306"
---
# <a name="azure-data-box-edge-limits"></a>Azure Data Box Edge-gränser

Tänk på dessa gränser när du distribuerar och använder din Microsoft Azure Data Box Edge-lösning.

## <a name="data-box-edge-service-limits"></a>Servicegränser för Data Box Edge

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="data-box-edge-device-limits"></a>Gränser för databoxkantens enhet

I följande tabell beskrivs gränserna för Data Box Edge-enheten.

| Beskrivning | Värde |
|---|---|
|Nej. filer per enhet |100 miljoner |
|Nej. av aktier per enhet |24 |
|Nej. av aktier per behållare |1 |
|Maximal filstorlek som skrivits till en resurs| 5 TB |

## <a name="azure-storage-limits"></a>Azure-lagringsgränser

[!INCLUDE [data-box-edge-gateway-storage-limits](../../includes/data-box-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Varningar för datauppladdning

[!INCLUDE [data-box-edge-gateway-storage-data-upload-caveats](../../includes/data-box-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Storleksbegränsningar för Azure-lagringskonto och objektstorlek

[!INCLUDE [data-box-edge-gateway-storage-acct-limits](../../includes/data-box-edge-gateway-storage-acct-limits.md)]


## <a name="azure-object-size-limits"></a>Storleksbegränsningar för Azure-objekt

[!INCLUDE [data-box-edge-gateway-storage-object-limits](../../includes/data-box-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Nästa steg

- [Förbered att distribuera Azure Data Box Edge](data-box-edge-deploy-prep.md)
