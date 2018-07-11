---
title: Självstudien skapa en sammansatt entitet för att extrahera komplexa data – Azure | Microsoft Docs
description: Lär dig hur du skapar en sammansatt entitet i din LUIS-app för att extrahera olika typer av entitetsdata.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: article
ms.date: 07/09/2018
ms.author: v-geberr
ms.openlocfilehash: d73dc9b9f204e334a75c9de5e19c6b11e3a95b12
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2018
ms.locfileid: "37929193"
---
# <a name="tutorial-6-add-composite-entity"></a>Självstudie: 6. Lägg till sammansatta entitet 
I den här självstudien lägger du till en sammansatt entitet för att bifoga extraherade data i en innehållande entiteten.

I den här guiden får du lära dig hur man:

<!-- green checkmark -->
> [!div class="checklist"]
> * Förstå sammansatta entiteter 
> * Lägg till sammansatta entitet för att extrahera data
> * Träna och publicera app
> * Skicka en fråga till appens slutpunkt för att se LUIS JSON-svar

## <a name="before-you-begin"></a>Innan du börjar
Om du inte har appen Human Resources (Personalfrågor) från självstudien om [hierarchical entity](luis-quickstart-intent-and-hier-entity.md) (hierarkisk entitet) ska du [importera](luis-how-to-start-new-app.md#import-new-app) JSON till en ny app på [LUIS-webbplatsen](luis-reference-regions.md#luis-website). Importeringsappen finns på [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-hier-HumanResources.json)-GitHub-lagringsplatsen.

Om du vill behålla den ursprungliga Human Resources-appen (Personalfrågor) klonar du versionen på sidan [Settings](luis-how-to-manage-versions.md#clone-a-version) (Inställningar) och ger den namnet `composite`. Kloning är ett bra sätt att prova på olika LUIS-funktioner utan att påverka originalversionen.  

## <a name="composite-entity-is-a-logical-grouping"></a>Sammansatt entitet är en logisk gruppering 
Syftet med den sammansatta entiteten är att gruppera relaterade entiteter i en överordnad kategori. Informationen finns som separata entiteter innan en sammansatta skapas. Det liknar hierarkisk entiteten, men kan innehålla flera typer av enheter. 

 Skapa en sammansatt entitet när separata entiteter kan grupperas logiskt och den här logisk gruppering kan vara bra att klientprogrammet. 

I den här appen medarbetarnamn har definierats i den **medarbetare** listan entitet och omfattar namn, e-postadress, företagets anknytningsnumret, mobiltelefonnummer och USA Federal skatte-ID. 

Den **MoveEmployee** syftet har exempel yttranden att begära en anställd flyttas från en kontorsbyggnad och till en annan. Skapa namn är alfabetiskt: ”A”, ”B” och annat kontor är numeriska: ”1234”, ”13245”. 

Exempel yttranden i den **MoveEmployee** avsikt inkluderar:

|Exempel på yttranden|
|--|
|Flytta John W. Smith ska kunna a-2345|
|flytta x12345 till h-1234 imorgon|
 
Begäran om att flytta bör minst omfatta medarbetaren (med några synonymen) och den slutgiltiga kontorsbyggnad och platsen. Begäran kan även innehålla den ursprungliga office samt ett datum som flytten som ska hända. 

Extraherade data från slutpunkten bör innehålla den här informationen och returnera den på i en `RequestEmployeeMove` sammansatt entitet. 

## <a name="create-composite-entity"></a>Skapa sammansatta entitet
1. Kontrollera att Human Resources-appen (Personalfrågor) finns i avsnittet **Build** (Skapa) i LUIS. Du kan ändra till det här avsnittet genom att välja **Build** (Skapa) i menyraden längst upp till höger. 

    [ ![Skärmbild på LUIS-appen med Build (Skapa) markerat i navigeringsfältet längst upp till höger](./media/luis-tutorial-composite-entity/hr-first-image.png)](./media/luis-tutorial-composite-entity/hr-first-image.png#lightbox)

2. På den **avsikter** väljer **MoveEmployee** avsikt. 

    [![](media/luis-tutorial-composite-entity/hr-intents-moveemployee.png "Skärmbild av LUIS med 'MoveEmployee' avsikt markerat")](media/luis-tutorial-composite-entity/hr-intents-moveemployee.png#lightbox)

3. Välj på förstoringsglaset i verktygsfältet om du vill filtrera listan yttranden. 

    [![](media/luis-tutorial-composite-entity/hr-moveemployee-magglass.png "Skärmbild av LUIS på 'MoveEmployee' avsikten med förstoringsglaset markerat")](media/luis-tutorial-composite-entity/hr-moveemployee-magglass.png#lightbox)

4. Ange `tomorrow` i textrutan filter för att hitta uttryck `shift x12345 to h-1234 tomorrow`.

    [![](media/luis-tutorial-composite-entity/hr-filter-by-tomorrow.png "Skärmbild av LUIS på 'MoveEmployee' syftet med filtret ”morgondagens' markerat")](media/luis-tutorial-composite-entity/hr-filter-by-tomorrow.png#lightbox)

    En annan metod är att filtrera entiteten efter datetimeV2, genom att välja **entitet filter** därefter **datetimeV2** i listan. 

5. Välj den första entiteten `Employee`och välj sedan **omsluta i sammansatt entitet** i listan över popup-menyn. 

    [![](media/luis-tutorial-composite-entity/hr-create-entity-1.png "Skärmbild av LUIS på 'MoveEmployee' avsikt att välja första entiteten i sammansatta markerat")](media/luis-tutorial-composite-entity/hr-create-entity-1.png#lightbox)


6. Välj sedan det senaste entitet omedelbart `datetimeV2` i uttryck. En grön stapel dras under de valda ord som anger en sammansatt entitet. I popup-menyn, anger du sammansatta namn `RequestEmployeeMove` därefter **skapa nya sammansatta** på popup-menyn. 

    [![](media/luis-tutorial-composite-entity/hr-create-entity-2.png "Skärmbild av LUIS på 'MoveEmployee' avsikt att välja senaste entitet i sammansatt och skapa entiteten markerat")](media/luis-tutorial-composite-entity/hr-create-entity-2.png#lightbox)

7. I **vilken typ av enhet vill du skapa?**, nästan alla fält som krävs finns i listan. Endast den ursprungliga platsen saknas. Välj **lägga till en underordnad entitet**väljer **Locations::Origin** från listan över befintliga entiteter, Välj **klar**. 

  ![Skärmbild av LUIS på 'MoveEmployee' avsikt att lägga till en annan entitet i popup-fönster](media/luis-tutorial-composite-entity/hr-create-entity-ddl.png)

8. Välj på förstoringsglaset i verktygsfältet för att ta bort filtret. 

## <a name="label-example-utterances-with-composite-entity"></a>Etikett exempel yttranden med sammansatta entitet
1. Välj vänster-entitet som ska vara i sammansatt i varje exempel-uttryck. Välj sedan **omsluta i sammansatt entitet**.

    [![](media/luis-tutorial-composite-entity/hr-label-entity-1.png "Skärmbild av LUIS på 'MoveEmployee' avsikt att välja första entiteten i sammansatta markerat")](media/luis-tutorial-composite-entity/hr-label-entity-1.png#lightbox)

2. Välj det sista ordet i sammansatt entiteten och välj sedan **RequestEmployeeMove** på snabbmenyn. 

    [![](media/luis-tutorial-composite-entity/hr-label-entity-2.png "Skärmbild av LUIS på 'MoveEmployee' avsikt att välja senaste entitet i sammansatta markerat")](media/luis-tutorial-composite-entity/hr-label-entity-2.png#lightbox)

3. Kontrollera alla uttryck i avsikten är märkta med den sammansatta entitet. 

    [![](media/luis-tutorial-composite-entity/hr-all-utterances-labeled.png "Skärmbild av LUIS på MoveEmployee om du med alla yttranden som är märkt")](media/luis-tutorial-composite-entity/hr-all-utterances-labeled.png#lightbox)

## <a name="train-the-luis-app"></a>Träna LUIS-appen
LUIS vet inte om den nya sammansatta entiteten tills appen har tränats. 

1. Längst uppe till höger på LUIS-webbplatsen väljer du knappen **Train** (Träna).

    ![Träna appen](./media/luis-tutorial-composite-entity/hr-train-button.png)

2. Träningen är klar när du ser det gröna statusfältet som bekräftar att det är klart längst upp på webbplatsen.

    ![Träningen är klar](./media/luis-tutorial-composite-entity/hr-trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publicera appen för att få slutpunkts-URL
För att få en LUIS-förutsägelse i en chattrobot eller i ett annat program måste du publicera appen. 

1. Längst uppe till höger på LUIS-webbplatsen väljer du knappen **Publish** (Publicera). 

2. Välj platsen Production (Produktionsplats) och knappen **Publish** (Publicera).

    ![Publicera app](./media/luis-tutorial-composite-entity/hr-publish-to-production.png)

3. Publiceringen är klar när du ser det gröna statusfältet som bekräftar att det är klart längst upp på webbplatsen.

## <a name="query-the-endpoint"></a>Fråga slutpunkten 
1. På sidan **Publish** (Publicera) väljer du länken **endpoint** (slutpunkt) längst ned på sidan. Den här åtgärden öppnar ett nytt webbläsarfönster med slutpunkts-URL i adressfältet. 

    ![Välj slutpunkts-URL](./media/luis-tutorial-composite-entity/hr-publish-select-endpoint.png)

2. Gå till slutet av URL:en i adressen och ange `Move Jill Jones from a-1234 to z-2345 on March 3 2 p.m.`. Den sista frågesträngsparametern är `q`, uttryck frågan. 

    Eftersom det här testet är att verifiera sammansatt extraheras korrekt, kan ett test antingen inkludera en befintliga exempel-uttryck eller en ny uttryck. Ett bra test är att inkludera alla underordnade entiteter i sammansatt entiteten.

```JSON
{
  "query": "Move Jill Jones from a-1234 to z-2345 on March 3  2 p.m",
  "topScoringIntent": {
    "intent": "MoveEmployee",
    "score": 0.9959525
  },
  "intents": [
    {
      "intent": "MoveEmployee",
      "score": 0.9959525
    },
    {
      "intent": "GetJobInformation",
      "score": 0.009858314
    },
    {
      "intent": "ApplyForJob",
      "score": 0.00728598563
    },
    {
      "intent": "FindForm",
      "score": 0.0058053555
    },
    {
      "intent": "Utilities.StartOver",
      "score": 0.005371796
    },
    {
      "intent": "Utilities.Help",
      "score": 0.00266987388
    },
    {
      "intent": "None",
      "score": 0.00123299169
    },
    {
      "intent": "Utilities.Cancel",
      "score": 0.00116407464
    },
    {
      "intent": "Utilities.Confirm",
      "score": 0.00102653319
    },
    {
      "intent": "Utilities.Stop",
      "score": 0.0006628214
    }
  ],
  "entities": [
    {
      "entity": "march 3 2 p.m",
      "type": "builtin.datetimeV2.datetime",
      "startIndex": 41,
      "endIndex": 54,
      "resolution": {
        "values": [
          {
            "timex": "XXXX-03-03T14",
            "type": "datetime",
            "value": "2018-03-03 14:00:00"
          },
          {
            "timex": "XXXX-03-03T14",
            "type": "datetime",
            "value": "2019-03-03 14:00:00"
          }
        ]
      }
    },
    {
      "entity": "jill jones",
      "type": "Employee",
      "startIndex": 5,
      "endIndex": 14,
      "resolution": {
        "values": [
          "Employee-45612"
        ]
      }
    },
    {
      "entity": "z - 2345",
      "type": "Locations::Destination",
      "startIndex": 31,
      "endIndex": 36,
      "score": 0.9690751
    },
    {
      "entity": "a - 1234",
      "type": "Locations::Origin",
      "startIndex": 21,
      "endIndex": 26,
      "score": 0.9713137
    },
    {
      "entity": "-1234",
      "type": "builtin.number",
      "startIndex": 22,
      "endIndex": 26,
      "resolution": {
        "value": "-1234"
      }
    },
    {
      "entity": "-2345",
      "type": "builtin.number",
      "startIndex": 32,
      "endIndex": 36,
      "resolution": {
        "value": "-2345"
      }
    },
    {
      "entity": "3",
      "type": "builtin.number",
      "startIndex": 47,
      "endIndex": 47,
      "resolution": {
        "value": "3"
      }
    },
    {
      "entity": "2",
      "type": "builtin.number",
      "startIndex": 50,
      "endIndex": 50,
      "resolution": {
        "value": "2"
      }
    },
    {
      "entity": "jill jones from a - 1234 to z - 2345 on march 3 2 p . m",
      "type": "requestemployeemove",
      "startIndex": 5,
      "endIndex": 54,
      "score": 0.4027723
    }
  ],
  "compositeEntities": [
    {
      "parentType": "requestemployeemove",
      "value": "jill jones from a - 1234 to z - 2345 on march 3 2 p . m",
      "children": [
        {
          "type": "builtin.datetimeV2.datetime",
          "value": "march 3 2 p.m"
        },
        {
          "type": "Locations::Destination",
          "value": "z - 2345"
        },
        {
          "type": "Employee",
          "value": "jill jones"
        },
        {
          "type": "Locations::Origin",
          "value": "a - 1234"
        }
      ]
    }
  ],
  "sentimentAnalysis": {
    "label": "neutral",
    "score": 0.5
  }
}
```

Den här uttryck returnerar en matris med sammansatta entiteter. Varje entitet är angiven typ och värde. Du hittar mer precision för varje underordnad entitet genom att använda en kombination av typen och värdet från sammansatta matris-objektet för att hitta motsvarande objekt i matrisen entiteter.  

## <a name="what-has-this-luis-app-accomplished"></a>Vad har den här LUIS-appen åstadkommit?
Den här appen har identifierat en fråga avsikt för naturligt språk och returnerade de extraherade data som en namngiven grupp. 

Din chattrobot har nu tillräcklig information för att fastställa den primära åtgärden och relaterade detaljer i uttryck. 

## <a name="where-is-this-luis-data-used"></a>Var används dessa LUIS-data? 
LUIS är klar med den här begäran. Det anropande programmet, till exempel en chattrobot, kan använda topScoringIntent-resultatet och data från entiteten för att gå vidare. LUIS utför inte detta programmässiga arbete för roboten eller det anropande programmet. LUIS tar endast reda på vad användarens avsikt är. 

## <a name="clean-up-resources"></a>Rensa resurser
Ta bort LUIS-appen när den inte längre behövs. Välj **Mina appar** på menyn längst upp till vänster. Välj ellipsen (***...*** ) till höger om appnamnet i applistan väljer **ta bort**. På popup-dialogrutan **Delete app?** (Ta bort appen?) väljer du **Ok**.

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"] 
> [Lär dig hur du lägger till en enkel enhet med en fras-lista](luis-quickstart-primary-and-secondary-data.md)  