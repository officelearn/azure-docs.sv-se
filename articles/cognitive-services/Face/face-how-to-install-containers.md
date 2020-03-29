---
title: Installera och köra behållare - Face
titleSuffix: Azure Cognitive Services
description: Den här artikeln visar hur du hämtar, installerar och kör behållare för Face i den här genomgången självstudiekurs.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: e467b195ab1e2124286bfef74d7d1b71a4d99dd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "76165984"
---
# <a name="install-and-run-face-containers-preview"></a>Installera och kör Ansiktsbehållare (Förhandsversion)

Azure Cognitive Services Face tillhandahåller en standardiserad Linux-behållare för Docker som identifierar mänskliga ansikten i avbildningar. Den identifierar också attribut, som inkluderar ansikte landmärken såsom näsor och ögon, kön, ålder, och andra maskin-förutspådde ansiktsdrag. Förutom identifiering kan Face kontrollera om två ansikten i samma bild eller olika bilder är desamma med hjälp av en konfidenspoäng. Face kan också jämföra ansikten mot en databas för att se om det redan finns ett liknande eller identiskt ansikte. Det kan också organisera liknande ansikten i grupper med hjälp av delade visuella egenskaper.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="prerequisites"></a>Krav

Du måste uppfylla följande förutsättningar innan du använder Face-tjänstbehållarna.

|Krävs|Syfte|
|--|--|
|Docker-motorn| Docker-motorn måste vara installerad på en [värddator](#the-host-computer). Docker innehåller paket som konfigurerar Docker-miljön på [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) och [Linux](https://docs.docker.com/engine/installation/#supported-platforms). En introduktion till grunderna för Docker och containrar finns i [Docker-översikt](https://docs.docker.com/engine/docker-overview/).<br><br> Docker måste konfigureras så att behållarna kan ansluta till och skicka faktureringsdata till Azure. <br><br> I Windows måste Docker också konfigureras för att stödja Linux-behållare.<br><br>|
|Förtrogenhet med Docker | Du behöver en grundläggande förståelse för Docker-begrepp, till exempel register, databaser, behållare och behållaravbildningar. Du behöver också `docker` kunskap om grundläggande kommandon.| 
|Ansiktsresurs |Om du vill använda behållaren måste du ha:<br><br>En Azure **Face-resurs** och den associerade API-nyckeln och slutpunkts-URI.An Azure Face resource and the associated API key and the endpoint URI. Båda värdena är tillgängliga på sidorna **Översikt** **och Nycklar** för resursen. De måste starta containern.<br><br>**{API_KEY}**: En av de två tillgängliga resursnycklarna på sidan **Nycklar**<br><br>**{ENDPOINT_URI}**: Slutpunkten som anges på **sidan Översikt**

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="request-access-to-the-private-container-registry"></a>Begär åtkomst till det privata behållarregistret

[!INCLUDE [Request access to private container registry](../../../includes/cognitive-services-containers-request-access.md)]

### <a name="the-host-computer"></a>Värddatorn

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Krav och rekommendationer för behållare

I följande tabell beskrivs de minsta och rekommenderade CPU-kärnor och minne som ska allokeras för varje Face-tjänstbehållare.

| Container | Minimum | Rekommenderas | Transaktioner per sekund<br>(Minimum, maximum)|
|-----------|---------|-------------|--|
|Ansikte | 1 kärna, 2 GB minne | 1 kärna, 4 GB minne |10, 20|

* Varje kärna måste vara minst 2,6 GHz eller snabbare.
* Transaktioner per sekund (TPS).

Kärna och minne `--cpus` motsvarar `--memory` och inställningar, som används `docker run` som en del av kommandot.

## <a name="get-the-container-image-with-docker-pull"></a>Hämta behållaravbildningen med docker pull

Behållaravbildningar för Face-tjänsten är tillgängliga. 

| Container | Lagringsplats |
|-----------|------------|
| Ansikte | `containerpreview.azurecr.io/microsoft/cognitive-services-face:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-face-container"></a>Docker dra för Face behållaren

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-face:latest
```

## <a name="use-the-container"></a>Använd behållaren

När behållaren finns på [värddatorn](#the-host-computer)använder du följande process för att arbeta med behållaren.

1. [Kör behållaren](#run-the-container-with-docker-run) med de faktureringsinställningar som krävs. Fler [exempel](./face-resource-container-config.md#example-docker-run-commands) `docker run` på kommandot är tillgängliga. 
1. [Fråga behållarens förutsägelseslutpunkt](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Kör behållaren med dockerkörning

Använd [kommandot docker run](https://docs.docker.com/engine/reference/commandline/run/) för att köra behållaren. Se [samla in nödvändiga parametrar](#gathering-required-parameters) för `{ENDPOINT_URI}` information `{API_KEY}` om hur du får och värden.

[Exempel](face-resource-container-config.md#example-docker-run-commands) på `docker run` kommandot är tillgängliga.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-face \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Det här kommandot:

* Kör en ansiktsbehållare från behållaravbildningen.
* Allokerar en CPU-kärna och 4 GB minne.
* Exponerar TCP-port 5000 och allokerar en pseudo TTY för behållaren.
* Tar automatiskt bort behållaren när den har avslutats. Behållaravbildningen är fortfarande tillgänglig på värddatorn. 

Fler [exempel](./face-resource-container-config.md#example-docker-run-commands) `docker run` på kommandot är tillgängliga. 

> [!IMPORTANT]
> Alternativen `Eula` `Billing`, `ApiKey` och måste anges för att behållaren ska kunna köras, annars startar inte behållaren. Mer information finns i [Fakturering](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>Fråga behållarens förutsägelseslutpunkt

Behållaren tillhandahåller REST-baserade slutpunkts-API:er för frågeförutsägels. 

Använd värden, `http://localhost:5000`för behållar-API:er.


<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Stoppa containern

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Felsökning

Om du kör behållaren med en [utdatamontering](./face-resource-container-config.md#mount-settings) och loggning är aktiverad genererar behållaren loggfiler som är användbara för att felsöka problem som inträffar medan du startar eller kör behållaren.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Fakturering

Face-tjänstbehållarna skickar faktureringsinformation till Azure med hjälp av en Face-resurs på ditt Azure-konto. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Mer information om dessa alternativ finns i [Konfigurera behållare](./face-resource-container-config.md).

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Sammanfattning

I den här artikeln har du lärt dig begrepp och arbetsflöden för hur du hämtar, installerar och kör Face-tjänstbehållare. Sammanfattningsvis:

* Behållaravbildningar hämtas från Azure Container Registry.
* Behållaravbildningar körs i Docker.
* Du kan använda antingen REST API eller SDK för att anropa åtgärder i Face-tjänstbehållare genom att ange behållarens värd-URI.
* Du måste ange faktureringsinformation när du instansierar en behållare.

> [!IMPORTANT]
> Cognitive Services-behållare är inte licensierade för att köras utan att vara anslutna till Azure för mätning. Kunderna måste alltid kunna kommunicera faktureringsinformation med mätartjänsten. Cognitive Services-behållare skickar inte kunddata, till exempel bilden eller texten som analyseras, till Microsoft.

## <a name="next-steps"></a>Nästa steg

* Konfigurationsinställningar finns i [Konfigurera behållare](face-resource-container-config.md).
* Mer information om hur du identifierar och identifierar ansikten finns i [Översikt över ansiktet](Overview.md).
* Information om de metoder som stöds av behållaren finns i [Ansikts-API:](//westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)et .
* Information om hur du använder fler Cognitive Services-behållare finns i [Cognitive Services-behållare](../cognitive-services-container-support.md).
