---
title: 'Självstudie 4: Exakta textmatchningar – entiteten LUIS-lista'
titleSuffix: Azure Cognitive Services
description: Hämta data som matchar en fördefinierad lista med objekt. Varje objekt i listan kan ha synonymer som också matchar exakt
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: a4e294687b6c3ea2ba6ff8003e7a8f1ac69ea639
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/27/2018
ms.locfileid: "52425079"
---
# <a name="tutorial-4-extract-exact-text-matches"></a>Självstudie 4: Extrahera exakta textmatchningar
I den här självstudien får du lära dig hur du hämtar data som matchar en fördefinierad lista med objekt. Varje objekt i listan kan innehålla en lista med synonymer. I Human Resources-appen kan medarbetare identifieras med flera olika uppgifter, som namn, e-post, telefonnummer och skatte-id. 

Human Resources-appen måste fastställa vilken medarbetare som flyttar från en byggnad till en annan. I ett yttrande om en medarbetarflytt fastställer LUIS avsikten och extraherar medarbetaren så att klientappen kan skapa en standardorder om att flytta medarbetaren.

I den här appen används en listentitet till att extrahera medarbetaren. Medarbetaren kan identifieras med hjälp av namn, anknytning, mobiltelefonnummer, e-postadress eller amerikanskt socialförsäkringsnummer. 

En listentitet är ett bra alternativ för den här typen av data när:

* Datavärdena är en känd uppsättning.
* Uppsättningen inte överskrider de högsta [gränserna](luis-boundaries.md) för LUIS för den här entitetstypen.
* Texten i yttrandet stämmer exakt med en synonym eller det kanoniska namnet. 

**I den här självstudiekursen får du lära du dig att:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Använda en befintlig självstudieapp
> * Lägga till avsikten MoveEmployee
> * Lägg till listentitet 
> * Träna 
> * Publicera
> * Hämta avsikter och entiteter från en slutpunkt

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Använda en befintlig app
Fortsätt med appen du skapade i föregående självstudie med namnet **HumanResources**. 

Om du inte har appen HumanResources från föregående självstudie gör du så här:

1.  Ladda ned och spara [JSON-filen för appen](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-regex-HumanResources.json).

2. Importera JSON-koden till en ny app.

3. I avsnittet **Hantera** går du till fliken **Versioner**, klonar versionen och ger den namnet `list`. Kloning är ett bra sätt att prova på olika LUIS-funktioner utan att påverka originalversionen. Eftersom versionsnamnet används i webbadressen får namnet inte innehålla några tecken som är ogiltiga i webbadresser. 


## <a name="moveemployee-intent"></a>Avsikten MoveEmployee

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Välj **Create new intent** (Skapa ny avsikt). 

3. Ange `MoveEmployee` i popup-dialogrutan och välj sedan **Done** (Klar). 

    ![Skärmbild på dialogrutan Create new intent (Skapa ny avsikt)](./media/luis-quickstart-intent-and-list-entity/hr-create-new-intent-ddl.png)

4. Lägg till exempel på yttranden i avsikten.

    |Exempel på yttranden|
    |--|
    |flytta John W. Smith från B-1234 till H-4452|
    |flytta john.w.smith@mycompany.com från kontoret b-1234 till kontoret h-4452|
    |flytta x12345 till h-1234 imorgon|
    |placera 425-555-1212 i HH-2345|
    |flytta 123-45-6789 från A-4321 till J-23456|
    |flytta Jill Jones från D-2345 till J-23456|
    |flytta jill-jones@mycompany.com till M-12345|
    |x23456 till M-12345|
    |425-555-0000 till h-4452|
    |234-56-7891 till hh-2345|

    [ ![Skärmbild på sidan Intent (Avsikt) med nya yttranden markerade](./media/luis-quickstart-intent-and-list-entity/hr-enter-utterances.png) ](./media/luis-quickstart-intent-and-list-entity/hr-enter-utterances.png#lightbox)

    Kom ihåg att number och datetimeV2 lades till i en tidigare självstudie och automatiskt märks ut när de identifieras i yttranden.

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]  

## <a name="employee-list-entity"></a>Listentitet för medarbetare
Avsikten **MoveEmployee** innehåller nu exempelyttranden, och LUIS behöver förstå vad en medarbetare är. 

Det primära, _kanoniska_ namnet för varje objekt är medarbetarens anställningsnummer. Här är några exempel på synonymer för den här domänen: 

|Synonymsyfte|Synonymvärde|
|--|--|
|Namn|John W. Smith|
|E-postadress|john.w.smith@mycompany.com|
|Telefonanknytning|x12345|
|Personligt mobilnummer|425-555-1212|
|Amerikanskt socialförsäkringsnummer|123-45-6789|


1. Välj **Entities** (Entiteter) på den vänstra panelen.

2. Välj **Create new entity** (Skapa ny entitet).

