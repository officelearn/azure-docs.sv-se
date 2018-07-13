---
title: Lägg till fördefinierade avsikter och entiteter för att extrahera data i Language Understanding – Azure | Microsoft Docs
description: Läs om hur du använder fördefinierade avsikter och entiteter för att extrahera olika typer av entitetsdata.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 06/29/2018
ms.author: v-geberr
ms.openlocfilehash: 075cb270641ca995eec95aa6aa8986c90077148a
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37112208"
---
# <a name="tutorial-2-add-prebuilt-intents-and-entities"></a>Självstudie: 2. Lägg till fördefinierade avsikter och entiteter
Lägg till fördefinierade avsikter och entiteter i Human Resources-självstudieappen för att snabbt få avsiktsförutsägelse och dataextrahering. 

I den här guiden får du lära dig att:

> [!div class="checklist"]
* lägga till fördefinierade avsikter 
* lägga till fördefinierade entiteter, datetimeV2 och nummer
* träna och publicera
* fråga LUIS och få svar på förutsägelser

## <a name="before-you-begin"></a>Innan du börjar
Om du inte har appen Human Resources (Personalfrågor) från den föregående självstudien [custom domain](luis-quickstart-intents-only.md) (anpassad domän) ska du [importera](create-new-app.md#import-new-app) JSON till en ny app på [LUIS-webbplatsen](luis-reference-regions.md#luis-website) från [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-intent-only-HumanResources.json)-GitHub-lagringsplatsen.

Om du vill behålla den ursprungliga Human Resources-appen (Personalfrågor) klonar du versionen på sidan [Settings](luis-how-to-manage-versions.md#clone-a-version) (Inställningar) och ger den namnet `prebuilts`. Kloning är ett bra sätt att prova på olika LUIS-funktioner utan att påverka originalversionen. 

## <a name="add-prebuilt-intents"></a>Lägga till fördefinierade avsikter
LUIS har flera fördefinierade avsikter som hjälper dig med vanliga användaravsikter.  

1. Se till att appen finns i avsnittet **Build** (Skapa) i LUIS. Du kan ändra till det här avsnittet genom att välja **Build** (Skapa) i menyraden längst upp till höger. 

    [ ![Skärmbild på LUIS-appen med Build (Skapa) markerat i navigeringsfältet längst upp till höger](./media/luis-tutorial-prebuilt-intents-and-entities/first-image.png)](./media/luis-tutorial-prebuilt-intents-and-entities/first-image.png#lightbox)

2. Välj **Add prebuilt domain intent** (Lägg till fördefinierad domänavsikt). 

    [ ![Skärmbild av sidan Intents (Avsikter) med knappen Add prebuilt domain intent (Lägg till fördefinierad domänavsikt) markerad](./media/luis-tutorial-prebuilt-intents-and-entities/add-prebuilt-domain-button.png) ](./media/luis-tutorial-prebuilt-intents-and-entities/add-prebuilt-domain-button.png#lightbox)

3. Sök efter `Utilities`. 

    [ ![Skärmbild av dialogrutan för fördefinierade avsikter med Utilities (Verktyg) i sökrutan](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png)](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png#lightbox)

4. Välj följande avsikter och välj **Done** (Klar): 

    * Utilities.Cancel (Verktyg.Avbryt)
    * Utilities.Confirm (Verktyg.Bekräfta)
    * Utilities.Help (Verktyg.Hjälp)
    * Utilities.StartOver (Verktyg.Börja_om)
    * Utilities.Stop (Verktyg.Stoppa)


## <a name="add-prebuilt-entities"></a>Lägg till fördefinierade entiteter
LUIS har flera fördefinierade entiteter för extrahering av data. 

1. Välj **Entities** (Entiteter) på den vänstra navigeringsmenyn.

    [ ![Skärmbild av listan Intents (Avsikter) med Entities (Entiteter) markerat i navigeringen till vänster](./media/luis-tutorial-prebuilt-intents-and-entities/entities-navigation.png)](./media/luis-tutorial-prebuilt-intents-and-entities/entities-navigation.png#lightbox)

2. Välj knappen **Manage prebuilt entities** (Hantera fördefinierade entiteter).

    [ ![Skärmbild på listan Entities (Entiteter) med Manage prebuilt entities (Hantera fördefinierade entiteter) markerad](./media/luis-tutorial-prebuilt-intents-and-entities/manage-prebuilt-entities-button.png)](./media/luis-tutorial-prebuilt-intents-and-entities/manage-prebuilt-entities-button.png#lightbox)

3. Välj **number** (nummer) och **datetimeV2** i listan över fördefinierade entiteter och sedan **Done** (Klar).

    ![Skärmbild på dialogrutan för fördefinierade entiteter med nummer markerat](./media/luis-tutorial-prebuilt-intents-and-entities/select-prebuilt-entities.png)

## <a name="train-and-publish-the-app"></a>Träna och publicera appen
1. Längst uppe till höger på LUIS-webbplatsen väljer du knappen **Train** (Träna). 

    ![Knappen Train (Träna)](./media/luis-quickstart-intents-only/train-button.png)

    Träningen är klar när du ser det gröna statusfältet som bekräftar att det är klart längst upp på webbplatsen.

    ![Statusfält för avslutad träning](./media/luis-quickstart-intents-only/trained.png)

2. Längst uppe till höger på LUIS-webbplatsen väljer du knappen **Publish** (Publicera) för att öppna sidan Publish (Publicera). 

3. Produktionsplatsen väljs som standard. Välj knappen **Publish** (Publicera) bredvid valet av produktionsplats. Publiceringen är klar när du ser det gröna statusfältet som bekräftar att det är klart längst upp på webbplatsen.

    Du behöver inte skapa en LUIS-slutpunktsnyckel i Azure-portalen innan du publicerar eller testar slutpunktens URL. Varje LUIS-app har en kostnadsfri startnyckel för redigering. Det ger dig obegränsad redigering och [några slutpunktsanrop](luis-boundaries.md#key-limits). 

## <a name="query-endpoint-with-an-utterance"></a>Skicka fråga till slutpunkten med ett yttrande
På sidan **Publish** (Publicera) väljer du länken **endpoint** (slutpunkt) längst ned på sidan. Den här åtgärden öppnar ett nytt webbläsarfönster med slutpunkts-URL i adressfältet. Gå till slutet av URL:en i adressen och ange `I want to cancel on March 3`. Den sista frågesträngsparametern är `q`, yttrande**frågan**. 

Resultatet förutsåg avsikten Utilities.Cancel (Verktyg.Avbryt) och extraherade datumet March 3 (3 mars) och numret 3. 

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

Det finns två värden för March 3 (3 mars) eftersom yttrande inte anger om March 3 (3 mars) är i dåtid eller framtid. Det är upp till programmet som anropar LUIS att göra ett antagande eller be om förtydligande, om det behövs. 

Genom att enkelt och snabbt lägga till fördefinierade avsikter och entiteter kan klientprogrammet lägga till konversationshantering och extrahera vanliga datatyper. 

## <a name="clean-up-resources"></a>Rensa resurser
Ta bort LUIS-appen när den inte längre behövs. Det gör du genom att välja **My apps** (Mina appar) på menyn längst upp till vänster. Välj menyn med tre punkter (...) till höger om appnamnet i applistan och välj **Delete** (Ta bort). På popup-dialogrutan **Delete app?** (Ta bort appen?) väljer du **Ok**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lägga till en entitet för reguljärt uttryck i appen](luis-quickstart-intents-regex-entity.md)

