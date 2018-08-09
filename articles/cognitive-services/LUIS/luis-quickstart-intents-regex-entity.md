---
title: Självstudie om att skapa en LUIS-app för att hämta data som matchas med reguljära uttryck – Azure | Microsoft Docs
description: I den här självstudien skapar du en enkel LUIS-app med hjälp av avsikter och en entitet för reguljära uttryck för att extrahera data.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 08/02/2018
ms.author: diberry
ms.openlocfilehash: 994bd6f2a041e25d15c7e0b4a216952cec4101fa
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2018
ms.locfileid: "39492831"
---
# <a name="tutorial-3-add-regular-expression-entity"></a>Självstudie: 3. Lägg till entitet för reguljära uttryck
I den här självstudien skapar du en app som visar hur det går till att extrahera konsekvent formaterade data från ett yttrande med hjälp av entiteten **Regular Expression** (Reguljärt uttryck).


<!-- green checkmark -->
> [!div class="checklist"]
> * Förstå entiteter för reguljära uttryck 
> * Använda en LUIS-app för en HR-domän (Human Resources) med avsikten FindForm
> * Lägga till entitet för reguljära uttryck för att extrahera formulärnummer från yttrande
> * Träna och publicera app
> * Skicka en fråga till appens slutpunkt för att se LUIS JSON-svar

[!include[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>Innan du börjar
Om du inte har appen Human Resources (Personalfrågor) från självstudien om [fördefinierade entiteter](luis-tutorial-prebuilt-intents-entities.md) ska du [importera](luis-how-to-start-new-app.md#import-new-app) JSON till en ny app på [LUIS-webbplatsen](luis-reference-regions.md#luis-website) från [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-prebuilts-HumanResources.json)-GitHub-lagringsplatsen.

Om du vill behålla den ursprungliga Human Resources-appen (Personalfrågor) klonar du versionen på sidan [Settings](luis-how-to-manage-versions.md#clone-a-version) (Inställningar) och ger den namnet `regex`. Kloning är ett bra sätt att prova på olika LUIS-funktioner utan att påverka originalversionen. 


## <a name="purpose-of-the-regular-expression-entity"></a>Syftet med entitet för reguljära uttryck
Syftet med en entitet är att hämta viktiga data som finns i yttrandet. Appens användning av entiteten för reguljära uttryck är att hämta formaterade Human Resources-formulärnummer (Personalfrågor) från ett yttrande. Den är inte maskininlärd. 

Enkla exempel på yttranden innefattar:

```
Where is HRF-123456?
Who authored HRF-123234?
HRF-456098 is published in French?
```

Förkortade versioner av eller slangvarianter på yttranden innefattar:

```
HRF-456098
HRF-456098 date?
HRF-456098 title?
```
 
Entiteten för reguljära uttryck för att matcha formulärnumret är `hrf-[0-9]{6}`. Den här reguljära uttrycket matchar de exakta tecknen `hrf -` men ignorerar konjugationer och kulturella varianter. Det matchar siffrorna 0–9 för exakt 6 siffror.

HRF står för Human Resources Form (formulär för personalfrågor).

### <a name="tokenization-with-hyphens"></a>Tokenisering med bindestreck
LUIS tokeniserar yttrandet när yttrandet läggs till i en avsikt. Tokeniseringen för de här yttrandena lägger till blanksteg före och efter bindestrecket, `Where is HRF - 123456?`  Det reguljära uttrycket tillämpas på yttrandet i obearbetat format, innan det tokeniseras. Eftersom det tillämpas i _obearbetat_ format behöver det reguljära uttrycket inte hantera ordgränser. 


## <a name="add-findform-intent"></a>Lägg till avsikten FindForm

1. Kontrollera att Human Resources-appen (Personalfrågor) finns i avsnittet **Build** (Skapa) i LUIS. Du kan ändra till det här avsnittet genom att välja **Build** (Skapa) i menyraden längst upp till höger. 

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

## <a name="create-an-hrf-number-regular-expression-entity"></a>Skapa en entitet för reguljära uttryck för HRF-nummer 
Skapa en entitet för reguljära uttryck för att ange för LUIS vad ett HRF-nummerformat är med följande steg:

1. Välj **Entities** (Entiteter) på den vänstra panelen.

2. Välj knappen **Create new entity** (Skapa ny entitet) på sidan Entities (Entiteter). 

3. I popop-dialogrutan anger du det nya entitetsnamnet `HRF-number`, väljer **RegEx** som enhetstyp, anger `hrf-[0-9]{6}` som Regex och väljer sedan **Done** (Klar).

    ![Skärmbild på inställning för popup-dialogruta med egenskaper för ny entitet](./media/luis-quickstart-intents-regex-entity/create-regex-entity.png)

4. Välj **Intents** (Avsikter) och sedan avsikten **FindForm** för att se det reguljära uttrycket märkt i yttrandena. 

    [![Skärmbild på yttrandet Label (Etikett) med befintlig entitet och regexmönster](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png)](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png#lightbox)

    Eftersom entiteten inte är en maskininlärd entitet tillämpas etiketten på yttrandena och visas på LUIS-webbplatsen så snart den skapas.

## <a name="train-the-luis-app"></a>Träna LUIS-appen

[!include[LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publicera appen för att få slutpunkts-URL

[!include[LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint-with-a-different-utterance"></a>Skicka fråga till slutpunkten med ett annat yttrande

1. [!include[LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Gå till slutet av URL:en i adressen och ange `When were HRF-123456 and hrf-234567 published in the last year?`. Den sista frågesträngsparametern är `q`, yttrande**frågan**. Det här yttrandet är inte samma som någon av de märkta yttrandena. Därför är det ett bra test och bör returnera avsikten `FindForm` med de två formulärnumren `HRF-123456` och `hrf-234567`.

    ```
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

## <a name="what-has-this-luis-app-accomplished"></a>Vad har den här LUIS-appen åstadkommit?
Den här appen identifierade avsikten och returnerade extraherade data. 

Din chattrobot har nu tillräckligt med information för att bestämma den primära åtgärden, `FindForm`, och de formulärnummer som fanns i sökningen. 

## <a name="where-is-this-luis-data-used"></a>Var används dessa LUIS-data? 
LUIS är klar med den här begäran. Det anropande programmet, till exempel en chattrobot, kan använda topScoringIntent-resultatet och formulärnumren för att söka ett tredjeparts-API. LUIS utför inte det arbetet. LUIS tar endast reda på vad användarens avsikt är och extraherar data om den avsikten. 

## <a name="clean-up-resources"></a>Rensa resurser

[!include[LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om listentiteten](luis-quickstart-intent-and-list-entity.md)

