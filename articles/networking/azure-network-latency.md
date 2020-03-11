---
title: Svars tids statistik för Azure Network tur och retur | Microsoft Docs
description: Läs mer om svars tids fördröjnings statistik mellan Azure-regioner.
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 03/10/2020
ms.author: kumud
ms.openlocfilehash: 3469daa8b81b20d5d0052a23ce3236fc4fde75de
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2020
ms.locfileid: "79082954"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Svars tids statistik för Azure Network tur och retur

Azure övervakar kontinuerligt svars tiden (hastighet) för kärn områdena i sitt nätverk med hjälp av interna övervaknings verktyg samt mått som samlas in av [ThousandEyes](https://thousandeyes.com), en syntetisk övervaknings tjänst från tredje part.

## <a name="how-are-the-measurements-collected"></a>Hur samlas mätningarna in?

Fördröjnings mätningarna samlas in från ThousandEyes-agenter, som finns i Azures moln regioner över hela världen, som kontinuerligt skickar nätverks avsökningar mellan sig i intervall på 1 minut. Statistiken för månatlig latens beräknas från medelvärdet av de insamlade exemplen för månaden.

## <a name="february-2020-round-trip-latency-figures"></a>Svars tid i februari 2020 tur och retur

De månatliga genomsnittliga fördröjningarna mellan Azure-regioner under de senaste 29 dagarna (från och med den 29 februari 2020) visas nedan. Följande mätningar drivs av [ThousandEyes](https://thousandeyes.com).

[![statistik över svars tider i Azure i regioner](media/azure-network-latency/azure-network-latency.png)](media/azure-network-latency/azure-network-latency.png#lightbox)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [Azure-regioner](https://azure.microsoft.com/global-infrastructure/regions/).
