---
title: Konfigurera behållare – Visuellt innehåll
titleSuffix: Azure Cognitive Services
description: Konfigurera olika inställningar för Identifiera text behållare i Visuellt innehåll.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: dapine
ms.custom: seodec18
ms.openlocfilehash: 3e1dc68ec67e8a7a24c3459519df80a8faf2fc01
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565650"
---
# <a name="configure-recognize-text-docker-containers"></a>Konfigurera Identifiera text Docker-behållare

Körnings miljön för **identifiera text** container konfigureras med `docker run` hjälp av kommando argumenten. Den här behållaren har flera inställningar som krävs, tillsammans med några valfria inställningar. Flera [exempel](#example-docker-run-commands) kommandots är tillgängliga. De behållar-/regionsspecifika inställningarna är fakturerings inställningarna. 

## <a name="configuration-settings"></a>Konfigurationsinställningar

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> Den [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), och [ `Eula` ](#eula-setting) inställningar används tillsammans, och du måste ange giltiga värden för alla tre av dem, annars din behållare startar inte. Läs mer om att använda dessa konfigurationsinställningar för att skapa en instans av en behållare, [fakturering](computer-vision-how-to-install-containers.md).

## <a name="apikey-configuration-setting"></a>ApiKey konfigurationsinställning

Inställningen anger den Azure `Cognitive Services` -resurs nyckel som används för att spåra fakturerings information för behållaren. `ApiKey` Du måste ange ett värde för ApiKey och värdet måste vara en giltig nyckel för den _Cognitive Services_ resurs som angetts för [`Billing`](#billing-configuration-setting) konfigurations inställningen.

Du hittar den här inställningen på följande plats:

* Azure-portalen: **Cognitive Services** Resurs hantering, under **nycklar**

## <a name="applicationinsights-setting"></a>Inställningen för ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Fakturering konfigurationsinställning

Inställningen anger slut punkts-URI för den Cognitive Services resursen på Azure som används för att mäta fakturerings information för behållaren.  `Billing` Du måste ange ett värde för den här konfigurations inställningen och värdet måste vara en giltig slut punkts-URI för en _Cognitive Services_ -resurs på Azure. Behållar rapporteringen visar var 10 till 15: e minut.

Du hittar den här inställningen på följande plats:

* Azure-portalen: **Cognitive Services** Översikt, märkt`Endpoint`

Kom ihåg att lägga `vision/v1.0` till operationsföljden i slut punkts-URI: n enligt följande tabell. 

|Obligatorisk| Namn | Datatyp | Beskrivning |
|--|------|-----------|-------------|
|Ja| `Billing` | Sträng | Fakturering endpoint URI<br><br>Exempel:<br>`Billing=https://westcentralus.api.cognitive.microsoft.com/vision/v1.0` |

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

Visuellt innehåll behållare använder inte indata eller utdata monteras för att lagra utbildning eller tjänst data. 

Den exakta syntaxen hos montera värdplats varierar beroende på värdens operativsystem. Dessutom kanske [värd datorns](computer-vision-how-to-install-containers.md#the-host-computer)monterings plats inte är tillgänglig på grund av en konflikt mellan behörigheter som används av Docker-tjänstkontot och värd monterings platsens behörigheter. 

|Valfritt| Namn | Datatyp | Beskrivning |
|-------|------|-----------|-------------|
|Inte tillåtet| `Input` | Sträng | Visuellt innehåll behållare använder inte detta.|
|Valfritt| `Output` | Sträng | Utdata mount-mål. Standardvärdet är `/output`. Det här är platsen för loggarna. Detta inkluderar behållar loggar. <br><br>Exempel:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Exempel docker-kommandon 

I följande exempel används konfigurationsinställningarna som illustrerar hur du skriver och använda `docker run` kommandon.  När du kör, behållaren fortsätter att köras tills du [stoppa](computer-vision-how-to-install-containers.md#stop-the-container) den.

* **Rad fortsättnings avstånd**: Docker- `\`kommandona i följande avsnitt använder omvänt snedstreck, som ett fortsättnings streck. Ersätta eller ta bort detta baserat på din värdoperativsystemet. 
* **Argument ordning**: Ändra inte ordningen på argumenten om du inte är bekant med Docker-behållare.

Kom ihåg att lägga `vision/v1.0` till operationsföljden i slut punkts-URI: n enligt följande tabell. 

Ersätt {_argument_name_} med dina egna värden:

| Platshållare | Värde | Format eller exempel |
|-------------|-------|---|
|{API_KEY} | Cognitive Services resursens slut punkts nyckel. |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{ENDPOINT_URI} | Värdet för fakturerings slut punkten inklusive region.|`https://westcentralus.api.cognitive.microsoft.com/vision/v1.0`|

> [!IMPORTANT]
> Den `Eula`, `Billing`, och `ApiKey` alternativ måste anges för att köra behållaren, i annat fall startar inte behållaren.  Mer information finns i [fakturering](computer-vision-how-to-install-containers.md#billing).
> ApiKey-värdet är **nyckeln** på sidan med Azures `Cognitive Services` resurs nycklar. 

## <a name="recognize-text-container-docker-examples"></a>Identifiera Docker-exempel för text behållare

Följande Docker-exempel är för text behållaren identifiera text. 

### <a name="basic-example"></a>Grundläggande exempel 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example"></a>Loggnings exempel 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>Nästa steg

* Granska [hur du installerar och kör behållare](computer-vision-how-to-install-containers.md)
