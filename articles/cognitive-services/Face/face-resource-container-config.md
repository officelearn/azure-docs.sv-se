---
title: Konfigurera behållare – ansikts-API
titleSuffix: Azure Cognitive Services
description: Konfigurationsinställningar för behållare.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: dapine
ms.openlocfilehash: 752613becb92711866d520e6fcd46ed3a320353f
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860265"
---
# <a name="configure-face-docker-containers"></a>Konfigurera ansikts Docker-behållare

Runtime-miljön för **ansikts** behållare konfigureras med `docker run` hjälp av kommando argumenten. Den här behållaren har flera inställningar som krävs, tillsammans med några valfria inställningar. Flera [exempel](#example-docker-run-commands) kommandots är tillgängliga. De behållar-/regionsspecifika inställningarna är fakturerings inställningarna. 

## <a name="configuration-settings"></a>Konfigurationsinställningar

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> Den [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), och [ `Eula` ](#eula-setting) inställningar används tillsammans, och du måste ange giltiga värden för alla tre av dem, annars din behållare startar inte. Läs mer om att använda dessa konfigurationsinställningar för att skapa en instans av en behållare, [fakturering](face-how-to-install-containers.md#billing).

## <a name="apikey-configuration-setting"></a>ApiKey konfigurationsinställning

Den `ApiKey` inställningen anger du Azure-resurs-nyckeln som används för att spåra faktureringsinformation för behållaren. Du måste ange ett värde för ApiKey och värdet måste vara en giltig nyckel för den _Cognitive Services_ resurs som angetts för [`Billing`](#billing-configuration-setting) konfigurations inställningen.

Du hittar den här inställningen på följande plats:

* Azure-portalen: **Cognitive Services** Resurs hantering, under **nycklar**

## <a name="applicationinsights-setting"></a>Inställningen för ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Fakturering konfigurationsinställning

Inställningen anger slut punkts-URI för den Cognitive Services resursen på Azure som används för att mäta fakturerings information för behållaren. `Billing` Du måste ange ett värde för den här konfigurations inställningen och värdet måste vara en giltig slut punkts-URI för en _Cognitive Services_ -resurs på Azure. Behållar rapporteringen visar var 10 till 15: e minut.

Du hittar den här inställningen på följande plats:

* Azure-portalen: **Cognitive Services** Översikt, märkt`Endpoint`

Kom ihåg att lägga till _ansikts_ dirigering till slut punkts-URI: n som visas i exemplet. 

|Obligatorisk| Namn | Datatyp | Beskrivning |
|--|------|-----------|-------------|
|Ja| `Billing` | Sträng | Fakturering endpoint URI<br><br>Exempel:<br>`Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0` |

<!-- specific to face only -->

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
  Ansikts-behållare använder Azure Storage och Azure Cosmos DB för att distribuera dessa fyra typer av data till beständig lagring. BLOB-och kön hanteras av Azure Storage. Metadata och cachelagrade data hanteras av Azure Cosmos DB. Om Ansikts-behållaren stoppas eller tas bort, förblir alla data i lagring för den behållaren lagras i Azure Storage och Azure Cosmos DB.  
  De resurser som används av Azure storage-scenariot har följande ytterligare krav
  * Azure Storage-resursen måste använda StorageV2-kontotyp
  * Den Azure Cosmos DB resursen måste använda Azure Cosmos DBs API för MongoDB

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

Ansikts behållare använder inte indata eller utdata monteras för att lagra utbildning eller tjänst data. 

Den exakta syntaxen hos montera värdplats varierar beroende på värdens operativsystem. Dessutom kanske [värd datorns](face-how-to-install-containers.md#the-host-computer)monterings plats inte är tillgänglig på grund av en konflikt mellan behörigheter som används av Docker-tjänstkontot och värd monterings platsens behörigheter. 

|Valfritt| Name | Datatyp | Beskrivning |
|-------|------|-----------|-------------|
|Inte tillåtet| `Input` | Sträng | Ansikts behållare använder inte detta.|
|Valfritt| `Output` | Sträng | Utdata mount-mål. Standardvärdet är `/output`. Det här är platsen för loggarna. Detta inkluderar behållar loggar. <br><br>Exempel:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Exempel docker-kommandon 

I följande exempel används konfigurationsinställningarna som illustrerar hur du skriver och använda `docker run` kommandon.  När du kör, behållaren fortsätter att köras tills du [stoppa](face-how-to-install-containers.md#stop-the-container) den.

* **Rad fortsättnings avstånd**: Docker- `\`kommandona i följande avsnitt använder omvänt snedstreck, som ett fortsättnings streck. Ersätta eller ta bort detta baserat på din värdoperativsystemet. 
* **Argument ordning**: Ändra inte ordningen på argumenten om du inte är bekant med Docker-behållare.

Ersätt {_argument_name_} med dina egna värden:

| Platshållare | Värde | Format eller exempel |
|-------------|-------|---|
|{API_KEY} | Cognitive Services resursens slut punkts nyckel. |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{ENDPOINT_URI} | Slut punktens URL-värde.|`https://myresourcename.cognitive.microsoft.com/face/v1.0`|

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> Den `Eula`, `Billing`, och `ApiKey` alternativ måste anges för att köra behållaren, i annat fall startar inte behållaren.  Mer information finns i [fakturering](face-how-to-install-containers.md#billing).
> ApiKey-värdet är **nyckeln** på sidan med Azures `Cognitive Services` resurs nycklar. 

## <a name="face-container-docker-examples"></a>Docker-exempel för ansikts behållare

Följande Docker-exempel är för ansikts behållaren. 

### <a name="basic-example"></a>Grundläggande exempel 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-face \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example"></a>Loggnings exempel 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face \
  Eula=accept \
  Billing={ENDPOINT_URI} ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>Nästa steg

* Granska [hur du installerar och kör behållare](face-how-to-install-containers.md)
