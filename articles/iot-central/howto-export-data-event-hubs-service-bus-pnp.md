---
title: Exportera data till Azure Event Hubs och Azure Service Bus | Microsoft Docs
description: Så här exporterar du data från ditt Azure IoT Central-program till Azure Event Hubs och Azure Service Bus
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 07/09/2019
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: fed9c924274cb66671e233a7dc6d431d81e0dbfb
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973182"
---
# <a name="export-your-data-in-azure-iot-central-preview-features"></a>Exportera dina data i Azure IoT Central (för hands versions funktioner)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

*Det här avsnittet gäller för administratörer.*

Den här artikeln beskriver hur du använder funktionen för kontinuerlig data export i Azure IoT Central för att exportera dina data till dina egna **Azure-Event Hubs**och **Azure Service Bus** instanser. Du kan exportera **telemetri**, **enheter**och **enhetsspecifika** till ditt eget mål för att kunna använda insikter och analyser av varma sökvägar. Detta inkluderar att aktivera anpassade regler i Azure Stream Analytics, utlösa anpassade arbets flöden i Azure Logic Apps eller skicka det via Azure Functions som ska omvandlas.

> [!Note]
> När du sedan aktiverar kontinuerlig data export får du bara data från det tillfället. För närvarande går det inte att hämta data under en tid då kontinuerliga data exporter ATS. Aktivera kontinuerlig data export tidigt om du vill behålla mer historiska data.

## <a name="prerequisites"></a>Förutsättningar

- Du måste vara administratör i IoT Centrals programmet

## <a name="set-up-export-destination"></a>Konfigurera export mål

Om du inte har någon befintlig Event Hubs/Service Bus att exportera till, följer du dessa steg för att skapa en:

### <a name="create-event-hubs-namespace"></a>Skapa Event Hubs namn område

