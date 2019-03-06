---
title: Enkel entitet, fraslista
titleSuffix: Azure Cognitive Services
description: I den här självstudien extraherar du data med jobbnamn via maskininlärning ur ett yttrande med hjälp av en enkel entitet. Om du vill öka noggrannheten i extraheringen lägger du till en fraslista med villkor som är specifika för den enkla entiteten.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 02/19/2019
ms.author: diberry
ms.openlocfilehash: d333eb6baa41321fdf7daca3c545d5d5e3ed5fe4
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2019
ms.locfileid: "56732712"
---
# <a name="tutorial-extract-names-with-simple-entity-and-a-phrase-list"></a>Självstudier: Extrahera namn med en enkel entitet och en fraslista

I den här självstudien extraherar du data med jobbnamn via maskininlärning ur ett yttrande med hjälp av en **enkel** entitet. Om du vill öka noggrannheten i extraheringen lägger du till en fraslista med villkor som är specifika för den enkla entiteten.

Den enkla entiteten identifierar ett datakoncept i ord eller fraser.

**I den här självstudiekursen får du lära du dig att:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importera en exempelapp
> * Lägga till en enkel entitet 
> * Lägga till en fraslista för att förbättra signalord
> * Träna 
> * Publicera 
> * Hämta avsikter och entiteter från en slutpunkt

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]


## <a name="simple-entity"></a>Enkel entitet

I den här självstudien lägger du till en ny enkel entitet för att extrahera jobbnamnet. Syftet med den enkla entiteten är att lära LUIS vad ett jobbnamn är och var det kan hittas i ett yttrande. Den del av yttrandet som utgör jobbnamnet kan variera mellan olika yttranden baserat på ordval och yttrandets längd. LUIS behöver exempel på jobbnamn i alla typer av avsikter där jobbnamn används.  

En enkel entitet passar bra till den här typen av data i följande fall:

* Data har ett enda koncept.
* Data är inte välformade som i reguljära uttryck.
* Data är inte enhetliga som i en fördefinierad entitet med telefonnummer eller data.
* Data kan inte matchas exakt mot en lista med kända ord, som i en listentitet.
* Data innehåller inte andra dataobjekt, som med sammansatta entiteter eller en hierarkisk entitet.

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

## <a name="import-example-app"></a>Importera en exempelapp

1.  Ladda ned och spara [app-JSON-filen](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/build-app/intentonly.json) från självstudien om avsikter.

2. Importera JSON-koden till en ny app.

3. I avsnittet **Hantera** går du till fliken **Versioner**, klonar versionen och ger den namnet `simple`. Kloning är ett bra sätt att prova på olika LUIS-funktioner utan att påverka originalversionen. Eftersom versionsnamnet används i webbadressen får namnet inte innehålla några tecken som är ogiltiga i webbadresser.

## <a name="mark-entities-in-example-utterances-of-an-intent"></a>Markera entiteter i exempelyttranden för en avsikt

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. På sidan **Intents** (Avsikter) väljer du avsikten **ApplyForJob**. 

