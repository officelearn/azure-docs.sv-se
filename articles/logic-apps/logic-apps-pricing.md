---
title: Priser och fakturering - Azure Logic Apps | Microsoft Docs
description: Lär dig hur priser och fakturering fungerar för Logikappar i Azure
services: logic-apps
author: kevinlam1
manager: jeconnoc
editor: ''
documentationcenter: ''
ms.assetid: f8f528f5-51c5-4006-b571-54ef74532f32
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 05/11/2018
ms.author: klam
ms.openlocfilehash: e1702de42be8510412a6479b594a198a84d15ae2
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299264"
---
# <a name="logic-apps-pricing-model"></a>Prissättningsmodell för Logic Apps

Du kan skapa och köra automatiserade skalbara integration arbetsflöden i molnet med Azure Logikappar. Här finns information om hur fakturering och priser fungerar för Logic Apps. 

## <a name="consumption-pricing-model"></a>Priserna för förbrukning

Med nyligen skapade logic apps betalar bara för att du använder. Nya logikappar Använd förbrukning plan och prissättningsmodell, vilket innebär att alla åtgärd körningar som utförs av en logik app-instansen är avgiftsbelagda. Varje steg i en definition av logik app är en åtgärd som innehåller utlösare, kontroll flödet steg, anrop till inbyggda åtgärder och anrop till kopplingar. Mer information finns i [Logic Apps priser](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="triggers"></a>

## <a name="triggers"></a>Utlösare

Utlösare är särskilda åtgärder för att skapar en logik app instans när en viss händelse inträffar. Utlösare fungerar på olika sätt, som påverkar hur logikappen mäts.

* **Avsökningen utlösaren** – den här utlösaren kontinuerligt kontrollerar en slutpunkt för meddelanden som uppfyller kriterierna för att skapa en logik app-instansen och starta om arbetsflödet. Varje avsökning begäran räknas som en körning och mäts, även när inga logik app-instansen har skapats. Ställ in utlösaren via logik App Designer för att ange avsökningsintervallet.

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **Webhook-utlösare** – den här utlösaren väntar på att en klient kan skicka en begäran till en viss slutpunkt. Varje begäran som skickas till slutpunkten webhook räknas som en körning av åtgärden. Till exempel utlösaren begäran och HTTP-Webhook är båda webhook-utlösare.

* **Återkommande utlösaren** – den här utlösaren skapar en logik app-instansen baserat på återkommande intervall som du ställer in i utlösaren. Exempelvis kan du ange upp en upprepning utlösare som körs varje tre dagar eller på ett mer komplext schema.

Utlösaren körningar hittar du i din logikapp översikt översiktsrutan under avsnittet utlösare tidigare.

## <a name="actions"></a>Åtgärder

Inbyggda åtgärder, till exempel åtgärder som anropa HTTP, Azure Functions eller API-hantering och också styra flödet stegen är avgiftsbelagda som interna åtgärder, som har sina respektive typer. Åtgärder som anropar [kopplingar](https://docs.microsoft.com/connectors) har typen ”ApiConnection”. De här anslutningarna är klassificerade som standard eller enterprise kopplingar, vilka är avgiftsbelagda baserat på deras respektive [priser][pricing]. 

Alla fel och har kör åtgärder räknas och uppmätta som åtgärden körningar. Dock räknas åtgärder som hoppas över, på grund av unmet villkor eller åtgärder som inte kör eftersom logikappen avslutas innan du kan inte som åtgärden körningar. Inaktiverade logikappar kan inte instansiera nya instanser så att de inte debiteras när de är inaktiverade.

> [!NOTE]
> När du inaktiverar en logikapp kan alla instanser ta en stund innan de slutar helt.

Åtgärder som körs i slingor räknas per varje cykel i en slinga. En enda åtgärd i en ”för var och en” loop som bearbetar en lista över 10-objekt räknas exempelvis genom att multiplicera antalet poster (10) med antalet åtgärder i en slinga (1) plus en för att starta loopen. I det här exemplet är därför, beräkningen (10 * 1) + 1, vilket resulterar i 11 åtgärd körningar.

## <a name="integration-account-usage"></a>Användningen av integration konton

Förbrukningsbaserad användning innehåller en [integrering konto](logic-apps-enterprise-integration-create-integration-account.md) där du kan utforska, utveckla och testa den [B2B/EDI](logic-apps-enterprise-integration-b2b.md) och [XML-bearbetning](logic-apps-enterprise-integration-xml.md) funktioner i Logic Apps utan extra kostnad. Du kan ha ett konto för integrering per region och lagra upp till specifika [antal artefakter](../logic-apps/logic-apps-limits-and-config.md), till exempel EDI handelspartner och avtal, kartor, scheman, sammansättningar, certifikat och batch-konfigurationer.

Logic Apps erbjuder också basic och standard integrationskonton med stöds Logic Apps SLA. Du kan använda grundläggande integrationskonton när du antingen använda endast meddelandehantering eller fungera som en partner för småföretag som har en handel partner-relation med en större företag entitet. Standard integrationskonton stöd för mer komplexa B2B-relationer och öka antalet enheter som du kan hantera. Mer information finns i [priser för Azure](https://azure.microsoft.com/pricing/details/logic-apps).

## <a name="next-steps"></a>Nästa steg

* [Mer information om Logic Apps][whatis]
* [Skapa din första logikapp][create]

[pricing]: https://azure.microsoft.com/pricing/details/logic-apps/
[whatis]: logic-apps-overview.md
[create]: quickstart-create-first-logic-app-workflow.md

