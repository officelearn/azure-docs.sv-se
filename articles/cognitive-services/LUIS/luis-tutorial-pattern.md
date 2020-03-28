---
title: 'Självstudiekurs: Mönster - LUIS'
titleSuffix: Azure Cognitive Services
description: Använd mönster för att öka avsikts- och entitetsförutsägelsen samtidigt som du tillhandahåller färre exempelyttranden i den här självstudien. Mönstret tillhandahålls som ett mallutseende exempel, som innehåller syntax för att identifiera entiteter och ignorerande text.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: diberry
ms.openlocfilehash: 69894dfc6bcbe9eb56451524c78e82da2745aa52
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979768"
---
# <a name="tutorial-add-common-pattern-template-utterance-formats-to-improve-predictions"></a>Självstudiekurs: Lägg till vanliga uttrycksformat för mönstermallar för att förbättra förutsägelser

I den här självstudien använder du mönster för att öka avsikt och entitetsförutsägels, vilket gör att du kan ge färre exempelyttranden. Mönstret är mallutseende som tilldelats en avsikt, som innehåller syntax för att identifiera entiteter och ignorerande text.

**I den här självstudiekursen får du lära du dig att:**

> [!div class="checklist"]
> * Skapa ett mönster
> * Verifiera förbättringar av mönsterförutsägelser
> * Markera text ignorerbar och kapsla i mönstret
> * Använda testpanel för att verifiera lyckat mönster

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="utterances-in-intent-and-pattern"></a>Yttranden i avsikt och mönster

Det finns två typer av yttranden som lagras i LUIS-appen:

* Exempel yttranden i avsikt
* Mallyttranden i mönstret

Genom att lägga till mallyttranden som ett mönster kan du ge färre exempelyttranden totalt sett till en avsikt.

Ett mönster används som en kombination av uttrycksmatchning och maskininlärning.  Mallutsikten, tillsammans med exempelyttrandena, ger LUIS en bättre förståelse för vilka yttranden som passar avsikten.

## <a name="import-example-app-and-clone-to-new-version"></a>Importera exempelapp och klona till ny version

Använd följande steg:

1.  Ladda ner och spara [appen JSON-fil](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-batchtest-HumanResources.json?raw=true).

