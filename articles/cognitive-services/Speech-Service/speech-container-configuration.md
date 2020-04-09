---
title: Konfigurera talbehållare
titleSuffix: Azure Cognitive Services
description: Taltjänsten ger varje behållare ett gemensamt konfigurationsramverk, så att du enkelt kan konfigurera och hantera lagring, loggning och telemetri samt säkerhetsinställningar för dina behållare.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 5c74aa48b18661236eb55278d1e5a05215b2432c
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877583"
---
# <a name="configure-speech-service-containers"></a>Konfigurera behållare för taltjänst

Talbehållare gör det möjligt för kunder att skapa en talprogramarkitektur som är optimerad för att dra nytta av både robusta molnfunktioner och kantlokal. De fyra talbehållare som vi stöder nu är, **tal-till-text,** **anpassad-tal-till-text,** **text-till-tal**och **anpassad text-till-tal**.

Körningsmiljön **talbehållare** är konfigurerad `docker run` med kommandoargumenten. Den här behållaren har flera nödvändiga inställningar, tillsammans med några valfria inställningar. Flera [exempel på](#example-docker-run-commands) kommandot är tillgängliga. De behållarspecifika inställningarna är faktureringsinställningarna.

## <a name="configuration-settings"></a>Konfigurationsinställningar

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [`ApiKey`](#apikey-configuration-setting)Inställningarna [`Billing`](#billing-configuration-setting), [`Eula`](#eula-setting) och används tillsammans och du måste ange giltiga värden för alla tre. Annars startar inte behållaren. Mer information om hur du använder de här konfigurationsinställningarna för att instansiera en behållare finns i [Fakturering](speech-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>Konfigurationsinställning för ApiKey

Inställningen `ApiKey` anger den Azure-resursnyckel som används för att spåra faktureringsinformation för behållaren. Du måste ange ett värde för ApiKey och värdet måste vara en [`Billing`](#billing-configuration-setting) giltig nyckel för den _talresurs_ som angetts för konfigurationsinställningen.

Den här inställningen finns på följande plats:

- Azure-portal: **Tals** resurshantering, under **Nycklar**

## <a name="applicationinsights-setting"></a>Inställning av ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Konfigurationsinställning för fakturering

Inställningen `Billing` anger slutpunkts-URI för _talresursen_ på Azure som används för att mäta faktureringsinformation för behållaren. Du måste ange ett värde för den här konfigurationsinställningen och värdet måste vara en giltig slutpunkts-URI för en _talresurs_ på Azure. Behållaren rapporterar användning ungefär var 10 till 15:e minut.

Den här inställningen finns på följande plats:

- Azure-portal: Översikt över **tal,** märkt`Endpoint`

| Krävs | Namn | Datatyp | Beskrivning |
| -------- | ---- | --------- | ----------- |
| Ja | `Billing` | Sträng | Fakturering slutpunkt URI. Mer information om hur du hämtar fakturerings-URI finns i [samla in obligatoriska parametrar](speech-container-howto.md#gathering-required-parameters). Mer information och en fullständig lista över regionala slutpunkter finns i [Anpassade underdomännamn för Cognitive Services](../cognitive-services-custom-subdomains.md). |

## <a name="eula-setting"></a>Eula-inställning

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Flytande inställningar

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Inställningar för HTTP-proxyautentiseringsuppgifter

[!INCLUDE [Container shared HTTP proxy settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Loggningsinställningar

[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Montera inställningar

Använd bindningsfästen för att läsa och skriva data till och från behållaren. Du kan ange ett indatafäste eller `--mount` utdatafäste genom att ange alternativet i [dockerkörningskommandot.](https://docs.docker.com/engine/reference/commandline/run/)

Standardtalbehållarna använder inte indata- eller utdatafästen för att lagra tränings- eller servicedata. Anpassade talbehållare är dock beroende av volymfästen.

Den exakta syntaxen för värdmonteringsplatsen varierar beroende på värdoperativsystemet. Dessutom kanske [värddatorns](speech-container-howto.md#the-host-computer)monteringsplats inte är tillgänglig på grund av en konflikt mellan behörigheter som används av docker-tjänstkontot och värdfästeplatsbehörigheterna.

| Valfri | Namn | Datatyp | Beskrivning |
| -------- | ---- | --------- | ----------- |
| Inte tillåten | `Input` | Sträng | Standardbehållare använder inte detta. Anpassade talbehållare använder [volymfästen](#volume-mount-settings).                                                                                    |
| Valfri | `Output` | Sträng | Målet för utmatningsfästet. Standardvärdet är `/output`. Det här är platsen för loggarna. Detta inkluderar behållarloggar. <br><br>Exempel:<br>`--mount type=bind,src=c:\output,target=/output` |

## <a name="volume-mount-settings"></a>Inställningar för volymmontering

De anpassade talbehållarna använder [volymfästen](https://docs.docker.com/storage/volumes/) för att bevara anpassade modeller. Du kan ange en volymmontering genom att lägga till `-v` alternativet (eller `--volume`) i [dockerkörningskommandot.](https://docs.docker.com/engine/reference/commandline/run/)

Anpassade modeller hämtas första gången som en ny modell förtärs som en del av kommandot för anpassad tahållerdockningskörning. Sekventiella körningar av `ModelId` samma för en anpassad talbehållare använder den tidigare hämtade modellen. Om volymfästet inte tillhandahålls kan anpassade modeller inte sparas.

Inställningen för volymmontering består `:` av tre färgavgränsade fält:

1. Det första fältet är namnet på volymen på värddatorn, till exempel _C:\input_.
2. Det andra fältet är katalogen i behållaren, till exempel _/usr/local/models_.
3. Det tredje fältet (valfritt) är en kommaavgränsad lista med alternativ, för mer information se [använda volymer](https://docs.docker.com/storage/volumes/).

### <a name="volume-mount-example"></a>Exempel på volymmontering

```bash
-v C:\input:/usr/local/models
```

Det här kommandot monterar värddatorn _C:\indatakatalogen_ till katalogen behållare _/usr/local/models._

> [!IMPORTANT]
> Inställningarna för volymmontering gäller endast för **anpassade tal-till-text-** och **anpassade text-till-tal-behållare.** Standardbehållare **för tal-till-text** och **text-till-tal** använder inte volymfästen.

## <a name="example-docker-run-commands"></a>Exempel på dockerkörningskommandon

I följande exempel används konfigurationsinställningarna för `docker run` att illustrera hur du skriver och använder kommandon. När den körs fortsätter behållaren att köras tills du [stoppar](speech-container-howto.md#stop-the-container) den.

- **Rad-fortsättning**tecken : Docker kommandon i följande avsnitt använder `\`tillbaka snedstreck, , som en rad fortsättning tecken. Ersätt eller ta bort detta baserat på värdoperativsystemets krav.
- **Argumentordning**: Ändra inte ordningen på argumenten om du inte är bekant med Docker-behållare.

Ersätt {_argument_name_} med dina egna värderingar:

| Platshållare | Värde | Format eller exempel |
| ----------- | ----- | ----------------- |
| **{API_KEY}** | Slutpunktsnyckeln `Speech` för resursen `Speech` på sidan Azure Keys.   | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`                                                                                  |
| **{ENDPOINT_URI}** | Värdet för faktureringsslutpunkt är `Speech` tillgängligt på sidan Azure Overview. | Se [samla in nödvändiga parametrar](speech-container-howto.md#gathering-required-parameters) för explicita exempel. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> Alternativen `Eula` `Billing`, `ApiKey` och måste anges för att behållaren ska kunna köras. Annars startar inte behållaren. Mer information finns i [Fakturering](#billing-configuration-setting).
> ApiKey-värdet är **nyckeln** från sidan Azure Speech Resource keys.

## <a name="speech-container-docker-examples"></a>Exempel på docker i talbehållare

Följande Docker-exempel är för behållaren Tal.

## <a name="speech-to-text"></a>[Tal till text](#tab/stt)

### <a name="basic-example-for-speech-to-text"></a>Grundläggande exempel för tal-till-text

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-speech-to-text"></a>Loggningsexempel för tal till text

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="custom-speech-to-text"></a>[Anpassad tal-till-text](#tab/cstt)

### <a name="basic-example-for-custom-speech-to-text"></a>Grundläggande exempel för Anpassad tal-till-text

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-custom-speech-to-text"></a>Loggningsexempel för anpassad tal-till-text

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="text-to-speech"></a>[Text till tal](#tab/tss)

### <a name="basic-example-for-text-to-speech"></a>Grundläggande exempel för Text-till-tal

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-text-to-speech"></a>Loggningsexempel för text-till-tal

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="custom-text-to-speech"></a>[Anpassad text till tal](#tab/ctts)

### <a name="basic-example-for-custom-text-to-speech"></a>Grundläggande exempel för Anpassad text till tal

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-custom-text-to-speech"></a>Loggningsexempel för anpassad text till tal

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

---

## <a name="next-steps"></a>Nästa steg

- Granska [Hur du installerar och kör behållare](speech-container-howto.md)
