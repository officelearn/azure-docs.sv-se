---
title: Sammansatt entitet ”
titleSuffix: Azure Cognitive Services
description: Lägg till en sammansatt entitet för att paketera olika typer av extraherade data i en enda innehållande entiteten. Genom att paketera data extrahera klientprogrammet enkelt relaterade data i olika datatyper.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: 18a32f5e07470f71ba276fbe3a2633150b1bf188
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/22/2018
ms.locfileid: "53754672"
---
# <a name="tutorial-group-and-extract-related-data"></a>Självstudier: Gruppera och extrahera relaterade data
I den här självstudien lägger du till en sammansatt entitet för att paketera olika typer av extraherade data i en enda innehållande entiteten. Genom att paketera data extrahera klientprogrammet enkelt relaterade data i olika datatyper.

Syftet med den sammansatta entiteten är att gruppera relaterade entiteter i en överordnad kategori. Informationen finns som separata entiteter innan en sammansatta skapas. Det liknar en hierarkisk entitet, men kan innehålla olika typer av enheter. 

Sammansatta entiteten är ett bra alternativ för den här typen av data eftersom data:

* Är relaterade till varandra. 
* Använda en mängd olika typer av enheter.
* Båda måste grupperas och bearbetas av klientappen som en informationsenhet.

**I den här självstudiekursen får du lära du dig att:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Exempelapp för import
> * Skapa avsikt
> * Lägg till sammansatt entitet 
> * Träna
> * Publicera
> * Hämta avsikter och entiteter från en slutpunkt

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Exempelapp för import

1.  Hämta och spara den [app JSON-fil](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/build-app/tutorial_list.json) från listan entitet självstudierna.

2. Importera JSON-koden till en ny app.

3. I avsnittet **Hantera** går du till fliken **Versioner**, klonar versionen och ger den namnet `composite`. Kloning är ett bra sätt att prova på olika LUIS-funktioner utan att påverka originalversionen. Eftersom versionsnamnet används i webbadressen får namnet inte innehålla några tecken som är ogiltiga i webbadresser.

## <a name="composite-entity"></a>Sammansatt entitet

I den här appen, ett avdelningsnamn definieras i den **avdelning** listan entitet och omfattar. 

Den **TransferEmployeeToDepartment** syftet har exempel yttranden att begära en anställd flyttas till en ny avdelning. 

Exempel yttranden avsikt följande:

|Exempel på yttranden|
|--|
|Flytta John W. Smith till ekonomiavdelningen|
|överföra Jill Jones från till R & D|
 
Begäran om att flytta bör innehålla avdelningsnamnet på och medarbetarnamn. 

## <a name="add-the-personname-prebuilt-entity-to-help-with-common-data-type-extraction"></a>Lägg till PersonName fördefinierade entitet som hjälper med common dataextrahering

LUIS har flera fördefinierade entiteter för extrahering av data. 

1. Välj **skapa** från det övre navigeringsfältet väljer **entiteter** från den vänstra navigeringsmenyn.

1. Välj knappen **Manage prebuilt entity** (Hantera fördefinierad entitet).

1. Välj **[PersonName](luis-reference-prebuilt-person.md)** Välj sedan i listan över fördefinierade entiteter **klar**.

    ![Skärmbild på dialogrutan för fördefinierade entiteter med nummer markerat](./media/luis-tutorial-composite-entity/add-personname-prebuilt-entity.png)

    Den här entiteten kan du lägga till namnet taligenkänning att ditt klientprogram.

## <a name="create-composite-entity-from-example-utterances"></a>Skapa sammansatta entitet från exempel yttranden

1. Välj **Intents** (Avsikter) i det vänstra navigeringsfönstret.

1. Välj **TransferEmployeeToDepartment** från listan över avsikter.

1. Välj personName-entitet i den första uttryck `John Jackson`och välj sedan **starta wrapping sammansatt entitet** i listan över popup-menyn för följande uttryck:

    `place John Jackson in engineering`

1. Välj sedan det senaste entitet omedelbart `engineering` i uttryck. En grön stapel dras under de valda ord som anger en sammansatt entitet. I popup-menyn, anger du sammansatta namn `TransferEmployeeInfo` och sedan anger du väljer. 

1. I **vilken typ av enhet vill du skapa?**, alla fält som krävs finns i listan: `personName` och `Department`. Välj **Done** (Klar). 

    Observera att entiteten fördefinierade personName, har lagts till sammansatta entiteten. Om du hade en fördefinierade entitet visas mellan första och sista token på en sammansatt entitet innehålla sammansatta entiteten de fördefinierade entiteterna. Om det inte ingår förskapade entiteter, sammansatta entiteten är inte korrekt förutse utan varje enskilt element.

## <a name="label-example-utterances-with-composite-entity"></a>Etikett exempel yttranden med sammansatta entitet


1. Välj vänster-entitet som ska vara i sammansatt i varje exempel-uttryck. Välj sedan **omsluta i sammansatt entitet**.

1. Välj det sista ordet i sammansatt entiteten och välj sedan **TransferEmployeeInfo** på snabbmenyn. 

1. Kontrollera alla uttryck i avsikten är märkta med den sammansatta entitet. 

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Träna appen så att ändringarna ska avsikten kan testas 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Publicera appen så att den tränade modellen är frågningsbar från slutpunkten

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Hämta avsikt och entiteten förutsägelse från slutpunkten 

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

* [Lista entitet självstudien](luis-quickstart-intents-only.md)
* [Sammansatt entitet](luis-concept-entity-types.md) konceptuell information
* [Hur du tränar](luis-how-to-train.md)
* [Så här publicerar du](luis-how-to-publish-app.md)
* [Testa LUIS-portalen](luis-interactive-test.md)


## <a name="next-steps"></a>Nästa steg

Den här självstudiekursen skapades en sammansatt entitet för att kapsla in befintliga entiteter. På så sätt kan klientprogram för att hitta en grupp av relaterade data i olika datatyper fortsätta konversationen. Ett klientprogram för den här personalapp kan be vilken dag och tid flytten måste börja och sluta. Det kan också fråga om andra logistik av flytt till exempel en fysisk telefon. 

> [!div class="nextstepaction"] 
> [Lär dig hur du lägger till en enkel enhet med en fras-lista](luis-quickstart-primary-and-secondary-data.md)  