1. Skapa ett [nytt Event Hubs-namnområde i Azure Portal](https://ms.portal.azure.com/#create/Microsoft.EventHub). Du kan läsa mer i [Azure Event Hubs-dokument](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).
2. Välj en prenumeration. 

    > [!Note] 
    > Nu kan du exportera data till andra prenumerationer som **inte är samma** som den som du betalar per användning för IoT Central programmet. Du kommer att ansluta med hjälp av en anslutnings sträng i det här fallet.
3. Skapa en Event Hub i Event Hubs namn området. Gå till ditt namn område och välj **+ Event Hub** överst för att skapa en Event Hub-instans.

### <a name="create-service-bus-namespace"></a>Skapa Service Bus namn område

1. Skapa ett [nytt Service Bus-namnområde i Azure Portal](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5) . Du kan läsa mer i [Azure Service Bus dokument](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-create-namespace-portal).
2. Välj en prenumeration. 

    > [!Note] 
    > Nu kan du exportera data till andra prenumerationer som **inte är samma** som den som du betalar per användning för IoT Central programmet. Du kommer att ansluta med hjälp av en anslutnings sträng i det här fallet.

3. Gå till din Service Bus namnrymd och välj **+ kö** eller **+ ämne** överst för att skapa en kö eller ett ämne att exportera till.


## <a name="set-up-continuous-data-export"></a>Konfigurera kontinuerlig data export

Nu när du har ett Event Hubs/Service Bus-mål att exportera data till, följer du dessa steg för att konfigurera kontinuerlig data export. 

1. Logga in på ditt IoT Central-program.

2. På den vänstra menyn väljer du **data export**.

    > [!Note]
    > Om du inte ser data export på den vänstra menyn är du inte administratör i din app. Prata med en administratör för att konfigurera data export.

3. Välj knappen **+ ny** längst upp till höger. Välj en av **Azure Event Hubs** eller **Azure Service Bus** som export mål. 

    > [!NOTE] 
    > Det maximala antalet exporter per app är fem. 

    ![Skapa ny kontinuerlig data export](media/howto-export-data-pnp/export-new2.png)

4. I list rutan väljer du namn området för **Event Hubs namnrymd/Service Bus**. Du kan också välja det sista alternativet i listan som **anger en anslutnings sträng**. 

    > [!NOTE] 
    > Du ser bara lagrings konton/Event Hubs namn områden/Service Bus namn områden i **samma prenumeration som din IoT Central-app**. Om du vill exportera till ett mål utanför den här prenumerationen väljer du **Ange en anslutnings sträng** och se steg 5.

    > [!NOTE] 
    > För 7 dagars test av appar är det enda sättet att konfigurera kontinuerlig data export genom en anslutnings sträng. Detta beror på att sju dagars prov versioner inte har någon tillhör ande Azure-prenumeration.

    ![Skapa ny CDE-Händelsehubben](media/howto-export-data-pnp/export-eh.png)

5. Valfritt Om du väljer **Ange en anslutnings sträng**visas en ny ruta där du kan klistra in anslutnings strängen. Så här hämtar du anslutnings strängen för din:
    - Event Hubs eller Service Bus går du till namn området i Azure Portal.
        - Under **Inställningar**väljer du **principer för delad åtkomst**
        - Välj standard- **RootManageSharedAccessKey** eller skapa en ny
        - Kopiera antingen den primära eller sekundära anslutnings strängen

6. Välj en Händelsehubben/kö eller ett ämne i list rutan.

7. Under **data som ska exporteras**anger du varje typ av data som ska exporteras genom att ange typen till **på**.

8. Om du vill aktivera kontinuerlig data export kontrollerar du att aktivera **data export** är **aktiverat**. Välj **Spara**.

9. Efter några minuter visas dina data i det valda målet.


## <a name="data-format"></a>Dataformat

Telemetri-, enhets-och enhets data exporteras till händelsehubben eller Service Bus kö eller ämne i nära real tid. Exporterade telemetridata innehåller hela det meddelande som dina enheter skickade till IoT Central, inte bara själva telemetri värden. Exporterade enhets data innehåller ändringar av egenskaper och metadata för alla enheter och exporterade enheter innehåller ändringar i alla enhets mallar. Exporterade data finns i "Body"-egenskapen och är i JSON-format.

> [!NOTE]
> När du väljer en Service Bus som ett export mål får inte köer och ämnen **ha sessioner eller dubblettidentifiering aktiverade**. Om något av dessa alternativ är aktiverat kommer vissa meddelanden inte att tas emot i din kö eller ämne.

### <a name="telemetry"></a>Telemetri

Ett nytt meddelande exporteras snabbt när IoT Central tar emot meddelandet från en enhet. Varje exporterat meddelande i Event Hubs och Service Bus innehåller det fullständiga meddelande som enheten skickade i "Body"-egenskapen i JSON-format. 

> [!NOTE]
> De enheter som skickar telemetri representeras av enhets-ID: n (se följande avsnitt). För att hämta namnen på enheterna, exportera enhets data och korrelera varje messsage med hjälp av **connectionDeviceId** som matchar enhets meddelandets **deviceId** .

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

### <a name="devices"></a>Enheter

Meddelanden som innehåller enhets data skickas till händelsehubben eller Service Bus kö eller ämne en gång om några minuter. Det innebär att en batch med meddelanden tas emot med data om
- Nya enheter som har lagts till
- Enheter med ändrade egenskaps värden

Varje meddelande representerar en eller flera ändringar av en enhet sedan det senaste exporterade meddelandet. Information som ska skickas i varje meddelande inkluderar:
- `@id` av enheten i IoT Central
- `name` av enheten
- `deviceId` från [enhets etablerings tjänsten](https://aka.ms/iotcentraldocsdps)
- Information om enhets mal len
- Egenskapsvärden

> [!NOTE]
> Enheter som tagits bort sedan den senaste batchen inte exporteras. För närvarande finns det inga indikatorer i exporterade meddelanden för borttagna enheter.
>
> Enhets mal len som varje enhet tillhör representeras av ett mall-ID för enhet. Om du vill hämta namnet på enhets mal len måste du också exportera enhetens mall data.

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

### <a name="device-templates"></a>Enhetsmallar

Meddelanden som innehåller information om enhetens mallar skickas till händelsehubben eller Service Bus kö eller ämne en gång om några minuter. Det innebär att en batch med meddelanden tas emot med data om
- Nya enhetsspecifika mallar som har lagts till eller versioner ATS
- Enhets mallar med ändrade capabilityModels, cloudProperties, åsidosättningar och initiala värden

Varje meddelande representerar en eller flera ändringar i en enhets mall sedan det senaste exporterade meddelandet. Information som ska skickas i varje meddelande inkluderar:
- `@id` av enhets mal len
- `name` av enhets mal len
- `version` av enhets mal len
- Enhets `capabilityModel`, inklusive dess `interfaces`, och definitioner för telemetri, egenskaper och kommandon
- `cloudProperties` definitioner
- Åsidosättningar och initiala värden, infogade med `capabilityModel`

> [!NOTE]
> Enhetsspecifika har tagits bort sedan den senaste batchen inte har exporter ATS. För närvarande finns det inga indikatorer i exporterade meddelanden för borttagna enhets mallar.

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

## <a name="next-steps"></a>Nästa steg

Nu när du vet hur du exporterar dina data till Azure Event Hubs och Azure Service Bus fortsätter du till nästa steg:

> [!div class="nextstepaction"]
> [Så här utlöser du Azure Functions](howto-trigger-azure-functions.md)
