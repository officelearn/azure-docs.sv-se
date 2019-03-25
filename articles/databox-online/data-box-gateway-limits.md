---
title: Azure Data Box Gateway begränsar | Microsoft Docs
description: Beskriver system gränser och storlekar som rekommenderas för Microsoft Azure Data Box-Gateway.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/20/2019
ms.author: alkohli
ms.openlocfilehash: e80b03f696a78887676e9f16750055a4dcfac230
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2019
ms.locfileid: "58401575"
---
# <a name="azure-data-box-gateway-limits"></a>Azure Data Box Gateway-begränsningar

Överväg att dessa gränser som du distribuerar och använder din Microsoft Azure Data Box Gateway-lösning. 


## <a name="data-box-gateway-service-limits"></a>Tjänstbegränsningar för data Box-Gateway

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="data-box-gateway-device-limits"></a>Enhetsgränser för data Box-Gateway

I följande tabell beskrivs gränserna för Data Box-Gateway-enheten.

| Beskrivning | Värde |
|---|---|
|Nej. filer per enhet |100 miljoner <br> Gränsen är ~ 25 miljoner filer för varje 2 TB diskutrymme med maxgränsen på 100 miljoner |
|Nej. resurser per enhet |24 |
|Nej. resurser per Azure-lagringsbehållare |1 |
|Maximal filstorlek som skrivs till en resurs|Maximal filstorlek är 500 GB för virtuella enheter 2 TB. <br> Den maximala filstorleken ökar med datadiskstorleken i föregående förhållandet tills den når högst 5 TB. |

## <a name="azure-storage-limits"></a>Azure storage-begränsningar

[!INCLUDE [data-box-edge-gateway-storage-limits](../../includes/data-box-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Överföra data varningar

[!INCLUDE [data-box-edge-gateway-storage-data-upload-caveats](../../includes/data-box-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Azure-konto storlek och objektet storleksgränser för storage

[!INCLUDE [data-box-edge-gateway-storage-acct-limits](../../includes/data-box-edge-gateway-storage-acct-limits.md)]

## <a name="azure-object-size-limits"></a>Storleksgränser för Azure-objekt

[!INCLUDE [data-box-edge-gateway-storage-object-limits](../../includes/data-box-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Nästa steg

- [Förbereda distributionen av Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
