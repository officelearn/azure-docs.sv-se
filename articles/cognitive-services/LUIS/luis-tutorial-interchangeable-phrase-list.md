---
title: Självstudie som använder en fras-lista för att förbättra LUIS förutsägelser – Azure | Microsoft Docs
description: Lägga till en fras-lista till en LUIS-app och se förbättring av poängen i de här självstudierna.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2017
ms.author: v-geberr
ms.openlocfilehash: 2fd53225a455a34d03772487a10f62a94ac86735
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/06/2018
ms.locfileid: "37868981"
---
# <a name="tutorial-add-phrase-list-to-improve-predictions"></a>Självstudie: Lägg till frasen lista för att förbättra förutsägelser
I de här självstudierna förbättra avsikt poäng och identifiera entiteter för ord som har samma innebörd (synonymer) genom att lägga till en utbytbara [frasen lista funktionen](./luis-concept-feature.md).

> [!div class="checklist"]
* Importera en ny app  
* Fråga slutpunkt med kända uttryck 
* Fråga slutpunkt med _okänd_ uttryck
* Lägg till frasen lista för att förbättra okänt uttryck poäng
* Kontrollera att hitta enheten när du använder frasen lista

För den här artikeln behöver du ett kostnadsfritt [LUIS-konto][LUIS] för att kunna redigera LUIS-programmet.

## <a name="import-a-new-app"></a>Importera en ny app
1. Ladda ned den [exempel LUIS-app] [ LuisSampleApp] som har utformats för den här självstudiekursen. Du kan använda den i nästa steg. 

