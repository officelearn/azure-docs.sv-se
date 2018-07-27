---
title: Självstudie om att skapa en LUIS-app för att få exakt textmatchning i listdata – Azure | Microsoft Docs
description: I den här självstudien skapar du en enkel LUIS-app med hjälp av avsikter och listentiteter för att extrahera data i den här snabbstarten.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 06/29/2018
ms.author: diberry
ms.openlocfilehash: 4ba2ba5d947a112f780579bf4b31ba38cb26ae03
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2018
ms.locfileid: "39222978"
---
# <a name="tutorial-4-add-list-entity"></a>Självstudie: 4. Lägg till listentitet
I den här självstudien skapar du en app som visar hur det går till att hämta data som matchar en fördefinierad lista. 

<!-- green checkmark -->
> [!div class="checklist"]
> * Förstå listentiteter 
> * Skapa en ny LUIS-app för personalfrågedomänen med avsikten MoveEmployee
> * Lägga till listentitet för att extrahera medarbetare från yttrande
> * Träna och publicera app
> * Skicka en fråga till appens slutpunkt för att se LUIS JSON-svar

För den här artikeln behöver du ett kostnadsfritt [LUIS-konto](luis-reference-regions.md#luis-website) för att kunna redigera LUIS-programmet.

## <a name="before-you-begin"></a>Innan du börjar
Om du inte har appen Human Resources (Personalfrågor) från självstudien om [regex-entiteten](luis-quickstart-intents-regex-entity.md) ska du [importera](luis-how-to-start-new-app.md#import-new-app) JSON till en ny app på [LUIS-webbplatsen](luis-reference-regions.md#luis-website). Importeringsappen finns på [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-regex-HumanResources.json)-GitHub-lagringsplatsen.

Om du vill behålla den ursprungliga Human Resources-appen (Personalfrågor) klonar du versionen på sidan [Settings](luis-how-to-manage-versions.md#clone-a-version) (Inställningar) och ger den namnet `list`. Kloning är ett bra sätt att prova på olika LUIS-funktioner utan att påverka originalversionen. 

## <a name="purpose-of-the-list-entity"></a>Syftet med listentiteten
Den här appen förutsäger yttranden om att flytta en medarbetare från en byggnad till en annan. Appen använder en listentitet för att extrahera en medarbetare. Medarbetaren kan refereras till med hjälp av namn, telefonnummer, e-postadress eller amerikanskt socialförsäkringsnummer. 

En listentitet kan innehålla flera objekt med synonymer för varje objekt. I små till medelstora företag används en listentitet till att extrahera information om medarbetare. 

Det kanoniska namnet för varje objekt är medarbetarens nummer. Exempel på synonymer för den här domänen: 

|Synonymsyfte|Synonymvärde|
|--|--|
|Namn|John W. Smith|
|E-postadress|john.w.smith@mycompany.com|
|Telefonanknytning|x12345|
|Personligt mobilnummer|425-555-1212|
|Amerikanskt socialförsäkringsnummer|123-45-6789|

En listentitet är ett bra alternativ för den här typen av data när:

* Datavärdena är en känd uppsättning.
* Uppsättningen inte överskrider de högsta [gränserna](luis-boundaries.md) för LUIS för den här entitetstypen.
* Texten i yttrandet stämmer exakt med ett synonym. 

LUIS extraherar medarbetare på ett sätt så att en standardorder för att flytta medarbetare kan skapas av klientprogrammet.
<!--
## Example utterances
Simple example utterances for a `MoveEmployee` inent:

```
move John W. Smith from B-1234 to H-4452
mv john.w.smith@mycompany from office b-1234 to office h-4452

```
-->

## <a name="add-moveemployee-intent"></a>Lägga till avsikten MoveEmployee

1. Kontrollera att Human Resources-appen (Personalfrågor) finns i avsnittet **Build** (Skapa) i LUIS. Du kan ändra till det här avsnittet genom att välja **Build** (Skapa) i menyraden längst upp till höger. 

    [ ![Skärmbild på LUIS-app med Build (Skapa) markerat i navigeringsfältet längst upp till höger](./media/luis-quickstart-intent-and-list-entity/hr-first-image.png)](./media/luis-quickstart-intent-and-list-entity/hr-first-image.png#lightbox)

2. Välj **Create new intent** (Skapa ny avsikt). 

    [ ![Skärmbild på sidan Intents (Avsikter) med knappen ”Create new intent” (Skapa ny avsikt) markerad](./media/luis-quickstart-intent-and-list-entity/hr-create-new-intent-button.png) ](./media/luis-quickstart-intent-and-list-entity/hr-create-new-intent-button.png#lightbox)

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

## <a name="create-an-employee-list-entity"></a>Skapa en listentitet för medarbetare
Avsikten **MoveEmployee** innehåller nu yttranden, och LUIS behöver förstå vad en medarbetare är. 

1. Välj **Entities** (Entiteter) på den vänstra panelen.

    [ ![Skärmbild på sidan Intent (Avsikt) med knappen Entities (Entiteter) markerad i navigeringen till vänster](./media/luis-quickstart-intent-and-list-entity/hr-select-entity-button.png) ](./media/luis-quickstart-intent-and-list-entity/hr-select-entity-button.png#lightbox)

2. Välj **Create new entity** (Skapa ny entitet).

    [ ![Skärmbild på sidan Entities (Entiteter) med alternativet Create new entity (Skapa ny entitet) markerat](./media/luis-quickstart-intent-and-list-entity/hr-create-new-entity-button.png) ](./media/luis-quickstart-intent-and-list-entity/hr-create-new-entity-button.png#lightbox)

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

## <a name="train-the-luis-app"></a>Träna LUIS-appen
LUIS känner inte till ändringarna av avsikterna och entiteterna (modellen) förrän den tränas. 

1. Längst uppe till höger på LUIS-webbplatsen väljer du knappen **Train** (Träna).

    ![Träna appen](./media/luis-quickstart-intent-and-list-entity/train-button.png)

2. Träningen är klar när du ser det gröna statusfältet som bekräftar att det är klart längst upp på webbplatsen.

    ![Träningen är klar](./media/luis-quickstart-intent-and-list-entity/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publicera appen för att få slutpunkts-URL
För att få en LUIS-förutsägelse i en chattrobot eller i ett annat program måste du publicera appen. 

1. Längst uppe till höger på LUIS-webbplatsen väljer du knappen **Publish** (Publicera). 

    [![](media/luis-quickstart-intent-and-list-entity/publish.png "Skärmbild på publiceringsknapp som väljs")](media/luis-quickstart-intent-and-list-entity/publish.png#lightbox)

2. Välj platsen Production (Produktionsplats) och knappen **Publish** (Publicera). 

    [![](media/luis-quickstart-intent-and-list-entity/publish-to-production.png "Skärmbild på knappen ”Publish to production slot” (Publicera till produktionsplats) som väljs")](media/luis-quickstart-intent-and-list-entity/publish-to-production.png#lightbox)

3. Publiceringen är klar när du ser det gröna statusfältet som bekräftar att det är klart längst upp på webbplatsen.

## <a name="query-the-endpoint-with-a-different-utterance"></a>Skicka fråga till slutpunkten med ett annat yttrande
1. På sidan **Publish** (Publicera) väljer du länken **endpoint** (slutpunkt) längst ned på sidan. Den här åtgärden öppnar ett nytt webbläsarfönster med slutpunkts-URL i adressfältet. 

    [![](media/luis-quickstart-intent-and-list-entity/publish-select-endpoint.png "Skärmbild på slutpunkts-URL på sidan Publish (Publicera)")](media/luis-quickstart-intent-and-list-entity/publish-select-endpoint.png#lightbox)

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

## <a name="where-is-the-natural-language-processing-in-the-list-entity"></a>Var är bearbetningen av naturligt språk i listentiteten? 
Eftersom listentiteten är en exakt matchning förlitar den sig inte på bearbetning av naturligt språk (eller maskininlärning). LUIS använder bearbetning av naturligt språk (eller maskininlärning) för att välja rätt avsikt med högst poäng. Dessutom kan ett yttrande vara en blandning av mer än en entitet eller mer än en typ av entitet. Varje yttrande bearbetas för alla entiteter i appen, inklusive entiteter för bearbetning av naturligt språk (eller maskininlärning).

## <a name="what-has-this-luis-app-accomplished"></a>Vad har den här LUIS-appen åstadkommit?
Med hjälp av en listentitet har appen extraherat rätt medarbetare. 

Din chattrobot har nu tillräckligt med information för att bestämma den primära åtgärden, `MoveEmployee`, och vilken medarbetare som ska flyttas. 

## <a name="where-is-this-luis-data-used"></a>Var används dessa LUIS-data? 
LUIS är klar med den här begäran. Det anropande programmet, till exempel en chattrobot, kan använda topScoringIntent-resultatet och data från entiteten för att gå vidare. LUIS utför inte detta programmässiga arbete för roboten eller det anropande programmet. LUIS tar endast reda på vad användarens avsikt är. 

## <a name="clean-up-resources"></a>Rensa resurser
Ta bort LUIS-appen när den inte längre behövs. Välj **My apps** (Mina appar) i menyn längst upp till vänster. Välj ellipsen (***...***) till höger om appnamnet i applistan och välj **Delete** (Ta bort). På popup-dialogrutan **Delete app?** (Ta bort appen?) väljer du **Ok**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lägg till en hierarkisk enhet i appen](luis-quickstart-intent-and-hier-entity.md)

