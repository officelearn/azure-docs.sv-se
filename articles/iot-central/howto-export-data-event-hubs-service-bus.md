---
title: Exportera data till Azure Event Hubs och Azure Service Bus | Microsoft Docs
description: Hur du exporterar data från Azure IoT Central programmet till Azure Event Hubs och Azure Service Bus
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 03/20/2019
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: b0a34cd8eebdd816cb780b34de936a6cc56d0994
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64730227"
---
# <a name="export-your-data-in-azure-iot-central"></a>Exportera dina data i Azure IoT Central

*Det här avsnittet gäller för administratörer.*

Den här artikeln beskriver hur du använder funktionen löpande export i Azure IoT Central för att exportera data till dina egna **Azure Event Hubs**, och **Azure Service Bus** instanser. Du kan exportera **mätningar av**, **enheter**, och **enheten mallar** till dina egna mål för varma sökvägen insikter och analyser. Detta inkluderar att utlösa anpassade regler i Azure Stream Analytics, utlösa anpassade arbetsflöden i Azure Logic Apps eller omvandlar data och skicka den via Azure Functions. 

> [!Note]
> Igen när du aktiverar löpande dataexport, får du endast data från det ögonblick då och uppåt. För närvarande går inte att hämta data under en tid när löpande dataexport var inaktiverat. Om du vill behålla fler historiska data, aktivera löpande dataexport tidigt.


## <a name="prerequisites"></a>Nödvändiga komponenter

- Du måste vara administratör i din IoT Central-App

## <a name="set-up-export-destination"></a>Konfigurera exportera

Följ dessa steg om du inte har en befintlig Event Hubs/Service Bus för att exportera till:

## <a name="create-event-hubs-namespace"></a>Skapa Event Hubs-namnområdet

