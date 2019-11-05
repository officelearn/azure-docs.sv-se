---
title: Vad är nytt – Language Understanding (LUIS)
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller nyheter om Language Understanding.
author: diberry
manager: nitinme
ms.custom: experiment-luis-0519
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: diberry
ms.openlocfilehash: 0788df1c05d70c01d4de7f7a6ba62a476252f75a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498935"
---
# <a name="whats-new-in-language-understanding"></a>Vad är nytt i Language Understanding

Läs om vad som är nytt i tjänsten. Dessa objekt innehåller viktig information, videor, blogg inlägg och andra typer av information. Skapa ett bok märke för den här sidan för att hålla dig uppdaterad med tjänsten.  

## <a name="release-notes"></a>Viktig information 

### <a name="november-4-2019---ignite"></a>4 november 2019-antändning

* Förbättrad produktivitet för utvecklare
    * Allmän tillgänglighet för vår [förutsägelse slut punkt v3](luis-migration-api-v3.md). 
    * Möjlighet att importera och exportera appar med formatet. lu ([LUDown](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown)). På så sätt kan du paves en effektiv CI/CD-process. 
* Språk expansion
    * [Arabiska och hindi](luis-language-support.md) i offentlig för hands version.
* För skapande av modeller
    * [Fördefinierade domäner](luis-reference-prebuilt-domains.md) är nu allmänt tillgängliga (ga)
    * Japanska [fördefinierade entiteter](luis-reference-prebuilt-entities.md#japanese-entity-support) – ålder, valuta, tal, procent stöder inte i v3.
    * [Fördefinierade italienska entiteter](luis-reference-prebuilt-entities.md#italian-entity-support) – ålder, valuta, dimension, nummer, procents ATS av matchning har ändrats från v2.
* Förbättra användar upplevelsen i [Preview.Luis.AI-portalen](https://preview.luis.ai) – förbättringar etiketting-upplevelse för att möjliggöra skapande och fel sökning av komplexa modeller.
* Förhands språk förstå funktioner – [skapa sofistikerade språk modeller](luis-concept-entity-types.md) med mindre ansträngning. 
* Definiera Machine Learning-funktioner på modell nivå och aktivera modeller som ska användas som signaler till annan modell, som att använda entiteter som funktioner till avsikter och andra entiteter.
* Nya, utökade [gränser](luis-boundaries.md) – högre max för fras listor och total fraser, ny modell som funktions gränser
* Extrahera information från text i formatet djup hierarki-struktur, så att konversations program blir mer kraftfulla.

    ![enhets bild som sparats av datorn](./media/whats-new/deep-entity-extraction-example.png)

### <a name="september-3-2019"></a>3 september 2019

* Azure Authoring-resurs – [migrera nu](luis-migration-authoring.md).
    * 500 appar per Azure-resurs
    * 100 versioner per app
* Turkiskt stöd för fördefinierade entiteter
* Italiensk support för datetimeV2

### <a name="july-23-2019"></a>23 juli 2019

* Uppdatera [identifierarna – text](https://github.com/microsoft/Recognizers-Text/releases/tag/dotnet-v1.2.3) till 1.2.3
    * Ålder, temperatur, dimension och valuta igenkänning i italienska.
    * Förbättra helg igenkänning på engelska för att beräkna tacksägelse datum baserade datum på ett korrekt sätt.
    * Förbättringar på franska datum/tid för att minska antalet falska positiva identifieringar av icke-datum-och icke-tidsentiteter.
    * Stöd för kalender/skola/räkenskapsår och akronymer på engelska DateRange.
    * Förbättrad telefonnummer på telefonnummer på kinesiska och japanska.
    * Förbättrat stöd för NumberRange på engelska.
    * Prestanda förbättringar.

### <a name="june-24-2019"></a>24 juni 2019

* [OrdinalV2-fördefinierad entitet](luis-reference-prebuilt-ordinal-v2.md) som stöd för sortering, till exempel nästa, föregående och sista. Endast engelsk kultur.

### <a name="may-6-2019---build-conference"></a>6 maj 2019-build-konferens

Följande funktioner släpptes på Build 2019-konferensen:

* [För hands version av v3 API migration guide](luis-migration-api-v3.md)
* [Förbättrad analys instrument panel](luis-how-to-use-dashboard.md)
* [Förbättrade fördefinierade domäner](luis-reference-prebuilt-domains.md) 
* [Dynamiska List enheter](luis-migration-api-v3.md#dynamic-lists-passed-in-at-prediction-time)
* [Externa entiteter](luis-migration-api-v3.md#external-entities-passed-in-at-prediction-time)

## <a name="blogs"></a>Bloggar

[Bot Framework](https://blog.botframework.com/)

## <a name="videos"></a>Videoklipp

### <a name="2019-build-videos"></a>2019 Bygg videor

[Så här använder du Azure Conversation AI för att skala din verksamhet för nästa generation](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="service-updates"></a>Tjänstuppdateringar

[Azure Update-meddelanden för Cognitive Services](https://azure.microsoft.com/updates/?product=cognitive-services)
