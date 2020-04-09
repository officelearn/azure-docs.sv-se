---
title: Konfigurera en behållare för API för avvikelsedetektor
titleSuffix: Azure Cognitive Services
description: API-behållarens körningsmiljö för avvikelsedetektorn `docker run` är konfigurerad med hjälp av kommandoargumenten. Den här behållaren har flera nödvändiga inställningar, tillsammans med några valfria inställningar.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 569499002c5e047d7030575342790e9a074b9404
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875188"
---
# <a name="configure-anomaly-detector-containers"></a>Konfigurera avvikelseidentifieringscontainrar

Behållarens körningsmiljö **för avvikelsedetektor** är `docker run` konfigurerad med hjälp av kommandoargumenten. Den här behållaren har flera nödvändiga inställningar, tillsammans med några valfria inställningar. Flera [exempel på](#example-docker-run-commands) kommandot är tillgängliga. De behållarspecifika inställningarna är faktureringsinställningarna. 

## <a name="configuration-settings"></a>Konfigurationsinställningar

Den här behållaren har följande konfigurationsinställningar:

|Krävs|Inställning|Syfte|
|--|--|--|
|Ja|[ApiKey (ApiKey)](#apikey-configuration-setting)|Används för att spåra faktureringsinformation.|
|Inga|[ApplicationInsights](#applicationinsights-setting)|Gör att du kan lägga till [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) telemetri stöd till din behållare.|
|Ja|[Fakturering](#billing-configuration-setting)|Anger slutpunkts-URI för tjänstresursen på Azure.|
|Ja|[Eula](#eula-setting)| Anger att du har accepterat licensen för behållaren.|
|Inga|[Flytande](#fluentd-settings)|Skriv logg och, eventuellt, måttdata till en Fluentd-server.|
|Inga|[Http Proxy](#http-proxy-credentials-settings)|Konfigurera en HTTP-proxy för att göra utgående begäranden.|
|Inga|[Loggning](#logging-settings)|Innehåller ASP.NET Core-loggningsstöd för din behållare. |
|Inga|[Fästen](#mount-settings)|Läsa och skriva data från värddator till behållare och från behållare tillbaka till värddatorn.|

> [!IMPORTANT]
> [`ApiKey`](#apikey-configuration-setting)Inställningarna [`Billing`](#billing-configuration-setting), [`Eula`](#eula-setting) och används tillsammans och du måste ange giltiga värden för alla tre. Annars startar inte behållaren. Mer information om hur du använder de här konfigurationsinställningarna för att instansiera en behållare finns i [Fakturering](anomaly-detector-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>Konfigurationsinställning för ApiKey

Inställningen `ApiKey` anger den Azure-resursnyckel som används för att spåra faktureringsinformation för behållaren. Du måste ange ett värde för ApiKey och värdet måste vara en giltig [`Billing`](#billing-configuration-setting) nyckel för den _avvikelsedetektorresurs_ som angetts för konfigurationsinställningen.

Den här inställningen finns på följande plats:

* Azure-portal: **Avvikelsedetektorns** resurshantering under **Nycklar**

## <a name="applicationinsights-setting"></a>Inställning av ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Konfigurationsinställning för fakturering

Inställningen `Billing` anger slutpunkts-URI för _avvikelsedetektorresursen_ på Azure som används för att mäta faktureringsinformation för behållaren. Du måste ange ett värde för den här konfigurationsinställningen och värdet måste vara en giltig slutpunkts-URI för en _avvikelsedetektorresurs_ på Azure.

Den här inställningen finns på följande plats:

* Azure-portal: **Översikt över avvikelsedetektorn,** märkt`Endpoint`

|Krävs| Namn | Datatyp | Beskrivning |
|--|------|-----------|-------------|
|Ja| `Billing` | Sträng | Fakturering slutpunkt URI. Mer information om hur du hämtar fakturerings-URI finns i [samla in obligatoriska parametrar](anomaly-detector-container-howto.md#gathering-required-parameters). Mer information och en fullständig lista över regionala slutpunkter finns i [Anpassade underdomännamn för Cognitive Services](../cognitive-services-custom-subdomains.md). |

## <a name="eula-setting"></a>Eula-inställning

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Flytande inställningar

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Inställningar för http-proxyautentiseringsuppgifter

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Loggningsinställningar
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]


## <a name="mount-settings"></a>Montera inställningar

Använd bindningsfästen för att läsa och skriva data till och från behållaren. Du kan ange ett indatafäste eller `--mount` utdatafäste genom att ange alternativet i [dockerkörningskommandot.](https://docs.docker.com/engine/reference/commandline/run/)

Behållaren för avvikelsedetektor använder inte indata- eller utdatafästen för att lagra tränings- eller servicedata. 

Den exakta syntaxen för värdmonteringsplatsen varierar beroende på värdoperativsystemet. Dessutom kanske [värddatorns](anomaly-detector-container-howto.md#the-host-computer)monteringsplats inte är tillgänglig på grund av en konflikt mellan behörigheter som används av Docker-tjänstkontot och värdfästeplatsbehörigheterna. 

|Valfri| Namn | Datatyp | Beskrivning |
|-------|------|-----------|-------------|
|Inte tillåten| `Input` | Sträng | Behållare för avvikelsedetektorer använder inte detta.|
|Valfri| `Output` | Sträng | Målet för utmatningsfästet. Standardvärdet är `/output`. Det här är platsen för loggarna. Detta inkluderar behållarloggar. <br><br>Exempel:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Exempel på dockerkörningskommandon 

I följande exempel används konfigurationsinställningarna för `docker run` att illustrera hur du skriver och använder kommandon.  När den körs fortsätter behållaren att köras tills du [stoppar](anomaly-detector-container-howto.md#stop-the-container) den.

* **Rad-fortsättning**tecken : Docker kommandon i följande avsnitt använder `\`tillbaka snedstreck, , som en rad fortsättning tecken för en bash skal. Ersätt eller ta bort detta baserat på värdoperativsystemets krav. Till exempel är linjefortsättningstecknet för `^`fönster en caret, . Byt ut det bakre snedstrecket mot careten. 
* **Argumentordning**: Ändra inte ordningen på argumenten om du inte är väl bekant med Docker-behållare.

Ersätt värdet inom `{}`parentes, med dina egna värden:

| Platshållare | Värde | Format eller exempel |
|-------------|-------|---|
| **{API_KEY}** | Slutpunktsnyckeln `Anomaly Detector` för resursen `Anomaly Detector` på sidan Azure Keys. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | Värdet för faktureringsslutpunkt är `Anomaly Detector` tillgängligt på sidan Azure Overview.| Se [samla in nödvändiga parametrar](anomaly-detector-container-howto.md#gathering-required-parameters) för explicita exempel. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> Alternativen `Eula` `Billing`, `ApiKey` och måste anges för att behållaren ska kunna köras. Annars startar inte behållaren.  Mer information finns i [Fakturering](anomaly-detector-container-howto.md#billing).
> ApiKey-värdet är **nyckeln** från sidan Azure Anomaly Detector Resource keys. 

## <a name="anomaly-detector-container-docker-examples"></a>Exempel på behållaredockningsdockor för avvikelsedetektor

Följande Docker-exempel är för behållaren för avvikelsedetektor. 

### <a name="basic-example"></a>Grundläggande exempel 

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example-with-command-line-arguments"></a>Loggningsexempel med kommandoradsargument

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector \
  Eula=accept \
  Billing={ENDPOINT_URI} ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>Nästa steg

* [Distribuera en avvikelsedetektorbehållare till Azure Container-instanser](how-to/deploy-anomaly-detection-on-container-instances.md)
* [Läs mer om API-tjänsten för avvikelsedetektorn](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
