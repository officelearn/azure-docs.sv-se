---
title: Skapa en sammansatt entitet om du vill extrahera komplexa data - Azure | Microsoft Docs
description: Lär dig hur du skapar en sammansatt entitet i THOMAS appen för att extrahera olika typer av entitetsdata.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: article
ms.date: 03/28/2018
ms.author: v-geberr
ms.openlocfilehash: cb581ee60dea2b0810332933455a03a8b68e16ea
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264393"
---
# <a name="use-composite-entity-to-extract-complex-data"></a>Använda sammansatta entiteten för att extrahera komplexa data
Den här enkla appen har två [intents](luis-concept-intent.md) och flera enheter. Syftet är att bok flygplan, till exempel '1 biljetten från Seattle Kairo fredag' och returnera specifika för reservation som en enda typ av data. 

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
* Lägg till fördefinierade entiteter datetimeV2 och nummer
* Skapa en sammansatt entitet
* Fråga THOMAS och ta emot data för sammansatt entitet

## <a name="before-you-begin"></a>Innan du börjar
* THOMAS appen från den  **[hierarkiska quickstart](luis-tutorial-composite-entity.md)**. 

> [!Tip]
> Om du inte redan har en prenumeration kan du registrera för en [kostnadsfritt konto](https://azure.microsoft.com/free/).

## <a name="composite-entity-is-a-logical-grouping"></a>Sammansatt entitet är en logisk gruppering 
Syftet med entiteten är att hitta och kategorisera delar av texten i utterance. En [sammansatta](luis-concept-entity-types.md) entitet består av andra typer av enheter som registrerats från kontexten. För den här resa app som tar svarta reservationer, finns det flera typer av information, till exempel datum, platser och antalet platser. 

Informationen finns som separata entiteterna innan sammansatt skapas. Skapa en sammansatt entitet när separata entiteterna kan grupperas logiskt och den här logisk gruppering är användbart i chatbot eller annat program förbrukar THOMAS. 

Enkelt exempel utterances från användare omfattar:

```
Book a flight to London for next Monday
2 tickets from Dallas to Dublin this weekend
Reserve a seat from New York to Paris on the first of April
```
 
Sammansatt entitet matchar antal platser, ursprung plats målplatsen och datum. 

## <a name="what-luis-does"></a>THOMAS har
När avsikt och entiteter av utterance identifieras [extraheras](luis-concept-data-extraction.md#list-entity-data), och returneras i JSON från den [endpoint](https://aka.ms/luis-endpoint-apis), THOMAS görs. Det anropande programmet eller chatbot tar som JSON-svar och uppfyller begäran – på valfritt sätt app eller chatbot är utformat för att göra. 

## <a name="add-prebuilt-entities-number-and-datetimev2"></a>Lägg till många fördefinierade entiteter och datetimeV2
1. Välj den `MyTravelApp` appen från listan över appar på den [THOMAS] [ LUIS] webbplats.

2. När appen öppnas, Välj den **entiteter** vänstra navigeringsfönstret länk.

    ![Välj knappen entiteter](./media/luis-tutorial-composite-entity/intents-page-select-entities.png)    

3. Välj **hantera färdiga entiteter**.

    ![Välj knappen entiteter](./media/luis-tutorial-composite-entity/manage-prebuilt-entities-button.png)

4. Markera i rutan popup- **nummer** och **datetimeV2**.

    ![Välj knappen entiteter](./media/luis-tutorial-composite-entity/prebuilt-entity-ddl.png)

5. För de nya enheterna som ska extraheras, välja **Train** i det övre navigeringsfältet.

    ![Välj train-knappen](./media/luis-tutorial-composite-entity/train.png)

## <a name="use-existing-intent-to-create-composite-entity"></a>Använd befintlig avsikt för att skapa sammansatt entitet
1. Välj **Intents** från det vänstra navigeringsfönstret. 

    ![Välj avsikter sida](./media/luis-tutorial-composite-entity/intents-from-entities-page.png)

2. Välj `BookFlight` från den **Intents** lista.  

    ![Välj BookFlight avsikt listan](./media/luis-tutorial-composite-entity/intent-page-with-prebuilt-entities-labeled.png)

    Antal och datetimeV2 färdiga entiteter är märkta på utterances.

3. För utterance `book 2 flights from seattle to cairo next monday`, Välj blå `number` entitetstyper, välj sedan **omsluter i sammansatta entiteten** från listan. En grön linje under ord, följer markören flyttas till höger, som anger en sammansatt entitet. Gå sedan till höger för att välja den senaste färdiga entiteten `datetimeV2`, ange `FlightReservation` i textrutan i popup-fönstret, Välj **skapa nya sammansatt**. 

    ![Skapa sammansatt entitet på avsikter sida](./media/luis-tutorial-composite-entity/create-new-composite.png)

4. En dialogruta visas där du kan kontrollera underordnade sammansatt entitet. Välj **klar**.

    ![Skapa sammansatt entitet på avsikter sida](./media/luis-tutorial-composite-entity/validate-composite-entity.png)

## <a name="wrap-the-entities-in-the-composite-entity"></a>Omsluta entiteterna i sammansatta entiteten
När du har skapat den sammansatta enheten etikett återstående utterances i sammansatta entiteten. För att omsluta en fras som en sammansatt enhet, måste du markera det vänstra och välj sedan **omsluter i sammansatta entiteten** från listan som visas, sedan markerar du det längst till höger och välj sedan namngiven sammansatta enhet `FlightReservation`. Det här är en snabb och smidig steg valen som delas upp i följande steg:

1. I utterance `schedule 4 seats from paris to london for april 1`, Välj 4 som antalet färdiga enhet.

    ![Markera vänstra ord](./media/luis-tutorial-composite-entity/wrap-composite-step-1.png)

2. Välj **omsluter i sammansatta entiteten** från listan som visas.

    ![Välj radbyte i listan](./media/luis-tutorial-composite-entity/wrap-composite-step-2.png)

3. Markera det längst till höger. Det visas en grön linje under fras, som anger en sammansatt entitet.

    ![Markera längst till höger ord](./media/luis-tutorial-composite-entity/wrap-composite-step-3.png)

4. Välj sammansatta namn `FlightReservation` från listan som visas.

    ![Välj namngiven sammansatta enhet](./media/luis-tutorial-composite-entity/wrap-composite-step-4.png)

    Senaste utterance omsluter `London` och `tomorrow` med samma instruktioner i entiteten sammansatta. 

## <a name="train-the-luis-app"></a>Träna THOMAS appen
THOMAS inte vet om ändringarna till avsikter och enheter (model) förrän den har installerats. 

1. I upp till höger på THOMAS webbplatsen, väljer du den **Train** knappen.

    ![Träna appen](./media/luis-tutorial-composite-entity/train-button.png)

2. Utbildning är klar när du ser grön statusfältet längst upp på webbplatsen bekräftar lyckades.

    ![Utbildning lyckades](./media/luis-tutorial-composite-entity/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publicera appen för att få slutpunkts-URL
Du måste publicera appen för att få en THOMAS förutsägelse i en chatbot eller ett annat program. 

1. I upp till höger på THOMAS webbplatsen, väljer du den **publicera** knappen. 

2. Välj produktionsplatsen och **publicera** knappen.

    ![Publicera appen](./media/luis-tutorial-composite-entity/publish-to-production.png)

3. Publiceringen är klar när du ser grön statusfältet längst upp på webbplatsen bekräftar lyckades.

## <a name="query-the-endpoint-with-a-different-utterance"></a>Frågan slutpunkten med en annan utterance
1. På den **publicera** väljer den **endpoint** länken längst ned på sidan. Den här åtgärden öppnar ett nytt webbläsarfönster med slutpunkts-URL i adressfältet. 

    ![Välj slutpunkts-URL](./media/luis-tutorial-composite-entity/publish-select-endpoint.png)

2. Gå till slutet av URL-Adressen i adressen och ange `reserve 3 seats from London to Cairo on Sunday`. Den sista parametern querystring är `q`, utterance frågan. Den här utterance är inte samma som någon av etiketterade utterances så att den är ett bra test ska returnera den `BookFlight` avsiktshantering med entiteten hierarkiska extraheras.

```
{
  "query": "reserve 3 seats from London to Cairo on Sunday",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.999999046
  },
  "intents": [
    {
      "intent": "BookFlight",
      "score": 0.999999046
    },
    {
      "intent": "None",
      "score": 0.227036044
    }
  ],
  "entities": [
    {
      "entity": "sunday",
      "type": "builtin.datetimeV2.date",
      "startIndex": 40,
      "endIndex": 45,
      "resolution": {
        "values": [
          {
            "timex": "XXXX-WXX-7",
            "type": "date",
            "value": "2018-03-25"
          },
          {
            "timex": "XXXX-WXX-7",
            "type": "date",
            "value": "2018-04-01"
          }
        ]
      }
    },
    {
      "entity": "3 seats from london to cairo on sunday",
      "type": "flightreservation",
      "startIndex": 8,
      "endIndex": 45,
      "score": 0.6892485
    },
    {
      "entity": "cairo",
      "type": "Location::Destination",
      "startIndex": 31,
      "endIndex": 35,
      "score": 0.557570755
    },
    {
      "entity": "london",
      "type": "Location::Origin",
      "startIndex": 21,
      "endIndex": 26,
      "score": 0.8933808
    },
    {
      "entity": "3",
      "type": "builtin.number",
      "startIndex": 8,
      "endIndex": 8,
      "resolution": {
        "value": "3"
      }
    }
  ],
  "compositeEntities": [
    {
      "parentType": "flightreservation",
      "value": "3 seats from london to cairo on sunday",
      "children": [
        {
          "type": "builtin.datetimeV2.date",
          "value": "sunday"
        },
        {
          "type": "Location::Destination",
          "value": "cairo"
        },
        {
          "type": "builtin.number",
          "value": "3"
        },
        {
          "type": "Location::Origin",
          "value": "london"
        }
      ]
    }
  ]
}
```

Den här utterance returnerar ett sammansatt entiteter matris inklusive den **flightreservation** objektet med de data som extraheras.  

## <a name="what-has-this-luis-app-accomplished"></a>Vad har appen THOMAS åstadkommas?
Den här appen med två avsikter och en sammansatt entitet identifierade en naturligt språk frågan avsikt och returnerade data som extraheras. 

Din chatbot har nu tillräckligt med information för att fastställa den primära åtgärden `BookFlight`, och reservation information som finns i utterance. 

## <a name="where-is-this-luis-data-used"></a>Var används den här THOMAS data? 
THOMAS görs med denna begäran. Det anropande programmet, till exempel en chatbot kan ta topScoringIntent resultatet och data från enheten att gå vidare. THOMAS inte programmässiga arbetet bot eller anropande programmet. THOMAS anger endast användarens avsikt är. 

## <a name="next-steps"></a>Nästa steg

[Mer information om entiteter](luis-concept-entity-types.md). 

<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[LUIS-regions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#publishing-regions
