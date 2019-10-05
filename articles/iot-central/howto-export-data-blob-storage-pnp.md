---
title: Exportera data till Azure Blob Storage | Microsoft Docs
description: Så här exporterar du data från ditt Azure IoT Central-program till Azure Blob Storage
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 09/26/2019
ms.topic: conceptual
ms.service: iot-central
manager: corywink
ms.openlocfilehash: 9ac650273a53da715b89e3233b30cf50710cfcfd
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973225"
---
# <a name="export-your-data-to-azure-blob-storage-preview-features"></a>Exportera data till Azure Blob Storage (för hands versions funktioner)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

*Det här avsnittet gäller för administratörer.*

Den här artikeln beskriver hur du använder funktionen för kontinuerlig data export i Azure IoT Central för att regelbundet exportera data till ditt **Azure Blob Storage-konto** eller **Azure Data Lake Storage Gen2 lagrings konto**. Du kan exportera mallar för **telemetri**, **enheter**och **enheter** till filer i JSON-format. Exporterade data kan användas för kall Sök vägs analys som utbildnings modeller i Azure Machine Learning eller långsiktig trend analys i Microsoft Power BI.

> [!Note]
> När du aktiverar kontinuerlig data export får du bara data från det här tillfället. För närvarande går det inte att hämta data under en tid då kontinuerliga data exporter ATS. Aktivera kontinuerlig data export tidigt om du vill behålla mer historiska data.


## <a name="prerequisites"></a>Förutsättningar

- Du måste vara administratör i IoT Centrals programmet

## <a name="create-storage-account"></a>Skapa lagringskonto
Om du inte har en befintlig lagrings enhet att exportera till följer du dessa steg:

1. Skapa ett [nytt lagrings konto i Azure Portal](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Du kan lära dig mer om att skapa nya [Azure Blob Storage-konton](https://aka.ms/blobdocscreatestorageaccount) eller [Azure Data Lake Storage v2-lagrings konton](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-account).

    > [!Note] 
    > Om du väljer att exportera data till ett ADLS v2-lagrings konto måste du välja **konto typ** som **BlobStorage**. 

    > [!Note] 
    > Du kan exportera data till lagrings konton i olika prenumerationer än den som du betalar per användning för IoT Central. Du kommer att ansluta med hjälp av en anslutnings sträng i det här fallet.

2. Skapa en behållare i ditt lagrings konto. Gå till ditt lagringskonto. Under **BLOB service**väljer du **Bläddra i blobbar**. Välj **+ behållare** överst för att skapa en ny behållare.


## <a name="set-up-continuous-data-export"></a>Konfigurera kontinuerlig data export

Nu när du har ett lagrings mål att exportera data till, följer du dessa steg för att konfigurera kontinuerlig data export. 

1. Logga in på ditt IoT Central-program.

2. På den vänstra menyn väljer du **data export**.

    > [!Note]
    > Om du inte ser data export på den vänstra menyn är du inte administratör i din app. Prata med en administratör för att konfigurera data export.

3. Välj knappen **+ ny** längst upp till höger. Välj **Azure Blob Storage** som mål för exporten. 

    > [!NOTE] 
    > Det maximala antalet exporter per app är fem. 

    ![Skapa ny kontinuerlig data export](media/howto-export-data-pnp/export-new2.png)

4. I list rutan väljer du ditt **lagrings konto namn område**. Du kan också välja det sista alternativet i listan som **anger en anslutnings sträng**. 

    > [!NOTE] 
    > Du ser bara lagrings konto namn områden i **samma prenumeration som din IoT Central-app**. Om du vill exportera till ett mål utanför den här prenumerationen väljer du **Ange en anslutnings sträng** och se steg 5.

    > [!NOTE] 
    > För 7 dagars test av appar är det enda sättet att konfigurera kontinuerlig data export genom en anslutnings sträng. Detta beror på att sju dagars prov versioner inte har någon tillhör ande Azure-prenumeration.

    ![Skapa ny export till BLOB](media/howto-export-data-pnp/export-create-blob2.png)

5. Valfritt Om du väljer **Ange en anslutnings sträng**visas en ny ruta där du kan klistra in anslutnings strängen. För att hämta anslutnings strängen för ditt lagrings konto går du till lagrings kontot i Azure Portal:
    - Under **Inställningar**väljer du **åtkomst nycklar**
    - Kopiera antingen anslutnings strängen KEY1 eller key2-anslutningssträngen
 
6. Välj en behållare i list rutan. Om du inte har en behållare går du till ditt lagrings konto i Azure Portal:
    - Under **BLOB service**väljer du **blobbar**. Klicka på **+ container** och ge containern ett namn. Välj en offentlig åtkomst nivå för dina data (alla kommer att fungera med kontinuerlig data export). Läs mer i [Azure Storage dokument](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal#create-a-container).

7.  Under **data som ska exporteras**anger du varje typ av data som ska exporteras genom att ange typen till **på**.
   
    > [!NOTE] 
    > Data exporteras i JSON-format.

8. Om du vill aktivera kontinuerlig data export kontrollerar du att aktivera **data export** är aktiverat. Välj **Spara**.

   ![Konfigurera kontinuerlig data export](media/howto-export-data-pnp/export-list-blob2.png)

9. Efter några minuter visas dina data i ditt lagrings konto.


## <a name="path-structure"></a>Sök vägs struktur

Telemetri-, enhets-och enhets data exporteras till ditt lagrings konto en gång per minut, med varje fil som innehåller ändrings gruppen sedan den senaste exporterade filen. Exporterade data placeras i tre mappar i JSON-format. Standard Sök vägarna i ditt lagrings konto är:
- Telemetri: {container}/{app-id}/telemetry/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}
- Enheter: {container}/{app-id}/devices/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}
- Enhets mallar: {container}/{app-id}/deviceTemplates/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}

