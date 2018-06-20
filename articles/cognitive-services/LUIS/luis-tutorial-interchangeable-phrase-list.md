---
title: Självstudier med en fras lista för att förbättra THOMAS förutsägelser - Azure | Microsoft Docs
description: Lägga till en fras lista till en THOMAS app i den här självstudiekursen och se förbättring av poängsättningen.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2017
ms.author: v-geberr
ms.openlocfilehash: feb8acb674fd2dc62b62c26da6a6b42515f30242
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265979"
---
# <a name="tutorial-add-phrase-list-to-improve-predictions"></a>Självstudier: Lägg till fras lista för att förbättra förutsägelser
I den här självstudiekursen förbättra avsiktshantering resultat och identifiera entiteter för ord som har samma betydelse (synonymer) genom att lägga till en utbytbara [frasen listfunktionen](./luis-concept-feature.md).

> [!div class="checklist"]
* Importera en ny app  
* Frågan slutpunkten med kända utterance 
* Fråga slutpunkten med _okänd_ utterance
* Lägg till fras lista för att förbättra okänd utterance poäng
* Kontrollera entiteten finns när du använder frasen lista

För den här artikeln behöver du ett kostnadsfritt [THOMAS] [ LUIS] konto för att kunna redigera THOMAS programmet.

## <a name="import-a-new-app"></a>Importera en ny app
1. Hämta den [exempel THOMAS app] [ LuisSampleApp] som är utformade för den här kursen. Du kan använda den i nästa steg. 

