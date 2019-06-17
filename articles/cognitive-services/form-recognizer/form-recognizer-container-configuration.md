---
title: Konfigurera behållaren - formuläret Igenkännande
titleSuffix: Azure Cognitive Services
description: Lär dig hur du konfigurerar formuläret Igenkännande behållaren för att analysera form-och tabelldata.
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: overview
ms.date: 06/10/2019
ms.author: dapine
ms.openlocfilehash: bdff74be8578bb862974479b3151b0d922f00dd9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67064002"
---
# <a name="configure-form-recognizer-containers"></a>Konfigurera formuläret Igenkännande behållare

Genom att använda Azure formuläret Igenkännande behållare kan skapa du en programarkitektur som är optimerad för att dra nytta av både robusta funktioner och edge ort.

Du konfigurerar formuläret Igenkännande-körningsmiljön för behållaren med hjälp av den `docker run` kommandot argument. Den här behållaren har flera krävs inställningar och några valfria inställningar. Några exempel finns i den [”exempel docker-kommandon”](#example-docker-run-commands) avsnittet. Behållare-specifika inställningar är de fakturering inställningarna.

## <a name="configuration-settings"></a>Konfigurationsinställningar

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> Den [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), och [ `Eula` ](#eula-setting) inställningarna används tillsammans. Du måste ange giltiga värden för alla tre inställningar. Annars startar behållaren inte. Läs mer om att använda dessa konfigurationsinställningar för att skapa en instans av en behållare, [fakturering](form-recognizer-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>ApiKey konfigurationsinställning

Den `ApiKey` inställningen anger du Azure-resurs-nyckel som används för att spåra faktureringsinformation för behållaren. Värdet för ApiKey måste vara en giltig nyckel för den _formuläret Igenkännande_ resurs som har angetts för `Billing` i avsnittet ”fakturering konfigurationsinställning”.

Du hittar den här inställningen i Azure-portalen i **formuläret Igenkännande resurshantering**under **nycklar**.

## <a name="applicationinsights-setting"></a>Inställningen för ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Fakturering konfigurationsinställning

Den `Billing` inställningen anger URI för den _formuläret Igenkännande_ resurs på Azure som används för att läsa av faktureringsinformation för behållaren. Värdet för den här inställningen måste vara en giltig URI-slutpunkt för en _formuläret Igenkännande_ resurs på Azure. Behållaren rapporterar användning ungefär var 10 – 15 minuter.

Du hittar den här inställningen i Azure-portalen i **formuläret Igenkännande översikt**under **Endpoint**.

|Obligatoriskt| Namn | Datatyp | Beskrivning |
|--|------|-----------|-------------|
|Ja| `Billing` | Sträng | Fakturering endpoint URI<br><br>Exempel:<br>`Billing=https://westus2.api.cognitive.microsoft.com/` |

## <a name="eula-setting"></a>Licensvillkor för inställningen

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd-inställningar

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>HTTP-proxyinställningarna för autentiseringsuppgifter

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Loggningsinställningar

[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]


## <a name="mount-settings"></a>Monteringsinställningar

Använd bindning monterar för att läsa och skriva data till och från behållaren. Du kan ange en inkommande montera eller en utdata mount genom att ange den `--mount` alternativet i den [ `docker run` kommandot](https://docs.docker.com/engine/reference/commandline/run/).

Behållaren formuläret Igenkännande kräver en inkommande montera och en utdata mount. Inkommande monterings kan vara skrivskyddade och det krävs för åtkomst till data som används för träning och bedömning. Utdata mount måste vara skrivbar och du kan använda den för att lagra modeller och tillfälliga data.

Den exakta syntaxen hos montera värdplats varierar beroende på värdens operativsystem. Dessutom kan montera platsen för den [värddatorn](form-recognizer-container-howto.md#the-host-computer) kan inte nås på grund av en konflikt mellan behörigheterna för Docker-tjänsten och behörigheterna som värd montera plats.

|Valfri| Namn | Datatyp | Beskrivning |
|-------|------|-----------|-------------|
|Obligatoriskt| `Input` | String | Inkommande monterings-mål. Standardvärdet är `/input`.    <br><br>Exempel:<br>`--mount type=bind,src=c:\input,target=/input`|
|Obligatoriskt| `Output` | String | Utdata mount-mål. Standardvärdet är `/output`.  <br><br>Exempel:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Exempel docker-kommandon

I följande exempel används konfigurationsinställningarna som illustrerar hur du skriver och använda `docker run` kommandon. När den körs behållaren fortsätter att köras tills du [stoppa den](form-recognizer-container-howto.md#stop-the-container).

* **Fortsättning på raden tecknet**: Docker-kommandon i följande avsnitt använder ett omvänt snedstreck (\\) som en fortsättning tecknet. Ersätta eller ta bort det här tecknet, beroende på din värdoperativsystemet krav.
* **Argumentet order**: Inte ändra ordningen på argumenten om du inte är bekant med Docker-behållare.

Ersätt {_argument_name_} i tabellen nedan med dina egna värden:

| Platshållare | Värde |
|-------------|-------|
|{BILLING_KEY} | Den nyckel som används för att starta behållaren. Det är tillgängligt på Azure portal sidan formuläret Igenkännande nycklar.  |
|{BILLING_ENDPOINT_URI} | Fakturering slutpunkten URI-värdet är tillgänglig på Azure portal formuläret Igenkännande översiktssidan.|
|{COMPUTER_VISION_API_KEY}| Nyckeln är tillgänglig på Azure portal datornycklar Vision API-sidan.|
|{COMPUTER_VISION_ENDPOINT_URI}|Fakturering slutpunkten. URI-värdet är tillgängligt på Azure portal översiktssidan för datorn Vision API om du använder en molnbaserad visuellt resurs. Om du använder en *cognitive services-identifiera – fulltext* behållare, använda fakturering slutpunkts-URL som skickades till behållaren i den `docker run` kommando.|

> [!IMPORTANT]
> Kör behållaren genom att ange den `Eula`, `Billing`, och `ApiKey` alternativ; i annat fall startar inte behållaren. Mer information finns i [fakturering](#billing-configuration-setting).

> [!NOTE] 
> ApiKey-värdet är den **nyckel** från sidan nycklar för Azure formuläret Igenkännande resurs.

## <a name="form-recognizer-container-docker-examples"></a>Formuläret Igenkännande behållare Docker-exempel

I följande exempel Docker är för formuläret Igenkännande behållaren.

### <a name="basic-example-for-form-recognizer"></a>Grundläggande exempel för formuläret Igenkännande

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

### <a name="logging-example-for-form-recognizer"></a>Loggning exempel för formuläret Igenkännande

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
Logging:Console:LogLevel:Default=Information
```


## <a name="next-steps"></a>Nästa steg

* Granska [installera och kör behållare](form-recognizer-container-howto.md).
