---
title: Självstudier med mönster för att förbättra LUIS förutsägelser – Azure | Microsoft Docs
titleSuffix: Cognitive Services
description: I den här självstudien använder du mönstret för avsikter för att förbättra LUIS avsikt och entiteten förutsägelser.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 07/30/2018
ms.author: diberry
ms.openlocfilehash: 9c14f2121cd83cec802f4fd4a92661d58eb7efb3
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44159581"
---
# <a name="tutorial-improve-app-with-patterns"></a>Självstudie: Förbättra appen med mönster

Använda mönster i de här självstudierna för att öka avsikt och entiteten förutsägelse.  

> [!div class="checklist"]
* Så här identifierar du att ett mönster vill hjälpa din app
* Så här skapar du ett mönster
* Så här verifierar du mönstret förutsägelse förbättringar

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>Innan du börjar

Om du inte har personalapp från den [batch test](luis-tutorial-batch-testing.md) självstudien [importera](luis-how-to-start-new-app.md#import-new-app) JSON-koden i en ny app i den [LUIS](luis-reference-regions.md#luis-website) webbplats. App att importera finns i den [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-batchtest-HumanResources.json) GitHub-lagringsplatsen.

Om du vill behålla den ursprungliga Human Resources-appen (Personalfrågor) klonar du versionen på sidan [Settings](luis-how-to-manage-versions.md#clone-a-version) (Inställningar) och ger den namnet `patterns`. Kloning är ett bra sätt att prova på olika LUIS-funktioner utan att påverka originalversionen. 

## <a name="patterns-teach-luis-common-utterances-with-fewer-examples"></a>Mönster Lär LUIS vanliga yttranden med färre exempel

På grund av mänskliga resursdomänen finns det några vanliga sätt att be om medarbetaren relationer i organisationer. Exempel:

|Yttranden|
|--|
|Som rapporterar Jill Jones till?|
|Som rapporterar till Jill Jones?|

Dessa uttryck är för Stäng om du vill fastställa sammanhangsberoende unikhet för var och en utan att ange många uttryck exemplen. Lär sig vanliga mönster för uttryck för en avsikt utan att ange många uttryck exempel genom att lägga till ett mönster för en avsikt, LUIS. 

Exempel mallen yttranden avsikt följande:

|Exempel mallen yttranden|
|--|
|Vem gör {medarbetare} rapporterar till?|
|Som rapporterar till {medarbetare}?|

Mönstret tillhandahålls via en mall uttryck exempel, som inkluderar syntax för att identifiera entiteter och ignorable text. Ett mönster är en kombination av matchning med reguljära uttryck och machine learning.  Uttryck mallexemplet är tillsammans med avsikt yttranden ge LUIS bättre förståelse för vilka yttranden passar avsikten.

För ett mönster som ska matchas till ett uttryck, måste entiteter i uttryck stämma överens entiteter i mallen uttryck först. Dock mallen inte hjälper till att förutsäga entiteter, endast avsikter. 

**Medan mönster kan du ge färre exempel yttranden, om entiteterna inte identifieras, matchar inte mönstret.**

Kom ihåg att anställda har skapats i den [lista entitet självstudien](luis-quickstart-intent-and-list-entity.md).

## <a name="create-new-intents-and-their-utterances"></a>Skapa ny avsikter och deras yttranden

Lägg till två nya avsikter: `OrgChart-Manager` och `OrgChart-Reports`. När LUIS returnerar en förutsägelse klientappar avsikt namnet kan användas som ett funktionsnamn i klientappen och att de anställda entiteten kan användas som en parameter för funktionen.

```Javascript
OrgChart-Manager(employee){
    ///
}
```

1. Kontrollera att Human Resources-appen (Personalfrågor) finns i avsnittet **Build** (Skapa) i LUIS. Du kan ändra till det här avsnittet genom att välja **Build** (Skapa) i menyraden längst upp till höger. 

2. På sidan **Intents** (Avsikter) väljer du **Create new intent** (Skapa ny avsikt). 

3. Ange `OrgChart-Manager` i popup-dialogrutan och välj sedan **Done** (Klar).

    ![Skapa nytt meddelande popup-fönster](media/luis-tutorial-pattern/hr-create-new-intent-popup.png)

4. Lägg till exempel på yttranden i avsikten.

    |Exempel på yttranden|
    |--|
    |Vem är John W. Smith underordnad av?|
    |Som rapporterar John W. Smith till?|
    |Vem är John W. Smith manager?|
    |Som Jill Jones direkt rapporterar till?|
    |Vem är Jill Jones Övervakaren?|

    [![Skärmbild av LUIS att lägga till nya yttranden till avsikt](media/luis-tutorial-pattern/hr-orgchart-manager-intent.png "skärmbild LUIS att lägga till nya yttranden till avsikt")](media/luis-tutorial-pattern/hr-orgchart-manager-intent.png#lightbox)

    Oroa dig inte om entiteten keyPhrase är märkt med yttranden av avsikten i stället för entiteten medarbetare. Båda är korrekt förutse i rutan och på slutpunkten. 

5. Välj **Intents** (Avsikter) i det vänstra navigeringsfältet.

6. Välj **Create new intent** (Skapa ny avsikt). 

7. Ange `OrgChart-Reports` i popup-dialogrutan och välj sedan **Done** (Klar).

8. Lägg till exempel på yttranden i avsikten.

    |Exempel på yttranden|
    |--|
    |Vem är John W. Smith underordnade?|
    |Som rapporterar till John W. Smith?|
    |Vem som hanterar John W. Smith?|
    |Vem är Jill Jones direktrapporter?|
    |Vem som Jill Jones övervaka?|

## <a name="caution-about-example-utterance-quantity"></a>Varning om exempel uttryck kvantitet

Hur många exempel yttranden i dessa avsikter är inte tillräckligt för att träna LUIS korrekt. Varje avsikt bör ha minst 15 yttranden med en rad olika längd för word val och uttryck i en verklig app. De här några yttranden markerade specifikt för att markera mönster. 

## <a name="train-the-luis-app"></a>Träna LUIS-appen

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publicera appen för att få slutpunkts-URL

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint-with-a-different-utterance"></a>Skicka fråga till slutpunkten med ett annat yttrande

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Gå till slutet av URL:en i adressen och ange `Who is the boss of Jill Jones?`. Den sista frågesträngsparametern är `q`, yttrande**frågan**. 

    ```JSON
    {
        "query": "who is the boss of jill jones?",
        "topScoringIntent": {
            "intent": "OrgChart-Manager",
            "score": 0.353984952
        },
        "intents": [
            {
                "intent": "OrgChart-Manager",
                "score": 0.353984952
            },
            {
                "intent": "OrgChart-Reports",
                "score": 0.214128986
            },
            {
                "intent": "EmployeeFeedback",
                "score": 0.08434003
            },
            {
                "intent": "MoveEmployee",
                "score": 0.019131
            },
            {
                "intent": "GetJobInformation",
                "score": 0.004819009
            },
            {
                "intent": "Utilities.Confirm",
                "score": 0.0043958663
            },
            {
                "intent": "Utilities.StartOver",
                "score": 0.00312064588
            },
            {
                "intent": "Utilities.Cancel",
                "score": 0.002265454
            },
            {
                "intent": "Utilities.Help",
                "score": 0.00133465114
            },
            {
                "intent": "None",
                "score": 0.0011388344
            },
            {
                "intent": "Utilities.Stop",
                "score": 0.00111166481
            },
            {
                "intent": "FindForm",
                "score": 0.0008900076
            },
            {
                "intent": "ApplyForJob",
                "score": 0.0007836131
            }
        ],
        "entities": [
            {
                "entity": "jill jones",
                "type": "Employee",
                "startIndex": 19,
                "endIndex": 28,
                "resolution": {
                    "values": [
                        "Employee-45612"
                    ]
                }
            },
            {
                "entity": "boss of jill jones",
                "type": "builtin.keyPhrase",
                "startIndex": 11,
                "endIndex": 28
            }
        ]
    }
    ```

Det gick att aktivera den här frågan? För den här cykeln utbildning lyckades. Poäng för de två översta avsikterna ligger nära. Eftersom LUIS utbildning inte är exakt samma varje gång, det finns lite variation och dessa två poäng kunde Invertera under nästa cykel för utbildning. Resultatet är att fel avsikten kan returneras. 

Använda mönster för att göra rätt avsikten poäng är betydligt högre i procent och längre bort från nästa högst poäng. 

Lämna andra webbläsarfönstret öppen. Du kan använda den igen senare under kursen. 

## <a name="add-the-template-utterances"></a>Lägg till mall-yttranden

1. Välj **skapa** på den översta menyn.

2. I det vänstra navigeringsfönstret under **förbättra apprestanda**väljer **mönster** i det vänstra navigeringsfönstret.

3. Välj den **Organisationsschema Manager** avsikt, ange följande uttryck från mall, en i taget, att välja returtangenten efter varje mall-uttryck:

    |Mallen yttranden|
    |:--|
    |Vem är {medarbetare} underordnat [?]|
    |Vem gör {medarbetare} rapporterar till [?]|
    |Vem är chef för {medarbetare} [s] [?]|
    |Vem gör {medarbetare} rapporterar direkt till [?]|
    |Vem är ansvarig för {medarbetare} [s] [?]|
    |Vem är chef för {anställd} [?]|

    Den `{Employee}` syntax markerar entitet plats i mallen-uttryck som samt vilken entitet som det är. 

    Entiteter med roller använder syntax som innehåller namnet på rollen och de behandlas i en [separat självstudien för roller](luis-tutorial-pattern-roles.md). 

    Valfria syntax `[]`, markerar ord eller skiljetecken är valfria. LUIS matchar uttryck, ignorerar valfritt texten inom hakparentes.

    Om du skriver mall uttryck, LUIS hjälper till att du fyller i entiteten när du anger vänster klammer `{`.

    [![Skärmbild av hur du anger mallen yttranden för avsikt](./media/luis-tutorial-pattern/hr-pattern-missing-entity.png)](./media/luis-tutorial-pattern/hr-pattern-missing-entity.png#lightbox)

4. Välj den **Organisationsschema rapporter** avsikt, ange följande uttryck från mall, en i taget, att välja returtangenten efter varje mall-uttryck:

    |Mallen yttranden|
    |:--|
    |Vem är {medarbetare} [s] underordnade [?]|
    |Som rapporterar till {medarbetare} [?]|
    |Vem gör {medarbetare} hantera [?]|
    |Vem är {medarbetare} direktrapporter [?]|
    |Vem gör {medarbetare} övervaka [?]|
    |Vem gör {medarbetare} chef [?]|

## <a name="query-endpoint-when-patterns-are-used"></a>Fråga slutpunkt när mönster används

1. Träna och publicera appen igen.

2. Växla flikar i webbläsaren tillbaka till fliken endpoint URL: en.

3. Gå till i slutet av URL-adressen och ange `Who is the boss of Jill Jones?` som uttryck. Den sista frågesträngsparametern är `q`, yttrande**frågan**. 

    ```JSON
    {
        "query": "who is the boss of jill jones?",
        "topScoringIntent": {
            "intent": "OrgChart-Manager",
            "score": 0.9999989
        },
        "intents": [
            {
                "intent": "OrgChart-Manager",
                "score": 0.9999989
            },
            {
                "intent": "OrgChart-Reports",
                "score": 7.616303E-05
            },
            {
                "intent": "EmployeeFeedback",
                "score": 7.84204349E-06
            },
            {
                "intent": "GetJobInformation",
                "score": 1.20674213E-06
            },
            {
                "intent": "MoveEmployee",
                "score": 7.91245157E-07
            },
            {
                "intent": "None",
                "score": 3.875E-09
            },
            {
                "intent": "Utilities.StartOver",
                "score": 1.49E-09
            },
            {
                "intent": "Utilities.Confirm",
                "score": 1.34545453E-09
            },
            {
                "intent": "Utilities.Help",
                "score": 1.34545453E-09
            },
            {
                "intent": "Utilities.Stop",
                "score": 1.34545453E-09
            },
            {
                "intent": "Utilities.Cancel",
                "score": 1.225E-09
            },
            {
                "intent": "FindForm",
                "score": 1.123077E-09
            },
            {
                "intent": "ApplyForJob",
                "score": 5.625E-10
            }
        ],
        "entities": [
            {
                "entity": "jill jones",
                "type": "Employee",
                "startIndex": 19,
                "endIndex": 28,
                "resolution": {
                    "values": [
                        "Employee-45612"
                    ]
                },
                "role": ""
            },
            {
                "entity": "boss of jill jones",
                "type": "builtin.keyPhrase",
                "startIndex": 11,
                "endIndex": 28
            }
        ]
    }
    ```

Avsiktshantering förutsägelse är nu betydligt högre.

## <a name="working-with-optional-text-and-prebuilt-entities"></a>Arbeta med valfri text och fördefinierade entiteter

Talade tidigare mönstret mallen i den här självstudien hade några exempel på valfri text som exempelvis Possessiv användning av bokstaven s, `'s`, och användning av frågetecknet, `?`. Anta att slutpunkten yttranden visar att chefer och personal representanter söker historiska data samt planerat anställda flyttar inom företaget händer ett datum i framtiden.

Exempel yttranden är:

|Avsikt|Exempel yttranden med valfri text och fördefinierade entiteter|
|:--|:--|
|Organisationsschema Manager|`Who was Jill Jones manager on March 3?`|
|Organisationsschema Manager|`Who is Jill Jones manager now?`|
|Organisationsschema Manager|`Who will be Jill Jones manager in a month?`|
|Organisationsschema Manager|`Who will be Jill Jones manager on March 3?`|

Var och en av de här exemplen använder ett verb Tempus `was`, `is`, `will be`, samt ett datum `March 3`, `now`, och `in a month`, som LUIS behöver förutsäga korrekt. Lägg märke till att de senaste två exemplen använder nästan samma text undantag för `in` och `on`.

Exempel mallen yttranden:
|Avsikt|Exempel yttranden med valfri text och fördefinierade entiteter|
|:--|:--|
|Organisationsschema Manager|`who was {Employee}['s] manager [[on]{datetimeV2}?`]|
|Organisationsschema Manager|`who is {Employee}['s] manager [[on]{datetimeV2}?]`|
|Organisationsschema Manager|`who will be {Employee}['s] manager [[in]{datetimeV2}?]`|
|Organisationsschema Manager|`who will be {Employee}['s] manager [[on]{datetimeV2}?]`|

Användning av hakparenteser och valfria syntax `[]`, gör den här valfri text enkelt att lägga till mall-uttryck och kan kapslas i upp till en andra nivå `[[]]`, och innehålla entiteter eller text.

**Fråga: Varför det gick inte att de sista två exempel yttranden kombinera i en enda mall-uttryck?** Mönster för mallen stöder inte OR-syntax. För att fånga upp både den `in` version och `on` version, var och en måste vara en separat mall-uttryck.

**Fråga: Varför är alla de `w` bokstäver, den första bokstaven i varje mall uttryck gemener? Inte bör de vara eventuellt versaler eller gemener?** Det uttryck som skickats till fråga-slutpunkten av klientprogrammet, konverteras till gemener. Mall-uttryck kan vara versaler eller gemener och slutpunkt-uttryck kan även vara antingen. Jämförelsen görs alltid efter konverteringen till gemener.

**Fråga: Varför är inte fördefinierade nummer en del av mallen uttryck om 3 mars är förväntad både som tal `3` och datum `March 3`?** Mall-uttryck sammanhangsmässigt använder ett datum, antingen bokstavligen i `March 3` eller abstraherad som `in a month`. Ett datum kan innehålla flera men ett tal kan inte nödvändigtvis visas som ett datum. Använd alltid den entitet som bäst representerar den typ som du vill ska returneras i förutsägelseresultat som JSON.  

**Fråga: Vad är dåligt fraserats yttranden som `Who will {Employee}['s] manager be on March 3?`.** Grammatiskt annat verbtempus, till exempel det här var den `will` och `be` är åtskilda måste vara en ny mall-uttryck. Uttryck för den befintliga mallen kommer inte att matcha den. Även om syftet med uttryck inte har ändrats, har word placering i uttryck ändrats. Den här ändringen påverkar förutsägelser i LUIS.

**Kom ihåg: entiteter finns först och sedan mönstret matchas.**

## <a name="edit-the-existing-pattern-template-utterance"></a>Redigera den befintliga mönstret mall uttryck

1. LUIS-webbplatsen, Välj **skapa** i den översta menyn och markera **mönster** på den vänstra menyn. 

2. Hitta den befintliga mallen uttryck `Who is {Employee}['s] manager[?]`, och välj ellipsen (***...*** ) till höger. 

3. Välj **redigera** på snabbmenyn. 

4. Ändra mall-uttryck till: `who is {Employee}['s] manager [[on]{datetimeV2}?]]`

## <a name="add-new-pattern-template-utterances"></a>Lägg till nya mönster för mallen yttranden

1. När den är fortfarande i den **mönster** delen av **skapa**, lägga till flera nya mönstret mall yttranden. Välj **Organisationsschema Manager** avsikt nedrullningsbara menyn och ange var och en av följande mall-uttryck:

    |Avsikt|Exempel yttranden med valfri text och fördefinierade entiteter|
    |--|--|
    |Organisationsschema Manager|`who was {Employee}['s] manager [[on]{datetimeV2}?]`|
    |Organisationsschema Manager|`who is {Employee}['s] manager [[on]{datetimeV2}?]`|
    |Organisationsschema Manager|`who will be {Employee}['s] manager [[in]{datetimeV2}?]`|
    |Organisationsschema Manager|`who will be {Employee}['s] manager [[on]{datetimeV2}?]`|

2. Träna appen.

3. Välj **Test** högst upp på panelen för att öppna panelen testning. 

4. Ange flera test-uttryck för att verifiera att matchas mönstret och den avsikt poängen är mycket stora. 

    När du har angett den första uttryck, Välj **granska** under resultatet så att du kan se de förutsagda resultaten.

    |Yttrande|
    |--|
    |Vem är Jill Jones manager|
    |Vem är jill jones manager|
    |Vem är Jill Jones manager?|
    |Vem är Jill jones manager 3 mars|
    |Vem är Jill Jones manager nästa månad|
    |Vem är Jill Jones manager under en månad?|

Alla dessa yttranden samlade entiteterna i, därför de matchar samma mönster och har en hög förutsägelse poäng.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig hur du använder roller med ett mönster](luis-tutorial-pattern-roles.md)