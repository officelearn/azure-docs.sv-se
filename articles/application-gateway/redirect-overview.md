---
title: Omdirigeringsöversikt för Azure Application Gateway
description: Lär dig mer om omdirigeringsfunktionen i Azure Application Gateway för att omdirigera trafik som tas emot på en lyssnare till en annan lyssnare eller till en extern webbplats.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 11/16/2019
ms.author: amsriva
ms.openlocfilehash: 5943d8aad4d5dd0d981fae9b2325dd3fc75b31e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74129881"
---
# <a name="application-gateway-redirect-overview"></a>Översikt över omdirigering av programgateway

Du kan använda programgateway för att omdirigera trafik.  Den har en allmän omdirigeringsmekanism som gör det möjligt att omdirigera trafik som tas emot i en lyssnare till en annan lyssnare eller till en webbadress till en extern webbplats. Detta förenklar programkonfigurationen, optimerar resursanvändningen och stöder nya omdirigeringsscenarier, inklusive global och sökvägsbaserad omdirigering.

Ett vanligt omdirigeringsscenario för många webbprogram är att stödja automatisk HTTP-till HTTPS-omdirigering för att säkerställa all kommunikation mellan programmet och dess användare sker via en krypterad sökväg. Tidigare har kunder använt tekniker som att skapa en dedikerad backend-pool vars enda syfte är att omdirigera begäranden som den tar emot på HTTP till HTTPS. Med omdirigeringsstöd i Application Gateway kan du åstadkomma detta genom att helt enkelt lägga till en ny omdirigeringskonfiguration i en routningsregel och ange en annan lyssnare med HTTPS-protokoll som målavlyssnare.

Följande typer av omdirigering stöds:

- 301 Permanent omdirigering
- 302 Hittade
- 303 Se andra
- 307 Tillfällig omdirigering

Stöd för Application Gateway-stöd har följande funktioner:

-  **Global omdirigering**

   Omdirigerar från en lyssnare till en annan lyssnare på gatewayen. Det möjliggör HTTP till HTTPS-omdirigering på en webbplats.
- **Sökvägsbaserad omdirigering**

   Den här typen av omdirigering gör det möjligt för HTTP till HTTPS-omdirigering endast på ett visst platsområde, till exempel ett kundvagnsområde som betecknas med /cart/*.
- **Omdirigera till extern webbplats**

![Omdirigera](./media/redirect-overview/redirect.png)

Med den här ändringen måste kunderna skapa ett nytt omdirigeringskonfigurationsobjekt, som anger målavlyssnaren eller den externa platsen som omdirigering önskas. Konfigurationselementet stöder också alternativ för att aktivera aktivering av URI-sökvägen och frågesträngen till den omdirigerade URL:en. Du kan också välja typ av omdirigering. När den här omdirigeringskonfigurationen har skapats kopplas den till källavlyssnaren via en ny regel. När du använder en grundregel associeras omdirigeringskonfigurationen med en källavlyssnare och är en global omdirigering. När en sökvägsbaserad regel används definieras omdirigeringskonfigurationen på URL-sökvägen. Så det gäller bara för det specifika sökvägsområdet på en webbplats.

### <a name="next-steps"></a>Nästa steg

[Konfigurera URL-omdirigering på en programgateway](tutorial-url-redirect-powershell.md)
