---
title: "Omdirigera översikt för Azure Programgateway | Microsoft Docs"
description: "Lär dig mer om omdirigering-funktionen i Azure Programgateway"
services: application-gateway
documentationcenter: na
author: amsriva
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/18/2017
ms.author: amsriva
ms.openlocfilehash: ea9ae8373ff67bf9557b06bbc8a4b0d82a03e2d0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="application-gateway-redirect-overview"></a>Översikt över omdirigering Gateway

Ett vanligt scenario för många webbprogram är att stödja automatisk HTTP till HTTPS-omdirigering för att säkerställa att all kommunikation mellan program och dess användare sker via en krypterad sökväg. Kunder har tidigare använt tekniker som skapar en dedikerad backend-adresspool vars syfte är att omdirigera begäranden tas emot av HTTP till HTTPS.  Programgateway stöder nu möjligheten att omdirigera trafik på Programgatewayen. Detta förenklar programkonfigurationen, optimerar resursanvändningen och har stöd för den nya omdirigering scenarier inklusive globala och sökväg-baserade omdirigering. Stöd för program Gateway omdirigering är inte begränsat till HTTP -> enbart HTTPS-omdirigering. Detta är en generisk omdirigering-mekanism som gör det möjligt för omdirigering av trafik som tas emot på en lyssnare på en annan lyssnare på en Programgateway. Det stöder också omdirigering till en extern plats. Programstöd för Gateway-omdirigering innehåller följande funktioner:

1. Globala omdirigering från en lyssnare på en annan lyssnare på Gateway. Detta gör HTTP till HTTPS-omdirigering på en plats.
2. Sökväg-baserade omdirigering. Den här typen av omdirigering aktiverar HTTP till HTTPS-omdirigering endast på en viss plats, till exempel ett shopping kundvagn område betecknas med/kundvagn / *.
3. Omdirigera till extern webbplats.

![omdirigering](./media/application-gateway-redirect-overview/redirect.png)

Med den här ändringen skulle kunder måste du skapa en ny omdirigering konfigurationsobjekt, som anger mål-lyssnaren eller extern webbplats som önskas omdirigering. Konfigurationselementet har också stöd för alternativ för att aktivera att URI-sökväg och fråga strängen till den omdirigerade platsen. Kunder kan också välja om omdirigering är en tillfällig (HTTP-statuskod 302) eller en permanent omdirigering (HTTP-statuskod 301). När du skapat den här konfigurationen för omdirigering är kopplad till käll-lyssnaren via en ny regel. När du använder en enkel regel omdirigerings-konfigurationen är associerad med en käll-lyssnare och är en global omdirigering. När en sökväg-baserade regel används omdirigerings-konfigurationen har definierats på kartan för URL-sökväg och gäller därför bara till området angiven sökväg för en plats.

### <a name="next-steps"></a>Nästa steg

[Konfigurera URL: en omdirigering på en Programgateway](application-gateway-configure-redirect-powershell.md)
