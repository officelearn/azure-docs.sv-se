---
title: 'Självstudiekurs: Självstudiekurs för sammansatt entitet - LUIS'
titleSuffix: Azure Cognitive Services
description: I den här självstudien lägger du till en sammansatt entitet för att paketera extraherade data av olika typer i en enda innehållande entitet. Genom att bunta ihop data kan klientprogrammet enkelt extrahera relaterade data i olika datatyper.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: diberry
ms.openlocfilehash: f2b2c3f52610cd9fae0845b15aebf032a088000b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75447948"
---
# <a name="tutorial-group-and-extract-related-data"></a>Självstudiekurs: Gruppera och extrahera relaterade data
I den här självstudien lägger du till en sammansatt entitet för att paketera extraherade data av olika typer i en enda innehållande entitet. Genom att bunta ihop data kan klientprogrammet enkelt extrahera relaterade data i olika datatyper.

Syftet med den sammansatta entiteten är att gruppera relaterade entiteter i en moderkategori. Informationen finns som separata entiteter innan en sammansatt skapas.

Den sammansatta entiteten passar bra för den här typen av data eftersom data:

* Är relaterade till varandra.
* Använd en mängd olika entitetstyper.
* Båda måste grupperas och bearbetas av klientappen som en informationsenhet.

**I den här självstudiekursen får du lära du dig att:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importera exempelappen
> * Skapa avsikt
> * Lägg till sammansatt entitet
> * Träna
> * Publicera
> * Hämta avsikter och entiteter från en slutpunkt

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Importera exempelappen

1.  Hämta och spara [filen JSON i](
https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/build-app/tutorial_list.json?raw=true) listentiteten.

