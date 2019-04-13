---
title: Exakt textmatchning
titleSuffix: Azure Cognitive Services
description: Hämta data som matchar en fördefinierad lista med objekt. Varje objekt i listan kan ha synonymer som också matchar exakt
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: 9083227dd81dca219666e07b70f487069413855d
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/12/2019
ms.locfileid: "59521279"
---
# <a name="tutorial-get-exact-text-matched-data-from-an-utterance"></a>Självstudier: Hämta exakta textmatchade data från ett uttryck

I den här självstudien får du lära dig hur du hämtar entitetsdata som matchar en fördefinierad lista med objekt. 

**I den här självstudiekursen får du lära du dig att:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Skapa app
> * Lägga till avsikt
> * Lägg till listentitet 
> * Träna 
> * Publicera
> * Hämta avsikter och entiteter från en slutpunkt

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="what-is-a-list-entity"></a>Vad är en listentitet?

En listentitet är en exakt textmatchning av orden i ett uttryck. 

Varje objekt i listan kan innehålla en lista med synonymer. För personalappen kan en företagsavdelning identifieras genom olika typer av viktig information, t.ex. ett officiellt namn, en vanlig förkortning eller en faktureringskod. 

Personalappen måste fastställa vilken avdelning en medarbetare överförs till. 

En listentitet är ett bra alternativ för den här typen av data när:

