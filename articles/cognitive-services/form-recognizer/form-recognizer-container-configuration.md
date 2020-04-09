---
title: Konfigurera en behållare för formulärkoneformare
titleSuffix: Azure Cognitive Services
description: Lär dig hur du konfigurerar behållaren Formulärconformering för att tolka formulär- och tabelldata.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: bc48c0ba23e73adec312adfeeb1fcd57dba6ceec
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879164"
---
# <a name="configure-form-recognizer-containers"></a>Konfigurera behållare för formulärkonekänningsbehållare

Genom att använda Azure Form Recognizer-behållare kan du skapa en programarkitektur som är optimerad för att dra nytta av både robusta molnfunktioner och kantlokalitet.

Du konfigurerar körningsmiljön för formulärreformbehållaren med hjälp av kommandoargumenten. `docker run` Den här behållaren har flera nödvändiga inställningar och några valfria inställningar. Några exempel finns i avsnittet ["Exempel på docker-körkommandon".](#example-docker-run-commands) De behållarspecifika inställningarna är faktureringsinställningarna.

> [!IMPORTANT]
> Formulärreformerarbehållarna använder för närvarande version 1.0 av API:et för formulärre recognizeer. Du kan komma åt den senaste versionen av API:et med hjälp av den hanterade tjänsten i stället.

## <a name="configuration-settings"></a>Konfigurationsinställningar

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [`ApiKey`](#apikey-configuration-setting)Inställningarna [`Billing`](#billing-configuration-setting), [`Eula`](#eula-setting) och används tillsammans. Du måste ange giltiga värden för alla tre inställningarna. Annars startar inte behållaren. Mer information om hur du använder de här konfigurationsinställningarna för att instansiera en behållare finns i [Fakturering](form-recognizer-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>Konfigurationsinställning för ApiKey

Inställningen `ApiKey` anger den Azure-resursnyckel som används för att spåra faktureringsinformation för behållaren. Värdet för ApiKey måste vara en giltig nyckel för den _formulärkonformeringsresurs_ som anges i `Billing` avsnittet "Faktureringskonfiguration".

Du hittar den här inställningen i Azure-portalen i **Form Recognizer Resource Management**under **Nycklar**.

## <a name="applicationinsights-setting"></a>Inställning av ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Konfigurationsinställning för fakturering

Inställningen `Billing` anger slutpunkts-URI för _formulärkonformeringens_ resurs på Azure som används för att mäta faktureringsinformation för behållaren. Värdet för den här konfigurationsinställningen måste vara en giltig slutpunkts-URI för en _formulärkonformeringsresurs_ på Azure. Behållaren rapporterar användning ungefär var 10 till 15:e minut.

Du hittar den här inställningen i Azure-portalen i **Översikt över formulärrecenser**under **Slutpunkt**.

|Krävs| Namn | Datatyp | Beskrivning |
|--|------|-----------|-------------|
|Ja| `Billing` | Sträng | Fakturering slutpunkt URI. Mer information om hur du hämtar fakturerings-URI finns i [samla in obligatoriska parametrar](form-recognizer-container-howto.md#gathering-required-parameters). Mer information och en fullständig lista över regionala slutpunkter finns i [Anpassade underdomännamn för Cognitive Services](../cognitive-services-custom-subdomains.md). |

## <a name="eula-setting"></a>Eula-inställning

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Flytande inställningar

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Inställningar för HTTP-proxyautentiseringsuppgifter

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Loggningsinställningar

[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]


## <a name="mount-settings"></a>Montera inställningar

Använd bindningsfästen för att läsa och skriva data till och från behållaren. Du kan ange ett indatafäste eller `--mount` ett utdatafäste genom att ange alternativet i [ `docker run` kommandot](https://docs.docker.com/engine/reference/commandline/run/).

Behållaren För formulärkonkänningsbehållare kräver ett indatafäste och ett utdatafäste. Indatafästet kan vara skrivskyddat och det krävs för åtkomst till de data som används för träning och poängsättning. Utdatafästet måste vara skrivbart och du använder det för att lagra modeller och tillfälliga data.

Den exakta syntaxen för värdmonteringsplatsen varierar beroende på värdoperativsystemet. Dessutom kanske [värddatorns](form-recognizer-container-howto.md#the-host-computer) monteringsplats inte är tillgänglig på grund av en konflikt mellan behörigheterna för Docker-tjänstkontot och värdmonteringsplatsbehörigheterna.

|Valfri| Namn | Datatyp | Beskrivning |
|-------|------|-----------|-------------|
|Krävs| `Input` | Sträng | Målet för ingångsfästet. Standardvärdet är `/input`.    <br><br>Exempel:<br>`--mount type=bind,src=c:\input,target=/input`|
|Krävs| `Output` | Sträng | Målet för utmatningsfästet. Standardvärdet är `/output`.  <br><br>Exempel:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Exempel på dockerkörningskommandon

I följande exempel används konfigurationsinställningarna för `docker run` att illustrera hur du skriver och använder kommandon. När den körs fortsätter behållaren att köras tills du [stoppar den.](form-recognizer-container-howto.md#stop-the-container)

* **Rad-fortsättningstecken**: Docker-kommandona i följande avsnitt\\använder ett tillbakasnedstreck ( ) som ett radfortsättningstecken. Ersätt eller ta bort det här tecknet, beroende på värdoperativsystemets krav.
* **Argumentordning**: Ändra inte ordningen på argumenten om du inte är bekant med Docker-behållare.

Ersätt {_argument_name_} i följande tabell med dina egna värden:

| Platshållare | Värde |
|-------------|-------|
| **{FORM_RECOGNIZER_API_KEY}** | Nyckeln som används för att starta behållaren. Den är tillgänglig på sidan Azure portal Form Recognizer Keys. |
| **{FORM_RECOGNIZER_ENDPOINT_URI}** | URI-värdet för faktureringsslutpunkt är tillgängligt på översiktssidan för Azure Portal Form Recognizer.|
| **{COMPUTER_VISION_API_KEY}** | Nyckeln är tillgänglig på sidan Azure portal Computer Vision API Keys.|
| **{COMPUTER_VISION_ENDPOINT_URI}** | Slutpunkten för fakturering. Om du använder en molnbaserad datorseenderesurs är URI-värdet tillgängligt på sidan Azure portal Computer Vision API Overview. Om du använder en behållare *för kognitiva tjänster-igen-text* använder du url:en för `docker run` faktureringsslutpunkt som skickas till behållaren i kommandot. |

Se [samla in nödvändiga parametrar](form-recognizer-container-howto.md#gathering-required-parameters) för information om hur du hämtar dessa värden.

[!INCLUDE [cognitive-services-custom-subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> Om du vill köra `Eula` `Billing`behållaren `ApiKey` anger du alternativen och alternativen . Annars startar inte behållaren. Mer information finns i [Fakturering](#billing-configuration-setting).

## <a name="form-recognizer-container-docker-examples"></a>Exempel på formulärconformatorbehållare Docker

Följande Docker-exempel är för behållaren Formulärre recognizeer.

### <a name="basic-example-for-form-recognizer"></a>Grundläggande exempel för Formulärdekänning

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

### <a name="logging-example-for-form-recognizer"></a>Loggningsexempel för formulärdekänning

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
Logging:Console:LogLevel:Default=Information
```

## <a name="next-steps"></a>Nästa steg

* Granska [Installera och kör behållare](form-recognizer-container-howto.md).
