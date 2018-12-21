---
title: Mönsterroller
titleSuffix: Azure Cognitive Services
description: Använd ett mönster för att extrahera data från ett välformaterat mallyttrande. Mallyttranden använder en enkel entitet och roller för att extrahera relaterade data, till exempel ursprungsplatsen och målplatsen.
ms.custom: seodec18
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: b6d800705509edc31b410d1e9cd30f8b53702010
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53094414"
---
# <a name="tutorial-4-extract-contextually-related-patterns"></a>Självstudie 4: Extrahera sammanhangsbaserade mönster

I den här självstudien använder du ett mönster för att extrahera data från ett välformaterat mallyttrande. Mallyttranden använder en enkel entitet och roller för att extrahera relaterade data, till exempel ursprungsplatsen och målplatsen.  När du använder mönster behövs färre exempelyttranden för avsikten.

Syftet med roller är att extrahera kontextrelaterade entiteter i ett yttrande. I yttrandet `Move new employee Robert Williams from Sacramento and San Francisco`, är ursprungsstadens och destinationsstadens värden relaterade till varandra och använder vanliga språk för att ange varje plats. 


Namnet på den nya medarbetaren Billy Patterson finns inte på listentiteten för **medarbetare** ännu. Namnet på nya medarbetaren extraheras först för att skicka namnet till ett externt system för att skapa autentiseringsuppgifter för företaget. När företagets autentiseringsuppgifter skapas läggs medarbetarens autentiseringsuppgifter till i listentiteten **medarbetare**.

Den nya medarbetaren och familjen måste flyttas från sin nuvarande ort till en stad där det fiktiva företaget finns. Eftersom en ny medarbetare kan komma från en annan stad måste platserna identifieras. En fast lista, till exempel en listentitet, fungerar inte eftersom endast orter i listan skulle extraheras.

Rollnamnen som är associerade med ursprungs- och destinationsstäderna måste vara unika för alla entiteter. Ett enkelt sätt att kontrollera att rollerna är unika är att koppla dem till den innehållande entiteten via en namnstrategi. Entiteten **NewEmployeeRelocation** är en enkel entitet med två roller: **NewEmployeeReloOrigin** och **NewEmployeeReloDestination**. Relo står för flytt.

Eftersom exempelyttrandet `Move new employee Robert Williams from Sacramento and San Francisco` endast har maskininlärda entiteter är det viktigt att tillhandahålla tillräckligt med exempelyttranden för intentionen så att avsikterna identifieras.  

**Med mönster kan du ge färre exempelyttranden, men om entiteterna inte identifieras matchar inte mönstret.**

Om du har problem med enkel entitetsidentifiering eftersom det är ett namn, till exempel på en stad, kan du överväga att lägga till en fraslista med liknande värden. Detta gör det enklare att identifiera namnet på staden eftersom LUIS får ytterligare en signal om den typen av ord eller fraser. Fraslistor hjälper endast mönstret genom att hjälpa till med entitetsidentifieringen, vilket är nödvändigt för att mönstret ska matcha. 

**I den här självstudiekursen får du lära du dig att:**

> [!div class="checklist"]
> * Använda en befintlig självstudieapp
> * Skapa nya entiteter
> * Skapa ny avsikt
> * Träna
> * Publicera
> * Hämta avsikter och entiteter från en slutpunkt
> * Skapa mönster med roller
> * Skapa en fraslista över städer
> * Hämta avsikter och entiteter från en slutpunkt

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Använda en befintlig app
Fortsätt med appen du skapade i föregående självstudie med namnet **HumanResources**. 

Om du inte har appen HumanResources från föregående självstudie gör du så här:

1.  Ladda ned och spara [JSON-filen för appen](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-patterns-HumanResources-v2.json).

2. Importera JSON-koden till en ny app.

3. I avsnittet **Hantera** går du till fliken **Versioner**, klonar versionen och ger den namnet `roles`. Kloning är ett bra sätt att prova på olika LUIS-funktioner utan att påverka originalversionen. Eftersom versionsnamnet används i webbadressen får namnet inte innehålla några tecken som är ogiltiga i webbadresser.

