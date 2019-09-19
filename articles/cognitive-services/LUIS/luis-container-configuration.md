---
title: Inställningar för Docker-behållare – LUIS
titleSuffix: Azure Cognitive Services
description: LUIS-behållaren körningsmiljö konfigureras med hjälp av den `docker run` kommandot argument. LUIS har flera inställningar som krävs, tillsammans med några valfria inställningar.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: dapine
ms.openlocfilehash: 9760475886ecb0f20d9f0f3981eab8246643da21
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2019
ms.locfileid: "71101988"
---
# <a name="configure-language-understanding-docker-containers"></a>Konfigurera Language Understanding Docker-behållare 

**Language Understanding** (Luis) container Runtime Environment konfigureras med hjälp av `docker run` kommando argumenten. LUIS har flera inställningar som krävs, tillsammans med några valfria inställningar. Flera [exempel](#example-docker-run-commands) kommandots är tillgängliga. Behållare-specifika inställningarna är indata [Monteringsinställningar](#mount-settings) och fakturering inställningarna. 

## <a name="configuration-settings"></a>Konfigurationsinställningar

Den här behållaren har följande konfigurationsinställningar:

|Krävs|Inställning|Syfte|
|--|--|--|
|Ja|[ApiKey](#apikey-setting)|Används för att spåra faktureringsinformation.|
|Nej|[ApplicationInsights](#applicationinsights-setting)|Du kan lägga till [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) telemetri stöd till behållaren.|
|Ja|[Billing](#billing-setting)|Anger slut punkts-URI för tjänst resursen på Azure.|
|Ja|[Eula](#eula-setting)| Anger att du har godkänt licensen för behållaren.|
|Nej|[Fluentd](#fluentd-settings)|Skriva log och eventuellt måttdata till en Fluentd-server.|
|Nej|[Http-proxy](#http-proxy-credentials-settings)|Konfigurera en HTTP-proxy för att göra utgående begär Anden.|
|Nej|[Loggning](#logging-settings)|Ger ASP.NET Core loggning stöd för din behållare. |
|Ja|[Mounts](#mount-settings)|Läsa och skriva data från värddatorn till behållaren och från behållaren tillbaka till värddatorn.|

> [!IMPORTANT]
> Den [ `ApiKey` ](#apikey-setting), [ `Billing` ](#billing-setting), och [ `Eula` ](#eula-setting) inställningar används tillsammans, och du måste ange giltiga värden för alla tre av dem, annars din behållare startar inte. Läs mer om att använda dessa konfigurationsinställningar för att skapa en instans av en behållare, [fakturering](luis-container-howto.md#billing).

## <a name="apikey-setting"></a>ApiKey inställningen

Den `ApiKey` inställningen anger du Azure-resurs-nyckeln som används för att spåra faktureringsinformation för behållaren. Du måste ange ett värde för ApiKey och värdet måste vara en giltig nyckel för den _Cognitive Services_ resurs som angetts för [`Billing`](#billing-setting) konfigurations inställningen.

Du hittar den här inställningen på följande platser:

* Azure-portalen: **Cognitive Services** Resurs hantering, under **nycklar**
* LUIS-Portal: Sidan **nycklar och slut punkts inställningar** . 

Använd inte starter-nyckel eller nyckeln för redigering. 

## <a name="applicationinsights-setting"></a>Inställningen för ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-setting"></a>Inställningen för fakturering

Inställningen anger slut punkts-URI för den Cognitive Services resursen på Azure som används för att mäta fakturerings information för behållaren. `Billing` Du måste ange ett värde för den här konfigurations inställningen och värdet måste vara en giltig slut punkts-URI för en _Cognitive Services_ -resurs på Azure. Behållar rapporteringen visar var 10 till 15: e minut.

Du hittar den här inställningen på följande platser:

* Azure-portalen: **Cognitive Services** Översikt, märkt`Endpoint`
* LUIS-Portal: Sidan **nycklar och slut punkts inställningar** som en del av slut punktens URI.

Kom ihåg att inkludera `luis/v2.0` dirigeringen i URL: en som visas i följande tabell:


|Obligatorisk| Namn | Datatyp | Beskrivning |
|--|------|-----------|-------------|
|Ja| `Billing` | Sträng | Fakturering endpoint URI<br><br>Exempel:<br>`Billing=https://westus.api.cognitive.microsoft.com/luis/v2.0` |

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

LUIS-behållare använder inte indata eller utdata monterar för att lagra utbildning eller tjänstdata. 

Den exakta syntaxen hos montera värdplats varierar beroende på värdens operativsystem. Dessutom kan den [värddatorn](luis-container-howto.md#the-host-computer)'s montera platsen är kanske inte tillgänglig på grund av en konflikt mellan behörigheter som används av docker-tjänstkontot och värden montera plats behörigheter. 

I följande tabell beskrivs de inställningar som stöds.

|Krävs| Namn | Datatyp | Beskrivning |
|-------|------|-----------|-------------|
|Ja| `Input` | Sträng | Inkommande monterings-mål. Standardvärdet är `/input`. Det här är platsen för LUIS-paketfilerna. <br><br>Exempel:<br>`--mount type=bind,src=c:\input,target=/input`|
|Nej| `Output` | Sträng | Utdata mount-mål. Standardvärdet är `/output`. Det här är platsen för loggarna. Detta inkluderar LUIS frågeloggar och behållarloggar. <br><br>Exempel:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Exempel docker-kommandon

I följande exempel används konfigurationsinställningarna som illustrerar hur du skriver och använda `docker run` kommandon.  När du kör, behållaren fortsätter att köras tills du [stoppa](luis-container-howto.md#stop-the-container) den.

* I de här exemplen används katalogen `C:` från enheten för att undvika eventuella behörighets konflikter i Windows. Om du vill använda en viss katalog som den inkommande katalogen kan du behöva ge docker tjänsten behörighet. 
* Ändra inte argumentens ordning om du inte är bekant med docker-behållare.
* Om du använder ett annat operativ system använder du rätt konsol/Terminal, kommandosyntax för montering och linje fortsättnings text för systemet. Dessa exempel förutsätter en Windows-konsol med ett linje `^`fortsättnings steg. Eftersom behållaren är ett Linux-operativsystem använder mål-Mount en syntax för en mappvy i Linux-typ.

Kom ihåg att inkludera `luis/v2.0` dirigeringen i URL: en som visas i följande tabell.

Ersätt {_argument_name_} med dina egna värden:

| Platshållare | Värde | Format eller exempel |
|-------------|-------|---|
| **{API_KEY}** | `LUIS` Resursens slut punkts nyckel på sidan med `LUIS` Azure-nycklar. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | Värdet för fakturerings slut punkten är tillgängligt på sidan `LUIS` Azure-översikt.| Se [samla in obligatoriska parametrar](luis-container-howto.md#gathering-required-parameters) för explicita exempel. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> Den `Eula`, `Billing`, och `ApiKey` alternativ måste anges för att köra behållaren, i annat fall startar inte behållaren. Mer information finns i [fakturering](luis-container-howto.md#billing).
> ApiKey-värdet är **nyckeln** på sidan nycklar och slut punkter i Luis-portalen och finns också på sidan med Azures `Cognitive Services` resurs nycklar. 

### <a name="basic-example"></a>Grundläggande exempel

I följande exempel har de minsta antalet argument som går att köra behållaren:

```console
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis:latest ^
Eula=accept ^
Billing={ENDPOINT_URL} ^
ApiKey={API_KEY}
```

### <a name="applicationinsights-example"></a>Exempel för ApplicationInsights

I följande exempel anger argumentet ApplicationInsights att skicka telemetri till Application Insights medan behållaren körs:

```console
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis:latest ^
Eula=accept ^
Billing={ENDPOINT_URL} ^
ApiKey={API_KEY} ^
InstrumentationKey={INSTRUMENTATION_KEY}
```

### <a name="logging-example"></a>Loggnings exempel 

Följande kommando anger loggningsnivån, `Logging:Console:LogLevel`, för att konfigurera loggningsnivån till [ `Information` ](https://msdn.microsoft.com). 

```console
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis:latest ^
Eula=accept ^
Billing={ENDPOINT_URL} ^
ApiKey={API_KEY} ^
Logging:Console:LogLevel:Default=Information
```

## <a name="next-steps"></a>Nästa steg

* Granska [hur du installerar och kör behållare](luis-container-howto.md)
* Se [fel sökning](troubleshooting.md) för att lösa problem som rör Luis-funktioner.
* Använd fler [Cognitive Services behållare](../cognitive-services-container-support.md)
