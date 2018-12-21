---
title: Entitet med reguljärt uttryck
titleSuffix: Azure Cognitive Services
description: Extrahera data med ett enhetligt format från ett yttrande med entiteten för reguljära uttryck.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/07/2018
ms.author: diberry
ms.openlocfilehash: d4deeec2c5af5047fa16a2d80f0992409d517910
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53135584"
---
# <a name="tutorial-3-extract-well-formatted-data"></a>Självstudie 3: Extrahera välutformade data
I den här självstudien ska du ändra Human Resources-appen så att den extraherar data med ett enhetligt format från yttranden med entiteten **Regular Expression**.

Syftet med en entitet är att extrahera viktiga data från yttrandet. I den här appen används entiteten för reguljära uttryck till att hämta formaterade HR-formulärnummer (Human Resources) från ett yttrande. Även om avsikten med yttrandet alltid fastställs med maskininlärning är just den här entitetstypen inte maskininlärd. 

**Här är några exempelyttranden:**

|Exempel på yttranden|
|--|
|Var finns HRF-123456?|
|Vem skapade HRF-123234?|
|Har HRF-456098 publicerats på franska?|
|HRF-456098|
|Datum för HRF-456098?|
 
Ett reguljärt uttryck passar bra för den här typen av data i följande fall:

* data är välformade.

**I den här självstudiekursen får du lära du dig att:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Använda en befintlig självstudieapp
> * Lägg till avsikten FindForm
> * Lägg till entitet för reguljära uttryck 
> * Träna
> * Publicera
> * Hämta avsikter och entiteter från en slutpunkt

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Använda en befintlig app
Fortsätt med appen du skapade i föregående självstudie med namnet **HumanResources**. 

Om du inte har appen HumanResources från föregående självstudie gör du så här:

1. Ladda ned och spara [JSON-filen för appen](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-prebuilts-HumanResources.json).

2. Importera JSON-koden till en ny app.

3. I avsnittet **Hantera** går du till fliken **Versioner**, klonar versionen och ger den namnet `regex`. Kloning är ett bra sätt att prova på olika LUIS-funktioner utan att påverka originalversionen. Eftersom versionsnamnet används i webbadressen får namnet inte innehålla några tecken som är ogiltiga i webbadresser. 

## <a name="findform-intent"></a>Avsikten FindForm

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Välj **Create new intent** (Skapa ny avsikt). 

3. Ange `FindForm` i popup-dialogrutan och välj sedan **Done** (Klar). 

    ![Skärmbild på dialogrutan Create new intent (Skapa ny avsikt) med Utilities (Verktyg) i sökrutan](./media/luis-quickstart-intents-regex-entity/create-new-intent-ddl.png)

4. Lägg till exempel på yttranden i avsikten.

    |Exempel på yttranden|
    |--|
    |Vad är URL för hrf-123456?|
    |Var är hrf-345678?|
    |När uppdaterades hrf-456098?|
    |Uppdaterade Johan Svensson hrf-234639 förra veckan?|
    |Hur många versioner av hrf-345123 finns det?|
    |Vem behöver godkänna formuläret hrf-123456?|
    |Hur många personer måste signera hrf-345678?|
    |datum för hrf-234123?|
    |författare av hrf-546234?|
    |rubrik för hrf-456234?|

    [ ![Skärmbild på sidan Intent (Avsikt) med nya yttranden markerade](./media/luis-quickstart-intents-regex-entity/findform-intent.png) ](./media/luis-quickstart-intents-regex-entity/findform-intent.png#lightbox)

    Programmet har en fördefinierad nummerentitet som lagts till från den föregående självstudien. Därför är varje formulärnummer taggat. Det här kan vara tillräckligt för klientprogrammet, men numret kommer inte att märkas med den typen av nummer. Om en ny entitet med ett lämpligt namn skapas kan klientprogrammet bearbeta entiteten på rätt sätt när den returneras från LUIS.

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]  

## <a name="regular-expression-entity"></a>Entitet för reguljära uttryck 
Entiteten för reguljära uttryck för att matcha formulärnumret är `hrf-[0-9]{6}`. Den här reguljära uttrycket matchar de exakta tecknen `hrf-` men ignorerar konjugationer och kulturella varianter. Det matchar siffrorna 0–9 för exakt 6 siffror.

HRF står för `human resources form`.

