---
title: Svars tids statistik för Azure Network tur och retur | Microsoft Docs
description: Läs mer om svars tids fördröjnings statistik mellan Azure-regioner.
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 05/26/2020
ms.author: kumud
ms.openlocfilehash: 8cf5d07bb071217a5ecafca8f351c94590291603
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "83994023"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Svars tids statistik för Azure Network tur och retur

Azure övervakar kontinuerligt svars tiden (hastighet) för kärn områdena i sitt nätverk med hjälp av interna övervaknings verktyg samt mått som samlas in av [ThousandEyes](https://thousandeyes.com), en syntetisk övervaknings tjänst från tredje part.

## <a name="how-are-the-measurements-collected"></a>Hur samlas mätningarna in?

Fördröjnings mätningarna samlas in från ThousandEyes-agenter, som finns i Azures moln regioner över hela världen, som kontinuerligt skickar nätverks avsökningar mellan sig i intervall på 1 minut. Statistiken för månatlig latens beräknas från medelvärdet av de insamlade exemplen för månaden.

## <a name="april-2020-round-trip-latency-figures"></a>Latens latens i april 2020

De månatliga genomsnittliga fördröjningarna mellan Azure-regioner under de senaste 30 dagarna (från och med den 30 april 2020) visas nedan. Följande mätningar drivs av [ThousandEyes](https://thousandeyes.com).

[![Statistik över svars tider i Azure mellan regioner](media/azure-network-latency/azure-network-latency.png)](media/azure-network-latency/azure-network-latency.png#lightbox)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [Azure-regioner](https://azure.microsoft.com/global-infrastructure/regions/).
