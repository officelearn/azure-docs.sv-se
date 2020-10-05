---
title: Prissättnings scenarier för att anropa (röst/video) och chatta
titleSuffix: An Azure Communication Services concept document
description: Läs mer om pris modell för kommunikations tjänster.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/29/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 0b82d7dc8f6aaaa28a5293966440f058fbf42dc1
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "91460942"
---
# <a name="pricing-scenarios"></a>Prissättnings scenarier

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

<!--
> [!WARNING]
> This document is under construction and needs the following items to be addressed:
> - Looks like other resources point to a /pricing page that is managed by Commerce or Marketing? https://azure.microsoft.com/pricing/details/functions/ Should we? FOLLOWING UP WITH KRISTIN TO FIND THE RIGHT ACS PAGE
-->

Priserna för Azure Communication Services baseras på en modell där du betalar per användning utan några start avgifter. Du debiteras bara för förbrukningen och användningen av tjänsterna.

## <a name="voicevideo-calling-and-screen-sharing"></a>Röst-/video samtal och skärm delning

Azure Communication Services gör det möjligt att lägga till röst-/video samtal och skärm delning till dina program. Du kan bädda in upplevelsen i dina program med hjälp av Java Script, mål-C (Apple), Java (Android) eller .NET-klient bibliotek. Se vår [fullständiga lista över tillgängliga klient bibliotek](./sdk-options.md).

### <a name="pricing"></a>Prissättning

Anrops-och skärm delnings tjänster debiteras per minut per deltagare vid $0,004 per deltagare per minut för grupp anrop. Se [den här sidan](./call-flows.md)för att förstå de olika anrops flöden som är möjliga.

Varje deltagare i samtalet debiteras för varje minut som de är anslutna till samtalet. Detta gäller oavsett om användaren är video samtal, röst samtal eller skärm delning.

### <a name="pricing-example-group-audiovideo-call-using-js-and-ios-client-libraries"></a>Prissättnings exempel: gruppera ljud-/video samtal med hjälp av JS-och iOS-klient bibliotek

Alice gjorde ett grupp samtal med kollegor, Bob och Kalle. Alice och Robert använde JS-klient biblioteken, Kalle-klient bibliotek för iOS.

- Anropet varar totalt 60 minuter.
- Alice och Bob deltog i hela anropet. Alice aktiverade videon i fem minuter och delat sin skärm i 23 minuter. Bob hade videon på hela samtalet (60 minuter) och delat sin skärm i 12 minuter.
- Kalle lämnade samtalet efter 43 minuter. Kalle använde ljud och video under tiden som han deltog (43 minuter).

**Kostnads beräkningar**

- 2 deltagare x 60 minuter x $0,004 per deltagare per minut = $0,48 [både video och ljud debiteras enligt samma pris]
- 1 deltagare x 43 minuter x $0,004 per deltagare per minut = $0,172 [både video och ljud debiteras enligt samma pris]

**Total kostnad för grupp anropet**: $0,48 + $0,172 = $0,652

## <a name="chat"></a>Chatt

Med kommunikations tjänster kan du förbättra ditt program med möjligheten att skicka och ta emot chatt meddelanden mellan två eller flera användare. Chat-klient bibliotek är tillgängliga för Java Script, .NET, python och Java. Läs [mer om klient bibliotek på den här sidan](./sdk-options.md)

### <a name="price"></a>Pris

- Du debiteras $0,0008 för varje chatt-meddelande som skickas.

### <a name="pricing-example-chat-between-two-users"></a>Prissättnings exempel: chatta mellan två användare 

Geeta startar en chatt-tråd med Emily för att dela en uppdatering och skicka 5 meddelanden. Chatten varar 10 minuter där Geeta och Emily skickar ytterligare 15 meddelanden vart och ett.

**Kostnads beräkningar** 
- Antal skickade meddelanden (5 + 15 + 15) x $0,0008 = $0,028

### <a name="pricing-example-group-chat-with-multiple-users"></a>Prissättnings exempel: gruppera chatt med flera användare 

Kalle startar en chatt-tråd med sina vänner Casey & Jasmine för att planera en semester. De chattar för ett tag därin Kalle, Casey & Jasmine skicka 20, 30 respektive 18 meddelanden. De inser att deras vän ros kan vara intresserade av att även delta i resan, så att de lägger till henne i chatten och delar alla meddelande historiken med henne. 

Ros ser meddelandena och börjar chatta. I Casey får du ett samtal och han bestämmer sig för att komma igång med konversationen senare. Kalle, Jasmine & ros bestämmer sig för rese datum och skickar ytterligare 30, 25 respektive 35-meddelanden.

**Kostnads beräkningar** 

- Antal skickade meddelanden (20 + 30 + 18 + 30 + 25 + 35) x $0,0008 = $0,1264
