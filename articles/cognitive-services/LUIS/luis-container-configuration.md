---
title: Inställningar för docker-behållare
titleSuffix: Language Understanding - Azure Cognitive Services
description: LUIS-behållaren körningsmiljö konfigureras med hjälp av den `docker run` kommandot argument. LUIS har flera inställningar som krävs, tillsammans med några valfria inställningar.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: diberry
ms.openlocfilehash: 31d6725b6e02bbc583ad80f235360574941a97d3
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54468343"
---
# <a name="configure-language-understanding-docker-containers"></a>Konfigurera Language Understanding docker-behållare 

Körningsmiljö för Språkförståelse (LUIS) behållare konfigureras med hjälp av den `docker run` kommandot argument. LUIS har flera inställningar som krävs, tillsammans med några valfria inställningar. Flera [exempel](#example-docker-run-commands) kommandots är tillgängliga. Behållare-specifika inställningarna är indata [Monteringsinställningar](#mount-settings) och fakturering inställningarna. 

Behållarinställningar är [hierarkiska](#hierarchical-settings) och kan ställas in med [miljövariabler](#environment-variable-settings) eller docker [kommandoradsargument](#command-line-argument-settings).

## <a name="configuration-settings"></a>Konfigurationsinställningar

Den här behållaren har följande konfigurationsinställningar:

|Krävs|Inställning|Syfte|
|--|--|--|
|Ja|[ApiKey](#apikey-setting)|Används för att spåra faktureringsinformation.|
|Nej|[ApplicationInsights](#applicationinsights-setting)|Du kan lägga till [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) telemetri stöd till behållaren.|
|Ja|[Billing](#billing-setting)|Anger URI för tjänstresursen på Azure.|
|Ja|[Licensvillkor](#eula-setting)| Anger att du har godkänt licensen för behållaren.|
|Nej|[Fluentd](#fluentd-settings)|Skriva log och eventuellt måttdata till en Fluentd-server.|
|Nej|[HTTP-Proxy](#http-proxy-credentials-settings)|Konfigurera en HTTP-proxy för utgående förfrågningar.|
|Nej|[Loggning](#logging-settings)|Ger ASP.NET Core loggning stöd för din behållare. |
|Ja|[Monterar](#mount-settings)|Läsa och skriva data från värddatorn till behållare och behållaren tillbaka till värddatorn.|

> [!IMPORTANT]
> Den [ `ApiKey` ](#apikey-setting), [ `Billing` ](#billing-setting), och [ `Eula` ](#eula-setting) inställningar används tillsammans, och du måste ange giltiga värden för alla tre av dem, annars din behållare startar inte. Läs mer om att använda dessa konfigurationsinställningar för att skapa en instans av en behållare, [fakturering](luis-container-howto.md#billing).

## <a name="apikey-setting"></a>ApiKey inställningen

Den `ApiKey` inställningen anger du Azure-resurs-nyckeln som används för att spåra faktureringsinformation för behållaren. Du måste ange ett värde för ApiKey och värdet måste vara en giltig nyckel för den _Språkförståelse_ resurs som angetts för den [ `Billing` ](#billing-setting) konfigurationsinställning.

Den här inställningen kan hittas på följande platser:

* Azure-portalen: **Language Understanding** resurshantering under **nycklar**
* LUIS-portalen: **Nycklar och slutpunktsinställningarna** sidan. 

Använd inte starter-nyckel eller nyckeln för redigering. 

## <a name="applicationinsights-setting"></a>Inställningen för ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-setting"></a>Inställningen för fakturering

Den `Billing` inställningen anger URI för den _Språkförståelse_ resurs på Azure som används för att läsa av faktureringsinformation för behållaren. Du måste ange ett värde för den här inställningen och värdet måste vara en giltig URI-slutpunkt för en _Språkförståelse_ resurs på Azure.

Den här inställningen kan hittas på följande platser:

* Azure-portalen: **Language Understanding** översikt, märkt `Endpoint`
* LUIS-portalen: **Nycklar och slutpunktsinställningarna** sidan som en del av URI-slutpunkten.

|Krävs| Namn | Datatyp | Beskrivning |
|--|------|-----------|-------------|
|Ja| `Billing` | Sträng | Fakturering endpoint URI<br><br>Exempel:<br>`Billing=https://westus.api.cognitive.microsoft.com/luis/v2.0` |

## <a name="eula-setting"></a>Licensvillkor för inställningen

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd-inställningar


[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>HTTP-proxyinställningarna för autentiseringsuppgifter

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Loggningsinställningar
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]


## <a name="mount-settings"></a>Monteringsinställningar

Använd bindning monterar för att läsa och skriva data till och från behållaren. Du kan ange en monteringspunkt som indata eller utdata mount genom att ange den `--mount` alternativet i den [docker kör](https://docs.docker.com/engine/reference/commandline/run/) kommando. 

LUIS-behållare använder inte indata eller utdata monterar för att lagra utbildning eller tjänstdata. 

Den exakta syntaxen hos montera värdplats varierar beroende på värdens operativsystem. Dessutom kan den [värddatorn](luis-container-howto.md#the-host-computer)'s montera platsen är kanske inte tillgänglig på grund av en konflikt mellan behörigheter som används av docker-tjänstkontot och värden montera plats behörigheter. 

I följande tabell beskrivs de inställningar som stöds.

|Krävs| Namn | Datatyp | Beskrivning |
|-------|------|-----------|-------------|
|Ja| `Input` | Sträng | Inkommande monterings-mål. Standardvärdet är `/input`. Det här är platsen för LUIS-paketfilerna. <br><br>Exempel:<br>`--mount type=bind,src=c:\input,target=/input`|
|Nej| `Output` | Sträng | Utdata mount-mål. Standardvärdet är `/output`. Det här är platsen för loggarna. Detta inkluderar LUIS frågeloggar och behållarloggar. <br><br>Exempel:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="hierarchical-settings"></a>Hierarkisk inställningar

[!INCLUDE [Container shared configuration hierarchical settings](../../../includes/cognitive-services-containers-configuration-shared-hierarchical-settings.md)]


## <a name="example-docker-run-commands"></a>Exempel docker-kommandon

I följande exempel används konfigurationsinställningarna som illustrerar hur du skriver och använda `docker run` kommandon.  När du kör, behållaren fortsätter att köras tills du [stoppa](luis-container-howto.md#stop-the-container) den.


* **Fortsättning på raden tecknet**: Docker-kommandon i följande avsnitt använder det omvända snedstrecket `\`, som en fortsättning tecknet. Ersätta eller ta bort detta baserat på din värdoperativsystemet. 
* **Argumentet order**: Ändra inte argumentens ordning om du inte är bekant med docker-behållare.

Ersätt {_argument_name_} med dina egna värden:

| Platshållare | Värde | Format eller exempel |
|-------------|-------|---|
|{ENDPOINT_KEY} | Slutpunktsnyckeln av tränade LUIS-programmet. |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{BILLING_ENDPOINT} | Fakturering slutpunktsvärdet är tillgänglig på Azure portal Language Understanding översiktssidan.|https://westus.api.cognitive.microsoft.com/luis/v2.0|

> [!IMPORTANT]
> Den `Eula`, `Billing`, och `ApiKey` alternativ måste anges för att köra behållaren, i annat fall startar inte behållaren.  Mer information finns i [fakturering](luis-container-howto.md#billing).
> ApiKey-värdet är den **nyckel** från nycklar och slutpunkter i LUIS-portalen och är också tillgängliga på sidan nycklar för Azure Language Understanding-resurs. 

### <a name="basic-example"></a>Grundläggande exempel

I följande exempel har de minsta antalet argument som går att köra behållaren:

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 \
--mount type=bind,src=c:\input,target=/input \
--mount type=bind,src=c:\output,target=/output \
mcr.microsoft.com/azure-cognitive-services/luis:latest \
Eula=accept \
Billing={BILLING_ENDPOINT} \
ApiKey={ENDPOINT_KEY}
```

> [!Note] 
> Föregående kommando använder katalogen av den `c:` enhet för att undvika eventuella behörighetskonflikter på Windows. Om du vill använda en viss katalog som den inkommande katalogen kan du behöva ge docker tjänsten behörighet. Kommandot ovan docker använder det omvända snedstrecket `\`, som en fortsättning tecknet. Ersätta eller ta bort detta baserat på din [värddatorn](luis-container-howto.md#the-host-computer) operativsystemets krav. Ändra inte argumentens ordning om du inte är bekant med docker-behållare.


### <a name="applicationinsights-example"></a>Exempel för ApplicationInsights

I följande exempel anger argumentet ApplicationInsights att skicka telemetri till Application Insights medan behållaren körs:

```bash
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 \
--mount type=bind,src=c:\input,target=/input \
--mount type=bind,src=c:\output,target=/output \
mcr.microsoft.com/azure-cognitive-services/luis:latest \
Eula=accept \
Billing={BILLING_ENDPOINT} \
ApiKey={ENDPOINT_KEY}
InstrumentationKey={INSTRUMENTATION_KEY}
```

### <a name="logging-example-with-command-line-arguments"></a>Exempel på loggning med kommandoradsargument

Följande kommando anger loggningsnivån, `Logging:Console:LogLevel`, för att konfigurera loggningsnivån till [ `Information` ](https://msdn.microsoft.com). 

```bash
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 \
--mount type=bind,src=c:\input,target=/input \
--mount type=bind,src=c:\output,target=/output \
mcr.microsoft.com/azure-cognitive-services/luis:latest \
Eula=accept \
Billing={BILLING_ENDPOINT} \
ApiKey={ENDPOINT_KEY} \
Logging:Console:LogLevel=Information
```

### <a name="logging-example-with-environment-variable"></a>Exempel på loggning med miljövariabeln

Använd för följande kommandon en miljövariabel med namnet `Logging:Console:LogLevel` konfigurera loggningsnivån till [ `Information` ](https://msdn.microsoft.com). 

```bash
SET Logging:Console:LogLevel=Information
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 \
--mount type=bind,src=c:\input,target=/input \
--mount type=bind,src=c:\output,target=/output \
mcr.microsoft.com/azure-cognitive-services/luis:latest \
Eula=accept \
Billing={BILLING_ENDPOINT} \
ApiKey={APPLICATION_ID} \
```

## <a name="next-steps"></a>Nästa steg

* Granska [hur du installerar och kör behållare](luis-container-howto.md)
* Referera till [vanliga frågor (och svar FAQ)](luis-resources-faq.md) att lösa problem som rör LUIS-funktioner.
* Använder mer [Cognitive Services-behållare](../cognitive-services-container-support.md)