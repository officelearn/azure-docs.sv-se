---
title: Svars tids statistik för Azure Network tur och retur | Microsoft Docs
description: Läs mer om svars tids fördröjnings statistik mellan Azure-regioner.
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 03/10/2020
ms.author: kumud
ms.openlocfilehash: d9cae04499f046749e504bcab89b893fcc31a81c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80886965"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Svars tids statistik för Azure Network tur och retur

Azure övervakar kontinuerligt svars tiden (hastighet) för kärn områdena i sitt nätverk med hjälp av interna övervaknings verktyg samt mått som samlas in av [ThousandEyes](https://thousandeyes.com), en syntetisk övervaknings tjänst från tredje part.

## <a name="how-are-the-measurements-collected"></a>Hur samlas mätningarna in?

Fördröjnings mätningarna samlas in från ThousandEyes-agenter, som finns i Azures moln regioner över hela världen, som kontinuerligt skickar nätverks avsökningar mellan sig i intervall på 1 minut. Statistiken för månatlig latens beräknas från medelvärdet av de insamlade exemplen för månaden.

## <a name="march-2020-round-trip-latency-figures"></a>Svars tid i mars 2020 tur och retur

De månatliga genomsnittliga fördröjningarna mellan Azure-regioner under de senaste 31 dagarna (från och med den 31 mars 2020) visas nedan. Följande mätningar drivs av [ThousandEyes](https://thousandeyes.com).

[![Statistik över svars tider i Azure mellan regioner](media/azure-network-latency/azure-network-latency.png)](media/azure-network-latency/azure-network-latency.png#lightbox)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [Azure-regioner](https://azure.microsoft.com/global-infrastructure/regions/).
