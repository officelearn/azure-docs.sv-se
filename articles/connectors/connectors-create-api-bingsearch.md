---
title: Ansluta till Bing Search - Azure Logic Apps
description: 'Hitta nyheter med Bing Search REST API: er och Azure Logic Apps'
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 05/21/2018
tags: connectors
ms.openlocfilehash: 7146e59eabf9e30fa263f957f1c546414ad0fe26
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/21/2019
ms.locfileid: "58313557"
---
# <a name="find-news-with-bing-search-and-azure-logic-apps"></a>Hitta nyheter med Bing Search och Azure Logic Apps

Den här artikeln visar hur du hittar nyheter, videor och andra objekt via Bing Search från inuti en logikapp med Bing Search connector. På så sätt kan du skapa logikappar som automatiserar uppgifter och arbetsflöden för bearbetning sökresultat och göra de objekt som är tillgänglig för andra åtgärder. 

Du kan till exempel hitta nyhetsobjekt baserat på sökvillkor och har Twitter bokför dessa artiklar som tweets på ditt Twitter-flöde.

Om du heller inte har någon Azure-prenumeration kan du <a href="https://azure.microsoft.com/free/" target="_blank">registrera ett kostnadsfritt Azure-konto</a>. Om du är nybörjare till logic apps, granska [vad är Azure Logic Apps](../logic-apps/logic-apps-overview.md) och [snabbstarten: Skapa din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Specifika teknisk information finns i den <a href="https://docs.microsoft.com/connectors/bingsearch/" target="blank">referens för Bing Search-anslutningsapp</a>.

## <a name="prerequisites"></a>Förutsättningar

* En [Cognitive Services-konto](../cognitive-services/cognitive-services-apis-create-account.md)

* En [Bing Search API-nyckel](https://azure.microsoft.com/try/cognitive-services/?api=bing-news-search-api), vilket ger åtkomst från din logikapp till API: er för Bing-sökresultat

* Logikappen där du vill få åtkomst till din Event Hub. Du behöver för att starta din logikapp med en utlösare för Bing Search, en [tom logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-trigger"></a>

## <a name="add-a-bing-search-trigger"></a>Lägg till en utlösare för Bing Search

I Azure Logic Apps varje logikapp måste börja med en [utlösaren](../logic-apps/logic-apps-overview.md#logic-app-concepts), som utlöses när en specifik händelse sker eller när ett specifikt villkor uppfylls. Varje gång utlösaren Logic Apps-motorn skapar en logikappinstans och börjar köras appens arbetsflöde.

1. I Azure portal eller Visual Studio, skapar du en tom logikapp som öppnas Logic App Designer. Det här exemplet används Azure-portalen.

2. I sökrutan anger du ”Bing search” som filter. Välj utlösaren som du vill använda från listan över utlösare.

   Det här exemplet använder den här utlösaren: **Bing Search - på nya nyhetsartikel**

   ![Hitta Bing Search-utlösare](./media/connectors-create-api-bing-search/add-trigger.png)

3. Om du uppmanas anslutningsinformation [skapa anslutningen Bing Search nu](#create-connection).
Eller om anslutningen redan finns, ange nödvändig information för utlösaren.

   Ange villkor för att returnera matchande nyhetsartiklar från Bing Search i det här exemplet.

   | Egenskap  | Krävs | Value | Beskrivning |
   |----------|----------|-------|-------------|
   | Search Query | Ja | <*search-words*> | Ange sökord som du vill använda. |
   | Market | Ja | <*Nationella inställningar*> | Sök nationella inställningar. Standardvärdet är ”en-US”, men du kan välja ett annat värde. |
   | Safe Search | Ja | <*Sök på servernivå*> | Filternivå för att undanta vuxet innehåll. Standardvärdet är ”måttlig”, men du väljer en annan nivå. |
   | Count | Nej | <*antal resultat*> | Returnera det angivna antalet resultat. Standardvärdet är 20, men du kan ange ett annat värde. Det faktiska antalet returnerade resultaten kan vara mindre än det angivna värdet. |
   | Offset | Nej | <*skip-value*> | Antalet resultat som hoppas över innan det returneras resultat |
   |||||

   Exempel:

   ![Konfigurera utlösare](./media/connectors-create-api-bing-search/bing-search-trigger.png)

4. Välj intervall och frekvens för hur ofta du vill att utlösaren ska kontrollera resultatet.

5. När du är klar på verktygsfältet för appdesignern väljer **spara**.

6. Nu ska du fortsätta att lägga till en eller flera åtgärder i din logikapp för uppgifter som du vill utföra med utlösare resultaten.

<a name="add-action"></a>

## <a name="add-a-bing-search-action"></a>Lägg till en åtgärd för sökning i Bing

I Azure Logic Apps, en [åtgärd](../logic-apps/logic-apps-overview.md#logic-app-concepts) är ett steg i arbetsflödet som följer en utlösare eller en annan åtgärd. I det här exemplet börjar logic app med en utlösare för Bing Search som returnerar nyhetsartiklar som matchar de angivna villkoren.

1. Öppna logikappen i Logic App Designer i Azure portal eller Visual Studio. Det här exemplet används Azure-portalen.

2. Under utlösaren eller åtgärden, väljer **nytt steg** > **Lägg till en åtgärd**.

   Det här exemplet använder den här utlösaren:

   **Bing Search - på nya nyhetsartikel**

   ![Lägg till åtgärd](./media/connectors-create-api-bing-search/add-action.png)

   Flytta musen över den anslutande pilen för att lägga till en åtgärd mellan befintliga steg. 
   Välj plustecknet (**+**) som visas och välj sedan **Lägg till en åtgärd**.

3. I sökrutan anger du ”Bing search” som filter.
Välj vilken åtgärd du önska från åtgärdslistan över.

   Det här exemplet använder den här åtgärden:

   **Bing Search - listan nyheter av frågan**

   ![Hitta Bing Search-åtgärd](./media/connectors-create-api-bing-search/bing-search-select-action.png)

4. Om du uppmanas anslutningsinformation [skapa anslutningen Bing Search nu](#create-connection). Eller om anslutningen redan finns, ange informationen som krävs för åtgärden.

   Ange kriterierna för att returnera en delmängd av utlösarens resultat i det här exemplet.

   | Egenskap  | Krävs | Value | Beskrivning |
   |----------|----------|-------|-------------|
   | Search Query | Ja | <*search-expression*> | Ange ett uttryck för att fråga utlösaren resultaten. Du kan välja från fälten i listan med dynamiskt innehåll eller skapa ett uttryck med Uttrycksverktyget. |
   | Market | Ja | <*Nationella inställningar*> | Sök nationella inställningar. Standardvärdet är ”en-US”, men du kan välja ett annat värde. |
   | Safe Search | Ja | <*Sök på servernivå*> | Filternivå för att undanta vuxet innehåll. Standardvärdet är ”måttlig”, men du väljer en annan nivå. |
   | Count | Nej | <*antal resultat*> | Returnera det angivna antalet resultat. Standardvärdet är 20, men du kan ange ett annat värde. Det faktiska antalet returnerade resultaten kan vara mindre än det angivna värdet. |
   | Offset | Nej | <*skip-value*> | Antalet resultat som hoppas över innan det returneras resultat |
   |||||

   Anta exempelvis att du vill att dessa resultat vars kategorinamn innehåller ordet ”teknisk”.

   1. Klicka på den **sökfråga** rutan så visas listan med dynamiskt innehåll. 
   Välj den listan **uttryck** så Uttrycksverktyget visas. 

      ![Bing Search-utlösare](./media/connectors-create-api-bing-search/bing-search-action.png)

      Nu kan du börja skapa uttrycket.

   2. Functions-listan väljer du den **contains()** som visas sedan i resultatrutan. Klicka på **dynamiskt innehåll** så att listan visas igen, men se till att markören ligger inuti parenteserna.

      ![Välj en funktion](./media/connectors-create-api-bing-search/expression-select-function.png)

   3. I fältlistan, Välj **kategori**, som konverterar till en parameter. 
   Lägg till ett kommatecken efter den första parametern och efter kommatecknet, Lägg till följande ord: `'tech'` 

      ![Välj ett fält](./media/connectors-create-api-bing-search/expression-select-field.png)

   4. När du är klar väljer du **OK**.

      Uttrycket som nu visas i den **sökfråga** rutan i följande format:

      ![Klar uttryck](./media/connectors-create-api-bing-search/resolved-expression.png)

      I kodvyn visas det här uttrycket i det här formatet:

      `"@{contains(triggerBody()?['category'],'tech')}"`

5. När du är klar på verktygsfältet för appdesignern väljer **spara**.

<a name="create-connection"></a>

## <a name="connect-to-bing-search"></a>Ansluta till Bing Search

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. När du uppmanas att ange anslutningsinformationen Tillhandahåll följande information:

   | Egenskap  | Krävs | Value | Beskrivning |
   |----------|----------|-------|-------------|
   | Anslutningsnamn | Ja | <*connection-name*> | Namn för att skapa för anslutningen |
   | API-version | Ja | <*API-version*> | Bing Search API-versionen är som standard den aktuella versionen. Du kan välja en tidigare version vid behov. |
   | API-nyckel | Ja | <*API-key*> | Bing Search API-nyckeln som du fick tidigare. Om du inte har en nyckel kan få din [nu API-nyckel](https://azure.microsoft.com/try/cognitive-services/?api=bing-news-search-api). |  
   |||||  

   Exempel:

   ![Skapa anslutning](./media/connectors-create-api-bing-search/bing-search-create-connection.png)

2. När du är klar väljer du **Skapa**.

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information, till exempel utlösare och åtgärder gränser, som beskrivs av anslutningsappens OpenAPI (tidigare Swagger) fil, finns i den [anslutningsappens-referenssida](/connectors/bingsearch/).

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* Läs mer om andra [Logic Apps-anslutningsprogram](../connectors/apis-list.md)
