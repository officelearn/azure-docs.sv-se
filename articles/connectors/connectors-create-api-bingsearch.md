---
title: Anslut till Bing-sökning
description: Automatisera uppgifter och arbetsflöden som hittar resultat i Bing Search med hjälp av Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 05/21/2018
tags: connectors
ms.openlocfilehash: e547ae59f7b3260f46756825bca2bef1c10bcc97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75665895"
---
# <a name="find-results-in-bing-search-by-using-azure-logic-apps"></a>Hitta resultat i Bing Search med hjälp av Azure Logic Apps

Den här artikeln visar hur du kan hitta nyheter, videor och andra objekt via Bing Search inifrån en logikapp med Bing Search-kopplingen. På så sätt kan du skapa logikappar som automatiserar uppgifter och arbetsflöden för bearbetning av sökresultat och gör dessa objekt tillgängliga för andra åtgärder. 

Du kan till exempel hitta nyheter baserat på sökkriterier och låta Twitter publicera dessa objekt som tweets i ditt Twitter-flöde.

Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). Om du inte har tidigare i logikappar läser du [Vad är Azure Logic Apps](../logic-apps/logic-apps-overview.md) och [Snabbstart: Skapa din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Anslutningsspecifik teknisk information finns i [Bing Search-anslutningsreferensen](https://docs.microsoft.com/connectors/bingsearch/).

## <a name="prerequisites"></a>Krav

* Ett [Cognitive Services-konto](../cognitive-services/cognitive-services-apis-create-account.md)

* En [API-nyckel för Bing-sökning](https://azure.microsoft.com/try/cognitive-services/?api=bing-news-search-api)som ger åtkomst från logikappen till API:erna för Bing-sökning

* Logikappen där du vill komma åt din eventhubb. Om du vill starta logikappen med en Bing Search-utlösare behöver du en [tom logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-trigger"></a>

## <a name="add-a-bing-search-trigger"></a>Lägga till en Bing-sökutlösare

I Azure Logic Apps måste varje logikapp starta med en [utlösare](../logic-apps/logic-apps-overview.md#logic-app-concepts), som utlöses när en viss händelse inträffar eller när ett visst villkor uppfylls. Varje gång utlösaren utlöses skapar Logic Apps-motorn en logikappinstans och börjar köra appens arbetsflöde.

1. Skapa en tom logikapp i Azure-portalen eller Visual Studio som öppnar Logic App Designer. I det här exemplet används Azure-portalen.

2. I sökrutan anger du "Bing-sökning" som filter. Välj den utlösare du vill använda i listan utlösare.

   I det här exemplet används den här utlösaren: **Bing Search - På ny nyhetsartikel**

   ![Hitta Bing-sökutlösare](./media/connectors-create-api-bing-search/add-trigger.png)

3. Om du uppmanas att ange anslutningsinformation [skapar du din Bing Search-anslutning nu](#create-connection).
Eller, om anslutningen redan finns, ange nödvändig information för utlösaren.

   I det här exemplet kan du ange kriterier för att returnera matchande nyhetsartiklar från Bing Search.

   | Egenskap | Krävs | Värde | Beskrivning |
   |----------|----------|-------|-------------|
   | Sökfråga | Ja | <*sökord*> | Ange de sökord som du vill använda. |
   | Marknaden | Ja | <*Locale*> | Söklokalen. Standardvärdet är "en-US", men du kan välja ett annat värde. |
   | Säker sökning | Ja | <*söknivå*> | Filternivån för att utesluta barnförbjudet innehåll. Standardär "Måttlig", men du väljer en annan nivå. |
   | Antal | Inga | <*resultaträkning*> | Returnera det angivna antalet resultat. Standardvärdet är 20, men du kan ange ett annat värde. Det faktiska antalet returnerade resultat kan vara mindre än det angivna talet. |
   | Offset | Inga | <*hoppa över värde*> | Antalet resultat att hoppa över innan resultaten returneras |
   |||||

   Ett exempel:

   ![Ställ in utlösare](./media/connectors-create-api-bing-search/bing-search-trigger.png)

4. Välj intervall och frekvens för hur ofta du vill att utlösaren ska söka efter resultat.

5. När du är klar väljer du **Spara**i designerverktygsfältet .

6. Fortsätt nu att lägga till en eller flera åtgärder i logikappen för de uppgifter som du vill utföra med utlösarresultaten.

<a name="add-action"></a>

## <a name="add-a-bing-search-action"></a>Lägga till en Bing-sökåtgärd

I Azure Logic Apps är en [åtgärd](../logic-apps/logic-apps-overview.md#logic-app-concepts) ett steg i arbetsflödet som följer en utlösare eller en annan åtgärd. I det här exemplet börjar logikappen med en Bing Search-utlösare som returnerar nyhetsartiklar som matchar de angivna villkoren.

1. Öppna logikappen i Logic App Designer i Azure-portalen eller Visual Studio. I det här exemplet används Azure-portalen.

2. Under utlösaren eller åtgärden väljer du **Nytt steg** > **Lägg till en åtgärd**.

   I det här exemplet används den här utlösaren:

   **Bing Search - På nya nyheter artikel**

   ![Lägg till åtgärd](./media/connectors-create-api-bing-search/add-action.png)

   Om du vill lägga till en åtgärd mellan befintliga steg flyttar du musen över anslutningspilen. 
   Markera plustecknet**+**( ) som visas och välj sedan **Lägg till en åtgärd**.

3. I sökrutan anger du "Bing-sökning" som filter.
Välj den åtgärd du vill använda i åtgärdslistan.

   I det här exemplet används den här åtgärden:

   **Bing Search - Lista nyheter efter fråga**

   ![Sök i Bing-sökning](./media/connectors-create-api-bing-search/bing-search-select-action.png)

4. Om du uppmanas att ange anslutningsinformation [skapar du din Bing Search-anslutning nu](#create-connection). Eller, om anslutningen redan finns, ange nödvändig information för åtgärden.

   I det här exemplet anger du villkoren för att returnera en delmängd av utlösarens resultat.

   | Egenskap | Krävs | Värde | Beskrivning |
   |----------|----------|-------|-------------|
   | Sökfråga | Ja | <*sök-uttryck*> | Ange ett uttryck för att fråga utlösarresultaten. Du kan välja bland fälten i listan med dynamiskt innehåll eller skapa ett uttryck med uttrycksverktyget. |
   | Marknaden | Ja | <*Locale*> | Söklokalen. Standardvärdet är "en-US", men du kan välja ett annat värde. |
   | Säker sökning | Ja | <*söknivå*> | Filternivån för att utesluta barnförbjudet innehåll. Standardär "Måttlig", men du väljer en annan nivå. |
   | Antal | Inga | <*resultaträkning*> | Returnera det angivna antalet resultat. Standardvärdet är 20, men du kan ange ett annat värde. Det faktiska antalet returnerade resultat kan vara mindre än det angivna talet. |
   | Offset | Inga | <*hoppa över värde*> | Antalet resultat att hoppa över innan resultaten returneras |
   |||||

   Anta till exempel att du vill ha de resultat vars kategorinamn innehåller ordet "tech".

   1. Klicka i rutan **Sökfråga** så att listan med dynamiskt innehåll visas. 
   Välj **Uttryck** i listan så att uttrycksverktyget visas. 

      ![Bing-sökning utlösare](./media/connectors-create-api-bing-search/bing-search-action.png)

      Nu kan du börja skapa ditt uttryck.

   2. Välj funktionen **contains()** i funktionslistan, som sedan visas i uttrycksrutan. Klicka på **Dynamiskt innehåll** så att fältlistan visas igen, men se till att markören stannar inom parenteserna.

      ![Välj en funktion](./media/connectors-create-api-bing-search/expression-select-function.png)

   3. Välj **Kategori**i fältlistan som konverteras till en parameter. 
   Lägg till ett kommatecken efter den första parametern och lägg efter kommatecknet till det här ordet:`'tech'` 

      ![Markera ett fält](./media/connectors-create-api-bing-search/expression-select-field.png)

   4. När du är klar väljer du **Ok**.

      Uttrycket visas nu i rutan **Sökfråga** i det här formatet:

      ![Avslutat uttryck](./media/connectors-create-api-bing-search/resolved-expression.png)

      I kodvyn visas det här uttrycket i det här formatet:

      `"@{contains(triggerBody()?['category'],'tech')}"`

5. När du är klar väljer du **Spara**i designerverktygsfältet .

<a name="create-connection"></a>

## <a name="connect-to-bing-search"></a>Anslut till Bing-sökning

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Ange följande information när du uppmanas att ange anslutningsinformation:

   | Egenskap | Krävs | Värde | Beskrivning |
   |----------|----------|-------|-------------|
   | Anslutningsnamn | Ja | <*anslutningsnamn*> | Namnet som ska skapas för anslutningen |
   | API-version | Ja | <*API-version*> | Som standard är API-versionen för Bing Search inställd på den aktuella versionen. Du kan välja en tidigare version om det behövs. |
   | API-nyckel | Ja | <*API-nyckel*> | Api-nyckeln för Bing-sökning som du fick tidigare. Om du inte har en nyckel hämtar du [API-nyckeln nu](https://azure.microsoft.com/try/cognitive-services/?api=bing-news-search-api). |  
   |||||  

   Ett exempel:

   ![Skapa anslutning](./media/connectors-create-api-bing-search/bing-search-create-connection.png)

2. När du är klar väljer du **Skapa**.

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information, till exempel utlösare, åtgärder och begränsningar, enligt beskrivningen i kopplingens Swagger-fil, finns på [kopplingens referenssida](/connectors/bingsearch/).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps-kopplingar](../connectors/apis-list.md)