1. Skapa en [nya Event Hubs-namnområde i Azure-portalen](https://ms.portal.azure.com/#create/Microsoft.EventHub). Du kan läsa mer i [Händelsehubbar i Azure-docs](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).
2. Välj en prenumeration. 

    > [!Note] 
    > Nu kan du exportera data till andra prenumerationer som är **inte samma** som programmets användningsbaserad IoT Central. Du ansluter med en anslutningssträng i det här fallet.
3. Skapa en händelsehubb i namnområdet för Event Hubs. Gå till ditt namnområde och välj **+ Event Hub** överst för att skapa en event hub-instans.

## <a name="create-service-bus-namespace"></a>Skapa Service Bus-namnområde

1. Skapa en [nya Service Bus-namnområde i Azure-portalen](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5) . Du kan läsa mer i [Azure Service Bus docs](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-create-namespace-portal).
2. Välj en prenumeration. 

    > [!Note] 
    > Nu kan du exportera data till andra prenumerationer som är **inte samma** som programmets användningsbaserad IoT Central. Du ansluter med en anslutningssträng i det här fallet.

3. Gå till ditt Service Bus-namnområde och välj **+ kö** eller **+ ämne** överst för att skapa en kö eller ämne att exportera till.


## <a name="set-up-continuous-data-export"></a>Konfigurera löpande dataexport

Nu när du har ett Event Hubs/Service Bus-mål för att exportera data för att följa dessa steg för att konfigurera löpande dataexport. 

1. Logga in på ditt IoT Central-program.

2. I den vänstra menyn väljer du **löpande Export av Data**.

    > [!Note]
    > Om du inte ser löpande Export av Data på den vänstra menyn kan är du inte administratör i din app. Kontakta en administratör att ställa in export av data.

    ![Skapa ny cde Event Hub](media/howto-export-data/export_menu.PNG)

3. Välj den **+ ny** knappen uppe till höger. Välj något av **Azure Event Hubs** eller **Azure Service Bus** som mål för exporten. 

    > [!NOTE] 
    > Det maximala antalet exporter per app är fem. 

    ![Skapa ny löpande dataexport](media/howto-export-data/export_new.PNG)

4. I den nedrullningsbara listrutan väljer du din **Event Hubs-namnområde/Service Bus-namnområde**. Du kan också välja alternativet sist i listan som **ange en anslutningssträng**. 

    > [!NOTE] 
    > Du ser bara Storage konton/Event Hubs-namnområden/Service Bus-namnområden i den **samma prenumeration som din IoT Central-app**. Om du vill exportera till ett mål utanför den här prenumerationen kan du välja **ange en anslutningssträng** och finns i steg 5.

    > [!NOTE] 
    > 7 dagars utvärderingsversion appar, det enda sättet att konfigurera kontinuerlig data exportera är i via en anslutningssträng. Det beror på att 7 dagars utvärderingsversion appar inte har en associerad Azure-prenumeration.

    ![Skapa ny cde Event Hub](media/howto-export-data/export_create.PNG)

5. (Valfritt) Om du har valt **ange en anslutningssträng**, en ny ruta visas där du kan klistra in anslutningssträngen. Att hämta anslutningssträngen för din:
    - Event Hubs eller Azure Service Bus, går du till namnområdet i Azure-portalen.
        - Under **inställningar**väljer **principer för delad åtkomst**
        - Välj standard **RootManageSharedAccessKey** eller skapa en ny
        - Kopiera den primär eller sekundär anslutningssträngen
 
6. Välj en Event hub/kö eller ämne i nedrullningsbara listrutan.

7. Under **Data som ska exporteras**, ange varje typ av data som ska exporteras genom att ställa in typen **på**.

6. Se till att aktivera löpande dataexport genom **dataexport** är **på**. Välj **Spara**.

  ![Konfigurera löpande dataexport](media/howto-export-data/export_list.PNG)

7. Efter ett par minuter visas dina data i ditt valda mål.


## <a name="export-to-azure-event-hubs-and-azure-service-bus"></a>Exportera till Azure Event Hubs och Azure Service Bus

Mått, enheter och mallar enhetsdata exporteras till din händelsehubb eller Service Bus-kö eller ett ämne i nära realtid. Exporterade mätningar data innehåller i sin helhet meddelandet dina enheter som skickas till IoT Central, inte bara värdena för själva mätningarna. Exporterade enheter data innehåller ändringar av egenskaper och inställningar för alla enheter och exporterade enheten mallar innehåller ändringar i alla mallar för enheten. Exporterade data är i egenskapen ”body” och är i JSON-format.

> [!NOTE]
> När du väljer en Service Bus som en export-mål, köer och ämnen **får inte ha sessioner eller dubblettidentifiering aktiverat**. Om något av dessa alternativ är aktiverat, kommer inte några meddelanden anländer i din kö eller ämne.

### <a name="measurements"></a>Mått

Ett nytt meddelande exporteras snabbt efter IoT Central får meddelandet från en enhet. Varje exporterad meddelande i Händelsehubbar och Service Bus innehåller hela meddelandet enheten skickades i egenskapen ”body” i JSON-format. 

> [!NOTE]
> De enheter som skickar mått som representeras av enhets-ID (se nedan). Om du vill hämta namnen på enheterna som, exportera enhetsdata och korrelera varje meddelandet med hjälp av den **connectionDeviceId** som matchar den **deviceId** för enhet-meddelande.

I följande exempel visas ett meddelande om mätningar av data som tas emot i event hub eller Service Bus-kö eller ämne.

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
  "enqueuedTimeUtc": "2018-10-12T21:50:30.200Z",
  "offset": "<offset>",
  "properties": {
    "content_type": "application/json",
    "content_encoding": "utf-8"
  }
}
```

### <a name="devices"></a>Enheter

Meddelanden som innehåller enhetsdata skickas till din händelsehubb eller Service Bus-kö eller ett ämne en gång några minuters mellanrum. Det innebär att några minuters mellanrum, en grupp med meddelanden anländer med data om
- Nya enheter som har lagts till
- Enheter med ändrade egenskapen och ange värden

Varje meddelande som representerar en eller flera ändringar till en enhet eftersom det sista meddelandet som exporterade. Information som skickas i varje meddelande som innehåller:
- `id` för enheten i IoT Central
- `name` enhetens
- `deviceId` från [Device Provisioning-tjänst](https://aka.ms/iotcentraldocsdps)
- Mallen enhetsinformation
- Egenskapsvärden
- Inställningsvärden

> [!NOTE]
> Enheter som har tagits bort sedan den senaste batchen inte exporteras. Det finns för närvarande inga indikatorer i exporterade meddelanden för enheter som har tagits bort.
>
> Mallen enhet som varje enhet som tillhör representeras av en enhet mall-ID. För att hämta namnet på mallen enheten, måste du Exportera mall enhetsdata för.

I följande exempel visas ett meddelande om enhetsdata i event hub eller Service Bus-kö eller ett ämne:


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
  "enqueuedTimeUtc": "2018-10-11T16:22:39.654Z",
  "offset": "<offset>",
}
```

