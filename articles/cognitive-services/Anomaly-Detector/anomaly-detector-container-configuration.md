---
title: Så här konfigurerar du en behållare för avvikelse detektor API
titleSuffix: Azure Cognitive Services
description: Körnings miljön för avvikelse detektorns API-behållare konfigureras `docker run` med hjälp av kommando argumenten. Den här behållaren har flera inställningar som krävs, tillsammans med några valfria inställningar.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: dapine
ms.openlocfilehash: 617a8fc823b7c40d047e5825dc31b095da132f29
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2019
ms.locfileid: "68321451"
---
# <a name="configure-anomaly-detector-containers"></a>Konfigurera avvikelse detektor behållare

Körnings miljön för **avvikelse detektor** behållare konfigureras med hjälp `docker run` av kommando argumenten. Den här behållaren har flera inställningar som krävs, tillsammans med några valfria inställningar. Flera [exempel](#example-docker-run-commands) kommandots är tillgängliga. De behållar-/regionsspecifika inställningarna är fakturerings inställningarna. 

# <a name="configuration-settings"></a>Konfigurationsinställningar

Den här behållaren har följande konfigurationsinställningar:

|Krävs|Inställning|Syfte|
|--|--|--|
|Ja|[ApiKey](#apikey-configuration-setting)|Används för att spåra faktureringsinformation.|
|Nej|[ApplicationInsights](#applicationinsights-setting)|Du kan lägga till [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) telemetri stöd till behållaren.|
|Ja|[Billing](#billing-configuration-setting)|Anger slut punkts-URI för tjänst resursen på Azure.|
|Ja|[Eula](#eula-setting)| Anger att du har godkänt licensen för behållaren.|
|Nej|[Fluentd](#fluentd-settings)|Skriva log och eventuellt måttdata till en Fluentd-server.|
|Nej|[Http-proxy](#http-proxy-credentials-settings)|Konfigurera en HTTP-proxy för att göra utgående begär Anden.|
|Nej|[Loggning](#logging-settings)|Ger ASP.NET Core loggning stöd för din behållare. |
|Nej|[Mounts](#mount-settings)|Läsa och skriva data från värddatorn till behållaren och från behållaren tillbaka till värddatorn.|

> [!IMPORTANT]
> Den [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), och [ `Eula` ](#eula-setting) inställningar används tillsammans, och du måste ange giltiga värden för alla tre av dem, annars din behållare startar inte. Läs mer om att använda dessa konfigurationsinställningar för att skapa en instans av en behållare, [fakturering](anomaly-detector-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>ApiKey konfigurationsinställning

Den `ApiKey` inställningen anger du Azure-resurs-nyckeln som används för att spåra faktureringsinformation för behållaren. Du måste ange ett värde för ApiKey och värdet måste vara en giltig nyckel för den _avvikelse detektor_ resurs som angetts för [`Billing`](#billing-configuration-setting) konfigurations inställningen.

Du hittar den här inställningen på följande plats:

* Azure-portalen: **Avvikelse detektor** Resurs hantering, under **nycklar**

## <a name="applicationinsights-setting"></a>Inställningen för ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Fakturering konfigurationsinställning

Inställningen anger slut punkts-URI för _avvikelse identifierings_ resursen på Azure som används för att mäta fakturerings information för behållaren. `Billing` Du måste ange ett värde för den här konfigurations inställningen och värdet måste vara en giltig slut punkts-URI för en _avvikelse detektor_ resurs på Azure.

Du hittar den här inställningen på följande plats:

* Azure-portalen: **Avvikelse detektor** Översikt, märkt`Endpoint`

|Krävs| Namn | Datatyp | Beskrivning |
|--|------|-----------|-------------|
|Ja| `Billing` | Sträng | Fakturering endpoint URI<br><br>Exempel:<br>`Billing=https://westus2.api.cognitive.microsoft.com` |

## <a name="eula-setting"></a>Licensvillkor för inställningen

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd-inställningar

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Inställningar för autentiseringsuppgifter för http-proxy

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Loggningsinställningar
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]


## <a name="mount-settings"></a>Monteringsinställningar

Använd bindning monterar för att läsa och skriva data till och från behållaren. Du kan ange en monteringspunkt som indata eller utdata mount genom att ange den `--mount` alternativet i den [docker kör](https://docs.docker.com/engine/reference/commandline/run/) kommando.

De avvikande detektor behållarna använder inte indata eller utdata monteras för att lagra utbildning eller tjänst data. 

Den exakta syntaxen hos montera värdplats varierar beroende på värdens operativsystem. Dessutom kanske [värd datorns](anomaly-detector-container-howto.md#the-host-computer)monterings plats inte är tillgänglig på grund av en konflikt mellan behörigheter som används av Docker-tjänstkontot och värd monterings platsens behörigheter. 

|Valfri| Namn | Datatyp | Beskrivning |
|-------|------|-----------|-------------|
|Tillåts inte| `Input` | Sträng | Avvikelse detektor behållare använder inte detta.|
|Valfri| `Output` | Sträng | Utdata mount-mål. Standardvärdet är `/output`. Det här är platsen för loggarna. Detta inkluderar behållar loggar. <br><br>Exempel:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Exempel docker-kommandon 

I följande exempel används konfigurationsinställningarna som illustrerar hur du skriver och använda `docker run` kommandon.  När du kör, behållaren fortsätter att köras tills du [stoppa](anomaly-detector-container-howto.md#stop-the-container) den.

* **Rad fortsättnings avstånd**: Docker- `\`kommandona i följande avsnitt använder omvänt snedstreck, som ett linje fortsättnings Character för ett bash-gränssnitt. Ersätta eller ta bort detta baserat på din värdoperativsystemet. Linje fortsättnings tecknet för Windows är till exempel ett cirkumflex, `^`. Ersätt omvänt snedstreck med cirkumflex. 
* **Argument ordning**: Ändra inte ordningen på argumenten om du inte är bekant med Docker-behållare.

Ersätt värdet inom hakparenteser, `{}`med dina egna värden:

| Platshållare | Värde | Format eller exempel |
|-------------|-------|---|
|{API_KEY} | Slut punkts nyckeln för avvikelse identifierings resursen. |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{ENDPOINT_URI} | Värdet för fakturerings slut punkten inklusive region.|`https://westus2.api.cognitive.microsoft.com`|

> [!IMPORTANT]
> Den `Eula`, `Billing`, och `ApiKey` alternativ måste anges för att köra behållaren, i annat fall startar inte behållaren.  Mer information finns i [fakturering](anomaly-detector-container-howto.md#billing).
> ApiKey-värdet är **nyckeln** från sidan med resurs nycklar för Azure avvikelser. 

## <a name="anomaly-detector-container-docker-examples"></a>Docker-exempel för avvikande detektor behållare

Följande Docker-exempel är för behållaren för avvikelse detektor. 

### <a name="basic-example"></a>Grundläggande exempel 

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example-with-command-line-arguments"></a>Exempel på loggning med kommandoradsargument

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector \
  Eula=accept \
  Billing={ENDPOINT_URI} ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```
