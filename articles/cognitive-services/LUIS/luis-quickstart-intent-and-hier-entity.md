---
title: Självstudie om att skapa en LUIS-app för att hämta platsdata – Azure | Microsoft Docs
description: I den här självstudien skapar du en enkel LUIS-app med hjälp av avsikter och en hierarkisk entitet för att extrahera data.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: v-geberr
ms.openlocfilehash: 2547407126943161ba604fa2f5e80b9186cae57e
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266506"
---
# <a name="tutorial-create-app-that-uses-hierarchical-entity"></a>Självstudie: skapa app som använder hierarkisk entitet
I den här självstudien skapar du en app som visar hur det går till att hitta relaterade datadelar baserat på kontext. 

<!-- green checkmark -->
> [!div class="checklist"]
> * Förstå hierarkiska entiteter och underordnade element med kontextuell inlärning 
> * Skapa ny LUIS-app för resedomän med avsikten Bookflight
> * Lägga till avsikten _None_ (Ingen) och lägga till exempelyttranden
> * Lägg till platshierarkisk entitet med ursprung och underordnade destinationselement
> * Träna och publicera app
> * Skicka en fråga till appens slutpunkt för att se LUIS JSON-svar inklusive hierarkiska underordnade element 

För den här artikeln behöver du ett kostnadsfritt [LUIS-konto][LUIS] för att kunna redigera LUIS-programmet.

## <a name="purpose-of-the-app-with-this-entity"></a>Syftet med appen med den här entiteten
Den här bedömer om en användare vill boka en flygresa. Den använder den hierarkiska entiteten för att bestämma platser, ursprungsort och destinationsort inom användarens kontext. 

Den hierarkiska entiteten passar bra för den här typen av data eftersom följande gäller för de två datadelarna:

* Båda är platser, vilket vanligtvis uttrycks som orter eller flygplatskoder.
* Vanligtvis finns det unika ordval runt orden för att kunna avgöra vad som är ursprunget och vad som är målet. De här orden innefattar: till, mot, från, avgår.
* Båda platserna förekommer ofta i samma yttrande. 

Syftet med den **hierarkiska** entiteten är att hitta relaterade data inom yttrandet baserat på kontext. Ta följande yttrande som exempel:

