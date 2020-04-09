---
title: Så här installerar och kör du behållare - Datorseende
titleSuffix: Azure Cognitive Services
description: Så här hämtar, installerar och kör du behållare för datorseende i den här genomgången självstudiekurs.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.custom: seodec18
ms.openlocfilehash: 5f36c429041a8182551d1f077f0a1229f520e8c1
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879351"
---
# <a name="install-and-run-read-containers-preview"></a>Installera och köra läsbehållare (förhandsversion)

Med behållare kan du köra API:erna för datorseende i din egen miljö. Behållare är bra för specifika säkerhets- och datastyrningskrav. I den här artikeln får du lära dig hur du hämtar, installerar och kör en datorseendebehållare.

En enda Docker-behållare, *Read,* är tillgänglig för datorseende. *Med läsbehållaren* kan du identifiera och extrahera *tryckt text* från bilder av olika objekt med olika ytor och bakgrunder, till exempel kvitton, affischer och visitkort. Dessutom identifierar *läsbehållaren* *handskriven text* i bilder och tillhandahåller stöd för PDF-, TIFF- och flersidiga filer. Mer information finns i [dokumentationen till Läs](concept-recognizing-text.md#read-api) API.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="prerequisites"></a>Krav

Du måste uppfylla följande förutsättningar innan du använder behållarna:

|Krävs|Syfte|
|--|--|
|Docker-motorn| Du måste dockermotorn installerad på en [värddator](#the-host-computer). Docker innehåller paket som konfigurerar Docker-miljön på [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) och [Linux](https://docs.docker.com/engine/installation/#supported-platforms). En introduktion till grunderna för Docker och containrar finns i [Docker-översikt](https://docs.docker.com/engine/docker-overview/).<br><br> Docker måste konfigureras så att behållarna kan ansluta till och skicka faktureringsdata till Azure. <br><br> **I Windows**måste Docker också konfigureras för att stödja Linux-behållare.<br><br>|
|Förtrogenhet med Docker | Du bör ha en grundläggande förståelse för Docker-begrepp, till exempel register, databaser, behållare och `docker` behållaravbildningar, samt kunskap om grundläggande kommandon.| 
|Resurs för datorseende |För att kunna använda behållaren måste du ha:<br><br>En Azure **Computer Vision-resurs** och den associerade API-nyckeln slutpunkten URI. Båda värdena är tillgängliga på sidorna Översikt och Nycklar för resursen och krävs för att starta behållaren.<br><br>**{API_KEY}**: En av de två tillgängliga resursnycklarna på sidan **Nycklar**<br><br>**{ENDPOINT_URI}**: Slutpunkten som anges på **sidan Översikt**|

## <a name="request-access-to-the-private-container-registry"></a>Begär åtkomst till det privata behållarregistret

[!INCLUDE [Request access to public preview](../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>Värddatorn

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Avancerat stöd för vektortillägg

**Värddatorn** är den dator som kör dockerbehållaren. Värden *måste ha stöd för* [avancerade vektortillägg](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2). Du kan söka efter AVX2-stöd på Linux-värdar med följande kommando:

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```
> [!WARNING]
> Värddatorn *krävs* för att stödja AVX2. Behållaren *fungerar inte* korrekt utan AVX2-stöd.

### <a name="container-requirements-and-recommendations"></a>Krav och rekommendationer för behållare

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="get-the-container-image-with-docker-pull"></a>Hämta behållaravbildningen med`docker pull`

Behållaravbildningar för Läsning är tillgängliga.

| Container | Behållarens register / Databas / Bildnamn |
|-----------|------------|
| Läsa | `containerpreview.azurecr.io/microsoft/cognitive-services-read:latest` |

Använd [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) kommandot för att hämta en behållaravbildning.

### <a name="docker-pull-for-the-read-container"></a>Docker dra för Läsbehållaren

```bash
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-read:latest
```

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>Så här använder du behållaren

När behållaren finns på [värddatorn](#the-host-computer)använder du följande process för att arbeta med behållaren.

1. [Kör behållaren](#run-the-container-with-docker-run)med de faktureringsinställningar som krävs. Fler [exempel](computer-vision-resource-container-config.md) `docker run` på kommandot är tillgängliga. 
1. [Fråga behållarens förutsägelseslutpunkt](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Kör behållaren med`docker run`

Använd [kommandot docker run](https://docs.docker.com/engine/reference/commandline/run/) för att köra behållaren. Se [samla in nödvändiga parametrar](#gathering-required-parameters) för `{ENDPOINT_URI}` information `{API_KEY}` om hur du får och värden.

[Exempel](computer-vision-resource-container-config.md#example-docker-run-commands) på `docker run` kommandot är tillgängliga.

```bash
docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
containerpreview.azurecr.io/microsoft/cognitive-services-read \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Det här kommandot:

* Kör läskbehållaren från behållaravbildningen.
* Allokerar 8 CPU-kärnor och 16 GB minne.
* Exponerar TCP-port 5000 och allokerar en pseudo-TTY för behållaren.
* Tar automatiskt bort behållaren när den har avslutats. Behållaravbildningen är fortfarande tillgänglig på värddatorn.

Fler [exempel](./computer-vision-resource-container-config.md#example-docker-run-commands) `docker run` på kommandot är tillgängliga. 

> [!IMPORTANT]
> Alternativen `Eula` `Billing`, `ApiKey` och måste anges för att behållaren ska kunna köras. Annars startar inte behållaren.  Mer information finns i [Fakturering](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Fråga behållarens förutsägelseslutpunkt

Behållaren tillhandahåller REST-baserade slutpunkts-API:er för frågeförutsägels. 

Använd värden, `http://localhost:5000`för behållar-API:er.

### <a name="asynchronous-read"></a>Asynkron läsning

Du kan `POST /vision/v2.0/read/core/asyncBatchAnalyze` använda `GET /vision/v2.0/read/operations/{operationId}` och åtgärder i samförstånd för att asynkront läsa en bild, liknande hur tjänsten Datorseende använder motsvarande REST-åtgärder. Den asynkrona POST-metoden `operationId` returnerar en som används som identifer till HTTP GET-begäran.

Välj för att expandera det `asyncBatchAnalyze` i webbläsaren i användargränssnittet i swagger.From the swagger UI, select the to expand it in the browser. Välj sedan **Prova** > **fil**. I det här exemplet använder vi följande bild:

![flikar vs blanksteg](media/tabs-vs-spaces.png)

När asynkront POST har körts returneras en **HTTP 202-statuskod.** Som en del av svaret `operation-location` finns det ett huvud som innehåller resultatslutpunkten för begäran.

```http
 content-length: 0
 date: Fri, 13 Sep 2019 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v2.0/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

Den `operation-location` är den fullt kvalificerade WEBBADRESSEN och nås via en HTTP GET. Här är JSON-svaret från `operation-location` att köra webbadressen från föregående bild:

```json
{
  "status": "Succeeded",
  "recognitionResults": [
    {
      "page": 1,
      "clockwiseOrientation": 2.42,
      "width": 502,
      "height": 252,
      "unit": "pixel",
      "lines": [
        {
          "boundingBox": [ 56, 39, 317, 50, 313, 134, 53, 123 ],
          "text": "Tabs VS",
          "words": [
            {
              "boundingBox": [ 90, 43, 243, 53, 243, 123, 94, 125 ],
              "text": "Tabs",
              "confidence": "Low"
            },
            {
              "boundingBox": [ 259, 55, 313, 62, 313, 122, 259, 123 ],
              "text": "VS"
            }
          ]
        },
        {
          "boundingBox": [ 221, 148, 417, 146, 417, 206, 227, 218 ],
          "text": "Spaces",
          "words": [
            {
              "boundingBox": [ 230, 148, 416, 141, 419, 211, 232, 218 ],
              "text": "Spaces"
            }
          ]
        }
      ]
    }
  ]
}
```

### <a name="synchronous-read"></a>Synkron läsning

Du kan `POST /vision/v2.0/read/core/Analyze` använda åtgärden för att synkroniskt läsa en bild. När bilden läses i sin helhet, då och först då returnerar API:et ett JSON-svar. Det enda undantaget är om ett fel inträffar. När ett fel uppstår returneras följande JSON:

```json
{
    status: "Failed"
}
```

JSON-svarsobjektet har samma objektdiagram som den asynkrona versionen. Om du är JavaScript-användare och vill ha typsäkerhet kan följande typer användas `AnalyzeResult` för att casta JSON-svaret som ett objekt.

```typescript
export interface AnalyzeResult {
    status: Status;
    recognitionResults?: RecognitionResult[] | null;
}

export enum Status {
    NotStarted = 0,
    Running = 1,
    Failed = 2,
    Succeeded = 3
}

export enum Unit {
    Pixel = 0,
    Inch = 1
}

export interface RecognitionResult {
    page?: number | null;
    clockwiseOrientation?: number | null;
    width?: number | null;
    height?: number | null;
    unit?: Unit | null;
    lines?: Line[] | null;
}

export interface Line {
    boundingBox?: number[] | null;
    text: string;
    words?: Word[] | null;
}

export enum Confidence {
    High = 0,
    Low = 1
}

export interface Word {
  boundingBox?: number[] | null;
  text: string;
  confidence?: Confidence | null;
}
```

Ett exempel på användningsfall finns i <a href="https://aka.ms/ts-read-api-types" target="_blank" rel="noopener noreferrer">Sandlådan <span class="docon docon-navigate-external x-hidden-focus"></span> TypeScript här</a> och väljer **Kör** för att visualisera dess användarvänlighet.

## <a name="stop-the-container"></a>Stoppa containern

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Felsökning

Om du kör behållaren med en [utdatamontering](./computer-vision-resource-container-config.md#mount-settings) och loggning aktiverad genererar behållaren loggfiler som är användbara för att felsöka problem som inträffar när du startar eller kör behållaren.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Fakturering

Cognitive Services-behållarna skickar faktureringsinformation till Azure med motsvarande resurs på ditt Azure-konto.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Mer information om dessa alternativ finns i [Konfigurera behållare](./computer-vision-resource-container-config.md).

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Sammanfattning

I den här artikeln har du lärt dig begrepp och arbetsflöden för att hämta, installera och köra Datorseende behållare. Sammanfattningsvis:

* Computer Vision tillhandahåller en Linux-behållare för Docker, kapsla in Read.
* Behållaravbildningar hämtas från behållarförhandsgranskningsregistret i Azure.
* Behållaravbildningar körs i Docker.
* Du kan använda antingen REST API eller SDK för att anropa åtgärder i Läs-behållare genom att ange behållarens värd-URI.
* Du måste ange faktureringsinformation när du instansierar en behållare.

> [!IMPORTANT]
> Cognitive Services-behållare är inte licensierade för att köras utan att vara anslutna till Azure för mätning. Kunderna måste alltid kunna kommunicera faktureringsinformation med mätartjänsten. Cognitive Services-behållare skickar inte kunddata (till exempel den bild eller text som analyseras) till Microsoft.

## <a name="next-steps"></a>Nästa steg

* Granska [Konfigurera behållare](computer-vision-resource-container-config.md) för konfigurationsinställningar
* Granska [översikten Över visuellt](Home.md) innehåll om du vill veta mer om hur du känner igen tryckt och handskriven text
* Mer information om de metoder som stöds av behållaren finns i [API:et](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) för visuellt innehåll.
* Se [Vanliga frågor och svar om](FAQ.md) vanliga frågor och svar för att lösa problem som rör datorseendefunktioner.
* Använda fler [Cognitive Services-behållare](../cognitive-services-container-support.md)