## <a name="create-new-entities"></a>Skapa nya entiteter

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Välj **Entities** (Entiteter) i det vänstra navigeringsfönstret. 

3. Välj **Create new entity** (Skapa ny entitet).

4. I popup-fönstret anger du `NewEmployee` som en **enkel** entitet.

5. Välj **Create new entity** (Skapa ny entitet).

6. I popup-fönstret anger du `NewEmployeeRelocation` som en **enkel** entitet.

7. Välj **NewEmployeeRelocation** från listan över entiteter. 

8. Ange den första rollen som `NewEmployeeReloOrigin` och välj Retur.

9. Ange den andra rollen som `NewEmployeeReloDestination` och välj Retur.

## <a name="create-new-intent"></a>Skapa ny avsikt
Etiketteringen av entiteter i de här stegen kan vara enklare om du tar bort den fördefinierade entiteten keyPhrase innan du börjar och sedan lägger tillbaka den när du är klar med stegen i det här avsnittet. 

1. Välj **Intents** (Avsikter) i det vänstra navigeringsfönstret.

2. Välj **Create new intent** (Skapa ny avsikt). 

3. Ange `NewEmployeeRelocationProcess` som avsiktsnamn i popup-dialogrutan.

4. Ange följande exempelyttranden som etiketterar nya entiteter. Enhet och rollvärdena är visas med fet stil. Kom ihåg att växla till **tokenvyn** om det är lättare att etikettera texten. 

    Du anger inte entitetens roll när du etiketterar avsikten. Du gör det senare när du skapar mönstret. 

    |Yttrande|NewEmployee|NewEmployeeRelocation|
    |--|--|--|
    |Flytta **Bob Jones** från **Seattle** till **Los Colinas**|Bob Jones|Seattle, Los Colinas|
    |Flytta **Dave C. Cooper** från **Redmond** till **New York City**|Dave C. Cooper|Redmond, New York City|
    |Flytta **Jim Paul Smith** från **Toronto** till **West Vancouver**|Jim Paul Smith|Toronto, West Vancouver|
    |Flytta **J. Benson** från **Boston** till **Staines-upon-Thames**|J. Benson|Boston, Staines-upon-Thames|
    |Flytta **Travis ”Trav” Hinton** från **Castelo Branco** till **Orlando**|Travis ”Trav” Hinton|Castelo Branco Orlando|
    |Flytta **Trevor Nottington III** från **Aranda de Duero** till **Boise**|Trevor Nottington III|Aranda de Duero, Boise|
    |Flytta **Dr. Greg Williams** från **Orlando** till **Ellicott City**|Dr. Greg Williams|Orlando, Ellicott City|
    |Flytta **Robert ”Bobby” Gregson** från **Kansas City** till **San Juan Capistrano**|Robert ”Bobby” Gregson|Kansas City, San Juan Capistrano|
    |Flytta **Patti Owens** från **Bellevue** till **Rockford**|Patti Owens|Bellevue, Rockford|
    |Flytta **Johanna Bartlet** från **Toscana** till **Santa Fe**|Johanna Bartlet|Toscana, Santa Fe|

    Medarbetarens namn har en blandning av prefix, ordräkning, syntax och suffix. Det är viktigt för LUIS för att förstå variationerna av ett nytt medarbetarnamn. Stadsnamnen har också en mängd olika ordräkningar och syntax. Det här variationerna är viktiga för att lära LUIS hur entiteterna kan visas i en användares yttrande. 
    
    Om någon entitet hade haft samma ordräkning och inga andra varianter skulle du lära LUIS att den här entiteten bara har den ordräkningen och inga andra variationer. LUIS skulle inte kunna förutse en bredare uppsättning variationer korrekt eftersom det inte visades. 

    Om du har tagit bort entiteten keyPhrase lägger du till den i appen igen nu.

## <a name="train"></a>Träna

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Publicera

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Hämta avsikter och entiteter från slutpunkten

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