```JSON
1 ticket from Seattle to Cairo`
```

I yttrandet finns två angivna platser. Den ena är ursprungsorten, Seattle, och den andra är destinationsorten, Kairo. Båda dessa orter är viktiga för att boka en flygresa. De skulle kunna hittas med hjälp av enkla entiteter, men de är relaterade till varandra och förekommer ofta i samma yttrande. Därför är det logiskt att gruppera båda två som underordnade element i en hierarkisk entitet, **”Location”** (Plats). 

Eftersom det gäller maskininlärda enheter behöver appen exempel på yttranden där ursprungsorten och destinationsorten är märkta. Det här lär LUIS var entiteterna finns i yttrandena, hur långa de är och vilka ord som finns runtom. 

## <a name="app-intents"></a>App-avsikter
Avsikterna är de kategorier av saker som användaren vill ha. Den här appen har två avsikter: BookFlight och None. Avsikten [None](luis-concept-intent.md#none-intent-is-fallback-for-app) (Ingen) har syftet att indikera allt som faller utanför appen.  

## <a name="hierarchical-entity-is-contextually-learned"></a>Hierarkisk entitet lärs kontextuellt 
Syftet med entiteten är att hitta och kategorisera delar av texten i yttrandet. En [hierarkisk](luis-concept-entity-types.md) entitet är en över-underordnad entitet baserad på användningens kontext. En person kan fastställa ursprungs- och destinationsorterna i ett yttrande baserat på användningen av `to` och `from`. Det här är ett exempel på kontextuell användning.  

För den här Travel-appen (Resor) extraherar LUIS ursprungs- och destinationsplatserna på ett sätt som gör att en standardbokning kan skapas och genomföras. I LUIS kan yttranden ha variationer, förkortningar och slang. 

Enkla exempel på yttranden från användare innefattar:

```
Book a flight to London for next Monday
2 tickets from Dallas to Dublin this weekend
Researve a seat from New York to Paris on the first of April
```

Förkortade versioner av eller slangvarianter på yttranden innefattar:

```
LHR tomorrow
SEA to NYC next Monday
LA to MCO spring break
```
 
Den hierarkiska enheten matchar ursprungs- och destinationsplatserna. Om endast ett underordnat element (ursprung eller destination) för en hierarkisk entitet finns extraheras det ändå. Det är inte nödvändigt att hitta alla underordnade element för att endast ett eller några element ska kunna extraheras. 

## <a name="what-luis-does"></a>What LUIS gör
När yttrandets avsikt och entiteter identifierats, [extraherats](luis-concept-data-extraction.md#list-entity-data) och returnerats i JSON från [slutpunkten](https://aka.ms/luis-endpoint-apis) är LUIS klar. Det anropande programmet eller chattroboten använder JSON-svaret och uppfyller begäran på det sätt som appen eller chattroboten har instruerats att göra. 

## <a name="create-a-new-app"></a>Skapa en ny app
1. Logga in på [LUIS-webbplatsen][LUIS]. Se till att logga in på den [region][LUIS-regions] där du behöver få LUIS-slutpunkterna publicerade.

2. På [LUIS-webbplatsen][LUIS] väljer du **Create new app** (Skapa ny app).  

    [![](media/luis-quickstart-intent-and-hier-entity/app-list.png "Skärmbild på sidan App lists (App-listor)")](media/luis-quickstart-intent-and-hier-entity/app-list.png#lightbox)

3. I popup-dialogrutan anger du namnet `MyTravelApp`. 

    [![](media/luis-quickstart-intent-and-hier-entity/create-new-app.png "Skärmbild på popup-dialogrutan Create new app (Skapa ny app)")](media/luis-quickstart-intent-and-hier-entity/create-new-app.png#lightbox)

4. När processen är klar visar appen sidan **Intents** (Avsikter) med avsikten **None** (Ingen). 

    [![](media/luis-quickstart-intent-and-hier-entity/intents-page-none-only.png "Skärmbild på Intents list (Avsiktslista) med endast avsikten None (Ingen)")](media/luis-quickstart-intent-and-hier-entity/intents-page-none-only.png#lightbox)

## <a name="create-a-new-intent"></a>Skapa en ny avsikt

1. På sidan **Intents** (Avsikter) väljer du **Create new intent** (Skapa ny avsikt). 

    [![](media/luis-quickstart-intent-and-hier-entity/create-new-intent-button.png "Skärmbild på Intents list (Avsiktslista) med knappen ”Create new intent” (Skapa ny avsikt) markerad")](media/luis-quickstart-intent-and-hier-entity/create-new-intent-button.png#lightbox)

2. Ange det nya avsiktsnamnet `BookFlight`. Den här avsikten ska väljas när en användare vill boka flygresor.

    Genom att skapa en avsikt skapar du den primära kategorin för information som du vill identifiera. Tack vare att kategorin får ett namn kan andra program som använder LUIS-frågeresultaten använda det kategorinamnet för att hitta ett lämpligt svar eller utföra lämpliga åtgärder. LUIS svarar inte på de här frågorna, utan identifierar bara vilken typ av information som det frågas om i naturligt språk. 

    [![](media/luis-quickstart-intent-and-hier-entity/create-new-intent.png "Skärmbild på popup-dialogrutan Create new intent (Skapa ny avsikt)")](media/luis-quickstart-intent-and-hier-entity/create-new-intent.png#lightbox)

3. Lägg till flera yttranden till avsikten `BookFlight` som du förväntar dig att en användare begär, till exempel:

    | Exempel på yttranden|
    |--|
    |Boka 2 flygresor från Seattle till Kairo nästa måndag|
    |Boka en biljett till London i morgon|
    |Reservera 4 biljetter från Paris till London 1 april|

    [![](media/luis-quickstart-intent-and-hier-entity/enter-utterances-on-intent.png "Skärmbild på yttranden som anges på sidan för BookFlight-avsikt")](media/luis-quickstart-intent-and-hier-entity/enter-utterances-on-intent.png#lightbox)

## <a name="add-utterances-to-none-intent"></a>Lägga till yttranden till avsikten None (Ingen)

LUIS-appen har för närvarande inga yttranden för avsikten **None** (Ingen). Den behöver yttranden som du inte vill att appen svarar på. Därför behöver den ha yttranden i avsikten **None** (Ingen). Lämna den inte tom. 

1. Välj **Intents** (Avsikter) på den vänstra panelen. 

    [![](media/luis-quickstart-intent-and-hier-entity/select-intents-from-bookflight-intent.png "Skärmbild på sidan för BookFlight-avsikt med knappen Intents (Avsikter) markerad")](media/luis-quickstart-intent-and-hier-entity/select-intents-from-bookflight-intent.png#lightbox)

2. Välj avsikten **None** (Ingen). Lägg till tre yttranden som din använda kan tänkas ange men som inte är relevanta för appen:

    | Exempel på yttranden|
    |--|
    |Avbryt!|
    |Hej då|
    |Vad är det som händer?|

## <a name="when-the-utterance-is-predicted-for-the-none-intent"></a>När yttrandet förväntas för avsikten None (Ingen)
I det LUIS-anropande programmet (till exempel en chattrobot) kan roboten när LUIS returnerar avsikten **None** (Ingen) för ett yttrande fråga om användaren vill avsluta konversationen. Roboten kan även ge fler anvisningar för att fortsätta konversationen om användaren inte vill avsluta den. 

Entiteter fungerar i avsikten **None** (Ingen). Om **None** (Ingen) är avsikten med högst poäng men en entitet som är väsentlig för chattroboten extraheras kan chattroboten följa upp med en fråga som fokuserar användarens avsikt. 

## <a name="create-a-location-entity-from-the-intent-page"></a>Skapa en platsentitet från sidan Intent (Avsikt)
Nu när de två avsikterna har yttranden behöver LUIS förstå vad en plats är för något. Gå tillbaka till avsikten `BookFlight` och etikettera (märk) ortnamnen i ett yttrande genom att följa stegen:

1. Gå tillbaka till avsikten `BookFlight` genom att välja **Intents** (Avsikter) på den vänstra panelen.

2. Välj `BookFlight` från listan över avsikter.

3. I yttrandet `Book 2 flights from Seattle to Cairo next Monday` väljer du ordet `Seattle`. En listrutemeny visas med en textruta längst upp att för skapa en ny entitet. Ange entitetsnamnet `Location` i textrutan och välj sedan **Create new entity** (Skapa ny entitet) i listrutan. 

    [![](media/luis-quickstart-intent-and-hier-entity/label-seattle-in-utterance.png "Skärmbild på sidan för BookFlight-avsikt där en ny entitet skapas från markerad text")](media/luis-quickstart-intent-and-hier-entity/label-seattle-in-utterance.png#lightbox)

4. I popup-fönstret väljer du entitetstypen **Hierarchical** (Hierarkisk) med `Origin` och `Destination` som underordnade entiteter. Välj **Done** (Klar).

    [![](media/luis-quickstart-intent-and-hier-entity/hier-entity-ddl.png "Skärmbild på popup-dialogruta för skapande av entitet för den nya entiteten Location (Plats)")](media/luis-quickstart-intent-and-hier-entity/hier-entity-ddl.png#lightbox)

    Etiketten för `Seattle` har märkts som `Location` eftersom LUIS inte känner till om termen var ursprunget, målet, eller ingetdera. Välj `Seattle`, välj sedan Location (Plats) och följ därefter menyn till höger och välj `Origin`.

5. Nu när entiteten har skapats och ett yttrande har märkts ska du märka de andra orterna genom att välja ortnamnet och sedan välja Location (Plats), och därefter följa menyn till höger och välja `Origin` eller `Destination`.

    [![](media/luis-quickstart-intent-and-hier-entity/label-destination-in-utterance.png "Skärmbild på entiteten Bookflight med yttrandetext markerad för entitetsmarkering")](media/luis-quickstart-intent-and-hier-entity/label-destination-in-utterance.png#lightbox)

## <a name="train-the-luis-app"></a>Träna LUIS-appen
LUIS känner inte till ändringarna av avsikterna och entiteterna (modellen) förrän den tränas. 

1. Längst uppe till höger på LUIS-webbplatsen väljer du knappen **Train** (Träna).

    ![Träna appen](./media/luis-quickstart-intent-and-hier-entity/train-button.png)

2. Träningen är klar när du ser det gröna statusfältet som bekräftar att det är klart längst upp på webbplatsen.

    ![Träningen är klar](./media/luis-quickstart-intent-and-hier-entity/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publicera appen för att få slutpunkts-URL
För att få en LUIS-förutsägelse i en chattrobot eller i ett annat program måste du publicera appen. 

1. Längst uppe till höger på LUIS-webbplatsen väljer du knappen **Publish** (Publicera). 

    [![](media/luis-quickstart-intent-and-hier-entity/publish.png "Skärmbild på avsikten Bookflight med knappen Publish (Publicera) markerad")](media/luis-quickstart-intent-and-hier-entity/publish.png#lightbox)

2. Välj platsen Production (Produktionsplats) och knappen **Publish** (Publicera).

    [![](media/luis-quickstart-intent-and-hier-entity/publish-to-production.png "Skärmbild på sidan Publish (Publicera) med knappen Publish to production slot (Publicera till produktionsplats) markerad")](media/luis-quickstart-intent-and-hier-entity/publish-to-production.png#lightbox)

3. Publiceringen är klar när du ser det gröna statusfältet som bekräftar att det är klart längst upp på webbplatsen.

## <a name="query-the-endpoint-with-a-different-utterance"></a>Skicka fråga till slutpunkten med ett annat yttrande
1. På sidan **Publish** (Publicera) väljer du länken **endpoint** (slutpunkt) längst ned på sidan. Den här åtgärden öppnar ett nytt webbläsarfönster med slutpunkts-URL i adressfältet. 

    [![](media/luis-quickstart-intent-and-hier-entity/publish-select-endpoint.png "Skärmbild på sidan Publish (Publicera) med slutpunkts-URL markerad")](media/luis-quickstart-intent-and-hier-entity/publish-select-endpoint.png#lightbox)

2. Gå till slutet av URL:en i adressen och ange `1 ticket to Portland on Friday`. Den sista frågesträngsparametern är `q`, yttrande**f**rågan. Det här yttrandet är inte samma som någon av de märkta yttrandena. Därför är det ett bra test och bör returnera avsikten `BookFlight` med den hierarkiska entiteten extraherad.

```
{
  "query": "1 ticket to Portland on Friday",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.9998226
  },
  "intents": [
    {
      "intent": "BookFlight",
      "score": 0.9998226
    },
    {
      "intent": "None",
      "score": 0.221926212
    }
  ],
  "entities": [
    {
      "entity": "portland",
      "type": "Location::Destination",
      "startIndex": 12,
      "endIndex": 19,
      "score": 0.564448953
    }
  ]
}
```

## <a name="what-has-this-luis-app-accomplished"></a>Vad har den här LUIS-appen åstadkommit?
Med hjälp av endast två avsikter och en hierarkisk entitet har den här appen identifierat en frågeavsikt i naturligt språk och returnerat extraherade data. 

Din chattrobot har nu tillräckligt med information för att bestämma den primära åtgärden, `BookFlight`, och den platsinformation som hittades i yttrandet. 

## <a name="where-is-this-luis-data-used"></a>Var används dessa LUIS-data? 
LUIS är klar med den här begäran. Det anropande programmet, till exempel en chattrobot, kan använda topScoringIntent-resultatet och data från entiteten för att gå vidare. LUIS utför inte detta programmässiga arbete för roboten eller det anropande programmet. LUIS tar endast reda på vad användarens avsikt är. 

## <a name="clean-up-resources"></a>Rensa resurser
Ta bort LUIS-appen när den inte längre behövs. För att göra det väljer du menyn med tre punkter (...) till höger om appnamnet i applistan och väljer **Delete** (Ta bort). På popup-dialogrutan **Delete app?** (Ta bort appen?) väljer du **Ok**.

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"] 
> [Lär dig hur du lägger till en listentitet](luis-quickstart-intent-and-list-entity.md) 

Lägg till **numret** [fördefinierad entitet](luis-how-to-add-entities.md#add-prebuilt-entity) för att extrahera numret. 

Lägg till **datetimeV2**, [en fördefinierad entitet](luis-how-to-add-entities.md#add-prebuilt-entity), för att extrahera datuminformationen.


<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[LUIS-regions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#publishing-regions
