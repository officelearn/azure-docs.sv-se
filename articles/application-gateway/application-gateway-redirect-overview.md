---
title: Omdirigera översikt för Azure Application Gateway | Microsoft Docs
description: Lär dig mer om omdirigering-funktionen i Azure Application Gateway
services: application-gateway
documentationcenter: na
author: amsriva
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/18/2017
ms.author: amsriva
ms.openlocfilehash: d05d509b67fd26c958e0e2fa2bbd877db26e6521
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2019
ms.locfileid: "54232217"
---
# <a name="application-gateway-redirect-overview"></a>Översikt över Application Gateway-omdirigering

Ett vanligt scenario för många webbprogram är att stödja automatisk HTTP till HTTPS-omdirigering för att säkerställa att all kommunikation mellan program och dess användare sker via en krypterad sökväg. Kunder har tidigare använde tekniker som skapar en dedikerad backend-adresspool vars syfte är att omdirigera begäran som tas emot på HTTP till HTTPS.  Application gateway stöder nu möjligheten att omdirigera trafik på Application Gateway. Detta förenklar konfigurationen, optimerar resursanvändningen och har stöd för den nya omdirigering scenarier inklusive globala och sökvägsbaserad omdirigering. Omdirigering av stöd för Application Gateway är inte begränsad till HTTP -> enbart HTTPS-omdirigering. Det här är en mekanism för allmän omdirigering där för att dirigera om trafik som tas emot i en lyssnare till en annan lyssnaren på Application Gateway. Den har även stöd för omdirigering till en extern plats. Stöd för Application Gateway-stöd har följande funktioner:

1. Global omdirigering från en lyssnare till en annan lyssnaren på gatewayen. Det möjliggör HTTP till HTTPS-omdirigering på en webbplats.
2. Sökvägsbaserad omdirigering. Den här typen av omdirigering kan HTTP till HTTPS-omdirigering endast på en viss plats, till exempel ett i kundvagnen område enligt/kundvagn / *.
3. Omdirigera till extern webbplats.

![omdirigera](./media/application-gateway-redirect-overview/redirect.png)

Med den här ändringen behöver kunder att skapa en ny omdirigerings-konfigurationsobjekt, som anger mållyssnare eller extern webbplats som omdirigering är det önskade. Konfigurationselementet har även stöd för alternativ för att lägga till URI-sökväg och fråga strängen till den omdirigerade platsen. Kunder kan också välja om omdirigering är en tillfällig (HTTP-statuskod 302) eller en permanent omdirigering (HTTP-statuskod 301). När du skapat den här omdirigerings-konfigurationen är kopplat till käll-lyssnaren via en ny regel. När du använder en grundläggande regel som är associerad med en käll-lyssnare omdirigerings-konfigurationen och är en global omdirigering. När en sökvägsbaserad regel används omdirigerings-konfiguration har definierats för Webbadress för sökvägskarta och gäller därför bara till området angiven sökväg för en plats.

### <a name="next-steps"></a>Nästa steg

[Konfigurera HTTP till HTTPS-omdirigering på application gateway](redirect-http-to-https-portal.md)
