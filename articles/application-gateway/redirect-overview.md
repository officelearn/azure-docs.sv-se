---
title: Översikt över omdirigering för Azure Programgateway
description: Lär dig mer om omdirigering-funktionen i Azure Programgateway
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
ms.openlocfilehash: 65c631ca9beb5eab5d8fe2b7e71daa0cf3b768fa
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/04/2018
ms.locfileid: "33204385"
---
# <a name="application-gateway-redirect-overview"></a>Översikt över omdirigering Gateway

Ett vanligt scenario för många webbprogram är att stödja automatisk HTTP till HTTPS-omdirigering för att säkerställa att all kommunikation mellan program och dess användare sker via en krypterad sökväg. Kunder har tidigare använt tekniker som skapar en dedikerad backend-adresspool vars syfte är att omdirigera begäranden tas emot av HTTP till HTTPS.

Application Gateway stöder nu möjligheten att omdirigera trafik på gateway. Detta förenklar programkonfigurationen, optimerar resursanvändningen och har stöd för den nya omdirigering scenarier inklusive globala och sökväg-baserade omdirigering. Stöd för program Gateway omdirigering är inte begränsat till HTTP -> enbart HTTPS-omdirigering. Den har en generisk omdirigering mekanism som tillåter trafik omdirigering som tagits emot efter att en lyssnare på en annan lyssnare på en Programgateway. Extern webbplats omdirigering stöds också.

Programstöd för Gateway-omdirigering innehåller följande funktioner:

-  **Globala omdirigering**

   Omdirigeringar från en lyssnare på en annan lyssnare på gateway. Detta gör HTTP till HTTPS-omdirigering på en plats.
- **Sökväg-baserade omdirigering**

   Den här typen av omdirigering aktiverar HTTP till HTTPS-omdirigering endast på en viss plats, till exempel ett shopping kundvagn område betecknas med/kundvagn / *.
- **Omdirigera till extern webbplats**

![omdirigering](./media/redirect-overview/redirect.png)

Med den här ändringen måste kunder du skapa en ny omdirigering konfigurationsobjekt, som anger mål-lyssnaren eller extern webbplats som önskas omdirigering. Konfigurationselementet har också stöd för alternativ för att aktivera att URI-sökväg och fråga strängen till den omdirigerade platsen. Du kan också välja om omdirigering är en tillfällig (HTTP-statuskod 302) eller en permanent omdirigering (HTTP-statuskod 301). När du skapat är den här konfigurationen för omdirigering kopplad till käll-lyssnaren via en ny regel. När du använder en enkel regel omdirigerings-konfigurationen är associerad med en käll-lyssnare och är en global omdirigering. När du använder en sökväg-baserade regel definieras omdirigerings-konfigurationen på kartan för URL-sökväg. Detta gäller endast till området angiven sökväg för en plats.

### <a name="next-steps"></a>Nästa steg

[Konfigurera URL: en omdirigering på en Programgateway](tutorial-url-redirect-powershell.md)
