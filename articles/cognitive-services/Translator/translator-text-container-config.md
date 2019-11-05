---
title: Konfigurera behållare – Translator Text
titleSuffix: Azure Cognitive Services
description: Translator Text innehåller behållare med ett gemensamt konfigurations ramverk, så att du enkelt kan konfigurera och hantera lagring, loggning och telemetri och säkerhets inställningar för dina behållare.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 3dbedcb5e5212091dc8906defa2b1cf5c7868d60
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507675"
---
# <a name="configure-translator-text-docker-containers"></a>Konfigurera Translator Text Docker-behållare

Translator Text förser varje behållare med ett gemensamt konfigurations ramverk, så att du enkelt kan konfigurera och hantera lagring, loggning och telemetri och säkerhets inställningar för dina behållare.

## <a name="configuration-settings"></a>Konfigurationsinställningar

Behållaren har följande konfigurations inställningar:

|Krävs|Inställning|Syfte|
|--|--|--|
|Nej|[ApplicationInsights](#applicationinsights-setting)|Aktiverar tillägg av stöd för [Azure Application insikter](https://docs.microsoft.com/azure/application-insights) -telemetri till din behållare.|
|Ja|[Villkoren](#eula-setting)| Anger att du har accepterat licensen för behållaren.|
|Nej|[Fluent](#fluentd-settings)|Skriver logg och, om du vill, Metric-data till en Fluent-Server.|
|Nej|HTTP-proxy|Konfigurerar en HTTP-proxy för att göra utgående begär Anden.|
|Nej|[Logging](#logging-settings)|Tillhandahåller ASP.NET Core loggnings stöd för din behållare. |
|Nej|[Monterar](#mount-settings)|Läser och skriver data från värddatorn till behållaren och från behållaren tillbaka till värddatorn.|

> [!IMPORTANT]
> Inställningen [`Eula`](#eula-setting) måste anges med värdet för `accept`. annars startar inte behållaren.

## <a name="applicationinsights-setting"></a>ApplicationInsights-inställning

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="eula-setting"></a>Licens avtals inställning

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluent-inställningar

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Inställningar för autentiseringsuppgifter för HTTP-proxy

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Loggnings inställningar
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Monterings inställningar

Använd bind-monteringar för att läsa och skriva data till och från behållaren. Du kan ange en inmatnings montering eller utmatnings montering genom att ange alternativet `--mount` i kommandot [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) .

Translator Text behållare använder inte indata eller utdata monteras för att lagra utbildning eller tjänst data. 

Den exakta syntaxen för värd monterings platsen varierar beroende på värd operativ systemet. Dessutom kanske [värd datorns](how-to-install-containers.md#the-host-computer)monterings plats inte är tillgänglig på grund av en konflikt mellan behörigheter som används av Docker-tjänstkontot och värd monterings platsens behörigheter. 

|Valfri| Namn | Datatyp | Beskrivning |
|-------|------|-----------|-------------|
|Tillåts inte| `Input` | Sträng | Translator Text behållare använder inte detta.|
|Valfri| `Output` | Sträng | Målet för utmatnings monteringen. Standardvärdet är `/output`. Detta är platsen för loggarna. Detta inkluderar behållar loggar. <br><br>Exempel:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Exempel på Docker-körnings kommandon 

I följande exempel används konfigurations inställningarna för att illustrera hur du skriver och använder `docker run`-kommandon.  När den körs fortsätter behållaren att köras tills du [stoppar](how-to-install-containers.md#stop-the-container) den.

* **Rad fortsättnings bokstav**: Docker-kommandona i följande avsnitt använder omvänt snedstreck `\`, som ett fortsättnings streck. Ersätt eller ta bort detta baserat på värd operativ systemets krav. 
* **Argument ordning**: Ändra inte ordningen på argumenten om du inte är bekant med Docker-behållare.

## <a name="translator-text-container-docker-examples"></a>Docker-exempel för Translator Text container

Följande Docker-exempel är för Translator Text container.

### <a name="basic-example"></a>Basic-exempel

```
docker run --rm -it -p 5000:80 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-translator-text \
Eula=accept
```

### <a name="logging-example"></a>Loggnings exempel

```
docker run --rm -it -p 5000:80 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-translator-text \
Eula=accept \
Logging:Console:LogLevel:Default=Information
```

## <a name="next-steps"></a>Nästa steg

* Granska [hur du installerar och kör behållare](how-to-install-containers.md)
* Använd fler [Cognitive Services behållare](../cognitive-services-container-support.md)
