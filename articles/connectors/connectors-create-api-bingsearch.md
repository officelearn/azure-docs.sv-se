---
title: Ansluta till Bing Search - Azure Logikappar | Microsoft Docs
description: 'Söka efter nyheter med Bing Search REST API: er och Azure Logic Apps'
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 05/21/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 8ac67f9df0e5baccc668c2aeb70f65d96e574df5
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37021293"
---
# <a name="find-news-with-bing-search-and-azure-logic-apps"></a>Söka efter nyheter med Bing Search och Azure Logic Apps 

Den här artikeln visar hur du kan hitta nyheter, videor och andra objekt via Bing Search från inuti en logikapp med Bing Search-anslutningen. På så sätt kan du skapa logikappar som automatiserar uppgifter och arbetsflöden för bearbetning sökresultat och göra de objekt som är tillgängliga för andra åtgärder. 

Du kan exempelvis söka efter artiklar baserat på sökvillkor och har Twitter bokför dessa artiklar som tweets i din Twitter-flöde.

Om du heller inte har någon Azure-prenumeration kan du <a href="https://azure.microsoft.com/free/" target="_blank">registrera ett kostnadsfritt Azure-konto</a>. Om du har använt logikappar granska [vad är Azure Logikappar](../logic-apps/logic-apps-overview.md) och [Snabbstart: skapa din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Connector-specifik teknisk information finns i <a href="https://docs.microsoft.com/connectors/bingsearch/" target="blank">Bing Search connector referens</a>.

## <a name="prerequisites"></a>Förutsättningar

* En [kognitiva Services-konto](../cognitive-services/cognitive-services-apis-create-account.md)

* En [Bing Search API-nyckeln](https://azure.microsoft.com/try/cognitive-services/?api=bing-news-search-api), som ger åtkomst från din logikapp till API: er för Bing Search 

* Logikappen där du vill ha åtkomst till din Event Hub. Om du vill starta din logikapp med en utlösare för Bing Search behöver du en [tom logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

<a name="add-trigger"></a>

## <a name="add-a-bing-search-trigger"></a>Lägg till en utlösare för Bing Search

I Azure Logic Apps varje logikapp måste börja med en [utlösaren](../logic-apps/logic-apps-overview.md#logic-app-concepts), som utlöses när en viss händelse inträffar eller när ett specifikt villkor uppfylls. Varje gång utlösaren-utlöses Logic Apps motorn skapar en logik app-instansen och börjar köras appens arbetsflöde.

1. Skapa en tom logikapp, vilket öppnar logik App Designer i Azure-portalen eller Visual Studio. Det här exemplet använder Azure-portalen.

2. I sökrutan anger du ”Bing search” som filter. Välj utlösare som du vill använda från listan över utlösare. 

   Det här exemplet används den här utlösaren: **Bing Search - på ny nyhetsartikel**

   ![Hitta utlösare för Bing Search](./media/connectors-create-api-bing-search/add-trigger.png)

3. Om du uppmanas anslutningsinformation [skapar anslutningen Bing Sök nu](#create-connection). Eller, om det finns redan en anslutning, ange nödvändig information för utlösaren.

   Ange villkor för att returnera matchande nyhetsartiklar från Bing sökning i det här exemplet.

   | Egenskap  | Krävs | Värde | Beskrivning | 
   |----------|----------|-------|-------------| 
   | Sökfråga | Ja | <*Sök-ord*> | Ange sökord som du vill använda. |
   | Marknad | Ja | <*Nationella inställningar*> | Sök-språk. Standardvärdet är ”sv-se”, men du kan välja ett annat värde. | 
   | Säker sökning | Ja | <*Sök-nivå*> | Filternivån för att undanta vuxet innehåll. Standardvärdet är ”mellan”, men du väljer en annan nivå. | 
   | Antal | Nej | <*antalet resultat*> | Returnera det angivna antalet resultat. Standardvärdet är 20, men du kan ange ett annat värde. Det faktiska antalet returnerade resultat kan vara mindre än det angivna värdet. | 
   | Offset | Nej | <*Skip-värde*> | Antalet resultat som ska hoppas över innan den returnerar resultat | 
   ||||| 

   Exempel:

   ![Ställ in utlösare](./media/connectors-create-api-bing-search/bing-search-trigger.png)

4. Välj intervall och frekvens för hur ofta du vill att utlösaren ska kontrollera resultatet.

5. När du är klar i verktygsfältet designer väljer **spara**. 

6. Fortsätt att lägga till en eller flera åtgärder i din logikapp för de aktiviteter som du vill utföra med Utlösare resultat.

<a name="add-action"></a>

## <a name="add-a-bing-search-action"></a>Lägg till en åtgärd för Bing Search

I Azure Logikappar en [åtgärd](../logic-apps/logic-apps-overview.md#logic-app-concepts) är ett steg i arbetsflödet som följer en utlösare eller en annan åtgärd. I det här exemplet logikappen som börjar med en utlösare för Bing Search som returnerar nyhetsartiklar som matchar de angivna villkoren. 

1. Öppna logikappen i logik App Designer i Azure-portalen eller Visual Studio. Det här exemplet använder Azure-portalen.

2. Välj under utlösare eller åtgärd, **nytt steg** > **lägga till en åtgärd**.

   Det här exemplet används den här utlösaren: **Bing Search - på ny nyhetsartikel**

   ![Lägg till åtgärd](./media/connectors-create-api-bing-search/add-action.png)

   Om du vill lägga till en åtgärd mellan befintliga steg, håller du muspekaren över anslutande pilen. 
   Välj på plustecknet (**+**) som visas och sedan välja **lägga till en åtgärd**.

3. I sökrutan anger du ”Bing search” som filter.
Välj den åtgärd som du vill använda från listan över åtgärder.

   Det här exemplet använder den här åtgärden: **Bing Search - listan nyheter av frågan**

   ![Hitta Bing Search-åtgärd](./media/connectors-create-api-bing-search/bing-search-select-action.png)

4. Om du uppmanas anslutningsinformation [skapar anslutningen Bing Sök nu](#create-connection). Eller, om det finns redan en anslutning, ange nödvändig information för åtgärden. 

   Ange kriterierna för att returnera en delmängd av utlösarens resultat i det här exemplet.

   | Egenskap  | Krävs | Värde | Beskrivning | 
   |----------|----------|-------|-------------| 
   | Sökfråga | Ja | <*Sök-uttryck*> | Ange ett uttryck för att fråga efter utlösaren resultaten. Du kan välja från fälten i den dynamiska innehåll listan eller skapa ett uttryck med uttrycket builder. |
   | Marknad | Ja | <*Nationella inställningar*> | Sök-språk. Standardvärdet är ”sv-se”, men du kan välja ett annat värde. | 
   | Säker sökning | Ja | <*Sök-nivå*> | Filternivån för att undanta vuxet innehåll. Standardvärdet är ”mellan”, men du väljer en annan nivå. | 
   | Antal | Nej | <*antalet resultat*> | Returnera det angivna antalet resultat. Standardvärdet är 20, men du kan ange ett annat värde. Det faktiska antalet returnerade resultat kan vara mindre än det angivna värdet. | 
   | Offset | Nej | <*Skip-värde*> | Antalet resultat som ska hoppas över innan den returnerar resultat | 
   ||||| 

   Anta att du vill att dessa resultat vars namn innehåller ordet ”teknisk”. 
   
   1. Klicka i den **sökfråga** rutan så visas listan dynamiskt innehåll. 
   I listan, Välj **uttryck** så att uttrycksbyggare visas. 

      ![Utlösare för Bing Search](./media/connectors-create-api-bing-search/bing-search-action.png)

      Nu kan du börja skapa ditt uttryck.

   2. Från listan över funktioner, Välj den **contains()** funktion, vilket visas i resultatrutan. Klicka på **dynamiskt innehåll** så att fältlistan visas igen, men se till att markören förblir inom parentes.

      ![Välj en funktion](./media/connectors-create-api-bing-search/expression-select-function.png)

   3. I listrutan väljer du **kategori**, vilket omvandlas till en parameter. 
   Lägga till ett komma efter den första parametern och efter ett kommatecken, lägga till detta ord: `'tech'` 

      ![Välj ett fält](./media/connectors-create-api-bing-search/expression-select-field.png)
   
   4. När du är klar väljer **OK**.

      Uttrycket nu visas i den **sökfråga** rutan i det här formatet:

      ![Klar uttryck](./media/connectors-create-api-bing-search/resolved-expression.png)

      I kodvyn visas det här uttrycket i det här formatet:

      `"@{contains(triggerBody()?['category'],'tech')}"`

5. När du är klar i verktygsfältet designer väljer **spara**.

<a name="create-connection"></a>

## <a name="connect-to-bing-search"></a>Ansluta till Bing Search

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)] 

1. När du uppmanas att ange anslutningsinformation Tillhandahåll följande information:

   | Egenskap  | Krävs | Värde | Beskrivning | 
   |----------|----------|-------|-------------| 
   | Anslutningsnamn | Ja | <*Anslutningens namn*> | Namn för att skapa för din anslutning |
   | API-version | Ja | <*API-version*> | Bing Search API-versionen är som standard till den aktuella versionen. Du kan välja en tidigare version vid behov. | 
   | API-nyckel | Ja | <*API-nyckel*> | Bing Search API-nyckeln som du tidigare fått. Om du inte har en nyckel kan hämta din [API-nyckeln nu](https://azure.microsoft.com/try/cognitive-services/?api=bing-news-search-api). |  
   |||||  

   Exempel:

   ![Skapa anslutning](./media/connectors-create-api-bing-search/bing-search-create-connection.png)

2. När du är klar väljer du **Skapa**.

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information, till exempel utlösare och åtgärder som gränser, enligt beskrivningen av kopplingens Swagger-filen finns den [kopplingens referenssida](/connectors/bingsearch/). 

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps kopplingar](../connectors/apis-list.md)
