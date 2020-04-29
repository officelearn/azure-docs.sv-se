---
title: 'Självstudie: själv studie kurs om sammansatta entiteter – LUIS'
description: I den här självstudien lägger du till en sammansatt enhet för att bunta extraherade data av olika typer till en enda som innehåller entiteten. Genom att koppla samman data kan klient programmet enkelt extrahera relaterade data i olika data typer.
ms.topic: tutorial
ms.date: 03/31/2020
ms.openlocfilehash: 5b8185a56c54ec92ce8ceaf1cd029dd31f6e709c
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80478679"
---
# <a name="tutorial-group-and-extract-related-data"></a>Självstudie: gruppera och extrahera relaterade data
I den här självstudien lägger du till en sammansatt enhet för att bunta extraherade data av olika typer till en enda som innehåller entiteten. Genom att koppla samman data kan klient programmet enkelt extrahera relaterade data i olika data typer.

Syftet med den sammansatta entiteten är att gruppera relaterade entiteter i en entitet för överordnad kategori. Informationen finns som separata entiteter innan en sammansatt skapas.

Den sammansatta entiteten passar bra för den här typen av data eftersom data:

* Är relaterade till varandra.
* Använd olika typer av enheter.
* Båda måste grupperas och bearbetas av klientappen som en informationsenhet.

**I den här guiden får du lära dig att:**

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

1.  Ladda ned och spara [app-JSON-filen](
https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/build-app/tutorial_list.json?raw=true) från själv studie kursen för List entiteter.

2. Importera JSON till en ny app med hjälp av [Luis-portalen](https://www.luis.ai).

3. I avsnittet **Hantera** går du till fliken **Versioner**, klonar versionen och ger den namnet `composite`. Kloning är ett bra sätt att prova på olika LUIS-funktioner utan att påverka originalversionen. Eftersom versionsnamnet används i webbadressen får namnet inte innehålla några tecken som är ogiltiga i webbadresser.

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

1. I uttryck `place John Jackson in engineering`väljer du entiteten `John Jackson`PersonName och väljer sedan **Radbryt i sammansatt entitet** i popup-menyn för följande uttryck.

    ![Skärm bild av den nedrullningsbara dialog rutan Markera figursatta sammansatt i](./media/luis-tutorial-composite-entity/hr-create-composite-entity-1.png)

1. Välj sedan omedelbart den sista entiteten `engineering` i uttryck. Ett grönt fält ritas under de markerade orden som visar en sammansatt entitet. I popup-menyn anger du det sammansatta namnet `TransferEmployeeInfo` och väljer sedan Retur.

    ![Skärm bild av dialog rutan Ange sammansatt namn i listruta](./media/luis-tutorial-composite-entity/hr-create-composite-entity-2.png)

1. I **vilken typ av entitet vill du skapa?**, alla fält som krävs finns i listan: `personName` och. `Department` Välj **Klar**. Observera att den fördefinierade entiteten personName har lagts till i den sammansatta entiteten. Om du kan ha en fördefinierad entitet som visas mellan början och slutet av en sammansatt entitet måste den sammansatta entiteten innehålla de fördefinierade entiteterna. Om fördefinierade entiteter inte ingår, förutsägs inte den sammansatta entiteten, men varje enskilt element är.

    ![Skärm bild av dialog rutan Ange sammansatt namn i listruta](./media/luis-tutorial-composite-entity/hr-create-composite-entity-3.png)

## <a name="label-example-utterances-with-composite-entity"></a>Label-exempel yttranden med sammansatt entitet

1. I varje exempel uttryck väljer du den vänstra entiteten som ska vara i den sammansatta. Välj sedan **Radbryt i sammansatt entitet**.

1. Välj det sista ordet i den sammansatta entiteten och välj sedan **TransferEmployeeInfo** på popup-menyn.

1. Kontrol lera att alla yttranden i avsikten är märkta med den sammansatta entiteten.

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Träna appen så att avsiktsändringarna kan testas

Välj **träna**för att träna appen. Träningen tillämpar ändringarna, till exempel nya entiteter och den märkta yttranden, i den aktiva modellen.

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>Publicera appen för att få åtkomst till den från HTTP-slutpunkten

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Hämta avsikts- och entitetsförutsägelser från slutpunkten

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Gå till slutet av URL:en i adressen och ange `Move Jill Jones to DevOps`. Den sista frågesträngsparametern är `q`, yttrandefrågan.

    Eftersom det här testet är att kontrol lera att den sammansatta informationen extraheras korrekt, kan ett test antingen ta med ett befintligt exempel-uttryck eller en ny uttryck. Ett lyckat test är att inkludera alla underordnade entiteter i den sammansatta entiteten.

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

   Den här uttryck returnerar en matris för sammansatta entiteter. Varje entitet tilldelas en typ och ett värde. Om du vill hitta mer precision för varje underordnad entitet använder du kombinationen av typ och värde från det sammansatta array-objektet för att hitta motsvarande objekt i entiteten entiteter.

[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Relaterad information

* [Självstudie för lista med entiteter](luis-quickstart-intents-only.md)
* Konceptuell information om [sammansatt entitet](luis-concept-entity-types.md)
* [Så här tränar du](luis-how-to-train.md)
* [Så här publicerar du](luis-how-to-publish-app.md)
* [Så här testar du i LUIS-portalen](luis-interactive-test.md)


## <a name="next-steps"></a>Nästa steg

I den här självstudien skapade du en sammansatt entitet för att kapsla in befintliga entiteter. Detta gör att klient programmet kan hitta en grupp relaterade data i olika data typer för att fortsätta konversationen. Ett klient program för den här typen av personal kan fråga den dag och den tid som flytten måste börja och sluta. Det kan också fråga om andra logistiker av flytten, till exempel en fysisk telefon.

> [!div class="nextstepaction"]
> [Åtgärda osäkra förutsägelser genom att granska slutpunktsyttranden](luis-tutorial-review-endpoint-utterances.md)
