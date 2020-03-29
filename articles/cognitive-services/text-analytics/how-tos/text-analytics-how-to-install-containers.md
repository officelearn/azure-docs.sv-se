---
title: Installera och köra behållare - Textanalys
titleSuffix: Azure Cognitive Services
description: Så här hämtar, installerar och kör du behållare för TextAnalys i den här genomgången självstudiekurs.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: dapine
ms.openlocfilehash: 65033f9b6599d690b1097b4b78aa01148a40fc39
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79037510"
---
# <a name="install-and-run-text-analytics-containers"></a>Installera och kör Textanalys-containrar

Behållare kan du köra textnalytiska API:er i din egen miljö och är bra för dina specifika säkerhets- och datastyrningskrav. Textanalys-behållarna innehåller avancerad bearbetning av naturligt språk över rå text och innehåller tre huvudfunktioner: sentimentanalys, extrahering av nyckelfraser och språkidentifiering. Entitetslänkning stöds för närvarande inte i en behållare.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

> [!IMPORTANT]
> Det kostnadsfria kontot är begränsat till 5 000 transaktioner per månad och endast <a href="https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics" target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> prisnivåerna</a> **för fri och** **standard** är giltiga för behållare. Mer information om transaktionsbegäranrhastigheter finns i [Datagränser](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview#data-limits).

## <a name="prerequisites"></a>Krav

Om du vill köra någon av Text Analytics-behållarna måste du ha värddator- och behållarmiljöer.

## <a name="preparation"></a>Förberedelse

Du måste uppfylla följande förutsättningar innan du använder Text Analytics-behållare:

|Krävs|Syfte|
|--|--|
|Docker-motorn| Du måste dockermotorn installerad på en [värddator](#the-host-computer). Docker innehåller paket som konfigurerar Docker-miljön på [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) och [Linux](https://docs.docker.com/engine/installation/#supported-platforms). En introduktion till grunderna för Docker och containrar finns i [Docker-översikt](https://docs.docker.com/engine/docker-overview/).<br><br> Docker måste konfigureras så att behållarna kan ansluta till och skicka faktureringsdata till Azure. <br><br> **I Windows**måste Docker också konfigureras för att stödja Linux-behållare.<br><br>|
|Förtrogenhet med Docker | Du bör ha en grundläggande förståelse för Docker-begrepp, till exempel register, databaser, behållare och `docker` behållaravbildningar, samt kunskap om grundläggande kommandon.| 
|Textanalysresurs |För att kunna använda behållaren måste du ha:<br><br>En Azure [Text Analytics-resurs](../../cognitive-services-apis-create-account.md) för att hämta den associerade API-nyckeln och slutpunkts-URI. Båda värdena är tillgängliga på Azure-portalens textanalysöversikt och nycklar och krävs för att starta behållaren.<br><br>**{API_KEY}**: En av de två tillgängliga resursnycklarna på sidan **Nycklar**<br><br>**{ENDPOINT_URI}**: Slutpunkten som anges på **sidan Översikt**|

[!INCLUDE [Gathering required parameters](../../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>Värddatorn

[!INCLUDE [Host Computer requirements](../../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Krav och rekommendationer för behållare

I följande tabell beskrivs de minsta och rekommenderade CPU-kärnorna, minst 2,6 gigahertz (GHz) eller snabbare, och minne, i gigabyte (GB), att allokera för varje Text Analytics-behållare.

# <a name="key-phrase-extraction"></a>[Extrahering av diskussionsämne](#tab/keyphrase)

[!INCLUDE [key-phrase-extraction-container-requirements](../includes/key-phrase-extraction-container-requirements.md)]

# <a name="language-detection"></a>[Språkidentifiering](#tab/language)

[!INCLUDE [language-detection-container-requirements](../includes/language-detection-container-requirements.md)]

# <a name="sentiment-analysis"></a>[Attitydanalys](#tab/sentiment)

[!INCLUDE [sentiment-analysis-container-requirements](../includes/sentiment-analysis-container-requirements.md)]

***

* Varje kärna måste vara minst 2,6 gigahertz (GHz) eller snabbare.
* TPS - transaktioner per sekund

Kärna och minne `--cpus` motsvarar `--memory` och inställningar, som används `docker run` som en del av kommandot.

## <a name="get-the-container-image-with-docker-pull"></a>Hämta behållaravbildningen med`docker pull`

Behållaravbildningar för TextAnalys finns i Microsofts behållarregister.

# <a name="key-phrase-extraction"></a>[Extrahering av diskussionsämne](#tab/keyphrase)

[!INCLUDE [key-phrase-extraction-container-repository](../includes/key-phrase-extraction-container-repository.md)]

# <a name="language-detection"></a>[Språkidentifiering](#tab/language)

[!INCLUDE [language-detection-container-repository](../includes/language-detection-container-repository.md)]

# <a name="sentiment-analysis"></a>[Attitydanalys](#tab/sentiment)

[!INCLUDE [sentiment-analysis-container-repository](../includes/sentiment-analysis-container-repository.md)]

***

[!INCLUDE [Tip for using docker list](../../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-text-analytics-containers"></a>Docker dra för Text Analytics-behållarna

# <a name="key-phrase-extraction"></a>[Extrahering av diskussionsämne](#tab/keyphrase)

[!INCLUDE [docker-pull-key-phrase-extraction-container](../includes/docker-pull-key-phrase-extraction-container.md)]

# <a name="language-detection"></a>[Språkidentifiering](#tab/language)

[!INCLUDE [docker-pull-language-detection-container](../includes/docker-pull-language-detection-container.md)]

# <a name="sentiment-analysis"></a>[Attitydanalys](#tab/sentiment)

[!INCLUDE [docker-pull-sentiment-analysis-container](../includes/docker-pull-sentiment-analysis-container.md)]

***

## <a name="how-to-use-the-container"></a>Så här använder du behållaren

När behållaren finns på [värddatorn](#the-host-computer)använder du följande process för att arbeta med behållaren.

1. [Kör behållaren](#run-the-container-with-docker-run)med de faktureringsinställningar som krävs. Fler [exempel](../text-analytics-resource-container-config.md#example-docker-run-commands) `docker run` på kommandot är tillgängliga.
1. [Fråga behållarens förutsägelseslutpunkt](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Kör behållaren med`docker run`

Använd [kommandot docker run](https://docs.docker.com/engine/reference/commandline/run/) för att köra någon av de tre behållarna. Se [Samla in obligatoriska parametrar](#gathering-required-parameters) för `{ENDPOINT_URI}` information `{API_KEY}` om hur du hämtar värden och värden.

[Exempel](../text-analytics-resource-container-config.md#example-docker-run-commands) på `docker run` kommandot är tillgängliga.

# <a name="key-phrase-extraction"></a>[Extrahering av diskussionsämne](#tab/keyphrase)

[!INCLUDE [docker-run-key-phrase-extraction-container](../includes/docker-run-key-phrase-extraction-container.md)]

# <a name="language-detection"></a>[Språkidentifiering](#tab/language)

[!INCLUDE [docker-run-language-detection-container](../includes/docker-run-language-detection-container.md)]

# <a name="sentiment-analysis"></a>[Attitydanalys](#tab/sentiment)

[!INCLUDE [docker-run-sentiment-analysis-container](../includes/docker-run-sentiment-analysis-container.md)]

***

> [!IMPORTANT]
> Alternativen `Eula` `Billing`, `ApiKey` och måste anges för att behållaren ska kunna köras. Annars startar inte behållaren.  Mer information finns i [Fakturering](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Fråga behållarens förutsägelseslutpunkt

Behållaren tillhandahåller REST-baserade slutpunkts-API:er för frågeförutsägels.

Använd värden, `http://localhost:5000`för behållar-API:er.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Stoppa containern

[!INCLUDE [How to stop the container](../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Felsökning

Om du kör behållaren med en [utdatamontering](../text-analytics-resource-container-config.md#mount-settings) och loggning aktiverad genererar behållaren loggfiler som är användbara för att felsöka problem som inträffar när du startar eller kör behållaren.

[!INCLUDE [Cognitive Services FAQ note](../../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Fakturering

Text Analytics-behållarna skickar faktureringsinformation till Azure med hjälp av en _Text Analytics-resurs_ på ditt Azure-konto. 

[!INCLUDE [Container's Billing Settings](../../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Mer information om dessa alternativ finns i [Konfigurera behållare](../text-analytics-resource-container-config.md).

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Sammanfattning

I den här artikeln har du lärt dig begrepp och arbetsflöden för att hämta, installera och köra Text Analytics-behållare. Sammanfattningsvis:

* Text Analytics tillhandahåller tre Linux-behållare för Docker, som kapslar in olika funktioner:
   * *Extrahering av diskussionsämne*
   * *Språkidentifiering*
   * *Attitydanalys*
* Behållaravbildningar hämtas från MCR (Microsoft Container Registry) i Azure.
* Behållaravbildningar körs i Docker.
* Du kan använda antingen REST API eller SDK för att anropa åtgärder i Text Analytics-behållare genom att ange behållarens värd-URI.
* Du måste ange faktureringsinformation när du instansierar en behållare.

> [!IMPORTANT]
> Cognitive Services-behållare är inte licensierade för att köras utan att vara anslutna till Azure för mätning. Kunderna måste alltid kunna kommunicera faktureringsinformation med mätartjänsten. Cognitive Services-behållare skickar inte kunddata (t.ex. den bild eller text som analyseras) till Microsoft.

## <a name="next-steps"></a>Nästa steg

* Granska [Konfigurera behållare](../text-analytics-resource-container-config.md) för konfigurationsinställningar
* Se Vanliga frågor och svar om vanliga [frågor (FAQ)](../text-analytics-resource-faq.md) för att lösa problem som rör funktionalitet.