1. Importera JSON till en ny app till [luis-portalen för förhandsversionen](https://preview.luis.ai).

1. I avsnittet **Hantera** går du till fliken **Versioner**, klonar versionen och ger den namnet `patterns`. Kloning är ett bra sätt att prova på olika LUIS-funktioner utan att påverka originalversionen. Eftersom versionsnamnet används i webbadressen får namnet inte innehålla några tecken som är ogiltiga i webbadresser.

## <a name="create-new-intents-and-their-utterances"></a>Skapa nya avsikter och deras yttranden

1. Välj **Bygg** i navigeringsfältet.

1. På sidan **Avsikter** väljer du **+ Skapa** för att skapa en ny avsikt.

1. Ange `OrgChart-Manager` i popup-dialogrutan och välj sedan **Done** (Klar).

    ![Skapa ett nytt popup-fönster för meddelanden](media/luis-tutorial-pattern/hr-create-new-intent-popup.png)

1. Lägg till exempel på yttranden i avsikten. Dessa yttranden är inte _exakt_ likadana men har ett mönster som kan extraheras.

    |Exempel på yttranden|
    |--|
    |`Who is John W. Smith the subordinate of?`|
    |`Who does John W. Smith report to?`|
    |`Who is John W. Smith's manager?`|
    |`Who does Jill Jones directly report to?`|
    |`Who is Jill Jones supervisor?`|

    Oroa dig inte om keyPhrase-entiteten är märkt i yttranden för avsikten i stället för medarbetarentiteten. Båda förutsägs korrekt i rutan Text och i slutpunkten.

1. Välj **Intents** (Avsikter) i det vänstra navigeringsfältet.

1. Välj **+ Skapa** om du vill skapa en ny avsikt. Ange `OrgChart-Reports` i popup-dialogrutan och välj sedan **Done** (Klar).

1. Lägg till exempel på yttranden i avsikten.

    |Exempel på yttranden|
    |--|
    |`Who are John W. Smith's subordinates?`|
    |`Who reports to John W. Smith?`|
    |`Who does John W. Smith manage?`|
    |`Who are Jill Jones direct reports?`|
    |`Who does Jill Jones supervise?`|

### <a name="caution-about-example-utterance-quantity"></a>Varning om kvantiteten för exempeluttryck

[!INCLUDE [Too few examples](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]

### <a name="train-the-app-before-testing-or-publishing"></a>Träna appen innan du testar eller publicerar

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

### <a name="publish-the-app-to-query-from-the-endpoint"></a>Publicera appen att fråga från slutpunkten

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

### <a name="get-intent-and-entities-from-endpoint"></a>Hämta avsikter och entiteter från slutpunkten

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Gå till slutet av URL:en i adressen och ange `Who is the boss of Jill Jones?`. Den sista frågesträngparametern `query`är uttrycket .

    ```json
    {
        "query": "Who is the boss of Jill Jones?",
        "prediction": {
            "topIntent": "OrgChart-Manager",
            "intents": {
                "OrgChart-Manager": {
                    "score": 0.313054234
                },
                "OrgChart-Reports": {
                    "score": 0.2462688
                },
                "EmployeeFeedback": {
                    "score": 0.0488328524
                },
                "GetJobInformation": {
                    "score": 0.0156933
                },
                "MoveEmployee": {
                    "score": 0.011265873
                },
                "Utilities.StartOver": {
                    "score": 0.003065792
                },
                "Utilities.Stop": {
                    "score": 0.00300148362
                },
                "Utilities.Cancel": {
                    "score": 0.00271081156
                },
                "None": {
                    "score": 0.00212835032
                },
                "ApplyForJob": {
                    "score": 0.0020669254
                },
                "Utilities.Confirm": {
                    "score": 0.00200891262
                },
                "FindForm": {
                    "score": 0.00194145238
                },
                "Utilities.Help": {
                    "score": 0.00182301877
                }
            },
            "entities": {
                "keyPhrase": [
                    "boss of Jill Jones"
                ],
                "Employee": [
                    [
                        "Employee-45612"
                    ]
                ],
                "$instance": {
                    "keyPhrase": [
                        {
                            "type": "builtin.keyPhrase",
                            "text": "boss of Jill Jones",
                            "startIndex": 11,
                            "length": 18,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "Employee": [
                        {
                            "type": "Employee",
                            "text": "Jill Jones",
                            "startIndex": 19,
                            "length": 10,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```

Lyckades frågan? För den här träningscykeln lyckades den. Poängen för de två bästa avsikter är nära men den högsta avsikten är inte signifikant hög (över 60%) och är inte tillräckligt långt över nästa avsikt poäng.

Eftersom LUIS-träningen inte är exakt samma varje gång (det finns lite variationer) kan dessa två poäng vara omvända i nästa träningscykel. Resultatet är att fel avsikt kan returneras.

Använd mönster för att göra den korrekta avsiktens poäng betydligt högre procentuellt och längre ifrån den näst högsta poängen.

Lämna det här andra webbläsarfönstret öppet. Du använder det igen senare i den här kursen.

## <a name="template-utterances"></a>Mallyttranden
På grund av Human Resources-domänens natur finns det några vanliga sätt att fråga om medarbetares relationer i organisationer. Ett exempel:

|Yttranden|
|--|
|`Who does Jill Jones report to?`|
|`Who reports to Jill Jones?`|

Dessa yttranden ligger för nära varandra för att fastställa den kontextuella unikheten hos respektive yttrande utan att ge många yttrandeexmpel. Genom att lägga till ett mönster för en avsikt lär sig LUIS vanliga yttrandemönster för en avsikt utan att ge många yttrandeexempel.

Exempel på mallyttranden för den här avsikten är:

|Exempel på mallyttranden|syntaxbetydelse|
|--|--|
|`Who does {Employee} report to[?]`|Utbytbara`{Employee}`<br>Ignorera`[?]`|
|`Who reports to {Employee}[?]`|Utbytbara`{Employee}`<br>Ignorera`[?]`|

Syntaxen `{Employee}` markerar entitetsplatsen i mallyttrandet och vilken entitet det är. Den valfria `[?]`syntaxen, markerar ord eller skiljetecken som är valfri. LUIS matchar yttrandet, ignorerar den valfria texten inom hakparentes.

Syntaxen ser ut som ett reguljärt uttryck, men det är inte ett reguljärt uttryck. Endast syntax inom klamrar, `{}`, och hakparentes, `[]`, stöds. De kan kapslas upp till två nivåer.

För att ett mönster ska kunna matchas till ett yttrande måste entiteterna i yttrandet matcha entiteterna i mallyttrandet först. Det innebär att entiteterna måste ha tillräckligt med exempel i exempelyttranden med en hög grad av förutsägelse innan mönster med entiteter lyckas. Men mallen hjälper inte att förutsäga entiteter, bara avsikter.

**Med mönster kan du ge färre exempelyttranden, men om entiteterna inte identifieras matchar inte mönstret.**

### <a name="add-the-patterns-for-the-orgchart-manager-intent"></a>Lägg till mönstren för orgchart-manager-avsikten

1. Välj **Build** (Skapa) i huvudmenyn.

1. I den vänstra navigeringen, under **Improve app performance** (Förbättra appens prestanda) väljer du **Patterns** (Mönster) i den vänstra navigeringen.

1. Välj avsikten **OrgChart-Manager** och ange sedan följande mallyttranden:

    |Mallyttranden|
    |:--|
    |`Who is {Employee} the subordinate of[?]`|
    |`Who does {Employee} report to[?]`|
    |`Who is {Employee}['s] manager[?]`|
    |`Who does {Employee} directly report to[?]`|
    |`Who is {Employee}['s] supervisor[?]`|
    |`Who is the boss of {Employee}[?]`|

1. När du fortfarande är på sidan Mönster väljer du avsikten **Med OrgChart-Reports** och anger sedan följande mallyttranden:

    |Mallyttranden|
    |:--|
    |`Who are {Employee}['s] subordinates[?]`|
    |`Who reports to {Employee}[?]`|
    |`Who does {Employee} manage[?]`|
    |`Who are {Employee} direct reports[?]`|
    |`Who does {Employee} supervise[?]`|
    |`Who does {Employee} boss[?]`|

### <a name="query-endpoint-when-patterns-are-used"></a>Frågeslutpunkt där mönster används

Nu när mönstren läggs till i appen tränar, tränar, publicerar och frågar appen vid slutpunkten för förutsägelsekörning.

1. Välj **Tåg**. När utbildningen är klar väljer du **Publicera** och väljer **produktionsplatsen** och väljer sedan **Klar**.

1. När publiceringen är klar växlar du tillbaka webbläsarflikarna till url-fliken för slutpunkt.

1. Gå till slutet av URL:en i adressen och ange `Who is the boss of Jill Jones?` som yttrande. Den sista frågesträngparametern är `query`.

    ```json
    {
        "query": "Who is the boss of Jill Jones?",
        "prediction": {
            "topIntent": "OrgChart-Manager",
            "intents": {
                "OrgChart-Manager": {
                    "score": 0.999997854
                },
                "OrgChart-Reports": {
                    "score": 6.13748343E-05
                },
                "EmployeeFeedback": {
                    "score": 8.052567E-06
                },
                "GetJobInformation": {
                    "score": 1.18197136E-06
                },
                "MoveEmployee": {
                    "score": 7.65549657E-07
                },
                "None": {
                    "score": 3.975E-09
                },
                "Utilities.StartOver": {
                    "score": 1.53E-09
                },
                "Utilities.Confirm": {
                    "score": 1.38181822E-09
                },
                "Utilities.Help": {
                    "score": 1.38181822E-09
                },
                "Utilities.Stop": {
                    "score": 1.38181822E-09
                },
                "Utilities.Cancel": {
                    "score": 1.25833333E-09
                },
                "FindForm": {
                    "score": 1.15384613E-09
                },
                "ApplyForJob": {
                    "score": 5.26923061E-10
                }
            },
            "entities": {
                "keyPhrase": [
                    "boss of Jill Jones"
                ],
                "Employee": [
                    [
                        "Employee-45612"
                    ]
                ],
                "$instance": {
                    "keyPhrase": [
                        {
                            "type": "builtin.keyPhrase",
                            "text": "boss of Jill Jones",
                            "startIndex": 11,
                            "length": 18,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "Employee": [
                        {
                            "type": "Employee",
                            "text": "Jill Jones",
                            "startIndex": 19,
                            "length": 10,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```

Avsikten förutsägelse är nu betydligt mer självsäker och den näst högsta avsikten poäng är betydligt lägre. Dessa två avsikter kommer inte att flip-flop när du tränar.

### <a name="working-with-optional-text-and-prebuilt-entities"></a>Arbeta med valfri text och fördefinierade entiteter

Föregående mallyttranden för mönster i den här kursen hade några exempel på valfri text, som possessiv användning av bokstaven s `'s` (se exemplen på engelska) och användningen av frågetecken, `?`. Anta att du måste tillåta aktuella och framtida datum i uttryckstexten.

Exempelyttranden är:

|Avsikt|Exempelyttranden med valfri text och fördefinierade entiteter|
|:--|:--|
|OrgChart-Manager (Organisationsschema-Chef)|`Who was Jill Jones manager on March 3?`|
|OrgChart-Manager (Organisationsschema-Chef)|`Who is Jill Jones manager now?`|
|OrgChart-Manager (Organisationsschema-Chef)|`Who will be Jill Jones manager in a month?`|
|OrgChart-Manager (Organisationsschema-Chef)|`Who will be Jill Jones manager on March 3?`|

Alla dessa exempel använder ett verbtempus, `was`, `is`, `will be` samt ett datum, `March 3`, `now` och `in a month`, som LUIS behöver för att förutsäga korrekt. Observera att de två sista exemplen i tabellen `in` `on`använder nästan samma text förutom och .

Exempel på mallyttranden som tillåter den här valfria informationen:

|Avsikt|Exempelyttranden med valfri text och fördefinierade entiteter|
|:--|:--|
|OrgChart-Manager (Organisationsschema-Chef)|`who was {Employee}['s] manager [[on]{datetimeV2}?`]|
|OrgChart-Manager (Organisationsschema-Chef)|`who is {Employee}['s] manager [[on]{datetimeV2}?]`|


Användningen av valfri syntax inom hakparentes, `[]`, gör den här valfria texten enkel att lägga till i mallyttrandet och kan kapslas upp till en andra nivå, `[[]]`, och innehåller entiteter eller text.


**Fråga: Varför är `w` alla bokstäver, den första bokstaven i varje mall yttrande, gemener? Borde de inte vara valfria versaler eller gemener?** Yttrandet som skickas till frågeslutpunkten, av frågeslutpunkten, konverteras till gemener. Mallyttrandet kan vara versaler eller gemener och slutpunktsyttrandet kan också vara antingen eller. Jämförelsen görs alltid efter konverteringen till gemener.

**Fråga: Varför är inte fördefinierat tal ett mallyttrande om 3 mars förutsägs både som talet `3` och datumet `March 3`?** Mallyttrandet använder kontextuellt ett datum, antingen bokstavligen som i `March 3` eller abstraheras som `in a month`. Ett datum innehåller ett tal men ett tal kanske inte nödvändigtvis ses som ett datum. Använd alltid entiteten som bäst representerar den typ du vill ska returneras i resultatet för förutsägelse-JSON.

**Fråga: Hur är det med dåligt uttryckta yttranden som `Who will {Employee}['s] manager be on March 3?`?** Grammatiskt olika verbtempus som det här där `will` och `be` är åtskilda måste vara ett nytt mallyttrande. Det befintliga mallyttrandet kommer inte att matcha det. Avsikten för yttrandet har inte ändrats men ordets placering i yttrandet har ändrats. Den här ändringen påverkar förutsägelsen i LUIS. Du kan [gruppera och eller](#use-the-or-operator-and-groups) verb-tempus för att kombinera dessa yttranden.

**Kom ihåg: entiteter hittas först, sedan matchas mönstret.**

### <a name="edit-the-existing-pattern-template-utterance"></a>Redigera det befintliga mallyttrandet för mönster

1. I luis-portalen för förhandsversionen väljer du **Bygg** i den övre menyn och väljer sedan **Mönster** på den vänstra menyn.

1. Sök efter den befintliga `Who is {Employee}['s] manager[?]`mallutseendet och välj ellipsen (***...***) till höger och välj sedan **Redigera** på popup-menyn.

1. Ändra mallyttrandet till: `who is {Employee}['s] manager [[on]{datetimeV2}?]`

### <a name="add-new-pattern-template-utterances"></a>Lägga till nya mallyttranden för mönster

1. Lägg till flera nya mallyttranden för mönster när du fortfarande är i avsnittet **Patterns** (Mönster) i **Build** (Skapa). Välj **OrgChart-Manager** i den nedrullningsbara menyn Intent (Avsikt) och ange vart och ett av följande yttranden:

    |Avsikt|Exempelyttranden med valfri text och fördefinierade entiteter|
    |--|--|
    |OrgChart-Manager (Organisationsschema-Chef)|`who was {Employee}['s] manager [[on]{datetimeV2}?]`|
    |OrgChart-Manager (Organisationsschema-Chef)|`who will be {Employee}['s] manager [[in]{datetimeV2}?]`|
    |OrgChart-Manager (Organisationsschema-Chef)|`who will be {Employee}['s] manager [[on]{datetimeV2}?]`|

2. Välj **Träna** i navigeringsfältet för att träna appen.

3. När träningen är klar väljer du **Testa** högst upp på panelen för att öppna testpanelen.

4. Ange flera testyttranden för att verifiera att mönstret matchas och avsiktspoängen är mycket höga.

    När du har angett det första yttrandet väljer du **Inspect** (Granska) under resultatet så att du kan se alla förutsägelseresultat. Varje uttryck ska ha avsikten **OrgChart-Manager** och extrahera värdena för entiteterna för medarbetare och datetimeV2.

    |Yttrande|
    |--|
    |`Who will be Jill Jones manager`|
    |`who will be jill jones's manager`|
    |`Who will be Jill Jones's manager?`|
    |`who will be Jill jones manager on March 3`|
    |`Who will be Jill Jones manager next Month`|
    |`Who will be Jill Jones manager in a month?`|

Alla dessa yttranden hittade entiteterna inuti, och därför matchar de samma mönster, och har höga förutsägelsepoäng. Du har lagt till några mönster som matchar många varianter av yttranden. Du behövde inte lägga till några exempel yttranden i avsikt att ha mall yttrande arbete i mönstret.

Denna användning av mönster som:
* högre förutsägelsepoäng
* med samma exempel yttranden i avsikt
* med bara några welll-konstruerade mall yttranden i mönstret

### <a name="use-the-or-operator-and-groups"></a>Använda operatorn ELLER och grupper

Flera av de tidigare mallyttrandena är mycket nära. Använd **syntaxen för gruppen** `()` och **ELLER** `|` för att minska mallyttrandena.

Följande 2 mönster kan kombineras till ett `()` enda `|` mönster med hjälp av gruppen och ELLER-syntaxen.

|Avsikt|Exempelyttranden med valfri text och fördefinierade entiteter|
|--|--|
|OrgChart-Manager (Organisationsschema-Chef)|`who will be {Employee}['s] manager [[in]{datetimeV2}?]`|
|OrgChart-Manager (Organisationsschema-Chef)|`who will be {Employee}['s] manager [[on]{datetimeV2}?]`|

Den nya mallutseendet kommer att vara:

`who ( was | is | will be ) {Employee}['s] manager [([in]|[on]){datetimeV2}?]`.

Detta använder en **grupp** runt det `in` nödvändiga `on` verbet spänt och det valfria och med en **eller** pipe mellan dem.

1. Välj filtret **OrgChart-Manager** på sidan **Mönster.** Begränsa listan genom `manager`att söka efter .

1. Behåll en version av mallutlysningen (för att redigera i nästa steg) och ta bort de andra varianterna.

1. Ändra mallyttrandet till: 

    `who ( was | is | will be ) {Employee}['s] manager [([in]|[on]){datetimeV2}?]`

2. Välj **Träna** i navigeringsfältet för att träna appen.

3. När träningen är klar väljer du **Testa** högst upp på panelen för att öppna testpanelen.

    Använd testfönstret för att testa versioner av uttrycket:

    |Yttranden som ska skrivas in i testfönstret|
    |--|
    |`Who is Jill Jones manager this month`|
    |`Who is Jill Jones manager on July 5th`|
    |`Who was Jill Jones manager last month`|
    |`Who was Jill Jones manager on July 5th`|
    |`Who will be Jill Jones manager in a month`|
    |`Who will be Jill Jones manager on July 5th`|

Genom att använda mer mönstersyntax kan du minska antalet mallyttranden som du måste behålla i appen, samtidigt som du har en hög förutsägelsepoäng.

### <a name="use-the-utterance-beginning-and-ending-anchors"></a>Använda början- och slutankringsankringsankare för uttryck

Mönstersyntaxen ger början- och slutsynpunktsankaresyntax för en caret. `^` Början och uttryck ankare kan användas tillsammans för att rikta mycket specifika och eventuellt bokstavlig uttryck eller användas separat för att rikta avsikter.

## <a name="using-patternany-entity"></a>Med Pattern.any-entitet

Med entiteten pattern.any kan du söka efter friformsdata där formuleringen i entiteten gör det svårt att fastställa var entiteten slutar baserat på resten av yttrandet.

Den här personalappen hjälper anställda att hitta företagsformulär.

|Yttrande|
|--|
|Var finns **HRF-123456**?|
|Vem skapade **HRF 123234**?|
|Har **HRF 456098** publicerats på franska?|

Varje formulär har dock både ett formaterat namn, som används i tabellen ovan, samt ett eget namn, till exempel `Request relocation from employee new to the company 2018 version 5`.

Yttranden med det egna formulärnamnet ser ut så här:

|Yttrande|
|--|
|Var finns **Begär flytt från medarbetare som är ny i företaget 2018 version 5**?|
|Vem skapade **”Begär flytt från medarbetare som är ny i företaget 2018 version 5”**?|
|Har **Begära flytt från medarbetare som är ny i företagets 2018 version 5** publicerats på franska?|

Den varierande längden innehåller ord som kan förvirra LUIS om var entiteten slutar. Genom att använda en Pattern.any-entitet i ett mönster kan du ange formulärnamnets början och slut, så att LUIS kan extrahera formulärnamnet korrekt.

|Exempelmall för yttranden|
|--|
|Var finns {FormName}[?]|
|Vem skapade {FormName}[?]|
|Har {FormName} publicerats på franska[?]|

### <a name="add-example-utterances-with-patternany"></a>Lägg till exempelyttranden med Pattern.any

1. Välj **Skapa** från det övre navigeringsfältet, och välj sedan **Avsikter** i det vänstra navigeringsfältet.

1. Välj **FindForm** från listan över avsikter.

1. Lägg till några exempel på yttranden:

    |Exempel på yttrande|Formulärnamn|
    |--|--|
    |Var finns formuläret **Vad gör du vid eldsvåda i laboratoriet** och vem behöver signera det när jag har läst det?|Vad ska man göra när en brand bryter ut i labbet
    |Var finns **Begär flytt från medarbetare som är ny i företaget** på servern?|Begär flytt från medarbetare till företaget|
    |Vem skapade "**Hälsofrågor på huvudcampus**" och vilken version är mest aktuell?|Hälso- och friskvårdsförfrågningar på huvudcampuset|
    |Jag letar efter formuläret med namnet "**Begäran om kontorsflytt inklusive fysiska tillgångar**". |Office flytta begäran inklusive fysiska tillgångar|

    Utan en Pattern.any-entitet skulle det vara svårt för LUIS för att förstå var formulärrubriken slutar på grund av de många varianterna på formulärnamn.

### <a name="create-a-patternany-entity"></a>Skapa en Pattern.any-entitet
Entiteten Pattern.any extraherar entiteter med olika längd. Det fungerar bara i ett mönster eftersom mönstret markerar början och slutet av entiteten med syntax.

1. Välj **Entiteter** i det vänstra navigeringsfältet.

1. Välj **+ Skapa**, `FormName`ange namnet och välj **Pattern.any** som typ. Välj **Skapa**.

### <a name="add-a-pattern-that-uses-the-patternany"></a>Lägga till ett mönster som använder Pattern.any

1. Välj **Mönster** i det vänstra navigeringsfönstret.

1. Välj avsikten **FindForm**.

1. Ange följande mallyttranden, som använder den nya entiteten:

    |Mallyttranden|
    |--|
    |`Where is the form ["]{FormName}["] and who needs to sign it after I read it[?]`|
    |`Where is ["]{FormName}["] on the server[?]`|
    |`Who authored ["]{FormName}["] and what is the most current version[?]`|
    |`I'm looking for the form named ["]{FormName}["][.]`|

1. Träna appen.

### <a name="test-the-new-pattern-for-free-form-data-extraction"></a>Testa det nya mönstret för extrahering av friformsdata
1. Välj **Testa** från det översta fältet för att öppna testpanelen.

1. Fyll i följande yttrande:

    `Where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

1. Välj **Granska** under resultatet för att se testresultaten för entitet och avsikt.

    Entiteten `FormName` hittas först och sedan mönstret, som fastställer avsikten. Om du har ett testresultat där entiteterna inte identifieras och mönstret därmed inte hittas, behöver du lägga till fler exempelyttranden för avsikten (inte mönstret).

1. Stäng testpanelen genom att välja knappen **Testa** i det övre navigeringsfältet.

### <a name="using-an-explicit-list"></a>Använda en explicit lista

Om du tycker att mönstret extraherar entiteterna felaktigt när det innehåller en Pattern.any-entitet, kan du åtgärda problemet med hjälp av en [explicit lista](reference-pattern-syntax.md#explicit-lists).

## <a name="what-did-this-tutorial-accomplish"></a>Vad gjorde den här guiden åstadkomma?

Den här självstudien lade till mönster för att hjälpa LUIS att förutsäga avsikten med en betydligt högre poäng utan att behöva lägga till fler exempelyttranden. Markera entiteter och ignorerbar text tillåtna LUIS för att tillämpa mönstret på en mängd olika yttranden.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg


> [!div class="nextstepaction"]
> [Lär dig hur du använder roller med ett mönster](luis-tutorial-pattern.md)