3. I dialogrutan för entiteter anger du `Employee` som entitetsnamn och **List** (Lista) som entitetstyp. Välj **Done** (Klar).  

    [![](media/luis-quickstart-intent-and-list-entity/hr-list-entity-ddl.png "Skärmbild på dialogrutan för att skapa en ny entitet")](media/luis-quickstart-intent-and-list-entity/hr-list-entity-ddl.png#lightbox)

4. På entitetssidan för medarbetare anger du `Employee-24612` som det nya värdet.

    [![](media/luis-quickstart-intent-and-list-entity/hr-emp1-value.png "Skärmbild på värde som anges")](media/luis-quickstart-intent-and-list-entity/hr-emp1-value.png#lightbox)

5. Lägg till följande värden för synonymer:

    |Synonymsyfte|Synonymvärde|
    |--|--|
    |Namn|John W. Smith|
    |E-postadress|john.w.smith@mycompany.com|
    |Telefonanknytning|x12345|
    |Personligt mobilnummer|425-555-1212|
    |Amerikanskt socialförsäkringsnummer|123-45-6789|

    [![](media/luis-quickstart-intent-and-list-entity/hr-emp1-synonyms.png "Skärmbild på synonymer som läggs till")](media/luis-quickstart-intent-and-list-entity/hr-emp1-synonyms.png#lightbox)

6. Ange `Employee-45612` som nytt värde.

7. Lägg till följande värden för synonymer:

    |Synonymsyfte|Synonymvärde|
    |--|--|
    |Namn|Jill Jones|
    |E-postadress|jill-jones@mycompany.com|
    |Telefonanknytning|x23456|
    |Personligt mobilnummer|425-555-0000|
    |Amerikanskt socialförsäkringsnummer|234-56-7891|

## <a name="train"></a>Träna

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Publicera

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Hämta avsikter och entiteter från slutpunkten

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

2. Gå till slutet av URL:en i adressen och ange `shift 123-45-6789 from Z-1242 to T-54672`. Den sista frågesträngsparametern är `q`, yttrande**f**rågan. Det här yttrandet är inte samma som någon av de märkta yttrandena. Därför är det ett bra test och bör returnera avsikten `MoveEmployee` med `Employee` extraherad.

  ```JSON
  {
    "query": "shift 123-45-6789 from Z-1242 to T-54672",
    "topScoringIntent": {
      "intent": "MoveEmployee",
      "score": 0.9882801
    },
    "intents": [
      {
        "intent": "MoveEmployee",
        "score": 0.9882801
      },
      {
        "intent": "FindForm",
        "score": 0.016044287
      },
      {
        "intent": "GetJobInformation",
        "score": 0.007611245
      },
      {
        "intent": "ApplyForJob",
        "score": 0.007063288
      },
      {
        "intent": "Utilities.StartOver",
        "score": 0.00684710965
      },
      {
        "intent": "None",
        "score": 0.00304174074
      },
      {
        "intent": "Utilities.Help",
        "score": 0.002981
      },
      {
        "intent": "Utilities.Confirm",
        "score": 0.00212222221
      },
      {
        "intent": "Utilities.Cancel",
        "score": 0.00191026414
      },
      {
        "intent": "Utilities.Stop",
        "score": 0.0007461446
      }
    ],
    "entities": [
      {
        "entity": "123 - 45 - 6789",
        "type": "Employee",
        "startIndex": 6,
        "endIndex": 16,
        "resolution": {
          "values": [
            "Employee-24612"
          ]
        }
      },
      {
        "entity": "123",
        "type": "builtin.number",
        "startIndex": 6,
        "endIndex": 8,
        "resolution": {
          "value": "123"
        }
      },
      {
        "entity": "45",
        "type": "builtin.number",
        "startIndex": 10,
        "endIndex": 11,
        "resolution": {
          "value": "45"
        }
      },
      {
        "entity": "6789",
        "type": "builtin.number",
        "startIndex": 13,
        "endIndex": 16,
        "resolution": {
          "value": "6789"
        }
      },
      {
        "entity": "-1242",
        "type": "builtin.number",
        "startIndex": 24,
        "endIndex": 28,
        "resolution": {
          "value": "-1242"
        }
      },
      {
        "entity": "-54672",
        "type": "builtin.number",
        "startIndex": 34,
        "endIndex": 39,
        "resolution": {
          "value": "-54672"
        }
      }
    ]
  }
  ```

  Medarbetaren hittades och returnerades som typen `Employee` med lösningsvärdet `Employee-24612`.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg
I den här självstudien har du skapat en ny avsikt, lagt till exempelyttranden och sedan skapat en listentitet för att extrahera exakta textmatchningar från yttranden. När appen har tränats upp och publicerats identifierade en fråga till slutpunkten aktuell avsikt och extraherade data returnerades.

> [!div class="nextstepaction"]
> [Lägg till en hierarkisk enhet i appen](luis-quickstart-intent-and-hier-entity.md)

