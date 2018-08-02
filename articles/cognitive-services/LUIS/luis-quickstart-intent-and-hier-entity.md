---
title: Självstudie om att skapa en LUIS-app för att hämta platsdata – Azure | Microsoft Docs
description: I den här självstudien skapar du en enkel LUIS-app med hjälp av avsikter och en hierarkisk entitet för att extrahera data.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 07/26/2018
ms.author: diberry
ms.openlocfilehash: f4e03271f45c29ed2556256346e29c297be563cc
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2018
ms.locfileid: "39345366"
---
# <a name="tutorial-5-add-hierarchical-entity"></a>Självstudie: 5. Lägg till hierarkisk entitet
I den här självstudien skapar du en app som visar hur det går till att hitta relaterade datadelar baserat på kontext. 

<!-- green checkmark -->
> [!div class="checklist"]
> * Förstå hierarkiska entiteter och underordnade element med kontextuell inlärning 
> * Använda LUIS-appen i HR-domänen (Human Resources) 
> * Lägg till platshierarkisk entitet med ursprung och underordnade destinationselement
> * Träna och publicera app
> * Skicka en fråga till appens slutpunkt för att se LUIS JSON-svar inklusive hierarkiska underordnade element 

[!include[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>Innan du börjar
Om du inte har appen Human Resources (Personalfrågor) från självstudien om [list entities](luis-quickstart-intent-and-list-entity.md) (listentiteter) ska du [importera](luis-how-to-start-new-app.md#import-new-app) JSON till en ny app på [LUIS-webbplatsen](luis-reference-regions.md#luis-website). Importeringsappen finns på [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-list-HumanResources.json)-GitHub-lagringsplatsen.

Om du vill behålla den ursprungliga Human Resources-appen (Personalfrågor) klonar du versionen på sidan [Settings](luis-how-to-manage-versions.md#clone-a-version) (Inställningar) och ger den namnet `hier`. Kloning är ett bra sätt att prova på olika LUIS-funktioner utan att påverka originalversionen. 

## <a name="purpose-of-the-app-with-this-entity"></a>Syftet med appen med den här entiteten
Den här appen bestämmer var en medarbetare ska flyttas från (byggnad och kontor) och till (byggnad och kontor). Den använder den hierarkiska entiteten för att urskilja platser i yttrandet. 

Den hierarkiska entiteten passar bra för den här typen av data eftersom följande gäller för de två datadelarna:

* De är relaterade till varandra i yttrandet.
* Specifika ord används för att ange varje plats. Exempel på sådana ord: från/till, lämnar/ska till, bort från/till.
* Båda platserna förekommer ofta i samma yttrande. 

Syftet med den **hierarkiska** entiteten är att hitta relaterade data inom yttrandet baserat på kontext. Ta följande yttrande som exempel:

```JSON
mv Jill Jones from a-2349 to b-1298
```
I yttrandet finns två angivna platser: `a-2349` och `b-1298`. Anta att bokstaven motsvarar ett byggnadsnamn och numret indikerar kontoret i byggnaden. Båda dessa är underordnade den hierarkiska entiteten `Locations` eftersom båda datadelar behöver extraheras från yttrandet och de är relaterade till varandra. 
 
Om endast ett underordnat element (ursprung eller destination) för en hierarkisk entitet finns extraheras det ändå. Det är inte nödvändigt att hitta alla underordnade element för att endast ett eller några element ska kunna extraheras. 

## <a name="remove-prebuilt-number-entity-from-app"></a>Ta bort fördefinierad nummerentitet från appen
Om du vill se hela yttrandet och märka de underordnade delarna i hierarkin kan du ta bort den fördefinierade nummerentiteten tillfälligt.

1. Kontrollera att Human Resources-appen (Personalfrågor) finns i avsnittet **Build** (Skapa) i LUIS. Du kan ändra till det här avsnittet genom att välja **Build** (Skapa) i menyraden längst upp till höger. 

2. Välj **Entities** (Entiteter) på den vänstra menyn.

3. Välj ellipsknappen (***...***) till höger om nummerentiteten i listan. Välj **Ta bort**. 

    [ ![Skärmbild på LUIS-appen med sidan för entitetslistan, och borttagningsknappen för den fördefinierade nummerentiteten markerad](./media/luis-quickstart-intent-and-hier-entity/hr-delete-number-prebuilt.png)](./media/luis-quickstart-intent-and-hier-entity/hr-delete-number-prebuilt.png#lightbox)


## <a name="add-utterances-to-moveemployee-intent"></a>Lägga till yttranden till avsikten MoveEmployee

1. Välj **Intents** (Avsikter) på den vänstra menyn.

2. Välj **MoveEmployee** i listan med avsikter.

    [ ![Skärmbild på LUIS-appen med avsikten MoveEmployee markerad på menyn till vänster](./media/luis-quickstart-intent-and-hier-entity/hr-intents-list-moveemployee.png)](./media/luis-quickstart-intent-and-hier-entity/hr-intents-list-moveemployee.png#lightbox)

3. Lägg till följande exempelyttranden:

    |Exempel på yttranden|
    |--|
    |Flytta John W. Smith **till** a-2345|
    |Hänvisa Jill Jones **till** b-3499|
    |Ordna flytt för x23456 **från** hh-2345 **till** e-0234|
    |Påbörja pappersarbetet för att x12345 **ska flytta från** a-3459 **till** f-34567|
    |Flytta 425-555-0000 **från** g-2323 **till** hh-2345|

    [ ![Skärmbild på LUIS-appen med nya yttranden i avsikten MoveEmployee](./media/luis-quickstart-intent-and-hier-entity/hr-enter-utterances.png)](./media/luis-quickstart-intent-and-hier-entity/hr-enter-utterances.png#lightbox)

    I enlighet med självstudien om [listentitet](luis-quickstart-intent-and-list-entity.md) kan en medarbetare anges med namn, e-postadress, telefonanknytning, mobilnummer eller amerikanskt socialförsäkringsnummer. Dessa medarbetarnummer används i yttranden. Föregående exempelyttranden innehåller olika sätt att ange ursprungs- och målplatser, markerade i fetstil. I några av yttrandena ingår endast målplatser. Detta hjälper LUIS-appen att förstå hur dessa platser placeras i yttranden när ursprungsplats inte anges.     

## <a name="create-a-location-entity"></a>Skapa en platsentitet
LUIS-appen behöver förstå vad en plats är genom att märka ursprungs- och målplatser i yttranden. Om du behöver se ett yttrande i tokenvyn (obearbetad) väljer du växlingsknappen i fältet ovanför yttrandena som är märkta **Entities View** (Entitetsvy). När du byter läge är kontrollen märkt **Tokens View** (Tokenvy).

1. I yttrandet `Displace 425-555-0000 away from g-2323 toward hh-2345` väljer du ordet `g-2323`. En listrutemeny visas med en textruta längst upp. Ange entitetsnamnet `Locations` i textrutan och välj sedan **Create new entity** (Skapa ny entitet) i listrutan. 

    [![](media/luis-quickstart-intent-and-hier-entity/hr-create-new-entity-1.png "Skärmbild på en ny entitet som skapas på avsiktssidan")](media/luis-quickstart-intent-and-hier-entity/hr-create-new-entity-1.png#lightbox)

2. I popup-fönstret väljer du entitetstypen **Hierarchical** (Hierarkisk) med `Origin` och `Destination` som underordnade entiteter. Välj **Done** (Klar).

    ![](media/luis-quickstart-intent-and-hier-entity/hr-create-new-entity-2.png "Skärmbild på dialogruta för att skapa entitet för den nya entiteten Location (Plats)")

3. Etiketten för `g-2323` har märkts som `Locations` eftersom LUIS inte känner till om termen var ursprunget, målet, eller ingetdera. Välj `g-2323`, välj sedan **Locations** (Platser) och följ därefter menyn till höger och välj `Origin`.

    [![](media/luis-quickstart-intent-and-hier-entity/hr-label-entity.png "Skärmbild på dialogruta för att märka entiteter och ändra underordnad platsentitet")](media/luis-quickstart-intent-and-hier-entity/hr-label-entity.png#lightbox)

5. Märk de andra platserna i alla andra yttranden genom att välja byggnad och kontor i yttrandet, välja Locations (Platser), följa menyn till höger och välja `Origin` eller `Destination`. När alla platser är märkta börjar yttrandena i **Tokens View** (Tokenvy) att få ett mönster. 

    [![](media/luis-quickstart-intent-and-hier-entity/hr-entities-labeled.png "Skärmbild på entiteten Locations (Platser) märkt i yttranden")](media/luis-quickstart-intent-and-hier-entity/hr-entities-labeled.png#lightbox)

## <a name="add-prebuilt-number-entity-to-app"></a>Lägga till fördefinierad nummerentitet i appen
Lägg till den fördefinierade nummerentiteten i appen.

1. Välj **Entities** (Entiteter) på den vänstra navigeringsmenyn.

2. Välj knappen **Manage prebuilt entities** (Hantera fördefinierade entiteter).

    [ ![Skärmbild på listan Entities (Entiteter) med Manage prebuilt entities (Hantera fördefinierade entiteter) markerad](./media/luis-quickstart-intent-and-hier-entity/hr-manage-prebuilt-button.png)](./media/luis-quickstart-intent-and-hier-entity/hr-manage-prebuilt-button.png#lightbox)

3. Välj **number** (nummer) i listan över fördefinierade entiteter och sedan **Done** (Klar).

    ![Skärmbild på dialogrutan för fördefinierade entiteter med nummer markerat](./media/luis-quickstart-intent-and-hier-entity/hr-add-number-back-ddl.png)

## <a name="train-the-luis-app"></a>Träna LUIS-appen
LUIS känner inte till ändringarna av avsikterna och entiteterna (modellen) förrän den tränas. 

1. Längst uppe till höger på LUIS-webbplatsen väljer du knappen **Train** (Träna).

    ![Träna appen](./media/luis-quickstart-intent-and-hier-entity/train-button.png)

2. Träningen är klar när du ser det gröna statusfältet som bekräftar att det är klart längst upp på webbplatsen.

    ![Träningen är klar](./media/luis-quickstart-intent-and-hier-entity/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publicera appen för att få slutpunkts-URL

[!include[LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint-with-a-different-utterance"></a>Skicka fråga till slutpunkten med ett annat yttrande

1. [!include[LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]


2. Gå till slutet av webbadressen i adressfältet och ange `Please relocation jill-jones@mycompany.com from x-2345 to g-23456`. Den sista frågesträngsparametern är `q`, yttrande**frågan**. Det här yttrandet är inte samma som någon av de märkta yttrandena. Därför är det ett bra test och bör returnera avsikten `MoveEmployee` med den hierarkiska entiteten extraherad.

  ```JSON
  {
    "query": "Please relocation jill-jones@mycompany.com from x-2345 to g-23456",
    "topScoringIntent": {
      "intent": "MoveEmployee",
      "score": 0.9966052
    },
    "intents": [
      {
        "intent": "MoveEmployee",
        "score": 0.9966052
      },
      {
        "intent": "Utilities.Stop",
        "score": 0.0325253047
      },
      {
        "intent": "FindForm",
        "score": 0.006137873
      },
      {
        "intent": "GetJobInformation",
        "score": 0.00462633232
      },
      {
        "intent": "Utilities.StartOver",
        "score": 0.00415637763
      },
      {
        "intent": "ApplyForJob",
        "score": 0.00382325822
      },
      {
        "intent": "Utilities.Help",
        "score": 0.00249120337
      },
      {
        "intent": "None",
        "score": 0.00130756292
      },
      {
        "intent": "Utilities.Cancel",
        "score": 0.00119622645
      },
      {
        "intent": "Utilities.Confirm",
        "score": 1.26910036E-05
      }
    ],
    "entities": [
      {
        "entity": "jill - jones @ mycompany . com",
        "type": "Employee",
        "startIndex": 18,
        "endIndex": 41,
        "resolution": {
          "values": [
            "Employee-45612"
          ]
        }
      },
      {
        "entity": "x - 2345",
        "type": "Locations::Origin",
        "startIndex": 48,
        "endIndex": 53,
        "score": 0.8520272
      },
      {
        "entity": "g - 23456",
        "type": "Locations::Destination",
        "startIndex": 58,
        "endIndex": 64,
        "score": 0.974032
      },
      {
        "entity": "-2345",
        "type": "builtin.number",
        "startIndex": 49,
        "endIndex": 53,
        "resolution": {
          "value": "-2345"
        }
      },
      {
        "entity": "-23456",
        "type": "builtin.number",
        "startIndex": 59,
        "endIndex": 64,
        "resolution": {
          "value": "-23456"
        }
      }
    ]
  }
  ```

## <a name="could-you-have-used-a-regular-expression-for-each-location"></a>Går det att använda ett reguljärt uttryck för varje plats?
Ja, skapa det reguljära uttrycket med ursprungs- och målroller och använd det i ett mönster.

Platserna i det här exemplet, som `a-1234`, har ett visst format med en eller två bokstäver följt av ett bindestreck, och sedan ett nummer med 4–5 tal. Dessa data kan beskrivas som en entitet för reguljära uttryck med en roll för varje plats. Roller är tillgängliga för mönster. Du kan skapa mönster baserade på dessa yttranden och sedan skapa ett reguljärt uttryck för platsformatet och lägga till det i mönster. <!-- Go to this tutorial to see how that is done -->

## <a name="what-has-this-luis-app-accomplished"></a>Vad har den här LUIS-appen åstadkommit?
Med hjälp av endast några få avsikter och en hierarkisk entitet har den här appen identifierat en frågeavsikt i naturligt språk och returnerat extraherade data. 

Din chattrobot har nu tillräckligt med information för att bestämma den primära åtgärden, `MoveEmployee`, och den platsinformation som hittades i yttrandet. 

## <a name="where-is-this-luis-data-used"></a>Var används dessa LUIS-data? 
LUIS är klar med den här begäran. Det anropande programmet, till exempel en chattrobot, kan använda topScoringIntent-resultatet och data från entiteten för att gå vidare. LUIS utför inte detta programmässiga arbete för roboten eller det anropande programmet. LUIS tar endast reda på vad användarens avsikt är. 

## <a name="clean-up-resources"></a>Rensa resurser
Ta bort LUIS-appen när den inte längre behövs. För att göra det väljer du ellipsknappen (***...***) till höger om appnamnet i applistan och väljer **Delete** (Ta bort). På popup-dialogrutan **Delete app?** (Ta bort appen?) väljer du **Ok**.

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"] 
> [Lär dig hur du lägger till en sammansatt entitet](luis-tutorial-composite-entity.md) 