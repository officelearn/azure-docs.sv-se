---
title: Installera och köra behållare – Textanalys
titleSuffix: Azure Cognitive Services
description: Hämta, installera och kör behållare för Textanalys i den här själv studie kursen.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 2d44df1bb828140e662b06ffbe5fb14f207f68e0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "80877081"
---
# <a name="install-and-run-text-analytics-containers"></a>Installera och kör Textanalys-containrar

Med behållare kan du köra API: er för text analys i din egen miljö och passar dina specifika krav för säkerhet och data styrning. Textanalys behållare tillhandahåller avancerad naturlig språk bearbetning över rå text och innehåller tre huvud funktioner: sentiment analys, extrahering av nyckel fraser och språk identifiering. Det finns för närvarande inte stöd för att länka entiteter i en behållare.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

> [!IMPORTANT]
> Det kostnads fria kontot är begränsat till 5 000 transaktioner per månad och bara <a href="https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics" target="_blank">pris nivåerna <span class="docon docon-navigate-external x-hidden-focus"></span> </a> **kostnads fri** och **standard** är giltiga för behållare. Mer information om avgifter för transaktions begär Anden finns i [data begränsningar](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview#data-limits).

## <a name="prerequisites"></a>Krav

Om du vill köra någon av de Textanalys behållarna måste du ha värd datorn och behållar miljöerna.

## <a name="preparation"></a>Förberedelse

Du måste uppfylla följande krav innan du använder Textanalys behållare:

|Krävs|Syfte|
|--|--|
|Docker-motorn| Du behöver Docker-motorn installerad på en [värddator](#the-host-computer). Docker innehåller paket som konfigurerar Docker-miljön på [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) och [Linux](https://docs.docker.com/engine/installation/#supported-platforms). En introduktion till grunderna för Docker och containrar finns i [Docker-översikt](https://docs.docker.com/engine/docker-overview/).<br><br> Docker måste konfigureras för att tillåta att behållarna ansluter till och skicka fakturerings data till Azure. <br><br> **I Windows**måste Docker också konfigureras för att stödja Linux-behållare.<br><br>|
|Bekant med Docker | Du bör ha grundläggande kunskaper om Docker-koncept, t. ex. register, databaser, behållare och behållar avbildningar, samt kunskaper `docker` om grundläggande kommandon.| 
|Textanalys resurs |För att du ska kunna använda behållaren måste du ha:<br><br>En Azure [textanalys-resurs](../../cognitive-services-apis-create-account.md) för att hämta tillhör ande API-nyckel och slut punkts-URI. Båda värdena är tillgängliga på Azure Portal Textanalys översikt och nycklar sidor och krävs för att starta behållaren.<br><br>**{Api_key}**: en av de två tillgängliga resurs nycklarna på sidan **nycklar**<br><br>**{ENDPOINT_URI}**: slut punkten enligt vad som anges på sidan **Översikt**|

[!INCLUDE [Gathering required parameters](../../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>Värddatorn

[!INCLUDE [Host Computer requirements](../../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Krav och rekommendationer för behållare

I följande tabell beskrivs de minsta och rekommenderade processor kärnorna, minst 2,6 gigahertz (GHz) eller snabbare, och minne, i gigabyte (GB), för att allokera för varje Textanalys behållare.

# <a name="key-phrase-extraction"></a>[Extrahering av nyckelfraser](#tab/keyphrase)

[!INCLUDE [key-phrase-extraction-container-requirements](../includes/key-phrase-extraction-container-requirements.md)]

# <a name="language-detection"></a>[Språkidentifiering](#tab/language)

[!INCLUDE [language-detection-container-requirements](../includes/language-detection-container-requirements.md)]

# <a name="sentiment-analysis"></a>[Attitydanalys](#tab/sentiment)

[!INCLUDE [sentiment-analysis-container-requirements](../includes/sentiment-analysis-container-requirements.md)]

***

* Varje kärna måste vara minst 2,6 gigahertz (GHz) eller snabbare.
* TPS-transaktioner per sekund

Core och minne motsvarar inställningarna `--cpus` och `--memory` som används som en del av `docker run` kommandot.

## <a name="get-the-container-image-with-docker-pull"></a>Hämta behållar avbildningen med`docker pull`

Behållar avbildningar för Textanalys finns tillgängliga på Microsoft Container Registry.

# <a name="key-phrase-extraction"></a>[Extrahering av nyckelfraser](#tab/keyphrase)

[!INCLUDE [key-phrase-extraction-container-repository](../includes/key-phrase-extraction-container-repository.md)]

# <a name="language-detection"></a>[Språkidentifiering](#tab/language)

[!INCLUDE [language-detection-container-repository](../includes/language-detection-container-repository.md)]

# <a name="sentiment-analysis"></a>[Attitydanalys](#tab/sentiment)

[!INCLUDE [sentiment-analysis-container-repository](../includes/sentiment-analysis-container-repository.md)]

***

[!INCLUDE [Tip for using docker list](../../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-text-analytics-containers"></a>Docker-hämtning för Textanalys behållare

# <a name="key-phrase-extraction"></a>[Extrahering av nyckelfraser](#tab/keyphrase)

[!INCLUDE [docker-pull-key-phrase-extraction-container](../includes/docker-pull-key-phrase-extraction-container.md)]

# <a name="language-detection"></a>[Språkidentifiering](#tab/language)

[!INCLUDE [docker-pull-language-detection-container](../includes/docker-pull-language-detection-container.md)]

# <a name="sentiment-analysis"></a>[Attitydanalys](#tab/sentiment)

[!INCLUDE [docker-pull-sentiment-analysis-container](../includes/docker-pull-sentiment-analysis-container.md)]

***

## <a name="how-to-use-the-container"></a>Använda behållaren

När behållaren är på [värddatorn](#the-host-computer)använder du följande process för att arbeta med behållaren.

1. [Kör behållaren](#run-the-container-with-docker-run)med de fakturerings inställningar som krävs. Fler [exempel](../text-analytics-resource-container-config.md#example-docker-run-commands) på `docker run` kommandot är tillgängliga.
1. [Fråga behållarens förutsägelse slut punkt](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Kör behållaren med`docker run`

Använd kommandot [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) för att köra någon av de tre behållarna. Läs om hur du [samlar in nödvändiga parametrar](#gathering-required-parameters) för information om hur `{ENDPOINT_URI}` du `{API_KEY}` hämtar och-värden.

[Exempel](../text-analytics-resource-container-config.md#example-docker-run-commands) på `docker run` kommandot är tillgängliga.

# <a name="key-phrase-extraction"></a>[Extrahering av nyckelfraser](#tab/keyphrase)

[!INCLUDE [docker-run-key-phrase-extraction-container](../includes/docker-run-key-phrase-extraction-container.md)]

# <a name="language-detection"></a>[Språkidentifiering](#tab/language)

[!INCLUDE [docker-run-language-detection-container](../includes/docker-run-language-detection-container.md)]

# <a name="sentiment-analysis"></a>[Attitydanalys](#tab/sentiment)

[!INCLUDE [docker-run-sentiment-analysis-container](../includes/docker-run-sentiment-analysis-container.md)]

***

> [!IMPORTANT]
> Alternativen `Eula`, `Billing`och `ApiKey` måste anges för att köra behållaren. annars startar inte behållaren.  Mer information finns i [fakturering](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Fråga behållarens förutsägelse slut punkt

Behållaren innehåller REST-baserade slut punkts-API: er för frågor förutsägelse.

Använd värden, `http://localhost:5000`för behållar-API: er.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Stoppa containern

[!INCLUDE [How to stop the container](../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Felsökning

Om du kör behållaren med en utgående [montering](../text-analytics-resource-container-config.md#mount-settings) och loggning aktive rad genererar behållaren loggfiler som är till hjälp vid fel sökning av problem som inträffar när du startar eller kör behållaren.

[!INCLUDE [Cognitive Services FAQ note](../../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Fakturering

Textanalys behållare skickar fakturerings information till Azure med hjälp av en _textanalys_ resurs på ditt Azure-konto. 

[!INCLUDE [Container's Billing Settings](../../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Mer information om dessa alternativ finns i [Configure containers](../text-analytics-resource-container-config.md).

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Sammanfattning

I den här artikeln har du lärt dig begrepp och arbets flöde för att ladda ned, installera och köra Textanalys behållare. Sammanfattningsvis:

* Textanalys tillhandahåller tre Linux-behållare för Docker, som kapslar in olika funktioner:
   * *Extrahering av nyckelfraser*
   * *Språkidentifiering*
   * *Attitydanalys*
* Behållar avbildningar hämtas från Microsoft Container Registry (MCR) i Azure.
* Behållar avbildningar körs i Docker.
* Du kan använda antingen REST API eller SDK för att anropa åtgärder i Textanalys behållare genom att ange behållarens värd-URI.
* Du måste ange fakturerings information när du instansierar en behållare.

> [!IMPORTANT]
> Cognitive Services behållare är inte licensierade att köras utan att vara anslutna till Azure för mätning. Kunderna behöver göra det möjligt för behållarna att kommunicera fakturerings information med mät tjänsten hela tiden. Cognitive Services behållare skickar inte kund information (t. ex. den bild eller text som analyseras) till Microsoft.

## <a name="next-steps"></a>Nästa steg

* Granska [Konfigurera behållare](../text-analytics-resource-container-config.md) för konfigurations inställningar
* Läs vanliga [frågor och svar (FAQ)](../text-analytics-resource-faq.md) för att lösa problem som rör funktioner.
