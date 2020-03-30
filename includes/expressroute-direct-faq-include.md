---
title: ta med fil
description: ta med fil
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: include
ms.date: 10/29/2019
ms.author: jaredro
ms.custom: include file
ms.openlocfilehash: 1f6c65d6d8da275d6c52ac2802092dbb5e4e507d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "73182286"
---
### <a name="what-is-expressroute-direct"></a>Vad är ExpressRoute Direct?

Med ExpressRoute Direct får kunder möjligheten att ansluta direkt till Microsofts globala nätverk vid peering-platser strategiskt fördelade runt om i världen. ExpressRoute Direct ger dubbla 100 eller 10 Gbit/s-anslutning, som stöder aktiv/aktiv anslutning i stor skala. 

### <a name="how-do-customers-connect-to-expressroute-direct"></a>Hur ansluter kunder till ExpressRoute Direct? 

Kunderna måste arbeta med sina lokala operatörer och samlokaliseringsleverantörer för att få anslutning till ExpressRoute-routrar för att dra nytta av ExpressRoute Direct.

### <a name="what-locations-currently-support-expressroute-direct"></a>Vilka platser har för närvarande stöd för ExpressRoute Direct? 

Kontrollera tillgängligheten på [platssidan](../articles/expressroute/expressroute-locations-providers.md). 

### <a name="what-is-the-sla-for-expressroute-direct"></a>Vad är SLA för ExpressRoute Direct?

ExpressRoute Direct kommer att använda samma [företagsklass på ExpressRoute](https://azure.microsoft.com/support/legal/sla/expressroute/v1_3/). 

### <a name="what-scenarios-should-customers-consider-with-expressroute-direct"></a>Vilka scenarier bör kunderna tänka på med ExpressRoute Direct?  

ExpressRoute Direct ger kunderna direkt 100 eller 10 Gbps portpar i Microsofts globala ryggrad. De scenarier som ger kunderna de största fördelarna är: Massiv datainmatning, fysisk isolering för reglerade marknader och dedikerad kapacitet för burst-scenario, som rendering. 

### <a name="what-is-the-billing-model-for-expressroute-direct"></a>Vad är faktureringsmodellen för ExpressRoute Direct? 

ExpressRoute Direct faktureras för portparet till ett fast belopp. Standardkretsar kommer att inkluderas utan ytterligare timmar och premium kommer att ha en liten tilläggsavgift. Utgående kommer att faktureras per krets baserat på zonen för peering-platsen.

### <a name="when-does-billing-start-for-the-expressroute-direct-port-pairs"></a>När startar faktureringen för ExpressRoute Direct-portpar?

ExpressRoute Directs portpar faktureras 45 dagar in i skapandet av ExpressRoute Direct-resursen eller när 1 eller båda länkarna är aktiverade, beroende på vilket som inträffar först. Respitperioden på 45 dagar beviljas för att tillåta kunder att slutföra anslutningsprocessen med samlokaliseringsleverantören.
