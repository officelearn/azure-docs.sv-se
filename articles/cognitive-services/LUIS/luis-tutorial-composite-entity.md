---
title: 'Självstudie: själv studie kurs om sammansatta entiteter – LUIS'
titleSuffix: Azure Cognitive Services
description: Lägg till en sammansatt entitet för att paketera olika typer av extraherade data i en enda innehållande entiteten. Genom att paketera data extrahera klientprogrammet enkelt relaterade data i olika datatyper.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: diberry
ms.openlocfilehash: 0e72563f366330f841d1a61ed67956b6314c769a
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2019
ms.locfileid: "74893201"
---
# <a name="tutorial-group-and-extract-related-data"></a>Självstudie: gruppera och extrahera relaterade data
I den här självstudien lägger du till en sammansatt entitet för att paketera olika typer av extraherade data i en enda innehållande entiteten. Genom att paketera data extrahera klientprogrammet enkelt relaterade data i olika datatyper.

Syftet med den sammansatta entiteten är att gruppera relaterade entiteter i en överordnad kategori. Informationen finns som separata entiteter innan en sammansatta skapas.

Sammansatta entiteten är ett bra alternativ för den här typen av data eftersom data:

* Är relaterade till varandra.
* Använda en mängd olika typer av enheter.
* Båda måste grupperas och bearbetas av klientappen som en informationsenhet.

[!INCLUDE [Only valid with current portal](includes/old-portal-only.md)]

**I den här självstudiekursen får du lära du dig att:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importera exempelappen
> * Skapa avsikt
> * Lägg till sammansatt entitet
> * Lär in
> * Publicera
> * Hämta avsikter och entiteter från slutpunkt

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Importera exempelappen

1.  Ladda ned och spara [app-JSON-filen](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/build-app/tutorial_list.json) från själv studie kursen för List entiteter.

2. Importera JSON-koden till en ny app.

3. I avsnittet **Hantera** går du till fliken **Versioner**, klonar versionen och ger den namnet `composite`. Kloning är ett bra sätt att prova på olika LUIS-funktioner utan att påverka originalversionen. Eftersom versionsnamnet används som en del av URL-vägen får namnet inte innehålla några tecken som inte är giltiga i en URL.

## <a name="composite-entity"></a>Sammansatt entitet

I den här appen definieras avdelnings namnet i entiteten **avdelnings** lista och innehåller synonymer.

**TransferEmployeeToDepartment** -avsikten har till exempel yttranden att begära att en medarbetare flyttas till en ny avdelning.

Exempel på yttranden för den här avsikten är:

|Exempel på yttranden|
|--|
|Flytta John W. Smith till ekonomiavdelningen|
|överför Jill Jones till utvecklingsavdelningen|

Flyttnings förfrågan ska innehålla avdelnings namnet och namnet på den anställda.

## <a name="add-the-personname-prebuilt-entity-to-help-with-common-data-type-extraction"></a>Lägg till PersonName-fördefinierad entitet för att hjälpa till med gemensam data typs extrahering

LUIS har flera fördefinierade entiteter för extrahering av data.

1. Välj **build (Bygg** ) i det övre navigerings fältet och välj sedan **entiteter** i den vänstra navigerings menyn.

1. Välj knappen **Manage prebuilt entity** (Hantera fördefinierad entitet).

1. Välj **[PersonName](luis-reference-prebuilt-person.md)** i listan över fördefinierade entiteter och välj sedan **slutfört**.

    ![Skärmbild på dialogrutan för fördefinierade entiteter med nummer markerat](./media/luis-tutorial-composite-entity/add-personname-prebuilt-entity.png)

    Med den här entiteten kan du lägga till namn igenkänning i klient programmet.

## <a name="create-composite-entity-from-example-utterances"></a>Skapa sammansatt entitet från exempel yttranden

1. Välj **Intents** (Avsikter) i det vänstra navigeringsfönstret.

