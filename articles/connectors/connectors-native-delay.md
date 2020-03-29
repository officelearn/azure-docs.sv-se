---
title: Fördröja nästa åtgärd i arbetsflöden
description: Vänta med att köra nästa åtgärd i logikapparbetsflöden med hjälp av åtgärderna Fördröjning eller Fördröjning tills i Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: deli, klam, logicappspm
ms.topic: conceptual
ms.date: 05/25/2019
tags: connectors
ms.openlocfilehash: 5348ade1ba6eec6cbd360849411b4520cb3c2b19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74787344"
---
# <a name="delay-running-the-next-action-in-azure-logic-apps"></a>Fördröja körningen av nästa åtgärd i Azure Logic Apps

Om du vill att logikappen ska vänta en viss tid innan du kör nästa åtgärd kan du lägga till den inbyggda **åtgärden Fördröjning - Schemalägg** innan en åtgärd i logikappens arbetsflöde. Du kan också lägga till den inbyggda **fördröjningen tills - Schemalägg** åtgärden för att vänta tills ett visst datum och en viss tid innan du kör nästa åtgärd. Mer information om de inbyggda Schemaåtgärderna och utlösare finns i [Schemalägga och köra återkommande automatiserade, uppgifter och arbetsflöden med Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

* **Fördröjning**: Vänta på det angivna antalet tidsenheter, till exempel sekunder, minuter, timmar, dagar, veckor eller månader, innan nästa åtgärd körs.

* **Fördröjning till**: Vänta tills det angivna datumet och tiden innan nästa åtgärd körs.

Här är några exempel sätt att använda dessa åtgärder:

* Vänta tills en veckodag för att skicka en statusuppdatering via e-post.

* Fördröja arbetsflödet tills ett HTTP-samtal avslutas innan data återupptas och hämtas.

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte har en prenumeration kan du [registrera dig för ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Grundläggande kunskaper om [logikappar](../logic-apps/logic-apps-overview.md). Innan du kan använda en åtgärd måste logikappen börja med en utlösare. Du kan använda vilken utlösare du vill ha och lägga till andra åtgärder innan du lägger till en fördröjningsåtgärd. I det här avsnittet används en Office 365 Outlook-utlösare. Om du inte har tidigare i logikappar kan du läsa om hur du [skapar din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-delay"></a>

## <a name="add-the-delay-action"></a>Lägg till åtgärden Fördröjning

1. Välj **Nytt steg**under det steg där du vill lägga till fördröjningsåtgärden i Logic App Designer.

   Om du vill lägga till fördröjningsåtgärden mellan stegen flyttar du pekaren över pilen som kopplar stegen. Välj plustecknet (+) som visas och välj sedan **Lägg till en åtgärd**.

1. I sökrutan anger du "fördröjning" som filter. Välj den här åtgärden i åtgärdslistan: **Fördröj**

   ![Lägg till åtgärden "Fördröjning"](./media/connectors-native-delay/add-delay-action.png)

1. Ange hur lång tid du ska vänta innan nästa åtgärd körs.

   ![Ange tid för förseningen](./media/connectors-native-delay/delay-time-intervals.png)

   | Egenskap | JSON-namn | Krävs | Typ | Beskrivning |
   |----------|-----------|----------|------|-------------|
   | Antal | count | Ja | Integer | Antalet tidsenheter som ska fördröjas |
   | Enhet | unit | Ja | String | `Second`Tidsenheten, till exempel: `Minute` `Hour`, `Day` `Week`, , , eller`Month` |
   ||||||

1. Lägg till andra åtgärder som du vill köra i arbetsflödet.

1. När du är klar sparar du logikappen.

<a name="add-delay-until"></a>

## <a name="add-the-delay-until-action"></a>Lägg till åtgärden Fördröjning tills

1. Välj **Nytt steg**under det steg där du vill lägga till fördröjningsåtgärden i Logic App Designer.

   Om du vill lägga till fördröjningsåtgärden mellan stegen flyttar du pekaren över pilen som kopplar stegen. Välj plustecknet (+) som visas och välj sedan **Lägg till en åtgärd**.

1. I sökrutan anger du "fördröjning" som filter. Välj den här åtgärden i åtgärdslistan: **Fördröja tills**

   ![Lägg till åtgärden "Fördröjning till"](./media/connectors-native-delay/add-delay-until-action.png)

1. Ange slutdatum och sluttid för när du vill återuppta arbetsflödet.

   ![Ange tidsstämpel för när fördröjningen ska avslutas](./media/connectors-native-delay/delay-until-timestamp.png)

   | Egenskap | JSON-namn | Krävs | Typ | Beskrivning |
   |----------|-----------|----------|------|-------------|
   | Tidsstämpel | timestamp | Ja | String | Slutdatum och sluttid för att återuppta arbetsflödet med det här formatet: <p>YYYY-MM-DDThh:mm:ssZ <p>Om du till exempel vill ha 18 september 2017 klockan 14:00 anger du "2017-09-18T14:00:00Z". <p>**Anm.:** Det här tidsformatet måste följa [datumtidsspecifikationen ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) i [UTC-datumtidsformatet](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), men utan en [UTC-förskjutning](https://en.wikipedia.org/wiki/UTC_offset). Utan en tidszon måste du lägga till bokstaven "Z" i slutet utan några blanksteg. Detta "Z" avser motsvarande [nautisk tid](https://en.wikipedia.org/wiki/Nautical_time). |
   ||||||

1. Lägg till andra åtgärder som du vill köra i arbetsflödet.

1. När du är klar sparar du logikappen.

## <a name="next-steps"></a>Nästa steg

* [Skapa, schemalägga och köra återkommande uppgifter och arbetsflöden med utlösaren Återkommande](../connectors/connectors-native-recurrence.md)
* [Anslutningsappar för Logic Apps](../connectors/apis-list.md)