---
title: Konfigurera behållare – för visuellt innehåll
titlesuffix: Azure Cognitive Services
description: Konfigurera olika inställningar för identifiera Text behållare i visuellt.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: db33ce748928b954f5447a82550c6ecde2188abf
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2019
ms.locfileid: "58877133"
---
# <a name="configure-recognize-text-docker-containers"></a>Konfigurera identifiera Text Docker-behållare

Den **identifiera Text** behållare körningsmiljö konfigureras med hjälp av den `docker run` kommandot argument. Den här behållaren har flera inställningar som krävs, tillsammans med några valfria inställningar. Flera [exempel](#example-docker-run-commands) kommandots är tillgängliga. Behållare-specifika inställningar är de fakturering inställningarna. 

## <a name="configuration-settings"></a>Konfigurationsinställningar

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> Den [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), och [ `Eula` ](#eula-setting) inställningar används tillsammans, och du måste ange giltiga värden för alla tre av dem, annars din behållare startar inte. Läs mer om att använda dessa konfigurationsinställningar för att skapa en instans av en behållare, [fakturering](computer-vision-how-to-install-containers.md).

## <a name="apikey-configuration-setting"></a>ApiKey konfigurationsinställning

Den `ApiKey` inställningen anger du Azure-resurs-nyckeln som används för att spåra faktureringsinformation för behållaren. Du måste ange ett värde för ApiKey och värdet måste vara en giltig nyckel för den _visuellt_ resurs som angetts för den [ `Billing` ](#billing-configuration-setting) konfigurationsinställning.

Den här inställningen kan hittas på följande plats:

* Azure-portalen: **Datorn Vision** resurshantering under **nycklar**

## <a name="applicationinsights-setting"></a>Inställningen för ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Fakturering konfigurationsinställning

Den `Billing` inställningen anger URI för den _visuellt_ resurs på Azure som används för att läsa av faktureringsinformation för behållaren. Du måste ange ett värde för den här inställningen och värdet måste vara en giltig URI-slutpunkt för en _visuellt_ resurs på Azure. Behållaren rapporterar användning ungefär var 10 – 15 minuter.

Den här inställningen kan hittas på följande plats:

* Azure-portalen: **Datorn Vision** översikt, märkt `Endpoint`

|Krävs| Namn | Datatyp | Beskrivning |
|--|------|-----------|-------------|
|Ja| `Billing` | Sträng | Fakturering endpoint URI<br><br>Exempel:<br>`Billing=https://westcentralus.api.cognitive.microsoft.com/vision/v1.0` |

## <a name="eula-setting"></a>Licensvillkor för inställningen

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd-inställningar

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>HTTP-proxyinställningarna för autentiseringsuppgifter

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Loggningsinställningar
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Monteringsinställningar

Använd bindning monterar för att läsa och skriva data till och från behållaren. Du kan ange en monteringspunkt som indata eller utdata mount genom att ange den `--mount` alternativet i den [docker kör](https://docs.docker.com/engine/reference/commandline/run/) kommando.

Visuellt behållare använder inte indata eller utdata monterar för att lagra utbildning eller tjänstdata. 

Den exakta syntaxen hos montera värdplats varierar beroende på värdens operativsystem. Dessutom kan den [värddatorn](computer-vision-how-to-install-containers.md#the-host-computer)'s montera platsen är kanske inte tillgänglig på grund av en konflikt mellan behörigheter som används av Docker-tjänstkontot och värden montera plats behörigheter. 

|Valfri| Namn | Datatyp | Beskrivning |
|-------|------|-----------|-------------|
|Inte tillåtet| `Input` | String | Datorn Vision behållare Använd inte detta.|
|Valfri| `Output` | String | Utdata mount-mål. Standardvärdet är `/output`. Det här är platsen för loggarna. Detta inkluderar behållarloggarna. <br><br>Exempel:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Exempel docker-kommandon 

I följande exempel används konfigurationsinställningarna som illustrerar hur du skriver och använda `docker run` kommandon.  När du kör, behållaren fortsätter att köras tills du [stoppa](computer-vision-how-to-install-containers.md#stop-the-container) den.

* **Fortsättning på raden tecknet**: Docker-kommandon i följande avsnitt använder det omvända snedstrecket `\`, som en fortsättning tecknet. Ersätta eller ta bort detta baserat på din värdoperativsystemet. 
* **Argumentet order**: Ändra inte argumentens ordning om du inte är bekant med Docker-behållare.

Ersätt {_argument_name_} med dina egna värden:

| Platshållare | Värde | Format eller exempel |
|-------------|-------|---|
|{BILLING_KEY} | Slutpunktsnyckeln för visuellt innehåll-resurs. |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{BILLING_ENDPOINT_URI} | Fakturering slutpunktsvärdet inklusive region.|`https://westcentralus.api.cognitive.microsoft.com/vision/v1.0`|

> [!IMPORTANT]
> Den `Eula`, `Billing`, och `ApiKey` alternativ måste anges för att köra behållaren, i annat fall startar inte behållaren.  Mer information finns i [fakturering](computer-vision-how-to-install-containers.md#billing).
> ApiKey-värdet är den **nyckel** från sidan nycklar för Azure datorresurs för visuellt innehåll. 

## <a name="recognize-text-container-docker-examples"></a>Identifiera text behållare Docker-exempel

I följande exempel Docker är för behållaren identifiera text. 

### <a name="basic-example"></a>Grundläggande exempel 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
  Eula=accept \
  Billing={BILLING_ENDPOINT_URI} \
  ApiKey={BILLING_KEY} 
  ```

### <a name="logging-example"></a>Exempel för loggning 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
  Eula=accept \
  Billing={BILLING_ENDPOINT_URI} \
  ApiKey={BILLING_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>Nästa steg

* Granska [hur du installerar och kör behållare](computer-vision-how-to-install-containers.md)
