---
title: 'Självstudie 4: Mönstret roller för kontexten relaterade data'
titleSuffix: Azure Cognitive Services
description: Använda ett mönster för att extrahera data från en mall för välutformat-uttryck. Mall-uttryck används en enkel enhet och roller för att extrahera relaterade data, till exempel ursprungsplatsen och målplatsen.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.technology: language-understanding
ms.topic: article
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: 07fc753f7f60b51fc6b27e43ffb8c181c570bb5a
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47032520"
---
# <a name="tutorial-4-extract-contextually-related-patterns"></a>Självstudie: 4. Extrahera sammanhangsbaserade mönster

I den här självstudien använder du ett mönster för att extrahera data från en mall för välutformat-uttryck. Mall-uttryck används en enkel enhet och roller för att extrahera relaterade data, till exempel ursprungsplatsen och målplatsen.  När du använder mönster, behövs färre exempel yttranden för avsikten.

Syftet med roller är att extrahera sammanhangsmässigt relaterade entiteter i ett uttryck. I uttryck, `Move new employee Robert Williams from Sacramento and San Francisco`, ursprung stad och mål stad värden är relaterade till varandra och Använd vanliga språk för att ange varje plats. 


Namnet på den nya medarbetaren Billy Patterson ingår inte i listan entiteten **medarbetare** ännu. Namnet på nya medarbetaren extraheras först för att skicka namnet till ett externt system att skapa autentiseringsuppgifter för företaget. När företagets autentiseringsuppgifter skapas, anställd autentiseringsuppgifterna läggs till i listan entiteten **medarbetare**.

Ny medarbetare och familj måste flyttas från den nuvarande ort till en stad där det fiktiva företaget finns. Eftersom en ny medarbetare kan komma från någon annan stad, måste platserna som ska identifieras. En fast lista, till exempel en lista över entitet fungerar inte eftersom endast orter i listan skulle extraheras.

De roll som är associerade med original och beskrivning städer måste vara unikt för alla entiteter. Ett enkelt sätt att kontrollera att rollerna som är unika är att koppla dem till den innehållande entiteten via en namnstrategin. Den **NewEmployeeRelocation** entiteten är en enkel enhet med två roller: **NewEmployeeReloOrigin** och **NewEmployeeReloDestination**. Relo är kort för dataflytt.

Eftersom exempel uttryck `Move new employee Robert Williams from Sacramento and San Francisco` har endast datorn lärt dig entiteter, är det viktigt att tillhandahålla tillräckligt med exempel yttranden med intentionen så att entiteterna som har identifierats.  

**Medan mönster kan du ge färre exempel yttranden, om entiteterna inte identifieras, matchar inte mönstret.**

Om du har problem med enkel enhet identifiering eftersom det är ett namn, till exempel en stad kan du överväga att lägga till en fras lista med liknande värden. Detta hjälper att identifiera namnet på staden genom att ge LUIS en ytterligare signal om den typ av ord eller fraser. Fras listor att endast mönstret genom att hjälpa med entiteten identifiering, vilket är nödvändigt för att mönstret ska matcha. 

**I den här självstudien får du lära dig hur du:**

> [!div class="checklist"]
> * Använd befintliga självstudieappen
> * Skapa nya entiteter
> * Skapa ny avsikt
> * Träna
> * Publicera
> * Hämta avsikter och entiteter från slutpunkten
> * Skapa mönster med roller
> * Skapa frasen lista över städer
> * Hämta avsikter och entiteter från slutpunkten

