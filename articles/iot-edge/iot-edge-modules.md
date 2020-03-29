---
title: Lär dig hur moduler kör logik på dina enheter - Azure IoT Edge | Microsoft-dokument
description: Azure IoT Edge-moduler är behållarenheter av logik som kan distribueras och hanteras på distans så att du kan köra affärslogik på IoT Edge-enheter
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/21/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1c625e628f53d156ad56a1c69df1c23aec9120ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76548721"
---
# <a name="understand-azure-iot-edge-modules"></a>Förstå Azure IoT Edge-moduler

Med Azure IoT Edge kan du distribuera och hantera affärslogik på gränsen i form av *moduler*. Azure IoT Edge-moduler är den minsta beräkningsenheten som hanteras av IoT Edge och kan innehålla Azure-tjänster (till exempel Azure Stream Analytics) eller din egen lösningsspecifika kod. För att förstå hur moduler utvecklas, distribueras och underhålls, överväga de fyra begreppselementen i en modul:

* En **modulavbildning** är ett paket som innehåller programvaran som definierar en modul.
* En **modulinstans** är den specifika beräkningsenhet som kör modulavbildningen på en IoT Edge-enhet. Modulinstansen startas av IoT Edge-körningen.
* En **modulidentitet** är en information (inklusive säkerhetsreferenser) som lagras i IoT Hub och som är associerad med varje modulinstans.
* En **modultvilling** är ett JSON-dokument som lagras i IoT Hub och som innehåller tillståndsinformation för en modulinstans, inklusive metadata, konfigurationer och villkor.

## <a name="module-images-and-instances"></a>Modulavbildningar och instanser

IoT Edge-modulavbildningar innehåller program som utnyttjar hanterings-, säkerhets- och kommunikationsfunktionerna i IoT Edge-körningen. Du kan utveckla egna modulavbildningar eller exportera en från en Azure-tjänst som stöds, till exempel Azure Stream Analytics.
Avbildningarna finns i molnet och de kan uppdateras, ändras och distribueras i olika lösningar. Till exempel finns en modul som använder maskininlärning för att förutsäga produktionslinjeutdata som en separat avbildning än en modul som använder datorseende för att styra en drönare.

Varje gång en modulavbildning distribueras till en enhet och startas av IoT Edge-körningen skapas en ny instans av modulen. Två enheter i olika delar av världen kan använda samma modulbild. Varje enhet skulle dock ha en egen modulinstans när modulen startas på enheten.

![Diagram - Modulavbildningar i molnet, modulinstanser på enheter](./media/iot-edge-modules/image_instance.png)

I genomförandet finns moduler avbildningar som behållaravbildningar i en databas och modulinstanser är behållare på enheter.

<!--
As use cases for Azure IoT Edge grow, new types of module images and instances will be created. For example, resource constrained devices cannot run containers so may require module images that exist as dynamic link libraries and instances that are executables. 
-->

## <a name="module-identities"></a>Modulidentiteter

När en ny modulinstans skapas av IoT Edge-körningen får den en motsvarande modulidentitet. Modulidentiteten lagras i IoT Hub och används som adresserings- och säkerhetsomfång för all lokal och molnkommunikation för den modulinstansen.

Identiteten som är associerad med en modulinstans beror på identiteten på den enhet som instansen körs på och namnet som du anger till modulen i din lösning. Om du till `insight` exempel anropar en modul som använder en Azure `Hannover01`Stream Analytics och distribuerar den på en `/devices/Hannover01/modules/insight`enhet som kallas, skapar IoT Edge-körningen en motsvarande modulidentitet som kallas .

I scenarier när du behöver distribuera en modulavbildning flera gånger på samma enhet kan du distribuera samma avbildning flera gånger med olika namn.

![Diagram - Modulidentiteter är unika inom enheter och mellan enheter](./media/iot-edge-modules/identity.png)

## <a name="module-twins"></a>Modul tvillingar

Varje modulinstans har också en motsvarande modultvilling som du kan använda för att konfigurera modulinstansen. Instansen och tvillingen associeras med varandra via modulidentiteten.

En modultvilling är ett JSON-dokument som lagrar modulinformation och konfigurationsegenskaper. Detta koncept paralleller [enheten twin](../iot-hub/iot-hub-devguide-device-twins.md) konceptet från IoT Hub. Strukturen på en modultvilling är densamma som en enhetstvilling. Api:erna som används för att interagera med båda typerna av tvillingar är också desamma. Den enda skillnaden mellan de två är den identitet som används för att instansiera klienten SDK.

```csharp
// Create a ModuleClient object. This ModuleClient will act on behalf of a
// module since it is created with a module’s connection string instead
// of a device connection string.
ModuleClient client = new ModuleClient.CreateFromEnvironmentAsync(settings);
await client.OpenAsync();

// Get the module twin
Twin twin = await client.GetTwinAsync();
```

## <a name="offline-capabilities"></a>Offlinefunktioner

Azure IoT Edge-moduler kan fungera offline på obestämd tid efter synkronisering med IoT Hub minst en gång. IoT Edge-enheter kan också utöka den här offlinefunktionen till andra IoT-enheter. Mer information finns i [Förstå utökade offlinefunktioner för IoT Edge-enheter, moduler och underordnade enheter](offline-capabilities.md).

## <a name="next-steps"></a>Nästa steg

* [Förstå kraven och verktygen för att utveckla IoT Edge-moduler](module-development.md)
* [Förstå Azure IoT Edge-körningen och dess arkitektur](iot-edge-runtime.md)
