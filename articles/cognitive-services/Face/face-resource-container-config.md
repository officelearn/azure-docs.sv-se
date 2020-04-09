---
title: Konfigurera behållare - Ansikte
titleSuffix: Azure Cognitive Services
description: Face-behållarens körningsmiljö är `docker run` konfigurerad med hjälp av kommandoargumenten. Det finns både obligatoriska och valfria inställningar.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 2f608843e27b79d02697df8e2a7f2aba6695e10a
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878433"
---
# <a name="configure-face-docker-containers"></a>Konfigurera Face Docker-behållare

Face-behållarens körningsmiljö är `docker run` konfigurerad med hjälp av kommandoargumenten. **Face** Den här behållaren har flera nödvändiga inställningar, tillsammans med några valfria inställningar. Flera [exempel på](#example-docker-run-commands) kommandot är tillgängliga. De behållarspecifika inställningarna är faktureringsinställningarna. 

## <a name="configuration-settings"></a>Konfigurationsinställningar

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [`ApiKey`](#apikey-configuration-setting)Inställningarna [`Billing`](#billing-configuration-setting), [`Eula`](#eula-setting) och används tillsammans och du måste ange giltiga värden för alla tre. Annars startar inte behållaren. Mer information om hur du använder de här konfigurationsinställningarna för att instansiera en behållare finns i [Fakturering](face-how-to-install-containers.md#billing).

## <a name="apikey-configuration-setting"></a>Konfigurationsinställning för ApiKey

Inställningen `ApiKey` anger den Azure-resursnyckel som används för att spåra faktureringsinformation för behållaren. Du måste ange ett värde för ApiKey och värdet måste vara en giltig [`Billing`](#billing-configuration-setting) nyckel för cognitive services-resursen som angetts för konfigurationsinställningen. _Cognitive Services_

Den här inställningen finns på följande plats:

* Azure-portal: **Resurshantering för Kognitiva tjänster** under **Nycklar**

## <a name="applicationinsights-setting"></a>Inställning av ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Konfigurationsinställning för fakturering

Inställningen `Billing` anger slutpunkts-URI för _Cognitive Services-resursen_ på Azure som används för att mäta faktureringsinformation för behållaren. Du måste ange ett värde för den här konfigurationsinställningen och värdet måste vara en giltig slutpunkts-URI för en _Cognitive Services-resurs_ på Azure. Behållaren rapporterar användning ungefär var 10 till 15:e minut.

Den här inställningen finns på följande plats:

* Azure-portal: Översikt över **Kognitiva tjänster,** märkt`Endpoint`

Kom ihåg _Face_ att lägga till ansiktsdirigeringen i slutpunkts-URI:n enligt exemplet. 

|Krävs| Namn | Datatyp | Beskrivning |
|--|------|-----------|-------------|
|Ja| `Billing` | Sträng | Fakturering slutpunkt URI. Mer information om hur du hämtar fakturerings-URI finns i [samla in obligatoriska parametrar](face-how-to-install-containers.md#gathering-required-parameters). Mer information och en fullständig lista över regionala slutpunkter finns i [Anpassade underdomännamn för Cognitive Services](../cognitive-services-custom-subdomains.md). |

<!-- specific to face only -->

## <a name="cloudai-configuration-settings"></a>Konfigurationsinställningar för CloudAI

Konfigurationsinställningarna `CloudAI` i avsnittet innehåller behållarspecifika alternativ som är unika för din behållare. Följande inställningar och objekt stöds för Ansiktsbehållaren i avsnittet `CloudAI`

| Namn | Datatyp | Beskrivning |
|------|-----------|-------------|
| `Storage` | Objekt | Lagringsscenariot som används av Ansiktsbehållaren. Mer information om lagringsscenarier `Storage` och associerade inställningar för objektet finns i Inställningar för [lagringsscenario](#storage-scenario-settings) |

### <a name="storage-scenario-settings"></a>Inställningar för lagringsscenario

Ansiktsbehållaren lagrar blob-, cache-, metadata- och ködata, beroende på vad som lagras. Till exempel lagras träningsindex och resultat för en stor persongrupp som blobdata. Face-behållaren innehåller två olika lagringsscenarier när du interagerar med och lagrar dessa typer av data:

* Minne  
  Alla fyra typer av data lagras i minnet. De är inte distribuerade, inte heller är de envisa. Om Ansiktsbehållaren stoppas eller tas bort förstörs alla data som lagras för den behållaren.  
  Det här är standardlagringsscenariot för Ansiktsbehållaren.
* Azure  
  Face-behållaren använder Azure Storage och Azure Cosmos DB för att distribuera dessa fyra typer av data över beständig lagring. Blob- och ködata hanteras av Azure Storage. Metadata och cachedata hanteras av Azure Cosmos DB. Om Face-behållaren stoppas eller tas bort lagras alla data i lagring för den behållaren i Azure Storage och Azure Cosmos DB.  
  De resurser som används av Azure-lagringsscenariot har följande ytterligare krav
  * Azure Storage-resursen måste använda storagev2-kontosd:n
  * Azure Cosmos DB-resursen måste använda Azure Cosmos DB:s API för MongoDB

Lagringsscenarier och associerade konfigurationsinställningar hanteras av `Storage` objektet under konfigurationsavsnittet. `CloudAI` Följande konfigurationsinställningar är `Storage` tillgängliga i objektet:

| Namn | Datatyp | Beskrivning |
|------|-----------|-------------|
| `StorageScenario` | Sträng | Lagringsscenariot som stöds av behållaren. Följande värden är tillgängliga<br/>`Memory`- Standardvärde. Behållaren använder icke-beständig, icke-distribuerad och minnesbaserad lagring, för tillfällig användning med enkel nod. Om behållaren stoppas eller tas bort förstörs lagringen för den behållaren.<br/>`Azure`- Behållaren använder Azure-resurser för lagring. Om behållaren stoppas eller tas bort sparas lagringen för den behållaren.|
| `ConnectionStringOfAzureStorage` | Sträng | Anslutningssträngen för Azure Storage-resursen som används av behållaren.<br/>Den här inställningen `Azure` gäller endast `StorageScenario` om anges för konfigurationsinställningen. |
| `ConnectionStringOfCosmosMongo` | Sträng | MongoDB-anslutningssträngen för Azure Cosmos DB-resursen som används av behållaren.<br/>Den här inställningen `Azure` gäller endast `StorageScenario` om anges för konfigurationsinställningen. |

Följande kommando anger till exempel Azure-lagringsscenariot och tillhandahåller exempelanslutningssträngar för Azure Storage- och Cosmos DB-resurser som används för att lagra data för Face-behållaren.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 CloudAI:Storage:StorageScenario=Azure CloudAI:Storage:ConnectionStringOfCosmosMongo="mongodb://samplecosmosdb:0123456789@samplecosmosdb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb" CloudAI:Storage:ConnectionStringOfAzureStorage="DefaultEndpointsProtocol=https;AccountName=sampleazurestorage;AccountKey=0123456789;EndpointSuffix=core.windows.net"
  ```

Lagringsscenariot hanteras separat från indatafästen och utdatafästen. Du kan ange en kombination av dessa funktioner för en enda behållare. Följande kommando definierar till exempel en Docker-bindningsfäste till `D:\Output` mappen på värddatorn som utdatafästet och instansierar sedan en behållare från ansiktsbehållarens avbildning, vilket sparar loggfiler i JSON-format till utdatafästet. Kommandot anger också Azure-lagringsscenariot och tillhandahåller exempelanslutningssträngar för Azure Storage- och Cosmos DB-resurser som används för att lagra data för Face-behållaren.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 --mount type=bind,source=D:\Output,destination=/output containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 Logging:Disk:Format=json CloudAI:Storage:StorageScenario=Azure CloudAI:Storage:ConnectionStringOfCosmosMongo="mongodb://samplecosmosdb:0123456789@samplecosmosdb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb" CloudAI:Storage:ConnectionStringOfAzureStorage="DefaultEndpointsProtocol=https;AccountName=sampleazurestorage;AccountKey=0123456789;EndpointSuffix=core.windows.net"
  ```

## <a name="eula-setting"></a>Eula-inställning

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Flytande inställningar

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Inställningar för http-proxyautentiseringsuppgifter

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Loggningsinställningar
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Montera inställningar

Använd bindningsfästen för att läsa och skriva data till och från behållaren. Du kan ange ett indatafäste eller `--mount` utdatafäste genom att ange alternativet i [dockerkörningskommandot.](https://docs.docker.com/engine/reference/commandline/run/)

Ansiktsbehållarna använder inte indata- eller utdatafästen för att lagra tränings- eller servicedata. 

Den exakta syntaxen för värdmonteringsplatsen varierar beroende på värdoperativsystemet. Dessutom kanske [värddatorns](face-how-to-install-containers.md#the-host-computer)monteringsplats inte är tillgänglig på grund av en konflikt mellan behörigheter som används av Docker-tjänstkontot och värdfästeplatsbehörigheterna. 

|Valfri| Namn | Datatyp | Beskrivning |
|-------|------|-----------|-------------|
|Inte tillåten| `Input` | Sträng | Ansiktsbehållare använder inte detta.|
|Valfri| `Output` | Sträng | Målet för utmatningsfästet. Standardvärdet är `/output`. Det här är platsen för loggarna. Detta inkluderar behållarloggar. <br><br>Exempel:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Exempel på dockerkörningskommandon 

I följande exempel används konfigurationsinställningarna för `docker run` att illustrera hur du skriver och använder kommandon.  När den körs fortsätter behållaren att köras tills du [stoppar](face-how-to-install-containers.md#stop-the-container) den.

* **Rad-fortsättning**tecken : Docker kommandon i följande avsnitt använder `\`tillbaka snedstreck, , som en rad fortsättning tecken. Ersätt eller ta bort detta baserat på värdoperativsystemets krav. 
* **Argumentordning**: Ändra inte ordningen på argumenten om du inte är väl bekant med Docker-behållare.

Ersätt {_argument_name_} med dina egna värderingar:

| Platshållare | Värde | Format eller exempel |
|-------------|-------|---|
| **{API_KEY}** | Slutpunktsnyckeln `Face` för resursen `Face` på sidan Azure Keys. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | Värdet för faktureringsslutpunkt är `Face` tillgängligt på sidan Azure Overview.| Se [samla in nödvändiga parametrar](face-how-to-install-containers.md#gathering-required-parameters) för explicita exempel. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> Alternativen `Eula` `Billing`, `ApiKey` och måste anges för att behållaren ska kunna köras. Annars startar inte behållaren.  Mer information finns i [Fakturering](face-how-to-install-containers.md#billing).
> ApiKey-värdet är **nyckeln** från `Cognitive Services` sidan Azure Resource keys. 

## <a name="face-container-docker-examples"></a>Exempel på docker för ansiktsbehållare

Följande Docker-exempel är för ansiktsbehållaren. 

### <a name="basic-example"></a>Grundläggande exempel 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-face \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example"></a>Exempel på loggning 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face \
  Eula=accept \
  Billing={ENDPOINT_URI} ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>Nästa steg

* Granska [Hur du installerar och kör behållare](face-how-to-install-containers.md)
