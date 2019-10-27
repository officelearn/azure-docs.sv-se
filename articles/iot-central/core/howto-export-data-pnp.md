---
title: Exportera dina Azure IoT Central-data | Microsoft Docs
description: Så här exporterar du data från ditt Azure IoT Central-program till Azure Event Hubs, Azure Service Bus och Azure Blob Storage
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 10/15/2019
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: bc78f11d4f61c46e2ad4f7143fe8b3af6762b0b4
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2019
ms.locfileid: "72950423"
---
# <a name="export-your-azure-iot-central-datapreview-features"></a>Exportera dina Azure IoT Central-data (för hands versions funktioner)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

*Det här avsnittet gäller för administratörer.*

Den här artikeln beskriver hur du använder funktionen för kontinuerlig data export i Azure IoT Central för att exportera data till Azure Event Hubs, Azure Service Bus eller Azure Blob Storage-instanser. Data exporteras i JSON-format och kan innehålla telemetri, enhets information och information om enhets mal len. Använd exporterade data för:

- Insikter och analys av varma sökvägar. Det här alternativet inkluderar utlöser anpassade regler i Azure Stream Analytics, utlöser anpassade arbets flöden i Azure Logic Apps eller skickar dem via Azure Functions som ska omvandlas.
- Kall vägs analys, till exempel utbildnings modeller i Azure Machine Learning eller långsiktig trend analys i Microsoft Power BI.

> [!Note]
> När du aktiverar kontinuerlig data export får du bara data från det här tillfället. För närvarande går det inte att hämta data under en tid då kontinuerliga data exporter ATS. Aktivera kontinuerlig data export tidigt om du vill behålla mer historiska data.

## <a name="prerequisites"></a>Krav

Du måste vara administratör i IoT Centrals programmet

## <a name="set-up-export-destination"></a>Konfigurera export mål

Export målet måste finnas innan du konfigurerar din kontinuerliga data export.

### <a name="create-event-hubs-namespace"></a>Skapa Event Hubs namn område

Följ dessa steg om du inte har ett befintligt Event Hubs namn område att exportera till:

