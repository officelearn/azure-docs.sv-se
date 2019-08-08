---
title: Installera och köra behållare – Textanalys
titleSuffix: Azure Cognitive Services
description: Så här hämtar, installerar och kör behållare för textanalys i den här genomgången självstudien.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: dapine
ms.openlocfilehash: ddbe586c03d9f722d844d06968aa25e4b4a5aac0
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68815289"
---
# <a name="install-and-run-text-analytics-containers"></a>Installera och kör Textanalys behållare

Med behållare kan du köra API: er för text analys i din egen miljö och passar dina specifika krav för säkerhet och data styrning. Textanalys behållare tillhandahåller avancerad naturlig språk bearbetning över rå text och innehåller tre huvud funktioner: sentiment analys, extrahering av nyckel fraser och språk identifiering. Det finns för närvarande inte stöd för att länka entiteter i en behållare.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill köra någon av de Textanalys behållarna måste du ha värd datorn och behållar miljöerna.

## <a name="preparation"></a>Förberedelse

Du måste uppfylla följande krav innan du kan använda textanalys behållare:

|Obligatorisk|Syfte|
|--|--|
|Docker-motor| Du behöver Docker-motorn installerad på en [värddator](#the-host-computer). Docker innehåller paket som konfigurerar Docker-miljön på [MacOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/)och [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Få en genomgång om grunderna för Docker och behållare finns i den [översikt över Docker](https://docs.docker.com/engine/docker-overview/).<br><br> Docker måste konfigureras för att tillåta behållarna för att ansluta till och skicka faktureringsdata till Azure. <br><br> **I Windows**måste Docker också konfigureras för att stödja Linux-behållare.<br><br>|
|Bekant med Docker | Du bör ha grundläggande kunskaper om Docker-koncept, t. ex. register, databaser, behållare och behållar avbildningar, samt kunskaper `docker` om grundläggande kommandon.| 
|Textanalys resurs |För att du ska kunna använda behållaren måste du ha:<br><br>En Azure [textanalys-resurs](../../cognitive-services-apis-create-account.md) för att hämta tillhör ande API-nyckel och slut punkts-URI. Båda värdena är tillgängliga på Azure Portal Textanalys översikt och nycklar sidor och krävs för att starta behållaren.<br><br>**{API_KEY}** : En av de två tillgängliga resurs nycklarna på sidan **nycklar**<br><br>**{ENDPOINT_URI}** : Slut punkten enligt vad som anges på sidan **Översikt**|

[!INCLUDE [Gathering required parameters](../../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>Värddatorn

[!INCLUDE [Host Computer requirements](../../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Behållarkrav och rekommendationer

I följande tabell beskrivs de minsta och rekommenderade CPU-kärnorna minst 2,6 GHz (gigahertz) eller snabbare, och minne i gigabyte (GB), att tilldela för varje behållare för textanalys.

| Container | Minimum | Rekommenderas | TPS<br>(Minimum, maximum)|
|-----------|---------|-------------|--|
|Extrahering av nyckelfraser | 1 kärna, 2 GB minne | 1 kärna, 4 GB minne |15, 30|
|Språkidentifiering | 1 kärna, 2 GB minne | 1 kärna, 4 GB minne |15, 30|
|Attitydanalys | 1 kärna, 2 GB minne | 1 kärna, 4 GB minne |15, 30|

* Varje kärna måste vara minst 2,6 gigahertz (GHz) eller snabbare.
* TPS-transaktioner per sekund

Core och minne motsvarar `--cpus` inställningarna och `--memory` som `docker run` används som en del av kommandot.

## <a name="get-the-container-image-with-docker-pull"></a>Hämta behållar avbildningen med`docker pull`

Behållaravbildningar för textanalys är tillgängliga från Microsoft Container Registry.

| Container | Lagringsplats |
|-----------|------------|
|Extrahering av nyckelfraser | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
|Språkidentifiering | `mcr.microsoft.com/azure-cognitive-services/language` |
|Attitydanalys| `mcr.microsoft.com/azure-cognitive-services/sentiment` |

[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) Använd kommandot för att hämta en behållar avbildning från Microsoft container Registry.

En fullständig beskrivning av tillgängliga taggar för textanalys-behållare finns i följande behållare i Docker Hub:

* [Extrahering av diskussionsämne](https://go.microsoft.com/fwlink/?linkid=2018757)
* [Språkidentifiering](https://go.microsoft.com/fwlink/?linkid=2018759)
* [Attitydanalys](https://go.microsoft.com/fwlink/?linkid=2018654)

[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) Använd kommandot för att ladda ned en behållar avbildning.

### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>Docker pull för extraherings behållaren för nyckel fraser

```
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```

### <a name="docker-pull-for-the-language-detection-container"></a>Docker-hämtning för språk identifierings behållaren

```
docker pull mcr.microsoft.com/azure-cognitive-services/language:latest
```

### <a name="docker-pull-for-the-sentiment-container"></a>Docker pull för sentiment-behållaren

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:latest
```

[!INCLUDE [Tip for using docker list](../../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>Använda behållaren

När behållaren är på värddatorn [](#the-host-computer)använder du följande process för att arbeta med behållaren.

1. [Kör behållaren](#run-the-container-with-docker-run)med de fakturerings inställningar som krävs. Fler [exempel](../text-analytics-resource-container-config.md#example-docker-run-commands) på `docker run` kommandot är tillgängliga.
1. [Fråga behållarens förutsägelse slut punkt](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Kör behållaren med`docker run`

Använd kommandot [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) för att köra någon av de tre behållarna. Läs om hur du [samlar in nödvändiga parametrar](#gathering-required-parameters) för information om hur `{Endpoint_URI}` du `{API_Key}` hämtar och-värden.

[Exempel](../text-analytics-resource-container-config.md#example-docker-run-commands) på `docker run` kommandot är tillgängliga.

### <a name="run-container-example-of-docker-run-command"></a>Kör container-exempel för kommandot Docker Run

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/keyphrase \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Det här kommandot:

* Kör en nyckel fras behållare från behållar avbildningen
* Allokerar en processor kärna och 4 GB minne
* Visar TCP-port 5000 och allokerar en pseudo-TTY för behållaren
* Tar automatiskt bort behållaren när den har avslut ATS. Behållar avbildningen är fortfarande tillgänglig på värddatorn.


> [!IMPORTANT]
> Den `Eula`, `Billing`, och `ApiKey` alternativ måste anges för att köra behållaren, i annat fall startar inte behållaren.  Mer information finns i [fakturering](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Fråga behållarens förutsägelse slut punkt

Behållaren innehåller REST-baserade slut punkts-API: er för frågor förutsägelse.

Använd värden, `https://localhost:5000`för behållar-API: er.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Stoppa behållaren

[!INCLUDE [How to stop the container](../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Felsökning

Om du kör behållaren med en utgående [montering](../text-analytics-resource-container-config.md#mount-settings) och loggning aktive rad genererar behållaren loggfiler som är till hjälp vid fel sökning av problem som inträffar när du startar eller kör behållaren.

## <a name="billing"></a>Fakturering

Textanalys behållare skickar fakturerings information till Azure med hjälp av en _textanalys_ resurs på ditt Azure-konto. 

[!INCLUDE [Container's Billing Settings](../../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Mer information om alternativen finns i [konfigurera behållare](../text-analytics-resource-container-config.md).

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Sammanfattning

I den här artikeln beskrivs begrepp och arbetsflöde för att ladda ned, installera och textanalys-behållare som körs. Sammanfattningsvis:

* Textanalys innehåller tre Linux-behållare för Docker, som kapslar in extrahering av diskussionsämne, språkidentifiering och attitydanalys.
* Behållaravbildningar laddas ned från Microsoft Container Registry (MCR) i Azure.
* Behållaravbildningar som körs i Docker.
* Du kan använda antingen SDK eller REST API för att anropa åtgärder i textanalys behållare genom att ange värden URI: N för behållaren.
* Du måste ange faktureringsinformation när instanser skapades av en behållare.

> [!IMPORTANT]
> Cognitive Services-behållare är inte licensierad för att köra inte är ansluten till Azure för att mäta. Kunder måste du aktivera behållarna för att kommunicera faktureringsinformation med tjänsten Avläsning av programvara vid alla tidpunkter. Cognitive Services-behållare Skicka inte kunddata (t.ex. bild eller text som analyseras) till Microsoft.

## <a name="next-steps"></a>Nästa steg

* Granska [konfigurera behållare](../text-analytics-resource-container-config.md) för konfigurationsinställningar
* Läs vanliga [frågor och svar (FAQ)](../text-analytics-resource-faq.md) för att lösa problem som rör funktioner.
