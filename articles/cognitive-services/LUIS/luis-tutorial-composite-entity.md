---
title: 'Självstudiekurs 6: Extrahera sammansatta data med LUIS sammansatta entitet'
titleSuffix: Azure Cognitive Services
description: Lägg till en sammansatt entitet för att paketera olika typer av extraherade data i en enda innehållande entiteten. Genom att paketera data extrahera klientprogrammet enkelt relaterade data i olika datatyper.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: 192be1561559ac17ae98ae24ee92b292f38313a9
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/15/2018
ms.locfileid: "45629142"
---
# <a name="tutorial-6-group-and-extract-related-data"></a>Självstudiekurs 6: Gruppera och extrahera relaterade data
I den här självstudien lägger du till en sammansatt entitet för att paketera olika typer av extraherade data i en enda innehållande entiteten. Genom att paketera data extrahera klientprogrammet enkelt relaterade data i olika datatyper.

Syftet med den sammansatta entiteten är att gruppera relaterade entiteter i en överordnad kategori. Informationen finns som separata entiteter innan en sammansatta skapas. Det liknar hierarkisk entitet men kan innehålla olika typer av enheter. 

Sammansatta entiteten är ett bra alternativ för den här typen av data eftersom data:

* Är relaterade till varandra. 
* Använda en mängd olika typer av enheter.
* Måste vara grupperade och bearbetas av klientapp som en enhet med information.

**I den här självstudien får du lära dig hur du:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Använd befintliga självstudieappen
> * Lägg till sammansatt entitet 
> * Träna
> * Publicera
> * Hämta avsikter och entiteter från slutpunkten

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Använd befintlig app
Fortsätt med den app som skapats i den sista självstudien med namnet **ska**. 

Om du inte har appen ska från den tidigare självstudiekursen, använder du följande steg:

1.  Hämta och spara [app JSON-fil](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-hier-HumanResources.json).

2. Importera JSON till en ny app.

3. Från den **hantera** avsnittet på den **versioner** fliken klona versionen och ge den namnet `composite`. Kloning är ett bra sätt att prova på olika LUIS-funktioner utan att påverka originalversionen. Eftersom versionsnamnet används som en del av URL: en väg, får inte namnet innehålla några tecken som inte är giltiga i en URL.


## <a name="composite-entity"></a>Sammansatt entitet
Skapa en sammansatt entitet när separata entiteter kan grupperas logiskt och den här logisk gruppering kan vara bra att klientprogrammet. 

I den här appen medarbetarnamn har definierats i den **medarbetare** listan entitet och omfattar namn, e-postadress, företagets anknytningsnumret, mobiltelefonnummer och USA Federal skatte-ID. 

Den **MoveEmployee** syftet har exempel yttranden att begära en anställd flyttas från en kontorsbyggnad och till en annan. Skapa namn är alfabetiskt: ”A”, ”B” och annat kontor är numeriska: ”1234”, ”13245”. 

Exempel yttranden i den **MoveEmployee** avsikt inkluderar:

|Exempel på yttranden|
|--|
|Flytta John W. Smith ska kunna a-2345|
|flytta x12345 till h-1234 imorgon|
 
Begäran om att flytta bör ta medarbetaren (med några synonymen) och den slutgiltiga kontorsbyggnad och platsen. Begäran kan även innehålla den ursprungliga office samt ett datum som flytten som ska hända. 

Extraherade data från slutpunkten bör innehålla den här informationen och returnera den i den `RequestEmployeeMove` sammansatt entitet:

```JSON
"compositeEntities": [
  {
    "parentType": "RequestEmployeeMove",
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
]
```

