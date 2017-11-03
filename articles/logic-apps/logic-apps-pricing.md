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
ms.date: 01/23/2017
ms.author: LADocs; klam
ms.openlocfilehash: 63784c5e3af360b2f3f8cb330a9df8b27a85d859
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="logic-apps-pricing-model"></a>Prissättningsmodell för Logic Apps
Med Azure Logikappar kan du skala och köra integration arbetsflöden i molnet.  Nedan visas information om fakturering och priser planer för Logic Apps.
## <a name="consumption-pricing"></a>Priser för förbrukning
Logic Apps använder nyskapad en plan för användning. Med Logic Apps förbrukning priserna betalar du bara för det du använder.  Logic Apps är inte begränsas när du använder en plan för användning.
Alla åtgärder som utförs i en körning av en logik app-instansen är avgiftsbelagda.
### <a name="what-are-action-executions"></a>Vad är åtgärden körningar?
Varje steg i en definition av logik app är en åtgärd som innehåller utlösare, Kontrollflöde steg som villkor, scope för varje slingor gör tills slingor, anrop till kopplingar och anrop till interna åtgärder.
Utlösare är särskilda åtgärder som är utformade för att skapa en instans av en ny instans av logikappen när en viss händelse inträffar.  Det finns flera olika beteenden för utlösare som kan påverka hur logikappen mäts.
* **Avsökningen utlösaren** – den här utlösaren kontinuerligt avsöker en slutpunkt tills den får ett meddelande som uppfyller villkoren för att skapa en instans av logikappen.  Avsökningsintervallet kan konfigureras i utlösaren i logik App Designer.  Varje avsökning begäran, räknas även om det inte att skapa en instans av en logikapp som en körning av åtgärden.
* **Webhook-utlösare** – den här utlösaren väntar på att en klient kan skicka en begäran om en viss slutpunkt.  Varje begäran som skickas till slutpunkten webhook räknas som en körning av åtgärden. Begäran och HTTP-Webhook-utlösare är båda webhook-utlösare.
* **Återkommande utlösaren** – den här utlösaren skapas en instans av logikappen baserat på Upprepningsintervall som konfigurerats i utlösaren.  Till exempel kan en upprepning utlösare konfigureras för att köra alla tre dagar eller även varje minut.

Utlösaren körningar kan ses i resursbladet Logic Apps utlösaren historik del.

Alla åtgärder som utfördes, om de hade lyckades eller misslyckades är avgiftsbelagda som en körning av åtgärden.  Åtgärder som hoppades över på grund av ett villkor inte uppfylls eller åtgärder som inte köras eftersom logikappen avslutas innan räknas inte som åtgärden körningar.

Åtgärder som utförs i slingor räknas per upprepning av loopen.  Till exempel en enda åtgärd i ett för varje loop söka igenom en lista över 10 objekt som ska räknas som antalet objekt i listan (10) multiplicerat med antalet åtgärder i en slinga (1) plus en för initiering av loopen , som i det här exemplet skulle vara (10 * 1) + 1 = 11 åtgärd körningar.
Inaktiverad Logic Apps kan inte ha nya instanser instansieras och därför vid inaktiveras debiteras inte.  Tänk på att det kan ta lite tid för instanserna som du vill inaktivera innan helt inaktiverad när du har inaktiverat en logikapp.
### <a name="integration-account-usage"></a>Användningen av integration konton
Ingår i förbrukningsbaserad användning är en [integrering konto](logic-apps-enterprise-integration-create-integration-account.md) för undersökning, utveckling och testning så att du kan använda den [B2B/EDI](logic-apps-enterprise-integration-b2b.md) och [XML-bearbetning](logic-apps-enterprise-integration-xml.md)funktioner i Logic Apps utan extra kostnad. Du kan skapa maximalt ett konto per region och lagra upp till 10 avtal och 25 maps. Scheman, certifikat och partners har inga begränsningar och du kan ladda upp så många som du behöver.

Du kan också skapa standard integrationskonton utan dessa begränsningar och med vår standard Logic Apps SLA förutom inkludering av integrationskonton med förbrukning. Mer information finns i [priser för Azure](https://azure.microsoft.com/pricing/details/logic-apps).

## <a name="app-service-plans"></a>App Service-planer
Logikappar som tidigare har skapat refererar till en App Service-Plan fortsätter att fungera som tidigare. Beroende på den valda planen har begränsats efter föreskrivna dagliga körningar överskrids men debiteras med hjälp av åtgärden körning mätaren.
EA-kunder som har en Apptjänstplan i sin prenumeration som inte har uttryckligen kopplas till Logikappen, få förmånen inkluderade kvantiteter.  Till exempel om du har en Standard App Service-Plan i prenumerationen EA och en Logikapp i samma prenumeration debiteras sedan du inte för 10 000 åtgärder körningar per dag (se följande tabell). 

App Service-planer och deras dagliga körningar tillåten åtgärd:
|  | Kostnadsfri/delad/enkel | Standard | Premium |
| --- | --- | --- | --- |
| Åtgärden körningar per dag |200 |10 000 |50,000 |
### <a name="convert-from-app-service-plan-pricing-to-consumption"></a>Konvertera från Apptjänstplan priser till förbrukning
Ta bort referensen till den App Service-Plan i logkappsdefinitionen om du vill ändra en Logikapp som har en App Service-Plan som är kopplade till den till en modell för användning.  Den här ändringen kan utföras med ett anrop till en PowerShell-cmdlet:`Set-AzureRmLogicApp -ResourceGroupName ‘rgname’ -Name ‘wfname’ –UseConsumptionModel -Force`
## <a name="pricing"></a>Prissättning
Information om priser, se [Logic Apps priser](https://azure.microsoft.com/pricing/details/logic-apps).

## <a name="next-steps"></a>Nästa steg
* [En översikt över Logic Apps][whatis]
* [Skapa din första logikapp][create]

[pricing]: https://azure.microsoft.com/pricing/details/logic-apps/
[whatis]: logic-apps-what-are-logic-apps.md
[create]: logic-apps-create-a-logic-app.md

