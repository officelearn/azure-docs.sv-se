---
title: Exportera dina Azure IoT-centrala data | Microsoft-dokument
description: Så här exporterar du data från ditt Azure IoT Central-program till Azure Event Hubs, Azure Service Bus och Azure Blob Storage
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 01/30/2019
ms.topic: how-to
ms.service: iot-central
manager: corywink
ms.openlocfilehash: 725c5acf961fffb1fd4cf9bc17e37a5940f871cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157916"
---
# <a name="export-iot-data-to-destinations-in-azure"></a>Exportera IoT-data till destinationer i Azure

*Det här avsnittet gäller administratörer.*

I den här artikeln beskrivs hur du använder funktionen för kontinuerlig dataexport i Azure IoT Central för att exportera dina data till **Azure Event Hubs,** **Azure Service Bus**eller Azure **Blob storage** instances. Data exporteras i JSON-format och kan innehålla telemetri, enhetsinformation och enhetsmallsinformation. Använd exporterade data för:

- Insikter och analyser med varma vägar. Det här alternativet omfattar utlösande av anpassade regler i Azure Stream Analytics, utlösa anpassade arbetsflöden i Azure Logic Apps eller skicka dem via Azure-funktioner som ska omvandlas.
- Kallvägsanalys, till exempel utbildningsmodeller i Azure Machine Learning eller långsiktig trendanalys i Microsoft Power BI.

> [!Note]
> När du aktiverar kontinuerlig dataexport får du bara data från det ögonblicket och framåt. För närvarande kan data inte hämtas under en tid när kontinuerlig dataexport avbröts. Om du vill behålla mer historiska data aktiverar du kontinuerlig dataexport tidigt.

## <a name="prerequisites"></a>Krav

Du måste vara administratör i ditt IoT Central-program eller ha behörigheter för dataexport.

## <a name="set-up-export-destination"></a>Ställ in exportmål

Exportmålet måste finnas innan du konfigurerar din kontinuerliga dataexport.

### <a name="create-event-hubs-namespace"></a>Skapa en namnrymd för Event Hubs

Om du inte har ett befintligt namnområde för eventhubbar att exportera till gör du så här:

