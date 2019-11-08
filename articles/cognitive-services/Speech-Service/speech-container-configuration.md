---
title: Konfigurera tal behållare
titleSuffix: Azure Cognitive Services
description: Tal tjänster tillhandahåller varje behållare med ett gemensamt konfigurations ramverk, så att du enkelt kan konfigurera och hantera lagring, loggning och telemetri och säkerhets inställningar för dina behållare.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/07/2019
ms.author: dapine
ms.openlocfilehash: 4e09a476398134d92b4492c68ed4ebebc468f272
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2019
ms.locfileid: "73796185"
---
# <a name="configure-speech-service-containers"></a>Konfigurera tal tjänst behållare

Tal behållare gör det möjligt för kunder att bygga en tal program arkitektur som är optimerad för att dra nytta av både robusta moln funktioner och Edge-plats. De fyra tal behållarna vi stöder nu, **tal-till-text**, **anpassat tal till text**, **text till tal**och **anpassad text till tal**.

**Tal** behållarens körnings miljö konfigureras med hjälp av kommando argumenten `docker run`. Den här behållaren har flera inställningar som krävs, tillsammans med några valfria inställningar. Det finns flera [exempel](#example-docker-run-commands) på kommandot. De behållar-/regionsspecifika inställningarna är fakturerings inställningarna. 

## <a name="configuration-settings"></a>Konfigurationsinställningar

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> Inställningarna [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting)och [`Eula`](#eula-setting) används tillsammans och du måste ange giltiga värden för alla tre. annars startar inte behållaren. Mer information om hur du använder dessa konfigurations inställningar för att instansiera en behållare finns i [fakturering](speech-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>Konfigurations inställning för ApiKey

Inställningen `ApiKey` anger den Azure-resurs nyckel som används för att spåra fakturerings information för behållaren. Du måste ange ett värde för ApiKey och värdet måste vara en giltig nyckel för den _tal_ resurs som angetts för [`Billing`](#billing-configuration-setting) konfigurations inställningen.

Du hittar den här inställningen på följande plats:

* Azure Portal: **tal** resurs hantering under **nycklar**

## <a name="applicationinsights-setting"></a>ApplicationInsights-inställning

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Konfigurations inställning för fakturering

Inställningen `Billing` anger slut punkts-URI för _tal_ resursen i Azure som används för att mäta fakturerings information för behållaren. Du måste ange ett värde för den här konfigurations inställningen och värdet måste vara en giltig slut punkts-URI för en _tal_ resurs på Azure. Behållar rapporteringen visar var 10 till 15: e minut.

Du hittar den här inställningen på följande plats:

* Azure Portal: **tal** översikt, etiketterade `Endpoint`

|Krävs| Namn | Datatyp | Beskrivning |
|--|------|-----------|-------------|
|Ja| `Billing` | Sträng | URI för fakturerings slut punkt. Mer information om hur du skaffar fakturerings-URI: n finns i [samla in obligatoriska parametrar](speech-container-howto.md#gathering-required-parameters). Mer information och en fullständig lista över regionala slut punkter finns i [anpassade under domän namn för Cognitive Services](../cognitive-services-custom-subdomains.md). |

## <a name="eula-setting"></a>Licens avtals inställning

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluent-inställningar

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Inställningar för autentiseringsuppgifter för HTTP-proxy

[!INCLUDE [Container shared HTTP proxy settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Loggnings inställningar
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Monterings inställningar

Använd bind-monteringar för att läsa och skriva data till och från behållaren. Du kan ange en inmatnings montering eller utmatnings montering genom att ange alternativet `--mount` i kommandot [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) .

Standard tal behållarna använder inte indata eller utdata monteras för att lagra utbildning eller tjänst data. Anpassade tal behållare är dock beroende av volym monteringar.

Den exakta syntaxen för värd monterings platsen varierar beroende på värd operativ systemet. Dessutom kanske [värd datorns](speech-container-howto.md#the-host-computer)monterings plats inte är tillgänglig på grund av en konflikt mellan behörigheter som används av Docker-tjänstkontot och värd monterings platsens behörigheter. 

|Valfri| Namn | Datatyp | Beskrivning |
|-------|------|-----------|-------------|
|Tillåts inte| `Input` | Sträng | Standard tal behållare använder inte detta. Anpassade tal behållare använder [volym monteringar](#volume-mount-settings). |
|Valfri| `Output` | Sträng | Målet för utmatnings monteringen. Standardvärdet är `/output`. Detta är platsen för loggarna. Detta inkluderar behållar loggar. <br><br>Exempel:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="volume-mount-settings"></a>Volym monterings inställningar

De anpassade tal behållarna använder [volym monteringar](https://docs.docker.com/storage/volumes/) för att bevara anpassade modeller. Du kan ange en volym montering genom att lägga till alternativet `-v` (eller `--volume`) i kommandot [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) .

Anpassade modeller hämtas första gången en ny modell matas in som en del av den anpassade tal containerns Docker Kör-kommando. Sekventiella körningar av samma `ModelId` för en anpassad tal behållare kommer att använda den tidigare nedladdade modellen. Om volym montering inte anges kan inte anpassade modeller vara bestående.

Volym monterings inställningen består av tre färg `:` separerade fält:

1. Det första fältet är namnet på volymen på värddatorn, till exempel *C:\input*.
2. Det andra fältet är katalogen i behållaren, till exempel */usr/local/Models*.
3. Det tredje fältet (valfritt) är en kommaavgränsad lista med alternativ. mer information finns i [använda volymer](https://docs.docker.com/storage/volumes/).

### <a name="volume-mount-example"></a>Exempel på volym montering

```bash
-v C:\input:/usr/local/models
```

Det här kommandot monterar värd datorn *C:\input* -katalogen till behållar */usr/local/Models* -katalogen.

> [!IMPORTANT]
> Volym monterings inställningarna gäller endast för **Custom Speech till text** och **anpassade text till tal** -behållare. Standard text-till-tal **-behållare** och **text till tal** -behållare använder inte volym monteringar.

## <a name="example-docker-run-commands"></a>Exempel på Docker-körnings kommandon 

I följande exempel används konfigurations inställningarna för att illustrera hur du skriver och använder `docker run`-kommandon.  När den körs fortsätter behållaren att köras tills du [stoppar](speech-container-howto.md#stop-the-container) den.

* **Rad fortsättnings bokstav**: Docker-kommandona i följande avsnitt använder omvänt snedstreck `\`, som ett fortsättnings streck. Ersätt eller ta bort detta baserat på värd operativ systemets krav. 
* **Argument ordning**: Ändra inte ordningen på argumenten om du inte är bekant med Docker-behållare.

Ersätt {_argument_name_} med dina egna värden:

| Platshållare | Värde | Format eller exempel |
|-------------|-------|---|
| **{API_KEY}** | Slut punkts nyckeln för `Speech` resursen på sidan för Azure `Speech`-nycklar. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | Värdet för fakturerings slut punkten är tillgängligt på översikts sidan för Azure-`Speech`.| Se [samla in obligatoriska parametrar](speech-container-howto.md#gathering-required-parameters) för explicita exempel. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> Alternativen `Eula`, `Billing`och `ApiKey` måste anges för att köra behållaren. annars startar inte behållaren.  Mer information finns i [fakturering](#billing-configuration-setting).
> ApiKey-värdet är **nyckeln** på sidan med resurs nycklar för Azure-tal. 

## <a name="speech-container-docker-examples"></a>Exempel på tal container Docker

Följande Docker-exempel är för tal behållaren. 

# <a name="speech-to-texttabstt"></a>[Tal till text](#tab/stt)

### <a name="basic-example-for-speech-to-text"></a>Basic-exempel för tal till text

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-speech-to-text"></a>Loggnings exempel för tal till text

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

# <a name="custom-speech-to-texttabcstt"></a>[Custom Speech till text](#tab/cstt)

### <a name="basic-example-for-custom-speech-to-text"></a>Basic-exempel för Custom Speech-till-text

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-custom-speech-to-text"></a>Loggnings exempel för Custom Speech till text

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

# <a name="text-to-speechtabtss"></a>[Text till tal](#tab/tss)

### <a name="basic-example-for-text-to-speech"></a>Basic-exempel för text till tal

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-text-to-speech"></a>Exempel på loggning av text till tal

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

# <a name="custom-text-to-speechtabctts"></a>[Anpassad text till tal](#tab/ctts)

### <a name="basic-example-for-custom-text-to-speech"></a>Basic-exempel för anpassad text till tal

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-custom-text-to-speech"></a>Loggnings exempel för anpassad text till tal

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

***

## <a name="next-steps"></a>Nästa steg

* Granska [hur du installerar och kör behållare](speech-container-howto.md)
