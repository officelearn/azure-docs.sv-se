---
title: Konfigurera containrar
titlesuffix: Face - Azure Cognitive Services
description: Konfigurationsinställningar för behållare.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: 73fc17ae5c65cd1a6ce47a18cbe17e6c338b7aaf
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2019
ms.locfileid: "58882131"
---
# <a name="configure-face-docker-containers"></a>Konfigurera ansikte Docker-behållare

Den **ansikte** behållare körningsmiljö konfigureras med hjälp av den `docker run` kommandot argument. Den här behållaren har flera inställningar som krävs, tillsammans med några valfria inställningar. Flera [exempel](#example-docker-run-commands) kommandots är tillgängliga. Behållare-specifika inställningar är de fakturering inställningarna. 

## <a name="configuration-settings"></a>Konfigurationsinställningar

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> Den [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), och [ `Eula` ](#eula-setting) inställningar används tillsammans, och du måste ange giltiga värden för alla tre av dem, annars din behållare startar inte. Läs mer om att använda dessa konfigurationsinställningar för att skapa en instans av en behållare, [fakturering](face-how-to-install-containers.md#billing).

## <a name="apikey-configuration-setting"></a>ApiKey konfigurationsinställning

Den `ApiKey` inställningen anger du Azure-resurs-nyckeln som används för att spåra faktureringsinformation för behållaren. Du måste ange ett värde för ApiKey och värdet måste vara en giltig nyckel för den _ansikte_ resurs som angetts för den [ `Billing` ](#billing-configuration-setting) konfigurationsinställning.

Den här inställningen kan hittas på följande plats:

* Azure-portalen: **Ansiktes** resurshantering under **nycklar**

## <a name="applicationinsights-setting"></a>Inställningen för ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Fakturering konfigurationsinställning

Den `Billing` inställningen anger URI för den _ansikte_ resurs på Azure som används för att läsa av faktureringsinformation för behållaren. Du måste ange ett värde för den här inställningen och värdet måste vara en giltig URI-slutpunkt för en _ansikte_ resurs på Azure. Behållaren rapporterar användning ungefär var 10 – 15 minuter.

Den här inställningen kan hittas på följande plats:

* Azure-portalen: **Ansiktes** översikt, märkt `Endpoint`

|Krävs| Namn | Datatyp | Beskrivning |
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
  Ansikts-behållare använder Azure Storage och Azure Cosmos DB för att distribuera dessa fyra typer av data till beständig lagring. BLOB-och kön hanteras av Azure Storage. Metadata och cache hanteras av Azure Cosmos DB. Om Ansikts-behållaren stoppas eller tas bort, förblir alla data i lagring för den behållaren lagras i Azure Storage och Azure Cosmos DB.  
  De resurser som används av Azure storage-scenariot har följande ytterligare krav
  * Azure Storage-resursen måste använda StorageV2-kontotyp
  * Azure Cosmos DB-resursen måste använda Azure Cosmos DB: s API för MongoDB

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

## <a name="http-proxy-credentials-settings"></a>HTTP-proxyinställningarna för autentiseringsuppgifter

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Loggningsinställningar
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Monteringsinställningar

Använd bindning monterar för att läsa och skriva data till och från behållaren. Du kan ange en monteringspunkt som indata eller utdata mount genom att ange den `--mount` alternativet i den [docker kör](https://docs.docker.com/engine/reference/commandline/run/) kommando.

Ansikts-behållare använder inte indata eller utdata monterar för att lagra utbildning eller tjänstdata. 

Den exakta syntaxen hos montera värdplats varierar beroende på värdens operativsystem. Dessutom kan den [värddatorn](face-how-to-install-containers.md#the-host-computer)'s montera platsen är kanske inte tillgänglig på grund av en konflikt mellan behörigheter som används av Docker-tjänstkontot och värden montera plats behörigheter. 

|Valfri| Namn | Datatyp | Beskrivning |
|-------|------|-----------|-------------|
|Inte tillåtet| `Input` | String | Ansikte behållare Använd inte detta.|
|Valfri| `Output` | String | Utdata mount-mål. Standardvärdet är `/output`. Det här är platsen för loggarna. Detta inkluderar behållarloggarna. <br><br>Exempel:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Exempel docker-kommandon 

I följande exempel används konfigurationsinställningarna som illustrerar hur du skriver och använda `docker run` kommandon.  När du kör, behållaren fortsätter att köras tills du [stoppa](face-how-to-install-containers.md#stop-the-container) den.

* **Fortsättning på raden tecknet**: Docker-kommandon i följande avsnitt använder det omvända snedstrecket `\`, som en fortsättning tecknet. Ersätta eller ta bort detta baserat på din värdoperativsystemet. 
* **Argumentet order**: Ändra inte argumentens ordning om du inte är bekant med Docker-behållare.

Ersätt {_argument_name_} med dina egna värden:

| Platshållare | Värde | Format eller exempel |
|-------------|-------|---|
|{BILLING_KEY} | Slutpunktsnyckeln av Ansikts-resurs. |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{BILLING_ENDPOINT_URI} | Fakturering slutpunktsvärdet inklusive region.|`https://westcentralus.api.cognitive.microsoft.com/face/v1.0`|

> [!IMPORTANT]
> Den `Eula`, `Billing`, och `ApiKey` alternativ måste anges för att köra behållaren, i annat fall startar inte behållaren.  Mer information finns i [fakturering](face-how-to-install-containers.md#billing).
> ApiKey-värdet är den **nyckel** från sidan nycklar för Azure Ansikts-resurs. 

## <a name="face-container-docker-examples"></a>Står inför behållare Docker-exempel

I följande exempel Docker är avsedda för ansikts-behållaren. 

### <a name="basic-example"></a>Grundläggande exempel 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-face \
  Eula=accept \
  Billing={BILLING_ENDPOINT_URI} \
  ApiKey={BILLING_KEY} 
  ```

### <a name="logging-example"></a>Exempel för loggning 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face \
  Eula=accept \
  Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>Nästa steg

* Granska [hur du installerar och kör behållare](face-how-to-install-containers.md)
