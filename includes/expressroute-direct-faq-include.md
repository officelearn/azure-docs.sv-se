---
title: ta med fil
description: ta med fil
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 09/12/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f4a1937062f7e59cb3ef3f38610e077e8d36a3ac
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47005569"
---
### <a name="what-is-expressroute-direct"></a>Vad är ExpressRoute direkt?

ExpressRoute Direct ger kunderna möjlighet att ansluta direkt till Microsofts globala nätverk på peering-platser strategiskt distribueras över hela världen. ExpressRoute Direct innehåller dubbla 100Gbps anslutning som har stöd för aktiv/aktiv-anslutningar i stor skala. 

### <a name="how-do-customers-connect-to-expressroute-direct"></a>Hur ansluter kunder till ExpressRoute direkt? 

Kunder måste arbeta med sina lokala operatörer och samordningsleverantörer och få anslutningen till ExpressRoute-routrar att dra nytta av ExpressRoute direkt.

### <a name="what-locations-will-the-100gbps-expressroute-direct-be-available-for-public-preview"></a>Vilka platser 100Gbps ExpressRoute Direct blir tillgänglig i offentlig förhandsversion? 

Ett begränsat antal ExpressRoute-peeringplatser stöder detta i den offentliga förhandsversionen. Tillgängliga portar ska vara dynamisk och blir tillgängligt via PowerShell för att visa kapaciteten. Inkludera platser och *kan komma att ändras baserat på tillgänglighet*:

* Amsterdam
* Canberra
* Chicago
* Washington DC
* Dallas 
* Hongkong SAR
* Los Angeles
* New York City
* Paris
* SAN Antonio
* Silicon Valley
* Singapore 

### <a name="what-is-the-sla-for-expressroute-direct"></a>Vad är serviceavtalet för ExpressRoute direkt?

ExpressRoute Direct ska använda samma [företagsklass över ExpressRoute](https://azure.microsoft.com/support/legal/sla/expressroute/v1_3/). 

### <a name="what-scenarios-should-customers-consider-with-expressroute-direct"></a>Vilka scenarier bör kunderna med ExpressRoute direkt?  

ExpressRoute Direct tillhandahåller kunder med direkta 100Gbps portpar om global inom Microsofts stamnätverk. Scenarier som ger kunderna med de största fördelarna är: enorma datainmatning, fysisk isolering för reglerade marknader och dedikerad kapacitet för burst-scenariot som återgivning. 

### <a name="what-is-the-billing-model-for-expressroute-direct"></a>Vad är faktureringsmodellen för ExpressRoute direkt? 

ExpressRoute Direct debiteras för port-par för ett fast belopp. Standard-kretsar som ska inkluderas i inga ytterligare timmar och premium har en liten tillägg avgift. Utgående trafik debiteras på basis av per krets baserat på zonen på peering-plats.