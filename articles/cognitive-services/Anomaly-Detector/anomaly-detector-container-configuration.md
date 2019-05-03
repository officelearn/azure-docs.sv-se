---
title: Konfigurera behållare - Avvikelseidentifiering detektor
titleSuffix: Azure Cognitive Services
description: Körningsmiljö för Avvikelseidentifiering detektor behållare konfigureras med hjälp av den `docker run` kommandot argument. Den här behållaren har flera inställningar som krävs, tillsammans med några valfria inställningar.
services: cognitive-services
author: aahill
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: article
ms.date: 05/07/2019
ms.author: aahi
ms.openlocfilehash: 0d09ce29aa5431de3eb82e5d9fe7440d4e3352e1
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026396"
---
# <a name="configure-anomaly-detector-containers"></a>Konfigurera Avvikelseidentifiering detektor behållare

Den **Avvikelseidentifiering detektor** behållare körningsmiljö konfigureras med hjälp av den `docker run` kommandot argument. Den här behållaren har flera inställningar som krävs, tillsammans med några valfria inställningar. Flera [exempel](#example-docker-run-commands) kommandots är tillgängliga. Behållare-specifika inställningar är de fakturering inställningarna. 

# <a name="configuration-settings"></a>Konfigurationsinställningar

Den här behållaren har följande konfigurationsinställningar:

|Krävs|Inställning|Syfte|
|--|--|--|
|Ja|[ApiKey](#apikey-configuration-setting)|Används för att spåra faktureringsinformation.|
|Nej|[ApplicationInsights](#applicationinsights-setting)|Du kan lägga till [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) telemetri stöd till behållaren.|
|Ja|[Billing](#billing-configuration-setting)|Anger URI för tjänstresursen på Azure.|
|Ja|[Eula](#eula-setting)| Anger att du har godkänt licensen för behållaren.|
|Nej|[Fluentd](#fluentd-settings)|Skriva log och eventuellt måttdata till en Fluentd-server.|
|Nej|[HTTP-Proxy](#http-proxy-credentials-settings)|Konfigurera en HTTP-proxy för utgående förfrågningar.|
|Nej|[Loggning](#logging-settings)|Ger ASP.NET Core loggning stöd för din behållare. |
|Nej|[Mounts](#mount-settings)|Läsa och skriva data från värddatorn till behållaren och från behållaren tillbaka till värddatorn.|

> [!IMPORTANT]
> Den [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), och [ `Eula` ](#eula-setting) inställningar används tillsammans, och du måste ange giltiga värden för alla tre av dem, annars din behållare startar inte. Läs mer om att använda dessa konfigurationsinställningar för att skapa en instans av en behållare, [fakturering](anomaly-detector-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>ApiKey konfigurationsinställning

Den `ApiKey` inställningen anger du Azure-resurs-nyckeln som används för att spåra faktureringsinformation för behållaren. Du måste ange ett värde för ApiKey och värdet måste vara en giltig nyckel för den _Avvikelseidentifiering detektor_ resurs som angetts för den [ `Billing` ](#billing-configuration-setting) konfigurationsinställning.

Den här inställningen kan hittas på följande plats:

* Azure-portalen: **Avvikelseidentifiering detektor** resurshantering under **nycklar**

## <a name="applicationinsights-setting"></a>Inställningen för ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Fakturering konfigurationsinställning

Den `Billing` inställningen anger URI för den _Avvikelseidentifiering detektor_ resurs på Azure som används för att läsa av faktureringsinformation för behållaren. Du måste ange ett värde för den här inställningen och värdet måste vara en giltig URI-slutpunkt för en _Avvikelseidentifiering detektor_ resurs på Azure.

Den här inställningen kan hittas på följande plats:

* Azure-portalen: **Avvikelseidentifiering detektor** översikt, märkt `Endpoint`

|Krävs| Namn | Datatyp | Beskrivning |
|--|------|-----------|-------------|
|Ja| `Billing` | Sträng | Fakturering endpoint URI<br><br>Exempel:<br>`Billing=https://westus2.api.cognitive.microsoft.com` |

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

Avvikelseidentifiering detektor behållare använder inte indata eller utdata monterar för att lagra utbildning eller tjänstdata. 

Den exakta syntaxen hos montera värdplats varierar beroende på värdens operativsystem. Dessutom kan den [värddatorn](anomaly-detector-container-howto.md#the-host-computer)'s montera platsen är kanske inte tillgänglig på grund av en konflikt mellan behörigheter som används av Docker-tjänstkontot och värden montera plats behörigheter. 

|Valfri| Namn | Datatyp | Beskrivning |
|-------|------|-----------|-------------|
|Inte tillåtet| `Input` | String | Avvikelseidentifiering detektor behållare Använd inte detta.|
|Valfri| `Output` | String | Utdata mount-mål. Standardvärdet är `/output`. Det här är platsen för loggarna. Detta inkluderar behållarloggarna. <br><br>Exempel:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Exempel docker-kommandon 

I följande exempel används konfigurationsinställningarna som illustrerar hur du skriver och använda `docker run` kommandon.  När du kör, behållaren fortsätter att köras tills du [stoppa](anomaly-detector-container-howto.md#stop-the-container) den.

* **Fortsättning på raden tecknet**: Docker-kommandon i följande avsnitt använder det omvända snedstrecket `\`, som en fortsättning tecknet för ett bash-gränssnitt. Ersätta eller ta bort detta baserat på din värdoperativsystemet. Till exempel rad fortsättning tecken för windows är en hatt `^`. Ersätt det omvända snedstrecket med av cirkumflexet. 
* **Argumentet order**: Ändra inte argumentens ordning om du inte är bekant med Docker-behållare.

Ersätt värdet med hakparenteser, `{}`, med dina egna värden:

| Platshållare | Värde | Format eller exempel |
|-------------|-------|---|
|{BILLING_KEY} | Slutpunktsnyckeln för Avvikelseidentifiering detektor resursen. |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{BILLING_ENDPOINT_URI} | Fakturering slutpunktsvärdet inklusive region.|`https://westus2.api.cognitive.microsoft.com`|

> [!IMPORTANT]
> Den `Eula`, `Billing`, och `ApiKey` alternativ måste anges för att köra behållaren, i annat fall startar inte behållaren.  Mer information finns i [fakturering](anomaly-detector-container-howto.md#billing).
> ApiKey-värdet är den **nyckel** från sidan nycklar för Azure Avvikelseidentifiering detektor resurs. 

## <a name="anomaly-detector-container-docker-examples"></a>Avvikelseidentifiering detektor behållare Docker-exempel

I följande exempel Docker är avsedda för Avvikelseidentifiering detektor behållaren. 

### <a name="basic-example"></a>Grundläggande exempel 

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector \
  Eula=accept \
  Billing={BILLING_ENDPOINT_URI} \
  ApiKey={BILLING_KEY} 
  ```

### <a name="logging-example-with-command-line-arguments"></a>Exempel på loggning med kommandoradsargument

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector \
  Eula=accept \
  Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```