1. I yttrandet `I want to apply for the new accounting job` väljer du `accounting` och anger `Job` i det översta fältet på snabbmenyn. Välj sedan **Create new entity** (Skapa ny entitet) på snabbmenyn. 

    [![Skärmbild av LUIS med avsikten ”ApplyForJob” med steg för att skapa entiteten markerade](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png "Skärmbild av LUIS med avsikten ”ApplyForJob” med steg för att skapa entiteten markerade")](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png#lightbox)

1. Kontrollera entitetsnamnet och -typen i popup-fönstret och välj **Done** (Klar).

    ![Dialogruta för att skapa en enkel entitet med jobbnamn och entitetstyp](media/luis-quickstart-primary-and-secondary-data/hr-create-simple-entity-popup.png)

1. I de återstående yttrandena markerar du de jobbrelaterade orden entiteten **Jobb** genom att markera ordet eller frasen och sedan välja **Job** (Jobb) på snabbmenyn. 

    [![Skärmbild av LUIS med märkningsjobbentiteten markerad](media/luis-quickstart-primary-and-secondary-data/hr-label-simple-entity.png "Skärmbild av LUIS med märkningsjobbentiteten markerad")](media/luis-quickstart-primary-and-secondary-data/hr-label-simple-entity.png#lightbox)


## <a name="add-more-example-utterances-and-mark-entity"></a>Lägga till fler exempelyttranden och markera entitet

Enkla entiteter behöver många exempel för att kunna ha en hög exakthet för förutsägelser. 
 
1. Lägg till fler yttranden och märk jobborden eller -fraserna som **jobbentiteter**. 

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

## <a name="mark-job-entity-in-other-intents"></a>Markera jobbentitet i andra avsikter

1. Välj **Intents** (Avsikter) på den vänstra menyn.

1. Välj **GetJobInformation** i listan med avsikter. 

1. Märk jobben i exempelyttrandenna

    Om det finns fler exempelyttranden i en avsikt än en annan avsikt är det mer troligt att den avsikten är den starkast förutsagda avsikten. 

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Träna appen så att avsiktsändringarna kan testas 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Publicera appen så att frågor kan köras mot den tränade modellen från slutpunkten

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Hämta avsikts- och entitetsförutsägelser från slutpunkten 

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Gå till slutet av URL:en i adressen och ange `Here is my c.v. for the engineering job`. Den sista frågesträngsparametern är `q`, yttrande**frågan**. Det här yttrandet är inte samma som någon av de märkta yttrandena. Därför är det ett bra test och bör returnera yttrandena `ApplyForJob`.

    ```json
    {
      "query": "Here is my c.v. for the engineering job",
      "topScoringIntent": {
        "intent": "ApplyForJob",
        "score": 0.98052007
      },
      "intents": [
        {
          "intent": "ApplyForJob",
          "score": 0.98052007
        },
        {
          "intent": "GetJobInformation",
          "score": 0.03424581
        },
        {
          "intent": "None",
          "score": 0.0015820954
        }
      ],
      "entities": [
        {
          "entity": "engineering",
          "type": "Job",
          "startIndex": 24,
          "endIndex": 34,
          "score": 0.668959737
        }
      ]
    }
    ```
    
    LUIS hittade rätt avsikt, **ApplyForJob**, och extraherade rätt entitet, **Job**, med värdet `engineering`.


## <a name="names-are-tricky"></a>Namn kan vara svårhanterade
LUIS-appen hittade rätt avsikt med hög exakthet och extraherade jobbnamnet, men namn är svårare. Prova yttrandet `This is the lead welder paperwork`.  

I följande JSON svarar LUIS-appen med rätta avsikten `ApplyForJob`, men extraherar inte jobbnamnet `lead welder`. 

```json
{
  "query": "This is the lead welder paperwork",
  "topScoringIntent": {
    "intent": "ApplyForJob",
    "score": 0.860295951
  },
  "intents": [
    {
      "intent": "ApplyForJob",
      "score": 0.860295951
    },
    {
      "intent": "GetJobInformation",
      "score": 0.07265678
    },
    {
      "intent": "None",
      "score": 0.00482481951
    }
  ],
  "entities": []
}
```

Eftersom ett namn kan vara vad som helst förutsäger LUIS-appen entiteter mer korrekt om den har en lista med ordfraser för att förbättra extraheringen.

## <a name="to-boost-signal-of-the-job-related-words-add-a-phrase-list-of-job-related-words"></a>Lägga till en fraslista med jobbrelaterade ord för att öka signalen för de jobbrelaterade orden

Öppna filen [jobs-phrase-list.csv](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/job-phrase-list.csv) från GitHub-lagringsplatsen Azure-Samples. Listan innehåller över 1 000 jobbrelaterade ord och fraser. Titta igenom listan efter ord som kan vara till nytta för dig. Om ord eller fraser som du behöver inte finns med i listan kan du lägga till egna.

1. I avsnittet **Build** (Skapa) i LUIS-appen väljer du **Phrase lists** (Fraslistor) under menyn **Improve app performance** (Förbättra appens prestanda).

1. Välj **Create new phrase list** (Skapa ny fraslista). 

1. Namnge den nya fraslistan `JobNames` och kopiera listan från jobs-phrase-list.csv till textrutan **Values** (Värden). Välj Retur. 

    [![Skärmbild på dialogrutan för att skapa en ny fraslista](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png "Skärmbild på dialogrutan för att skapa en ny fraslista")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png#lightbox)

    Om du vill lägga till fler ord i fraslistan kan du välja **Recommand** (Nytt kommando), gå igenom nya **Related Values** (Relaterade värden) och lägga till det som behövs. 

    Se till att **These values are interchangeable** (Dessa värden är utbytbara) är markerad eftersom alla dessa värden ska behandlas som synonymer för jobb. Läs mer om utbytbara och icke utbytbara [begrepp i fraslistan](luis-concept-feature.md#how-to-use-phrase-lists).

1. Välj **Save** (Spara) så aktiveras fraslistan.

    [![Skärmbild på dialogrutan för att skapa en ny fraslista med ord i fraslistans värderuta](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png "Skärmbild på dialogrutan för att skapa en ny fraslista med ord i fraslistans värderuta")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png#lightbox)

1. Träna och publicera appen igen för att använda fraslistan.

1. Skicka om frågan till slutpunkten med samma yttrande: `This is the lead welder paperwork.`

    JSON-svaret innehåller den extraherade entiteten:

    ```json
      {
      "query": "This is the lead welder paperwork.",
      "topScoringIntent": {
        "intent": "ApplyForJob",
        "score": 0.983076453
      },
      "intents": [
        {
          "intent": "ApplyForJob",
          "score": 0.983076453
        },
        {
          "intent": "GetJobInformation",
          "score": 0.0120766377
        },
        {
          "intent": "None",
          "score": 0.00248388131
        }
      ],
      "entities": [
        {
          "entity": "lead welder",
          "type": "Job",
          "startIndex": 12,
          "endIndex": 22,
          "score": 0.8373154
        }
      ]
    }
    ```

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Relaterad information

* [Självstudie om avsikter utan entiteter](luis-quickstart-intents-only.md)
* Konceptuell information om [enkel entitet](luis-concept-entity-types.md)
* Konceptuell information om [fraslista](luis-concept-feature.md)
* [Så här tränar du](luis-how-to-train.md)
* [Så här publicerar du](luis-how-to-publish-app.md)
* [Så här testar du i LUIS-portalen](luis-interactive-test.md)


## <a name="next-steps"></a>Nästa steg

I den här självstudien använder appen Human Resources en maskininlärd enkel entitet till att hitta jobbnamn i yttranden. Eftersom jobbnamn kan vara så olika ord och fraser behöver appen en fraslista för att kunna hitta orden med jobbnamnen. 

> [!div class="nextstepaction"]
> [Lägg till en fördefinierad keyPhrase-entitet](luis-quickstart-intent-and-key-phrase.md)