LUIS tokeniserar yttrandet när det läggs till i en avsikt. Tokeniseringen för de här yttrandena lägger till blanksteg före och efter bindestrecket, `Where is HRF - 123456?`  Det reguljära uttrycket tillämpas på yttrandet i obearbetat format, innan det tokeniseras. Eftersom det tillämpas i _obearbetat_ format behöver det reguljära uttrycket inte hantera ordgränser. 

Skapa en entitet för reguljära uttryck för att ange för LUIS vad ett HRF-nummerformat är med följande steg:

1. Välj **Entities** (Entiteter) på den vänstra panelen.

2. Välj knappen **Create new entity** (Skapa ny entitet) på sidan Entities (Entiteter). 

3. Ange det nya entitetsnamnet `HRF-number` i dialogrutan, välj **RegEx** som entitetstyp, ange `hrf-[0-9]{6}` som **Regex**-värde och välj sedan **Klar**.

    ![Skärmbild på inställning för popup-dialogruta med egenskaper för ny entitet](./media/luis-quickstart-intents-regex-entity/create-regex-entity.png)

4. Välj **Avsikter** i menyn till vänster och sedan avsikten **FindForm** för att se det reguljära uttrycket uppmärkt i yttrandena. 

    [![Skärmbild på yttrandet Label (Etikett) med befintlig entitet och regexmönster](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png)](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png#lightbox)

    Eftersom entiteten inte är en maskininlärd entitet tillämpas etiketten på yttrandena och visas på LUIS-webbplatsen så snart den skapas.

## <a name="train"></a>Träna

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Publicera

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Hämta avsikter och entiteter från slutpunkten

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Gå till slutet av URL:en i adressen och ange `When were HRF-123456 and hrf-234567 published in the last year?`. Den sista frågesträngsparametern är `q`, yttrande**frågan**. Det här yttrandet är inte samma som någon av de märkta yttrandena. Därför är det ett bra test och bör returnera avsikten `FindForm` med de två formulärnumren `HRF-123456` och `hrf-234567`.

    ```json
    {
      "query": "When were HRF-123456 and hrf-234567 published in the last year?",
      "topScoringIntent": {
        "intent": "FindForm",
        "score": 0.9993477
      },
      "intents": [
        {
          "intent": "FindForm",
          "score": 0.9993477
        },
        {
          "intent": "ApplyForJob",
          "score": 0.0206110049
        },
        {
          "intent": "GetJobInformation",
          "score": 0.00533067342
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.004215215
        },
        {
          "intent": "Utilities.Help",
          "score": 0.00209096959
        },
        {
          "intent": "None",
          "score": 0.0017655947
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.00109490135
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.0005704638
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.000525338168
        }
      ],
      "entities": [
        {
          "entity": "last year",
          "type": "builtin.datetimeV2.daterange",
          "startIndex": 53,
          "endIndex": 61,
          "resolution": {
            "values": [
              {
                "timex": "2017",
                "type": "daterange",
                "start": "2017-01-01",
                "end": "2018-01-01"
              }
            ]
          }
        },
        {
          "entity": "hrf-123456",
          "type": "HRF-number",
          "startIndex": 10,
          "endIndex": 19
        },
        {
          "entity": "hrf-234567",
          "type": "HRF-number",
          "startIndex": 25,
          "endIndex": 34
        },
        {
          "entity": "-123456",
          "type": "builtin.number",
          "startIndex": 13,
          "endIndex": 19,
          "resolution": {
            "value": "-123456"
          }
        },
        {
          "entity": "-234567",
          "type": "builtin.number",
          "startIndex": 28,
          "endIndex": 34,
          "resolution": {
            "value": "-234567"
          }
        }
      ]
    }
    ```

    Numren i yttrandet returneras två gånger, en gång som den nya entiteten `hrf-number`, och en gång som en fördefinierad entitet, `number`. Ett yttrande kan ha mer än en entitet och mer än en av samma typ av enhet, vilket det här exemplet visar. När en entitet för reguljära uttryck används extraherar LUIS namngivna data, vilket är programmässigt mer användbart för det klientprogram som tar emot JSON-svaret.


## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg
I den här självstudien har du skapat en ny avsikt, lagt till exempelyttranden och sedan skapat en entitet för ett reguljärt uttryck för att extrahera välformade data från yttranden. När appen har tränats upp och publicerats identifierade en fråga till slutpunkten aktuell avsikt och extraherade data returnerades.

> [!div class="nextstepaction"]
> [Lär dig mer om listentiteten](luis-quickstart-intent-and-list-entity.md)

