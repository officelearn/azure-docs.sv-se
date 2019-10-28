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
ms.openlocfilehash: df1263e2cc13f875819c9bb16b5eb7399368b7f5
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2019
ms.locfileid: "72953530"
---
# <a name="export-your-data-in-azure-iot-central"></a>Exportera dina data i Azure IoT Central

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

*Det här avsnittet gäller för administratörer.*

Den här artikeln beskriver hur du använder funktionen för kontinuerlig data export i Azure IoT Central för att exportera dina data till dina egna **Azure-Event Hubs**och **Azure Service Bus** instanser. Du kan exportera **mått**, **enheter**och **enhetsspecifika** till ditt eget mål för att kunna använda insikter och analyser av varma sökvägar. Detta inkluderar att aktivera anpassade regler i Azure Stream Analytics, utlösa anpassade arbets flöden i Azure Logic Apps eller omvandla data och skicka dem via Azure Functions. 

> [!Note]
> När du sedan aktiverar kontinuerlig data export får du bara data från det tillfället. För närvarande går det inte att hämta data under en tid då kontinuerliga data exporter ATS. Aktivera kontinuerlig data export tidigt om du vill behålla mer historiska data.


## <a name="prerequisites"></a>Krav

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

2. I den vänstra rutan väljer du **kontinuerlig data export**.

    > [!Note]
    > Om du inte ser kontinuerliga data export i det vänstra fönstret är du inte administratör i din app. Prata med en administratör för att konfigurera data export.

3. Välj knappen **+ ny** längst upp till höger. Välj en av **Azure Event Hubs** eller **Azure Service Bus** som export mål. 

    > [!NOTE] 
    > Det maximala antalet exporter per app är fem. 

    ![Skapa ny kontinuerlig data export](media/howto-export-data/export-new2.png)

4. I list rutan väljer du namn området för **Event Hubs namnrymd/Service Bus**. Du kan också välja det sista alternativet i listan som **anger en anslutnings sträng**. 

    > [!NOTE] 
    > Du ser bara lagrings konton/Event Hubs namn områden/Service Bus namn områden i **samma prenumeration som din IoT Central-app**. Om du vill exportera till ett mål utanför den här prenumerationen väljer du **Ange en anslutnings sträng** och se steg 5.

    > [!NOTE] 
    > För 7 dagars test av appar är det enda sättet att konfigurera kontinuerlig data export genom en anslutnings sträng. Detta beror på att sju dagars prov versioner inte har någon tillhör ande Azure-prenumeration.

    ![Skapa ny CDE-Händelsehubben](media/howto-export-data/export-eh.png)

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

Data för mått, enheter och enhets information exporteras till händelsehubben eller Service Bus kö eller ämne i nära real tid. Exporterade mått data innehåller hela det meddelande som dina enheter skickade till IoT Central, inte bara värdena för själva måtten. Exporterade enhets data innehåller ändringar i egenskaper och inställningar för alla enheter och exporterade enheter innehåller ändringar i alla enhets mallar. Exporterade data finns i "Body"-egenskapen och är i JSON-format.

> [!NOTE]
> När du väljer en Service Bus som ett export mål får inte köer och ämnen **ha sessioner eller dubblettidentifiering aktiverade**. Om något av dessa alternativ är aktiverat kommer vissa meddelanden inte att tas emot i din kö eller ämne.

### <a name="measurements"></a>Mått

Ett nytt meddelande exporteras snabbt när IoT Central tar emot meddelandet från en enhet. Varje exporterat meddelande i Event Hubs och Service Bus innehåller det fullständiga meddelande som enheten skickade i "Body"-egenskapen i JSON-format. 

> [!NOTE]
> De enheter som skickar måtten representeras av enhets-ID: n (se följande avsnitt). För att hämta namnen på enheterna, exportera enhets data och korrelera varje messsage med hjälp av **connectionDeviceId** som matchar enhets meddelandets **deviceId** .

I följande exempel visas ett meddelande om mätnings data som tagits emot i Event Hub eller Service Bus kö eller ämne.

```json
{
  "body": {
    "humidity": 29.06963648666288,
    "temp": 8.4503795661685,
    "pressure": 1075.8334910110093,
    "magnetometerX": 408.6966458887116,
    "magnetometerY": -532.8809796603962,
    "magnetometerZ": 174.70653875528205,
    "accelerometerX": 1481.546749013788,
    "accelerometerY": -1139.4316656437406,
    "accelerometerZ": 811.6928695575307,
    "gyroscopeX": 442.19879163299856,
    "gyroscopeY": 123.23710975717177,
    "gyroscopeZ": 708.5397575786151,
    "deviceState": "DANGER"
  },
  "annotations": {
    "iothub-connection-device-id": "<connectionDeviceId>",
    "iothub-connection-auth-method": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "iothub-connection-auth-generation-id": "<generationId>",
    "iothub-enqueuedtime": 1539381029965,
    "iothub-message-source": "Telemetry",
    "x-opt-sequence-number": 25325,
    "x-opt-offset": "<offset>",
    "x-opt-enqueued-time": 1539381030200
  },
  "sequenceNumber": 25325,
  "enqueuedTimeUtc": "2018-10-02T21:50:30.200Z",
  "offset": "<offset>",
  "properties": {
    "content_type": "application/json",
    "content_encoding": "utf-8"
  }
}
```