1. Skapa ett [nytt namnområde för eventhubbar i Azure-portalen](https://ms.portal.azure.com/#create/Microsoft.EventHub). Du kan läsa mer i [Azure Event Hubs docs](../../event-hubs/event-hubs-create.md).

2. Välj en prenumeration. Du kan exportera data till andra prenumerationer som inte finns i samma prenumeration som ditt IoT Central-program. Du ansluter med hjälp av en anslutningssträng i det här fallet.

3. Skapa en händelsehubb i namnområdet Event Hubs. Gå till ditt namnområde och välj **+ Event Hub** högst upp för att skapa en händelsehubbinstans.

### <a name="create-service-bus-namespace"></a>Skapa servicebussnamnområde

Om du inte har ett befintligt Service Bus-namnområde att exportera till gör du så här:

1. Skapa ett [nytt servicebussnamnområde i Azure-portalen](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5). Du kan läsa mer i [Azure Service Bus-dokument](../../service-bus-messaging/service-bus-create-namespace-portal.md).
2. Välj en prenumeration. Du kan exportera data till andra prenumerationer som inte finns i samma prenumeration som ditt IoT Central-program. Du ansluter med hjälp av en anslutningssträng i det här fallet.

3. Gå till tjänstbussens namnområde och välj **+ Kö** eller **+ Ämne** högst upp för att skapa en kö eller ett ämne att exportera till.

När du väljer Service Bus som exportmål får köerna och ämnena inte ha sessioner eller dubblettidentifiering aktiverat. Om något av dessa alternativ är aktiverat kommer vissa meddelanden inte till din kö eller ditt ämne.

### <a name="create-storage-account"></a>Skapa lagringskonto

Om du inte har ett befintligt Azure Storage-konto att exportera till gör du så här:

1. Skapa ett [nytt lagringskonto i Azure-portalen](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Du kan läsa mer om hur du skapar nya [Azure Blob Storage-konton](https://aka.ms/blobdocscreatestorageaccount) eller [Azure Data Lake Storage v2-lagringskonton](../../storage/blobs/data-lake-storage-quickstart-create-account.md). Dataexport kan bara skriva data till lagringskonton som stöder blockblobar. Följande är en lista över kända kompatibla typer av lagringskonton: 

    |Prestandanivå|Typ av konto|
    |-|-|
    |Standard|Allmänt ändamål V2|
    |Standard|Allmänt ändamål V1|
    |Standard|Blob Storage|
    |Premium|Lagring av blockblob|

2. Skapa en behållare i ditt lagringskonto. Gå till ditt lagringskonto. Under **Blob-tjänst**väljer du **Bläddra blobbar**. Välj **+ Behållare** högst upp om du vill skapa en ny behållare.

## <a name="set-up-continuous-data-export"></a>Ställa in kontinuerlig dataexport

Nu när du har ett mål att exportera data till följer du dessa steg för att ställa in kontinuerlig dataexport.

1. Logga in på ditt IoT Central-program.

2. Välj **Dataexport**i den vänstra rutan .

    > [!Note]
    > Om du inte ser Dataexport i den vänstra rutan har du inte behörighet att konfigurera dataexport i appen. Prata med en administratör för att ställa in dataexport.

3. Välj knappen **+ Ny** längst upp till höger. Välj en av **Azure Event Hubs,** **Azure Service Bus**eller Azure **Blob storage** som mål för din export. Det maximala antalet exporter per ansökan är fem.

    ![Skapa ny kontinuerlig dataexport](media/howto-export-data/new-export-definition.png)

4. I listrutan väljer du **namnområdet Event Hubs,** **Service Bus-namnområde,** **Namnområde för lagringskonto**eller **Ange en anslutningssträng**.

    - Du ser bara namnområden för Lagringskonton, Event Hubs och Service Bus-namnområden i samma prenumeration som ditt IoT Central-program. Om du vill exportera till ett mål utanför den här prenumerationen väljer du **Ange en anslutningssträng** och se steg 5.
    - För appar som skapas med hjälp av den kostnadsfria prisplanen är det enda sättet att konfigurera kontinuerlig dataexport via en anslutningssträng. Appar på den kostnadsfria prisplanen har ingen associerad Azure-prenumeration.

    ![Skapa ny händelsehubb](media/howto-export-data/export-event-hub.png)

5. (Valfritt) Om du väljer **Ange en anslutningssträng**visas en ny ruta där du kan klistra in anslutningssträngen. Så här hämtar du anslutningssträngen för din:
    - Event Hubs eller Service Bus, gå till namnområdet i Azure-portalen.
        - Under **Inställningar**väljer du **Principer för delad åtkomst**
        - Välj **standardrothanageSharedAccessKey** eller skapa en ny
        - Kopiera antingen den primära eller sekundära anslutningssträngen
    - Lagringskonto, gå till lagringskontot i Azure-portalen:
        - Under **Inställningar**väljer du **Access-nycklar**
        - Kopiera antingen anslutningssträngen key1 eller anslutningssträngen key2

6. Välj en händelsenav, kö, ett ämne eller en behållare i listrutan.

7. Under **Data som ska exporteras**väljer du vilka typer av data som ska exporteras genom att ange typen till **På**.

8. Om du vill aktivera kontinuerlig dataexport kontrollerar du att **aktiverad** växlingsknapp är **På**. Välj **Spara**.

9. Efter några minuter visas dina data på den destination du valt.

## <a name="export-contents-and-format"></a>Exportera innehåll och format

Exporterade telemetridata innehåller hela meddelandet som dina enheter skickade till IoT Central, inte bara telemetrivärdena själva. Exporterade enhetsdata innehåller ändringar av egenskaper och metadata för alla enheter, och exporterade enhetsmallar innehåller ändringar i alla enhetsmallar.

För eventhubbar och servicebuss exporteras data i nästan realtid. Uppgifterna finns i brödtextegenskapen och är i JSON-format (se nedan för exempel).

För Blob Storage exporteras data en gång per minut, där varje fil innehåller den batch av ändringar som sedan den senast exporterade filen. Exporterade data placeras i tre mappar i JSON-format. Standardsökvägarna i ditt lagringskonto är:

- Telemetri: _{container}/{app-id}/telemetri/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_
- Enheter: _{container}/{app-id}/devices/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_
- Enhetsmallar: _{container}/{app-id}/deviceTemplates/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_

Du kan bläddra bland de exporterade filerna i Azure-portalen genom att navigera till filen och välja fliken **Redigera blob.**


## <a name="telemetry"></a>Telemetri

För Event Hubs och Service Bus exporteras ett nytt meddelande snabbt efter att IoT Central tar emot meddelandet från en enhet och varje exporterat meddelande innehåller hela meddelandet som enheten skickade i brödtextegenskapen i JSON-format.

För Blob Storage batchas och exporteras meddelanden en gång per minut. De exporterade filerna använder samma format som de meddelandefiler som exporteras av [IoT Hub-meddelanderoutning](../../iot-hub/tutorial-routing.md) till blob-lagring. 

> [!NOTE]
> För Blob Storage, se till att dina `contentType: application/JSON` `contentEncoding:utf-8` enheter `utf-16`skickar `utf-32`meddelanden som har och (eller , ). Mer information om [IoT Hub-dokumentationen](../../iot-hub/iot-hub-devguide-routing-query-syntax.md#message-routing-query-based-on-message-body) finns i dokumentationen till IoT Hub.

Enheten som skickade telemetrin representeras av enhets-ID :t (se följande avsnitt). Om du vill hämta namnen på enheterna exporterar du enhetsdata och korrelerar varje meddelande med hjälp av **connectionDeviceId** som matchar **enhetsmeddelandets enhet.**

Det här är ett exempelmeddelande som tas emot i en händelsenav eller servicebusskö eller ett ämne.

```json
{
  "body":{
    "temp":67.96099945281145,
    "humid":58.51139305465015,
    "pm25":36.91162432340187
  },
  "annotations":{
    "iothub-connection-device-id":"<deviceId>",
    "iothub-connection-auth-method":"{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "iothub-connection-auth-generation-id":"<generationId>",
    "iothub-enqueuedtime":1539381029965,
    "iothub-message-source":"Telemetry",
    "x-opt-sequence-number":25325,
    "x-opt-offset":"<offset>",
    "x-opt-enqueued-time":1539381030200
  },
  "sequenceNumber":25325,
  "enqueuedTimeUtc":"2018-10-12T21:50:30.200Z",
  "offset":"<offset>",
  "properties":{
    "content_type":"application/json",
    "content_encoding":"utf-8"
  }
}
```

Det här är en exempelpost som exporteras till blob-lagring:

```json
{
  "EnqueuedTimeUtc":"2019-09-26T17:46:09.8870000Z",
  "Properties":{

  },
  "SystemProperties":{
    "connectionDeviceId":"<deviceid>",
    "connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "connectionDeviceGenerationId":"637051167384630591",
    "contentType":"application/json",
    "contentEncoding":"utf-8",
    "enqueuedTime":"2019-09-26T17:46:09.8870000Z"
  },
  "Body":{
    "temp":49.91322758395974,
    "humid":49.61214852573155,
    "pm25":25.87332214661367
  }
}
```

## <a name="devices"></a>Enheter

Varje meddelande eller post i en ögonblicksbild representerar en eller flera ändringar i en enhet och dess enhets- och molnegenskaper sedan det senast exporterade meddelandet. Det här omfattar:

- `id`av enheten i IoT Central
- `displayName`av enheten
- Enhetsmall ID i`instanceOf`
- `simulated`flagga, sant om enheten är en simulerad enhet
- `provisioned`flagga, sant om enheten har etablerats
- `approved`flagga, sant om enheten har godkänts för att skicka data
- Egenskapsvärden
- `properties`inklusive enhets- och molnegenskaper

Borttagna enheter exporteras inte. För närvarande finns det inga indikatorer i exporterade meddelanden för borttagna enheter.

För eventhubbar och servicebuss skickas meddelanden som innehåller enhetsdata till din händelsenav eller servicebusskö eller -ämne i nära realtid, som det visas i IoT Central. 

För Blob Storage exporteras en ny ögonblicksbild som innehåller alla ändringar sedan den senast skrivna en gång per minut.

Det här är ett exempelmeddelande om enheter och egenskapsdata i händelsehubben eller Service Bus-kö eller -ämne:

```json
{
  "body":{
    "id": "<device Id>",
    "etag": "<etag>",
    "displayName": "Sensor 1",
    "instanceOf": "<device template Id>",
    "simulated": false,
    "provisioned": true,
    "approved": true,
    "properties": {
        "sensorComponent": {
            "setTemp": "30",
            "fwVersion": "2.0.1",
            "status": { "first": "first", "second": "second" },
            "$metadata": {
                "setTemp": {
                    "desiredValue": "30",
                    "desiredVersion": 3,
                    "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                    "ackVersion": 3
                },
                "fwVersion": { "ackVersion": 3 },
                "status": {
                    "desiredValue": {
                        "first": "first",
                        "second": "second"
                    },
                    "desiredVersion": 2,
                    "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                    "ackVersion": 2
                }
            },
            
        }
    },
    "installDate": { "installDate": "2020-02-01" }
},
  "annotations":{
    "iotcentral-message-source":"devices",
    "x-opt-partition-key":"<partitionKey>",
    "x-opt-sequence-number":39740,
    "x-opt-offset":"<offset>",
    "x-opt-enqueued-time":1539274959654
  },
  "partitionKey":"<partitionKey>",
  "sequenceNumber":39740,
  "enqueuedTimeUtc":"2020-02-01T18:14:49.3820326Z",
  "offset":"<offset>"
}
```

Det här är en exempelögonblicksbild som innehåller enheter och egenskapsdata i Blob Storage. Exporterade filer innehåller en enda rad per post.

```json
{
  "id": "<device Id>",
  "etag": "<etag>",
  "displayName": "Sensor 1",
  "instanceOf": "<device template Id>",
  "simulated": false,
  "provisioned": true,
  "approved": true,
  "properties": {
      "sensorComponent": {
          "setTemp": "30",
          "fwVersion": "2.0.1",
          "status": { "first": "first", "second": "second" },
          "$metadata": {
              "setTemp": {
                  "desiredValue": "30",
                  "desiredVersion": 3,
                  "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                  "ackVersion": 3
              },
              "fwVersion": { "ackVersion": 3 },
              "status": {
                  "desiredValue": {
                      "first": "first",
                      "second": "second"
                  },
                  "desiredVersion": 2,
                  "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                  "ackVersion": 2
              }
          },
          
      }
  },
  "installDate": { "installDate": "2020-02-01" }
}
```

## <a name="device-templates"></a>Enhetsmallar

Varje meddelande eller ögonblicksbildpost representerar en eller flera ändringar i en publicerad enhetsmall sedan det senast exporterade meddelandet. Information som skickas i varje meddelande eller post omfattar:

- `id`av enhetsmallen `instanceOf` som matchar av enheternas ström ovan
- `displayName`av enhetsmallen
- Enheten `capabilityModel` inklusive `interfaces`dess , och telemetri, egenskaper och kommandon definitioner
- `cloudProperties`Definitioner
- Åsidosättningar och initiala värden, infogade med`capabilityModel`

Borttagna enhetsmallar exporteras inte. För närvarande finns det inga indikatorer i exporterade meddelanden för borttagna enhetsmallar.

För eventhubbar och servicebuss skickas meddelanden som innehåller enhetsmalldata till din händelsenav eller servicebusskö eller -ämne i nära realtid, som det visas i IoT Central. 

För Blob Storage exporteras en ny ögonblicksbild som innehåller alla ändringar sedan den senast skrivna en gång per minut.

Det här är ett exempelmeddelande om enhetsmallar data i händelsehubben eller Service Bus-kö eller ämne:

```json
{
  "body":{
      "id": "<device template id>",
      "etag": "<etag>",
      "types": ["DeviceModel"],
      "displayName": "Sensor template",
      "capabilityModel": {
          "@id": "<capability model id>",
          "@type": ["CapabilityModel"],
          "contents": [],
          "implements": [
              {
                  "@id": "<component Id>",
                  "@type": ["InterfaceInstance"],
                  "name": "sensorComponent",
                  "schema": {
                      "@id": "<interface Id>",
                      "@type": ["Interface"],
                      "displayName": "Sensor interface",
                      "contents": [
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry"],
                              "displayName": "Humidity",
                              "name": "humidity",
                              "schema": "double"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry", "SemanticType/Event"],
                              "displayName": "Error event",
                              "name": "error",
                              "schema": "integer"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Set temperature",
                              "name": "setTemp",
                              "writable": true,
                              "schema": "integer",
                              "unit": "Units/Temperature/fahrenheit",
                              "initialValue": "30"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Firmware version read only",
                              "name": "fwversion",
                              "schema": "string"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Display status",
                              "name": "status",
                              "writable": true,
                              "schema": {
                                  "@id": "urn:testInterface:status:obj:ka8iw8wka:1",
                                  "@type": ["Object"]
                              }
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Command"],
                              "commandType": "synchronous",
                              "request": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Configuration",
                                  "name": "config",
                                  "schema": "string"
                              },
                              "response": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Response",
                                  "name": "response",
                                  "schema": "string"
                              },
                              "displayName": "Configure sensor",
                              "name": "sensorConfig"
                          }
                      ]
                  }
              }
          ],
          "displayName": "Sensor capability model"
      },
      "solutionModel": {
          "@id": "<id>",
          "@type": ["SolutionModel"],
          "cloudProperties": [
              {
                  "@id": "<id>",
                  "@type": ["CloudProperty"],
                  "displayName": "Install date",
                  "name": "installDate",
                  "schema": "dateTime",
                  "valueDetail": {
                      "@id": "<id>",
                      "@type": ["ValueDetail/DateTimeValueDetail"]
                  }
              }
          ]
      }
  },
    "annotations":{
      "iotcentral-message-source":"deviceTemplates",
      "x-opt-partition-key":"<partitionKey>",
      "x-opt-sequence-number":25315,
      "x-opt-offset":"<offset>",
      "x-opt-enqueued-time":1539274985085
    },
    "partitionKey":"<partitionKey>",
    "sequenceNumber":25315,
    "enqueuedTimeUtc":"2019-10-02T16:23:05.085Z",
    "offset":"<offset>"
  }
}
```

Det här är en exempelögonblicksbild som innehåller enheter och egenskapsdata i Blob Storage. Exporterade filer innehåller en enda rad per post.

```json
{
      "id": "<device template id>",
      "etag": "<etag>",
      "types": ["DeviceModel"],
      "displayName": "Sensor template",
      "capabilityModel": {
          "@id": "<capability model id>",
          "@type": ["CapabilityModel"],
          "contents": [],
          "implements": [
              {
                  "@id": "<component Id>",
                  "@type": ["InterfaceInstance"],
                  "name": "Sensor component",
                  "schema": {
                      "@id": "<interface Id>",
                      "@type": ["Interface"],
                      "displayName": "Sensor interface",
                      "contents": [
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry"],
                              "displayName": "Humidity",
                              "name": "humidity",
                              "schema": "double"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry", "SemanticType/Event"],
                              "displayName": "Error event",
                              "name": "error",
                              "schema": "integer"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Set temperature",
                              "name": "setTemp",
                              "writable": true,
                              "schema": "integer",
                              "unit": "Units/Temperature/fahrenheit",
                              "initialValue": "30"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Firmware version read only",
                              "name": "fwversion",
                              "schema": "string"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Display status",
                              "name": "status",
                              "writable": true,
                              "schema": {
                                  "@id": "urn:testInterface:status:obj:ka8iw8wka:1",
                                  "@type": ["Object"]
                              }
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Command"],
                              "commandType": "synchronous",
                              "request": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Configuration",
                                  "name": "config",
                                  "schema": "string"
                              },
                              "response": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Response",
                                  "name": "response",
                                  "schema": "string"
                              },
                              "displayName": "Configure sensor",
                              "name": "sensorconfig"
                          }
                      ]
                  }
              }
          ],
          "displayName": "Sensor capability model"
      },
      "solutionModel": {
          "@id": "<id>",
          "@type": ["SolutionModel"],
          "cloudProperties": [
              {
                  "@id": "<id>",
                  "@type": ["CloudProperty"],
                  "displayName": "Install date",
                  "name": "installDate",
                  "schema": "dateTime",
                  "valueDetail": {
                      "@id": "<id>",
                      "@type": ["ValueDetail/DateTimeValueDetail"]
                  }
              }
          ]
      }
  }
```
## <a name="data-format-change-notice"></a>Meddelande om ändring av dataformat

> [!Note]
> Dataformatet telemetriström påverkas inte av den här ändringen. Endast enheter och enhetsmallar som strömmar av data påverkas.

Om du har aktiverat en befintlig dataexport i förhandsgranskningsprogrammet med mallarna *Enheter* och *enhetsmallar* aktiverade måste du uppdatera exporten senast den **30 juni 2020**. Detta gäller för export till Azure Blob Storage, Azure Event Hubs och Azure Service Bus.

Från och med den 3 februari 2020 har all ny export i program med mallar med enheter och enheter aktiverade dataformatet som beskrivs ovan. All export som skapas före detta kommer att finnas kvar i det gamla dataformatet fram till den 30 juni 2020, varefter denna export automatiskt kommer att migreras till det nya dataformatet. Det nya dataformatet matchar [enheten,](https://docs.microsoft.com/rest/api/iotcentral/devices/get) [enhetsegenskapen,](https://docs.microsoft.com/rest/api/iotcentral/devices/getproperties) [enhetsmolnegenskapen](https://docs.microsoft.com/rest/api/iotcentral/devices/getcloudproperties) och [enhetsmallobjekten](https://docs.microsoft.com/rest/api/iotcentral/devicetemplates/get) i IoT Central public API. 
 
För **Enheter**är anmärkningsvärda skillnader mellan det gamla dataformatet och det nya dataformatet:
- `@id`för enheten tas `deviceId` bort, byter namn till`id` 
- `provisioned`flagga läggs till för att beskriva enhetens etableringsstatus
- `approved`flagga läggs till för att beskriva godkännandetillståndet för enheten
- `properties`inklusive enhets- och molnegenskaper, matchar entiteter i det offentliga API:et

För **enhetsmallar**är anmärkningsvärda skillnader mellan det gamla dataformatet och det nya dataformatet:

- `@id`för enhetsmallen har bytt namn till`id`
- `@type`för enhetsmallen har `types`bytt namn till , och är nu en matris

### <a name="devices-format-deprecated-as-of-3-february-2020"></a>Enheter (format inaktuella från och med den 3 februari 2020)
```json
{
  "@id":"<id-value>",
  "@type":"Device",
  "displayName":"Airbox",
  "data":{
    "$cloudProperties":{
        "Color":"blue"
    },
    "EnvironmentalSensor":{
      "thsensormodel":{
        "reported":{
          "value":"Neque quia et voluptatem veritatis assumenda consequuntur quod.",
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      },
      "pm25sensormodel":{
        "reported":{
          "value":"Aut alias odio.",
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      }
    },
    "urn_azureiot_DeviceManagement_DeviceInformation":{
      "totalStorage":{
        "reported":{
          "value":27900.9730905171,
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      },
      "totalMemory":{
        "reported":{
          "value":4667.82916715811,
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      }
    }
  },
  "instanceOf":"<template-id>",
  "deviceId":"<device-id>",
  "simulated":true
}
```

### <a name="device-templates-format-deprecated-as-of-3-february-2020"></a>Enhetsmallar (format inaktuella från och med den 3 februari 2020)
```json
{
  "@id":"<template-id>",
  "@type":"DeviceModelDefinition",
  "displayName":"Airbox",
  "capabilityModel":{
    "@id":"<id>",
    "@type":"CapabilityModel",
    "implements":[
      {
        "@id":"<id>",
        "@type":"InterfaceInstance",
        "name":"EnvironmentalSensor",
        "schema":{
          "@id":"<id>",
          "@type":"Interface",
          "comment":"Requires temperature and humidity sensors.",
          "description":"Provides functionality to report temperature, humidity. Provides telemetry, commands and read-write properties",
          "displayName":"Environmental Sensor",
          "contents":[
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current temperature on the device",
              "displayName":"Temperature",
              "name":"temp",
              "schema":"double",
              "unit":"Units/Temperature/celsius",
              "valueDetail":{
                "@id":"<id>",
                "@type":"ValueDetail/NumberValueDetail",
                "minValue":{
                  "@value":"50"
                }
              },
              "visualizationDetail":{
                "@id":"<id>",
                "@type":"VisualizationDetail"
              }
            },
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current humidity on the device",
              "displayName":"Humidity",
              "name":"humid",
              "schema":"integer"
            },
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current PM2.5 on the device",
              "displayName":"PM2.5",
              "name":"pm25",
              "schema":"integer"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "description":"T&H Sensor Model Name",
              "displayName":"T&H Sensor Model",
              "name":"thsensormodel",
              "schema":"string"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "description":"PM2.5 Sensor Model Name",
              "displayName":"PM2.5 Sensor Model",
              "name":"pm25sensormodel",
              "schema":"string"
            }
          ]
        }
      },
      {
        "@id":"<id>",
        "@type":"InterfaceInstance",
        "name":"urn_azureiot_DeviceManagement_DeviceInformation",
        "schema":{
          "@id":"<id>",
          "@type":"Interface",
          "displayName":"Device information",
          "contents":[
            {
              "@id":"<id>",
              "@type":"Property",
              "comment":"Total available storage on the device in kilobytes. Ex. 20480000 kilobytes.",
              "displayName":"Total storage",
              "name":"totalStorage",
              "displayUnit":"kilobytes",
              "schema":"long"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "comment":"Total available memory on the device in kilobytes. Ex. 256000 kilobytes.",
              "displayName":"Total memory",
              "name":"totalMemory",
              "displayUnit":"kilobytes",
              "schema":"long"
            }
          ]
        }
      }
    ],
    "displayName":"AAEONAirbox52"
  },
  "solutionModel":{
    "@id":"<id>",
    "@type":"SolutionModel",
    "cloudProperties":[
      {
        "@id":"<id>",
        "@type":"CloudProperty",
        "displayName":"Color",
        "name":"Color",
        "schema":"string",
        "valueDetail":{
          "@id":"<id>",
          "@type":"ValueDetail/StringValueDetail"
        },
        "visualizationDetail":{
          "@id":"<id>",
          "@type":"VisualizationDetail"
        }
      }
    ]
  }
}
```
## <a name="next-steps"></a>Nästa steg

Nu när du vet hur du exporterar dina data till Azure Event Hubs, Azure Service Bus och Azure Blob Storage fortsätter du till nästa steg:

> [!div class="nextstepaction"]
> [Så här skapar du webhooks](./howto-create-webhooks.md)
