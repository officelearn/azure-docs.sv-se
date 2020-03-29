---
title: Nyheter - Språkförståelse (LUIS)
description: Den här artikeln uppdateras regelbundet med nyheter om Azure Cognitive Services Language Understanding API.
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 562f7f8b4974363daab91991e6a8219b352432fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156012"
---
# <a name="whats-new-in-language-understanding"></a>Nyheter inom språk understanding

Läs om vad som är nytt i tjänsten. Dessa objekt inkluderar viktig information, videor, blogginlägg och andra typer av information. Bokmärk den här sidan för att hålla dig uppdaterad med tjänsten.

## <a name="release-notes"></a>Viktig information

### <a name="march-2020"></a>Mars 2020

* TLS 1.2 tillämpas nu för alla HTTP-begäranden till den här tjänsten. Mer information finns i [Azure Cognitive Services-säkerhet](../cognitive-services-security.md).

### <a name="november-4-2019---ignite"></a>4 november 2019 - Antända

* Video - [NLU-modeller (Advanced Natural Language Understanding) med LUIS och Azure Cognitive Services | BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

* Förbättrad produktivitet för utvecklare
    * Allmän tillgänglighet för vår [förutsägelseslutpunkt V3](luis-migration-api-v3.md).
    * Möjlighet att importera och exportera appar med .lu[(LUDown)](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown)format. Detta banar väg för en effektiv CI / CD-process.
* Språkexpansion
    * [Arabiska och hindi](luis-language-support.md) i offentlig förhandsvisning.
* Fördefinierade modeller
    * [Fördefinierade domäner](luis-reference-prebuilt-domains.md) är nu allmänt tillgängliga (GA)
    * Japanska [fördefinierade entiteter](luis-reference-prebuilt-entities.md#japanese-entity-support) - ålder, valuta, tal och procent stöds inte i V3.
    * Italienska [fördefinierade enheter](luis-reference-prebuilt-entities.md#italian-entity-support) - ålder, valuta, dimension, tal och procentuell upplösning ändras från V2.
* Förbättrad användarupplevelse i [preview.luis.ai portal](https://preview.luis.ai) - omarbetad märkningsupplevelse för att möjliggöra att skapa och felsöka komplexa modeller. Prova självstudierna för förhandsgranskningsportalen:
    * [Endast avsikter](tutorial-intents-only.md)
    * [Decomposable maskininlärd enhet](tutorial-machine-learned-entity.md)
* Förmåga att öka språkförståelse - [att bygga sofistikerade språkmodeller](luis-concept-entity-types.md) med mindre ansträngning.
* Definiera maskininlärningsfunktioner på modellnivå och gör det möjligt att använda modeller som signaler till andra modeller, till exempel med hjälp av entiteter som funktioner i avsikter och till andra entiteter.
* Nya, utökade [gränser](luis-boundaries.md) - högre maximum för fraslistor och totala fraser, ny modell som funktionsgränser
* Extrahera information från text i formatet djuphierarkistruktur, vilket gör konversationsprogram mer kraftfulla.

    ![bild av entiteten av maskin](./media/whats-new/deep-entity-extraction-example.png)

### <a name="september-3-2019"></a>den 3 september 2019

* Azure-redigeringsresurs – [migrera nu](luis-migration-authoring.md).
    * 500 appar per Azure-resurs
    * 100 versioner per app
* Turkiskt stöd till fördefinierade enheter
* Italienskt stöd för datetimeV2

### <a name="july-23-2019"></a>den 23 juli 2019

* Uppdatera [texten recognizers](https://github.com/microsoft/Recognizers-Text/releases/tag/dotnet-v1.2.3) till 1.2.3
    * Ålder, temperatur, dimension och valutakone igen på italienska.
    * Förbättring av Holiday erkännande på engelska för att korrekt beräkna Thanksgiving-baserade datum.
    * Förbättringar i franska DateTime för att minska falska positiva identifieringar av icke-datum och icke-time enheter.
    * Stöd för kalender/skola/räkenskapsår och förkortningar på engelska DateRange.
    * Förbättrad PhoneNumber erkännande på kinesiska och japanska.
    * Förbättrat stöd för NumberRange på engelska.
    * Prestandaförbättringar.

### <a name="june-24-2019"></a>den 24 juni 2019

* [OrdinalV2 fördefinierade entitet](luis-reference-prebuilt-ordinal-v2.md) för att stödja beställning, till exempel nästa, föregående och sista. Engelsk kultur bara.

### <a name="may-6-2019---build-conference"></a>6 maj 2019 - //Build Conference

Följande funktioner släpptes på Build 2019 Conference:

* [Förhandsgranskning av V3 API-migreringsguide](luis-migration-api-v3.md)
* [Förbättrad instrumentpanel för analys](luis-how-to-use-dashboard.md)
* [Förbättrade fördefinierade domäner](luis-reference-prebuilt-domains.md)
* [Dynamiska listentiteter](luis-migration-api-v3.md#dynamic-lists-passed-in-at-prediction-time)
* [Externa enheter](luis-migration-api-v3.md#external-entities-passed-in-at-prediction-time)

## <a name="blogs"></a>Bloggar

[Bot Framework](https://blog.botframework.com/)

## <a name="videos"></a>Videoklipp

### <a name="2019-ignite-videos"></a>2019 Antända videor

[NLU-modeller (Advanced Natural Language Understanding) med LUIS och Azure Cognitive Services | BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

### <a name="2019-build-videos"></a>2019 Bygg videor

[Så här använder du Azure Conversational AI för att skala upp ditt företag för nästa generation](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="service-updates"></a>Tjänstuppdateringar

[Azure-uppdateringsmeddelanden för Cognitive Services](https://azure.microsoft.com/updates/?product=cognitive-services)
