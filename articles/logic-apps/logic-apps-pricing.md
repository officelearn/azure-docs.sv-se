---
title: Priser och fakturering – Azure Logic Apps | Microsoft Docs
description: Läs om hur priser och fakturering fungerar för Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: logic-apps
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.assetid: f8f528f5-51c5-4006-b571-54ef74532f32
ms.topic: article
ms.date: 09/24/2018
ms.openlocfilehash: b75fba2ba0e9fa922b1252378e0bab326cada7d2
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46974314"
---
# <a name="pricing-model-for-azure-logic-apps"></a>Prismodellen för Azure Logic Apps

Du kan skapa och köra automatiska skalbara integration arbetsflöden i molnet med Azure Logic Apps. Här följer information om hur fakturering och priser fungerar för Logic Apps. 

<a name="consumption-pricing"></a>

## <a name="consumption-pricing-model"></a>Prismodellen för förbrukning

För nya logic-appar som du skapar med hjälp av offentliga eller ”global” Logic Apps-tjänsten, betalar du bara för det du använder. Dessa logikappar använder en förbrukningsbaserad plan och prissättningsmodell, vilket innebär att mäts alla åtgärdskörningar som utförs av en logikapp. Varje steg i en logikapp-definitioner är en åtgärd som innehåller utlösare, steg i flödet, anrop till inbyggda åtgärder och anrop till kopplingar. Mer information finns i [Logic Apps-priser](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="fixed-pricing"></a>

## <a name="fixed-pricing-model"></a>Fast prismodellen

> [!NOTE]
> Integreringstjänstmiljön är i *privat förhandsgranskning*. Att begära åtkomst, [skapa din begäran om att ansluta till här](https://aka.ms/iseprivatepreview).

För nya logic-appar som du skapar med en [ *integreringstjänstmiljön* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), vilket är ett privat isolerade Logic Apps-instans som använder dedikerade resurser, du betalar ett fast månadspris för inbyggda åtgärder och Standardanslutningar ISE etikett. Din ISE innehåller en Enterprise connector utan kostnad, medan ytterligare Enterprise-anslutningar debiteras baserat på priset för Enterprise-förbrukning. Mer information finns i [Logic Apps-priser](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="triggers"></a>

## <a name="triggers"></a>Utlösare

Utlösare är särskilda åtgärder för att skapar en logikappinstans när en viss händelse inträffar. Utlösare fungerar på olika sätt, vilket påverkar hur logikappen mäts.

* **Avsökningen utlösaren** – den här utlösaren kontinuerligt söker efter en slutpunkt för meddelanden som uppfyller kriterierna för att skapa en logikappinstans och starta arbetsflödet. Varje avsökning begäran räknas som en körning och mäts, även om inga logikappinstans skapas. Om du vill ange avsökningsintervallet för att konfigurera utlösaren via Logic App Designer.

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **Webhook-utlösaren** – den här utlösaren väntar på att en klient att skicka en begäran till en viss slutpunkt. Varje begäran som skickas till webhook-slutpunkt räknas som en åtgärdskörning. Till exempel begäran och HTTP-Webhook-utlösaren är båda webhook-utlösare.

* **Upprepningsutlösare** – den här utlösaren skapar en logikappinstans som baseras på upprepningsintervallet som du har konfigurerat i utlösaren. Du kan till exempel lägger upp en återkommande utlösare som körs var tredje dag eller enligt ett mer komplext schema.

Du kan hitta utlösarkörningar i din logikapp översiktsfönstret under avsnittet Utlösarhistorik.

## <a name="actions"></a>Åtgärder

Inbyggda åtgärder, till exempel åtgärder som att anropa HTTP, Azure Functions eller API Management och även styra steg i flödet mäts som inbyggda åtgärder som har sina respektive typer. Åtgärder som anropar [kopplingar](https://docs.microsoft.com/connectors) har typen ”ApiConnection”. Dessa anslutningar är klassificerade som standard eller enterprise anslutningsappar, som mäts baserat på deras respektive [priser][pricing]. 

Alla har och har inte kör åtgärder räknas och avgiftsbelagda som körningar. Åtgärder som hoppas över, på grund av motsvarar hittills ouppfyllda villkor eller åtgärder som inte kör eftersom logic app avslutas innan åtgärden har slutförts räknas dock inte som körningar. Inaktiverad logikappar kan inte skapa nya instanser så inte debiteras när de är inaktiverade.

> [!NOTE]
> När du inaktiverar en logikapp, kan alla instanser som körs för närvarande ta lite tid innan de helt slutar.

Åtgärder som körs inuti loopar räknas per varje cykel loopen. Till exempel räknas en enda åtgärd i en ”för var och en” loop som bearbetar en lista över 10-objekt genom att multiplicera antalet poster (10) med antalet åtgärder i loop (1) plus en för att starta loopen. I det här exemplet är därför beräkningen (10 * 1) + 1, vilket resulterar i 11 körningar.

## <a name="integration-account-usage"></a>Integrering Kontoanvändning

Förbrukningsbaserad användning innehåller en [integrationskontot](logic-apps-enterprise-integration-create-integration-account.md) där du kan utforska, utvecklar och testar den [B2B/EDI](logic-apps-enterprise-integration-b2b.md) och [XML-bearbetning](logic-apps-enterprise-integration-xml.md) funktioner i Logic Apps utan extra kostnad. Du kan ha ett integrationskonto per region och lagra upp till specifika [mängder artefakter](../logic-apps/logic-apps-limits-and-config.md), till exempel EDI-affärspartner och avtal, kartor, scheman, sammansättningar, certifikat och batchkonfigurationer.

Logic Apps erbjuder även grundläggande och standard-integrationskonton med stöds Logic Apps serviceavtal. Du kan använda basic-integrationskonton när du antingen vill använda endast meddelandehantering eller fungera som ett litet partnerföretag som har en handel partner-relation med en större företag entitet. Standard-integrationskonton stöder mer avancerade B2B-relationer och öka antalet enheter som du kan hantera. Mer information finns i [prissättning för Azure](https://azure.microsoft.com/pricing/details/logic-apps).

## <a name="next-steps"></a>Nästa steg

* [Läs mer om Logic Apps][whatis]
* [Skapa din första logikapp][create]

[pricing]: https://azure.microsoft.com/pricing/details/logic-apps/
[whatis]: logic-apps-overview.md
[create]: quickstart-create-first-logic-app-workflow.md