### <a name="device-templates"></a>Enheten mallar

Meddelanden som innehåller mallar enhetsdata skickas till din händelsehubb eller Service Bus-kö eller ämne en gång några minuters mellanrum. Det innebär att några minuters mellanrum, en grupp med meddelanden anländer med data om
- Ny enhet mallar som har lagts till
- Enheten mallar med ändrade mått, egenskap och ange definitioner

Varje meddelande som representerar en eller flera ändringar i en mall för enheten eftersom det sista meddelandet som exporterade. Information som skickas i varje meddelande som innehåller:
- `id` för mall för enhet
- `name` för mall för enhet
- `version` för mall för enhet
- Datatyper för mätning och min/max-värden
- Egenskapen datatyper och standardvärden
- Typer av data för inställningar och standardvärden

> [!NOTE]
> Enhet-mallar som har tagits bort sedan den senaste batchen exporteras inte. Det finns för närvarande inga indikatorer i exporterade meddelanden för borttagna mallar.

I följande exempel visas ett meddelande om mallar enhetsdata i event hub eller Service Bus-kö eller ett ämne:

```json
{
  "body": {
    "id": "<id>",
    "version": "1.0.0",
    "name": "<templateName>",
    "measurements": {
      "telemetry": {
        "humidity": {
          "dataType": "double",
          "name": "humidity"
        },
        "pressure": {
          "dataType": "double",
          "name": "pressure"
        },
        "temp": {
          "dataType": "double",
          "name": "temperature"
        }
      }
    },
    "properties": {
      "cloud": {
        "location": {
          "dataType": "string",
          "name": "Location"
        }
      },
      "device": {
        "dieNumber": {
          "dataType": "double",
          "name": "Die Number"
        }
      }
    },
    "settings": {
      "device": {
        "fanSpeed": {
          "dataType": "double",
          "name": "Fan Speed",
          "initialValue": 0
        }
      }
    }
  },
  "annotations": {
    "iotcentral-message-source": "deviceTemplates",
    "x-opt-partition-key": "<partitionKey>",
    "x-opt-sequence-number": 25315,
    "x-opt-offset": "<offset>",
    "x-opt-enqueued-time": 1539274985085
  },
  "partitionKey": "<partitionKey>",
  "sequenceNumber": 25315,
  "enqueuedTimeUtc": "2018-10-11T16:23:05.085Z",
  "offset": "<offset>",
}
```

## <a name="next-steps"></a>Nästa steg

Nu när du vet hur du exporterar dina data till Azure Event Hubs och Azure Service Bus kan du fortsätta till nästa steg:

> [!div class="nextstepaction"]
> [Hur du utlöser Azure Functions](howto-trigger-azure-functions.md)