2. Gå till slutet av URL:en i adressen och ange `Move Wayne Berry from Miami to Mount Vernon`. Den sista frågesträngsparametern är `q`, yttrande**frågan**. 

    ```json
    {
      "query": "Move Wayne Berry from Newark to Columbus",
      "topScoringIntent": {
        "intent": "NewEmployeeRelocationProcess",
        "score": 0.514479756
      },
      "intents": [
        {
          "intent": "NewEmployeeRelocationProcess",
          "score": 0.514479756
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.017118983
        },
        {
          "intent": "MoveEmployee",
          "score": 0.009982505
        },
        {
          "intent": "GetJobInformation",
          "score": 0.008637771
        },
        {
          "intent": "ApplyForJob",
          "score": 0.007115978
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.006120186
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.00452428637
        },
        {
          "intent": "None",
          "score": 0.00400899537
        },
        {
          "intent": "OrgChart-Reports",
          "score": 0.00240071164
        },
        {
          "intent": "Utilities.Help",
          "score": 0.001770991
        },
        {
          "intent": "EmployeeFeedback",
          "score": 0.001697356
        },
        {
          "intent": "OrgChart-Manager",
          "score": 0.00168116146
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.00163952739
        },
        {
          "intent": "FindForm",
          "score": 0.00112958835
        }
      ],
      "entities": [
        {
          "entity": "wayne berry",
          "type": "NewEmployee",
          "startIndex": 5,
          "endIndex": 15,
          "score": 0.629158735
        },
        {
          "entity": "newark",
          "type": "NewEmployeeRelocation",
          "startIndex": 22,
          "endIndex": 27,
          "score": 0.638941
        }
      ]
    }  
    ```

Avsiktsresultatet är endast cirka 50 %. Om klientprogrammet kräver en hög siffra måste det åtgärdas. Entiteterna var inte heller förutsagda.

En av platserna har extraherats men inte den andra. 

Mönster förbättrar förutsägelseresultatet, men entiteterna måste vara korrekt förutsagda innan mönstret matchar yttrandet. 

## <a name="pattern-with-roles"></a>Mönster med roller

1. Välj **Skapa** i det övre navigeringsfältet.

2. Välj **Mönster** i det vänstra navigeringsfönstret.

3. Välj **NewEmployeeRelocationProcess** i listrutan **Select an intent** (Välj en avsikt). 

4. Ange följande mönster: `move {NewEmployee} from {NewEmployeeRelocation:NewEmployeeReloOrigin} to {NewEmployeeRelocation:NewEmployeeReloDestination}[.]`

    Om du tränar, publicera och frågar slutpunkten kan du bli besviken när du ser att entiteterna inte hittas. Alltså matchade inte mönstret och därför förbättrades inte förutsägelsen. Det här är en följd av att det inte finns tillräckligt med exempelyttranden med etiketterade entiteter. I stället för att lägga till fler exempel lägger du till en fraslista för att åtgärda problemet.

## <a name="cities-phrase-list"></a>Fraslista för städer
Städer, precis som namn på personer, är svåra på så sätt att de kan vara en blandning av ord och skiljetecken. Städerna i regionerna och världen är kända, så LUIS behöver en fraslista över städer för att börja lära sig. 

1. Välj **Fraslista** i avsnittet **Förbättra apprestanda** på den vänstra menyn. 

2. Namnge listan `Cities` och lägg till följande `values` för listan:

    |Värdena i fraslistan|
    |--|
    |Seattle|
    |San Diego|
    |New York City|
    |Los Angeles|
    |Portland|
    |Philadephia|
    |Miami|
    |Dallas|

    Lägg inte till varje stad i världen eller varje stad i regionen. LUIS måste kunna generalisera vad som är en stad i listan. Se till att ha **These values are interchangeable** (De här värdena är utbytbara) markerat. Den här inställningen innebär att orden i listan behandlas som synonymer. 

3. Träna och publicera appen.

