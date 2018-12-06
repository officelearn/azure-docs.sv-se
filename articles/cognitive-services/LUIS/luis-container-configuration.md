---
title: Inställningar för docker-behållare för Språkförståelse (LUIS)
titleSuffix: Azure Cognitive Services
description: LUIS-behållaren körningsmiljö konfigureras med hjälp av den `docker run` kommandot argument. LUIS har flera inställningar som krävs, tillsammans med några valfria inställningar.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 12/04/2018
ms.author: diberry
ms.openlocfilehash: 020c623f881dd806cbc42b72596a2cc87e29045b
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52965061"
---
# <a name="configure-containers"></a>Konfigurera containrar

Körningsmiljö för Språkförståelse (LUIS) behållare konfigureras med hjälp av den `docker run` kommandot argument. LUIS har flera inställningar som krävs, tillsammans med några valfria inställningar. Flera [exempel](#example-docker-run-commands) kommandots är tillgängliga. Behållare-specifika inställningarna är indata [Monteringsinställningar](#mount-settings) och fakturering inställningarna. 

Behållarinställningar är [hierarkiska](#hierarchical-settings) och kan ställas in med [miljövariabler](#environment-variable-settings) eller docker [kommandoradsargument](#command-line-argument-settings).

## <a name="configuration-settings"></a>Konfigurationsinställningar

Den här behållaren har följande konfigurationsinställningar:

|Krävs|Inställning|Syfte|
|--|--|--|
|Ja|[ApiKey](#apikey-setting)|Används för att spåra faktureringsinformation.|
|Nej|[ApplicationInsights](#applicationinsights-setting)|Du kan lägga till [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) telemetri stöd till behållaren.|
|Ja|[Billing](#billing-setting)|Anger URI för den _Språkförståelse_ resurs på Azure.|
|Ja|[Licensvillkor](#eula-setting)| Anger att du har godkänt licensen för behållaren.|
|Nej|[Fluentd](#fluentd-settings)|Skriva log och eventuellt måttdata till en Fluentd-server.|
|Nej|[Loggning](#logging-settings)|Ger ASP.NET Core loggning stöd för din behållare. |
|Ja|[Monterar](#mount-settings)|Läsa och skriva data från [värddatorn](luis-container-howto.md#the-host-computer) till behållare och från behållare till värddatorn.|

> [!IMPORTANT]
> Den [ `ApiKey` ](#apikey-setting), [ `Billing` ](#billing-setting), och [ `Eula` ](#eula-setting) inställningar används tillsammans, och du måste ange giltiga värden för alla tre av dem, annars din behållare startar inte. Läs mer om att använda dessa konfigurationsinställningar för att skapa en instans av en behållare, [fakturering](luis-container-howto.md#billing).

## <a name="apikey-setting"></a>ApiKey inställningen

Den `ApiKey` inställningen anger du Azure-resurs-nyckeln som används för att spåra faktureringsinformation för behållaren. Du måste ange ett värde för ApiKey och värdet måste vara en giltig nyckel för den _Språkförståelse_ resurs som angetts för den [ `Billing` ](#billing-setting) konfigurationsinställning.

Den här inställningen kan hittas på två platser:

* Azure-portalen: **Language Understanding** resurshantering under **nycklar**
* LUIS-portalen: **nycklar och slutpunkten inställningar** sidan. 

Använd inte starter-nyckel eller nyckeln för redigering. 

## <a name="applicationinsights-setting"></a>Inställningen för ApplicationInsights

Den `ApplicationInsights` inställningen kan du lägga till [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) telemetri stöd till behållaren. Application Insights erbjuder djupgående övervakning av din behållare. Du kan enkelt övervaka din behållare för tillgänglighet, prestanda och användning. Du kan också snabbt identifiera och diagnostisera fel i din behållare.

I följande tabell beskrivs de konfigurationsinställningar som stöds den `ApplicationInsights` avsnittet.

|Krävs| Namn | Datatyp | Beskrivning |
|--|------|-----------|-------------|
|Nej| `InstrumentationKey` | Sträng | Instrumenteringsnyckeln för Application Insights-instans till vilken telemetri skickas data för behållaren. Mer information finns i [Application Insights för ASP.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core). <br><br>Exempel:<br>`InstrumentationKey=123456789`|


## <a name="billing-setting"></a>Inställningen för fakturering

Den `Billing` inställningen anger URI för den _Språkförståelse_ resurs på Azure som används för att läsa av faktureringsinformation för behållaren. Du måste ange ett värde för den här inställningen och värdet måste vara en giltig URI-slutpunkt för en _Språkförståelse_ resurs på Azure.

Den här inställningen kan hittas på två platser:

* Azure-portalen: **Language Understanding** översikt, märkt `Endpoint`
* LUIS-portalen: **nycklar och slutpunkten inställningar** sidan som en del av URI-slutpunkten.

|Krävs| Namn | Datatyp | Beskrivning |
|--|------|-----------|-------------|
|Ja| `Billing` | Sträng | Fakturering endpoint URI<br><br>Exempel:<br>`Billing=https://westus.api.cognitive.microsoft.com/luis/v2.0` |

## <a name="eula-setting"></a>Licensvillkor för inställningen

Den `Eula` inställningen visar att du har godkänt licensen för behållaren. Du måste ange ett värde för den här inställningen och värdet måste anges till `accept`.

|Krävs| Namn | Datatyp | Beskrivning |
|--|------|-----------|-------------|
|Ja| `Eula` | Sträng | Godkännande av licensen<br><br>Exempel:<br>`Eula=accept` |

Cognitive Services-behållare är licensierad under ditt avtal som reglerar användningen av Azure. Om du inte har en befintlig avtal som reglerar användningen av Azure, accepterar du att ditt avtal som reglerar användningen av Azure är Microsoft Online-prenumerationsavtalet (som införlivar villkoren för Online Services). För förhandsversioner också samtycker du till den kompletterande användningsvillkor för förhandsversioner av Microsoft Azure. Med hjälp av behållaren godkänner du dessa villkor.

## <a name="fluentd-settings"></a>Fluentd-inställningar

Fluentd är en öppen källkod för datainsamling för enhetlig loggning. Den `Fluentd` inställningar hantera behållarens anslutning till en [Fluentd](https://www.fluentd.org) server. LUIS-behållaren innehåller en Fluentd loggningsprovider, vilket gör att din behållare att skriva loggar och eventuellt måttdata till en Fluentd-server.

I följande tabell beskrivs de konfigurationsinställningar som stöds den `Fluentd` avsnittet.

| Namn | Datatyp | Beskrivning |
|------|-----------|-------------|
| `Host` | Sträng | IP-adressen eller DNS-värdnamn för Fluentd-servern. |
| `Port` | Integer | Porten som används av Fluentd-server.<br/> Standardvärdet är 24224. |
| `HeartbeatMs` | Integer | Pulsslag intervall i millisekunder. Om ingen händelse trafik har skickats innan det här intervallet upphör att gälla, skickas ett pulsslag till Fluentd-servern. Standardvärdet är 60000 millisekunder (1 minut). |
| `SendBufferSize` | Integer | Buffertutrymme för nätverk i byte som allokerats för skickar åtgärder. Standardvärdet är 32768 byte (32 kB). |
| `TlsConnectionEstablishmentTimeoutMs` | Integer | Tidsgräns i millisekunder för att upprätta en SSL/TLS-anslutning med Fluentd-servern. Standardvärdet är 10 000 millisekunder (10 sekunder).<br/> Om `UseTLS` har angetts till false, ignoreras det här värdet. |
| `UseTLS` | Boolesk | Anger om behållaren ska använda SSL/TLS för att kommunicera med Fluentd-servern. Standardvärdet är FALSKT. |

## <a name="logging-settings"></a>Loggningsinställningar

Den `Logging` inställningar hantera ASP.NET Core loggningsstöd för din behållare. Du kan använda samma konfigurationsinställningar och värden för din behållare som du använder för ett ASP.NET Core-program. 

Följande loggning providers som stöds av LUIS-behållaren:

|Leverantör|Syfte|
|--|--|
|[Konsol](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#console-provider)|ASP.NET Core `Console` loggningsprovider. Alla konfigurationsinställningar för ASP.NET Core och standardvärden för den här loggningsprovider stöds.|
|[Felsökning](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#debug-provider)|ASP.NET Core `Debug` loggningsprovider. Alla konfigurationsinställningar för ASP.NET Core och standardvärden för den här loggningsprovider stöds.|
|[Disk](#disk-logging)|Loggningsprovider JSON. Den här loggningsprovider skriver loggdata till utdata mount.|

### <a name="disk-logging"></a>Disk-loggning
  
Den `Disk` loggningsprovider stöder följande konfigurationsinställningar:  

| Namn | Datatyp | Beskrivning |
|------|-----------|-------------|
| `Format` | Sträng | Utdataformat för loggfiler.<br/> **Obs:** detta värde måste anges till `json` att aktivera loggning-providern. Ett fel inträffar om det här värdet anges utan att också ange en utdata mount vid instansiering en behållare. |
| `MaxFileSize` | Integer | Den maximala storleken i megabyte (MB) på en loggfil. När storleken på den aktuella loggfilen uppfyller eller överskrider detta värde, har en ny loggfil startats med loggningsprovider. Om -1 anges begränsas storleken på loggfilen bara av den maximala filstorleken för utdata mount. Standardvärdet är 1. |

Läs mer om hur du konfigurerar ASP.NET Core loggningsstöd [inställningar filkonfiguration](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#settings-file-configuration).

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

Inställningarna för behållaren LUIS är hierarkiska, och alla behållare på den [värddatorn](luis-container-howto.md#the-host-computer) använder en delad hierarki.

Du kan använda något av följande för att ange inställningar:

* [Miljövariabler](#environment-variable-settings)
* [Kommandoradsargument](#command-line-argument-settings)

Värden för miljövariabler åsidosätta värden för kommandoradsargument, vilket i sin tur åsidosätter standardvärdena för behållaravbildningen. Om du anger olika värden i en miljövariabel och ett argument på kommandoraden för samma Konfigurationsinställningen, används värdet i miljövariabeln av behållaren för skapade instanser.

|Prioritet|Ange plats|
|--|--|
|1|Miljövariabel| 
|2|Kommandorad|
|3|Standardvärdet för behållaren bild|

### <a name="environment-variable-settings"></a>Miljövariabelinställningar

Fördelarna med att använda miljövariabler är:

* Flera inställningar kan konfigureras.
* Flera behållare kan använda samma inställningar.

### <a name="command-line-argument-settings"></a>Kommandoradsargumentet inställningar

Fördelen med att använda kommandoradsargument är att varje behållare kan använda olika inställningar.

## <a name="example-docker-run-commands"></a>Exempel docker-kommandon

I följande exempel används konfigurationsinställningarna som illustrerar hur du skriver och använda `docker run` kommandon.  När du kör, behållaren fortsätter att köras tills du [stoppa](luis-container-howto.md#stop-the-container) den.


* **Fortsättning på raden tecknet**: docker-kommandon i följande avsnitt använder det omvända snedstrecket `\`, som en fortsättning tecknet. Ersätta eller ta bort detta baserat på din värdoperativsystemet. 
* **Argumentet order**: inte ändra ordningen på argumenten om du inte är bekant med docker-behållare.

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