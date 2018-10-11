---
title: 'Självstudie 7: Enkel entitet med fraslista i LUIS'
titleSuffix: Azure Cognitive Services
description: Extrahera data från ett uttryck via maskininlärning
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: e0cdda629ddded121a424af61377c04ee8d958d3
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2018
ms.locfileid: "48867552"
---
# <a name="tutorial-7-extract-names-with-simple-entity-and-phrase-list"></a>Självstudie 7: Extrahera namn med en enkel entitet och en fraslista

I den här självstudien extraherar du data med jobbnamn via maskininlärning ur ett yttrande med hjälp av en **enkel** entitet. Om du vill öka noggrannheten i extraheringen lägger du till en fraslista med villkor som är specifika för den enkla entiteten.

I den här självstudien lägger du till en ny enkel entitet för att extrahera jobbnamnet. Syftet med den enkla entiteten är att lära LUIS vad ett jobbnamn är och var det kan hittas i ett yttrande. Den del av yttrandet som utgör jobbnamnet kan variera mellan olika yttranden baserat på ordval och yttrandets längd. LUIS behöver exempel på jobbnamn i alla typer av avsikter där jobbnamn används.  

En enkel entitet passar bra till den här typen av data i följande fall:

* Data har ett enda koncept.
* Data är inte välformade som i reguljära uttryck.
* Data är inte enhetliga som i en fördefinierad entitet med telefonnummer eller data.
* Data kan inte matchas exakt mot en lista med kända ord, som i en listentitet.
* Data innehåller inte andra dataobjekt, som med sammansatta entiteter eller en hierarkisk entitet.

**I den här självstudiekursen får du lära du dig att:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Använda en befintlig självstudieapp
> * Lägga till en enkel entitet för att extrahera jobb från appen
> * Lägga till en fraslista för att förbättra extraheringen av jobbord
> * Träna 
> * Publicera 
> * Hämta avsikter och entiteter från en slutpunkt

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Använda en befintlig app

Fortsätt med appen du skapade i föregående självstudie med namnet **HumanResources**. 

Om du inte har appen HumanResources från föregående självstudie gör du så här:

1.  Ladda ned och spara [JSON-filen för appen](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-composite-HumanResources.json).

2. Importera JSON-koden till en ny app.

3. I avsnittet **Hantera** går du till fliken **Versioner**, klonar versionen och ger den namnet `simple`. Kloning är ett bra sätt att prova på olika LUIS-funktioner utan att påverka originalversionen. Eftersom versionsnamnet används i webbadressen får namnet inte innehålla några tecken som är ogiltiga i webbadresser.

## <a name="simple-entity"></a>Enkel entitet
Den enkla entiteten identifierar ett datakoncept i ord eller fraser.

Ta till exempel följande yttranden från en chattrobot:

|Yttrande|Jobbnamn som kan extraheras|
|:--|:--|
|Jag vill söka det nya jobbet inom redovisning.|redovisning|
|Jag skickar härmed mitt cv för att söka tjänsten inom teknik.|teknik|
|Fyll i ansökan till jobbet 123456|123456|

Jobbnamnet är svårt att urskilja eftersom ett namn kan vara ett substantiv, verb eller en fras med flera ord. Exempel:

|Jobb|
|--|
|tekniker|
|datatekniker|
|erfaren datatekniker|
|ansvara för en teknikgrupp |
|flygledare|
|fordonsförare|
|ambulansförare|
|skötare|
|maskinoperatör|
|montör|

Den här LUIS-appen har jobbnamn i flera avsikter. Genom att märka ut orden i en avsikts alla yttranden lär sig LUIS mer om vad ett jobbnamn är och var det kan förekomma i yttrandena.

När entiteterna har märkts ut i exempelyttrandena är det viktigt att du lägger till en fraslista som förbättrar extraheringen. En fraslista används **inte** för exakta matchningar och behöver inte innehålla alla möjliga värden du förväntar dig. 

