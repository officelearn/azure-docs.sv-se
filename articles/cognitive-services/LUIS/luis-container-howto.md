---
title: Docker behållare - LUIS
titleSuffix: Azure Cognitive Services
description: LUIS-behållaren läser in din tränade eller publicerade app i en dockerbehållare och ger åtkomst till frågeförutsägelserna från behållarens API-slutpunkter.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: dapine
ms.openlocfilehash: 308a474970db54022e5351fdf349d9572fbafb0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219933"
---
# <a name="install-and-run-luis-docker-containers"></a>Installera och köra LUIS docker-behållare
 
Luis-behållaren (Language Understanding) läser in din tränade eller publicerade språk understanding-modell. Som en [LUIS-app](https://www.luis.ai)ger docker-behållaren åtkomst till frågeförutsägelserna från behållarens API-slutpunkter. Du kan samla in frågeloggar från behållaren och ladda upp dem tillbaka till appen Språk understanding för att förbättra appens förutsägelsenoggrannhet.

Följande video visas med den här behållaren.

[![Containerdemonstration för Cognitive Services](./media/luis-container-how-to/luis-containers-demo-video-still.png)](https://aka.ms/luis-container-demo)

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill köra LUIS-behållaren bör du tänka på följande förutsättningar:

|Krävs|Syfte|
|--|--|
|Docker-motorn| Du måste dockermotorn installerad på en [värddator](#the-host-computer). Docker innehåller paket som konfigurerar Docker-miljön på [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) och [Linux](https://docs.docker.com/engine/installation/#supported-platforms). En introduktion till grunderna för Docker och containrar finns i [Docker-översikt](https://docs.docker.com/engine/docker-overview/).<br><br> Docker måste konfigureras så att behållarna kan ansluta till och skicka faktureringsdata till Azure. <br><br> **I Windows**måste Docker också konfigureras för att stödja Linux-behållare.<br><br>|
|Förtrogenhet med Docker | Du bör ha en grundläggande förståelse för Docker-begrepp, till exempel register, databaser, behållare och `docker` behållaravbildningar, samt kunskap om grundläggande kommandon.| 
|Azure-resurs `Cognitive Services` och [LUIS-paketerad appfil](luis-how-to-start-new-app.md) |För att kunna använda behållaren måste du ha:<br><br>* En _Cognitive Services_ Azure-resurs och tillhörande faktureringsnyckel faktureringsslutpunkten URI. Båda värdena är tillgängliga på sidorna Översikt och Nycklar för resursen och krävs för att starta behållaren. <br>* En tränad eller publicerad app paketerad som en monterad indata till behållaren med tillhörande App ID. Du kan hämta den paketerade filen från LUIS-portalen eller redigerings-API:erna. Om du får LUIS-paketerad app från [redigerings-API:erna](#authoring-apis-for-package-file)behöver du även din _redigeringsnyckel_.<br><br>Dessa krav används för att skicka kommandoradsargument till följande variabler:<br><br>**{AUTHORING_KEY}**: Den här nyckeln används för att hämta den paketerade appen från LUIS-tjänsten i molnet och ladda upp frågeloggarna tillbaka till molnet. Formatet är `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.<br><br>**{APP_ID}**: Det här ID:et används för att välja appen. Formatet är `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.<br><br>**{API_KEY}**: Den här nyckeln används för att starta behållaren. Du hittar slutpunktsnyckeln på två ställen. Den första är Azure-portalen i Cognitive Services-resursens nyckellista. _Cognitive Services_ Slutpunktsnyckeln är också tillgänglig i LUIS-portalen på sidan Inställningar för nycklar och slutpunkter. Använd inte startnyckeln.<br><br>**{ENDPOINT_URI}**: Slutpunkten som anges på sidan Översikt.<br><br>[Redigeringsnyckeln och slutpunktsnyckeln](luis-boundaries.md#key-limits) har olika syften. Använd dem inte omväxlande. |

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="authoring-apis-for-package-file"></a>Skapa API:er för paketfil

Skapa API:er för paketerade appar:

* [API för publicerat paket](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagepublishedapplicationasgzip)
* [Api:erat paket-API med inte publicerat, tränat paket](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagetrainedapplicationasgzip)

### <a name="the-host-computer"></a>Värddatorn

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Krav och rekommendationer för behållare

Den här behållaren stöder lägsta och rekommenderade värden för inställningarna:

|Container| Minimum | Rekommenderas | Tps<br>(Minimum, Maximum)|
|-----------|---------|-------------|--|
|LUIS|1 kärna, 2 GB minne|1 kärna, 4 GB minne|20, 40|

* Varje kärna måste vara minst 2,6 gigahertz (GHz) eller snabbare.
* TPS - transaktioner per sekund

Kärna och minne `--cpus` motsvarar `--memory` och inställningar, som används `docker run` som en del av kommandot.

## <a name="get-the-container-image-with-docker-pull"></a>Hämta behållaravbildningen med`docker pull`

Använd [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) kommandot för att hämta `mcr.microsoft.com/azure-cognitive-services/luis` en behållaravbildning från databasen:

```
docker pull mcr.microsoft.com/azure-cognitive-services/luis:latest
```

En fullständig beskrivning av tillgängliga `latest` taggar, till exempel som används i föregående kommando, finns i [LUIS](https://go.microsoft.com/fwlink/?linkid=2043204) på Docker Hub.

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>Så här använder du behållaren

När behållaren finns på [värddatorn](#the-host-computer)använder du följande process för att arbeta med behållaren.

![Process för att använda LUIS-behållare (Language Understanding)](./media/luis-container-how-to/luis-flow-with-containers-diagram.jpg)

1. [Exportera paket](#export-packaged-app-from-luis) för behållare från LUIS-portalen eller LUIS API:er.
1. Flytta paketfilen till den **nödvändiga indatakatalogen** på [värddatorn](#the-host-computer). Byt inte namn på, ändra, skriva över eller expandera LUIS-paketfilen.
1. [Kör behållaren](#run-the-container-with-docker-run)med de _indatamonterings-_ och faktureringsinställningar som krävs. Fler [exempel](luis-container-configuration.md#example-docker-run-commands) `docker run` på kommandot är tillgängliga. 
1. [Fråga behållarens förutsägelseslutpunkt](#query-the-containers-prediction-endpoint). 
1. När du är klar med behållaren [importerar du slutpunktsloggarna](#import-the-endpoint-logs-for-active-learning) från utdatafästet i LUIS-portalen och [stoppar](#stop-the-container) behållaren.
1. Använd LUIS-portalens [aktiva inlärning](luis-how-to-review-endpoint-utterances.md) på sidan **Granska slutpunktsyttranden** för att förbättra appen.

Appen som körs i behållaren kan inte ändras. För att ändra appen i behållaren måste du ändra appen i LUIS-tjänsten med [LUIS-portalen](https://www.luis.ai) eller använda [LUIS-redigerings-API:erna](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f). Träna och/eller publicera sedan, hämta sedan ett nytt paket och kör behållaren igen.

LUIS-appen i behållaren kan inte exporteras tillbaka till LUIS-tjänsten. Endast frågeloggarna kan överföras. 

## <a name="export-packaged-app-from-luis"></a>Exportera paketerad app från LUIS

LUIS-behållaren kräver en tränad eller publicerad LUIS-app för att svara på förutsägelsefrågor för användaryttranden. För att hämta LUIS-appen använder du antingen det tränade eller publicerade paket-API:et. 

Standardplatsen är `input` underkatalogen i förhållande till `docker run` var du kör kommandot.  

Placera paketfilen i en katalog och referera till den här katalogen som indatafäste när du kör docker-behållaren. 

### <a name="package-types"></a>Pakettyper

Indatamonteringskatalogen kan innehålla modellerna **Produktion,** **Förproduktion**och **Version av** appen samtidigt. Alla paket är monterade.

|Pakettyp|API för frågeslutpunkt|Tillgänglighet för frågor|Format för paketfilnamn|
|--|--|--|--|
|Version|FÅ, POST|Endast behållare|`{APP_ID}_v{APP_VERSION}.gz`|
|Mellanlagring|FÅ, POST|Azure och behållare|`{APP_ID}_STAGING.gz`|
|Produktion|FÅ, POST|Azure och behållare|`{APP_ID}_PRODUCTION.gz`|

> [!IMPORTANT]
> Byt inte namn på, ändra, skriva över eller expandera LUIS-paketfilerna.

### <a name="packaging-prerequisites"></a>Förpackningsförutsättning

Innan du paketerar ett LUIS-program måste du ha följande:

|Förpackningskrav|Information|
|--|--|
|Azure Cognitive Services-resursinstans _Cognitive Services_|Regioner som stöds inkluderar<br><br>Västra USA`westus`( )<br>Västeuropa (`westeurope`)<br>Östra Australien`australiaeast`( )|
|Tränad eller publicerad LUIS-app|Utan [beroenden som inte stöds][unsupported-dependencies]. |
|Tillgång till [värddatorns](#the-host-computer)filsystem |Värddatorn måste tillåta ett [inmatningsfäste](luis-container-configuration.md#mount-settings).|
  
### <a name="export-app-package-from-luis-portal"></a>Exportera apppaket från LUIS-portalen

[LUIS-portalen](https://www.luis.ai) ger möjlighet att exportera den tränade eller publicerade appens paket.

### <a name="export-published-apps-package-from-luis-portal"></a>Exportera publicerade appens paket från LUIS-portalen

Det publicerade appens paket är tillgängligt på listsidan **Mina appar.** 

1. Logga in på [LUIS-portalen](https://www.luis.ai).
1. Markera kryssrutan till vänster om appnamnet i listan. 
1. Markera **exportobjektet** i det kontextuella verktygsfältet ovanför listan.
1. Välj **Exportera för behållare (GZIP)**.
1. Välj miljö för **produktionsplats** eller **mellanlagringsplats**.
1. Paketet hämtas från webbläsaren.

![Exportera det publicerade paketet för behållaren från menyn Exportera app](./media/luis-container-how-to/export-published-package-for-container.png)

### <a name="export-versioned-apps-package-from-luis-portal"></a>Exportera version av appens paket från LUIS-portalen

Paketet med den versionerade appen är tillgängligt på listsidan **Versioner.**

1. Logga in på [LUIS-portalen](https://www.luis.ai).
1. Välj appen i listan. 
1. Välj **Hantera** i appens navigeringsfält.
1. Välj **Versioner** i det vänstra navigeringsfältet.
1. Markera kryssrutan till vänster om versionsnamnet i listan.
1. Markera **exportobjektet** i det kontextuella verktygsfältet ovanför listan.
1. Välj **Exportera för behållare (GZIP)**.
1. Paketet hämtas från webbläsaren.

![Exportera det tränade paketet för behållaren från menyn Exportera versioner](./media/luis-container-how-to/export-trained-package-for-container.png)

### <a name="export-published-apps-package-from-api"></a>Exportera publicerade appens paket från API

Använd följande REST API-metod för att paketera en LUIS-app som du redan har [publicerat](luis-how-to-publish-app.md). Ersätta dina egna lämpliga värden för platshållarna i API-anropet med hjälp av tabellen under HTTP-specifikationen.

```http
GET /luis/api/v2.0/package/{APP_ID}/slot/{SLOT_NAME}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| Platshållare | Värde |
|-------------|-------|
| **{APP_ID}** | Program-ID för den publicerade LUIS-appen. |
| **{SLOT_NAME}** | Miljön för den publicerade LUIS-appen. Använd något av följande värden:<br/>`PRODUCTION`<br/>`STAGING` |
| **{AUTHORING_KEY}** | Redigeringsnyckeln för LUIS-kontot för den publicerade LUIS-appen.<br/>Du kan hämta redigeringsnyckeln från sidan **Användarinställningar** på LUIS-portalen. |
| **{AZURE_REGION}** | Lämplig Azure-region:<br/><br/>`westus`- Västra USA<br/>`westeurope`- Västeuropa<br/>`australiaeast`- Östra Australien |

Information om hur du hämtar det publicerade paketet finns [i API-dokumentationen här][download-published-package]. Om det lyckas hämtas är svaret en LUIS-paketfil. Spara filen på den lagringsplats som angetts för behållarens indatafäste. 

### <a name="export-versioned-apps-package-from-api"></a>Exportera paketet för version av appen från API

Använd följande REST API-metod för att paketera ett LUIS-program som du redan har [tränat](luis-how-to-train.md). Ersätta dina egna lämpliga värden för platshållarna i API-anropet med hjälp av tabellen under HTTP-specifikationen.

```http
GET /luis/api/v2.0/package/{APP_ID}/versions/{APP_VERSION}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| Platshållare | Värde |
|-------------|-------|
| **{APP_ID}** | Program-ID för den tränade LUIS-appen. |
| **{APP_VERSION}** | Programversionen av den tränade LUIS-appen. |
| **{AUTHORING_KEY}** | Redigeringsnyckeln för LUIS-kontot för den publicerade LUIS-appen.<br/>Du kan hämta redigeringsnyckeln från sidan **Användarinställningar** på LUIS-portalen. |
| **{AZURE_REGION}** | Lämplig Azure-region:<br/><br/>`westus`- Västra USA<br/>`westeurope`- Västeuropa<br/>`australiaeast`- Östra Australien |

Information om hur du hämtar det versionstagna paketet finns i [API-dokumentationen här][download-versioned-package]. Om det lyckas hämtas är svaret en LUIS-paketfil. Spara filen på den lagringsplats som angetts för behållarens indatafäste. 

## <a name="run-the-container-with-docker-run"></a>Kör behållaren med`docker run`

Använd [kommandot docker run](https://docs.docker.com/engine/reference/commandline/run/) för att köra behållaren. Se [samla in nödvändiga parametrar](#gathering-required-parameters) för `{ENDPOINT_URI}` information `{API_KEY}` om hur du får och värden.

[Exempel](luis-container-configuration.md#example-docker-run-commands) på `docker run` kommandot är tillgängliga.

```console
docker run --rm -it -p 5000:5000 ^
--memory 4g ^
--cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output\,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis ^
Eula=accept ^
Billing={ENDPOINT_URI} ^
ApiKey={API_KEY}
```

* I det här exemplet `C:` används katalogen utanför enheten för att undvika behörighetskonflikter i Windows. Om du behöver använda en viss katalog som indatakatalog kan du behöva ge dockertjänsten behörighet. 
* Ändra inte ordningen på argumenten om du inte är bekant med dockercontainrar.
* Om du använder ett annat operativsystem använder du rätt konsol/terminal, mappsyntax för fästen och linjefortsättningstecken för ditt system. De här exemplen förutsätter en `^`Windows-konsol med ett linjefortsättningstecken . Eftersom behållaren är ett Linux-operativsystem använder målfästet en mappsyntax i Linux-stil.

Det här kommandot:

* Kör en behållare från LUIS-behållaravbildningen
* Laddar LUIS-appen från indatafästet vid *C:\indata*, som finns på behållarens värd
* Allokerar två CPU-kärnor och 4 GB minne
* Exponerar TCP-port 5000 och allokerar en pseudo-TTY för behållaren
* Sparar behållar- och LUIS-loggar till utdatamontering vid *C:\output*, som finns på behållarens värd
* Tar automatiskt bort behållaren när den har avslutats. Behållaravbildningen är fortfarande tillgänglig på värddatorn. 

Fler [exempel](luis-container-configuration.md#example-docker-run-commands) `docker run` på kommandot är tillgängliga. 

> [!IMPORTANT]
> Alternativen `Eula` `Billing`, `ApiKey` och måste anges för att behållaren ska kunna köras. Annars startar inte behållaren.  Mer information finns i [Fakturering](#billing).
> ApiKey-värdet är **nyckeln** från sidan **Azure Resources** i LUIS-portalen `Cognitive Services` och är också tillgängligt på sidan Azure-resursnycklar.  

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="endpoint-apis-supported-by-the-container"></a>Api:er för slutpunkt som stöds av behållaren

Både V2- och [V3-versioner](luis-migration-api-v3.md) av API:et finns med behållaren. 

## <a name="query-the-containers-prediction-endpoint"></a>Fråga behållarens förutsägelseslutpunkt

Behållaren tillhandahåller REST-baserade slutpunkts-API:er för frågeförutsägels. Slutpunkter för publicerade appar (mellanlagring eller produktion) har en _annan_ väg än slutpunkter för versionsappar.

Använd värden, `http://localhost:5000`för behållar-API:er.

# <a name="v3-prediction-endpoint"></a>[Slutpunkt för V3-förutsägelse](#tab/v3)

|Pakettyp|HTTP-verb|Routa|Frågeparametrar|
|--|--|--|--|
|Publicerad|FÅ, POST|`/luis/v3.0/apps/{appId}/slots/{slotName}/predict?`|`query={query}`<br>[`&verbose`]<br>[`&log`]<br>[`&show-all-intents`]|
|Version|FÅ, POST|`/luis/v3.0/apps/{appId}/versions/{versionId}/predict?`|`query={query}`<br>[`&verbose`]<br>[`&log`]<br>[`&show-all-intents`]|

Frågeparametrarna konfigurerar hur och vad som returneras i frågesvaret:

|Parameter för fråga|Typ|Syfte|
|--|--|--|
|`query`|sträng|Användarens yttrande.|
|`verbose`|boolean|Ett booleskt värde som anger om alla metadata ska returneras för de förväntade modellerna. Standardvärdet är false.|
|`log`|boolean|Loggar frågor, som kan användas senare för [aktiv inlärning](luis-how-to-review-endpoint-utterances.md). Standardvärdet är false.|
|`show-all-intents`|boolean|Ett booleskt värde som anger om alla avsikter eller den högsta poängmetoden ska returneras. Standardvärdet är false.|

# <a name="v2-prediction-endpoint"></a>[Slutpunkt för V2-förutsägelse](#tab/v2)

|Pakettyp|HTTP-verb|Routa|Frågeparametrar|
|--|--|--|--|
|Publicerad|[HÄMTA](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78), [POST](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee79)|`/luis/v2.0/apps/{appId}?`|`q={q}`<br>`&staging`<br>[`&timezoneOffset`]<br>[`&verbose`]<br>[`&log`]<br>|
|Version|FÅ, POST|`/luis/v2.0/apps/{appId}/versions/{versionId}?`|`q={q}`<br>[`&timezoneOffset`]<br>[`&verbose`]<br>[`&log`]|

Frågeparametrarna konfigurerar hur och vad som returneras i frågesvaret:

|Parameter för fråga|Typ|Syfte|
|--|--|--|
|`q`|sträng|Användarens yttrande.|
|`timezoneOffset`|nummer|Med tidszonoffset kan du [ändra tidszonen](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) som används av den fördefinierade entiteten datetimeV2.|
|`verbose`|boolean|Returnerar alla avsikter och deras poäng när de är inställda på true. Standard är falskt, som returnerar endast den översta avsikten.|
|`staging`|boolean|Returnerar frågan från mellanlagringsmiljöresultat om värdet är true. |
|`log`|boolean|Loggar frågor, som kan användas senare för [aktiv inlärning](luis-how-to-review-endpoint-utterances.md). Standardvärdet är true.|

***

### <a name="query-the-luis-app"></a>Fråga LUIS-appen

Ett exempel på CURL-kommando för att fråga behållaren för en publicerad app är:

# <a name="v3-prediction-endpoint"></a>[Slutpunkt för V3-förutsägelse](#tab/v3)

Om du vill fråga en modell i en plats använder du följande API:

```bash
curl -G \
-d verbose=false \
-d log=true \
--data-urlencode "query=turn the lights on" \
"http://localhost:5000/luis/v3.0/apps/{APP_ID}/slots/production/predict"
```

Om du vill ställa frågor till `production` **mellanlagringsmiljön** ersätter du i flödet med: `staging`

`http://localhost:5000/luis/v3.0/apps/{APP_ID}/slots/staging/predict`

Om du vill fråga en version av en version av en modell använder du följande API:

```bash
curl -G \
-d verbose=false \
-d log=false \
--data-urlencode "query=turn the lights on" \
"http://localhost:5000/luis/v3.0/apps/{APP_ID}/versions/{APP_VERSION}/predict"
```

# <a name="v2-prediction-endpoint"></a>[Slutpunkt för V2-förutsägelse](#tab/v2)

Om du vill fråga en modell i en plats använder du följande API:

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APP_ID}?q=turn%20on%20the%20lights&staging=false&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
Om du vill ställa frågor till **mellanlagringsmiljön** ändrar du parametervärdet för **mellanlagringsfrågesträng** till true: 

`staging=true`

Om du vill fråga en version av en version av en modell använder du följande API:

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APP_ID}/versions/{APP_VERSION}?q=turn%20on%20the%20lights&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
Versionsnamnet har högst 10 tecken och innehåller endast tecken som tillåts i en URL.

***

## <a name="import-the-endpoint-logs-for-active-learning"></a>Importera slutpunktsloggarna för aktiv inlärning

Om ett utdatafäste har angetts för LUIS-behållaren sparas appfrågeloggfiler i utdatakatalogen, där `{INSTANCE_ID}` är behållar-ID: et. Appfrågeloggen innehåller frågan, svaret och tidsstämplarna för varje förutsägelsefråga som skickas till LUIS-behållaren. 

Följande plats visar den kapslade katalogstrukturen för behållarens loggfiler.
```
/output/luis/{INSTANCE_ID}/
```
 
På LUIS-portalen väljer du din app och väljer sedan **Importera slutpunktsloggar** för att ladda upp dessa loggar. 

![Importera behållarens loggfiler för aktiv inlärning](./media/luis-container-how-to/upload-endpoint-log-files.png)

När loggen har överförts granskar du slutpunktsyttrandena i LUIS-portalen. [review the endpoint](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-review-endpoint-utterances)

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Stoppa containern

Om du vill stänga av behållaren trycker du på **Ctrl+C**i kommandoradsmiljön där behållaren körs .

## <a name="troubleshooting"></a>Felsökning

Om du kör behållaren med en [utdatamontering](luis-container-configuration.md#mount-settings) och loggning aktiverad genererar behållaren loggfiler som är användbara för att felsöka problem som inträffar när du startar eller kör behållaren.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Fakturering

LUIS-behållaren skickar faktureringsinformation till Azure med hjälp av en _Cognitive Services-resurs_ på ditt Azure-konto. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Mer information om dessa alternativ finns i [Konfigurera behållare](luis-container-configuration.md).

<!--blogs/samples/video courses -->
[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Sammanfattning

I den här artikeln har du lärt dig begrepp och arbetsflöden för att hämta, installera och köra LUIS-behållare (Language Understanding). Sammanfattningsvis:

* Språkförståelse (LUIS) innehåller en Linux-behållare för Docker som tillhandahåller slutpunktsflagförutsägelser för yttranden.
* Behållaravbildningar hämtas från MCR (Microsoft Container Registry).
* Behållaravbildningar körs i Docker.
* Du kan använda REST API för att fråga behållarslutpunkterna genom att ange behållarens värd-URI.
* Du måste ange faktureringsinformation när du instansierar en behållare.

> [!IMPORTANT]
> Cognitive Services-behållare är inte licensierade för att köras utan att vara anslutna till Azure för mätning. Kunderna måste alltid kunna kommunicera faktureringsinformation med mätartjänsten. Cognitive Services-behållare skickar inte kunddata (till exempel den bild eller text som analyseras) till Microsoft.

## <a name="next-steps"></a>Nästa steg

* Granska [Konfigurera behållare](luis-container-configuration.md) för konfigurationsinställningar.
* Se [LUIS-behållarbegränsningar](luis-container-limitations.md) för kända funktionsbegränsningar.
* Se [Felsökning](troubleshooting.md) för att lösa problem relaterade till LUIS-funktioner.
* Använda fler [Cognitive Services-behållare](../cognitive-services-container-support.md)

<!-- Links - external -->
[download-published-package]: https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagepublishedapplicationasgzip
[download-versioned-package]: https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagetrainedapplicationasgzip

[unsupported-dependencies]: luis-container-limitations.md#unsupported-dependencies-for-latest-container
