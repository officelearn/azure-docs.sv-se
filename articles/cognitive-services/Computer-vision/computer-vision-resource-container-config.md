---
title: Konfigurera containrar
titlesuffix: Computer Vision - Cognitive Services - Azure
description: Konfigurationsinställningar för behållare i visuellt.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: diberry
ms.openlocfilehash: 865300f74a74b9768d2d5f5fd532ce9e851b7afb
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52964108"
---
# <a name="configure-containers"></a>Konfigurera containrar

Visuellt innehåll ger identifiera Text behållaren med ett gemensamt ramverk för konfiguration, så att du enkelt kan konfigurera och hantera inställningar för lagring, loggning och telemetri och säkerhet för dina behållare.

## <a name="configuration-settings"></a>Konfigurationsinställningar

Konfigurationsinställningar i visuellt behållare är hierarkiska alla behållare använder en delad hierarki, baserat på följande toppnivåstruktur:

* [ApiKey](#apikey-configuration-setting)
* [ApplicationInsights](#applicationinsights-configuration-settings)
* [Autentisering](#authentication-configuration-settings)
* [Billing](#billing-configuration-setting)
* [Licensvillkor](#eula-configuration-setting)
* [Fluentd](#fluentd-configuration-settings)
* [Loggning](#logging-configuration-settings)
* [Monterar](#mounts-configuration-settings)

Du kan använda antingen [miljövariabler](#configuration-settings-as-environment-variables) eller [kommandoradsargument](#configuration-settings-as-command-line-arguments) att ange konfigurationsinställningar för när instanser skapades av en behållare från visuellt behållare.

Värden för miljövariabler åsidosätta värden för kommandoradsargument, vilket i sin tur åsidosätter standardvärdena för behållaravbildningen. Med andra ord, om du anger olika värden i en miljövariabel och ett argument på kommandoraden för samma konfigurationsinställning som `Logging:Disk:LogLevel`, sedan skapa en instans av en behållare, används värdet i miljövariabeln genom den skapade instanser behållaren.

### <a name="configuration-settings-as-environment-variables"></a>Konfigurationsinställningar som miljövariabler

Du kan använda den [variabeln syntax för ASP.NET Core-miljö](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.1&tabs=basicconfiguration#configuration-by-environment) att ange konfigurationsinställningar.

Behållaren läser miljövariabler för användaren när behållaren instantieras. Om det finns en miljövariabel, åsidosätter värdet för miljövariabeln standardvärdet för den angivna Konfigurationsinställningen. Fördelen med att använda miljövariabler är att flera konfigurationsinställningar kan anges innan kontrollanten behållare och flera behållare kan använda samma uppsättning inställningar automatiskt.

Till exempel följande kommandon använda en miljövariabel för att konfigurera konsolen loggningsnivån till [LogLevel.Information](https://msdn.microsoft.com), sedan skapar en instans av en behållare från behållaravbildningen identifiera Text. Värdet för miljövariabeln åsidosätter standardinställningen för konfigurationen.

  ```Docker
  SET Logging:Console:LogLevel=Information
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/vision/v1.0 ApiKey=0123456789
  ```

### <a name="configuration-settings-as-command-line-arguments"></a>Konfigurationsinställningar som kommandoradsargument

Du kan använda den [ASP.NET Core kommandoradsargumentet syntax](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.1&tabs=basicconfiguration#arguments) att ange konfigurationsinställningar.

Du kan ange konfigurationsinställningarna i den valfria `ARGS` -parametern för den [docker kör](https://docs.docker.com/engine/reference/commandline/run/) kommando som används för att skapa en instans av en behållare från en hämtade behållaravbildning. Fördelen med att använda kommandoradsargument är att varje behållare kan använda en annan, anpassad uppsättning konfigurationsinställningar.

Till exempel följande kommando skapar en instans av en behållare från behållaravbildningen identifiera Text och konfigurerar konsolen loggningsnivå LogLevel.Information, åsidosätter standardinställningen för konfigurationen.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/vision/v1.0 ApiKey=0123456789 Logging:Console:LogLevel=Information
  ```

## <a name="apikey-configuration-setting"></a>ApiKey konfigurationsinställning

Den `ApiKey` konfigurationsinställning anger konfigurationsnyckeln för visuellt innehåll-resurs på Azure som används för att spåra faktureringsinformation för behållaren. Du måste ange ett värde för den här inställningen och värdet måste vara en giltig Konfigurationsnyckel för den resursen för visuellt innehåll som angetts för den [ `Billing` ](#billing-configuration-setting) konfigurationsinställning.

> [!IMPORTANT]
> Den [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), och [ `Eula` ](#eula-configuration-setting) konfigurationsinställningar som används tillsammans och du måste ange giltiga värden för alla tre dem. Annars startar behållaren inte. Läs mer om att använda dessa konfigurationsinställningar för att skapa en instans av en behållare, [fakturering](computer-vision-how-to-install-containers.md#billing).

## <a name="applicationinsights-configuration-settings"></a>Inställningar för ApplicationInsights

Konfigurationsinställningarna i den `ApplicationInsights` avsnittet kan du lägga till [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) telemetri stöd till behållaren. Application Insights erbjuder djupgående övervakning av behållaren kod-nivå. Du kan enkelt övervaka din behållare för tillgänglighet, prestanda och användning. Du kan också snabbt identifiera och diagnostisera fel i din behållare utan att behöva vänta på att en användare rapporterar dem.

I följande tabell beskrivs de konfigurationsinställningar som stöds den `ApplicationInsights` avsnittet.

| Namn | Datatyp | Beskrivning |
|------|-----------|-------------|
| `InstrumentationKey` | Sträng | Instrumenteringsnyckeln för Application Insights-instans till vilken telemetri skickas data för behållaren. Mer information finns i [Application Insights för ASP.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core). |

## <a name="authentication-configuration-settings"></a>Konfigurationsinställningar för autentisering

Den `Authentication` konfigurationsinställningar tillhandahåller alternativ för Azure-säkerhet för dina behållare. Även om konfigurationsinställningarna i det här avsnittet är tillgängliga, använder inte behållaren identifiera Text i det här avsnittet.

## <a name="billing-configuration-setting"></a>Fakturering konfigurationsinställning

Den `Billing` konfigurationsinställning anger URI för visuellt innehåll-resursen i Azure används för att läsa av faktureringsinformation för behållaren. Du måste ange ett värde för den här inställningen och värdet måste vara en giltig slutpunkt URI för en resurs för visuellt innehåll på Azure.

> [!IMPORTANT]
> Den [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), och [ `Eula` ](#eula-configuration-setting) konfigurationsinställningar som används tillsammans och du måste ange giltiga värden för alla tre dem. Annars startar behållaren inte. Läs mer om att använda dessa konfigurationsinställningar för att skapa en instans av en behållare, [fakturering](computer-vision-how-to-install-containers.md#billing).

## <a name="eula-configuration-setting"></a>EULA konfigurationsinställning

Den `Eula` konfigurationsinställning indikerar att du har godkänt licensen för behållaren. Du måste ange ett värde för den här inställningen och värdet måste anges till `accept`.

> [!IMPORTANT]
> Den [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), och [ `Eula` ](#eula-configuration-setting) konfigurationsinställningar som används tillsammans och du måste ange giltiga värden för alla tre dem. Annars startar behållaren inte. Läs mer om att använda dessa konfigurationsinställningar för att skapa en instans av en behållare, [fakturering](computer-vision-how-to-install-containers.md#billing).

Cognitive Services-behållare är licensierad under ditt avtal som reglerar användningen av Azure. Om du inte har en befintlig avtal som reglerar användningen av Azure, accepterar du att ditt avtal som reglerar användningen av Azure är Microsoft Online-prenumerationsavtalet (som införlivar villkoren för Online Services). För förhandsversioner också samtycker du till den kompletterande användningsvillkor för förhandsversioner av Microsoft Azure. Med hjälp av behållaren godkänner du dessa villkor.

## <a name="fluentd-configuration-settings"></a>Fluentd konfigurationsinställningar

Den `Fluentd` avsnittet hanterar konfigurationsinställningar för [Fluentd](https://www.fluentd.org), en öppen källkod för datainsamling för enhetlig loggning. Datorn Vision behållare innehåller en Fluentd loggningsprovider vilket gör att din behållare att skriva log och eventuellt måttdata till en Fluentd-server.

I följande tabell beskrivs de konfigurationsinställningar som stöds den `Fluentd` avsnittet.

| Namn | Datatyp | Beskrivning |
|------|-----------|-------------|
| `Host` | Sträng | IP-adressen eller DNS-värdnamn för Fluentd-servern. |
| `Port` | Integer | Porten som används av Fluentd-server.<br/> Standardvärdet är 24224. |
| `HeartbeatMs` | Integer | Pulsslag intervall i millisekunder. Om ingen händelse trafik har skickats innan det här intervallet upphör att gälla, skickas ett pulsslag till Fluentd-servern. Standardvärdet är 60000 millisekunder (1 minut). |
| `SendBufferSize` | Integer | Buffertutrymme för nätverk i byte som allokerats för skickar åtgärder. Standardvärdet är 32768 byte (32 kB). |
| `TlsConnectionEstablishmentTimeoutMs` | Integer | Tidsgräns i millisekunder för att upprätta en SSL/TLS-anslutning med Fluentd-servern. Standardvärdet är 10 000 millisekunder (10 sekunder).<br/> Om `UseTLS` har angetts till false, ignoreras det här värdet. |
| `UseTLS` | Boolesk | Anger om behållaren ska använda SSL/TLS för att kommunicera med Fluentd-servern. Standardvärdet är FALSKT. |

## <a name="logging-configuration-settings"></a>Inställningar för loggning

Den `Logging` konfigurationsinställningar hantera ASP.NET Core loggningsstöd för din behållare. Du kan använda samma konfigurationsinställningar och värden för din behållare kan du för en ASP.NET Core-program. Följande loggning providers som stöds av behållare för visuellt innehåll:

* [Konsol](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#console-provider)  
  ASP.NET Core `Console` loggningsprovider. Alla konfigurationsinställningar för ASP.NET Core och standardvärden för den här loggningsprovider stöds.
* [Felsökning](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#debug-provider)  
  ASP.NET Core `Debug` loggningsprovider. Alla konfigurationsinställningar för ASP.NET Core och standardvärden för den här loggningsprovider stöds.
* Disk  
  Loggningsprovider JSON. Den här loggningsprovider skriver loggdata till utdata mount.  
  Den `Disk` loggningsprovider stöder följande konfigurationsinställningar:  

  | Namn | Datatyp | Beskrivning |
  |------|-----------|-------------|
  | `Format` | Sträng | Utdataformat för loggfiler.<br/> **Obs:** detta värde måste anges till `json` att aktivera loggning-providern. Ett fel inträffar om det här värdet anges utan att också ange en utdata mount vid instansiering en behållare. |
  | `MaxFileSize` | Integer | Den maximala storleken i megabyte (MB) på en loggfil. När storleken på den aktuella loggfilen uppfyller eller överskrider detta värde, har en ny loggfil startats med loggningsprovider. Om -1 anges begränsas storleken på loggfilen bara av den maximala filstorleken för utdata mount. Standardvärdet är 1. |

Läs mer om hur du konfigurerar ASP.NET Core loggningsstöd [inställningar filkonfiguration](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#settings-file-configuration).

## <a name="mounts-configuration-settings"></a>Monterar konfigurationsinställningar

Docker-behållare tillhandahålls av visuellt innehåll är avsedda att vara tillståndslösa såväl som inte kan ändras. Med andra ord lagras filer som skapas i en behållare i en skrivbar container-lager som kvarstår medan behållaren körs och enkelt kan inte nås. Om den behållaren stoppas eller tas bort, förstörs filerna som skapades i den behållaren med den.

Eftersom de är Docker-behållare, du dock använda Docker-lagringsalternativ, till exempel volymer och Binder monterar, läsa och skriva beständiga data utanför behållaren, om behållaren har stöd för den. Mer information om hur du anger och hantera Docker-lagringsalternativ finns i [hantera data i Docker](https://docs.docker.com/storage/).

> [!NOTE]
> Du behöver normalt inte ändra värdena för dessa konfigurationsinställningar. I stället ska du använda värdena som anges i dessa konfigurationsinställningar som mål när du anger inkommande och utgående monterar för behållaren. Läs mer om hur du anger inkommande och utgående monterar [indata och utdata monterar](#input-and-output-mounts).

I följande tabell beskrivs de konfigurationsinställningar som stöds den `Mounts` avsnittet.

| Namn | Datatyp | Beskrivning |
|------|-----------|-------------|
| `Input` | Sträng | Inkommande monterings-mål. Standardvärdet är `/input`. |
| `Output` | Sträng | Utdata mount-mål. Standardvärdet är `/output`. |

### <a name="input-and-output-mounts"></a>Indata- och monterar

Som standard varje behållare har stöd för en *inkommande montera*, som behållaren kan läsa data, och en *utdata mount*, som behållaren kan skriva data. Behållare är inte krävs för att stödja indata eller utdata monterar, men varje behållare kan använda både inkommande och utgående monterar i container-specifika syften förutom loggningsalternativen stöds av visuellt behållare.

Identifiera Text behållaren har inte stöd för inkommande monterar och du kan också stöder utdata monterar.

Du kan ange en monteringspunkt som indata eller utdata mount genom att ange den `--mount` alternativet i den [docker kör](https://docs.docker.com/engine/reference/commandline/run/) kommando som används för att skapa en instans av en behållare från en hämtade behållaravbildning. Som standard indata montera använder `/input` som dess mål- och utdata montera använder `/output` som mål. Alla Docker-lagringsalternativ för Docker-behållarvärden kan anges i den `--mount` alternativet.

Till exempel följande kommando definierar en Docker-bindning montera den `D:\Output` mapp på värddatorn som utdata mount, sedan instantierar en behållare från behållaravbildningen identifiera Text, spara loggfiler i JSON-format till utdata mount.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 --mount type=bind,source=D:\Output,destination=/output containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/vision/v1.0 ApiKey=0123456789 Logging:Disk:Format=json
  ```