* Datavärdena är en känd uppsättning.
* Uppsättningen inte överskrider de högsta [gränserna](luis-boundaries.md) för LUIS för den här entitetstypen.
* Texten i yttrandet stämmer exakt med en synonym eller det kanoniska namnet. LUIS använder inte listan för något mer än exakta textmatchningar. Ordstamsigenkänning, plural och andra variationer kan inte lösas enbart med en listentitet. Om du vill hantera variationer bör du överväga att använda ett [mönster](luis-concept-patterns.md#syntax-to-mark-optional-text-in-a-template-utterance) med valfri textsyntax. 

## <a name="create-a-new-app"></a>Skapa en ny app

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-an-intent-to-transfer-employees-to-a-different-department"></a>Skapa en avsikt att överföra anställda till en annan avdelning

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Välj **Create new intent** (Skapa ny avsikt). 

3. Ange `TransferEmployeeToDepartment` i popup-dialogrutan och välj sedan **Done** (Klar). 

    ![Skärmbild på dialogrutan Create new intent (Skapa ny avsikt)](./media/luis-quickstart-intent-and-list-entity/hr-create-new-intent-ddl.png)

4. Lägg till exempel på yttranden i avsikten.

    |Exempel på yttranden|
    |--|
    |Flytta John W. Smith till ekonomiavdelningen|
    |överför Jill Jones till utvecklingsavdelningen|
    |Avd 1234 har en ny medlem med namnet Bill Bradstreet|
    |Placera John Jackson på teknikavdelningen |
    |flytta Debra Doughtery till innesäljaravdelningen|
    |flytta Jill Jones till IT|
    |Flytta Alice Anderson till DevOps|
    |Carl Chamerlin till ekonomi|
    |Steve Standish till 1234|
    |Tanner Thompson till 3456|

    [![Skärmbild av avsikten med exempelyttranden](media/luis-quickstart-intent-and-list-entity/intent-transfer-employee-to-department.png "Skärmbild av avsikten med exempelyttranden")](media/luis-quickstart-intent-and-list-entity/intent-transfer-employee-to-department.png#lightbox)

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]  

## <a name="department-list-entity"></a>Entitet i avdelningslista

Nu när **TransferEmployeeToDepartment**-avsikten innehåller exempelyttranden måste LUIS förstå vad en avdelning är. 

Det primära, _kanoniska_ namnet för varje objekt är avdelningsnamnet. Några exempel på synonymer till respektive kanoniskt namn: 

|Kanoniskt namn|Synonymer|
|--|--|
|Redovisning|redov<br>redovisn<br>3456|
|Utvecklingsåtgärder|DevOps<br>4949|
|Teknik|eng<br>teknik<br>4567|
|Ekonomi|ek<br>2020|
|Informationsteknik|IT<br>2323|
|Innesäljare|isälj<br>insälj<br>1414|
|Forskning och utveckling|FoU<br>1234|

1. Välj **Entities** (Entiteter) på den vänstra panelen.

1. Välj **Create new entity** (Skapa ny entitet).

1. I dialogrutan för entiteter anger du `Department` som entitetsnamn och **List** (Lista) som entitetstyp. Välj **Done** (Klar).  

    [![Skärmbild av att skapa en ny popup-dialogruta för en entitet](media/luis-quickstart-intent-and-list-entity/create-new-list-entity-named-department.png "Skärmbild av att skapa en ny popup-dialogruta för en entitet")](media/luis-quickstart-intent-and-list-entity/create-new-list-entity-named-department.png#lightbox)

1. Ange `Accounting` som det nya värdet på avdelningsentitetssidan.

    [![Skärmbild av att ange värde](media/luis-quickstart-intent-and-list-entity/hr-emp1-value.png "Skärmbild av att ange värde")](media/luis-quickstart-intent-and-list-entity/hr-emp1-value.png#lightbox)

1. När det gäller synonymer lägger du till synonymerna från föregående tabell.

    [![Skärmbild av att ange synonymer](media/luis-quickstart-intent-and-list-entity/hr-emp1-synonyms.png "Skärmbild av att ange synonymer")](media/luis-quickstart-intent-and-list-entity/hr-emp1-synonyms.png#lightbox)

1. Fortsätt att lägga till alla kanoniska namn och deras synonymer. 

## <a name="add-example-utterances-to-the-none-intent"></a>Lägg till exempelyttranden till avsikten Ingen 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Träna appen så att avsiktsändringarna kan testas 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Publicera appen så att frågor kan köras mot den tränade modellen från slutpunkten

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Hämta avsikts- och entitetsförutsägelser från slutpunkten

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

1. Gå till slutet av URL:en i adressen och ange `shift Joe Smith to IT`. Den sista frågesträngsparametern är `q`, yttrande**f**rågan. Det här yttrandet är inte samma som någon av de märkta yttrandena. Därför är det ett bra test och bör returnera avsikten `TransferEmployeeToDepartment` med `Department` extraherad.

   ```json
    {
      "query": "shift Joe Smith to IT",
      "topScoringIntent": {
        "intent": "TransferEmployeeToDepartment",
        "score": 0.9775754
      },
      "intents": [
        {
          "intent": "TransferEmployeeToDepartment",
          "score": 0.9775754
        },
        {
          "intent": "None",
          "score": 0.0154493852
        }
      ],
      "entities": [
        {
          "entity": "it",
          "type": "Department",
          "startIndex": 19,
          "endIndex": 20,
          "resolution": {
            "values": [
              "Information Technology"
            ]
          }
        }
      ]
    }
   ```

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Relaterad information

* Konceptuell information om [listentitet](luis-concept-entity-types.md#list-entity)
* [Så här tränar du](luis-how-to-train.md)
* [Så här publicerar du](luis-how-to-publish-app.md)
* [Så här testar du i LUIS-portalen](luis-interactive-test.md)


## <a name="next-steps"></a>Nästa steg
I den här självstudien har du skapat en ny avsikt, lagt till exempelyttranden och sedan skapat en listentitet för att extrahera exakta textmatchningar från yttranden. När appen har tränats upp och publicerats identifierade en fråga till slutpunkten aktuell avsikt och extraherade data returnerades.

Fortsätt med den här appen och [lägg till en sammansatt entitet](luis-tutorial-composite-entity.md).

> [!div class="nextstepaction"]
> [Lägga till färdiga entitet med en roll i appen](tutorial-entity-roles.md)