## <a name="get-intent-and-entities-from-endpoint"></a>Hämta avsikter och entiteter från slutpunkten

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Gå till slutet av URL:en i adressen och ange `Move wayne berry from miami to mount vernon`. Den sista frågesträngsparametern är `q`, yttrande**frågan**. 

    ```json
    {
      "query": "Move Wayne Berry from Miami to Mount Vernon",
      "topScoringIntent": {
        "intent": "NewEmployeeRelocationProcess",
        "score": 0.9999999
      },
      "intents": [
        {
          "intent": "NewEmployeeRelocationProcess",
          "score": 0.9999999
        },
        {
          "intent": "Utilities.Confirm",
          "score": 1.49678385E-06
        },
        {
          "intent": "MoveEmployee",
          "score": 8.240291E-07
        },
        {
          "intent": "GetJobInformation",
          "score": 6.3131273E-07
        },
        {
          "intent": "None",
          "score": 4.25E-09
        },
        {
          "intent": "OrgChart-Manager",
          "score": 2.8E-09
        },
        {
          "intent": "OrgChart-Reports",
          "score": 2.8E-09
        },
        {
          "intent": "EmployeeFeedback",
          "score": 1.64E-09
        },
        {
          "intent": "Utilities.StartOver",
          "score": 1.64E-09
        },
        {
          "intent": "Utilities.Help",
          "score": 1.48181822E-09
        },
        {
          "intent": "Utilities.Stop",
          "score": 1.48181822E-09
        },
        {
          "intent": "Utilities.Cancel",
          "score": 1.35E-09
        },
        {
          "intent": "FindForm",
          "score": 1.23846156E-09
        },
        {
          "intent": "ApplyForJob",
          "score": 5.692308E-10
        }
      ],
      "entities": [
        {
          "entity": "wayne berry",
          "type": "builtin.keyPhrase",
          "startIndex": 5,
          "endIndex": 15
        },
        {
          "entity": "miami",
          "type": "builtin.keyPhrase",
          "startIndex": 22,
          "endIndex": 26
        },
        {
          "entity": "wayne berry",
          "type": "NewEmployee",
          "startIndex": 5,
          "endIndex": 15,
          "score": 0.9410646,
          "role": ""
        },
        {
          "entity": "miami",
          "type": "NewEmployeeRelocation",
          "startIndex": 22,
          "endIndex": 26,
          "score": 0.9853915,
          "role": "NewEmployeeReloOrigin"
        },
        {
          "entity": "mount vernon",
          "type": "NewEmployeeRelocation",
          "startIndex": 31,
          "endIndex": 42,
          "score": 0.986044347,
          "role": "NewEmployeeReloDestination"
        }
      ],
      "sentimentAnalysis": {
        "label": "neutral",
        "score": 0.5
      }
}
    ```

Avsiktspoängen är nu mycket högre och rollnamnen är en del av entitetssvaret.

## <a name="hierarchical-entities-versus-roles"></a>Hierarkiska entiteter jämfört med roller

I den [hierarkiska självstudien](luis-quickstart-intent-and-hier-entity.md) identifieras avsikten **MoveEmployee** när du ska flytta en befintlig medarbetare från en kontorsbyggnad till en annan. Exempelyttrandena hade ursprungs- och destinationsplatserna men använde inte roller. I stället var ursprung och destination underordnade den hierarkiska entiteten. 

I den här självstudien identifierar appen Human Resources yttranden om att flytta nya medarbetare från en stad till en annan. Dessa två typer av yttranden är desamma men löses med olika LUIS-funktioner.

|Självstudier|Exempel på yttrande|Ursprungs- och destinationsplatser|
|--|--|--|
|[Hierarkiska (inga roller)](luis-quickstart-intent-and-hier-entity.md)|flytta Jill Jones från **a-2349** till **b-1298**|a-2349, b-1298|
|Den här självstudien (med roller)|Flytta Billy Patterson från **Yuma** till **Denver**.|Yuma, Denver|

Mer information finns i [Roller jämfört med hierarkiska entiteter](luis-concept-roles.md#roles-versus-hierarchical-entities).

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien har en entitet med roller lagts till, och en avsikt med exempelyttranden. Den första slutpunktens förutsägelse med entiteten förutsåg avsikten korrekt men med låga förtroendepoäng. Endast en av de två entiteterna upptäcktes. Därefter lade självstudien till ett mönster som använde entitetsrollerna, och en fraslista för att öka värdet för stadsnamn i yttrandena. Den andra slutpunktens förutsägelse returnerade en mycket tillförlitlig poäng och hittade de båda entitetsrollerna. 

> [!div class="nextstepaction"]
> [Lär dig de bästa metoderna för LUIS-appar](luis-concept-best-practices.md)
