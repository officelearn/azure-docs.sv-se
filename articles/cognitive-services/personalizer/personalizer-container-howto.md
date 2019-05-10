---
title: Installera och kör behållare – Personalizer
titleSuffix: Azure Cognitive Services
description: Så här hämtar, installerar och kör behållare för Personalizer.
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/09/2019
ms.author: edjez
ms.openlocfilehash: a197531a7c78823271c0a5fa5413b76746f63a9a
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2019
ms.locfileid: "65507018"
---
# <a name="install-and-run-personalizer-containers"></a>Installera och köra Personalizer behållare

Tjänsten Personalizer har följande behållare: 

|Funktion|Funktioner|
|-|-|
|personalizer|Fastställa bästa åtgärd från aktuell kontext för innehåll och användare.|

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Du måste uppfylla följande krav innan du använder Personalizer-behållaren:

|Obligatoriskt|Syfte|
|--|--|
|Docker-motorn| Du behöver Docker-motorn installerad på en [värddatorn](#the-host-computer). Docker innehåller paket som konfigurerar Docker-miljön på [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/), och [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Få en genomgång om grunderna för Docker och behållare finns i den [översikt över Docker](https://docs.docker.com/engine/docker-overview/).<br><br> Docker måste konfigureras för att tillåta behållarna för att ansluta till och skicka faktureringsdata till Azure. <br><br> **På Windows**, Docker måste också konfigureras för att stödja Linux-behållare.<br><br>|
|Liknar processen med Docker | Du bör ha grundläggande kunskaper om Docker-begrepp som register, databaser, behållare, och behållaravbildningar samt kunskaper om grundläggande `docker` kommandon.| 
|Personalizer tjänstresurs |För att kunna använda de här behållarna, måste du ha:<br><br>En _Personalizer Service_ Azure-resurs att hämta associerade krypteringsnyckeln och fakturering slutpunkt URI. Båda värdena är tillgängliga på Azure portal Personalizer tjänstöversikt och nycklar sidor och krävs för att starta behållaren.<br><br>**{BILLING_KEY}** : Resursnyckeln<br><br>**{BILLING_ENDPOINT_URI}** : endpoint URI exempel är: `https://westus.api.cognitive.microsoft.com/`|

### <a name="the-host-computer"></a>Värddatorn

Den **värden** är den dator som kör docker-behållaren. Det kan vara en dator i din lokala enhet eller en docker som är värd för tjänsten i Azure, inklusive:

* [Azure Kubernetes Service](https://docs.microsoft.com/aks/index.yml)
* [Azure Container Instances](https://docs.microsoft.com/container-instances/index.yml)
* [Kubernetes](https://kubernetes.io/) kluster som distribueras till [Azure Stack](https://docs.microsoft.com/azure-stack/index.yml). Mer information finns i [distribuera Kubernetes i Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-solution-template-kubernetes-deploy.md).

### <a name="container-requirements-and-recommendations"></a>Behållarkrav och rekommendationer

I följande tabell beskrivs de minsta och rekommenderade processorkärnor och minne för att allokera för varje Personalizer Service-behållare.

| Container | Minimum | Rekommenderas |
|-----------|---------|-------------|
|personalizer | 1 kärna, 4 GB minne | 2 kärnor, 8 GB minne |

Varje kärna måste vara minst 2,6 GHz (gigahertz) eller snabbare.

Kärnor och minne som motsvarar den `--cpus` och `--memory` inställningar som används som en del av den `docker run` kommando.

Behållaren måste kunna ansluta till Azure EventHub i ordning till relay information i rangordning samt belöningar anrop till Personalizer-server i Azure och den måste kunna ansluta till Personalizer API: et för att läsa in konfiguration som krävs och senaste datorn Learning-modeller.

## <a name="get-the-container-image-with-docker-pull"></a>Hämta behållaravbildningen med `docker pull`

Behållaravbildningar för Personalizer-tjänsten är tillgängliga. 

| Container | Lagringsplats |
|-----------|------------|
| personalizer | `mcr.microsoft.com/azure-cognitive-services/personalizer:latest` |

> [!TIP]
> Du kan använda den [docker-avbildningar](https://docs.docker.com/engine/reference/commandline/images/) kommando för att lista din hämtade behållaravbildningar. Till exempel visar följande kommando ID, databasen och taggen för varje hämtade behållaravbildningen, formaterad som en tabell:
>
>  ```Docker
>  docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
>
>  IMAGE ID            REPOSITORY                                                                TAG
>  ebbee78a6baa        mcr.microsoft.com/azure-cognitive-services/personalizer                 latest
>  ``` 

### <a name="docker-pull-for-the-personalizer-service-container"></a>Docker pull för Personalizer tjänstbehållare

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/personalizer:latest
```

## <a name="how-the-container-works"></a>Så här fungerar behållaren

Behållaren Personalizer representerar klientdelen av tjänsten Personalizer. När behållaren körs, hämtar modeller och konfigurationsinställningar från tjänsten Personalizer i Azure-molnet. Behållartjänsten använder denna information för att svara på begäranden att **rangordning** och **utmärkelse**. Behållaren skickar också dessa begäranden till Personalizer resursen i Azure-molnet. Den här informationen används sedan för att träna din Personalizer loop modell som baseras på den aktuella inställningen för uppdateringsfrekvens för modellen. Den uppdaterade modellen skickas tillbaka till behållaren. 

![Lokalt, lokala arkitektur för Personalizer behållarens klient](./media/personalizer-container-howto/personalizer-container-architecture.png)

## <a name="how-to-use-the-container"></a>Hur du använder behållare

När behållaren är på den [värddatorn](#the-host-computer), använder du följande process för att arbeta med behållaren.

1. [Kör behållaren](#run-the-container-with-docker-run), med de nödvändiga fakturering inställningar. Mer [exempel](personalizer-container-configuration.md#example-docker-run-commands) av den `docker run` kommandot är tillgängliga. 
1. [Fråga förutsägelse behållarslutpunkten](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Kör behållaren med `docker run`

Använd den [docker kör](https://docs.docker.com/engine/reference/commandline/run/) kommando för att köra någon av tre behållarna. Kommandot använder följande parametrar:

| Platshållare | Value |
|-------------|-------|
|{BILLING_KEY} | Den här nyckeln används för att starta behållaren och är tillgänglig på sidan för Azure-portalens Personalizer Service nycklar.  |
|{BILLING_ENDPOINT_URI} | Fakturering slutpunkten URI-värdet är tillgänglig på översiktssidan för tjänsten Personalizer Azure portal.|

Ersätt parametrarna med dina egna värden i följande exempel `docker run` kommando.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/personalizer\
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Det här kommandot:

* Kör en Personalizer Service-behållare från behållaravbildningen
* Allokerar en CPU-kärna och 4 gigabyte (GB) minne
* Visar TCP-port 5000 och allokerar en pseudo-TTY för behållaren
* Tar automatiskt bort behållaren när avslutas. Behållaravbildningen finns kvar på värddatorn. 

> [!IMPORTANT]
> Den `Eula`, `Billing`, och `ApiKey` alternativ måste anges för att köra behållaren, i annat fall startar inte behållaren.  Mer information finns i [fakturering](#billing).

### <a name="run-multiple-containers-on-the-same-host"></a>Köra flera behållare på samma värd

Om du planerar att köra flera behållare med portar, se till att köra varje behållare med en annan port. Till exempel den första behållaren på port 5000 och andra behållare på port 5001.

Ersätt den `<container-registry>` och `<container-name>` med värdena för de behållare som du använder. De behöver inte vara samma behållare. Du kan ha Personalizer-behållaren och LUIS-behållaren som körs på värden tillsammans eller du kan ha flera Personalizer behållare som körs. 

Kör den första behållaren på port 5000. 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Kör andra behållare på port 5001.


```bash 
docker run --rm -it -p 5000:5001 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Varje efterföljande behållare ska vara på en annan port. 

## <a name="query-the-containers-prediction-endpoint"></a>Fråga behållarslutpunkten förutsägelse

Behållaren innehåller REST-baserade frågan förutsägelse endpoint API: er. 

Använd värden https://localhost:5000, för behållaren API: er.

## <a name="stop-the-container"></a>Stoppa behållaren

Om du vill stänga av behållaren i kommandoradsmiljön där behållaren körs, trycker du på **Ctrl + C**.

## <a name="troubleshoot"></a>Felsöka

Om du kör behållaren med ett utgående [montera](personalizer-container-configuration.md#mount-settings) och loggning är aktiverat, behållaren genererar loggfiler som är användbara för att felsöka problem som kan inträffa när startas eller körs i behållaren. 

## <a name="container-api-documentation"></a>Dokumentation om container API

Behållaren innehåller en fullständig uppsättning av dokumentationen för slutpunkter som en `Try it now` funktionen. Den här funktionen kan du ange dina inställningar i en webbaserad HTML-formulär och gör att frågan utan att behöva skriva någon kod. När frågan returnerar, ett exempel CURL-kommando har angetts för att visa de HTTP-rubrikerna och brödtext format som krävs. 

> [!TIP]
> Läs den [OpenAPI-specifikation](https://swagger.io/docs/specification/about/), som beskriver API-åtgärder som stöds av behållare, från den `/swagger` relativ URI. Exempel:
>
>  ```http
>  http://localhost:5000/swagger
>  ```

## <a name="billing"></a>Fakturering

Personalizer Service behållare skicka faktureringsinformation till Azure, med en _Personalizer Service_ resurs på ditt Azure-konto. 

Cognitive Services-behållare är inte licensierad för att köra inte är ansluten till Azure för att mäta. Kunder måste du aktivera behållarna för att kommunicera faktureringsinformation med tjänsten Avläsning av programvara vid alla tidpunkter.  

Den `docker run` kommando använder följande argument för fakturering:

| Alternativ | Beskrivning |
|--------|-------------|
| `ApiKey` | API-nyckeln för den _Personalizer Service_ resurs som används för att spåra faktureringsinformation. |
| `Billing` | Slutpunkten för den _Personalizer Service_ resurs som används för att spåra faktureringsinformation.|
| `Eula` | Anger att du har godkänt licensen för behållaren.<br/>Värdet för det här alternativet måste anges till `accept`. |

> [!IMPORTANT]
> Alla tre alternativ måste anges med giltiga värden eller behållaren startar inte.

Mer information om alternativen finns i [konfigurera behållare](personalizer-container-configuration.md).

## <a name="summary"></a>Sammanfattning

I den här artikeln beskrivs begrepp och arbetsflöde för att ladda ned, installera och köra Personalizer Service-behållare. Sammanfattningsvis:

* Personalizer Service tillhandahåller en Linux-behållare för Docker, som kapslar in anpassning.
* Behållaravbildningar laddas ned från Microsoft Container Registry (MCR) i Azure.
* Behållaravbildningar som körs i Docker.
* Du kan använda antingen SDK eller REST API för att anropa åtgärder i Personalizer-behållaren genom att ange värden URI: N för behållaren.
* Du måste ange faktureringsinformation när instanser skapades av en behållare.

> [!IMPORTANT]
> Cognitive Services-behållare är inte licensierad för att köra inte är ansluten till Azure för att mäta. Kunder måste du aktivera behållarna för att kommunicera faktureringsinformation med tjänsten Avläsning av programvara vid alla tidpunkter. Personalizer behållare kommer också att skicka begärandedata till motsvarande tjänst i Azure enligt onlineutbildning och får uppdatera modeller med jämna mellanrum från Azure.

## <a name="next-steps"></a>Nästa steg

[Så här konfigurerar du Docker-behållare för de `Docker Run` kommando](personalizer-container-configuration.md)