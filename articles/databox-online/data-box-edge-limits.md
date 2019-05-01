---
title: Azure Data Box Edge begränsar | Microsoft Docs
description: Beskriver system gränser och rekommenderade storlekar för Azure Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/22/2019
ms.author: alkohli
ms.openlocfilehash: b454b563cdb870ca8f07a45b796dc6b1e272502d
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64924605"
---
# <a name="azure-data-box-edge-limits"></a>Azure Data Box Edge-gränser

Överväg att dessa gränser som du distribuerar och använder din Microsoft Azure Data Box Edge-lösning.

## <a name="data-box-edge-service-limits"></a>Data Box Edge tjänstbegränsningar

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="data-box-edge-device-limits"></a>Databegränsningar Box Edge-enhet

I följande tabell beskrivs gränserna för Data Box Edge-enhet.

| Beskrivning | Värde |
|---|---|
|Nej. filer per enhet |100 miljoner |
|Nej. resurser per enhet |24 |
|Nej. resurser per behållare |1 |
|Maximal filstorlek som skrivs till en resurs| 5 TB |

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
