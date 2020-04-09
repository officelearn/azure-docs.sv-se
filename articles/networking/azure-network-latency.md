---
title: Statistik över svarstid för Azure-nätverk tur och retur | Microsoft-dokument
description: Läs mer om svarstidsstatistik för tur och retur mellan Azure-regioner.
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 03/10/2020
ms.author: kumud
ms.openlocfilehash: d9cae04499f046749e504bcab89b893fcc31a81c
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886965"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Latstidsstatistik för Azure-nätverk tur och retur

Azure övervakar kontinuerligt svarstiden (hastigheten) för kärnområden i nätverket med hjälp av interna övervakningsverktyg samt mätningar som samlats in av [ThousandEyes](https://thousandeyes.com), en syntetisk övervakningstjänst från tredje part.

## <a name="how-are-the-measurements-collected"></a>Hur samlas mätningarna in?

Svarstidsmätningarna samlas in från ThousandEyes-agenter, som finns i Azure-molnregioner över hela världen, som kontinuerligt skickar nätverksavsökningar sinsemellan i 1-minutersintervall. Månadsstatistiken för svarstid härleds från medelvärdet av de insamlade exemplen för månaden.

## <a name="march-2020-round-trip-latency-figures"></a>Mars 2020 tur och retur latens siffror

De månatliga genomsnittliga tur-och-returtiderna mellan Azure-regioner för de senaste 31 dagarna (som slutar den 31 mars 2020) visas nedan. Följande mätningar drivs av [ThousandEyes](https://thousandeyes.com).

[![Azure-statistik över svarstid mellan regioner](media/azure-network-latency/azure-network-latency.png)](media/azure-network-latency/azure-network-latency.png#lightbox)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [Azure-regioner](https://azure.microsoft.com/global-infrastructure/regions/).