2. Importera JSON till en ny app med [LUIS-portalen](https://www.luis.ai).

3. I avsnittet **Hantera** går du till fliken **Versioner**, klonar versionen och ger den namnet `composite`. Kloning är ett bra sätt att prova på olika LUIS-funktioner utan att påverka originalversionen. Eftersom versionsnamnet används i webbadressen får namnet inte innehålla några tecken som är ogiltiga i webbadresser.

## <a name="composite-entity"></a>Sammansatt entitet

I den här appen definieras avdelningsnamnet i **entiteten avdelningslista** och innehåller synonymer.

Avsikten **TransferEmployeeToDepartment** har exempelyttranden för att begära att en medarbetare flyttas till en ny avdelning.

Exempel yttranden för den här avsikten är:

|Exempel på yttranden|
|--|
|Flytta John W. Smith till ekonomiavdelningen|
|överför Jill Jones till utvecklingsavdelningen|

Flyttbegäran ska innehålla avdelningsnamnet och medarbetarens namn.

## <a name="add-the-personname-prebuilt-entity-to-help-with-common-data-type-extraction"></a>Lägga till den fördefinierade entiteten för personnamn för att hjälpa till med vanliga datatypsextrahering

LUIS har flera fördefinierade entiteter för extrahering av data.

1. Välj **Skapa** i den övre navigeringen och välj sedan **Entiteter** på den vänstra navigeringsmenyn.

1. Välj knappen **Manage prebuilt entity** (Hantera fördefinierad entitet).

1. Välj **[PersonName](luis-reference-prebuilt-person.md)** i listan över fördefinierade entiteter och välj sedan **Klar**.

    ![Skärmbild på dialogrutan för fördefinierade entiteter med nummer markerat](./media/luis-tutorial-composite-entity/add-personname-prebuilt-entity.png)

    Den här entiteten hjälper dig att lägga till namnigenkänning i klientprogrammet.

## <a name="create-composite-entity-from-example-utterances"></a>Skapa sammansatt entitet från exempelyttranden

1. Välj **Intents** (Avsikter) i det vänstra navigeringsfönstret.

1. Välj **TransferEmployeeToDepartment** i avsiktslistan.

1. Välj personName-entiteten i `place John Jackson in engineering` `John Jackson`uttrycksutseendeten och välj sedan **Radbryt i sammansatt entitet** i snabbmenyn för följande uttryck.

    ![Skärmbild av hur du väljer figursättningskomposit i listrutan](./media/luis-tutorial-composite-entity/hr-create-composite-entity-1.png)

1. Välj sedan omedelbart den `engineering` sista entiteten i uttryck. Ett grönt fält ritas under de markerade orden som anger en sammansatt entitet. På popup-menyn anger du det `TransferEmployeeInfo` sammansatta namnet och väljer sedan enter.

    ![Skärmbild av att ange sammansatt namn i listrutan](./media/luis-tutorial-composite-entity/hr-create-composite-entity-2.png)

1. I **Vilken typ av entitet vill du skapa?** `personName` `Department` Välj **Done** (Klar). Observera att den fördefinierade entiteten, personName, har lagts till i den sammansatta entiteten. Om du kunde ha en fördefinierad entitet som visas mellan början- och sluttoken för en sammansatt entitet, måste den sammansatta entiteten innehålla dessa fördefinierade entiteter. Om de fördefinierade entiteterna inte inkluderas förutses inte den sammansatta entiteten korrekt, men varje enskilt element är det.

    ![Skärmbild av att ange sammansatt namn i listrutan](./media/luis-tutorial-composite-entity/hr-create-composite-entity-3.png)

## <a name="label-example-utterances-with-composite-entity"></a>Etikettexempel yttranden med sammansatt entitet

1. I varje exempel yttrande väljer du den vänstra entiteten som ska vara i sammansatt. Välj sedan **Radbryt i sammansatt entitet**.

1. Markera det sista ordet i den sammansatta entiteten och välj sedan **TransferEmployeeInfo** på popup-menyn.

1. Kontrollera att alla yttranden i avsikten är märkta med den sammansatta entiteten.

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Träna appen så att avsiktsändringarna kan testas

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Publicera appen så att frågor kan köras mot den tränade modellen från slutpunkten

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Hämta avsikts- och entitetsförutsägelser från slutpunkten

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Gå till slutet av URL:en i adressen och ange `Move Jill Jones to DevOps`. Den sista frågesträngsparametern är `q`, yttrandefrågan.

    Eftersom det här testet är att kontrollera att kompositen extraheras korrekt, kan ett test antingen innehålla ett befintligt exempelyttrande eller ett nytt uttryck. Ett bra test är att inkludera alla underordnade entiteter i den sammansatta entiteten.

    ```json
    {
      "query": "Move Jill Jones to DevOps",
      "topScoringIntent": {
        "intent": "TransferEmployeeToDepartment",
        "score": 0.9882747
      },
      "intents": [
        {
          "intent": "TransferEmployeeToDepartment",
          "score": 0.9882747
        },
        {
          "intent": "None",
          "score": 0.00925369747
        }
      ],
      "entities": [
        {
          "entity": "jill jones",
          "type": "builtin.personName",
          "startIndex": 5,
          "endIndex": 14
        },
        {
          "entity": "devops",
          "type": "Department",
          "startIndex": 19,
          "endIndex": 24,
          "resolution": {
            "values": [
              "Development Operations"
            ]
          }
        },
        {
          "entity": "jill jones to devops",
          "type": "TransferEmployeeInfo",
          "startIndex": 5,
          "endIndex": 24,
          "score": 0.9607566
        }
      ],
      "compositeEntities": [
        {
          "parentType": "TransferEmployeeInfo",
          "value": "jill jones to devops",
          "children": [
            {
              "type": "builtin.personName",
              "value": "jill jones"
            },
            {
              "type": "Department",
              "value": "devops"
            }
          ]
        }
      ]
    }
    ```

   Det här uttrycket returnerar en sammansatt entitetsmatris. Varje entitet får en typ och ett värde. Om du vill hitta mer precision för varje underordnad entitet använder du kombinationen av typ och värde från sammansatta matrisobjekt för att hitta motsvarande objekt i entitetsmatrisen.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Relaterad information

* [Lista självstudiekurs för entitet](luis-quickstart-intents-only.md)
* Sammansatt begreppsmässig information [om entitet](luis-concept-entity-types.md)
* [Så här tränar du](luis-how-to-train.md)
* [Så här publicerar du](luis-how-to-publish-app.md)
* [Så här testar du i LUIS-portalen](luis-interactive-test.md)


## <a name="next-steps"></a>Nästa steg

Den här självstudien skapade en sammansatt entitet för att kapsla in befintliga entiteter. På så sätt kan klientprogrammet hitta en grupp relaterade data i olika datatyper för att fortsätta konversationen. Ett klientprogram för den här personalappen kan fråga vilken dag och tid flytten behöver börja och sluta. Det kan också fråga om annan logistik av flytten såsom en fysisk telefon.

> [!div class="nextstepaction"]
> [Åtgärda osäkra förutsägelser genom att granska slutpunktsyttranden](luis-tutorial-review-endpoint-utterances.md)
