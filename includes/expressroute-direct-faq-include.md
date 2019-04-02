---
title: ta med fil
description: ta med fil
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: include
ms.date: 02/25/2019
ms.author: jaredro
ms.custom: include file
ms.openlocfilehash: 6a0d84a2aaf4b52c7fb0163231574eaea63c18c3
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2019
ms.locfileid: "58807935"
---
### <a name="what-is-expressroute-direct"></a>Vad är ExpressRoute direkt?

Med ExpressRoute Direct får kunder möjligheten att ansluta direkt till Microsofts globala nätverk vid peering-platser strategiskt fördelade runt om i världen. ExpressRoute Direct innehåller dubbla 100 Gbit/s-anslutningar som har stöd för aktiv/aktiv-anslutningar i stor skala. 

### <a name="how-do-customers-connect-to-expressroute-direct"></a>Hur ansluter kunder till ExpressRoute direkt? 

Kunder måste arbeta med sina lokala operatörer och samordningsleverantörer och få anslutningen till ExpressRoute-routrar att dra nytta av ExpressRoute direkt.

### <a name="what-locations-currently-support-expressroute-direct"></a>Vilka platser stöds för närvarande ExpressRoute direkt? 

Tillgängliga portar ska vara dynamisk och blir tillgängligt via PowerShell för att visa kapaciteten. Inkludera platser och *kan komma att ändras baserat på tillgänglighet*:

* Amsterdam
* Canberra
* Chicago
* Washington DC
* Dallas 
* Hongkong SAR
* Los Angeles
* New York City
* Paris
* San Antonio
* Silicon Valley
* Singapore 

### <a name="what-is-the-sla-for-expressroute-direct"></a>Vad är serviceavtalet för ExpressRoute direkt?

ExpressRoute Direct ska använda samma [företagsklass över ExpressRoute](https://azure.microsoft.com/support/legal/sla/expressroute/v1_3/). 

### <a name="what-scenarios-should-customers-consider-with-expressroute-direct"></a>Vilka scenarier bör kunderna med ExpressRoute direkt?  

ExpressRoute Direct tillhandahåller kunder med direkta 100 Gbit/s portpar i global inom Microsofts stamnätverk. Scenarier som ger kunderna med de största fördelarna är: Omfattande datainmatning, fysisk isolering för reglerade marknader och dedikerad kapacitet för burst-scenariot, t.ex. rendering. 

### <a name="what-is-the-billing-model-for-expressroute-direct"></a>Vad är faktureringsmodellen för ExpressRoute direkt? 

ExpressRoute Direct debiteras för port-par för ett fast belopp. Standard-kretsar som ska inkluderas i inga ytterligare timmar och premium har en liten tillägg avgift. Utgående trafik debiteras på basis av per krets baserat på zonen på peering-plats.

### <a name="when-does-billing-start-for-the-expressroute-direct-port-pairs"></a>När fungerar faktureringen start för portpar ExpressRoute direkt?

ExpressRoute Direct portpar faktureras 45 dagar i skapandet av ExpressRoute Direct resursen eller om 1 eller båda länkarna är aktiverade, beroende på vilket som inträffar först. Respitperioden på 45 dagar beviljas för att ge kunderna möjlighet att slutföra korsanslutning med samordning-providern.
