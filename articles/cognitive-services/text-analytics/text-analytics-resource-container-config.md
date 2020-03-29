---
title: Konfigurera behållare - Textanalys
titleSuffix: Azure Cognitive Services
description: Text Analytics förser varje behållare med ett gemensamt konfigurationsramverk, så att du enkelt kan konfigurera och hantera lagring, loggning och telemetri samt säkerhetsinställningar för dina behållare.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 11/07/2019
ms.author: dapine
ms.openlocfilehash: 8a39327275dca43ddb6ce0e46a3e3bb51ec4555b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "73795297"
---
# <a name="configure-text-analytics-docker-containers"></a>Konfigurera dockerbehållare för textanalys

Text Analytics förser varje behållare med ett gemensamt konfigurationsramverk, så att du enkelt kan konfigurera och hantera lagring, loggning och telemetri samt säkerhetsinställningar för dina behållare.

## <a name="configuration-settings"></a>Konfigurationsinställningar

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [`ApiKey`](#apikey-configuration-setting)Inställningarna [`Billing`](#billing-configuration-setting), [`Eula`](#eula-setting) och används tillsammans och du måste ange giltiga värden för alla tre. Annars startar inte behållaren. Mer information om hur du använder de här konfigurationsinställningarna för att instansiera en behållare finns i [Fakturering](how-tos/text-analytics-how-to-install-containers.md#billing).

## <a name="apikey-configuration-setting"></a>Konfigurationsinställning för ApiKey

Inställningen `ApiKey` anger den Azure-resursnyckel som används för att spåra faktureringsinformation för behållaren. Du måste ange ett värde för ApiKey och värdet måste vara en giltig [`Billing`](#billing-configuration-setting) nyckel för den _Text Analytics-resurs_ som angetts för konfigurationsinställningen.

Den här inställningen finns på följande plats:

* Azure portal: **Text Analytics** resurshantering, under **Nycklar**

## <a name="applicationinsights-setting"></a>Inställning av ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Konfigurationsinställning för fakturering

Inställningen `Billing` anger slutpunkts-URI för _textanalysresursen_ på Azure som används för att mäta faktureringsinformation för behållaren. Du måste ange ett värde för den här konfigurationsinställningen och värdet måste vara en giltig slutpunkts-URI för en __Text Analytics-resurs_ på Azure. Behållaren rapporterar användning ungefär var 10 till 15:e minut.

Den här inställningen finns på följande plats:

* Översikt över Azure-portalen: Översikt över **textanalys,** märkt`Endpoint`

|Krävs| Namn | Datatyp | Beskrivning |
|--|------|-----------|-------------|
|Ja| `Billing` | String | Fakturering slutpunkt URI. Mer information om hur du hämtar fakturerings-URI finns i [samla in obligatoriska parametrar](how-tos/text-analytics-how-to-install-containers.md#gathering-required-parameters). Mer information och en fullständig lista över regionala slutpunkter finns i [Anpassade underdomännamn för Cognitive Services](../cognitive-services-custom-subdomains.md). |

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

Text Analytics-behållarna använder inte indata- eller utdatafästen för att lagra tränings- eller tjänstdata. 

Den exakta syntaxen för värdmonteringsplatsen varierar beroende på värdoperativsystemet. Dessutom kanske [värddatorns](how-tos/text-analytics-how-to-install-containers.md#the-host-computer)monteringsplats inte är tillgänglig på grund av en konflikt mellan behörigheter som används av docker-tjänstkontot och värdfästeplatsbehörigheterna. 

|Valfri| Namn | Datatyp | Beskrivning |
|-------|------|-----------|-------------|
|Inte tillåten| `Input` | String | Text Analytics-behållare använder inte detta.|
|Valfri| `Output` | String | Målet för utmatningsfästet. Standardvärdet är `/output`. Det här är platsen för loggarna. Detta inkluderar behållarloggar. <br><br>Exempel:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Exempel på dockerkörningskommandon 

I följande exempel används konfigurationsinställningarna för `docker run` att illustrera hur du skriver och använder kommandon.  När den körs fortsätter behållaren att köras tills du [stoppar](how-tos/text-analytics-how-to-install-containers.md#stop-the-container) den.

* **Rad-fortsättning**tecken : Docker kommandon i följande avsnitt använder `\`tillbaka snedstreck, , som en rad fortsättning tecken. Ersätt eller ta bort detta baserat på värdoperativsystemets krav. 
* **Argumentordning**: Ändra inte ordningen på argumenten om du inte är väl förtrogen med dockercontainrar.

Ersätt {_argument_name_} med dina egna värderingar:

| Platshållare | Värde | Format eller exempel |
|-------------|-------|---|
| **{API_KEY}** | Slutpunktsnyckeln `Text Analytics` för resursen `Text Analytics` som är tillgänglig på sidan Azure Keys. |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | Värdet för faktureringsslutpunkt är `Text Analytics` tillgängligt på sidan Azure Overview.| Se [samla in nödvändiga parametrar](how-tos/text-analytics-how-to-install-containers.md#gathering-required-parameters) för explicita exempel. |

> [!IMPORTANT]
> Alternativen `Eula` `Billing`, `ApiKey` och måste anges för att behållaren ska kunna köras. Annars startar inte behållaren.  Mer information finns i [Fakturering](how-tos/text-analytics-how-to-install-containers.md#billing).
> ApiKey-värdet är **nyckeln** från `Text Analytics` sidan Azure Resource keys. 

#### <a name="key-phrase-extraction"></a>[Extrahering av diskussionsämne](#tab/keyphrase)

[!INCLUDE [key-phrase-extraction-docker-examples](includes/key-phrase-extraction-docker-examples.md)]

#### <a name="language-detection"></a>[Språkidentifiering](#tab/language)

[!INCLUDE [language-detection-docker-examples](includes/language-detection-docker-examples.md)]

#### <a name="sentiment-analysis"></a>[Attitydanalys](#tab/sentiment)

[!INCLUDE [sentiment-analysis-docker-examples](includes/sentiment-analysis-docker-examples.md)]

***

## <a name="next-steps"></a>Nästa steg

* Granska [Hur du installerar och kör behållare](how-tos/text-analytics-how-to-install-containers.md)
* Använda fler [Cognitive Services-behållare](../cognitive-services-container-support.md)
