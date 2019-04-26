---
title: Mönster
titleSuffix: Azure Cognitive Services
description: Använd mönster för att förbättra avsikts- och entitetsförutsägelser och samtidigt ge färre exempelyttranden. Mönstret tillhandahålls via ett exempel på mallyttrande, som innehåller syntax för att identifiera entiteter och ignorerbar text.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 02/22/2019
ms.author: diberry
ms.openlocfilehash: 33541d2a61c52476f6e314f6981a623390de8fa9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60496969"
---
# <a name="tutorial-add-common-pattern-template-utterance-formats"></a>Självstudier: Lägga till format för mallyttrande med vanliga mönster

I den här kursen använder du mönster för att förbättra avsikts- och entitetsförutsägelser och samtidigt ge färre exempelyttranden. Mönstret tillhandahålls via ett exempel på mallyttrande, som innehåller syntax för att identifiera entiteter och ignorerbar text. Ett mönster är en kombination av uttrycksmatchning och maskininlärning.  Exemplet på mallyttrande, tillsammans med avsiktsyttranden, ger LUIS en bättre förståelse för vilka yttranden som passar avsikten. 

**I den här självstudiekursen får du lära du dig att:**

> [!div class="checklist"]
> * Importera en exempelapp 
> * Skapa avsikt
> * Träna
> * Publicera
> * Hämta avsikter och entiteter från en slutpunkt
> * Skapa ett mönster
> * Verifiera förbättringar av mönsterförutsägelser
> * Markera text ignorerbar och kapsla i mönstret
> * Använda testpanel för att verifiera lyckat mönster

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Importera en exempelapp

Fortsätt med appen du skapade i föregående självstudie med namnet **HumanResources**. 

Använd följande steg:

1.  Ladda ned och spara [JSON-filen för appen](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-batchtest-HumanResources.json).

2. Importera JSON-koden till en ny app.

3. I avsnittet **Hantera** går du till fliken **Versioner**, klonar versionen och ger den namnet `patterns`. Kloning är ett bra sätt att prova på olika LUIS-funktioner utan att påverka originalversionen. Eftersom versionsnamnet används i webbadressen får namnet inte innehålla några tecken som är ogiltiga i webbadresser.

## <a name="create-new-intents-and-their-utterances"></a>Skapa nya avsikter och deras yttranden

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. På sidan **Intents** (Avsikter) väljer du **Create new intent** (Skapa ny avsikt). 

3. Ange `OrgChart-Manager` i popup-dialogrutan och välj sedan **Done** (Klar).

    ![Skapa ett nytt popup-fönster för meddelanden](media/luis-tutorial-pattern/hr-create-new-intent-popup.png)