1. Skapa ett [nytt Event Hubs-namnområde i Azure Portal](https://ms.portal.azure.com/#create/Microsoft.EventHub). Du kan läsa mer i [Azure Event Hubs-dokument](../../event-hubs/event-hubs-create.md).

2. Välj en prenumeration. Du kan exportera data till andra prenumerationer som inte ingår i samma prenumeration som du IoT Central programmet betala per användning. Du ansluter med hjälp av en anslutnings sträng i det här fallet.

3. Skapa en Event Hub i Event Hubs namn området. Gå till ditt namn område och välj **+ Event Hub** överst för att skapa en Event Hub-instans.

### <a name="create-service-bus-namespace"></a>Skapa Service Bus namn område

Följ dessa steg om du inte har ett befintligt Service Bus namn område att exportera till:

1. Skapa ett [nytt Service Bus-namnområde i Azure Portal](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5). Du kan läsa mer i [Azure Service Bus dokument](../../service-bus-messaging/service-bus-create-namespace-portal.md).
2. Välj en prenumeration. Du kan exportera data till andra prenumerationer som inte ingår i samma prenumeration som du IoT Central programmet betala per användning. Du ansluter med hjälp av en anslutnings sträng i det här fallet.

3. Gå till din Service Bus namnrymd och välj **+ kö** eller **+ ämne** överst för att skapa en kö eller ett ämne att exportera till.

När du väljer Service Bus som export mål får inte köer och ämnen ha sessioner eller dubblettidentifiering aktiverade. Om något av dessa alternativ är aktiverat kommer vissa meddelanden inte att tas emot i din kö eller ämne.

### <a name="create-storage-account"></a>Skapa lagringskonto

Följ dessa steg om du inte har ett befintligt Azure Storage-konto att exportera till:

1. Skapa ett [nytt lagrings konto i Azure Portal](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Du kan lära dig mer om att skapa nya [Azure Blob Storage-konton](https://aka.ms/blobdocscreatestorageaccount) eller [Azure Data Lake Storage v2-lagrings konton](../../storage/blobs/data-lake-storage-quickstart-create-account.md).

    - Om du väljer att exportera data till ett Azure Data Lake Storage v2-lagrings konto måste du välja **BlobStorage** som **konto typ**.
    - Du kan exportera data till lagrings konton i olika prenumerationer än den som du betalar per användning för IoT Central. Du kommer att ansluta med hjälp av en anslutnings sträng i det här fallet.

2. Skapa en behållare i ditt lagrings konto. Gå till ditt lagringskonto. Under **BLOB service**väljer du **Bläddra i blobbar**. Välj **+ behållare** överst för att skapa en ny behållare.

## <a name="set-up-continuous-data-export"></a>Konfigurera kontinuerlig data export

Nu när du har ett mål att exportera data till, följer du dessa steg för att konfigurera kontinuerlig data export.

1. Logga in på ditt IoT Central-program.

2. Välj **data export**i det vänstra fönstret.

    > [!Note]
    > Om du inte ser data export i det vänstra fönstret är du inte administratör i din app. Prata med en administratör för att konfigurera data export.

3. Välj knappen **+ ny** längst upp till höger. Välj en **Azure-Event Hubs**, **Azure Service Bus**eller **Azure Blob Storage** som mål för exporten. Det maximala antalet exporter per program är fem.

    ![Skapa ny kontinuerlig data export](media/howto-export-data-pnp/export-new2.png)

4. I list rutan väljer du **Event Hubs namnrymd**, **Service Bus namnrymd**, **lagrings konto namnrum**eller **anger en anslutnings sträng**.

    - Du ser bara lagrings konton, Event Hubs namnrymder och Service Bus namnrum i samma prenumeration som ditt IoT Central-program. Om du vill exportera till ett mål utanför den här prenumerationen väljer du **Ange en anslutnings sträng** och se steg 5.
    - För sju dagars testappar är det enda sättet att konfigurera kontinuerlig data export via en anslutnings sträng. Sju dagars prov versioner har ingen associerad Azure-prenumeration.

    ![Skapa ny händelsehubben](media/howto-export-data-pnp/export-eh.png)

5. Valfritt Om du väljer **Ange en anslutnings sträng**visas en ny ruta där du kan klistra in anslutnings strängen. Så här hämtar du anslutnings strängen för din:
    - Event Hubs eller Service Bus går du till namn området i Azure Portal.
        - Under **Inställningar**väljer du **principer för delad åtkomst**
        - Välj standard- **RootManageSharedAccessKey** eller skapa en ny
        - Kopiera antingen den primära eller sekundära anslutnings strängen
    - Lagrings konto går du till lagrings kontot i Azure Portal:
        - Under **Inställningar**väljer du **åtkomst nycklar**
        - Kopiera antingen anslutnings strängen KEY1 eller key2-anslutningssträngen

6. Välj en händelsehubben, en kö, ett ämne eller en behållare i list rutan.

7. Under **data som ska exporteras**väljer du de typer av data som ska exporteras genom att ange typen till **på**.

8. Om du vill aktivera kontinuerlig data export kontrollerar du att aktivera **data export** är **aktiverat**. Välj **Spara**.

9. Efter några minuter visas dina data i det valda målet.

## <a name="data-format"></a>Dataformat

Data exporteras till händelsehubben eller Service Bus kö eller ämne i nära-real tid.

Data exporteras till ditt lagrings konto en gång per minut, med varje fil som innehåller grupp ändringar sedan den senaste exporterade filen. Exporterade data placeras i tre mappar i JSON-format. Standard Sök vägarna i ditt lagrings konto är:

- Telemetri: _{container}/{app-ID}/Telemetry/{YYYY}/{MM}/{DD}/{hh}/{mm}/{filename}_
- Enheter: _{container}/{app-ID}/Devices/{YYYY}/{MM}/{DD}/{hh}/{mm}/{filename}_
- Enhets mallar: _{container}/{app-ID}/deviceTemplates/{YYYY}/{MM}/{DD}/{hh}/{mm}/{filename}_

Du kan bläddra bland de exporterade filerna i Azure Portal genom att gå till filen och välja fliken **Redigera BLOB** .

Exporterade telemetridata innehåller hela det meddelande som dina enheter skickade till IoT Central, inte bara själva telemetri värden. Exporterade enhets data innehåller ändringar av egenskaper och metadata för alla enheter och exporterade enheter innehåller ändringar i alla enhets mallar. Exporterade data finns i egenskapen Body och är i JSON-format.

### <a name="telemetry"></a>Telemetri

Ett nytt meddelande exporteras snabbt när IoT Central tar emot meddelandet från en enhet.

- Varje exporterat meddelande i Event Hubs och Service Bus innehåller det fullständiga meddelandet enheten som skickas i egenskapen Body i JSON-format.
- De exporterade filerna i Blob Storage använder samma format som de meddelandefiler som exporteras av [IoT Hub](../../iot-hub/iot-hub-csharp-csharp-process-d2c.md) meddelanderoutning till Blob Storage. Se till att enheterna skickar meddelanden som har `contentType: application/JSON` och `contentEncoding:utf-8` (eller `utf-16``utf-32`). Se [IoT Hub-dokumentationen](../../iot-hub/iot-hub-devguide-routing-query-syntax.md#message-routing-query-based-on-message-body) för ett exempel.

De enheter som skickar telemetri representeras av enhets-ID: n (se följande avsnitt). För att hämta namnen på enheterna, exportera enhets data och korrelera varje meddelande med hjälp av **connectionDeviceId** som matchar enhets meddelandets **deviceId** .

I följande exempel visas ett meddelande om telemetri-data som tagits emot i händelsehubben eller Service Bus kö eller ämne.

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

I följande exempel visas en post i JSON-format i Blob Storage:

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

### <a name="devices"></a>Enheter

Meddelanden som innehåller enhets data skickas till händelsehubben eller Service Bus kö eller ämne en gång om några minuter. En ny ögonblicks bild skrivs till Blob Storage en gång per minut. Varje meddelande eller ögonblicks bild innehåller information om:

- Nya enheter som har lagts till
- Enheter med ändrade egenskaps värden

Varje meddelande eller post i en ögonblicks bild representerar en eller flera ändringar av enheten sedan det senaste exporterade meddelandet. Informationen omfattar:

- enhetens `@id` i IoT Central
- enhetens `name`
- `deviceId` från [enhets etablerings tjänsten](https://aka.ms/iotcentraldocsdps)
- Information om enhets mal len
- Egenskaps värden

Enheter som tagits bort sedan den senaste batchen inte exporteras. För närvarande finns det inga indikatorer i exporterade meddelanden för borttagna enheter.

Enhets mal len som varje enhet tillhör representeras av ett mall-ID för enhet. Om du vill hämta namnet på enhets mal len måste du också exportera enhetens mall data.

I följande exempel visas ett meddelande om enhets data i händelsehubben eller Service Bus kö eller ämne:

```json
{
  "body":{
    "@id":"<id>",
    "@type":"Device",
    "displayName":"Airbox - 266d30aedn5",
    "data":{
      "$cloudProperties":{
        "Color":"blue"
      },
      "EnvironmentalSensor":{
        "thsensormodel":{
          "reported":{
            "value":"A1",
            "$lastUpdatedTimestamp":"2019-10-02T18:14:49.3820326Z"
          }
        },
        "pm25sensormodel":{
          "reported":{
            "value":"P1",
            "$lastUpdatedTimestamp":"2019-10-02T18:14:49.3820326Z"
          }
        }
      },
      "urn_azureiot_DeviceManagement_DeviceInformation":{
        "totalStorage":{
          "reported":{
            "value":3088.1959855710156,
            "$lastUpdatedTimestamp":"2019-10-02T18:14:49.3820326Z"
          }
        },
        "totalMemory":{
          "reported":{
            "value":16005.703586477555,
            "$lastUpdatedTimestamp":"2019-10-02T18:14:49.3820326Z"
          }
        }
      }
    },
    "instanceOf":"<templateId>",
    "deviceId":"<deviceId>",
    "simulated":true
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
  "enqueuedTimeUtc":"2019-10-02T18:14:49.3820326Z",
  "offset":"<offset>"
}
```

Exporterade filer innehåller en enskild rad per post. I följande exempel visas en post i JSON-format.

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

### <a name="device-templates"></a>Enhets mallar

Meddelanden som innehåller information om enhetens mallar skickas till händelsehubben eller Service Bus kö eller ämne en gång om några minuter. En ny ögonblicks bild skrivs en gång per minut till Blob Storage. Därför tar en grupp meddelanden med data om:

Meddelanden som innehåller information om enhets mal len skickas till händelsehubben eller Service Bus kö eller ämne en gång om några minuter. En ny ögonblicks bild skrivs till Blob Storage en gång per minut. Varje meddelande eller ögonblicks bild innehåller information om:

- Nya enhetsspecifika mallar som har lagts till eller versioner ATS
- Enhets mallar med ändrade kapacitets modeller, moln egenskaper, åsidosättningar och ursprungliga värden

Varje meddelande-eller ögonblicks bild post representerar en eller flera ändringar i en enhets mall sedan det senaste exporterade meddelandet. Information som skickas i varje meddelande eller post innehåller:

- `@id` av enhets mal len
- `name` av enhets mal len
- `version` av enhets mal len
- Enheten `capabilityModel` inklusive dess `interfaces`, och definitioner för telemetri, egenskaper och kommandon
- `cloudProperties` definitioner
- Åsidosättningar och initiala värden, infogade i `capabilityModel`

Enhetsspecifika har tagits bort sedan den senaste batchen inte har exporter ATS. För närvarande finns det inga indikatorer i exporterade meddelanden för borttagna enhets mallar.

I följande exempel visas ett enhets mal linne-meddelande i händelsehubben eller Service Bus kö eller ämne:

```json
{
  "body":{
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

Exporterade filer innehåller en enskild rad per post. I följande exempel visas en post i JSON-format.

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

Nu när du vet hur du exporterar dina data till Azure Event Hubs och Azure Service Bus fortsätter du till nästa steg:

> [!div class="nextstepaction"]
> [Så här utlöser du Azure Functions](howto-trigger-azure-functions.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
