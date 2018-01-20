---
title: Priser och fakturering - Azure Logic Apps | Microsoft Docs
description: "Lär dig hur priser och fakturering fungerar för Logikappar i Azure."
author: kevinlam1
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: f8f528f5-51c5-4006-b571-54ef74532f32
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/11/2017
ms.author: LADocs; klam
ms.openlocfilehash: 49a66c826a98f7160b97b516e6fd541795ae3b0e
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2018
---
# <a name="logic-apps-pricing-model"></a>Prissättningsmodell för Logic Apps
Du kan skapa och köra automatiserade skalbara integration arbetsflöden i molnet med Azure Logikappar. Här finns information om hur fakturering och priser fungerar för Logic Apps.
## <a name="consumption-pricing-model"></a>Priserna för förbrukning
Med nyligen skapade logic apps betalar bara för att du använder. Nya logikappar använder en förbrukning plan och prissättning modell, vilket innebär att alla körningar som utförs av en logik app-instansen är avgiftsbelagda.
### <a name="what-are-action-executions"></a>Vad är åtgärden körningar?
Varje steg i en definition av logik app är en åtgärd som innehåller utlösare, kontroll flödet steg, anrop till inbyggda åtgärder och anrop till kopplingar.
### <a name="triggers"></a>Utlösare
Utlösare är särskilda åtgärder för att skapar en logik app instans när en viss händelse inträffar. Utlösare ha flera olika beteenden som påverkar hur logikappen mäts.
* **Avsökningen utlösaren** – den här utlösaren kontinuerligt kontrollerar att en slutpunkt tills den får ett meddelande som uppfyller kriterierna för att skapa en app-instansen för logik för att starta arbetsflödet. Du kan ställa in avsökningsintervallet i utlösaren via logik App Designer. Varje avsökning begäran räknas som en körning, även när inga logik app-instansen har skapats.
* **Webhook-utlösare** – den här utlösaren väntar på att en klient kan skicka en begäran till en viss slutpunkt. Varje begäran som skickas till slutpunkten webhook räknas som en körning av åtgärden. Till exempel utlösaren begäran och HTTP-Webhook är båda webhook-utlösare.
* **Återkommande utlösaren** – den här utlösaren skapar en logik app-instansen baserat på återkommande intervall som du ställer in i utlösaren. Exempelvis kan du ange upp en upprepning utlösare som körs varje tre dagar eller på ett mer komplext schema.

Utlösaren körningar hittar du i din logikapp översikt översiktsrutan under avsnittet utlösare tidigare.

### <a name="actions"></a>Åtgärder
Inbyggda åtgärder, t.ex, åtgärder som anropar HTTP, Azure Functions eller API-hantering, samt kontroll flödet stegen är avgiftsbelagda som interna åtgärder och har sina respektive typer. Åtgärder som anropar [kopplingar](https://docs.microsoft.com/connectors) har typen ”ApiConnection”. Kopplingar är klassificerade som standard eller enterprise kopplingar och förbrukade vid sina respektive [priser][pricing].
Alla fel och har kör åtgärder räknas och uppmätta som åtgärden körningar. Dock räknas åtgärder som hoppas över, på grund av unmet villkor eller åtgärder som inte kör eftersom logikappen avslutas innan du kan inte som åtgärden körningar. Inaktiverade logikappar kan inte instansiera nya instanser så att de inte debiteras när de är inaktiverade.

> [!NOTE]
> När du inaktiverar en logikapp kan dess instanser ta lite tid innan de slutar helt.

Åtgärder som körs i slingor räknas per varje cykel i en slinga. En enda åtgärd i en ”för var och en” loop som bearbetar en lista över 10-objekt räknas exempelvis genom att multiplicera antalet poster (10) med antalet åtgärder i en slinga (1) plus en för att starta loopen. I det här exemplet är därför, beräkningen (10 * 1) + 1, vilket resulterar i 11 åtgärd körningar.

### <a name="integration-account-usage"></a>Användningen av integration konton
Förbrukningsbaserad användning innehåller en [integrering konto](logic-apps-enterprise-integration-create-integration-account.md) där du kan utforska, utveckla och testa den [B2B/EDI](logic-apps-enterprise-integration-b2b.md) och [XML-bearbetning](logic-apps-enterprise-integration-xml.md) funktioner i Logic Apps utan extra kostnad. Du kan ha något av kontona integration per region och lagra upp till 10 avtal och 25 maps. Du kan ha och överför obegränsade partners, scheman och certifikat.

Logic Apps erbjuder också basic och standard integrationskonton med stöds Logic Apps SLA. Du kan använda grundläggande integrationskonton när du antingen använda endast meddelandehantering eller fungera som en partner för småföretag som har en handel partner-relation med en större företag entitet. Standard integrationskonton stöd för mer komplexa B2B-relationer och öka antalet enheter som du kan hantera. Mer information finns i [priser för Azure](https://azure.microsoft.com/pricing/details/logic-apps).

## <a name="pricing"></a>Prissättning
Mer information finns i [Logic Apps priser](https://azure.microsoft.com/pricing/details/logic-apps).

## <a name="next-steps"></a>Nästa steg
* [En översikt över Logic Apps][whatis]
* [Skapa din första logikapp][create]

[pricing]: https://azure.microsoft.com/pricing/details/logic-apps/
[whatis]: logic-apps-overview.md
[create]: quickstart-create-first-logic-app-workflow.md