1. [!include[Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. På sidan **Intents** (Avsikter) väljer du avsikten **ApplyForJob**. 

3. I yttrandet `I want to apply for the new accounting job` väljer du `accounting` och anger `Job` i det översta fältet på snabbmenyn. Välj sedan **Create new entity** (Skapa ny entitet) på snabbmenyn. 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png "Skärmbild på LUIS-appen med avsikten ApplyForJob och stegen för att skapa en entitet markerade")](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png#lightbox)

4. Kontrollera entitetsnamnet och -typen i popup-fönstret och välj **Done** (Klar).

    ![Dialogruta för att skapa en enkel entitet med jobbnamn och entitetstyp](media/luis-quickstart-primary-and-secondary-data/hr-create-simple-entity-popup.png)

5. I yttrandet, `Submit resume for engineering position`, märker du ordet `engineering` som en jobbentitet. Välj ordet `engineering` och välj sedan **Job** (Jobb) i snabbmenyn. 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-label-simple-entity.png "Skärmbild på LUIS-appen med markerad märkning av jobbentitet")](media/luis-quickstart-primary-and-secondary-data/hr-label-simple-entity.png#lightbox)

    Alla yttranden är märkta, men fem räcker inte för att lära LUIS tillräckligt många jobbrelaterade ord och fraser. För jobb med nummervärde behövs det inte fler exempel eftersom en entitet för reguljära uttryck används. För jobb som består av ord eller fraser krävs det minst 15 exempel till. 

6. Lägg till fler yttranden och märk jobborden eller -fraserna som **jobbentiteter**. Jobbtyperna är allmänna för anställning via ett anställningsföretag. Om du behöver jobb för en viss bransch ska jobborden spegla detta. 

    |Yttrande|Jobbentitet|
    |:--|:--|
    |Jag söker jobbet som programansvarig inom FoU|Programansvarig|
    |Här är min ansökan till kocktjänsten.|kocktjänst|
    |Mitt cv för att söka jobbet som lägerledare är bifogat.|lägerledare|
    |Jag skickar härmed mitt cv för att söka jobbet som administrativ assistent.|administrativ assistent|
    |Jag vill söka chefstjänsten inom försäljning.|chefstjänst, försäljning|
    |Jag söker härmed det nya jobbet inom redovisning.|redovisning|
    |Min ansökan till jobbet som diskare ingår.|diskare|
    |Jag söker jobbet som takläggare och snickare.|takläggare, snickare|
    |Mitt cv för att söka jobbet som bussförare bifogas härmed.|bussförare|
    |Jag är en legitimerad sjuksköterska. Härmed bifogas mitt cv.|legitimerad sjuksköterska|
    |Jag skickar härmed mina ansökningshandlingar för jobbet som lärare som fanns i tidningen.|lärare|
    |Jag skickar härmed mitt cv för att söka jobbet som varuplockare.|varuplockare|
    |Söka jobbet som kakelsättare.|kakelsättning|
    |Mitt cv är bifogat för tjänsten som landskapsarkitekt.|landskapsarkitekt|
    |Mitt curriculum vitae är bifogat för att söka tjänsten som professor i biologi.|professor i biologi|
    |Jag vill ansöka om jobbet som fotograf.|fotograf|git 

## <a name="label-entity-in-example-utterances"></a>Märka ut entiteter i exempelyttranden

När du _märker ut_ entiteten visar du för LUIS var entiteten förekommer i exempelyttrandena.

1. Välj **Intents** (Avsikter) på den vänstra menyn.

2. Välj **GetJobInformation** i listan med avsikter. 

3. Märk jobben i exempelyttranden:

    |Yttrande|Jobbentitet|
    |:--|:--|
    |Finns det möjlighet att få arbeta med databaser?|databaser|
    |Söker efter ett nytt arbete inom redovisning|redovisning|
    |Finns det lediga tjänster för erfarna tekniker?|erfarna tekniker|

    Det finns andra exempelyttranden som inte innehåller jobbrelaterade ord.

## <a name="train"></a>Träna

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Publicera

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Hämta avsikter och entiteter från slutpunkten 

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Gå till slutet av URL:en i adressen och ange `Here is my c.v. for the programmer job`. Den sista frågesträngsparametern är `q`, yttrande**frågan**. Det här yttrandet är inte samma som någon av de märkta yttrandena. Därför är det ett bra test och bör returnera yttrandena `ApplyForJob`.

    ```JSON
    {
      "query": "Here is my c.v. for the programmer job",
      "topScoringIntent": {
        "intent": "ApplyForJob",
        "score": 0.9826467
      },
      "intents": [
        {
          "intent": "ApplyForJob",
          "score": 0.9826467
        },
        {
          "intent": "GetJobInformation",
          "score": 0.0218927357
        },
        {
          "intent": "MoveEmployee",
          "score": 0.007849265
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.00349470088
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.00348804821
        },
        {
          "intent": "None",
          "score": 0.00319909188
        },
        {
          "intent": "FindForm",
          "score": 0.00222647213
        },
        {
          "intent": "Utilities.Help",
          "score": 0.00211193133
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.00172086991
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.00138010911
        }
      ],
      "entities": [
        {
          "entity": "programmer",
          "type": "Job",
          "startIndex": 24,
          "endIndex": 33,
          "score": 0.5230502
        }
      ]
    }
    ```
    
    LUIS hittade rätt avsikt, **ApplyForJob**, och extraherade rätt entitet, **Job**, med värdet `programmer`.


## <a name="names-are-tricky"></a>Namn kan vara svårhanterade
LUIS-appen hittade rätt avsikt med hög exakthet och extraherade jobbnamnet, men namn är svårare. Prova yttrandet `This is the lead welder paperwork`.  

I följande JSON svarar LUIS-appen med rätta avsikten `ApplyForJob`, men extraherar inte jobbnamnet `lead welder`. 

```JSON
{
  "query": "This is the lead welder paperwork.",
  "topScoringIntent": {
    "intent": "ApplyForJob",
    "score": 0.468558252
  },
  "intents": [
    {
      "intent": "ApplyForJob",
      "score": 0.468558252
    },
    {
      "intent": "GetJobInformation",
      "score": 0.0102701457
    },
    {
      "intent": "MoveEmployee",
      "score": 0.009442534
    },
    {
      "intent": "Utilities.StartOver",
      "score": 0.00639619166
    },
    {
      "intent": "None",
      "score": 0.005859333
    },
    {
      "intent": "Utilities.Cancel",
      "score": 0.005087704
    },
    {
      "intent": "Utilities.Stop",
      "score": 0.00315379258
    },
    {
      "intent": "Utilities.Help",
      "score": 0.00259344373
    },
    {
      "intent": "FindForm",
      "score": 0.00193389168
    },
    {
      "intent": "Utilities.Confirm",
      "score": 0.000420796918
    }
  ],
  "entities": []
}
```

Eftersom ett namn kan vara vad som helst förutsäger LUIS-appen entiteter mer korrekt om den har en lista med ordfraser för att förbättra extraheringen.

## <a name="to-boost-signal-add-phrase-list"></a>Lägg till en fraslista för att förbättra extraheringen

Öppna filen [jobs-phrase-list.csv](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/job-phrase-list.csv) från LUIS-Samples-GitHub-lagringsplatsen. Listan innehåller mer än tusen jobbrelaterade ord och fraser. Titta igenom listan efter ord som kan vara till nytta för dig. Om ord eller fraser som du behöver inte finns med i listan kan du lägga till egna.

1. I avsnittet **Build** (Skapa) i LUIS-appen väljer du **Phrase lists** (Fraslistor) under menyn **Improve app performance** (Förbättra appens prestanda).

2. Välj **Create new phrase list** (Skapa ny fraslista). 

3. Namnge den nya fraslistan `Job` och kopiera listan från jobs-phrase-list.csv till textrutan **Values** (Värden). Välj Retur. 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png "Skärmbild på dialogrutan för att skapa en ny fraslista")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png#lightbox)

    Om du vill lägga till fler ord i fraslistan kan du gå igenom **Related Values** (Relaterade värden) och lägga till det som behövs. 

