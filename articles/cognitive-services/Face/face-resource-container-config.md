---
title: Konfigurera containrar
titlesuffix: Face - Azure Cognitive Services
description: Konfigurationsinställningar för behållare.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: diberry
ms.openlocfilehash: 30546d31e96d7d7fa1009f16a50fe8fda12ead67
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53105112"
---
# <a name="configure-containers"></a>Konfigurera containrar

Ansikts-behållare använder ett gemensamt ramverk för konfiguration, så att du enkelt kan konfigurera och hantera inställningar för lagring, loggning och telemetri och säkerhet för dina behållare.

## <a name="configuration-settings"></a>Konfigurationsinställningar

Konfigurationsinställningar i Ansikts-behållaren är hierarkiska alla behållare använder en delad hierarki, baserat på följande toppnivåstruktur:

* [ApiKey](#apikey-configuration-setting)
* [ApplicationInsights](#applicationinsights-configuration-settings)
* [Autentisering](#authentication-configuration-settings)
* [Billing](#billing-configuration-setting)
* [CloudAI](#cloudai-configuration-settings)
* [Licensvillkor](#eula-configuration-setting)
* [Fluentd](#fluentd-configuration-settings)
* [Loggning](#logging-configuration-settings)
* [Monterar](#mounts-configuration-settings)

Du kan använda antingen [miljövariabler](#configuration-settings-as-environment-variables) eller [kommandoradsargument](#configuration-settings-as-command-line-arguments) att ange konfigurationsinställningar för när instanser skapades av en Ansikts-behållare.

Värden för miljövariabler åsidosätta värden för kommandoradsargument, vilket i sin tur åsidosätter standardvärdena för behållaravbildningen. Med andra ord, om du anger olika värden i en miljövariabel och ett argument på kommandoraden för samma konfigurationsinställning som `Logging:Disk:LogLevel`, sedan skapa en instans av en behållare, används värdet i miljövariabeln genom den skapade instanser behållaren.

### <a name="configuration-settings-as-environment-variables"></a>Konfigurationsinställningar som miljövariabler

Du kan använda den [variabeln syntax för ASP.NET Core-miljö](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.1&tabs=basicconfiguration#configuration-by-environment) att ange konfigurationsinställningar.

Behållaren läser miljövariabler för användaren när behållaren instantieras. Om det finns en miljövariabel, åsidosätter värdet för miljövariabeln standardvärdet för den angivna Konfigurationsinställningen. Fördelen med att använda miljövariabler är att flera konfigurationsinställningar kan anges innan kontrollanten behållare och flera behållare kan använda samma uppsättning inställningar automatiskt.

Till exempel följande kommandon använda en miljövariabel för att konfigurera konsolen loggningsnivån till [LogLevel.Information](https://msdn.microsoft.com), sedan skapar en instans av en behållare från Ansikts-behållaravbildning. Värdet för miljövariabeln åsidosätter standardinställningen för konfigurationen.

  ```Docker
  SET Logging:Console:LogLevel=Information
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789
  ```

### <a name="configuration-settings-as-command-line-arguments"></a>Konfigurationsinställningar som kommandoradsargument

Du kan använda den [ASP.NET Core kommandoradsargumentet syntax](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.1&tabs=basicconfiguration#arguments) att ange konfigurationsinställningar.

Du kan ange konfigurationsinställningarna i den valfria `ARGS` -parametern för den [docker kör](https://docs.docker.com/engine/reference/commandline/run/) kommando som används för att skapa en instans av en behållare från en hämtade behållaravbildning. Fördelen med att använda kommandoradsargument är att varje behållare kan använda en annan, anpassad uppsättning konfigurationsinställningar.

Till exempel följande kommando skapar en instans av en behållare från Ansikts-behållaravbildning och konfigurerar konsolen loggningsnivå LogLevel.Information, åsidosätter standardinställningen för konfigurationen.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 Logging:Console:LogLevel=Information
  ```

## <a name="apikey-configuration-setting"></a>ApiKey konfigurationsinställning

Den `ApiKey` konfigurationsinställning anger konfigurationsnyckeln för Ansikts-resurs på Azure som används för att spåra faktureringsinformation för behållaren. Du måste ange ett värde för den här inställningen och värdet måste vara en giltig Konfigurationsnyckel för Ansikts-resursen som angetts för den [ `Billing` ](#billing-configuration-setting) konfigurationsinställning.

> [!IMPORTANT]
> Den [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), och [ `Eula` ](#eula-configuration-setting) konfigurationsinställningar som används tillsammans och du måste ange giltiga värden för alla tre dem. Annars startar behållaren inte. Läs mer om att använda dessa konfigurationsinställningar för att skapa en instans av en behållare, [fakturering](face-how-to-install-containers.md#billing).

## <a name="applicationinsights-configuration-settings"></a>Inställningar för ApplicationInsights

Konfigurationsinställningarna i den `ApplicationInsights` avsnittet kan du lägga till [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) telemetri stöd till behållaren. Application Insights erbjuder djupgående övervakning av behållaren kod-nivå. Du kan enkelt övervaka din behållare för tillgänglighet, prestanda och användning. Du kan också snabbt identifiera och diagnostisera fel i din behållare utan att behöva vänta på att en användare rapporterar dem.

I följande tabell beskrivs de konfigurationsinställningar som stöds den `ApplicationInsights` avsnittet.

| Namn | Datatyp | Beskrivning |
|------|-----------|-------------|
| `InstrumentationKey` | Sträng | Instrumenteringsnyckeln för Application Insights-instans till vilken telemetri skickas data för behållaren. Mer information finns i [Application Insights för ASP.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core). |

## <a name="authentication-configuration-settings"></a>Konfigurationsinställningar för autentisering

Den `Authentication` konfigurationsinställningar tillhandahåller alternativ för Azure-säkerhet för dina behållare. Även om konfigurationsinställningarna i det här avsnittet är tillgängliga, använder inte Ansikts-behållaren i det här avsnittet.

| Namn | Datatyp | Beskrivning |
|------|-----------|-------------|
| `Storage` | Sträng | Instrumenteringsnyckeln för Application Insights-instans till vilken telemetri skickas data för behållaren. Mer information finns i [Application Insights för ASP.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core). |

## <a name="billing-configuration-setting"></a>Fakturering konfigurationsinställning

Den `Billing` konfigurationsinställning anger URI för Ansikts-resursen i Azure används för att läsa av faktureringsinformation för behållaren. Du måste ange ett värde för den här inställningen och värdet måste vara en giltig slutpunkt URI för en Ansikts-resurs på Azure.

> [!IMPORTANT]
> Den [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), och [ `Eula` ](#eula-configuration-setting) konfigurationsinställningar som används tillsammans och du måste ange giltiga värden för alla tre dem. Annars startar behållaren inte. Läs mer om att använda dessa konfigurationsinställningar för att skapa en instans av en behållare, [fakturering](face-how-to-install-containers.md#billing).

## <a name="cloudai-configuration-settings"></a>CloudAI konfigurationsinställningar

Konfigurationsinställningarna i den `CloudAI` avsnittet Ange container-specifika alternativ som är unik för din behållare. Följande inställningar och objekt stöds för Ansikts-behållaren i den `CloudAI` avsnittet

| Namn | Datatyp | Beskrivning |
|------|-----------|-------------|
| `Storage` | Objekt | Lagringsscenariot som används av Ansikts-behållaren. Mer information om storage-scenarier och tillhörande inställningarna för den `Storage` objekt, se [lagringsinställningar för scenario](#storage-scenario-settings) |

### <a name="storage-scenario-settings"></a>Inställningarna för scenario

Ansikts-behållaren lagrar blob, cache, metadata och data i kön, beroende på vad som lagras. Exempelvis lagras utbildning index och resultat för en stor persongrupp som blob-data. Ansikts-behållaren innehåller två olika lagringsscenarier när du interagerar med och lagra dessa typer av data:

* Minne  
  Alla fyra typer av data lagras i minnet. De inte har distribuerats, och inte heller de beständiga. Om Ansikts-behållaren stoppas eller tas bort, förstörs alla data i lagring för den behållaren.  
  Detta är standard storage scenariot för Ansikts-behållaren.
* Azure  
  Ansikts-behållare använder Azure Storage och Azure Cosmos DB för att distribuera dessa fyra typer av data till beständig lagring. BLOB-och kön hanteras av Azure Storage. Metadata och cache hanteras av Azure Cosmos DB med MongoDB-API. Om Ansikts-behållaren stoppas eller tas bort, förblir alla data i lagring för den behållaren lagras i Azure Storage och Azure Cosmos DB.  
  De resurser som används av Azure storage-scenariot har följande ytterligare krav
  * Azure Storage-resursen måste använda StorageV2-kontotyp
  * Azure Cosmos DB-resursen måste använda MongoDB-API

Storage-scenarier och tillhörande konfigurationsinställningar som hanteras av den `Storage` objekt under den `CloudAI` konfigurationsavsnittet. Följande inställningar är tillgängliga i den `Storage` objekt:

| Namn | Datatyp | Beskrivning |
|------|-----------|-------------|
| `StorageScenario` | Sträng | Storage-scenario som stöds av behållaren. Följande värden är tillgängliga<br/>`Memory` -Standardvärde. Behållaren använder icke-beständiga, icke-distribuerade och InMemory-lagring för en nod, tillfälligt användning. Om behållaren stoppas eller tas bort, förstörs lagring för den behållaren.<br/>`Azure` -Behållare använder Azure-resurser för lagring. Om behållaren stoppas eller tas bort, beständig lagring för den behållaren.|
| `ConnectionStringOfAzureStorage` | Sträng | Anslutningssträngen för Azure Storage-resurs som används av behållaren.<br/>Den här inställningen gäller endast om `Azure` har angetts för den `StorageScenario` konfigurationsinställning. |
| `ConnectionStringOfCosmosMongo` | Sträng | MongoDB-anslutningssträng för Azure Cosmos DB-resurs som används av behållaren.<br/>Den här inställningen gäller endast om `Azure` har angetts för den `StorageScenario` konfigurationsinställning. |

Till exempel följande kommando anger Azure storage-scenariot och ger exempel anslutningssträngar för Azure Storage och Cosmos DB-resurser som används för att lagra data för Ansikts-behållaren.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 CloudAI:Storage:StorageScenario=Azure CloudAI:Storage:ConnectionStringOfCosmosMongo="mongodb://samplecosmosdb:0123456789@samplecosmosdb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb" CloudAI:Storage:ConnectionStringOfAzureStorage="DefaultEndpointsProtocol=https;AccountName=sampleazurestorage;AccountKey=0123456789;EndpointSuffix=core.windows.net"
  ```

Storage-scenariot hanteras separat från indata monterar och utdata monterar. Du kan ange en kombination av dessa funktioner för en enskild behållare. Till exempel följande kommando definierar en Docker-bindning montera den `D:\Output` mapp på värddatorn som utdata mount, sedan instantierar en behållare från Ansikts-behållaravbildningen, och spara loggfiler i JSON-format till utdata mount. Kommandot också anger du det Azure storage-scenariot och ger exempel anslutningssträngar för Azure Storage och Cosmos DB-resurser som används för att lagra data för Ansikts-behållaren.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 --mount type=bind,source=D:\Output,destination=/output containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 Logging:Disk:Format=json CloudAI:Storage:StorageScenario=Azure CloudAI:Storage:ConnectionStringOfCosmosMongo="mongodb://samplecosmosdb:0123456789@samplecosmosdb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb" CloudAI:Storage:ConnectionStringOfAzureStorage="DefaultEndpointsProtocol=https;AccountName=sampleazurestorage;AccountKey=0123456789;EndpointSuffix=core.windows.net"
  ```

## <a name="eula-configuration-setting"></a>EULA konfigurationsinställning

Den `Eula` konfigurationsinställning indikerar att du har godkänt licensen för behållaren. Du måste ange ett värde för den här inställningen och värdet måste anges till `accept`.

> [!IMPORTANT]
> Den [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), och [ `Eula` ](#eula-configuration-setting) konfigurationsinställningar som används tillsammans och du måste ange giltiga värden för alla tre dem. Annars startar behållaren inte. Läs mer om att använda dessa konfigurationsinställningar för att skapa en instans av en behållare, [fakturering](face-how-to-install-containers.md#billing).

Cognitive Services-behållare är licensierad under [ditt avtal](https://go.microsoft.com/fwlink/?linkid=2018657) reglerar din användning av Azure. Om du inte har en befintlig avtal som reglerar användningen av Azure kan du godkänner att ditt avtal som reglerar användningen av Azure är den [prenumerationsavtalet för Microsoft Online](https://go.microsoft.com/fwlink/?linkid=2018755) (som införlivar den [villkoren för Online Services ](https://go.microsoft.com/fwlink/?linkid=2018760)). För förhandsversioner godkänner du även den [kompletterande användningsvillkor för förhandsversioner av Microsoft Azure](https://go.microsoft.com/fwlink/?linkid=2018815). Med hjälp av behållaren godkänner du dessa villkor.

## <a name="fluentd-configuration-settings"></a>Fluentd konfigurationsinställningar

Den `Fluentd` avsnittet hanterar konfigurationsinställningar för [Fluentd](https://www.fluentd.org), en öppen källkod för datainsamling för enhetlig loggning. Ansikts-behållaren innehåller en Fluentd loggningsprovider vilket gör att din behållare att skriva log och eventuellt måttdata till en Fluentd-server.

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

Den `Logging` konfigurationsinställningar hantera ASP.NET Core loggningsstöd för din behållare. Du kan använda samma konfigurationsinställningar och värden för din behållare kan du för en ASP.NET Core-program. Följande loggning providers som stöds av Ansikts-behållaren:

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

Docker-behållare tillhandahålls av ansikte är avsedda att vara tillståndslösa såväl som inte kan ändras. Med andra ord lagras filer som skapas i en behållare i en skrivbar container-lager som kvarstår medan behållaren körs och enkelt kan inte nås. Om den behållaren stoppas eller tas bort, förstörs filerna som skapades i den behållaren med den.

Eftersom de är Docker-behållare, du dock använda Docker-lagringsalternativ, till exempel volymer och Binder monterar, läsa och skriva beständiga data utanför behållaren, om behållaren har stöd för den. Mer information om hur du anger och hantera Docker-lagringsalternativ finns i [hantera data i Docker](https://docs.docker.com/storage/).

> [!NOTE]
> Du behöver normalt inte ändra värdena för dessa konfigurationsinställningar. I stället ska du använda värdena som anges i dessa konfigurationsinställningar som mål när du anger inkommande och utgående monterar för behållaren. Läs mer om hur du anger inkommande och utgående monterar [indata och utdata monterar](#input-and-output-mounts).

I följande tabell beskrivs de konfigurationsinställningar som stöds den `Mounts` avsnittet.

| Namn | Datatyp | Beskrivning |
|------|-----------|-------------|
| `Input` | Sträng | Inkommande monterings-mål. Standardvärdet är `/input`. |
| `Output` | Sträng | Utdata mount-mål. Standardvärdet är `/output`. |

### <a name="input-and-output-mounts"></a>Indata- och monterar

Som standard varje behållare har stöd för en *inkommande montera*, som behållaren kan läsa data, och en *utdata mount*, som behållaren kan skriva data. Behållare är inte krävs för att stödja indata eller utdata monterar, men varje behållare kan använda både inkommande och utgående monterar i container-specifika syften förutom loggningsalternativen stöds av Ansikts-behållaren.

Ansikts-behållaren har inte stöd för inkommande monterar och du kan också stöder utdata monterar.

Du kan ange en monteringspunkt som indata eller utdata mount genom att ange den `--mount` alternativet i den [docker kör](https://docs.docker.com/engine/reference/commandline/run/) kommando som används för att skapa en instans av en behållare från en hämtade behållaravbildning. Som standard indata montera använder `/input` som dess mål- och utdata montera använder `/output` som mål. Alla Docker-lagringsalternativ för Docker-behållarvärden kan anges i den `--mount` alternativet.

Till exempel följande kommando definierar en Docker-bindning montera den `D:\Output` mapp på värddatorn som utdata mount, sedan instantierar en behållare från Ansikts-behållaravbildningen, och spara loggfiler i JSON-format till utdata mount.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 --mount type=bind,source=D:\Output,destination=/output containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 Logging:Disk:Format=json
  ```

Ansikts-behållare använder inte indata eller utdata monterar för att lagra utbildning eller databasen. Ansikts-behållare ger i stället storage-scenarier för att hantera data i utbildnings- och databasen. Mer information om hur du använder storage-scenarier finns i [scenariot Lagringsinställningar](#storage-scenario-settings).
