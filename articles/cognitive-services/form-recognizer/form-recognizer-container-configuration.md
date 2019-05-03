---
title: Konfigurera behållaren - formuläret Igenkännande
titleSuffix: Azure Cognitive Services
description: Lär dig hur du använder formuläret Igenkännande behållare för att parsa form-och tabelldata.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: pafarley
ms.openlocfilehash: 6066e7856ddf8ef757afc2072218c87420a37c10
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027182"
---
# <a name="configure-form-recognizer-containers"></a>Konfigurera formuläret Igenkännande behållare

Formuläret Igenkännande behållare kan kunder skapa en programarkitektur som är optimerad för att dra nytta av både robusta funktioner och edge ort.

Den **formuläret Igenkännande** behållare körningsmiljö konfigureras med hjälp av den `docker run` kommandot argument. Den här behållaren har flera inställningar som krävs, tillsammans med några valfria inställningar. Flera [exempel](#example-docker-run-commands) kommandots är tillgängliga. Behållare-specifika inställningar är de fakturering inställningarna.

## <a name="configuration-settings"></a>Konfigurationsinställningar

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> Den [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), och [ `Eula` ](#eula-setting) inställningar används tillsammans, och du måste ange giltiga värden för alla tre av dem, annars din behållare startar inte. Läs mer om att använda dessa konfigurationsinställningar för att skapa en instans av en behållare, [fakturering](form-recognizer-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>ApiKey konfigurationsinställning

Den `ApiKey` inställningen anger du Azure-resurs-nyckeln som används för att spåra faktureringsinformation för behållaren. Du måste ange ett värde för ApiKey och värdet måste vara en giltig nyckel för den _formuläret Igenkännande_ resurs som angetts för den [ `Billing` ](#billing-configuration-setting) konfigurationsinställning.

Den här inställningen kan hittas på följande plats:

* Azure-portalen: **Formuläret Igenkännandes** resurshantering under **nycklar**

## <a name="applicationinsights-setting"></a>Inställningen för ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Fakturering konfigurationsinställning

Den `Billing` inställningen anger URI för den _formuläret Igenkännande_ resurs på Azure som används för att läsa av faktureringsinformation för behållaren. Du måste ange ett värde för den här inställningen och värdet måste vara en giltig URI-slutpunkt för en _formuläret Igenkännande_ resurs på Azure. Behållaren rapporterar användning ungefär var 10 – 15 minuter.

Den här inställningen kan hittas på följande plats:

* Azure-portalen: **Formuläret Igenkännandes** översikt, märkt `Endpoint`

|Krävs| Namn | Datatyp | Beskrivning |
|--|------|-----------|-------------|
|Ja| `Billing` | Sträng | Fakturering endpoint URI<br><br>Exempel:<br>`Billing=https://westus2.api.cognitive.microsoft.com/` |

## <a name="eula-setting"></a>Licensvillkor för inställningen

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd-inställningar

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="logging-settings"></a>Loggningsinställningar

[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]


## <a name="mount-settings"></a>Monteringsinställningar

Använd bindning monterar för att läsa och skriva data till och från behållaren. Du kan ange en monteringspunkt som indata eller utdata mount genom att ange den `--mount` alternativet i den [docker kör](https://docs.docker.com/engine/reference/commandline/run/) kommando.

Formuläret Igenkännande behållare kräver en in- och utdata mount. Inkommande monterings kan vara skrivskyddade och krävs för att komma åt data som ska användas för träning och bedömning. Utdata mount måste vara skrivbar och används för att lagra modeller och tillfälliga data.

Den exakta syntaxen hos montera värdplats varierar beroende på värdens operativsystem. Dessutom kan den [värddatorn](form-recognizer-container-howto.md#the-host-computer)'s montera platsen är kanske inte tillgänglig på grund av en konflikt mellan behörigheter som används av Docker-tjänstkontot och värden montera plats behörigheter.

|Valfri| Namn | Datatyp | Beskrivning |
|-------|------|-----------|-------------|
|Krävs| `Input` | String | Inkommande monterings-mål. Standardvärdet är `/input`.    <br><br>Exempel:<br>`--mount type=bind,src=c:\input,target=/input`|
|Krävs| `Output` | String | Utdata mount-mål. Standardvärdet är `/output`.  <br><br>Exempel:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Exempel docker-kommandon

I följande exempel används konfigurationsinställningarna som illustrerar hur du skriver och använda `docker run` kommandon.  När du kör, behållaren fortsätter att köras tills du [stoppa](form-recognizer-container-howto.md#stop-the-container) den.

* **Fortsättning på raden tecknet**: Docker-kommandon i följande avsnitt använder det omvända snedstrecket `\`, som en fortsättning tecknet. Ersätta eller ta bort detta baserat på din värdoperativsystemet.
* **Argumentet order**: Ändra inte argumentens ordning om du inte är bekant med Docker-behållare.

Ersätt {_argument_name_} med dina egna värden:

| Platshållare | Värde |
|-------------|-------|
|{BILLING_KEY} | Den här nyckeln används för att starta behållaren och är tillgänglig på sidan för Azure-portalens formuläret Igenkännande nycklar.  |
|{BILLING_ENDPOINT_URI} | Fakturering slutpunkten URI-värdet är tillgänglig på Azure portal formuläret Igenkännande översiktssidan.|
|{COMPUTER_VISION_API_KEY}| Nyckeln är tillgänglig på Azure portal datornycklar Vision API-sidan.|
|{COMPUTER_VISION_ENDPOINT_URI}|Fakturering slutpunkten. Om du använder en molnbaserad visuellt resurs är URI-värdet tillgängliga på Azure portal datorn Vision API översiktssidan. Om du använder en `cognitive-services-recognize-text` behållare, Använd fakturering slutpunkts-URL som skickades till behållaren i den `docker run` kommando.|

> [!IMPORTANT]
> Den `Eula`, `Billing`, och `ApiKey` alternativ måste anges för att köra behållaren, i annat fall startar inte behållaren.  Mer information finns i [fakturering](#billing-configuration-setting).
> ApiKey-värdet är den **nyckel** från sidan nycklar för Azure formuläret Igenkännande resurs.

## <a name="form-recognizer-container-docker-examples"></a>Formuläret Igenkännande behållare Docker-exempel

I följande exempel Docker är för formuläret Igenkännande behållaren.

### <a name="basic-example-for-form-recognizer"></a>Grundläggande exempel för formuläret Igenkännande

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

### <a name="logging-example-for-form-recognizer"></a>Loggning exempel för formuläret Igenkännande

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
Logging:Console:LogLevel:Default=Information
```


## <a name="next-steps"></a>Nästa steg

* Granska [hur du installerar och kör behållare](form-recognizer-container-howto.md)
