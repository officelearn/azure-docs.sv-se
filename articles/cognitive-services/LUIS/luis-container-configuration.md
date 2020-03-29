---
title: Docker-behållarinställningar - LUIS
titleSuffix: Azure Cognitive Services
description: LUIS-behållarkörningsmiljön är konfigurerad med hjälp av kommandoargumenten. `docker run` LUIS har flera nödvändiga inställningar, tillsammans med några valfria inställningar.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/07/2019
ms.author: dapine
ms.openlocfilehash: a30fcd0ec7e53c78876596baf787639e81c638db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "73795016"
---
# <a name="configure-language-understanding-docker-containers"></a>Konfigurera Docker-behållare för språkbeståelse 

Luis-behållarens körningsmiljö **(Language Understanding)** `docker run` är konfigurerad med hjälp av kommandoargumenten. LUIS har flera nödvändiga inställningar, tillsammans med några valfria inställningar. Flera [exempel på](#example-docker-run-commands) kommandot är tillgängliga. De behållarspecifika inställningarna är [indatamonteringsinställningarna](#mount-settings) och faktureringsinställningarna. 

## <a name="configuration-settings"></a>Konfigurationsinställningar

Den här behållaren har följande konfigurationsinställningar:

|Krävs|Inställning|Syfte|
|--|--|--|
|Ja|[ApiKey (ApiKey)](#apikey-setting)|Används för att spåra faktureringsinformation.|
|Inga|[ApplicationInsights](#applicationinsights-setting)|Gör att du kan lägga till [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) telemetri stöd till din behållare.|
|Ja|[Fakturering](#billing-setting)|Anger slutpunkts-URI för tjänstresursen på Azure.|
|Ja|[Eula](#eula-setting)| Anger att du har accepterat licensen för behållaren.|
|Inga|[Flytande](#fluentd-settings)|Skriv logg och, eventuellt, måttdata till en Fluentd-server.|
|Inga|[Http Proxy](#http-proxy-credentials-settings)|Konfigurera en HTTP-proxy för att göra utgående begäranden.|
|Inga|[Loggning](#logging-settings)|Innehåller ASP.NET Core-loggningsstöd för din behållare. |
|Ja|[Fästen](#mount-settings)|Läsa och skriva data från värddator till behållare och från behållare tillbaka till värddatorn.|

> [!IMPORTANT]
> [`ApiKey`](#apikey-setting)Inställningarna [`Billing`](#billing-setting), [`Eula`](#eula-setting) och används tillsammans och du måste ange giltiga värden för alla tre. Annars startar inte behållaren. Mer information om hur du använder de här konfigurationsinställningarna för att instansiera en behållare finns i [Fakturering](luis-container-howto.md#billing).

## <a name="apikey-setting"></a>ApiKey-inställning

Inställningen `ApiKey` anger den Azure-resursnyckel som används för att spåra faktureringsinformation för behållaren. Du måste ange ett värde för ApiKey och värdet måste vara en giltig [`Billing`](#billing-setting) nyckel för cognitive services-resursen som angetts för konfigurationsinställningen. _Cognitive Services_

Den här inställningen finns på följande platser:

* Azure-portal: **Resurshantering för Kognitiva tjänster** under **Nycklar**
* LUIS-portal: Sidan **Nycklar och inställningar för slutpunkt.** 

Använd inte startnyckeln eller redigeringsnyckeln. 

## <a name="applicationinsights-setting"></a>Inställning av ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-setting"></a>Inställning för fakturering

Inställningen `Billing` anger slutpunkts-URI för _Cognitive Services-resursen_ på Azure som används för att mäta faktureringsinformation för behållaren. Du måste ange ett värde för den här konfigurationsinställningen och värdet måste vara en giltig slutpunkts-URI för en _Cognitive Services-resurs_ på Azure. Behållaren rapporterar användning ungefär var 10 till 15:e minut.

Den här inställningen finns på följande platser:

* Azure-portal: Översikt över **Kognitiva tjänster,** märkt`Endpoint`
* LUIS-portal: Sidan **Nycklar och slutpunktsinställningar,** som en del av slutpunkts-URI:n.

| Krävs | Namn | Datatyp | Beskrivning |
|----------|------|-----------|-------------|
| Ja      | `Billing` | sträng | Fakturering slutpunkt URI. Mer information om hur du hämtar fakturerings-URI finns i [samla in obligatoriska parametrar](luis-container-howto.md#gathering-required-parameters). Mer information och en fullständig lista över regionala slutpunkter finns i [Anpassade underdomännamn för Cognitive Services](../cognitive-services-custom-subdomains.md). |

## <a name="eula-setting"></a>Eula-inställning

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Flytande inställningar

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Inställningar för HTTP-proxyautentiseringsuppgifter

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Loggningsinställningar
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Montera inställningar

Använd bindningsfästen för att läsa och skriva data till och från behållaren. Du kan ange ett indatafäste eller `--mount` utdatafäste genom att ange alternativet i [dockerkörningskommandot.](https://docs.docker.com/engine/reference/commandline/run/) 

LUIS-behållaren använder inte indata- eller utdatafästen för att lagra tränings- eller servicedata. 

Den exakta syntaxen för värdmonteringsplatsen varierar beroende på värdoperativsystemet. Dessutom kanske [värddatorns](luis-container-howto.md#the-host-computer)monteringsplats inte är tillgänglig på grund av en konflikt mellan behörigheter som används av docker-tjänstkontot och värdfästeplatsbehörigheterna. 

I följande tabell beskrivs de inställningar som stöds.

|Krävs| Namn | Datatyp | Beskrivning |
|-------|------|-----------|-------------|
|Ja| `Input` | String | Målet för ingångsfästet. Standardvärdet är `/input`. Det här är platsen för LUIS-paketfilerna. <br><br>Exempel:<br>`--mount type=bind,src=c:\input,target=/input`|
|Inga| `Output` | String | Målet för utmatningsfästet. Standardvärdet är `/output`. Det här är platsen för loggarna. Detta inkluderar LUIS-frågeloggar och behållarloggar. <br><br>Exempel:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Exempel på dockerkörningskommandon

I följande exempel används konfigurationsinställningarna för `docker run` att illustrera hur du skriver och använder kommandon.  När den körs fortsätter behållaren att köras tills du [stoppar](luis-container-howto.md#stop-the-container) den.

* I de här exemplen används katalogen `C:` utanför enheten för att undvika behörighetskonflikter i Windows. Om du behöver använda en viss katalog som indatakatalog kan du behöva ge dockertjänsten behörighet. 
* Ändra inte ordningen på argumenten om du inte är väl förtrogen med dockerbehållare.
* Om du använder ett annat operativsystem använder du rätt konsol/terminal, mappsyntax för fästen och linjefortsättningstecken för ditt system. De här exemplen förutsätter en `^`Windows-konsol med ett linjefortsättningstecken . Eftersom behållaren är ett Linux-operativsystem använder målfästet en mappsyntax i Linux-stil.

Ersätt {_argument_name_} med dina egna värderingar:

| Platshållare | Värde | Format eller exempel |
|-------------|-------|---|
| **{API_KEY}** | Slutpunktsnyckeln `LUIS` för resursen `LUIS` på sidan Azure Keys. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | Värdet för faktureringsslutpunkt är `LUIS` tillgängligt på sidan Azure Overview.| Se [samla in nödvändiga parametrar](luis-container-howto.md#gathering-required-parameters) för explicita exempel. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> Alternativen `Eula` `Billing`, `ApiKey` och måste anges för att behållaren ska kunna köras. Annars startar inte behållaren. Mer information finns i [Fakturering](luis-container-howto.md#billing).
> ApiKey-värdet är sidan **Nyckel** från nycklar och slutpunkter i LUIS-portalen `Cognitive Services` och är också tillgängligt på sidan Azure-resursnycklar. 

### <a name="basic-example"></a>Grundläggande exempel

I följande exempel finns minst möjliga argument för att köra behållaren:

```console
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis:latest ^
Eula=accept ^
Billing={ENDPOINT_URL} ^
ApiKey={API_KEY}
```

### <a name="applicationinsights-example"></a>Exempel på ApplicationInsights

I följande exempel anges argumentet ApplicationInsights för att skicka telemetri till Application Insights medan behållaren körs:

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

### <a name="logging-example"></a>Exempel på loggning 

Följande kommando anger loggningsnivån `Logging:Console:LogLevel`för att konfigurera [`Information`](https://msdn.microsoft.com)loggningsnivån till . 

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

* Granska [Hur du installerar och kör behållare](luis-container-howto.md)
* Se [Felsökning](troubleshooting.md) för att lösa problem relaterade till LUIS-funktioner.
* Använda fler [Cognitive Services-behållare](../cognitive-services-container-support.md)
