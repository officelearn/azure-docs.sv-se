---
title: Azure Data Box Edge begränsar | Microsoft Docs
description: Beskriver system gränser och storlekar som rekommenderas för Microsoft Azure Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 02/05/2019
ms.author: alkohli
ms.openlocfilehash: 30e0c37d3d0c03e77b6dab9c06c0a50bff27e8bc
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2019
ms.locfileid: "55967512"
---
# <a name="azure-data-box-edge-limits-preview"></a>Azure Data Box Edge-begränsningar (förhandsgranskning)

Överväg att dessa gränser som du distribuerar och använder din Microsoft Azure Data Box Edge-lösning.

> [!IMPORTANT]
> Data Box Edge är i förhandsversion. Läs [användningsvillkoren för förhandsversionen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) innan du distribuerar den här lösningen.


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
