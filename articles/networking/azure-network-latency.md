---
title: Statistik över svars tider för Azure Network tur | Microsoft Docs
description: Läs mer om svars tids fördröjnings statistik mellan Azure-regioner.
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 08/05/2020
ms.author: kumud
ms.openlocfilehash: a1ae6386ddbf9e8bc226598b0b9218ba20669356
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91336366"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Azure Network tur och retur latens-statistik

Azure övervakar kontinuerligt svars tiden (hastighet) för kärn områdena i sitt nätverk med hjälp av interna övervaknings verktyg samt mått som samlas in av [ThousandEyes](https://thousandeyes.com), en syntetisk övervaknings tjänst från tredje part.

## <a name="how-are-the-measurements-collected"></a>Hur samlas mätningarna in?

Fördröjnings mätningarna samlas in från ThousandEyes-agenter, som finns i Azures moln regioner över hela världen, som kontinuerligt skickar nätverks avsökningar mellan sig i intervall på 1 minut. Statistiken för månatlig latens beräknas från medelvärdet av de insamlade exemplen för månaden.

## <a name="august-2020-round-trip-latency-figures"></a>Latens svars tider i augusti 2020

De månatliga genomsnittliga fördröjningarna mellan Azure-regioner under de senaste 31 dagarna (från och med den 31 augusti 2020) visas nedan. Följande mätningar drivs av [ThousandEyes](https://thousandeyes.com).

[![Statistik över svars tider i Azure mellan regioner](media/azure-network-latency/azure-network-latency.png)](media/azure-network-latency/azure-network-latency.png#lightbox)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [Azure-regioner](https://azure.microsoft.com/global-infrastructure/regions/).