1. [!include[Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. På den **avsikter** väljer **MoveEmployee** avsikt. 

3. Välj på förstoringsglaset i verktygsfältet om du vill filtrera listan yttranden. 

    [![](media/luis-tutorial-composite-entity/hr-moveemployee-magglass.png "Skärmbild av LUIS på 'MoveEmployee' avsikten med förstoringsglaset markerat")](media/luis-tutorial-composite-entity/hr-moveemployee-magglass.png#lightbox)

4. Ange `tomorrow` i textrutan filter för att hitta uttryck `shift x12345 to h-1234 tomorrow`.

    [![](media/luis-tutorial-composite-entity/hr-filter-by-tomorrow.png "Skärmbild av LUIS på 'MoveEmployee' syftet med filtret ”morgondagens' markerat")](media/luis-tutorial-composite-entity/hr-filter-by-tomorrow.png#lightbox)

    En annan metod är att filtrera entiteten efter datetimeV2, genom att välja **entitet filter** därefter **datetimeV2** i listan. 

5. Välj den första entiteten `Employee`och välj sedan **omsluta i sammansatt entitet** i listan över popup-menyn. 

    [![](media/luis-tutorial-composite-entity/hr-create-entity-1.png "Skärmbild av LUIS på 'MoveEmployee' avsikt att välja första entiteten i sammansatta markerat")](media/luis-tutorial-composite-entity/hr-create-entity-1.png#lightbox)


6. Välj sedan det senaste entitet omedelbart `datetimeV2` i uttryck. En grön stapel dras under de valda ord som anger en sammansatt entitet. I popup-menyn, anger du sammansatta namn `RequestEmployeeMove` och sedan anger du väljer. 

    [![](media/luis-tutorial-composite-entity/hr-create-entity-2.png "Skärmbild av LUIS på 'MoveEmployee' avsikt att välja senaste entitet i sammansatt och skapa entiteten markerat")](media/luis-tutorial-composite-entity/hr-create-entity-2.png#lightbox)

7. I **vilken typ av enhet vill du skapa?**, nästan alla fält som krävs finns i listan. Endast den ursprungliga platsen saknas. Välj **lägga till en underordnad entitet**väljer **Locations::Origin** från listan över befintliga entiteter, Välj **klar**. 

    Observera att färdiga entitet, number, har lagts till i den sammansatta entiteten. Om du hade en fördefinierade entitet visas mellan första och sista token på en sammansatt entitet innehålla sammansatta entiteten de fördefinierade entiteterna. Om det inte ingår förskapade entiteter, sammansatta entiteten är inte korrekt förutse utan varje enskilt element.

    ![Skärmbild av LUIS på 'MoveEmployee' avsikt att lägga till en annan entitet i popup-fönster](media/luis-tutorial-composite-entity/hr-create-entity-ddl.png)

8. Välj på förstoringsglaset i verktygsfältet för att ta bort filtret. 

9. Ta bort ordet `tomorrow` från filtret så att du kan se alla exempel yttranden igen. 

## <a name="label-example-utterances-with-composite-entity"></a>Etikett exempel yttranden med sammansatta entitet


1. Välj vänster-entitet som ska vara i sammansatt i varje exempel-uttryck. Välj sedan **omsluta i sammansatt entitet**.

    [![](media/luis-tutorial-composite-entity/hr-label-entity-1.png "Skärmbild av LUIS på 'MoveEmployee' avsikt att välja första entiteten i sammansatta markerat")](media/luis-tutorial-composite-entity/hr-label-entity-1.png#lightbox)

2. Välj det sista ordet i sammansatt entiteten och välj sedan **RequestEmployeeMove** på snabbmenyn. 

    [![](media/luis-tutorial-composite-entity/hr-label-entity-2.png "Skärmbild av LUIS på 'MoveEmployee' avsikt att välja senaste entitet i sammansatta markerat")](media/luis-tutorial-composite-entity/hr-label-entity-2.png#lightbox)

3. Kontrollera alla uttryck i avsikten är märkta med den sammansatta entitet. 

    [![](media/luis-tutorial-composite-entity/hr-all-utterances-labeled.png "Skärmbild av LUIS på MoveEmployee om du med alla yttranden som är märkt")](media/luis-tutorial-composite-entity/hr-all-utterances-labeled.png#lightbox)

## <a name="train"></a>Träna

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Publicera

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Hämta avsikt och entiteter från slutpunkten 

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

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
          "type": "RequestEmployeeMove",
          "startIndex": 5,
          "endIndex": 54,
          "score": 0.4027723
        }
      ],
      "compositeEntities": [
        {
          "parentType": "RequestEmployeeMove",
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
      ]
    }
    ```

  Den här uttryck returnerar en matris med sammansatta entiteter. Varje entitet är angiven typ och värde. Du hittar mer precision för varje underordnad entitet genom att använda en kombination av typen och värdet från sammansatta matris-objektet för att hitta motsvarande objekt i matrisen entiteter.  

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg

Den här självstudiekursen skapades en sammansatt entitet för att kapsla in befintliga entiteter. På så sätt kan klientprogram för att hitta en grupp av relaterade data i olika datatyper fortsätta konversationen. Ett klientprogram för den här personalapp kan be vilken dag och tid flytten måste börja och sluta. Det kan också fråga om andra logistik av movesuch som en fysisk telefon. 

> [!div class="nextstepaction"] 
> [Lär dig hur du lägger till en enkel enhet med en fras-lista](luis-quickstart-primary-and-secondary-data.md)  