4. Välj **Save** (Spara) så aktiveras fraslistan.

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png "Skärmbild på dialogrutan för att skapa en ny fraslista med ord i fraslistans värderuta")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png#lightbox)

5. [Träna](#train) och [publicera](#publish) appen igen för att använda fraslistan.

6. Skicka om frågan till slutpunkten med samma yttrande: `This is the lead welder paperwork.`

    JSON-svaret innehåller den extraherade entiteten:

    ```JSON
    {
        "query": "This is the lead welder paperwork.",
        "topScoringIntent": {
            "intent": "ApplyForJob",
            "score": 0.920025647
        },
        "intents": [
            {
            "intent": "ApplyForJob",
            "score": 0.920025647
            },
            {
            "intent": "GetJobInformation",
            "score": 0.003800706
            },
            {
            "intent": "Utilities.StartOver",
            "score": 0.00299335527
            },
            {
            "intent": "MoveEmployee",
            "score": 0.0027167045
            },
            {
            "intent": "None",
            "score": 0.00259556063
            },
            {
            "intent": "FindForm",
            "score": 0.00224019377
            },
            {
            "intent": "Utilities.Stop",
            "score": 0.00200693542
            },
            {
            "intent": "Utilities.Cancel",
            "score": 0.00195913855
            },
            {
            "intent": "Utilities.Help",
            "score": 0.00162656687
            },
            {
            "intent": "Utilities.Confirm",
            "score": 0.0002851904
            }
        ],
        "entities": [
            {
            "entity": "lead welder",
            "type": "Job",
            "startIndex": 12,
            "endIndex": 22,
            "score": 0.8295959
            }
        ]
    }
    ```

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien använder appen Human Resources en maskininlärd enkel entitet till att hitta jobbnamn i yttranden. Eftersom jobbnamn kan vara så olika ord och fraser behöver appen en fraslista för att kunna hitta orden med jobbnamnen. 

> [!div class="nextstepaction"]
> [Lägg till en fördefinierad keyPhrase-entitet](luis-quickstart-intent-and-key-phrase.md)