Du kan bläddra bland de exporterade filerna i Azure Portal genom att gå till filen och välja fliken **Redigera BLOB** .

## <a name="data-format"></a>Dataformat
### <a name="telemetry"></a>Telemetri

De exporterade telemetridata har alla nya meddelanden som tagits emot av IoT Central från alla enheter under den tiden. De exporterade filerna använder samma format som de meddelandefiler som exporteras av [IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-csharp-csharp-process-d2c) meddelanderoutning till Blob Storage.

> [!NOTE]
> Se till att enheterna skickar meddelanden som har `contentType: application/JSON` och `contentEncoding:utf-8` (eller `utf-16`, `utf-32`). Se [IoT Hub-dokumentationen](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#message-routing-query-based-on-message-body) för ett exempel.

> [!NOTE]
> De enheter som skickar telemetri representeras av enhets-ID: n (se följande avsnitt). Exportera enhetens ögonblicks bilder för att hämta namnen på enheterna. Korrelera varje meddelande post med hjälp av **connectionDeviceId** som matchar enhets Postens **deviceId** .

I följande exempel visas en post i JSON-format.

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

När kontinuerlig data export först aktive ras, exporteras en enda ögonblicks bild med alla enheter. Varje enhet innehåller:
- `@id` av enheten i IoT Central
- `name` av enheten
- `deviceId` från [enhets etablerings tjänsten](https://aka.ms/iotcentraldocsdps)
- Information om enhets mal len
- Egenskapsvärden

En ny ögonblicks bild skrivs en gång per minut. Ögonblicks bilden innehåller:

- Nya enheter har lagts till sedan den senaste ögonblicks bilden.
- Enheter med ändrade egenskaps värden sedan den senaste ögonblicks bilden.

> [!NOTE]
> Enheter som har tagits bort sedan den senaste ögonblicks bilden exporteras inte. Ögonblicks bilderna har för närvarande inga indikatorer för borttagna enheter.
>
> Enhets mal len som varje enhet tillhör representeras av fältet `instanceOf`. Exportera enhets mal len ögonblicks bilder för att hämta namnet på enhets mal len.

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

### <a name="device-templates"></a>Enhetsmallar

När kontinuerlig data export har Aktiver ATS, exporteras en enda ögonblicks bild med alla enhets mallar. Varje enhets mall innehåller:
- `@id` av enhets mal len
- `name` av enhets mal len
- `version` av enhets mal len
- Enhets `capabilityModel`, inklusive dess `interfaces`, och definitioner för telemetri, egenskaper och kommandon
- `cloudProperties` definitioner
- Åsidosättningar och initiala värden, infogade med `capabilityModel`

En ny ögonblicks bild skrivs en gång per minut. Ögonblicks bilden innehåller:

- Nya enhetsspecifika mallar har lagts till sedan den senaste ögonblicks bilden. Detta inkluderar nya versioner av enhets mallar.
- Enhets mallar med ändrade åsidosättningar, initiala värden och definitioner för moln egenskaper sedan den senaste ögonblicks bilden.

> [!NOTE]
> Enhetsspecifika har tagits bort sedan den senaste ögonblicks bilden inte har exporter ATS. Ögonblicks bilderna har för närvarande inga indikatorer för borttagna enhetsspecifika mallar.

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

Nu när du vet hur du exporterar dina data kan du fortsätta till nästa steg:

> [!div class="nextstepaction"]
> [Använda IoT Central Device Bridge för att ansluta andra IoT-moln](howto-build-iotc-device-bridge.md)
