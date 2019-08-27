---
title: Konfigurera behållare – Textanalys
titleSuffix: Azure Cognitive Services
description: Textanalys ger varje behållare med ett gemensamt ramverk för konfiguration, så att du enkelt kan konfigurera och hantera inställningar för lagring, loggning och telemetri och säkerhet för dina behållare.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: dapine
ms.openlocfilehash: f1c42002343de1dd3b3ef6b9c9e35f458db925f4
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051112"
---
# <a name="configure-text-analytics-docker-containers"></a>Konfigurera Textanalys Docker-behållare

Textanalys ger varje behållare med ett gemensamt ramverk för konfiguration, så att du enkelt kan konfigurera och hantera inställningar för lagring, loggning och telemetri och säkerhet för dina behållare.

## <a name="configuration-settings"></a>Konfigurationsinställningar

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> Den [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), och [ `Eula` ](#eula-setting) inställningar används tillsammans, och du måste ange giltiga värden för alla tre av dem, annars din behållare startar inte. Läs mer om att använda dessa konfigurationsinställningar för att skapa en instans av en behållare, [fakturering](how-tos/text-analytics-how-to-install-containers.md#billing).

## <a name="apikey-configuration-setting"></a>ApiKey konfigurationsinställning

Den `ApiKey` inställningen anger du Azure-resurs-nyckeln som används för att spåra faktureringsinformation för behållaren. Du måste ange ett värde för ApiKey och värdet måste vara en giltig nyckel för den _textanalys_ resurs som angetts för [`Billing`](#billing-configuration-setting) konfigurations inställningen.

Du hittar den här inställningen på följande plats:

* Azure-portalen: **Textanalys** resurs hantering under **nycklar**

## <a name="applicationinsights-setting"></a>Inställningen för ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Fakturering konfigurationsinställning

Inställningen anger slut punkts-URI för den textanalys resursen på Azure som används för att mäta fakturerings information för behållaren. `Billing` Du måste ange ett värde för den här konfigurations inställningen och värdet måste vara en giltig slut punkts-URI för en __textanalys_ -resurs på Azure. Behållar rapporteringen visar var 10 till 15: e minut.

Du hittar den här inställningen på följande plats:

* Azure-portalen: **Textanalys** Översikt, märkt`Endpoint`

|Obligatorisk| Namn | Datatyp | Beskrivning |
|--|------|-----------|-------------|
|Ja| `Billing` | Sträng | Den obligatoriska URI för fakturerings slut punkt |

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

Textanalys behållare använder inte indata eller utdata monteras för att lagra utbildning eller tjänst data. 

Den exakta syntaxen hos montera värdplats varierar beroende på värdens operativsystem. Dessutom kan den [värddatorn](how-tos/text-analytics-how-to-install-containers.md#the-host-computer)'s montera platsen är kanske inte tillgänglig på grund av en konflikt mellan behörigheter som används av docker-tjänstkontot och värden montera plats behörigheter. 

|Valfritt| Name | Datatyp | Beskrivning |
|-------|------|-----------|-------------|
|Inte tillåtet| `Input` | Sträng | Textanalys behållare använder inte detta.|
|Valfritt| `Output` | Sträng | Utdata mount-mål. Standardvärdet är `/output`. Det här är platsen för loggarna. Detta inkluderar behållar loggar. <br><br>Exempel:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Exempel docker-kommandon 

I följande exempel används konfigurationsinställningarna som illustrerar hur du skriver och använda `docker run` kommandon.  När du kör, behållaren fortsätter att köras tills du [stoppa](how-tos/text-analytics-how-to-install-containers.md#stop-the-container) den.

* **Rad fortsättnings avstånd**: Docker- `\`kommandona i följande avsnitt använder omvänt snedstreck, som ett fortsättnings streck. Ersätta eller ta bort detta baserat på din värdoperativsystemet. 
* **Argument ordning**: Ändra inte argumentens ordning om du inte är bekant med docker-behållare.

Ersätt {_argument_name_} med dina egna värden:

| Platshållare | Värde | Format eller exempel |
|-------------|-------|---|
| **{API_KEY}** | Slut punkts nyckeln för `Text Analytics` resursen som är tillgänglig på `Text Analytics` sidan med Azure-nycklar. |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | Värdet för fakturerings slut punkten är tillgängligt på sidan `Text Analytics` Azure-översikt.| Se [samla in obligatoriska parametrar](how-tos/text-analytics-how-to-install-containers.md#gathering-required-parameters) för explicita exempel. |

> [!IMPORTANT]
> Den `Eula`, `Billing`, och `ApiKey` alternativ måste anges för att köra behållaren, i annat fall startar inte behållaren.  Mer information finns i [fakturering](how-tos/text-analytics-how-to-install-containers.md#billing).
> ApiKey-värdet är **nyckeln** på sidan med Azures `Text Analytics` resurs nycklar. 

#### <a name="key-phrase-extractiontabkeyphrase"></a>[Extrahering av diskussionsämne](#tab/keyphrase)

[!INCLUDE [key-phrase-extraction-docker-examples](includes/key-phrase-extraction-docker-examples.md)]

#### <a name="language-detectiontablanguage"></a>[Språkidentifiering](#tab/language)

[!INCLUDE [language-detection-docker-examples](includes/language-detection-docker-examples.md)]

#### <a name="sentiment-analysistabsentiment"></a>[Attitydanalys](#tab/sentiment)

[!INCLUDE [sentiment-analysis-docker-examples](includes/sentiment-analysis-docker-examples.md)]

***

## <a name="next-steps"></a>Nästa steg

* Granska [hur du installerar och kör behållare](how-tos/text-analytics-how-to-install-containers.md)
* Använd fler [Cognitive Services behållare](../cognitive-services-container-support.md)