2. Enligt beskrivningen i [skapa en app](Create-new-app.md#import-new-app), importera filen som du hämtade till den [THOMAS] [ LUIS] webbplatsen som en ny app. Namnet på appen är ”min frasen listan självstudiekurs”. Det har avsikter, enheter och utterances. 

3. [Train](luis-how-to-train.md) din app. Förrän den har installerats kan du inte [interaktivt testa](interactive-test.md#interactive-testing) den i den [THOMAS] [ LUIS] webbplats. 

4. På den [publicera](PublishApp.md) väljer den **inkludera alla förutsade avsiktshantering poäng** kryssrutan. När kryssrutan är markerad, returneras alla avsikter. När kryssrutan är avmarkerad returneras endast de översta avsikten. 

5. [Publicera](PublishApp.md) appen. Publicera appen kan du testa den med hjälp av HTTPS-slutpunkten. 

## <a name="test-a-trained-utterance"></a>Testa en tränad utterance
Använda publicerade slutpunkten för att fråga en utterance appen känner redan. Eftersom THOMAS redan känner av utterance, poängsättningen är hög och entiteten har identifierats.

1. På den [språk förstå (THOMAS)] [ LUIS] webbplats på den **publicera** för den nya appen, Välj slutpunkts-URL i den **resurser och nycklar**avsnitt. 

    ![Publicera slutpunkts-URL](./media/luis-tutorial-interchangeable-phrase-list/luis-publish-url.png)

2. Lägg till följande fråga efter i slutet av URL-Adressen i webbläsaren, den `q=`.

    `I want a computer replacement`

    Slutpunkten svarar med följande JSON:
    
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

    Avsiktshantering poängen för 0.973 och entiteten identifiering poängen för 0.846 är hög eftersom appen har tränas med den här utterance. Utterance är i THOMAS appen på sidan avsiktshantering för **GetHardware**. Den utterance text `computer`, är märkt som den **maskinvara** entitet. 
    
    |Status|Word| Avsiktshantering poäng | Entiteten poäng |
    |--|--|--|--|
    |Tränats| vill | 0.973 | 0.846 |
    
    
## <a name="test-an-untrained-utterance"></a>Testa ett omdöme utterance
Använd samma publicerade slutpunkten frågan med ett utterance som inte redan känner till appen i webbläsaren:

`I require a computer replacement`

Den här utterance använder en synonym för tidigare utterance:

| Utbildade word | Omdöme synonymen |
|--|--|
| vill | Kräver |

Svaret från slutpunkten är:

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
| Tränats| vill | 0.973 | 0.846 |
| Omdöme| Kräver | 0.840 | - |

Omdöme utterance avsiktshantering poäng är lägre än för märkt utterance eftersom THOMAS vet att meningen grammatiskt är samma. Men THOMAS vet inte att utterances har samma betydelse. Dessutom utan listan frasen den **maskinvara** entiteten finns inte.

Du måste lära THOMAS som *vill* och *kräver* betyda samma sak i denna tillämpningsdomän eftersom ett ord som kan ha fler än en betydelse. 

## <a name="improve-the-score-of-untrained-utterance-with-phrase-list"></a>Förbättra resultatet av omdöme utterance med frasen lista 
1. Lägg till en [frasen listan](luis-how-to-add-features.md) egenskapen med namnet **vill** med värdet för `want`, och välj sedan **RETUR**.

    > [!TIP]
    > Efter varje ord eller en fras, Välj den **RETUR** nyckel. Ett ord eller en fras som har lagts till i den **fras listvärden** rutan när markören hålls i den **värdet** rutan. Du kan ange flera värden snabbt med den här funktionen.

2. Om du vill visa de ord som THOMAS rekommenderar **rekommenderar**. 

    ![Rekommendera värden](./media/luis-tutorial-interchangeable-phrase-list/recommend.png)

3. Lägg till alla ord. Om `require` är inte i den rekommenderade listan Lägg till den som ett obligatoriskt värde. 

4. Eftersom dessa ord synonymer kan hålla den *utbytbara* inställningen och välj sedan **spara**.

    ![Frasen listvärden](./media/luis-tutorial-interchangeable-phrase-list/phrase-list-values.png)

5. I det övre navigeringsfältet väljer **träna** för att träna appen, men inte publicera den. Nu har du två modeller. Du kan jämföra värdena i de två modellerna.

## <a name="compare-the-phrase-list-model-to-the-published-model"></a>Jämför frasen listan modellen till publicerade modellen
Publicerade modellen har inte tränats med synonymerna i den här appen. För närvarande redigerade modellen innehåller frasen listan över synonymer. Om du vill jämföra modeller, Använd [interaktiva testa](interactive-test.md#interactive-testing). 

1. Öppna den **Test** rutan och ange följande utterance:

    `I require a computer replacement`

2. Om du vill öppna panelen inspektion, Välj **granska**. 

    ![Välj inspektera](./media/luis-tutorial-interchangeable-phrase-list/inspect-button.png)

3. Om du vill jämföra publicerade modellen till den nya modellen i frasen listan, Välj **Jämför med publicerade**.

    ![Inspektera publicerade jämfört med aktuella](./media/luis-tutorial-interchangeable-phrase-list/inspect.png)

När du lägger till listan frasen ökad korrektheten i utterance och **maskinvara** hitta enheten. 

|Status | Frasen lista| Avsiktshantering poäng | Entiteten poäng |
|--|--|--|--|
| Publicerad | - | 0,84 | - |
| Redigerar |✔| 0.92 | Maskinvaruenhet som identifieras |

> [!TIP]
> * Med hjälp av [interaktiva testning](interactive-test.md#interactive-testing), kan du jämföra publicerade modellen på alla utbildade ändringar som görs när du har publicerat. 
> * Med hjälp av [Endpoint testning](PublishApp.md#test-your-published-endpoint-in-a-browser), kan du visa det exakta svaret THOMAS JSON. 

## <a name="get-the-entity-score-with-the-endpoint-test"></a>Hämta entiteten poäng endpoint-test
Visa entitet poäng, [publicera modellen](PublishApp.md) och fråga slutpunkten. 

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

Den **maskinvara** entiteten visar ett resultat på 0.595 med frasen listan. Innan listan frasen fanns hittades entiteten inte. 

|Status | Frasen lista| Avsiktshantering poäng | Entiteten poäng |
|--|--|--|--|
| Publicerad | - | 0,84 | - |
| Redigerar |✔| 0.92 | 0.595 |


## <a name="clean-up-resources"></a>Rensa resurser
Ta bort THOMAS appen när de inte längre behövs. Om du vill göra det, väljer du menyn med tre punkter (...) till höger om appnamnet i applistan väljer **ta bort**. I popup-fönstret **ta bort appen?** väljer **Ok**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hämta utterance förutsägelse med slutpunkten fråga](luis-get-started-cs-get-intent.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions
[LuisFeatures]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-feature
[LuisSampleApp]: https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/phrase_list/interchangeable/luis-app-before-phrase-list.json
