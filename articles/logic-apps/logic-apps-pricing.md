---
title: Priser och fakturering – Azure Logic Apps | Microsoft Docs
description: Läs om hur priser och fakturering fungerar för Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: logic-apps
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
manager: carmonm
ms.assetid: f8f528f5-51c5-4006-b571-54ef74532f32
ms.topic: article
ms.date: 10/16/2018
ms.openlocfilehash: 04fb86f9b8f8be2c013f9bd7449dd5a4b2bcf90c
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2018
ms.locfileid: "51854126"
---
# <a name="pricing-model-for-azure-logic-apps"></a>Prismodellen för Azure Logic Apps

Du kan skapa och köra automatiserade integration arbetsflöden som kan skalas i molnet när du använder Azure Logic Apps. Här följer information om hur fakturering och priser fungerar för Logic Apps. 

<a name="consumption-pricing"></a>

## <a name="consumption-pricing-model"></a>Prismodellen för förbrukning

För nya logic apps som körs i offentlig eller ”global” Logic Apps-tjänsten, betalar du bara för det du använder. Dessa logikappar använder en förbrukningsbaserad plan och prismodell. I logic app-definition är varje steg en åtgärd. Åtgärder omfattar utlösaren, alla steg i flödet, inbyggda åtgärder och connector-anrop. Logic Apps-mätare alla åtgärder som körs i din logikapp.  
Mer information finns i [Logic Apps-priser](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="fixed-pricing"></a>

## <a name="fixed-pricing-model"></a>Fast prismodellen

För nya logic apps som körs i en [ *integreringstjänstmiljön* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), du betalar ett fast månadspris för inbyggda åtgärder och Standardanslutningar ISE etikett. En ISE kan du skapa och köra isolerade logikappar som kan komma åt resurser i Azure-nätverk.  

Din ISE innehåller en kostnadsfria anslutningstjänsten, som innehåller antalet anslutningar som du vill. Användningen av ytterligare Enterprise anslutningar debiteras baserat på priset för Enterprise-förbrukning. 

> [!NOTE]
> Integreringstjänstmiljön är i *privat förhandsgranskning*. Att begära åtkomst, [skapa din begäran om att ansluta till här](https://aka.ms/iseprivatepreview). Mer information finns i [Logic Apps-priser](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="triggers"></a>

## <a name="triggers"></a>Utlösare

Utlösare är särskilda åtgärder för att skapar en logikappinstans när en viss händelse inträffar. Utlösare fungerar på olika sätt, vilket påverkar hur logikappen mäts.

* **Avsökningen utlösaren** – den här utlösaren kontinuerligt söker efter en slutpunkt för meddelanden som uppfyller kriterierna för att skapa en logikappinstans och starta arbetsflödet. Även när inga logikappinstans skapas, Logic Apps-mätare varje avsökning-begäran som en körning. Om du vill ange avsökningsintervallet för att konfigurera utlösaren via Logic App Designer.

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **Webhook-utlösaren** – den här utlösaren väntar på att en klient att skicka en begäran till en viss slutpunkt. Varje begäran som skickas till webhook-slutpunkt räknas som en åtgärdskörning. Till exempel begäran och HTTP-Webhook-utlösaren är båda webhook-utlösare.

* **Upprepningsutlösare** – den här utlösaren skapar en logikappinstans som baseras på upprepningsintervallet som du har konfigurerat i utlösaren. Du kan till exempel lägger upp en återkommande utlösare som körs var tredje dag eller enligt ett mer komplext schema.

## <a name="actions"></a>Åtgärder

Logic Apps-mätare inbyggda åtgärder som inbyggda åtgärder. Till exempel omfattar inbyggda åtgärder anrop via HTTP, anrop från Azure Functions eller API Management och control flow steg, till exempel loopar och villkor 
- var och en med sin egen åtgärdstyp. Åtgärder som anropar [kopplingar](https://docs.microsoft.com/connectors) har typen ”ApiConnection”. Dessa anslutningar är klassificerade som standard eller enterprise anslutningsappar, som mäts baserat på deras respektive [priser][pricing]. Enterprise-anslutningsappar i *förhandsversion* debiteras som standard kopplingar.

Logic Apps taxor kör alla har och har inte åtgärder som körningar. De här åtgärderna läsa inte av Logic Apps: 

* Åtgärder som hämta hoppades över på grund av motsvarar hittills ouppfyllda villkor
* Åtgärder som inte kör eftersom logikappen har stoppats innan du avslutar

Inaktiverad logikappar debiteras inte när inaktiverats eftersom de inte kan skapa nya instanser.

> [!NOTE]
> När du inaktiverar en logikapp, kan alla instanser som körs för närvarande ta lite tid innan de helt slutar.

För åtgärder som körs i slingor, räknas Logic Apps varje åtgärd per cykel i loopen. Anta exempelvis att du har en ”för var och en” loop som bearbetar en lista. Logic Apps taxor en åtgärd i den loopen genom att multiplicera antalet lista över objekt med antalet åtgärder i loopen och lägger till den åtgärd som startar loopen. Beräkning för en lista över 10-objekt är (10 * 1) + 1, vilket resulterar i 11 körningar.

## <a name="integration-account-usage"></a>Integrering Kontoanvändning

Förbrukningsbaserad användning tillämpas till [integrationskonton](logic-apps-enterprise-integration-create-integration-account.md) där du kan utforska, utvecklar och testar den [B2B/EDI](logic-apps-enterprise-integration-b2b.md) och [XML-bearbetning](logic-apps-enterprise-integration-xml.md) funktioner i Logic Apps utan extra kostnad. Du kan ha ett integrationskonto per region. Varje integrationskontot kan lagra upp till specifika [mängder artefakter](../logic-apps/logic-apps-limits-and-config.md), som innehåller samarbetspartner, avtal, kartor, scheman, sammansättningar, certifikat, batchkonfigurationer och så vidare.

Logic Apps erbjuder även grundläggande och standard-integrationskonton med stöds Logic Apps serviceavtal. Du kan använda basic-integrationskonton när du bara vill meddelandehantering eller fungera som ett litet partnerföretag som har en handel partner-relation med en större företag entitet. Standard-integrationskonton stöder mer avancerade B2B-relationer och öka antalet enheter som du kan hantera. Mer information finns i [prissättning för Azure](https://azure.microsoft.com/pricing/details/logic-apps).

## <a name="next-steps"></a>Nästa steg

* [Läs mer om Logic Apps][whatis]
* [Skapa din första logikapp][create]

[pricing]: https://azure.microsoft.com/pricing/details/logic-apps/
[whatis]: logic-apps-overview.md
[create]: quickstart-create-first-logic-app-workflow.md

