---
title: 'Självstudie 8: Extrahering av nyckelfraser i LUIS'
titleSuffix: Azure Cognitive Services
description: Använd den fördefinierade keyPhrase-entiteten till att extrahera nyckelämnen från yttranden. Du behöver inte märka ut några yttranden med fördefinierade entiteter. Entiteten identifieras automatiskt.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: d6feb24b1e59aee70204d8438f1a4c51f71d1835
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2018
ms.locfileid: "51281820"
---
# <a name="tutorial-8-extract-key-phrases-of-utterance"></a>Självstudie 8: Extrahera nyckelfraser från yttranden
I den här självstudien använder du den fördefinierade keyPhrase-entiteten till att extrahera nyckelämnen från yttranden. Du behöver inte märka upp några yttranden med fördefinierade entiteter. Entiteten identifieras automatiskt.

Följande yttranden visar exempel på nyckelfraser:

|Yttrande|keyPhrase-entitetsvärden|
|--|--|
|Kommer det att erbjudas en ny sjukvårdsplan med lägre självrisk nästa år?|”lägre självrisk”<br>”ny sjukvårdsplan”<br>”år”|
|Ingår synbehandling i sjukvårdsplanen med hög självrisk?|”sjukvårdsplan med hög självrisk”<br>”synbehandling”|

Ditt klientprogram kan använda dessa värden tillsammans med andra extraherade entiteter för att avgöra nästa steg i konversationen.

**I den här självstudiekursen får du lära du dig att:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Använda en befintlig självstudieapp
> * Lägga till keyPhrase-entitet 
> * Träna
> * Publicera
> * Hämta avsikter och entiteter från en slutpunkt

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Använda en befintlig app

Fortsätt med appen du skapade i föregående självstudie med namnet **HumanResources**. 

Om du inte har appen HumanResources från föregående självstudie gör du så här:

1.  Ladda ned och spara [JSON-filen för appen](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-simple-HumanResources.json).

2. Importera JSON-koden till en ny app.

3. I avsnittet **Hantera** går du till fliken **Versioner**, klonar versionen och ger den namnet `keyphrase`. Kloning är ett bra sätt att prova på olika LUIS-funktioner utan att påverka originalversionen. Eftersom versionsnamnet används i webbadressen får namnet inte innehålla några tecken som är ogiltiga i webbadresser.

## <a name="add-keyphrase-entity"></a>Lägga till keyPhrase-entitet 
Lägg till den fördefinierade keyPhrase-entiteten för att extrahera ämnesinnehåll från yttranden.

1. [!INCLUDE[Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Välj **Entities** (Entiteter) på den vänstra menyn.

3. Välj alternativet för att **lägga till en fördefinierad entitet**.

4. I popup-dialogrutan väljer du **keyPhrase** och sedan **Done** (Klar). 

    [ ![Skärmbild på popup-dialogrutan Entities list (Lista över entiteter)](./media/luis-quickstart-intent-and-key-phrase/hr-add-or-remove-prebuilt-entities.png)](./media/luis-quickstart-intent-and-key-phrase/hr-add-or-remove-prebuilt-entities.png#lightbox)

5. Välj **Intents** (Avsikter) på menyn till vänster och välj avsikten **Utilities.Confirm**. Entiteten keyPhrase är märkt med flera yttranden. 

    [ ![Skärmbild på avsikten Utilities.Confirm med keyPhrases märkta i yttranden](./media/luis-quickstart-intent-and-key-phrase/hr-keyphrase-labeled.png)](./media/luis-quickstart-intent-and-key-phrase/hr-keyphrase-labeled.png#lightbox)

## <a name="train"></a>Träna

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Publicera

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Hämta avsikter och entiteter från slutpunkten

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Gå till slutet av URL:en i adressen och ange `does form hrf-123456 cover the new dental benefits and medical plan`. Den sista frågesträngsparametern är `q`, yttrande**frågan**. 
    
    ```JSON
    {
      "query": "does form hrf-123456 cover the new dental benefits and medical plan",
      "topScoringIntent": {
        "intent": "FindForm",
        "score": 0.9300641
      },
      "intents": [
        {
          "intent": "FindForm",
          "score": 0.9300641
        },
        {
          "intent": "ApplyForJob",
          "score": 0.0359598845
        },
        {
          "intent": "GetJobInformation",
          "score": 0.0141798034
        },
        {
          "intent": "MoveEmployee",
          "score": 0.0112197418
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.00507669244
        },
        {
          "intent": "None",
          "score": 0.00238501839
        },
        {
          "intent": "Utilities.Help",
          "score": 0.00202810857
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.00102957746
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.0008688423
        },
        {
          "intent": "Utilities.Confirm",
          "score": 3.557994E-05
        }
      ],
      "entities": [
        {
          "entity": "hrf-123456",
          "type": "HRF-number",git 
          "startIndex": 10,
          "endIndex": 19
        },
        {
          "entity": "new dental benefits",
          "type": "builtin.keyPhrase",
          "startIndex": 31,
          "endIndex": 49
        },
        {
          "entity": "medical plan",
          "type": "builtin.keyPhrase",
          "startIndex": 55,
          "endIndex": 66
        },
        {
          "entity": "hrf",
          "type": "builtin.keyPhrase",
          "startIndex": 10,
          "endIndex": 12
        },
        {
          "entity": "-123456",
          "type": "builtin.number",
          "startIndex": 13,
          "endIndex": 19,
          "resolution": {
            "value": "-123456"
          }
        }
      ]
    }
    ```

    Användaren som sökte efter ett formulär angav mer information än vad som var nödvändigt för att hitta det. Den ytterligare informationen returneras som **builtin.keyPhrase**. Klientprogrammet kan använda den ytterligare informationen till följdfrågor som ”Vill du tala med en representant på personalavdelningen om den nya tandvårdsförsäkringen?”, eller för att uppge en meny med fler alternativ som ”Mer information om den nya tandvårds- eller sjukvårdsförsäkringen”.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien lade du till den fördefinierade keyPhrase-entiteten för att snabbt kunna få fram nyckelfraser ur yttranden utan att behöva märka upp yttrandena. 

> [!div class="nextstepaction"]
> [Lägga till sentimentanalys i appen](luis-quickstart-intent-and-sentiment-analysis.md)