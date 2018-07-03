---
title: Självstudie om att skapa en LUIS-app för att extrahera data – Azure | Microsoft Docs
description: I den här självstudien skapar du en enkel LUIS-app med hjälp av avsikter och en enkel entitet för att extrahera maskininlärningsdata.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 06/26/2018
ms.author: v-geberr
ms.openlocfilehash: b718ed505babd2df6487aecd3a87f17590aef2b9
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37061255"
---
# <a name="tutorial-create-app-that-uses-simple-entity"></a>Självstudie: skapa app som använder enkel entitet
I den här självstudien skapar du en app som visar hur det går till att extrahera maskininlärningsdata från ett yttrande med hjälp av entiteten **Simple** (Enkel).

<!-- green checkmark -->
> [!div class="checklist"]
> * Förstå enkla entiteter 
> * Skapa en ny LUIS-app för HR-domänen (Human Resources) 
> * Lägga till en enkel entitet för att extrahera jobb från appen
> * Träna och publicera app
> * Skicka en fråga till appens slutpunkt för att se LUIS JSON-svar
> * Lägga till en fraslista för att förbättra extraheringen av jobbord
> * Träna, publicera appen och skicka om fråga till slutpunkten

För den här artikeln behöver du ett kostnadsfritt [LUIS-konto](luis-reference-regions.md#luis-website) för att kunna redigera LUIS-programmet.

## <a name="before-you-begin"></a>Innan du börjar
Om du inte har appen Human Resources (Personalfrågor) från självstudien om [hierarchical entity](luis-quickstart-intent-and-hier-entity.md) (hierarkisk entitet) ska du [importera](create-new-app.md#import-new-app) JSON till en ny app på [LUIS-webbplatsen](luis-reference-regions.md#luis-website). Importeringsappen finns på [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-hier-HumanResources.json)-GitHub-lagringsplatsen.

Om du vill behålla den ursprungliga Human Resources-appen (Personalfrågor) klonar du versionen på sidan [Settings](luis-how-to-manage-versions.md#clone-a-version) (Inställningar) och ger den namnet `simple`. Kloning är ett bra sätt att prova på olika LUIS-funktioner utan att påverka originalversionen.  

## <a name="purpose-of-the-app"></a>Syftet med appen
Den här appen visar hur det går till att hämta data från ett yttrande. Ta följande yttrande från en chattrobot som exempel:

|Yttrande|Jobbnamn som kan extraheras|
|:--|:--|
|Jag vill söka det nya jobbet inom redovisning.|redovisning|
|Jag skickar härmed mitt cv för att söka tjänsten inom teknik.|teknik|
|Fyll i ansökan till jobbet 123456|123456|

I den här självstudien lägger du till en ny entitet för att extrahera jobbnamnet. Funktionen att extrahera ett specifikt jobbnummer visas i [självstudien](luis-quickstart-intents-regex-entity.md) för reguljära uttryck. 

## <a name="purpose-of-the-simple-entity"></a>Syftet med den enkla entiteten
Syftet med den enkla entiteten är att lära LUIS vad ett jobbnamn är och var det kan hittas i ett yttrande. Den del av yttrandet som utgör jobbet kan variera från yttrande till yttrande baserat på ordval och yttrandets längd. LUIS behöver exempel på jobb i yttranden över alla avsikter.  

Jobbnamnet är svårt att urskilja eftersom ett namn kan vara ett substantiv, verb eller en fras med flera ord. Till exempel:

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

Den här LUIS-appen har jobbnamn i flera avsikter. Genom att märka orden i en avsikts alla yttranden lär sig LUIS mer om de olika jobben och var de finns i yttrandena.

## <a name="create-job-simple-entity"></a>Skapa en enkel jobbentitet

1. Kontrollera att Human Resources-appen (Personalfrågor) finns i avsnittet **Build** (Skapa) i LUIS. Du kan ändra till det här avsnittet genom att välja **Build** (Skapa) i menyraden längst upp till höger. 

    [ ![Skärmbild på LUIS-appen med Build (Skapa) markerat i navigeringsfältet längst upp till höger](./media/luis-quickstart-primary-and-secondary-data/hr-first-image.png)](./media/luis-quickstart-primary-and-secondary-data/hr-first-image.png#lightbox)

2. På sidan **Intents** (Avsikter) väljer du avsikten **ApplyForJob**. 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-select-applyforjob.png "Skärmbild på LUIS-appen med avsikten ApplyForJob markerad")](media/luis-quickstart-primary-and-secondary-data/hr-select-applyforjob.png#lightbox)

3. I yttrandet `I want to apply for the new accounting job` väljer du `accounting` och anger `Job` i det översta fältet på snabbmenyn. Välj sedan **Create new entity** (Skapa ny entitet) på snabbmenyn. 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png "Skärmbild på LUIS-appen med avsikten ApplyForJob och stegen för att skapa en entitet markerade")](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png#lightbox)

4. Kontrollera entitetsnamnet och -typen i popup-fönstret och välj **Done** (Klar).

    ![Dialogruta för att skapa en enkel entitet med jobbnamn och entitetstyp](media/luis-quickstart-primary-and-secondary-data/hr-create-simple-entity-popup.png)

5. I yttrandet `Submit resume for engineering position` märker du ordet teknik som en jobbentitet. Välj ordet teknik och välj sedan Job (Jobb) på snabbmenyn. 

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

## <a name="label-entity-in-example-utterances-for-getjobinformation-intent"></a>Märka entitet i exempelyttranden för avsikten GetJobInformation
1. Välj **Intents** (Avsikter) på den vänstra menyn.

2. Välj **GetJobInformation** i listan med avsikter. 

3. Märk jobben i exempelyttranden:

    |Yttrande|Jobbentitet|
    |:--|:--|
    |Finns det möjlighet att få arbeta med databaser?|databaser|
    |Söker efter ett nytt arbete inom redovisning|redovisning|
    |Finns det lediga tjänster för erfarna tekniker?|erfarna tekniker|

    Det finns andra exempelyttranden som inte innehåller jobbrelaterade ord.

## <a name="train-the-luis-app"></a>Träna LUIS-appen
LUIS känner inte till ändringarna av avsikterna och entiteterna (modellen) förrän den tränas. 

1. Längst uppe till höger på LUIS-webbplatsen väljer du knappen **Train** (Träna).

    ![Välj knappen train (träna)](./media/luis-quickstart-primary-and-secondary-data/train-button.png)

2. Träningen är klar när du ser det gröna statusfältet som bekräftar att det är klart längst upp på webbplatsen.

    ![Meddelande om att träning är klar](./media/luis-quickstart-primary-and-secondary-data/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publicera appen för att få slutpunkts-URL
För att få en LUIS-förutsägelse i en chattrobot eller i ett annat program måste du publicera appen. 

1. Längst uppe till höger på LUIS-webbplatsen väljer du knappen **Publish** (Publicera). 

2. Välj platsen Production (Produktionsplats) och knappen **Publish** (Publicera).

    [![](media/luis-quickstart-primary-and-secondary-data/publish-to-production.png "Skärmbild på sidan Publish (Publicera) med knappen Publish to production slot (Publicera till produktionsplats) markerad")](media/luis-quickstart-primary-and-secondary-data/publish-to-production.png#lightbox)

3. Publiceringen är klar när du ser det gröna statusfältet som bekräftar att det är klart längst upp på webbplatsen.

## <a name="query-the-endpoint-with-a-different-utterance"></a>Skicka fråga till slutpunkten med ett annat yttrande
På sidan **Publish** (Publicera) väljer du länken **endpoint** (slutpunkt) längst ned på sidan. 

[![](media/luis-quickstart-primary-and-secondary-data/publish-select-endpoint.png "Skärmbild på sidan Publish (Publicera) med slutpunkt markerad")](media/luis-quickstart-primary-and-secondary-data/publish-select-endpoint.png#lightbox)

Den här åtgärden öppnar ett nytt webbläsarfönster med slutpunkts-URL i adressfältet. Gå till slutet av URL:en i adressen och ange `Here is my c.v. for the programmer job`. Den sista frågesträngsparametern är `q`, yttrande**frågan**. Det här yttrandet är inte samma som någon av de märkta yttrandena. Därför är det ett bra test och bör returnera yttrandena `ApplyForJob`.

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

## <a name="to-boost-signal-add-jobs-phrase-list"></a>Lägg till jobb i fraslistan för att förbättra extraheringen
Öppna filen [jobs-phrase-list.csv](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/job-phrase-list.csv) från LUIS-Samples-GitHub-lagringsplatsen. Listan innehåller mer än tusen jobbrelaterade ord och fraser. Titta igenom listan efter ord som kan vara till nytta för dig. Om ord eller fraser som du behöver inte finns med i listan kan du lägga till egna.

1. I avsnittet **Build** (Skapa) i LUIS-appen väljer du **Phrase lists** (Fraslistor) under menyn **Improve app performance** (Förbättra appens prestanda).

    [![](media/luis-quickstart-primary-and-secondary-data/hr-select-phrase-list-left-nav.png "Skärmbild på Phrase lists (Fraslistor) med den vänstra navigeringsknappen markerad")](media/luis-quickstart-primary-and-secondary-data/hr-select-phrase-list-left-nav.png#lightbox)

2. Välj **Create new phrase list** (Skapa ny fraslista). 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-new-phrase-list.png "Skärmbild på markerad knapp för att skapa nya fraslistor")](media/luis-quickstart-primary-and-secondary-data/hr-create-new-phrase-list.png#lightbox)

3. Namnge den nya fraslistan `Jobs` och kopiera listan från jobs-phrase-list.csv till textrutan **Values** (Värden). Välj Retur. 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png "Skärmbild på dialogrutan för att skapa en ny fraslista")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png#lightbox)

    Om du vill lägga till fler ord i fraslistan kan du gå igenom de rekommenderade orden och lägga till det som behövs. 

4. Välj **Save** (Spara) så aktiveras fraslistan.

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png "Skärmbild på dialogrutan för att skapa en ny fraslista med ord i fraslistans värderuta")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png#lightbox)

5. [Träna](#train-the-luis-app) och [publicera](#publish-the-app-to-get-the-endpoint-URL) appen igen för att använda fraslistan.

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

## <a name="phrase-lists"></a>Fraslistor
Genom att lägga till fraslistan förbättrades listordens extrahering, men de är **inte** exakta matchningar. Fraslistan har flera jobb med det första ordet `lead` och med jobbet `welder`, men inte jobbet `lead welder`. Den här fraslistan över jobb behöver kanske kompletteras. Allt eftersom du [granskar slutpunktsyttranden](label-suggested-utterances.md) och hittar andra jobbord, lägger du till dem i fraslistan. Träna sedan appen igen och publicera om.

## <a name="what-has-this-luis-app-accomplished"></a>Vad har den här LUIS-appen åstadkommit?
Med hjälp av en enkel entitet och en fraslista har den här appen identifierat en frågeavsikt i naturligt språk och returnerat meddelandedata. 

Din chattrobot har nu tillräckligt med information för att bestämma den primära åtgärden vid jobbansökan och en parameter för åtgärden, vilket jobb det gäller. 

## <a name="where-is-this-luis-data-used"></a>Var används dessa LUIS-data? 
LUIS är klar med den här begäran. Det anropande programmet, till exempel en chattrobot, kan använda topScoringIntent-resultatet och data från entiteten för att skicka jobbinformation till någon på personalavdelningen via ett tredjeparts-API. Om det finns andra programmässiga alternativ för roboten eller det anropande programmet utför inte LUIS det arbetet. LUIS tar endast reda på vad användarens avsikt är. 

## <a name="clean-up-resources"></a>Rensa resurser
Ta bort LUIS-appen när den inte längre behövs. För att göra det väljer du menyn med tre punkter (...) till höger om appnamnet i applistan och väljer **Delete** (Ta bort). På popup-dialogrutan **Delete app?** (Ta bort appen?) väljer du **Ok**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig hur du lägger till en fördefinierad keyPhrase-entitet](luis-quickstart-intent-and-key-phrase.md)