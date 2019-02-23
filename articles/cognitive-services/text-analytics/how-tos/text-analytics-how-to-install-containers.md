---
title: Installera och köra containrar
titleSuffix: Text Analytics -  Azure Cognitive Services
description: Så här hämtar, installerar och kör behållare för textanalys i den här genomgången självstudien.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 02/21/2019
ms.author: diberry
ms.openlocfilehash: 9e4935c9aea87faa07481652ed70032c226e11d5
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2019
ms.locfileid: "56675270"
---
# <a name="install-and-run-text-analytics-containers"></a>Installera och köra textanalys behållare

Text Analytics-behållare ger avancerad bearbetning av naturligt språk rå text och omfattar tre huvudfunktioner: attitydanalys, extrahering av diskussionsämne och språkidentifiering. Entitetslänkning stöds inte för närvarande i en behållare. 

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att kunna köra någon av behållarna för textanalys, måste du ha följande:

## <a name="preparation"></a>Förberedelse

Du måste uppfylla följande krav innan du kan använda textanalys behållare:

|Krävs|Syfte|
|--|--|
|Docker-motorn| Du behöver Docker-motorn installerad på en [värddatorn](#the-host-computer). Docker innehåller paket som konfigurerar Docker-miljön på [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/), och [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Få en genomgång om grunderna för Docker och behållare finns i den [översikt över Docker](https://docs.docker.com/engine/docker-overview/).<br><br> Docker måste konfigureras för att tillåta behållarna för att ansluta till och skicka faktureringsdata till Azure. <br><br> **På Windows**, Docker måste också konfigureras för att stödja Linux-behållare.<br><br>|
|Liknar processen med Docker | Du bör ha grundläggande kunskaper om Docker-begrepp som register, databaser, behållare, och behållaravbildningar samt kunskaper om grundläggande `docker` kommandon.| 
|Text Analytics-resurs |För att kunna använda behållaren måste du ha:<br><br>En [ _textanalys_ ](text-analytics-how-to-access-key.md) Azure-resurs att hämta associerade krypteringsnyckeln och fakturering slutpunkt URI. Båda värdena är tillgängliga på Azure portal-sidorna för översikt över Text Analytics och nycklar och krävs för att starta behållaren.<br><br>**{BILLING_KEY}** : Resursnyckeln<br><br>**{BILLING_ENDPOINT_URI}** : endpoint URI exempel är: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0`|

### <a name="the-host-computer"></a>Värddatorn

[!INCLUDE [Request access to private preview](../../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Behållarkrav och rekommendationer

I följande tabell beskrivs de minsta och rekommenderade CPU-kärnorna minst 2,6 GHz (gigahertz) eller snabbare, och minne i gigabyte (GB), att tilldela för varje behållare för textanalys.

| Container | Minimum | Rekommenderas |
|-----------|---------|-------------|
|Extrahering av nyckelfraser | 1 kärna, 2 GB minne | 1 kärna, 4 GB minne |
|Språkidentifiering | 1 kärna, 2 GB minne | 1 kärna, 4 GB minne |
|Attitydanalys | 1 kärna, 2 GB minne | 1 kärna, 4 GB minne |

Kärnor och minne som motsvarar den `--cpus` och `--memory` inställningar som används som en del av den `docker run` kommando.

## <a name="get-the-container-image-with-docker-pull"></a>Hämta behållaravbildningen med `docker pull`

Behållaravbildningar för textanalys är tillgängliga från Microsoft Container Registry. 

| Container | Lagringsplats |
|-----------|------------|
|Extrahering av nyckelfraser | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
|Språkidentifiering | `mcr.microsoft.com/azure-cognitive-services/language` |
|Attitydanalys | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

Använd den [ `docker pull` ](https://docs.docker.com/engine/reference/commandline/pull/) för att ladda ned en behållaravbildning från Microsoft Container Registry...

En fullständig beskrivning av tillgängliga taggar för textanalys-behållare finns i följande behållare i Docker Hub:

* [Extrahering av diskussionsämne](https://go.microsoft.com/fwlink/?linkid=2018757)
* [Språkidentifiering](https://go.microsoft.com/fwlink/?linkid=2018759)
* [Attitydanalys](https://go.microsoft.com/fwlink/?linkid=2018654)

Använd den [ `docker pull` ](https://docs.docker.com/engine/reference/commandline/pull/) för att ladda ned en behållaravbildning.


### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>Docker pull för nyckelbehållare frasen extrahering

```
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```

### <a name="docker-pull-for-the-language-detection-container"></a>Docker pull för behållaren för identifiering av språk

```
docker pull mcr.microsoft.com/azure-cognitive-services/language:latest
```

### <a name="docker-pull-for-the-sentiment-container"></a>Docker pull för behållaren sentiment

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:latest
```

[!INCLUDE [Tip for using docker list](../../../../includes/cognitive-services-containers-docker-list-tip.md)]


## <a name="how-to-use-the-container"></a>Hur du använder behållare

När behållaren är på den [värddatorn](#the-host-computer), använder du följande process för att arbeta med behållaren.

1. [Kör behållaren](#run-the-container-with-docker-run), med de nödvändiga fakturering inställningar. Mer [exempel](../text-analytics-resource-container-config.md#example-docker-run-commands) av den `docker run` kommandot är tillgängliga. 
1. [Fråga förutsägelse behållarslutpunkten](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Kör behållaren med `docker run`

Använd den [docker kör](https://docs.docker.com/engine/reference/commandline/run/) kommando för att köra någon av tre behållarna. Kommandot använder följande parametrar:

| Platshållare | Värde |
|-------------|-------|
|{BILLING_KEY} | Den här nyckeln används för att starta behållaren och är tillgänglig på sidan för Azure-portalens Text Analytics nycklar.  |
|{BILLING_ENDPOINT_URI} | Fakturering slutpunkten URI-värdet är tillgänglig på Azure portal Text Analytics översiktssidan.|

Ersätt parametrarna med dina egna värden i följande exempel `docker run` kommando.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/keyphrase \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Det här kommandot:

* Kör en diskussionsämne behållare från behållaravbildningen
* Allokerar en CPU-kärnor och 4 gigabyte (GB) minne
* Visar TCP-port 5000 och allokerar en pseudo-TTY för behållaren
* Tar automatiskt bort behållaren när avslutas. Behållaravbildningen finns kvar på värddatorn. 

Mer [exempel](../text-analytics-resource-container-config.md#example-docker-run-commands) av den `docker run` kommandot är tillgängliga. 

> [!IMPORTANT]
> Den `Eula`, `Billing`, och `ApiKey` alternativ måste anges för att köra behållaren, i annat fall startar inte behållaren.  Mer information finns i [fakturering](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Fråga behållarslutpunkten förutsägelse

Behållaren innehåller REST-baserade frågan förutsägelse endpoint API: er. 

Använd värden https://localhost:5000, för behållaren API: er.

## <a name="stop-the-container"></a>Stoppa behållaren

[!INCLUDE [How to stop the container](../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Felsökning

Om du kör behållaren med ett utgående [montera](../text-analytics-resource-container-config.md#mount-settings) och loggning är aktiverat, behållaren genererar loggfiler som är användbara för att felsöka problem som kan inträffa när startas eller körs i behållaren. 

## <a name="containers-api-documentation"></a>Behållarens API-dokumentation

[!INCLUDE [Container's API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="billing"></a>Fakturering

Textanalys behållare skicka faktureringsinformation till Azure, med en _textanalys_ resurs på ditt Azure-konto. 

[!INCLUDE [Container's Billing Settings](../../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Mer information om alternativen finns i [konfigurera behållare](../text-analytics-resource-container-config.md).

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
* Referera till [vanliga frågor (och svar FAQ)](../text-analytics-resource-faq.md) att lösa problem som rör funktioner.

