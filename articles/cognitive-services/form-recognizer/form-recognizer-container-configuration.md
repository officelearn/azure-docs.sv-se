---
title: Så här konfigurerar du en behållare för formulär igenkänning
titleSuffix: Azure Cognitive Services
description: Lär dig hur du konfigurerar formulärets tolknings behållare för att tolka formulär-och tabell data.
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: dapine
ms.openlocfilehash: af30719ead8464d0420734818203b8070eb5d145
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2019
ms.locfileid: "71105110"
---
# <a name="configure-form-recognizer-containers"></a>Konfigurera formulär igenkännings behållare

Med hjälp av Azure formulär igenkännings behållare kan du skapa en program arkitektur som är optimerad för att dra nytta av både robusta moln funktioner och Edge-plats.

Du konfigurerar körnings miljön för formulär igenkännings behållare med hjälp `docker run` av kommando argumenten. Den här behållaren har flera nödvändiga inställningar och några valfria inställningar. Några exempel finns i avsnittet ["exempel på Docker körnings kommandon"](#example-docker-run-commands) . De behållar-/regionsspecifika inställningarna är fakturerings inställningarna.

## <a name="configuration-settings"></a>Konfigurationsinställningar

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> Inställningarna [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) [och`Eula`](#eula-setting) används tillsammans. Du måste ange giltiga värden för alla tre inställningarna. annars startar inte behållaren. Läs mer om att använda dessa konfigurationsinställningar för att skapa en instans av en behållare, [fakturering](form-recognizer-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>ApiKey konfigurationsinställning

`ApiKey` Inställningen anger den Azure-resurs nyckel som används för att spåra fakturerings information för behållaren. Värdet för ApiKey måste vara en giltig nyckel för _formulär igenkännings_ resursen som anges i avsnittet "konfigurations `Billing` inställning för fakturering".

Du hittar den här inställningen i Azure Portal i **formulär igenkännings resurs hantering**under **nycklar**.

## <a name="applicationinsights-setting"></a>Inställningen för ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Fakturering konfigurationsinställning

Inställningen anger slut punkts-URI för _formulär igenkännings_ resursen på Azure som används för att mäta fakturerings information för behållaren. `Billing` Värdet för den här konfigurations inställningen måste vara en giltig slut punkts-URI för en _formulär igenkännings_ resurs på Azure. Behållar rapporteringen visar var 10 till 15: e minut.

Du hittar den här inställningen i rutan Azure Portal, i **formulär igenkännings översikt**, under **slut punkt**.

|Obligatorisk| Namn | Datatyp | Beskrivning |
|--|------|-----------|-------------|
|Ja| `Billing` | Sträng | Fakturering endpoint URI<br><br>Exempel:<br>`Billing=https://westus2.api.cognitive.microsoft.com/` |

## <a name="eula-setting"></a>Licensvillkor för inställningen

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd-inställningar

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Inställningar för autentiseringsuppgifter för HTTP-proxy

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Loggningsinställningar

[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]


## <a name="mount-settings"></a>Monteringsinställningar

Använd bindning monterar för att läsa och skriva data till och från behållaren. Du kan ange en inmatnings montering eller en utmatnings `--mount` montering genom att ange alternativet [ `docker run` i kommandot](https://docs.docker.com/engine/reference/commandline/run/).

Formulär identifierarens behållare kräver en indata montering och en utgående data montering. Monteringen av indata kan vara skrivskyddad och krävs för åtkomst till de data som används för utbildning och bedömning. Monteringen av utdata måste vara skrivbar och du kan använda den för att lagra modeller och temporära data.

Den exakta syntaxen hos montera värdplats varierar beroende på värdens operativsystem. Dessutom kanske [värd datorns](form-recognizer-container-howto.md#the-host-computer) monterings plats inte är tillgänglig på grund av en konflikt mellan Docker-tjänstens konto behörigheter och värd monterings platsens behörigheter.

|Valfritt| Name | Datatyp | Beskrivning |
|-------|------|-----------|-------------|
|Obligatorisk| `Input` | Sträng | Inkommande monterings-mål. Standardvärdet är `/input`.    <br><br>Exempel:<br>`--mount type=bind,src=c:\input,target=/input`|
|Obligatorisk| `Output` | Sträng | Utdata mount-mål. Standardvärdet är `/output`.  <br><br>Exempel:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Exempel docker-kommandon

I följande exempel används konfigurationsinställningarna som illustrerar hur du skriver och använda `docker run` kommandon. När den körs fortsätter behållaren att köras tills du [stoppar den](form-recognizer-container-howto.md#stop-the-container).

* **Rad fortsättnings avstånd**: Docker-kommandona i följande avsnitt använder ett omvänt snedstreck\\() som ett fortsättnings streck. Ersätt eller ta bort det här tecknen, beroende på värd operativ systemets krav.
* **Argument ordning**: Ändra inte ordningen på argumenten om du inte är bekant med Docker-behållare.

Ersätt {_argument_name_} i följande tabell med dina egna värden:

| Platshållare | Value |
|-------------|-------|
| **{FORM_RECOGNIZER_API_KEY}** | Den nyckel som används för att starta behållaren. Den finns på sidan Azure Portal formulär igenkännings nycklar. |
| **{FORM_RECOGNIZER_ENDPOINT_URI}** | URI-värdet för fakturerings slut punkten är tillgängligt på översikts sidan för Azure Portal formulär igenkänning.|
| **{COMPUTER_VISION_API_KEY}** | Nyckeln finns på sidan Azure Portal API för visuellt innehåll nycklar.|
| **{COMPUTER_VISION_ENDPOINT_URI}** | Fakturerings slut punkten. Om du använder en molnbaserad Visuellt innehåll resurs finns URI-värdet på översikts sidan för Azure Portal API för visuellt innehåll. Om du använder en *kognitivt-tjänster-identifiera-text* behållare använder du URL: en för fakturerings slut punkt som skickas till behållaren i `docker run` kommandot. |

Se [samla in obligatoriska parametrar](form-recognizer-container-howto.md#gathering-required-parameters) för information om hur du hämtar dessa värden.

[!INCLUDE [cognitive-services-custom-subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> Om du vill köra behållaren anger `Eula`du alternativen, `Billing`och `ApiKey` . annars startar inte behållaren. Mer information finns i [fakturering](#billing-configuration-setting).

## <a name="form-recognizer-container-docker-examples"></a>Formulär identifierarens Docker-exempel

Följande Docker-exempel är för formulär igenkännings behållaren.

### <a name="basic-example-for-form-recognizer"></a>Basic-exempel för formulär igenkänning

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

### <a name="logging-example-for-form-recognizer"></a>Exempel på loggning av formulär tolken

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

* Granska [installations-och körnings behållare](form-recognizer-container-howto.md).
