---
title: Statistik över svarstid för Azure-nätverk tur och retur | Microsoft-dokument
description: Läs mer om svarstidsstatistik för tur och retur mellan Azure-regioner.
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 03/10/2020
ms.author: kumud
ms.openlocfilehash: 3469daa8b81b20d5d0052a23ce3236fc4fde75de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79082954"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Latstidsstatistik för Azure-nätverk tur och retur

Azure övervakar kontinuerligt svarstiden (hastigheten) för kärnområden i nätverket med hjälp av interna övervakningsverktyg samt mätningar som samlats in av [ThousandEyes](https://thousandeyes.com), en syntetisk övervakningstjänst från tredje part.

## <a name="how-are-the-measurements-collected"></a>Hur samlas mätningarna in?

Svarstidsmätningarna samlas in från ThousandEyes-agenter, som finns i Azure-molnregioner över hela världen, som kontinuerligt skickar nätverksavsökningar sinsemellan i 1-minutersintervall. Månadsstatistiken för svarstid härleds från medelvärdet av de insamlade exemplen för månaden.

## <a name="february-2020-round-trip-latency-figures"></a>Februari 2020 tur och retur latens siffror

De månatliga genomsnittliga tur-och-returtiderna mellan Azure-regioner för de senaste 29 dagarna (som slutar den 29 februari 2020) visas nedan. Följande mätningar drivs av [ThousandEyes](https://thousandeyes.com).

[![Azure-statistik över svarstid mellan regioner](media/azure-network-latency/azure-network-latency.png)](media/azure-network-latency/azure-network-latency.png#lightbox)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [Azure-regioner](https://azure.microsoft.com/global-infrastructure/regions/).