4. Lägg till exempel på yttranden i avsikten.

    |Exempel på yttranden|
    |--|
    |Who is John W. Smith the subordinate of? (Vem John W. Smith underordnad till?)|
    |Who does John W. Smith report to? (Vem rapporterar John W. Smith till?)|
    |Who is John W. Smith's manager? (Vem är John W. Smiths chef?)|
    |Who does Jill Jones directly report to? (Vem rapporterar Jill Jones direkt till?)|
    |Who is Jill Jones supervisor? (Vem är Jill Jones arbetsledare?)|

    [![Skärmbild av LUIS som lägger till nya yttranden till avsikt](media/luis-tutorial-pattern/hr-orgchart-manager-intent.png "Skärmbild av LUIS som lägger till nya yttranden till avsikt")](media/luis-tutorial-pattern/hr-orgchart-manager-intent.png#lightbox)

    Oroa dig inte om keyPhrase-entiteten är märkt i yttranden för avsikten i stället för medarbetarentiteten. Båda förutsägs korrekt i rutan Text och i slutpunkten. 

5. Välj **Intents** (Avsikter) i det vänstra navigeringsfältet.

6. Välj **Create new intent** (Skapa ny avsikt). 

7. Ange `OrgChart-Reports` i popup-dialogrutan och välj sedan **Done** (Klar).

8. Lägg till exempel på yttranden i avsikten.

    |Exempel på yttranden|
    |--|
    |Who are John W. Smith's subordinates? (Vilka är John W. Smiths underordnade?)|
    |Who reports to John W. Smith? (Vilka rapporterar till John W. Smith?)|
    |Who does John W. Smith manage? (Vilka är John W. Smith chef för?)|
    |Who are Jill Jones direct reports? (Vilka rapporterar direkt till Jill Jones?)|
    |Who does Jill Jones supervise? (Vilka är Jill Jones arbetsledare för?)|

## <a name="caution-about-example-utterance-quantity"></a>Varning om kvantiteten för exempeluttryck

[!INCLUDE [Too few examples](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]

## <a name="train"></a>Träna

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Publicera

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Hämta avsikter och entiteter från slutpunkten

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Gå till slutet av URL:en i adressen och ange `Who is the boss of Jill Jones?`. Den sista frågesträngsparametern är `q`, yttrande**frågan**. 

    ```json
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

Lyckades frågan? För den här träningscykeln lyckades den. Poängen för de två främsta avsikterna ligger nära varandra. Eftersom LUIS-träningen inte är exakt samma varje gång (det finns lite variationer) kan dessa två poäng vara omvända i nästa träningscykel. Resultatet är att fel avsikt kan returneras. 

Använd mönster för att göra den korrekta avsiktens poäng betydligt högre procentuellt och längre ifrån den näst högsta poängen. 

Lämna det här andra webbläsarfönstret öppet. Du använder det igen senare i den här kursen. 

## <a name="template-utterances"></a>Mallyttranden
På grund av Human Resources-domänens natur finns det några vanliga sätt att fråga om medarbetares relationer i organisationer. Exempel:

|Yttranden|
|--|
|Who does Jill Jones report to? (Vem rapporterar Jill Jones till?)|
|Who reports to Jill Jones? (Vilka rapporterar till Jill Jones?|

Dessa yttranden ligger för nära varandra för att fastställa den kontextuella unikheten hos respektive yttrande utan att ge många yttrandeexmpel. Genom att lägga till ett mönster för en avsikt lär sig LUIS vanliga yttrandemönster för en avsikt utan att ge många yttrandeexempel. 

Exempel på mallyttranden för den här avsikten är:

|Exempel på mallyttranden|syntaxbetydelse|
|--|--|
|Who does {Employee} report to[?] (Vem rapporterar {Medarbetare} till[?])|interchangeable {Employee}, ignore [?]} (utbytbar {Medarbetare}, ignorera [?]})|
|Who reports to {Employee}[?] (Vilka rapporterar till {Medarbetare}[?])|interchangeable {Employee}, ignore [?]} (utbytbar {Medarbetare}, ignorera [?]})|

Syntaxen `{Employee}` markerar entitetsplatsen i mallyttrandet och vilken entitet det är. Den valfria syntaxen, `[?]`, markerar ord eller skiljetecken som är valfria. LUIS matchar yttrandet, ignorerar den valfria texten inom hakparentes.

Syntaxen ser ut som reguljära uttryck men är inte reguljära uttryck. Endast syntax inom klamrar, `{}`, och hakparentes, `[]`, stöds. De kan kapslas upp till två nivåer.

För att ett mönster ska kunna matchas till ett yttrande måste entiteterna i yttrandet matcha entiteterna i mallyttrandet först. Men mallen hjälper inte att förutsäga entiteter, bara avsikter. 

**Med mönster kan du ge färre exempelyttranden, men om entiteterna inte identifieras matchar inte mönstret.**

## <a name="add-the-patterns-for-the-orgchart-manager-intent"></a>Lägg till mönster för organisationsschema Manager avsikten

1. Välj **Build** (Skapa) i huvudmenyn.

2. I den vänstra navigeringen, under **Improve app performance** (Förbättra appens prestanda) väljer du **Patterns** (Mönster) i den vänstra navigeringen.

3. Välj avsikten **OrgChart-Manager** och ange sedan följande mallyttranden:

    |Mallyttranden|
    |:--|
    |Who is {Employee} the subordinate of[?] (Vem är {Medarbetare} underordnad till[?])|
    |Who does {Employee} report to[?] (Vem rapporterar {Medarbetare} till[?])|
    |Who is {Employee}['s] manager[?] (Vem är {Medarbetare}[s] chef[?])|
    |Who does {Employee} directly report to[?] (Vem rapporterar {Medarbetare} direkt till[?])|
    |Who is {Employee}['s] supervisor[?] (Vem är {Medarbetare}[s] arbetsledare[?])|
    |Who is the boss of {Employee}[?] (Vem är chef för {Medarbetare}[?])|

    Entiteter med roller använder syntax som innehåller rollnamnet och omfattas i en [separat självstudiekurs för roller](luis-tutorial-pattern-roles.md). 

    Om du skriver mallyttrandet hjälper LUIS dig att fylla i entiteten när du anger den vänstra klammern, `{`.

    [![Skärmbild av att ange mallyttranden för avsikt](./media/luis-tutorial-pattern/hr-pattern-missing-entity.png)](./media/luis-tutorial-pattern/hr-pattern-missing-entity.png#lightbox)

4. När du fortfarande på sidan mönster, Välj den **Organisationsschema rapporter** avsikt, ange sedan följande mall-uttryck:

    |Mallyttranden|
    |:--|
    |Who are {Employee}['s] subordinates[?] (Vilka är {Medarbetare}[s] underordnade[?])|
    |Who reports to {Employee}[?] (Vilka rapporterar till {Medarbetare}[?])|
    |Who does {Employee} manage[?] (Vilka är {Medarbetare} chef för[?])|
    |Who are {Employee} direct reports[?] (Vilka rapporterar direkt till {Medarbetare}[?])|
    |Who does {Employee} supervise[?] (Vilka är {Medarbetare} arbetsledare för[?])|
    |Who does {Employee} boss[?] (Vilka är {Medarbetare} chef för[?])|

## <a name="query-endpoint-when-patterns-are-used"></a>Frågeslutpunkt där mönster används

Nu när mönster som läggs till i appen, träna, publicera och fråga appen vid förutsägelse runtime-slutpunkten.

1. Träna och publicera appen igen.

1. Byt webbläsarflik tillbaka till fliken med slutpunkts-URL:en.

1. Gå till slutet av URL:en i adressen och ange `Who is the boss of Jill Jones?` som yttrande. Den sista frågesträngsparametern är `q`, yttrande**frågan**. 

    ```json
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

Avsiktshantering förutsägelse är nu avsevärt tryggare.

## <a name="working-with-optional-text-and-prebuilt-entities"></a>Arbeta med valfri text och fördefinierade entiteter

Föregående mallyttranden för mönster i den här kursen hade några exempel på valfri text, som possessiv användning av bokstaven s `'s` (se exemplen på engelska) och användningen av frågetecken, `?`. Anta att du måste tillåta för aktuella och framtida datum i textrutan uttryck.

Exempelyttranden är:

|Avsikt|Exempelyttranden med valfri text och fördefinierade entiteter|
|:--|:--|
|OrgChart-Manager (Organisationsschema-Chef)|`Who was Jill Jones manager on March 3?`|
|OrgChart-Manager (Organisationsschema-Chef)|`Who is Jill Jones manager now?`|
|OrgChart-Manager (Organisationsschema-Chef)|`Who will be Jill Jones manager in a month?`|
|OrgChart-Manager (Organisationsschema-Chef)|`Who will be Jill Jones manager on March 3?`|

Alla dessa exempel använder ett verbtempus, `was`, `is`, `will be` samt ett datum, `March 3`, `now` och `in a month`, som LUIS behöver för att förutsäga korrekt. Observera att de senaste två exemplen använder nästan samma text för `in` och `on`.

Exempel mallen yttranden som tillåter att den här valfria informationen: 

|Avsikt|Exempelyttranden med valfri text och fördefinierade entiteter|
|:--|:--|
|OrgChart-Manager (Organisationsschema-Chef)|`who was {Employee}['s] manager [[on]{datetimeV2}?`]|
|OrgChart-Manager (Organisationsschema-Chef)|`who is {Employee}['s] manager [[on]{datetimeV2}?]`|


Användningen av valfri syntax inom hakparentes, `[]`, gör den här valfria texten enkel att lägga till i mallyttrandet och kan kapslas upp till en andra nivå, `[[]]`, och innehåller entiteter eller text.


**Fråga: Varför är alla `w`-bokstäver, den första bokstaven i varje mallyttrande, gemener? Borde det inte vara valfritt versaler eller gemener?** Yttrandet som skickas till frågeslutpunkten, av frågeslutpunkten, konverteras till gemener. Mallyttrandet kan vara versaler eller gemener och slutpunktsyttrandet kan också vara antingen eller. Jämförelsen görs alltid efter konverteringen till gemener.

**Fråga: Varför är inte fördefinierat tal ett mallyttrande om 3 mars förutsägs både som talet `3` och datumet `March 3`?** Mallyttrandet använder kontextuellt ett datum, antingen bokstavligen som i `March 3` eller abstraheras som `in a month`. Ett datum innehåller ett tal men ett tal kanske inte nödvändigtvis ses som ett datum. Använd alltid entiteten som bäst representerar den typ du vill ska returneras i resultatet för förutsägelse-JSON.  

**Fråga: Hur är det med dåligt uttryckta yttranden som `Who will {Employee}['s] manager be on March 3?`?** Grammatiskt olika verbtempus som det här där `will` och `be` är åtskilda måste vara ett nytt mallyttrande. Det befintliga mallyttrandet kommer inte att matcha det. Avsikten för yttrandet har inte ändrats men ordets placering i yttrandet har ändrats. Den här ändringen påverkar förutsägelsen i LUIS. Du kan [grupp och eller](#use-the-or-operator-and-groups) verbtempus att kombinera dessa yttranden. 

**Kom ihåg: entiteter hittas först, sedan matchas mönstret.**

## <a name="edit-the-existing-pattern-template-utterance"></a>Redigera det befintliga mallyttrandet för mönster

1. På LUIS-webbplatsen väljer du **Build** (Skapa) i huvudmenyn och välj sedan **Patterns** (Mönster) i den vänstra menyn. 

1. Sök efter den befintliga mallen uttryck `Who is {Employee}['s] manager[?]`, och välj ellipsen (***...*** ) till höger, Välj **redigera** på snabbmenyn. 

1. Ändra mallyttrandet till: `who is {Employee}['s] manager [[on]{datetimeV2}?]`

## <a name="add-new-pattern-template-utterances"></a>Lägga till nya mallyttranden för mönster

1. Lägg till flera nya mallyttranden för mönster när du fortfarande är i avsnittet **Patterns** (Mönster) i **Build** (Skapa). Välj **OrgChart-Manager** i den nedrullningsbara menyn Intent (Avsikt) och ange vart och ett av följande yttranden:

    |Avsikt|Exempelyttranden med valfri text och fördefinierade entiteter|
    |--|--|
    |OrgChart-Manager (Organisationsschema-Chef)|`who was {Employee}['s] manager [[on]{datetimeV2}?]`|
    |OrgChart-Manager (Organisationsschema-Chef)|`who will be {Employee}['s] manager [[in]{datetimeV2}?]`|
    |OrgChart-Manager (Organisationsschema-Chef)|`who will be {Employee}['s] manager [[on]{datetimeV2}?]`|

2. Träna appen.

3. Välj **Test** högst upp i panelen för att öppna testpanelen. 

4. Ange flera testyttranden för att verifiera att mönstret matchas och avsiktspoängen är mycket höga. 

    När du har angett det första yttrandet väljer du **Inspect** (Granska) under resultatet så att du kan se alla förutsägelseresultat. Varje uttryck ska ha den **Organisationsschema Manager** avsikten och bör extrahera värden för enheterna som anställda och datetimeV2.

    |Yttrande|
    |--|
    |Who will be Jill Jones manager (Vem blir Jill Jones chef)|
    |who will be Jill Jones's manager (vem blir jill jones chef)|
    |Who will be Jill Jones's manager? (Vem blir Jill Jones chef?)|
    |who will be Jill jones manager on March 3 (vem blir Jill jones chef den 3 mars)|
    |Who will be Jill Jones manager next Month (Vem blir Jill Jones chef nästa månad)|
    |Who will be Jill Jones manager in a month? (Vem blir Jill Jones chef om en månad?)|

Alla dessa yttranden hittade entiteterna inuti, och därför matchar de samma mönster, och har höga förutsägelsepoäng.

## <a name="use-the-or-operator-and-groups"></a>Använd OR-operator och grupper

Flera av de föregående mall-uttryck är mycket nära. Använd den **grupp** `()` och **eller** `|` syntaxen för att minska mall yttranden. 

Kombinera följande 2 mönster i ett enda mönster med hjälp av gruppen `()` och eller `|` syntax.

|Avsikt|Exempelyttranden med valfri text och fördefinierade entiteter|
|--|--|
|OrgChart-Manager (Organisationsschema-Chef)|`who will be {Employee}['s] manager [[in]{datetimeV2}?]`|
|OrgChart-Manager (Organisationsschema-Chef)|`who will be {Employee}['s] manager [[on]{datetimeV2}?]`|

Uttryck för den nya mallen kommer att: 

`who ( was | is | will be ) {Employee}['s] manager [([in]|[on]){datetimeV2}?]`. 

Här används en **grupp** runt krävs verb Tempus och den valfria `in` och `on` med en **eller** pipe mellan dem. 

1. På den **mönster** väljer den **Organisationsschema Manager** filter. Begränsa listan genom att söka efter `manager`. 

    ![Sök i Organisationsschema Manager avsikt mönster efter termen ”manager”](./media/luis-tutorial-pattern/search-patterns.png)

1. Håll en version av mall-uttryck (för att redigera i nästa steg) och ta bort andra varianter. 

1. Ändra mall-uttryck till: 

    `who ( was | is | will be ) {Employee}['s] manager [([in]|[on]){datetimeV2}?]`.

1. Träna appen.

1. Använd rutan för att testa versioner av uttryck:

    |Yttranden ange i Testfönster|
    |--|
    |`Who is Jill Jones manager this month`|
    |`Who is Jill Jones manager on July 5th`|
    |`Who was Jill Jones manager last month`|
    |`Who was Jill Jones manager on July 5th`|    
    |`Who will be Jill Jones manager in a month`|
    |`Who will be Jill Jones manager on July 5th`|


## <a name="use-the-utterance-beginning-and-ending-anchors"></a>Använda uttryck inledande och avslutande ankare

Mönstret syntaxen innehåller inledande och avslutande uttryck ankare syntaxen för en hatt `^`. Första och sista uttryck ankare kan användas tillsammans att målet specificerad och möjligen literal uttryck eller brukade separat target avsikter. 

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här kursen läggs två avsikter till för yttranden som var svåra att förutsäga med hög precision utan att ha många exempelyttranden. Lägga till mönster för dessa tillåtna LUIS för att bättre förutsäga avsikten med betydligt högre poäng. Markera entiteter och ignorerbar text tillåtna LUIS för att tillämpa mönstret på en mängd olika yttranden.

> [!div class="nextstepaction"]
> [Lär dig hur du använder roller med ett mönster](luis-tutorial-pattern-roles.md)
