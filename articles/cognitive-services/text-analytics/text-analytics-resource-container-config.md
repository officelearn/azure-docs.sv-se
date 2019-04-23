---
title: Konfigurera containrar
titlesuffix: Text Analytics - Azure Cognitive Services
description: Textanalys ger varje behållare med ett gemensamt ramverk för konfiguration, så att du enkelt kan konfigurera och hantera inställningar för lagring, loggning och telemetri och säkerhet för dina behållare.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: diberry
ms.openlocfilehash: 1333aefc145e95223624f42a28ec0bb31ab70065
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60011766"
---
# <a name="configure-text-analytics-docker-containers"></a>Konfigurera textanalys docker-behållare

Textanalys ger varje behållare med ett gemensamt ramverk för konfiguration, så att du enkelt kan konfigurera och hantera inställningar för lagring, loggning och telemetri och säkerhet för dina behållare.

## <a name="configuration-settings"></a>Konfigurationsinställningar

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> Den [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), och [ `Eula` ](#eula-setting) inställningar används tillsammans, och du måste ange giltiga värden för alla tre av dem, annars din behållare startar inte. Läs mer om att använda dessa konfigurationsinställningar för att skapa en instans av en behållare, [fakturering](how-tos/text-analytics-how-to-install-containers.md#billing).

## <a name="apikey-configuration-setting"></a>ApiKey konfigurationsinställning

Den `ApiKey` inställningen anger du Azure-resurs-nyckeln som används för att spåra faktureringsinformation för behållaren. Du måste ange ett värde för ApiKey och värdet måste vara en giltig nyckel för den _Cognitive Services_ resurs som angetts för den [ `Billing` ](#billing-configuration-setting) konfigurationsinställning.

Den här inställningen kan hittas på följande plats:

* Azure-portalen: **Cognitive Services** resurshantering under **nycklar**

## <a name="applicationinsights-setting"></a>Inställningen för ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Fakturering konfigurationsinställning

Den `Billing` inställningen anger URI för den _Cognitive Services_ resurs på Azure som används för att läsa av faktureringsinformation för behållaren. Du måste ange ett värde för den här inställningen och värdet måste vara en giltig slutpunkt URI för ett __Cognitive Services_ resurs på Azure. Behållaren rapporterar användning ungefär var 10 – 15 minuter.

Den här inställningen kan hittas på följande plats:

* Azure-portalen: **Cognitive Services** översikt, märkt `Endpoint`

Du måste lägga till den `text/analytics/v2.0` routning till slutpunkten URI som du ser i exemplet nedan BILLING_ENDPOINT_URI.

|Krävs| Namn | Datatyp | Beskrivning |
|--|------|-----------|-------------|
|Ja| `Billing` | Sträng | Fakturering endpoint URI<br><br>Exempel:<br>`Billing=https://westus.api.cognitive.microsoft.com/text/analytics/v2.1` |

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

Text Analytics-behållare använder inte indata eller utdata monterar för att lagra utbildning eller tjänstdata. 

Den exakta syntaxen hos montera värdplats varierar beroende på värdens operativsystem. Dessutom kan den [värddatorn](how-tos/text-analytics-how-to-install-containers.md#the-host-computer)'s montera platsen är kanske inte tillgänglig på grund av en konflikt mellan behörigheter som används av docker-tjänstkontot och värden montera plats behörigheter. 

|Valfri| Name | Datatyp | Beskrivning |
|-------|------|-----------|-------------|
|Inte tillåtet| `Input` | String | Text Analytics behållare Använd inte detta.|
|Valfri| `Output` | String | Utdata mount-mål. Standardvärdet är `/output`. Det här är platsen för loggarna. Detta inkluderar behållarloggarna. <br><br>Exempel:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Exempel docker-kommandon 

I följande exempel används konfigurationsinställningarna som illustrerar hur du skriver och använda `docker run` kommandon.  När du kör, behållaren fortsätter att köras tills du [stoppa](how-tos/text-analytics-how-to-install-containers.md#stop-the-container) den.

* **Fortsättning på raden tecknet**: Docker-kommandon i följande avsnitt använder det omvända snedstrecket `\`, som en fortsättning tecknet. Ersätta eller ta bort detta baserat på din värdoperativsystemet. 
* **Argumentet order**: Ändra inte argumentens ordning om du inte är bekant med docker-behållare.

Du måste lägga till den `text/analytics/v2.0` routning till slutpunkten URI som du ser i exemplet nedan BILLING_ENDPOINT_URI.

Ersätt {_argument_name_} med dina egna värden:

| Platshållare | Värde | Format eller exempel |
|-------------|-------|---|
|{BILLING_KEY} | Slutpunktsnyckeln av den `Cognitive Services` resurs som är tillgängliga på Azure `Cognitive Services` sidan nycklar. |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{BILLING_ENDPOINT_URI} | Fakturering slutpunktsvärdet är tillgänglig på Azure `Cognitive Services` översiktssidan.|`https://westus.api.cognitive.microsoft.com/text/analytics/v2.1`|

> [!IMPORTANT]
> Den `Eula`, `Billing`, och `ApiKey` alternativ måste anges för att köra behållaren, i annat fall startar inte behållaren.  Mer information finns i [fakturering](how-tos/text-analytics-how-to-install-containers.md#billing).
> ApiKey-värdet är den **nyckel** från Azure `Cognitive Services` resurssida nycklar. 

## <a name="key-phrase-extraction-container-docker-examples"></a>Diskussionsämne extrahering behållare docker-exempel

I följande exempel docker är för behållaren för extrahering av diskussionsämne. 

### <a name="basic-example"></a>Grundläggande exempel 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/keyphrase Eula=accept Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY} 
  ```

### <a name="logging-example"></a>Exempel för loggning 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/keyphrase Eula=accept Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY} Logging:Console:LogLevel:Default=Information
  ```

## <a name="language-detection-container-docker-examples"></a>Språk identifiering av behållare, docker exempel

I följande exempel docker är för behållaren för identifiering av språk. 

### <a name="basic-example"></a>Grundläggande exempel

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/language Eula=accept Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY} 
  ```

### <a name="logging-example"></a>Exempel för loggning

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/language Eula=accept Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY} Logging:Console:LogLevel:Default=Information
  ```
 
## <a name="sentiment-analysis-container-docker-examples"></a>Sentiment analysis behållare docker-exempel

I följande exempel docker är för behållaren sentiment analys. 

### <a name="basic-example"></a>Grundläggande exempel

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/sentiment Eula=accept Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY} 
  ```

### <a name="logging-example"></a>Exempel för loggning

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/sentiment Eula=accept Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY} Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>Nästa steg

* Granska [hur du installerar och kör behållare](how-tos/text-analytics-how-to-install-containers.md)
* Använder mer [Cognitive Services-behållare](../cognitive-services-container-support.md)
