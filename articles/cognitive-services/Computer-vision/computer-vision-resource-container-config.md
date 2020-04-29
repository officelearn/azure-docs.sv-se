---
title: Konfigurera behållare – Visuellt innehåll
titleSuffix: Azure Cognitive Services
description: Den här artikeln visar hur du konfigurerar både nödvändiga och valfria inställningar för Identifiera text behållare i Visuellt innehåll.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.custom: seodec18
ms.openlocfilehash: 3be302019c712c13bd29d7ed3781151a1648e847
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "80879324"
---
# <a name="configure-computer-vision-docker-containers"></a>Konfigurera Visuellt innehåll Docker-behållare

Du konfigurerar Visuellt innehåll behållarens körnings miljö med hjälp av `docker run` kommando argumenten. Den här behållaren har flera inställningar som krävs, tillsammans med några valfria inställningar. Det finns flera [exempel](#example-docker-run-commands) på kommandot. De behållar-/regionsspecifika inställningarna är fakturerings inställningarna. 

## <a name="configuration-settings"></a>Konfigurationsinställningar

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> Inställningarna [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting)och [`Eula`](#eula-setting) används tillsammans och du måste ange giltiga värden för alla tre. annars startar inte behållaren. Mer information om hur du använder dessa konfigurations inställningar för att instansiera en behållare finns i [fakturering](computer-vision-how-to-install-containers.md).

## <a name="apikey-configuration-setting"></a>Konfigurations inställning för ApiKey

`ApiKey` Inställningen anger den Azure `Cognitive Services` -resurs nyckel som används för att spåra fakturerings information för behållaren. Du måste ange ett värde för ApiKey och värdet måste vara en giltig nyckel för den _Cognitive Services_ resurs som angetts för [`Billing`](#billing-configuration-setting) konfigurations inställningen.

Du hittar den här inställningen på följande plats:

* Azure Portal: **Cognitive Services** resurs hantering under **nycklar**

## <a name="applicationinsights-setting"></a>ApplicationInsights-inställning

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Konfigurations inställning för fakturering

Inställningen anger slut punkts-URI för den Cognitive Services resursen på Azure som används för att mäta fakturerings information för behållaren. _Cognitive Services_ `Billing` Du måste ange ett värde för den här konfigurations inställningen och värdet måste vara en giltig slut punkts-URI för en _Cognitive Services_ -resurs på Azure. Behållar rapporteringen visar var 10 till 15: e minut.

Du hittar den här inställningen på följande plats:

* Azure Portal: **Cognitive Services** översikt, etiketterad`Endpoint`

Kom ihåg att lägga `vision/v1.0` till operationsföljden i slut punkts-URI: n enligt följande tabell. 

|Krävs| Name | Datatyp | Beskrivning |
|--|------|-----------|-------------|
|Ja| `Billing` | Sträng | URI för fakturerings slut punkt<br><br>Exempel:<br>`Billing=https://westcentralus.api.cognitive.microsoft.com/vision/v1.0` |

## <a name="eula-setting"></a>Licens avtals inställning

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluent-inställningar

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Inställningar för autentiseringsuppgifter för HTTP-proxy

[!INCLUDE [Container shared configuration HTTP proxy settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Loggningsinställningar
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Monterings inställningar

Använd bind-monteringar för att läsa och skriva data till och från behållaren. Du kan ange en inmatnings montering eller utmatnings `--mount` montering genom att ange alternativet i kommandot [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) .

Visuellt innehåll behållare använder inte indata eller utdata monteras för att lagra utbildning eller tjänst data. 

Den exakta syntaxen för värd monterings platsen varierar beroende på värd operativ systemet. Dessutom kanske [värd datorns](computer-vision-how-to-install-containers.md#the-host-computer)monterings plats inte är tillgänglig på grund av en konflikt mellan behörigheter som används av Docker-tjänstkontot och värd monterings platsens behörigheter. 

|Valfri| Name | Datatyp | Beskrivning |
|-------|------|-----------|-------------|
|Inte tillåten| `Input` | Sträng | Visuellt innehåll behållare använder inte detta.|
|Valfri| `Output` | Sträng | Målet för utmatnings monteringen. Standardvärdet är `/output`. Detta är platsen för loggarna. Detta inkluderar behållar loggar. <br><br>Exempel:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Exempel på Docker-körnings kommandon

I följande exempel används konfigurations inställningarna för att illustrera hur du skriver och använder `docker run` kommandon.  När den körs fortsätter behållaren att köras tills du [stoppar](computer-vision-how-to-install-containers.md#stop-the-container) den.

* **Rad fortsättnings bokstav**: Docker-kommandona i följande avsnitt använder omvänt snedstreck `\`, som ett fortsättnings steg. Ersätt eller ta bort detta baserat på värd operativ systemets krav. 
* **Argument ordning**: Ändra inte ordningen på argumenten om du inte är bekant med Docker-behållare.

Ersätt {_argument_name_} med dina egna värden:

| Platshållare | Värde | Format eller exempel |
|-------------|-------|---|
| **{API_KEY}** | `Computer Vision` Resursens slut punkts nyckel på sidan med `Computer Vision` Azure-nycklar. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | Värdet för fakturerings slut punkten är tillgängligt på sidan `Computer Vision` Azure-översikt.| Se [samla in obligatoriska parametrar](computer-vision-how-to-install-containers.md#gathering-required-parameters) för explicita exempel. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> Alternativen `Eula`, `Billing`och `ApiKey` måste anges för att köra behållaren. annars startar inte behållaren.  Mer information finns i [fakturering](computer-vision-how-to-install-containers.md#billing).
> ApiKey-värdet är **nyckeln** på sidan med Azures `Cognitive Services` resurs nycklar.

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
