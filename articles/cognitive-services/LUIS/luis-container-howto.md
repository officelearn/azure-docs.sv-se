---
title: Docker-containrar
titleSuffix: Language Understanding - Azure Cognitive Services
description: LUIS-behållaren läser in tränade eller publicerade appen i en docker-behållare och ger tillgång till fråga förutsägelser från behållarens API-slutpunkter.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 12/04/2018
ms.author: diberry
ms.openlocfilehash: 2542364db3a895c060c752beeb0cfabf75834f7d
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/02/2019
ms.locfileid: "53970284"
---
# <a name="install-and-run-luis-docker-containers"></a>Installera och köra LUIS docker-behållare
 
Behållaren för Språkförståelse (LUIS) läser in din tränade eller publicerade Language Understanding-modell, det vill säga som en [LUIS-app](https://www.luis.ai), i en docker-behållare och ger tillgång till fråga förutsägelser från behållarens API slutpunkter. Du kan samla in fråga loggar från behållaren och ladda upp dessa tillbaka till Azure Language Understanding-modellen för att förbättra appens prognosens noggrannhet.

Följande videoklipp visar hur du använder den här behållaren.

[![Demonstration av behållare för Cognitive Services](./media/luis-container-how-to/luis-containers-demo-video-still.png)](https://aka.ms/luis-container-demo)

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att kunna köra LUIS-behållare, måste du ha följande: 

|Krävs|Syfte|
|--|--|
|Docker-motorn| Du behöver Docker-motorn installerad på en [värddatorn](#the-host-computer). Docker innehåller paket som konfigurerar Docker-miljön på [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/), och [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Få en genomgång om grunderna för Docker och behållare finns i den [översikt över Docker](https://docs.docker.com/engine/docker-overview/).<br><br> Docker måste konfigureras för att tillåta behållarna för att ansluta till och skicka faktureringsdata till Azure. <br><br> **På Windows**, Docker måste också konfigureras för att stödja Linux-behållare.<br><br>|
|Liknar processen med Docker | Du bör ha grundläggande kunskaper om Docker-begrepp som register, databaser, behållare, och behållaravbildningar samt kunskaper om grundläggande `docker` kommandon.| 
|Språkresursen för Språkförståelse (LUIS) och tillhörande app |För att kunna använda behållaren måste du ha:<br><br>* A [ _Språkförståelse_ Azure-resurs](luis-how-to-azure-subscription.md), tillsammans med den associerade slutpunktsnyckel och slutpunkt URI (som används som fakturering slutpunkt).<br>* En tränad eller publicerade app paketerad som en monterad indata till behållaren med dess tillhörande App-ID.<br>* På redigering för att hämta app-paket om du gör detta API.<br><br>Dessa krav används för att överföra kommandoradsargument till följande variabler:<br><br>**{AUTHORING_KEY}** : Den här nyckeln används för att hämta den paketerade appen från LUIS-tjänsten i molnet och ladda upp loggarna fråga tillbaka till molnet. Formatet är `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.<br><br>**{APPLICATION_ID}** : Detta ID används för att välja appen. Formatet är `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.<br><br>**{ENDPOINT_KEY}** : Den här nyckeln används för att starta behållaren. Du hittar slutpunktsnyckeln på två platser. Först är Azure-portalen inom den _Språkförståelse_ resursens nycklar lista. Slutpunktsnyckeln är också tillgängligt i LUIS-portalen med nycklar och slutpunkten inställningssidan. Använd inte den starter-nyckeln.<br><br>**{BILLING_ENDPOINT}** : Fakturering slutpunktsvärdet är tillgänglig på Azure portal Language Understanding översiktssidan. Ett exempel är: `https://westus.api.cognitive.microsoft.com/luis/v2.0`.<br><br>Den [redigering nyckel och slutpunktsnyckeln](luis-boundaries.md#key-limits) har olika syften. Använd dem inte synonymt. |

### <a name="the-host-computer"></a>Värddatorn

Den **värden** är den dator som kör docker-behållaren. Det kan vara en dator i din lokala enhet eller en docker som är värd för tjänsten i Azure, inklusive:

* [Azure Kubernetes Service](../../aks/index.yml)
* [Azure Container Instances](../../container-instances/index.yml)
* [Kubernetes](https://kubernetes.io/) kluster som distribueras till [Azure Stack](../../azure-stack/index.yml). Mer information finns i [distribuera Kubernetes i Azure Stack](../../azure-stack/user/azure-stack-solution-template-kubernetes-deploy.md).

### <a name="container-requirements-and-recommendations"></a>Behållarkrav och rekommendationer

Den här behållaren har stöd för minsta och rekommenderade värden för inställningarna:

|Inställning| Minimum | Rekommenderas |
|-----------|---------|-------------|
|Kärnor<BR>`--cpus`|1 kärna<BR>minst 2,6 GHz (gigahertz) eller snabbare|1 kärna|
|Minne<BR>`--memory`|2 GB|4 GB|
|Transaktioner per sekund<BR>(TPS)|20 TPS|40 TPS|

Den `--cpus` och `--memory` inställningarna används som en del av den `docker run` kommando.

## <a name="get-the-container-image-with-docker-pull"></a>Hämta behållaravbildningen med `docker pull`

Använd den [ `docker pull` ](https://docs.docker.com/engine/reference/commandline/pull/) för att ladda ned en behållaravbildning från den `mcr.microsoft.com/azure-cognitive-services/luis` lagringsplats:

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/luis:latest
```

En fullständig beskrivning av tillgängliga taggar som `latest` används i det föregående kommandot, se [LUIS](https://hub.docker.com/r/microsoft/azure-cognitive-services-luis/) på Docker Hub.

> [!TIP]
> Du kan använda den [docker-avbildningar](https://docs.docker.com/engine/reference/commandline/images/) kommando för att lista din hämtade behållaravbildningar. Till exempel visar följande kommando ID, databasen och taggen för varje hämtade behållaravbildningen, formaterad som en tabell:
>
>  ```Docker
>  docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
>
>  IMAGE ID            REPOSITORY                                                                TAG
>  ebbee78a6baa        mcr.microsoft.com/azure-cognitive-services/luis                           latest
>  ``` 

## <a name="how-to-use-the-container"></a>Hur du använder behållare

När behållaren är på den [värddatorn](#the-host-computer), använder du följande process för att arbeta med behållaren.

![Processen för att använda behållare för Språkförståelse (LUIS)](./media/luis-container-how-to/luis-flow-with-containers-diagram.jpg)

1. [Exportera paket](#export-packaged-app-from-luis) för behållare från LUIS-portalen eller LUIS APIs.
1. Flytta fil för registreringspaketet till de nödvändiga **inkommande** på den [värddatorn](#the-host-computer). Inte byta namn på, ändra eller expandera LUIS paketfilen.
1. [Kör behållaren](##run-the-container-with-docker-run), med de nödvändiga _inkommande montera_ och fakturering inställningar. Mer [exempel](luis-container-configuration.md#example-docker-run-commands) av den `docker run` kommandot är tillgängliga. 
1. [Fråga förutsägelse behållarslutpunkten](#query-the-containers-prediction-endpoint). 
1. När du är klar med behållaren, [importera endpoint-loggarna](#import-the-endpoint-logs-for-active-learning) från utdata montera i LUIS-portalen och [stoppa](#stop-the-container) behållaren.
1. Använd LUIS portalen [aktiv inlärning](luis-how-to-review-endoint-utt.md) på den **granska endpoint yttranden** sidan för att förbättra appen.

När appen körs i behållaren kan inte ändras. Ordna ändringen appen i behållaren, måste du ändra appen i LUIS-tjänsten med den [LUIS](https://www.luis.ai) portal eller Använd LUIS [redigera API: er](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f). Sedan träna och/eller publicera, ladda ned ett nytt paket och kör behållaren igen.

LUIS-app i behållaren kan inte exporteras till LUIS-tjänsten. Endast frågeloggar kan laddas upp. 

## <a name="export-packaged-app-from-luis"></a>Exportera paketerad app från LUIS

LUIS-behållare kräver en tränad eller publicerade LUIS-app att besvara förutsägelsefrågor av användaren yttranden. Använd antingen tränade eller publicerade paketet API för att få LUIS-app. 

Standardplatsen är den `input` underkatalog i förhållande till där du kör den `docker run` kommando.  

Placera paketfilen i en katalog och referera till den här katalogen som den inkommande monteringen när du kör docker-behållaren. 

### <a name="package-types"></a>Pakettyper

Inkommande avbildningskatalog kan innehålla den **produktion**, **mellanlagring**, och **Trained** versioner av appen samtidigt. Alla paket är monterade. 

|Pakettyp|Fråge-API-slutpunkt|Fråga tillgänglighet|Paketets filnamn format|
|--|--|--|--|
|Tränas|Get, Post|Endast behållaren|`{APPLICATION_ID}_v{APPLICATION_VERSION}.gz`|
|Mellanlagring|Get, Post|Azure och behållare|`{APPLICATION_ID}_STAGING.gz`|
|Produktion|Get, Post|Azure och behållare|`{APPLICATION_ID}_PRODUCTION.gz`|

>**Viktigt!** Inte byta namn på, ändra eller expandera LUIS-paketfilerna.

### <a name="packaging-prerequisites"></a>Paketering krav

Innan du paketerar ett LUIS-program, måste du ha följande:

|Paketering krav|Information|
|--|--|
|Azure _Språkförståelse_ resursinstans|Regioner som stöds omfattar<br><br>Västra USA (```westus```)<br>Västeuropa (```westeurope```)<br>Australien, östra (```australiaeast```)|
|Tränade eller publicerade LUIS-app|Utan [stöds inte beroenden](#unsupported-dependencies). |
|Åtkomst till den [värddatorn](#the-host-computer)'s filsystem |Värddatorn måste tillåta en [inkommande montera](luis-container-configuration.md#mount-settings).|
  
### <a name="export-app-package-from-luis-portal"></a>Exportera app-paket från LUIS-portalen

LUIS [portal](https://www.luis.ai) ger dig möjlighet att exportera den tränade eller publicerade app-paket. 

### <a name="export-published-apps-package-from-luis-portal"></a>Exportera publicerad app-paket från LUIS-portalen

Den publicerade appen paketet är tillgängligt från den **Mina appar** sidan. 

1. Logga in på LUIS [portal](https://www.luis.ai).
1. Markera kryssrutan till vänster om appnamnet i listan. 
1. Välj den **exportera** objekt från sammanhangsberoende verktygsfältet ovanför listan.
1. Välj **exportera för behållare (GZIP)**.
1. Välj miljö **produktionsplatsen** eller **mellanlagringsplats**.
1. Paketet har hämtats från webbläsaren.

![Exportera publicerade paketet för behållaren på appsidan Export-menyn](./media/luis-container-how-to/export-published-package-for-container.png)

### <a name="export-trained-apps-package-from-luis-portal"></a>Exportera tränade app-paket från LUIS-portalen

Tränade app-paket är tillgängliga från den **versioner** sidan. 

1. Logga in på LUIS [portal](https://www.luis.ai).
1. Välj appen i listan. 
1. Välj **hantera** i appens navigeringsfältet.
1. Välj **versioner** i det vänstra navigeringsfältet.
1. Markera kryssrutan till vänster om versionsnamnet i listan.
1. Välj den **exportera** objekt från sammanhangsberoende verktygsfältet ovanför listan.
1. Välj **exportera för behållare (GZIP)**.
1. Paketet har hämtats från webbläsaren.

![Exportera tränade paketet för behållaren sidan versioner Export-menyn](./media/luis-container-how-to/export-trained-package-for-container.png)


### <a name="export-published-apps-package-from-api"></a>Exportera publicerad app-paket från API: et

Använd följande REST API-metod för att paketera en LUIS-app som du har redan [publicerade](luis-how-to-publish-app.md). Ersätt din egen lämpliga värden för platshållare i API-anrop med hjälp av tabellen nedan HTTP-specifikationen.

```http
GET /luis/api/v2.0/package/{APPLICATION_ID}/slot/{APPLICATION_ENVIRONMENT}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| Platshållare | Värde |
|-------------|-------|
|{APPLICATION_ID} | Program-ID för den publicerade LUIS-appen. |
|{APPLICATION_ENVIRONMENT} | Miljö av den publicerade LUIS-appen. Använd någon av följande värden:<br/>```PRODUCTION```<br/>```STAGING``` |
|{AUTHORING_KEY} | Redigering nyckeln för LUIS-konto för den publicerade LUIS-appen.<br/>Du kan hämta din redigering nyckeln från den **användarinställningar** sida på LUIS-portalen. |
|{AZURE_REGION} | Lämplig Azure-regionen:<br/><br/>```westus``` – USA, västra<br/>```westeurope``` –, Västra Europa<br/>```australiaeast``` – Australien, östra |

Du kan använda följande CURL-kommando för att hämta publicerade paketet, och ersätt dina egna värden:

```bash
curl -X GET \
https://{AZURE_REGION}.api.cognitive.microsoft.com/luis/api/v2.0/package/{APPLICATION_ID}/slot/{APPLICATION_ENVIRONMENT}/gzip  \
 -H "Ocp-Apim-Subscription-Key: {AUTHORING_KEY}" \
 -o {APPLICATION_ID}_{APPLICATION_ENVIRONMENT}.gz
```

Om detta lyckas är svaret en LUIS-paketfil. Spara filen på den lagringsplats som har angetts för den inkommande monteringen i behållaren. 

### <a name="export-trained-apps-package-from-api"></a>Exportera tränade app-paket från API: et

Använd följande REST API-metod för att paketera en LUIS-program som du har redan [tränade](luis-how-to-train.md). Ersätt din egen lämpliga värden för platshållare i API-anrop med hjälp av tabellen nedan HTTP-specifikationen.

```http
GET /luis/api/v2.0/package/{APPLICATION_ID}/versions/{APPLICATION_VERSION}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| Platshållare | Värde |
|-------------|-------|
|{APPLICATION_ID} | Program-ID för tränade LUIS-programmet. |
|{APPLICATION_VERSION} | Programversion för tränade LUIS-programmet. |
|{AUTHORING_KEY} | Redigering nyckeln för LUIS-konto för den publicerade LUIS-appen.<br/>Du kan hämta din redigering nyckeln från den **användarinställningar** sida på LUIS-portalen.  |
|{AZURE_REGION} | Lämplig Azure-regionen:<br/><br/>```westus``` – USA, västra<br/>```westeurope``` –, Västra Europa<br/>```australiaeast``` – Australien, östra |

Använd följande CURL-kommando för att hämta tränade paketet:

```bash
curl -X GET \
https://{AZURE_REGION}.api.cognitive.microsoft.com/luis/api/v2.0/package/{APPLICATION_ID}/versions/{APPLICATION_VERSION}/gzip  \
 -H "Ocp-Apim-Subscription-Key: {AUTHORING_KEY}" \
 -o {APPLICATION_ID}_v{APPLICATION_VERSION}.gz
```

Om detta lyckas är svaret en LUIS-paketfil. Spara filen på den lagringsplats som har angetts för den inkommande monteringen i behållaren. 

## <a name="run-the-container-with-docker-run"></a>Kör behållaren med `docker run`

Använd den [docker kör](https://docs.docker.com/engine/reference/commandline/run/) kommando för att köra behållaren. Kommandot använder följande parametrar:

| Platshållare | Värde |
|-------------|-------|
|{ENDPOINT_KEY} | Den här nyckeln används för att starta behållaren. Använd inte den starter-nyckeln. |
|{BILLING_ENDPOINT} | Fakturering slutpunktsvärdet är tillgänglig på Azure portal Language Understanding översiktssidan.|

Ersätt parametrarna med dina egna värden i följande exempel `docker run` kommando.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 \
--mount type=bind,src=c:\input,target=/input \
--mount type=bind,src=c:\output,target=/output \
mcr.microsoft.com/azure-cognitive-services/luis \
Eula=accept \
Billing={BILLING_ENDPOINT} \
ApiKey={ENDPOINT_KEY}
```

> [!Note] 
> Föregående kommando använder katalogen av den `c:` enhet för att undvika eventuella behörighetskonflikter på Windows. Om du vill använda en viss katalog som den inkommande katalogen kan du behöva ge docker tjänsten behörighet. Kommandot ovan docker använder det omvända snedstrecket `\`, som en fortsättning tecknet. Ersätta eller ta bort detta baserat på din [värddatorn](#the-host-computer) operativsystemets krav. Ändra inte argumentens ordning om du inte är bekant med docker-behållare.


Det här kommandot:

* Kör en behållare från LUIS behållaravbildning
* Läser in LUIS-app från inkommande montering på c:\input, finns på behållarvärd
* Allokerar två CPU-kärnor och 4 gigabyte (GB) minne
* Visar TCP-port 5000 och allokerar en pseudo-TTY för behållaren
* Sparar behållare och LUIS loggarna för utdata mount på c:\output, finns på behållarvärd
* Tar automatiskt bort behållaren när avslutas. Behållaravbildningen finns kvar på värddatorn. 

Mer [exempel](luis-container-configuration.md#example-docker-run-commands) av den `docker run` kommandot är tillgängliga. 

> [!IMPORTANT]
> Den `Eula`, `Billing`, och `ApiKey` alternativ måste anges för att köra behållaren, i annat fall startar inte behållaren.  Mer information finns i [fakturering](#billing).
> ApiKey-värdet är den **nyckel** från nycklar och slutpunkter i LUIS-portalen och är också tillgängliga på sidan nycklar för Azure Language Understanding-resurs.  

## <a name="query-the-containers-prediction-endpoint"></a>Fråga behållarslutpunkten förutsägelse

Behållaren innehåller REST-baserade frågan förutsägelse endpoint API: er. Slutpunkter för publicerade (mellanlagring eller produktion) appar har en _olika_ väg än slutpunkter för tränade appar. 

Använd värden https://localhost:5000, för behållaren API: er. 

|Pakettyp|Metod|Routa|Frågeparametrar|
|--|--|--|--|
|Publicerad|[Hämta](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78), [inlägg](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee79)|/ luis/v2.0/apps/{appId}?|q = {q}<br>& mellanlagring<br>[& timezoneOffset]<br>[& utförlig]<br>[& logg]<br>|
|Tränas|Get, Post|/ luis/v2.0/apps/{appId}/versions/{versionId}?|q = {q}<br>[& timezoneOffset]<br>[& utförlig]<br>[& logg]|

Frågeparametrarna konfigurera hur och vad returneras i svaret på frågan:

|Frågeparameter|Typ|Syfte|
|--|--|--|
|`q`|sträng|Användarens uttryck.|
|`timezoneOffset`|nummer|TimezoneOffset kan du [ändra tidszonen](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) används av färdiga entitet datetimeV2.|
|`verbose`|boolesk|Returnerar alla avsikter och deras resultat om värdet är true. Standardvärdet är false, vilket returnerar endast de översta avsikten.|
|`staging`|boolesk|Returnerar frågan från mellanlagring resultat miljön om inställd true. |
|`log`|boolesk|Loggar frågor som kan användas senare för [aktiv inlärning](luis-how-to-review-endoint-utt.md). Standardvärdet är true.|

### <a name="query-published-app"></a>Fråga efter publicerade appen

Ett exempel CURL-kommando för att fråga en behållare för en publicerad app är:

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APPLICATION_ID}?q=turn%20on%20the%20lights&staging=false&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
Att göra förfrågningar till den **mellanlagring** miljö, ändra den **mellanlagring** fråga string-parametervärde till true: 

`staging=true`

### <a name="query-trained-app"></a>Fråga tränade app

Ett exempel CURL-kommando för att fråga en behållare för en tränad app är: 

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APPLICATION_ID}/versions/{APPLICATION_VERSION}?q=turn%20on%20the%20lights&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
Versionsnamnet har högst 10 tecken och innehåller bara de tecken som tillåts i en URL. 

## <a name="import-the-endpoint-logs-for-active-learning"></a>Importera endpoint-loggarna för aktiv inlärning

Om en utdata mount anges för behållaren LUIS sparas app fråga loggfilerna i utdatakatalogen, där {INSTANCE_ID} är behållar-ID. Frågeloggen app innehåller fråga, svar och tidsstämplar för varje förutsägelsefråga som skickas till LUIS-behållaren. 

På följande plats visar kapslade katalogstrukturen för behållarens loggfiler.
`
/output/luis/{INSTANCE_ID}/
`
 
Välj din app från LUIS-portalen och välj sedan **importera endpoint loggarna** att ladda upp loggarna. 

![Importera behållarens loggfiler för aktiv inlärning](./media/luis-container-how-to/upload-endpoint-log-files.png)

När loggen har överförts [granska slutpunkten](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-review-endpoint-utterances) yttranden i LUIS-portalen.

## <a name="stop-the-container"></a>Stoppa behållaren

Om du vill stänga av behållaren i kommandoradsmiljön där behållaren körs, trycker du på **Ctrl + C**.

## <a name="troubleshooting"></a>Felsökning

Om du kör behållaren med ett utgående [montera](luis-container-configuration.md#mount-settings) och loggning är aktiverat, behållaren genererar loggfiler som är användbara för att felsöka problem som kan inträffa när startas eller körs i behållaren. 

## <a name="containers-api-documentation"></a>Behållarens API-dokumentation

Behållaren innehåller en fullständig uppsättning av dokumentationen för slutpunkter som en `Try it now` funktionen. Den här funktionen kan du ange dina inställningar i en webbaserad HTML-formulär och gör att frågan utan att behöva skriva någon kod. När frågan returnerar, ett exempel CURL-kommando har angetts för att visa de HTTP-rubrikerna och brödtext format som krävs. 

> [!TIP]
> Läs den [OpenAPI-specifikation](https://swagger.io/docs/specification/about/), som beskriver API-åtgärder som stöds av behållare, från den `/swagger` relativ URI. Exempel:
>
>  ```http
>  http://localhost:5000/swagger
>  ```

## <a name="billing"></a>Fakturering

LUIS behållare skickar information till Azure-fakturering, med en _Språkförståelse_ resurs på ditt Azure-konto. 

Cognitive Services-behållare är inte licensierad för att köra inte är ansluten till Azure för att mäta. Kunder måste du aktivera behållarna för att kommunicera faktureringsinformation med tjänsten Avläsning av programvara vid alla tidpunkter. Cognitive Services-behållare Skicka inte kunddata (uttryck) till Microsoft. 

Den `docker run` använder följande argument för fakturering:

| Alternativ | Beskrivning |
|--------|-------------|
| `ApiKey` | API-nyckeln för den _Språkförståelse_ resurs som används för att spåra faktureringsinformation.<br/>Värdet för det här alternativet måste anges till en API-nyckel för den etablerade LUIS Azure-resursen som anges i `Billing`. |
| `Billing` | Slutpunkten för den _Språkförståelse_ resurs som används för att spåra faktureringsinformation.<br/>Värdet för det här alternativet måste anges till slutpunkten för en etablerad LUIS Azure-resurs-URI.|
| `Eula` | Anger att du har godkänt licensen för behållaren.<br/>Värdet för det här alternativet måste anges till `accept`. |

> [!IMPORTANT]
> Alla tre alternativ måste anges med giltiga värden eller behållaren startar inte.

Mer information om alternativen finns i [konfigurera behållare](luis-container-configuration.md).

## <a name="unsupported-dependencies"></a>Beroenden som inte stöds

Du kan använda en LUIS-programmet om det **omfattar inte** någon av följande beroenden:

Konfigurationer som inte stöds|Information|
|--|--|
|Kulturer som stöds inte behållare| Tyska (de-DE)<br>Nederländska (nl-NL)<br>Japanska (ja-JP)<br>|
|Domäner som inte stöds|Fördefinierade domäner, inklusive fördefinierade domän avsikter och entiteter|
|Stöds inte entiteter för alla kulturer|[KeyPhrase](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-prebuilt-keyphrase) fördefinierade entitet för alla kulturer|
|Stöds inte entiteter för engelska (en-US) kultur|[GeographyV2](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-prebuilt-geographyv2) fördefinierade entiteter|
|Tal promotor|Externa beroenden stöds inte i behållaren.|
|Sentimentanalys|Externa beroenden stöds inte i behållaren.|
|Stavningskontroll i Bing|Externa beroenden stöds inte i behållaren.|

## <a name="summary"></a>Sammanfattning

I den här artikeln beskrivs begrepp och arbetsflöde för att hämta, installera och köra behållare för Språkförståelse (LUIS). Sammanfattningsvis:

* Språkförståelse (LUIS) tillhandahåller en Linux-behållare för Docker tillhandahåller endpoint fråga förutsägelser för yttranden.
* Behållaravbildningar laddas ned från Microsoft (MCR Container Registry).
* Behållaravbildningar som körs i Docker.
* Du kan använda REST API för att fråga behållarslutpunkter genom att ange värden URI: N för behållaren.
* Du måste ange faktureringsinformation när instanser skapades av en behållare.

> [!IMPORTANT]
> Cognitive Services-behållare är inte licensierad för att köra inte är ansluten till Azure för att mäta. Kunder måste du aktivera behållarna för att kommunicera faktureringsinformation med tjänsten Avläsning av programvara vid alla tidpunkter. Cognitive Services-behållare Skicka inte kunddata (t.ex. bild eller text som analyseras) till Microsoft.

## <a name="next-steps"></a>Nästa steg

* Granska [konfigurera behållare](luis-container-configuration.md) för konfigurationsinställningar
* Referera till [vanliga frågor (och svar FAQ)](luis-resources-faq.md) att lösa problem som rör LUIS-funktioner.