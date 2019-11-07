---
title: Statistik över svars tider för Azure Network tur | Microsoft Docs
description: Läs mer om svars tids fördröjnings statistik mellan Azure-regioner.
services: networking
documentationcenter: na
author: nayak-mahesh
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/04/2019
ms.author: mnayak
ms.openlocfilehash: 500676983233f943fdc9638d75758645dee65564
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73587594"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Azure Network tur och retur latens-statistik

Azure övervakar kontinuerligt svars tiden (hastighet) för kärn områdena i sitt nätverk med hjälp av interna övervaknings verktyg samt mått som samlas in av [ThousandEyes](https://thousandeyes.com), en syntetisk övervaknings tjänst från tredje part.

## <a name="how-are-the-measurements-collected"></a>Hur samlas mätningarna in?

Svars tids mätningarna samlas in från ThousandEyes-agenter som finns i Azure Cloud regionerna World-Wide, som kontinuerligt skickar nätverks avsökningar mellan sig, i intervall om 1 minut. Statistiken för månatlig latens beräknas från medelvärdet av de insamlade exemplen för månaden.

## <a name="october-2019-latency-figures"></a>Siffror i oktober 2019-latens

För de 31 dagar som avslutas den 31 oktober 2019, är månads fördröjningar och högsta svars tid för svars tider inom de aggregerade regionerna:

- **5 MS** till **72 MS** för tur och fördröjning inom **Nordamerika** regioner.
- **3 MS** till **28 MS** för tur och fördröjning i **Europa** -regioner.
- **4 MS** till **134 MS** för Round resor i **Asien** .

Följande mätningar mellan olika regioner drivs av [ThousandEyes](https://thousandeyes.com). Mått enheten i tabellen nedan är i millisekunder (MS).

![Statistik över svars tider i Azure mellan regioner](media/azure-network-latency/azure-inter-region-latency.png)


## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [Azure-regioner](https://azure.microsoft.com/global-infrastructure/regions/).