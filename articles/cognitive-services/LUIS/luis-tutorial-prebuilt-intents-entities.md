---
title: Lägga till färdiga avsikter och enheter för att extrahera gemensamma data i språk förstå - Azure | Microsoft Docs
description: Lär dig hur du använder fördefinierade avsikter och enheter för att extrahera olika typer av entitetsdata.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: article
ms.date: 06/11/2018
ms.author: v-geberr
ms.openlocfilehash: 20950ced66497fb0dc96365975b37f244f677ce3
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266387"
---
# <a name="use-prebuilt-intents-and-entities-to-handle-common-intents-and-data"></a>Använda fördefinierade avsikter och enheter för att hantera vanliga avsikter och data
Lägger till fördefinierade avsikter och entiteter för personalavdelningen quickstart app att snabbt få avsiktshantering extrahering av prognoser och data. 

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
* Lägg till fördefinierade avsikter 
* Lägg till fördefinierade entiteter datetimeV2 och nummer
* Träna och publicera
* Fråga THOMAS och förutsägelse svar

## <a name="before-you-begin"></a>Innan du börjar
Om du inte har appen personal från den [domänen](luis-quickstart-intents-only.md) Snabbstart, [importera](create-new-app.md#import-new-app) JSON till en ny app i den [THOMAS] [ LUIS] webbplats , från den [THOMAS prover](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-intent-only-HumanResources.json) Github-lagringsplatsen.

Om du vill behålla ursprungliga personal appen klona versionen på den [inställningar](luis-how-to-manage-versions.md#clone-a-version) sidan och ge den namnet `prebuilts`. Kloningen är ett bra sätt att spela med olika THOMAS funktioner utan att påverka den ursprungliga versionen. 

## <a name="add-prebuilt-intents"></a>Lägg till fördefinierade avsikter
THOMAS innehåller flera fördefinierade avsikter att hjälpa med vanliga användare avsikter.  

1. Kontrollera att din app finns i den **skapa** avsnitt i THOMAS. Du kan ändra till det här avsnittet genom att välja **skapa** Högerklicka menyfältet högst upp. 

    [ ![Skärmbild av THOMAS app med Build hightlighted i övre, höger navigeringsfält](./media/luis-tutorial-prebuilt-intents-and-entities/first-image.png)](./media/luis-tutorial-prebuilt-intents-and-entities/first-image.png#lightbox)

2. Välj **lägga till färdiga domän avsikt**. 

    [ ![Skärmbild av Intents sidan med knappen Lägg till fördefinierade domän avsiktshantering markerat](./media/luis-tutorial-prebuilt-intents-and-entities/add-prebuilt-domain-button.png) ](./media/luis-tutorial-prebuilt-intents-and-entities/add-prebuilt-domain-button.png#lightbox)

3. Sök efter `Utilities`. 

    [ ![Skärmbild av dialogrutan färdiga avsikter med verktyg i sökrutan](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png)](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png#lightbox)

4. Välj följande avsikter och **klar**: 

    * Utilities.Cancel
    * Utilities.Confirm
    * Utilities.Help
    * Utilities.Stop
    * Utilities.StartOver

## <a name="add-prebuilt-entities"></a>Lägg till fördefinierade entiteter
THOMAS innehåller flera fördefinierade enheter för vanliga extrahering av data. 

1. Välj **entiteter** från den vänstra navigeringsmenyn.

    [ ![Skärmbild av Intents listan med enheter som är markerade i navigeringen till vänster](./media/luis-tutorial-prebuilt-intents-and-entities/entities-navigation.png)](./media/luis-tutorial-prebuilt-intents-and-entities/entities-navigation.png#lightbox)

2. Välj **hantera färdiga entiteter** knappen.

    [ ![Skärmbild av entiteter lista med fördefinierade entiteter markerat hantera](./media/luis-tutorial-prebuilt-intents-and-entities/manage-prebuilt-entities-button.png)](./media/luis-tutorial-prebuilt-intents-and-entities/manage-prebuilt-entities-button.png#lightbox)

3. Välj **nummer** och **datetimeV2** från listan med fördefinierade entiteter Välj **klar**.

    ![Skärmbild av antalet Välj i dialogrutan för färdiga entiteter](./media/luis-tutorial-prebuilt-intents-and-entities/select-prebuilt-entities.png)

## <a name="train-and-publish-the-app"></a>Träna och publicera appen
1. I upp till höger på THOMAS webbplatsen, väljer du den **Train** knappen. 

    ![Train-knappen](./media/luis-quickstart-intents-only/train-button.png)

    Utbildning är klar när du ser grön statusfältet längst upp på webbplatsen bekräftar lyckades.

    ![Utbildade statusfältet](./media/luis-quickstart-intents-only/trained.png)

2. I övre högra sidan av webbplatsen THOMAS väljer den **publicera** knappen för att öppna sidan Publicera. Produktionsplatsen väljs som standard. Välj den **publicera** knappen av produktion fack val. Publiceringen är klar när du ser grön statusfältet längst upp på webbplatsen bekräftar lyckades.

    Du behöver inte skapa en THOMAS nyckel i Azure-portalen innan du publicerar eller innan du testar slutpunkts-URL. Varje THOMAS app har en kostnadsfri starter för redigering. Den ger dig obegränsad redigering och en [några endpoint träffar](luis-boundaries.md#key-limits). 

## <a name="query-endpoint-with-an-utterance"></a>Frågan slutpunkt med ett utterance
På den **publicera** väljer den **endpoint** länken längst ned på sidan. Den här åtgärden öppnar ett nytt webbläsarfönster med slutpunkts-URL i adressfältet. Gå till slutet av URL-Adressen i adressen och ange `I want to cancel on March 3`. Den sista parametern för frågesträngen är `q`, utterance **frågan**. 

Resultatet förutsade Utilities.Cancel avsikten och extraheras datumet för 3 mars och talet 3. 

    ```
    {
      "query": "I want to cancel on March 3",
      "topScoringIntent": {
        "intent": "Utilities.Cancel",
        "score": 0.7818295
      },
      "intents": [
        {
          "intent": "Utilities.Cancel",
          "score": 0.7818295
        },
        {
          "intent": "ApplyForJob",
          "score": 0.0237864349
        },
        {
          "intent": "GetJobInformation",
          "score": 0.017576348
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.0130122062
        },
        {
          "intent": "Utilities.Help",
          "score": 0.006731322
        },
        {
          "intent": "None",
          "score": 0.00524190161
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.004912514
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.00092950504
        }
      ],
      "entities": [
        {
          "entity": "march 3",
          "type": "builtin.datetimeV2.date",
          "startIndex": 20,
          "endIndex": 26,
          "resolution": {
            "values": [
              {
                "timex": "XXXX-03-03",
                "type": "date",
                "value": "2018-03-03"
              },
              {
                "timex": "XXXX-03-03",
                "type": "date",
                "value": "2019-03-03"
              }
            ]
          }
        },
        {
          "entity": "3",
          "type": "builtin.number",
          "startIndex": 26,
          "endIndex": 26,
          "resolution": {
            "value": "3"
          }
        }
      ]
    }
    ```

Snabbt och enkelt lägger du till fördefinierade avsikter och entiteter, klientprogrammet Lägg till konversationen hantering och extrahera vanliga datatyper. 

## <a name="next-steps"></a>Nästa steg

[Mer information om entiteter](luis-concept-entity-types.md). 

<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[LUIS-regions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#publishing-regions
