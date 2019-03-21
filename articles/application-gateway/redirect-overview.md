---
title: Översikt för omdirigering för Azure Application Gateway
description: Lär dig mer om omdirigering-funktionen i Azure Application Gateway
services: application-gateway
documentationcenter: na
author: amsriva
manager: jpconnock
tags: azure-resource-manager
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 3/19/2018
ms.author: amsriva
ms.openlocfilehash: 8e88e0e11b3ccab7cc2c68b2617df2d588680780
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58170062"
---
# <a name="application-gateway-redirect-overview"></a>Översikt över Application Gateway-omdirigering

Du kan använda application gateway för att omdirigera trafik.  Den har en allmän omdirigering mekanism som gör för att omdirigera trafik som tas emot i en lyssnare till en annan lyssnare eller till en extern webbplats. Detta förenklar konfigurationen, optimerar resursanvändningen och har stöd för den nya omdirigering scenarier inklusive globala och sökvägsbaserad omdirigering.

Ett vanligt scenario för omdirigering av för många webbprogram är att stödja automatisk HTTP till HTTPS-omdirigering för att säkerställa att all kommunikation mellan program och dess användare sker via en krypterad sökväg. Kunder har tidigare använde tekniker som skapar en dedikerad backend-adresspool vars syfte är att omdirigera begäran som tas emot på HTTP till HTTPS. Med stöd för omdirigering i Application Gateway kan åstadkommer du detta genom att lägga till en ny konfiguration för omdirigering till en regel för vidarebefordran och ange en annan lyssnare med HTTPS-protokollet som mållyssnare.

Följande typer av omdirigering stöds:

- 301 permanent omdirigering
- 302 hittades
- 303 Se annan
- 307 Temporary Redirect

Stöd för Application Gateway-stöd har följande funktioner:

-  **Global omdirigering**

   Omdirigerar från en lyssnare till en annan lyssnaren på gatewayen. Det möjliggör HTTP till HTTPS-omdirigering på en webbplats.
- **Sökvägsbaserad omdirigering**

   Den här typen av omdirigering kan HTTP till HTTPS-omdirigering endast på en viss plats, till exempel ett i kundvagnen område enligt/kundvagn / *.
- **Omdirigera till extern webbplats**

![omdirigera](./media/redirect-overview/redirect.png)

Kunder måste skapa en ny omdirigerings-konfigurationsobjekt, som anger mållyssnare eller extern webbplats som omdirigering är det önskade med den här ändringen. Konfigurationselementet har även stöd för alternativ för att lägga till URI-sökväg och fråga strängen till den omdirigerade platsen. Du kan också välja vilken typ av omdirigering. När du skapat är den här omdirigerings-konfigurationen kopplat till käll-lyssnaren via en ny regel. När du använder en grundläggande regel som är associerad med en käll-lyssnare omdirigerings-konfigurationen och är en global omdirigering. När en sökvägsbaserad regel används har omdirigerings-konfiguration definierats för Webbadress för sökvägskarta. Så gäller detta bara till området angiven sökväg för en plats.

### <a name="next-steps"></a>Nästa steg

[Konfigurera URL-omdirigering på application gateway](tutorial-url-redirect-powershell.md)
