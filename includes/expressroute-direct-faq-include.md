---
title: ta med fil
description: ta med fil
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: include
ms.date: 07/09/2019
ms.author: jaredro
ms.custom: include file
ms.openlocfilehash: 43d1942b1413569d77c6c17fee9fff14dc83c924
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2019
ms.locfileid: "68362637"
---
### <a name="what-is-expressroute-direct"></a>Vad är ExpressRoute Direct?

Med ExpressRoute Direct får kunder möjligheten att ansluta direkt till Microsofts globala nätverk vid peering-platser strategiskt fördelade runt om i världen. ExpressRoute Direct tillhandahåller dubbla 100-eller 10 Gbit/s-anslutningar, som har stöd för aktiv/aktiv anslutning i stor skala. 

### <a name="how-do-customers-connect-to-expressroute-direct"></a>Hur ansluter kunderna till ExpressRoute Direct? 

Kunderna måste arbeta med sina lokala operatörer och samlokaliserings leverantörer för att få anslutning till ExpressRoute-routrar för att kunna dra nytta av ExpressRoute Direct.

### <a name="what-locations-currently-support-expressroute-direct"></a>Vilka platser stöder för närvarande ExpressRoute Direct? 

De tillgängliga portarna är dynamiska och kommer att vara tillgängliga av PowerShell för att Visa kapaciteten. Platser är och *kan komma att ändras baserat på tillgängligheten*:

* Amsterdam
* Amsterdam2
* Auckland 
* Chicago
* Dallas
* Dublin
* Hongkong SAR
* London
* Melbourne
* New York City
* Perth
* San-Antonio
* Seattle
* Söul
* Silicon Valley
* Singapore2 
* Sydney
* Taipei
* Tokyo
* Toronto
* Washington DC
* Washington DC2

### <a name="what-is-the-sla-for-expressroute-direct"></a>Vad är service avtalet för ExpressRoute Direct?

ExpressRoute Direct kommer att använda samma [ExpressRoute i företags klass](https://azure.microsoft.com/support/legal/sla/expressroute/v1_3/). 

### <a name="what-scenarios-should-customers-consider-with-expressroute-direct"></a>Vilka scenarier bör kunderna överväga med ExpressRoute Direct?  

ExpressRoute Direct tillhandahåller kunder med port par för Direct 100 eller 10 Gbit/s i Microsofts globala stamnät. De scenarier som ger kunderna de största fördelarna är: Massiv data inmatning, fysisk isolering för reglerade marknader och dedikerad kapacitet för burst-scenario, t. ex. åter givning. 

### <a name="what-is-the-billing-model-for-expressroute-direct"></a>Vad är fakturerings modellen för ExpressRoute Direct? 

ExpressRoute Direct debiteras för port paret vid ett fast belopp. Standard kretsar tas med utan ytterligare timmar och Premium har en kort avgift. Utgående kommer att debiteras per krets baserat på peering-platsens zon.

### <a name="when-does-billing-start-for-the-expressroute-direct-port-pairs"></a>När startar faktureringen för ExpressRoute Direct-port par?

ExpressRoute Directs port par faktureras 45 dagar i skapandet av ExpressRoute Direct-resursen eller när 1 eller båda länkarna är aktiverade, beroende på vilket som kommer först. Perioden på 45 dagar beviljas för att tillåta kunder att slutföra processen för kors anslutnings processen med en provider för samlokalisering.