### <a name="devices"></a>Enheter

Meddelanden som innehåller enhets data skickas till händelsehubben eller Service Bus kö eller ämne en gång om några minuter. Det innebär att en batch med meddelanden tas emot med data om
- Nya enheter som har lagts till
- Enheter med ändrad egenskap och inställnings värden

Varje meddelande representerar en eller flera ändringar av en enhet sedan det senaste exporterade meddelandet. Information som ska skickas i varje meddelande inkluderar:
- enhetens `id` i IoT Central
- enhetens `name`
- `deviceId` från [enhets etablerings tjänsten](https://aka.ms/iotcentraldocsdps)
- Information om enhets mal len
- Egenskaps värden
- Inställnings värden

> [!NOTE]
> Enheter som tagits bort sedan den senaste batchen inte exporteras. För närvarande finns det inga indikatorer i exporterade meddelanden för borttagna enheter.
>
> Enhets mal len som varje enhet tillhör representeras av ett mall-ID för enhet. Om du vill hämta namnet på enhets mal len måste du också exportera enhetens mall data.

I följande exempel visas ett meddelande om enhets data i händelsehubben eller Service Bus kö eller ämne:


```json
{
  "body": {
    "id": "<id>",
    "name": "<deviceName>",
    "simulated": true,
    "deviceId": "<deviceId>",
    "deviceTemplate": {
      "id": "<templateId>",
      "version": "1.0.0"
    },
    "properties": {
      "cloud": {
        "location": "Seattle"
      },
      "device": {
        "dieNumber": 5445.5862873026645
      }
    },
    "settings": {
      "device": {
        "fanSpeed": 0
      }
    }
  },
  "annotations": {
    "iotcentral-message-source": "devices",
    "x-opt-partition-key": "<partitionKey>",
    "x-opt-sequence-number": 39740,
    "x-opt-offset": "<offset>",
    "x-opt-enqueued-time": 1539274959654
  },
  "partitionKey": "<partitionKey>",
  "sequenceNumber": 39740,
  "enqueuedTimeUtc": "2018-10-02T16:22:39.654Z",
  "offset": "<offset>",
}
```

### <a name="device-templates"></a>Enhets mallar

Meddelanden som innehåller information om enhetens mallar skickas till händelsehubben eller Service Bus kö eller ämne en gång om några minuter. Det innebär att en batch med meddelanden tas emot med data om
- Nya enhetsspecifika mallar som har lagts till
- Enhets mallar med ändrade mått, egenskaper och inställnings definitioner

Varje meddelande representerar en eller flera ändringar i en enhets mall sedan det senaste exporterade meddelandet. Information som ska skickas i varje meddelande inkluderar:
- `id` av enhets mal len
- `name` av enhets mal len
- `version` av enhets mal len
- Mät data typer och minsta/högsta värde
- Egenskaps data typer och standardvärden
- Ange data typer och standardvärden

> [!NOTE]
> Enhetsspecifika har tagits bort sedan den senaste batchen inte har exporter ATS. För närvarande finns det inga indikatorer i exporterade meddelanden för borttagna enhets mallar.

I följande exempel visas ett meddelande om Device templates-data i händelsehubben eller Service Bus kö eller ämne:

```json
{ 
  "body":{ 
    "id":"<id>",
    "version":"1.0.0",
    "name":"<templateName>",
    "measurements":{ 
      "telemetry":{ 
        "humidity":{ 
          "dataType":"double",
          "name":"humidity"
        },
        "pressure":{ 
          "dataType":"double",
          "name":"pressure"
        },
        "temp":{ 
          "dataType":"double",
          "name":"temperature"
        }
      }
    },
    "properties":{ 
      "cloud":{ 
        "location":{ 
          "dataType":"string",
          "name":"Location"
        }
      },
      "device":{ 
        "dieNumber":{ 
          "dataType":"double",
          "name":"Die Number"
        }
      }
    },
    "settings":{ 
      "device":{ 
        "fanSpeed":{ 
          "dataType":"double",
          "name":"Fan Speed",
          "initialValue":0
        }
      }
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
  "enqueuedTimeUtc":"2018-10-02T16:23:05.085Z",
  "offset":"<offset>"
}
```

## <a name="next-steps"></a>Nästa steg

Nu när du vet hur du exporterar dina data till Azure Event Hubs och Azure Service Bus fortsätter du till nästa steg:

> [!div class="nextstepaction"]
> [Så här utlöser du Azure Functions](howto-trigger-azure-functions.md)
