---
title: Skapa en sammansatt entitet för att extrahera komplexa data – Azure | Microsoft Docs
description: Lär dig hur du skapar en sammansatt entitet i din LUIS-app för att extrahera olika typer av entitetsdata.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: article
ms.date: 03/28/2018
ms.author: v-geberr
ms.openlocfilehash: 375b52f9206f55e620d5e664844b8fa1d7249a07
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/06/2018
ms.locfileid: "37888753"
---
# <a name="use-composite-entity-to-extract-complex-data"></a>Använda sammansatta entitet för att extrahera komplexa data
Den här enkla appen har två [avsikter](luis-concept-intent.md) och flera entiteter. Syftet är att boka flyg, till exempel '1-biljett från Seattle till Kairo fredagen ”och returnera alla ärendets natur reservationen som en enda typ av data. 

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
* Lägg till fördefinierade entiteter datetimeV2 och nummer
* Skapa en sammansatt entitet
* Fråga efter LUIS och ta emot sammansatta entitetsdata

## <a name="before-you-begin"></a>Innan du börjar
* LUIS-appen från den  **[hierarkiska Snabbstart](luis-tutorial-composite-entity.md)**. 

> [!Tip]
> Om du inte redan har en prenumeration kan du registrera dig för en [kostnadsfritt konto](https://azure.microsoft.com/free/).

## <a name="composite-entity-is-a-logical-grouping"></a>Sammansatt entitet är en logisk gruppering 
Syftet med entiteten är att hitta och kategorisera delar av texten i yttrandet. En [sammansatta](luis-concept-entity-types.md) entitet består av andra typer av enheter som registrerats från kontexten. Det finns flera olika typer av information, till exempel datum, platser och antalet platser för den här reseapp som tar flygning reservationer. 

Informationen finns som separata entiteter innan en sammansatta skapas. Skapa en sammansatt entitet när separata entiteter kan grupperas logiskt och den här logisk gruppering kan vara bra att chattrobot eller annat program förbrukar LUIS. 

Enkla exempel på yttranden från användare innefattar:

```
Book a flight to London for next Monday
2 tickets from Dallas to Dublin this weekend
Reserve a seat from New York to Paris on the first of April
```
 
Sammansatta entiteten matchar platsantal, ursprungsplatsen, målplatsen och datum. 

## <a name="what-luis-does"></a>What LUIS gör
När yttrandets avsikt och entiteter identifierats, [extraherats](luis-concept-data-extraction.md#list-entity-data) och returnerats i JSON från [slutpunkten](https://aka.ms/luis-endpoint-apis) är LUIS klar. Det anropande programmet eller chattroboten använder JSON-svaret och uppfyller begäran på det sätt som appen eller chattroboten har instruerats att göra. 

## <a name="add-prebuilt-entities-number-and-datetimev2"></a>Lägg till fördefinierade entiteter tal och datetimeV2
1. Välj den `MyTravelApp` appen från listan över appar på den [LUIS](luis-reference-regions.md#luis-website) webbplats.

2. När appen öppnas, Välj den **entiteter** vänstra navigeringsfönstret länk.

    ![Välj knappen entiteter](./media/luis-tutorial-composite-entity/intents-page-select-entities.png)    

3. Välj **Manage prebuilt entities** (Hantera fördefinierade entiteter).

    ![Välj knappen entiteter](./media/luis-tutorial-composite-entity/manage-prebuilt-entities-button.png)

4. I popup-rutan, Välj **nummer** och **datetimeV2**.

    ![Välj knappen entiteter](./media/luis-tutorial-composite-entity/prebuilt-entity-ddl.png)

5. För de nya entiteterna som ska extraheras, välja **träna** i det övre navigeringsfältet.

    ![Välj knappen train (träna)](./media/luis-tutorial-composite-entity/train.png)

## <a name="use-existing-intent-to-create-composite-entity"></a>Använd befintliga avsikt för att skapa sammansatta entitet
1. Välj **avsikter** i det vänstra navigeringsfönstret. 

    ![Välj avsikter sida](./media/luis-tutorial-composite-entity/intents-from-entities-page.png)

2. Välj `BookFlight` från den **avsikter** lista.  

    ![Välj BookFlight avsikt listan](./media/luis-tutorial-composite-entity/intent-page-with-prebuilt-entities-labeled.png)

    Antal och datetimeV2 förskapade entiteter är märkta på talade.

3. För uttryck `book 2 flights from seattle to cairo next monday`, Välj det blå fältet `number` entitet, välj sedan **omsluta i sammansatt entitet** i listan. En grön linje under ord följer markören som flyttas till höger, som anger en sammansatt entitet. Flytta till höger för att välja den senaste fördefinierade entiteten `datetimeV2`, ange sedan `FlightReservation` i textrutan i popup-fönstret, välj sedan **skapa nya sammansatta**. 

    ![Skapa sammansatta entiteten på avsikter sida](./media/luis-tutorial-composite-entity/create-new-composite.png)

4. En dialogruta visas där du kan verifiera sammansatt entitet underordnade. Välj **Done** (Klar).

    ![Skapa sammansatta entiteten på avsikter sida](./media/luis-tutorial-composite-entity/validate-composite-entity.png)

## <a name="wrap-the-entities-in-the-composite-entity"></a>Omsluta entiteterna i sammansatt entiteten
När den sammansatta entitet har skapats kan etikettera återstående yttranden i sammansatt entiteten. För att omsluta en fras som en sammansatt enhet, måste du markera det vänster och välj sedan **omsluta i sammansatt entitet** från listan som visas, sedan markerar du det höger och välj sedan namngivna sammansatta entiteten `FlightReservation`. Det här är en snabb, problemfri steg valen som delas upp i följande steg:

1. I uttryck `schedule 4 seats from paris to london for april 1`, Välj 4 som antalet fördefinierade entitet.

    ![Välj vänster word](./media/luis-tutorial-composite-entity/wrap-composite-step-1.png)

2. Välj **omsluta i sammansatt entitet** från listan som visas.

    ![Välj radbyte i listan](./media/luis-tutorial-composite-entity/wrap-composite-step-2.png)

3. Välj höger ordet. En grön linje visas under frasen, som anger en sammansatt entitet.

    ![Välj höger word](./media/luis-tutorial-composite-entity/wrap-composite-step-3.png)

4. Välj sammansatta namn `FlightReservation` från listan som visas.

    ![Välj namngiven sammansatta entitet](./media/luis-tutorial-composite-entity/wrap-composite-step-4.png)

    Senaste uttryck omsluta `London` och `tomorrow` med samma instruktioner i entiteten sammansatta. 

## <a name="train-the-luis-app"></a>Träna LUIS-appen
LUIS känner inte till ändringarna av avsikterna och entiteterna (modellen) förrän den tränas. 

1. Längst uppe till höger på LUIS-webbplatsen väljer du knappen **Train** (Träna).

    ![Träna appen](./media/luis-tutorial-composite-entity/train-button.png)

2. Träningen är klar när du ser det gröna statusfältet som bekräftar att det är klart längst upp på webbplatsen.

    ![Träningen är klar](./media/luis-tutorial-composite-entity/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publicera appen för att få slutpunkts-URL
För att få en LUIS-förutsägelse i en chattrobot eller i ett annat program måste du publicera appen. 

1. Längst uppe till höger på LUIS-webbplatsen väljer du knappen **Publish** (Publicera). 

2. Välj platsen Production (Produktionsplats) och knappen **Publish** (Publicera).

    ![Publicera app](./media/luis-tutorial-composite-entity/publish-to-production.png)

3. Publiceringen är klar när du ser det gröna statusfältet som bekräftar att det är klart längst upp på webbplatsen.

## <a name="query-the-endpoint-with-a-different-utterance"></a>Skicka fråga till slutpunkten med ett annat yttrande
1. På sidan **Publish** (Publicera) väljer du länken **endpoint** (slutpunkt) längst ned på sidan. Den här åtgärden öppnar ett nytt webbläsarfönster med slutpunkts-URL i adressfältet. 

    ![Välj slutpunkts-URL](./media/luis-tutorial-composite-entity/publish-select-endpoint.png)

2. Gå till slutet av URL:en i adressen och ange `reserve 3 seats from London to Cairo on Sunday`. Den sista frågesträngsparametern är `q`, uttryck frågan. Det här yttrandet är inte samma som någon av de märkta yttrandena. Därför är det ett bra test och bör returnera avsikten `BookFlight` med den hierarkiska entiteten extraherad.

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

Den här uttryck som returnerar en matris, sammansatta entiteter inklusive den **flightreservation** objekt med de data som extraheras.  

## <a name="what-has-this-luis-app-accomplished"></a>Vad har den här LUIS-appen åstadkommit?
Den här appen, med bara två avsikter och en sammansatt entitet identifierat ett naturligt språk fråga avsikt och returnerade de extraherade data. 

Din chattrobot har nu tillräcklig information för att fastställa den primära åtgärden `BookFlight`, och reservation information som finns i uttryck. 

## <a name="where-is-this-luis-data-used"></a>Var används dessa LUIS-data? 
LUIS är klar med den här begäran. Det anropande programmet, till exempel en chattrobot, kan använda topScoringIntent-resultatet och data från entiteten för att gå vidare. LUIS utför inte detta programmässiga arbete för roboten eller det anropande programmet. LUIS tar endast reda på vad användarens avsikt är. 

## <a name="next-steps"></a>Nästa steg

[Läs mer om entiteter](luis-concept-entity-types.md). 
