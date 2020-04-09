---
title: Konfigurera behållare - Datorseende
titleSuffix: Azure Cognitive Services
description: Den här artikeln visar hur du konfigurerar både obligatoriska och valfria inställningar för identifiera textbehållare i datorseende.
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
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879324"
---
# <a name="configure-computer-vision-docker-containers"></a>Konfigurera behållare för datorseendedocker

Du konfigurerar datorseende-behållarens körningsmiljö `docker run` med hjälp av kommandoargumenten. Den här behållaren har flera nödvändiga inställningar, tillsammans med några valfria inställningar. Flera [exempel på](#example-docker-run-commands) kommandot är tillgängliga. De behållarspecifika inställningarna är faktureringsinställningarna. 

## <a name="configuration-settings"></a>Konfigurationsinställningar

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [`ApiKey`](#apikey-configuration-setting)Inställningarna [`Billing`](#billing-configuration-setting), [`Eula`](#eula-setting) och används tillsammans och du måste ange giltiga värden för alla tre. Annars startar inte behållaren. Mer information om hur du använder de här konfigurationsinställningarna för att instansiera en behållare finns i [Fakturering](computer-vision-how-to-install-containers.md).

## <a name="apikey-configuration-setting"></a>Konfigurationsinställning för ApiKey

Inställningen `ApiKey` anger den `Cognitive Services` Azure-resursnyckel som används för att spåra faktureringsinformation för behållaren. Du måste ange ett värde för ApiKey och värdet måste vara en giltig [`Billing`](#billing-configuration-setting) nyckel för cognitive services-resursen som angetts för konfigurationsinställningen. _Cognitive Services_

Den här inställningen finns på följande plats:

* Azure-portal: **Resurshantering för Kognitiva tjänster** under **Nycklar**

## <a name="applicationinsights-setting"></a>Inställning av ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Konfigurationsinställning för fakturering

Inställningen `Billing` anger slutpunkts-URI för _Cognitive Services-resursen_ på Azure som används för att mäta faktureringsinformation för behållaren. Du måste ange ett värde för den här konfigurationsinställningen och värdet måste vara en giltig slutpunkts-URI för en _Cognitive Services-resurs_ på Azure. Behållaren rapporterar användning ungefär var 10 till 15:e minut.

Den här inställningen finns på följande plats:

* Azure-portal: Översikt över **Kognitiva tjänster,** märkt`Endpoint`

Kom ihåg `vision/v1.0` att lägga till routningen i slutpunkts-URI enligt följande tabell. 

|Krävs| Namn | Datatyp | Beskrivning |
|--|------|-----------|-------------|
|Ja| `Billing` | Sträng | URI för faktureringsslutpunkt<br><br>Exempel:<br>`Billing=https://westcentralus.api.cognitive.microsoft.com/vision/v1.0` |

## <a name="eula-setting"></a>Eula-inställning

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Flytande inställningar

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Inställningar för HTTP-proxyautentiseringsuppgifter

[!INCLUDE [Container shared configuration HTTP proxy settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Loggningsinställningar
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Montera inställningar

Använd bindningsfästen för att läsa och skriva data till och från behållaren. Du kan ange ett indatafäste eller `--mount` utdatafäste genom att ange alternativet i [dockerkörningskommandot.](https://docs.docker.com/engine/reference/commandline/run/)

Datorseende-behållarna använder inte indata- eller utdatafästen för att lagra tränings- eller servicedata. 

Den exakta syntaxen för värdmonteringsplatsen varierar beroende på värdoperativsystemet. Dessutom kanske [värddatorns](computer-vision-how-to-install-containers.md#the-host-computer)monteringsplats inte är tillgänglig på grund av en konflikt mellan behörigheter som används av Docker-tjänstkontot och värdfästeplatsbehörigheterna. 

|Valfri| Namn | Datatyp | Beskrivning |
|-------|------|-----------|-------------|
|Inte tillåten| `Input` | Sträng | Datorseende behållare använder inte detta.|
|Valfri| `Output` | Sträng | Målet för utmatningsfästet. Standardvärdet är `/output`. Det här är platsen för loggarna. Detta inkluderar behållarloggar. <br><br>Exempel:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Exempel på dockerkörningskommandon

I följande exempel används konfigurationsinställningarna för `docker run` att illustrera hur du skriver och använder kommandon.  När den körs fortsätter behållaren att köras tills du [stoppar](computer-vision-how-to-install-containers.md#stop-the-container) den.

* **Rad-fortsättning**tecken : Docker kommandon i följande avsnitt använder `\`tillbaka snedstreck, , som en rad fortsättning tecken. Ersätt eller ta bort detta baserat på värdoperativsystemets krav. 
* **Argumentordning**: Ändra inte ordningen på argumenten om du inte är väl bekant med Docker-behållare.

Ersätt {_argument_name_} med dina egna värderingar:

| Platshållare | Värde | Format eller exempel |
|-------------|-------|---|
| **{API_KEY}** | Slutpunktsnyckeln `Computer Vision` för resursen `Computer Vision` på sidan Azure Keys. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | Värdet för faktureringsslutpunkt är `Computer Vision` tillgängligt på sidan Azure Overview.| Se [samla in nödvändiga parametrar](computer-vision-how-to-install-containers.md#gathering-required-parameters) för explicita exempel. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> Alternativen `Eula` `Billing`, `ApiKey` och måste anges för att behållaren ska kunna köras. Annars startar inte behållaren.  Mer information finns i [Fakturering](computer-vision-how-to-install-containers.md#billing).
> ApiKey-värdet är **nyckeln** från `Cognitive Services` sidan Azure Resource keys.

## <a name="container-docker-examples"></a>Exempel på containerdockor

Följande Docker-exempel är för Läs-behållaren.

### <a name="basic-example"></a>Grundläggande exempel

  ```docker
  docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
  containerpreview.azurecr.io/microsoft/cognitive-services-read \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example"></a>Exempel på loggning 

  ```docker
  docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
  containerpreview.azurecr.io/microsoft/cognitive-services-read \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>Nästa steg

* Granska [Hur du installerar och kör behållare](computer-vision-how-to-install-containers.md).
