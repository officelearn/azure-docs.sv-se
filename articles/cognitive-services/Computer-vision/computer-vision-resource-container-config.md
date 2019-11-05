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
ms.date: 11/04/2019
ms.author: dapine
ms.custom: seodec18
ms.openlocfilehash: 1df8199abbbc195db873ab3da515cb1dd5fe9761
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73484093"
---
# <a name="configure-computer-vision-docker-containers"></a>Konfigurera Visuellt innehåll Docker-behållare

Du konfigurerar Visuellt innehåll behållarens körnings miljö med hjälp av `docker run` kommando argument. Den här behållaren har flera inställningar som krävs, tillsammans med några valfria inställningar. Det finns flera [exempel](#example-docker-run-commands) på kommandot. De behållar-/regionsspecifika inställningarna är fakturerings inställningarna. 

## <a name="configuration-settings"></a>Konfigurationsinställningar

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> Inställningarna [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting)och [`Eula`](#eula-setting) används tillsammans och du måste ange giltiga värden för alla tre. annars startar inte behållaren. Mer information om hur du använder dessa konfigurations inställningar för att instansiera en behållare finns i [fakturering](computer-vision-how-to-install-containers.md).

## <a name="apikey-configuration-setting"></a>Konfigurations inställning för ApiKey

Inställningen `ApiKey` anger den Azure `Cognitive Services` resurs nyckel som används för att spåra fakturerings information för behållaren. Du måste ange ett värde för ApiKey och värdet måste vara en giltig nyckel för den _Cognitive Services_ resurs som angetts för inställningen [`Billing`](#billing-configuration-setting) konfiguration.

Du hittar den här inställningen på följande plats:

* Azure Portal: **Cognitive Services** resurs hantering under **nycklar**

## <a name="applicationinsights-setting"></a>ApplicationInsights-inställning

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Konfigurations inställning för fakturering

Inställningen `Billing` anger slut punkts-URI för den _Cognitive Services_ resursen på Azure som används för att mäta fakturerings information för behållaren. Du måste ange ett värde för den här konfigurations inställningen och värdet måste vara en giltig slut punkts-URI för en _Cognitive Services_ -resurs på Azure. Behållar rapporteringen visar var 10 till 15: e minut.

Du hittar den här inställningen på följande plats:

* Azure Portal: **Cognitive Services** översikt, med etiketter `Endpoint`

Kom ihåg att lägga till `vision/v1.0` routning i slut punkts-URI: n enligt följande tabell. 

|Krävs| Namn | Datatyp | Beskrivning |
|--|------|-----------|-------------|
|Ja| `Billing` | Sträng | URI för fakturerings slut punkt<br><br>Exempel:<br>`Billing=https://westcentralus.api.cognitive.microsoft.com/vision/v1.0` |

## <a name="eula-setting"></a>Licens avtals inställning

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluent-inställningar

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Inställningar för autentiseringsuppgifter för HTTP-proxy

[!INCLUDE [Container shared configuration HTTP proxy settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Loggnings inställningar
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Monterings inställningar

Använd bind-monteringar för att läsa och skriva data till och från behållaren. Du kan ange en inmatnings montering eller utmatnings montering genom att ange alternativet `--mount` i kommandot [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) .

Visuellt innehåll behållare använder inte indata eller utdata monteras för att lagra utbildning eller tjänst data. 

Den exakta syntaxen för värd monterings platsen varierar beroende på värd operativ systemet. Dessutom kanske [värd datorns](computer-vision-how-to-install-containers.md#the-host-computer)monterings plats inte är tillgänglig på grund av en konflikt mellan behörigheter som används av Docker-tjänstkontot och värd monterings platsens behörigheter. 

|Valfri| Namn | Datatyp | Beskrivning |
|-------|------|-----------|-------------|
|Tillåts inte| `Input` | Sträng | Visuellt innehåll behållare använder inte detta.|
|Valfri| `Output` | Sträng | Målet för utmatnings monteringen. Standardvärdet är `/output`. Detta är platsen för loggarna. Detta inkluderar behållar loggar. <br><br>Exempel:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Exempel på Docker-körnings kommandon

I följande exempel används konfigurations inställningarna för att illustrera hur du skriver och använder `docker run`-kommandon.  När den körs fortsätter behållaren att köras tills du [stoppar](computer-vision-how-to-install-containers.md#stop-the-container) den.

* **Rad fortsättnings bokstav**: Docker-kommandona i följande avsnitt använder omvänt snedstreck `\`, som ett fortsättnings streck. Ersätt eller ta bort detta baserat på värd operativ systemets krav. 
* **Argument ordning**: Ändra inte ordningen på argumenten om du inte är bekant med Docker-behållare.

Ersätt {_argument_name_} med dina egna värden:

| Platshållare | Värde | Format eller exempel |
|-------------|-------|---|
| **{API_KEY}** | Slut punkts nyckeln för `Computer Vision` resursen på sidan för Azure `Computer Vision`-nycklar. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | Värdet för fakturerings slut punkten är tillgängligt på översikts sidan för Azure-`Computer Vision`.| Se [samla in obligatoriska parametrar](computer-vision-how-to-install-containers.md#gathering-required-parameters) för explicita exempel. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> Alternativen `Eula`, `Billing`och `ApiKey` måste anges för att köra behållaren. annars startar inte behållaren.  Mer information finns i [fakturering](computer-vision-how-to-install-containers.md#billing).
> ApiKey-värdet är **nyckeln** på sidan med resurs nycklar för Azure `Cognitive Services`.

## <a name="container-docker-examples"></a>Behållare Docker-exempel

Följande Docker-exempel är för Read-behållaren.

### <a name="basic-example"></a>Basic-exempel

  ```docker
  docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
  containerpreview.azurecr.io/microsoft/cognitive-services-read \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example"></a>Loggnings exempel 

  ```docker
  docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
  containerpreview.azurecr.io/microsoft/cognitive-services-read \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>Nästa steg

* Läs [om hur du installerar och kör behållare](computer-vision-how-to-install-containers.md).
