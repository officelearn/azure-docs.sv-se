---
title: Fördröjning nästa åtgärd i arbetsflöden – Azure Logic Apps
description: Vänta med att köra nästa åtgärd i logikappens arbetsflöde med hjälp av fördröjning eller fördröjning tills åtgärder i Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: deli, klam, LADocs
tags: connectors
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: 27475fb3f086dbc5166a473e9d657d2dab723938
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66297637"
---
# <a name="delay-running-the-next-action-in-azure-logic-apps"></a>Fördröjning som körs nästa åtgärd i Azure Logic Apps

Om du vill att din logikapp vänta en viss tidsperiod innan du kör nästa åtgärd, kan du lägga till inbyggt **fördröjning – schemalägga** före en instruktion i logikappens arbetsflöde. Du kan också lägga till inbyggt **Fördröj tills – schemalägga** åtgärder för att vänta tills ett specifikt datum och tid innan du kör nästa åtgärd. Läs mer om inbyggda schema-åtgärder och utlösare, [schema och kör återkommande automatiserade uppgifter och arbetsflöden med Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

* **fördröjning**: Vänta tills det angivna antalet tidsenheter, till exempel sekunder, minuter, timmar, dagar, veckor eller månader innan nästa åtgärd körs.

* **Fördröj tills**: Vänta tills angivet datum och tid innan nästa åtgärd körs.

Här följer några exempel-metoder för att använda de här åtgärderna:

* Vänta tills en veckodag att skicka en statusuppdatering via e-post.

* Fördröja arbetsflödet tills ett HTTP-anrop har slutförts innan du återupptar och hämtning av data.

## <a name="prerequisites"></a>Nödvändiga komponenter

* En Azure-prenumeration. Om du inte har någon prenumeration kan du [registrera dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/).

* Grundläggande kunskaper om [logikappar](../logic-apps/logic-apps-overview.md). Innan du kan använda en åtgärd, måste din logikapp först starta med en utlösare. Du kan använda en utlösare som du vill och lägga till andra åtgärder innan du lägger till en fördröjningsåtgärden. Det här avsnittet använder en Office 365 Outlook-utlösare. Om du är nybörjare till logic apps lär du dig [hur du skapar din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-delay"></a>

## <a name="add-the-delay-action"></a>Lägg till Delay-åtgärden

1. I Logic App Designer under steget där du vill lägga till delay-åtgärden Välj **nytt steg**.

   Flytta pekaren över pilen som ansluter stegen för att lägga till fördröjningsåtgärden mellan stegen. Välj plustecknet (+) som visas och välj sedan **Lägg till en åtgärd**.

1. I sökrutan anger du ”fördröjning” som filter. Välj den här åtgärden från åtgärdslistan över: **fördröjning**

   ![Lägg till ”fördröjningsåtgärden”](./media/connectors-native-delay/add-delay-action.png)

1. Ange hur lång tid att vänta innan nästa åtgärd körs.

   ![Angiven tid för fördröjningen](./media/connectors-native-delay/delay-time-intervals.png)

   | Egenskap | JSON-namn | Obligatoriskt | Typ | Beskrivning |
   |----------|-----------|----------|------|-------------|
   | Count | count | Ja | Integer | Antalet tidsenheter att fördröja |
   | Enhet | Enhet | Ja | String | Tidsenheten, till exempel: `Second`, `Minute`, `Hour`, `Day`, `Week`, eller `Month` |
   ||||||

1. Lägg till alla andra åtgärder som du vill köra i arbetsflödet.

1. När du är klar sparar du din logikapp.

<a name="add-delay-until"></a>

## <a name="add-the-delay-until-action"></a>Lägg till fördröjningen-förrän åtgärden

1. I Logic App Designer under steget där du vill lägga till delay-åtgärden Välj **nytt steg**.

   Flytta pekaren över pilen som ansluter stegen för att lägga till fördröjningsåtgärden mellan stegen. Välj plustecknet (+) som visas och välj sedan **Lägg till en åtgärd**.

1. I sökrutan anger du ”fördröjning” som filter. Välj den här åtgärden från åtgärdslistan över: **Fördröj tills**

   ![Lägg till ”Fördröj tills” åtgärd](./media/connectors-native-delay/add-delay-until-action.png)

1. Ange slutdatum och tidpunkt för när du vill återuppta arbetsflödet.

   ![Ange tidsstämpel när du vill sluta fördröjningen](./media/connectors-native-delay/delay-until-timestamp.png)

   | Egenskap | JSON-namn | Obligatoriskt | Typ | Beskrivning |
   |----------|-----------|----------|------|-------------|
   | Tidsstämpel | timestamp | Ja | String | Slutdatum och tid för att återuppta arbetsflödet med hjälp av det här formatet: <p>ÅÅÅÅ-MM-: ssZ <p>Till exempel om du vill 18 September 2017 kl 2:00, ange ”2017-09-18T14:00:00Z”. <p>**Obs:** Den här tidsformat måste följa den [ISO 8601 datum tidsangivelse](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) i [tidsformat för UTC-datum](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), men utan en [UTC-förskjutning](https://en.wikipedia.org/wiki/UTC_offset). Du måste lägga till Bokstaven ”Z” i slutet utan blanksteg utan en tidszon. Den här ”Z” avser motsvarande [nautiska tid](https://en.wikipedia.org/wiki/Nautical_time). |
   ||||||

1. Lägg till alla andra åtgärder som du vill köra i arbetsflödet.

1. När du är klar sparar du din logikapp.

## <a name="next-steps"></a>Nästa steg

* [Skapa, schemalägga och kör återkommande uppgifter och arbetsflöden med upprepningsutlösaren](../connectors/connectors-native-recurrence.md)
* [Anslutningsappar för Logic Apps](../connectors/apis-list.md)