1. Välj **TransferEmployeeToDepartment** i listan med intenter.

1. I uttryck `place John Jackson in engineering`väljer du entiteten personName, `John Jackson`och väljer sedan **Radbryt i sammansatt entitet** i listan med popup-menyn för följande uttryck.

    ![Skärm bild av den nedrullningsbara dialog rutan Markera figursatta sammansatt i](./media/luis-tutorial-composite-entity/hr-create-composite-entity-1.png)

1. Välj sedan det senaste entitet omedelbart `engineering` i uttryck. En grön stapel dras under de valda ord som anger en sammansatt entitet. I popup-menyn, anger du sammansatta namn `TransferEmployeeInfo` och sedan anger du väljer.

    ![Skärm bild av dialog rutan Ange sammansatt namn i listruta](./media/luis-tutorial-composite-entity/hr-create-composite-entity-2.png)

1. I **vilken typ av entitet vill du skapa?** , alla fält som krävs finns i listan: `personName` och `Department`. Välj **Done** (Klar). Observera att den fördefinierade entiteten personName har lagts till i den sammansatta entiteten. Om du hade en fördefinierade entitet visas mellan första och sista token på en sammansatt entitet innehålla sammansatta entiteten de fördefinierade entiteterna. Om det inte ingår förskapade entiteter, sammansatta entiteten är inte korrekt förutse utan varje enskilt element.

    ![Skärm bild av dialog rutan Ange sammansatt namn i listruta](./media/luis-tutorial-composite-entity/hr-create-composite-entity-3.png)

## <a name="label-example-utterances-with-composite-entity"></a>Etikett exempel yttranden med sammansatta entitet

1. Välj vänster-entitet som ska vara i sammansatt i varje exempel-uttryck. Välj sedan **omsluta i sammansatt entitet**.

1. Välj det sista ordet i den sammansatta entiteten och välj sedan **TransferEmployeeInfo** på popup-menyn.

1. Kontrollera alla uttryck i avsikten är märkta med den sammansatta entitet.

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Träna appen så att ändringar av avsikten kan testas

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Publicera appen så att frågor kan köras på den tränade modellen från slutpunkten

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Hämta avsikts- och entitetsförutsägelser från slutpunkten

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Gå till slutet av URL:en i adressen och ange `Move Jill Jones to DevOps`. Den sista frågesträngsparametern är `q`, uttryck frågan.

    Eftersom det här testet är att verifiera sammansatt extraheras korrekt, kan ett test antingen inkludera en befintliga exempel-uttryck eller en ny uttryck. Ett bra test är att inkludera alla underordnade entiteter i sammansatt entiteten.

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

   Den här uttryck returnerar en matris med sammansatta entiteter. Varje entitet är angiven typ och värde. Du hittar mer precision för varje underordnad entitet genom att använda en kombination av typen och värdet från sammansatta matris-objektet för att hitta motsvarande objekt i matrisen entiteter.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Relaterad information

* [Självstudie för lista med entiteter](luis-quickstart-intents-only.md)
* Konceptuell information om [sammansatt entitet](luis-concept-entity-types.md)
* [Så här tränar du](luis-how-to-train.md)
* [Så här publicerar du](luis-how-to-publish-app.md)
* [Så här testar du i LUIS-portalen](luis-interactive-test.md)


## <a name="next-steps"></a>Nästa steg

Den här självstudiekursen skapades en sammansatt entitet för att kapsla in befintliga entiteter. På så sätt kan klientprogram för att hitta en grupp av relaterade data i olika datatyper fortsätta konversationen. Ett klientprogram för den här personalapp kan be vilken dag och tid flytten måste börja och sluta. Det kan också fråga om andra logistiker av flytten, till exempel en fysisk telefon.

> [!div class="nextstepaction"]
> [Åtgärda osäker förutsägelse genom att granska slut punkts yttranden](luis-tutorial-review-endpoint-utterances.md)
