---
title: Vad är nytt – Language Understanding (LUIS)
description: Den här artikeln uppdateras regelbundet med nyheter om Azure Cognitive Services API för Language Understanding.
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 716860b54e7d8e75984c0365cac61d14153c09ff
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137796"
---
# <a name="whats-new-in-language-understanding"></a>Vad är nytt i Language Understanding

Läs om vad som är nytt i tjänsten. Dessa objekt innehåller viktig information, videor, blogg inlägg och andra typer av information. Skapa ett bok märke för den här sidan för att hålla dig uppdaterad med tjänsten.

## <a name="release-notes"></a>Viktig information

### <a name="november-4-2019---ignite"></a>4 november 2019-antändning

* NLU- [modeller (video avancerade naturliga language Understanding) med Luis och Azure Cognitive Services | BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

* Förbättrad produktivitet för utvecklare
    * Allmän tillgänglighet för vår [förutsägelse slut punkt v3](luis-migration-api-v3.md).
    * Möjlighet att importera och exportera appar med formatet. lu ([LUDown](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown)). På så sätt kan du paves en effektiv CI/CD-process.
* Språk expansion
    * [Arabiska och hindi](luis-language-support.md) i offentlig för hands version.
* Fördefinierade modeller
    * [Fördefinierade domäner](luis-reference-prebuilt-domains.md) är nu allmänt tillgängliga (ga)
    * Japanska [fördefinierade entiteter](luis-reference-prebuilt-entities.md#japanese-entity-support) – ålder, valuta, tal och procent stöds inte i v3.
    * [Inbyggda italienska entiteter](luis-reference-prebuilt-entities.md#italian-entity-support) – ålder, valuta, dimension, nummer och procents ATS för en procentuell matchning har ändrats från v2.
* Förbättrad användar upplevelse i [Preview.Luis.AI Portal](https://preview.luis.ai) – förbättringar etiketting-upplevelse som möjliggör skapande och fel sökning av komplexa modeller. Testa självstudierna för Preview-portalen:
    * [Endast avsikter](tutorial-intents-only.md)
    * [Desammansättnings bar enhet som har registrerats av enheten](tutorial-machine-learned-entity.md)
* Förhands språk förstå funktioner – [skapa sofistikerade språk modeller](luis-concept-entity-types.md) med mindre ansträngning.
* Definiera Machine Learning-funktioner på modell nivå och aktivera modeller som ska användas som signaler till andra modeller, till exempel att använda entiteter som funktioner till avsikter och andra entiteter.
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

### <a name="2019-ignite-videos"></a>2019 tänd videor

[Avancerade fysiska Language Understandings modeller (NLU) med LUIS och Azure Cognitive Services | BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

### <a name="2019-build-videos"></a>2019 Bygg videor

[Så här använder du Azure Conversation AI för att skala din verksamhet för nästa generation](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="service-updates"></a>Tjänstuppdateringar

[Azure Update-meddelanden för Cognitive Services](https://azure.microsoft.com/updates/?product=cognitive-services)