[!include[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Använd befintlig app
Fortsätt med den app som skapats i den sista självstudien med namnet **ska**. 

Om du inte har appen ska från den tidigare självstudiekursen, använder du följande steg:

1.  Hämta och spara [app JSON-fil](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-patterns-HumanResources-v2.json).

2. Importera JSON till en ny app.

3. Från den **hantera** avsnittet på den **versioner** fliken klona versionen och ge den namnet `roles`. Kloning är ett bra sätt att prova på olika LUIS-funktioner utan att påverka originalversionen. Eftersom versionsnamnet används som en del av URL: en väg, får inte namnet innehålla några tecken som inte är giltiga i en URL.

## <a name="create-new-entities"></a>Skapa nya entiteter

1. [!include[Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Välj **entiteter** i det vänstra navigeringsfönstret. 

3. Välj **Create new entity** (Skapa ny entitet).

4. I popup-fönstret, ange `NewEmployee` som en **enkel** entitet.

5. Välj **Create new entity** (Skapa ny entitet).

6. I popup-fönstret, ange `NewEmployeeRelocation` som en **enkel** entitet.

7. Välj **NewEmployeeRelocation** från listan över entiteter. 

8. Ange den första rollen som `NewEmployeeReloOrigin` och välj Ange.

9. Ange den andra rollen som `NewEmployeeReloDestination` och välj Ange.

## <a name="create-new-intent"></a>Skapa ny avsikt
Märkning entiteter i de här stegen kan vara enklare om entiteten fördefinierade keyPhrase tas bort innan du börjar som sedan läggs tillbaka när du är klar med stegen i det här avsnittet. 

1. Välj **avsikter** i det vänstra navigeringsfönstret.

2. Välj **Create new intent** (Skapa ny avsikt). 

3. Ange `NewEmployeeRelocationProcess` som avsiktlig namn i popup-dialogrutan.

4. Ange följande exempel talade, märkning nya entiteter. Enhet och roll värdena är visas med fet stil. Kom ihåg att växla till den **token visa** om det lättare att märka texten. 

    Du anger inte rollen för entiteten när märkning i avsikten. Du göra det senare när du skapar mönstret. 

    |Yttrande|NewEmployee|NewEmployeeRelocation|
    |--|--|--|
    |Flytta **Bob Jones** från **Seattle** till **Los Colinas**|Bob Jones|Seattle, Los Colinas|
    |Flytta **Dave C. Cooper** från **Redmond** till **New York City**|Dave C. Cooper|Redmond, New York City|
    |Flytta **Jim Paul Smith** från **Toronto** till **västra Vancouver**|Jim Paul Smith|Toronto, västra Vancouver|
    |Flytta **J. Benson** från **Boston** till **Staines vid Thames**|J. Benson|Boston, Staines vid Thames|
    |Flytta **Travis ”Trav” Hinton** från **Castelo Branco** till **Orlando**|Travis ”Trav” Hinton|Castelo Branco Orlando|
    |Flytta **Trevor Nottington III** från **Aranda de Duero** till **Boise**|Trevor Nottington III|Aranda de Duero, Boise|
    |Flytta **Dr. Greg Williams** från **Orlando** till **Ellicott stad**|Dr. Greg Williams|Orlando, Ellicott stad|
    |Flytta **Robert ”Bobby” Gregson** från **Kansas Stad** till **San Juan Capistrano**|Robert ”Bobby” Gregson|Kansas City, San Juan Capistrano|
    |Flytta **Patti Owens** från **Bellevue** till **Rockford**|Patti Owens|Bellevue, Rockford|
    |Flytta **Johanna Bartlet** från **Tuscan** till **Santa Fe**|Johanna Bartlet|Tuscan, Santa Fe|

    Medarbetarnamn har prefixet, räkna ord, syntax och suffix olika. Detta är viktigt för LUIS för att förstå variationer av ett nytt medarbetarnamn. Stadsnamnen har också en mängd olika räkna ord och syntax. Den här olika är viktigt att lära LUIS hur dessa entiteter kan visas i en användares uttryck. 
    
    Om någon entitet av samma ordräkning och inga andra varianter, skulle du lär LUIS att den här entiteten har bara den ordräkning eller inga andra ändringar. LUIS skulle inte kunna korrekt förutse en bredare uppsättning variationer eftersom den inte visades någon. 

    Om du har tagit bort entiteten keyPhrase lägga tillbaka det i appen nu.

## <a name="train"></a>Träna

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Publicera

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Hämta avsikt och entiteter från slutpunkten

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

2. Gå till slutet av URL:en i adressen och ange `Move Wayne Berry from Miami to Mount Vernon`. Den sista frågesträngsparametern är `q`, yttrande**frågan**. 

    ```JSON
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

Avsiktshantering förutsägelse poängen är endast 50%. Om klientprogrammet kräver en hög siffra, måste det kan åtgärdas. Entiteter har inte förväntad antingen.

En av platserna som har extraherats men den andra platsen var inte. 

Mönster hjälper förutsägelse poäng, men entiteterna måste att korrekt förutse innan mönstret matchar uttryck. 

## <a name="pattern-with-roles"></a>Mönstret med roller

1. Välj **skapa** i det övre navigeringsfältet.

2. Välj **mönster** i det vänstra navigeringsfönstret.

3. Välj **NewEmployeeRelocationProcess** från den **väljer ett intent** listrutan. 

4. Ange följande mönster: `move {NewEmployee} from {NewEmployeeRelocation:NewEmployeeReloOrigin} to {NewEmployeeRelocation:NewEmployeeReloDestination}[.]`

    Om du träna, publicera och fråga slutpunkten kan du bli besviken att se att entiteterna inte hittas, så mönstret matchade därför förutsägelsen inte förbättra. Det här är en följd av finns inte tillräckligt med exempel yttranden med märkta entiteter. I stället för att lägga till fler exempel, lägger du till en fras lista för att åtgärda problemet.

## <a name="cities-phrase-list"></a>Städer frasen lista
Städer, som t.ex. namn på personer är svårt att de kan vara en blandning av ord och skiljetecken. Städerna region och världen är kända så LUIS måste en fras över städer att börja lära dig. 

1. Välj **frasen lista** från den **förbättra apprestanda** avsnitt i den vänstra menyn. 

2. Namnge listan `Cities` och Lägg till följande `values` lista:

    |Värdena för frasen lista|
    |--|
    |Seattle|
    |San Diego|
    |New York City|
    |Los Angeles|
    |Portland|
    |Philadephia|
    |Miami|
    |Dallas|

    Lägg inte till varje stad i världen eller även varje stad i regionen. LUIS måste kunna generalisera vilken stad är i listan. Se till att välja **dessa värden är utbytbara** valda. Den här inställningen innebär att orden i listan på behandlas som synonymer. 

3. Träna och publicera appen.

## <a name="get-intent-and-entities-from-endpoint"></a>Hämta avsikt och entiteter från slutpunkten

1. [!include[Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Gå till slutet av URL:en i adressen och ange `Move wayne berry from miami to mount vernon`. Den sista frågesträngsparametern är `q`, yttrande**frågan**. 

    ```JSON
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

Avsiktshantering poängen är nu mycket högre och role-namn är en del av svaret för entiteten.

## <a name="hierarchical-entities-versus-roles"></a>Hierarkisk entiteter jämfört med roller

I den [hierarkiska självstudien](luis-quickstart-intent-and-hier-entity.md), **MoveEmployee** avsikt upptäckte när du ska flytta en befintlig medarbetare från en kontorsbyggnad och till en annan. Exempel yttranden hade ursprung- och målplatserna men använde inte roller. I stället har ursprung och målet underordnade till den hierarkiska entiteten. 

I den här självstudien identifierar appen personalfrågor yttranden om att flytta nya anställda från en stad till en annan. Dessa två typer av uttryck är samma men löst till olika LUIS-funktioner.

|Självstudier|Exempel-uttryck|Original och beskrivning platser|
|--|--|--|
|[Hierarkiska (inga roller)](luis-quickstart-intent-and-hier-entity.md)|MV Jill Jones från **a-2349** till **b-1298**|a-2349, b-1298|
|Den här självstudien (med roller)|Flytta Bengt Patterson från **Yuma** till **Denver**.|Yuma Denver|

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg

Den här självstudien lagt till en entitet med roller och syftet med exempel yttranden. Första slutpunkten förutsägelser med entiteten korrekt förutse avsikt men med ett låga förtroenderesultat. Endast en av de två entiteterna upptäcktes. Därefter självstudien lagt till ett mönster som används för rollerna entiteten och en fras lista för att öka värdet för stadsnamn i talade. Andra slutpunkten förutsägelsen returnerade en hög exakthet poäng och hitta båda rollerna för entiteten. 

> [!div class="nextstepaction"]
> [Läs om bästa praxis för LUIS-appar](luis-concept-best-practices.md)