2. Mer information finns i [skapa en app](Create-new-app.md#import-new-app), importera filen som du hämtade till den [LUIS] [ LUIS] webbplatsen som en ny app. Appens namn är ”min frasen lista självstudiekurs”. Den har avsikter och entiteter yttranden. 

3. [Träna](luis-how-to-train.md) din app. Förrän den har installerats kan du inte [interaktivt testa](interactive-test.md#interactive-testing) den i den [LUIS] [ LUIS] webbplats. 

4. På den [publicera](luis-how-to-publish-app.md) väljer den **inkludera alla förutse avsikt poäng** markerar du kryssrutan. När kryssrutan är markerad, returneras alla avsikter. När kryssrutan är avmarkerad, returneras endast det främsta syftet. 

5. [Publicera](luis-how-to-publish-app.md) appen. Publicera appen kan du testa den genom att använda HTTPS-slutpunkt. 

## <a name="test-a-trained-utterance"></a>Testa en tränad uttryck
Du kan använda den publicerade slutpunkten för att fråga ett uttryck som appen känner redan. Eftersom LUIS redan vet att uttryck, poängen är hög och entiteten har identifierats.

1. På den [Språkförståelse (LUIS)] [ LUIS] webbplats, på den **publicera** för den nya appen, Välj slutpunkts-URL i den **resurser och nycklar**avsnittet. 

    ![Publicera slutpunkts-URL](./media/luis-tutorial-interchangeable-phrase-list/luis-publish-url.png)

2. Lägg till följande fråga efter i slutet av Webbadressen i webbläsaren, den `q=`.

    `I want a computer replacement`

    Slutpunkten som svarar med följande JSON:
    
    ```JSON
    {
      "query": "I want a computer replacement",
      "topScoringIntent": {
        "intent": "GetHardware",
        "score": 0.9735458
      },
      "intents": [
        {
          "intent": "GetHardware",
          "score": 0.9735458
        },
        {
          "intent": "None",
          "score": 0.07053033
        },
        {
          "intent": "Whois",
          "score": 0.03760778
        },
        {
          "intent": "CheckCalendar",
          "score": 0.02285902
        },
        {
          "intent": "CheckInventory",
          "score": 0.0110072717
        }
      ],
      "entities": [
        {
          "entity": "computer",
          "type": "Hardware",
          "startIndex": 9,
          "endIndex": 16,
          "score": 0.8465915
        }
      ]
    }
    ```

    Avsiktshantering poängen för 0.973 och entiteten identifiering poängen för 0.846 är hög eftersom appen har lärt sig med den här uttryck. Uttryck som är i LUIS-appen på sidan avsiktshantering för **GetHardware**. Det uttryck text, `computer`, är märkta som den **maskinvara** entitet. 
    
    |Status|Word| Avsiktshantering poäng | Entiteten poäng |
    |--|--|--|--|
    |Tränas| vill | 0.973 | 0.846 |
    
    
## <a name="test-an-untrained-utterance"></a>Testa ett omdöme uttryck
Använd samma publicerade slutpunkten så att frågan med ett uttryck som inte redan känner till appen i webbläsaren:

`I require a computer replacement`

Den här uttryck använder en synonym för föregående uttryck:

| Tränade word | Omdöme synonymen |
|--|--|
| vill | Kräv |

Slutpunkt-svaret är:

```JSON
{
  "query": "I require a computer replacement",
  "topScoringIntent": {
    "intent": "GetHardware",
    "score": 0.840912163
  },
  "intents": [
    {
      "intent": "GetHardware",
      "score": 0.840912163
    },
    {
      "intent": "None",
      "score": 0.0972757638
    },
    {
      "intent": "Whois",
      "score": 0.0448251367
    },
    {
      "intent": "CheckCalendar",
      "score": 0.0291390456
    },
    {
      "intent": "CheckInventory",
      "score": 0.0137837809
    }
  ],
  "entities": []
}
```

| Status | Word | Avsiktshantering poäng | Entiteten poäng |
|--|--|--|--|
| Tränas| vill | 0.973 | 0.846 |
| Omdöme| Kräv | 0.840 | - |

Omdöme uttryck avsikt poängen är lägre än för den märkta uttryck eftersom LUIS vet att meningen grammatiskt är samma. Men LUIS vet inte att talade har samma innebörd. Dessutom utan listan frasen den **maskinvara** entiteten finns inte.

Du måste lära LUIS som *vill* och *kräver* betyda samma sak i den här appen domänen eftersom ett ord som kan ha fler än en betydelse. 

## <a name="improve-the-score-of-untrained-utterance-with-phrase-list"></a>Förbättra resultatet av omdöme uttryck med frasen lista 
1. Lägg till en [frasen lista](luis-how-to-add-features.md) funktion för **vill** med värdet för `want`, och välj sedan **RETUR**.

    > [!TIP]
    > När du har varje ord eller fraser, väljer den **RETUR** nyckel. Ordet eller frasen läggs till i den **fras listvärden** rutan samtidigt som markören fortfarande är i den **värdet** box. Du kan ange flera värden snabbt med den här funktionen.

2. Om du vill visa de ord som LUIS rekommenderar, Välj **rekommenderar**. 

    ![Rekommendera värden](./media/luis-tutorial-interchangeable-phrase-list/recommend.png)

3. Lägg till alla orden. Om `require` är inte i listan med rekommenderade lägga till det som ett obligatoriskt värde. 

4. Eftersom dessa ord är synonymer kan hålla den *utbytbara* inställning och välj sedan **spara**.

    ![Fras listvärden](./media/luis-tutorial-interchangeable-phrase-list/phrase-list-values.png)

5. I det övre navigeringsfältet väljer **träna** att träna appen, men inte publicerar den. Nu har du två modeller. Du kan jämföra värden i de två modellerna.

## <a name="compare-the-phrase-list-model-to-the-published-model"></a>Jämför listan frasen modellen till den publicerade modellen
I den här appen är inte publicerade modellen tränas med synonymerna. Endast just redigerat modellen innehåller frasen lista med synonymer. Om du vill jämföra modeller, använda [interaktiva testning](interactive-test.md#interactive-testing). 

1. Öppna den **Test** fönstret och ange följande uttryck:

    `I require a computer replacement`

2. Om du vill öppna panelen inspektion, Välj **granska**. 

    ![Välj inspektera](./media/luis-tutorial-interchangeable-phrase-list/inspect-button.png)

3. Om du vill jämföra den publicerade modellen till den nya modellen i frasen listan, Välj **Jämför med publicerade**.

    ![Inspektera publicerade gentemot ström](./media/luis-tutorial-interchangeable-phrase-list/inspect.png)

När du lägger till listan frasen ökad riktighet i uttryck och **maskinvara** hitta enheten. 

|Status | Fras lista| Avsiktshantering poäng | Entiteten poäng |
|--|--|--|--|
| Publicerad | - | 0,84 | - |
| Redigerar |✔| 0.92 | Maskinvara person |

> [!TIP]
> * Med hjälp av [interaktiva testning](interactive-test.md#interactive-testing), kan du jämföra den publicerade modellen för att alla tränade ändringar som görs när du har publicerat. 
> * Med hjälp av [Endpoint testning](luis-how-to-publish-app.md#test-your-published-endpoint-in-a-browser), du kan visa det exakta LUIS-svaret JSON. 

## <a name="get-the-entity-score-with-the-endpoint-test"></a>Hämta entity-poäng med slutpunkt-test
Visa entitet poäng, [publicera modellen](luis-how-to-publish-app.md) och skicka frågor till slutpunkten. 

`I require a computer replacement`

```JSON
{
  "query": "I require a computer replacement",
  "topScoringIntent": {
    "intent": "GetHardware",
    "score": 0.916503668
  },
  "intents": [
    {
      "intent": "GetHardware",
      "score": 0.916503668
    },
    {
      "intent": "None",
      "score": 0.136505231
    },
    {
      "intent": "Whois",
      "score": 0.02778677
    },
    {
      "intent": "CheckInventory",
      "score": 0.0144592477
    },
    {
      "intent": "CheckCalendar",
      "score": 0.01401332
    }
  ],
  "entities": [
    {
      "entity": "computer",
      "type": "Hardware",
      "startIndex": 12,
      "endIndex": 19,
      "score": 0.5959917
    }
  ]
}
```

Den **maskinvara** entiteten visar ett resultat på 0.595 innehåller frasen-listan. Innan det fanns listan frasen har entiteten inte identifierats. 

|Status | Fras lista| Avsiktshantering poäng | Entiteten poäng |
|--|--|--|--|
| Publicerad | - | 0,84 | - |
| Redigerar |✔| 0.92 | 0.595 |


## <a name="clean-up-resources"></a>Rensa resurser
Ta bort LUIS-appen när den inte längre behövs. Om du vill göra det, Välj ellipsen (***...*** ) till höger om appnamnet i programlistan, Välj **ta bort**. På popup-dialogrutan **Delete app?** (Ta bort appen?) väljer du **Ok**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hämta uttryck förutsägelse med slutpunkt-fråga](luis-get-started-cs-get-intent.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions
[LuisFeatures]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-feature
[LuisSampleApp]: https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/phrase_list/interchangeable/luis-app-before-phrase-list.json
