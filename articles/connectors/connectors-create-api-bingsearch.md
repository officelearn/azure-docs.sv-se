---
title: Anslut till Bing-sökning-Azure Logic Apps
description: 'Hitta nyheter med Bing-sökning REST-API: er och Azure Logic Apps'
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
manager: carmonm
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 05/21/2018
tags: connectors
ms.openlocfilehash: 61004ed75a1935ada21b5c620a909fb5289aebb8
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050994"
---
# <a name="find-news-with-bing-search-and-azure-logic-apps"></a>Hitta nyheter med Bing-sökning och Azure Logic Apps

Den här artikeln visar hur du kan hitta nyheter, videor och andra objekt genom Bing-sökning inifrån en Logic-app med Bing-sökning-anslutningen. På så sätt kan du skapa Logi Kap par som automatiserar aktiviteter och arbets flöden för bearbetning av Sök Resultat och gör dessa objekt tillgängliga för andra åtgärder. 

Du kan till exempel hitta nyhets objekt baserat på Sök villkor och låta Twitter publicera dessa objekt som tweets i ditt Twitter-flöde.

Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). Om du inte har arbetat med Logic Apps läser du [Vad är Azure Logic Apps](../logic-apps/logic-apps-overview.md) och [snabb start: Skapa din första Logic-](../logic-apps/quickstart-create-first-logic-app-workflow.md)app.
Information om anslutningsspecifika teknisk information finns i referens för [Bing-sökning Connector](https://docs.microsoft.com/connectors/bingsearch/).

## <a name="prerequisites"></a>Förutsättningar

* Ett [Cognitive Services konto](../cognitive-services/cognitive-services-apis-create-account.md)

* En [Bing-sökning API-nyckel](https://azure.microsoft.com/try/cognitive-services/?api=bing-news-search-api)som ger åtkomst från din Logic app till API:er för Bing-sökresultat

* Den Logic-app där du vill komma åt Händelsehubben. Om du vill starta din Logic app med en Bing-sökning utlösare behöver du en [Tom Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-trigger"></a>

## <a name="add-a-bing-search-trigger"></a>Lägg till en Bing-sökning-utlösare

I Azure Logic Apps måste varje Logi Kap par starta med en [](../logic-apps/logic-apps-overview.md#logic-app-concepts)utlösare som utlöses när en enskild händelse inträffar eller när ett särskilt villkor uppfylls. Varje gång utlösaren utlöses skapar Logic Apps-motorn en Logic App-instans och börjar köra appens arbets flöde.

1. I Azure Portal eller Visual Studio skapar du en tom Logic-app som öppnar Logic App Designer. I det här exemplet används Azure Portal.

2. Skriv "Bing search" som filter i rutan Sök. Välj den utlösare som du vill använda från listan utlösare.

   I det här exemplet används den här utlösaren: **Bing-sökning ny nyhets artikel**

   ![Hitta Bing-sökning utlösare](./media/connectors-create-api-bing-search/add-trigger.png)

3. Om du uppmanas att ange anslutnings information [skapar du din Bing-sökning-anslutning nu](#create-connection).
Eller, om anslutningen redan finns, anger du den information som krävs för utlösaren.

   I det här exemplet anger du kriterier för att returnera matchande nyhets artiklar från Bing-sökning.

   | Egenskap | Obligatorisk | Value | Beskrivning |
   |----------|----------|-------|-------------|
   | Search Query | Ja | <*Sök efter ord*> | Ange Sök nyckelorden som du vill använda. |
   | Market | Ja | <*locale*> | Sök språket. Standardvärdet är "en-US", men du kan välja ett annat värde. |
   | Safe Search | Ja | <*search-level*> | Filter nivån för att utesluta vuxna innehåll. Standardvärdet är "måttlig", men du väljer en annan nivå. |
   | Count | Nej | <*results-count*> | Returnera det angivna antalet resultat. Standardvärdet är 20, men du kan ange ett annat värde. Det faktiska antalet returnerade resultat kan vara mindre än det angivna antalet. |
   | Offset | Nej | <*skip-value*> | Antal resultat som ska hoppas över innan resultat returneras |
   |||||

   Exempel:

   ![Konfigurera utlösare](./media/connectors-create-api-bing-search/bing-search-trigger.png)

4. Välj intervall och frekvens för hur ofta du vill att utlösaren ska söka efter resultat.

5. När du är klar väljer du **Spara**i verktygsfältet designer.

6. Fortsätt nu att lägga till en eller flera åtgärder i din Logic app för de uppgifter som du vill utföra med utlösnings resultaten.

<a name="add-action"></a>

## <a name="add-a-bing-search-action"></a>Lägg till en Bing-sökning-åtgärd

I Azure Logic Apps är en [åtgärd](../logic-apps/logic-apps-overview.md#logic-app-concepts) ett steg i arbets flödet som följer en utlösare eller en annan åtgärd. I det här exemplet börjar Logic app med en Bing-sökning-utlösare som returnerar nyhets artiklar som matchar de angivna kriterierna.

1. Öppna din Logic app i Logic App Designer i Azure Portal eller Visual Studio. I det här exemplet används Azure Portal.

2. Under utlösare eller åtgärd väljer du **nytt steg** > **Lägg till en åtgärd**.

   I det här exemplet används den här utlösaren:

   **Bing-sökning ny nyhets artikel**

   ![Lägg till åtgärd](./media/connectors-create-api-bing-search/add-action.png)

   Om du vill lägga till en åtgärd mellan befintliga steg flyttar du musen över den anslutande pilen. 
   Välj plus tecknet ( **+** ) som visas och välj sedan **Lägg till en åtgärd**.

3. Skriv "Bing search" som filter i rutan Sök.
Välj den åtgärd du vill använda i listan åtgärder.

   I det här exemplet används den här åtgärden:

   **Bing-sökning Visa nyheter per fråga**

   ![Åtgärd för att hitta Bing-sökning](./media/connectors-create-api-bing-search/bing-search-select-action.png)

4. Om du uppmanas att ange anslutnings information [skapar du din Bing-sökning-anslutning nu](#create-connection). Eller, om anslutningen redan finns, anger du den information som krävs för åtgärden.

   I det här exemplet anger du villkoren för att returnera en delmängd av utlösarens resultat.

   | Egenskap | Obligatorisk | Value | Beskrivning |
   |----------|----------|-------|-------------|
   | Search Query | Ja | <*Sök-uttryck*> | Ange ett uttryck för att skicka frågor till utlösnings resultaten. Du kan välja bland fälten i listan med dynamiskt innehåll eller skapa ett uttryck med uttrycks verktyget. |
   | Market | Ja | <*locale*> | Sök språket. Standardvärdet är "en-US", men du kan välja ett annat värde. |
   | Safe Search | Ja | <*search-level*> | Filter nivån för att utesluta vuxna innehåll. Standardvärdet är "måttlig", men du väljer en annan nivå. |
   | Count | Nej | <*results-count*> | Returnera det angivna antalet resultat. Standardvärdet är 20, men du kan ange ett annat värde. Det faktiska antalet returnerade resultat kan vara mindre än det angivna antalet. |
   | Offset | Nej | <*skip-value*> | Antal resultat som ska hoppas över innan resultat returneras |
   |||||

   Anta till exempel att du vill ha dessa resultat vars kategori namn innehåller ordet "Tech".

   1. Klicka i rutan **Sök fråga** så att listan med dynamiskt innehåll visas. 
   Välj **uttryck** i listan så att uttrycks verktyget visas. 

      ![Bing-sökning utlösare](./media/connectors-create-api-bing-search/bing-search-action.png)

      Nu kan du börja skapa ditt uttryck.

   2. I listan funktioner väljer du funktionen **contains ()** som visas i rutan uttryck. Klicka på **dynamiskt innehåll** så att fält listan visas igen, men se till att markören stannar innanför parenteserna.

      ![Välj en funktion](./media/connectors-create-api-bing-search/expression-select-function.png)

   3. I listan fält väljer du **kategori**, som konverterar till en parameter. 
   Lägg till ett kommatecken efter den första parametern och efter kommatecken lägger du till det här ordet:`'tech'` 

      ![Välj ett fält](./media/connectors-create-api-bing-search/expression-select-field.png)

   4. När du är klar väljer du **Ok**.

      Uttrycket visas nu i rutan **Sök fråga** i följande format:

      ![Slut uttryck](./media/connectors-create-api-bing-search/resolved-expression.png)

      I kodvyn visas detta uttryck i följande format:

      `"@{contains(triggerBody()?['category'],'tech')}"`

5. När du är klar väljer du **Spara**i verktygsfältet designer.

<a name="create-connection"></a>

## <a name="connect-to-bing-search"></a>Anslut till Bing-sökning

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. När du uppmanas att ange anslutnings information anger du följande information:

   | Egenskap | Obligatorisk | Value | Beskrivning |
   |----------|----------|-------|-------------|
   | Anslutningens namn | Ja | <*anslutnings namn*> | Namnet som ska skapas för anslutningen |
   | API-version | Ja | <*API-version*> | Som standard är Bing-sökning API-versionen inställd på den aktuella versionen. Du kan välja en tidigare version om det behövs. |
   | API-nyckel | Ja | <*API-nyckel*> | Den Bing-sökning API-nyckel som du fick tidigare. Om du inte har någon nyckel kan du hämta din [API-nyckel nu](https://azure.microsoft.com/try/cognitive-services/?api=bing-news-search-api). |  
   |||||  

   Exempel:

   ![Skapa anslutning](./media/connectors-create-api-bing-search/bing-search-create-connection.png)

2. När du är klar väljer du **Skapa**.

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information, till exempel utlösare, åtgärder och gränser, som beskrivs i filens OpenAPI-fil (tidigare Swagger), finns på [kopplingens referens sida](/connectors/bingsearch/).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps anslutningar](../connectors/apis-list.md)
