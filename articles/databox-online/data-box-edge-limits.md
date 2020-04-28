---
title: Azure Data Box Edge gränser | Microsoft Docs
description: Beskriver system gränser och rekommenderade storlekar för Azure Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/22/2019
ms.author: alkohli
ms.openlocfilehash: 9adfc1ef355c31f0bfeeb6c3b194d62426b7b9b3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "72244306"
---
# <a name="azure-data-box-edge-limits"></a>Azure Data Box Edge gränser

Tänk på följande begränsningar när du distribuerar och använder Microsoft Azure Data Box Edge-lösningen.

## <a name="data-box-edge-service-limits"></a>Data Box Edge tjänst gränser

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="data-box-edge-device-limits"></a>Data Box Edge enhets gränser

I följande tabell beskrivs gränserna för den Data Box Edge enheten.

| Beskrivning | Värde |
|---|---|
|Nej. av filer per enhet |100 000 000 |
|Nej. av resurser per enhet |24 |
|Nej. av resurser per behållare |1 |
|Maximal fil storlek som skrivs till en resurs| 5 TB |

## <a name="azure-storage-limits"></a>Azure Storage-gränser

[!INCLUDE [data-box-edge-gateway-storage-limits](../../includes/data-box-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Varningar för datauppladdning

[!INCLUDE [data-box-edge-gateway-storage-data-upload-caveats](../../includes/data-box-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Storleks gränser för Azure Storage-konto och objekt storlek

[!INCLUDE [data-box-edge-gateway-storage-acct-limits](../../includes/data-box-edge-gateway-storage-acct-limits.md)]


## <a name="azure-object-size-limits"></a>Storleks gränser för Azure-objekt

[!INCLUDE [data-box-edge-gateway-storage-object-limits](../../includes/data-box-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Nästa steg

- [Förbered att distribuera Azure Data Box Edge](data-box-edge-deploy-prep.md)
