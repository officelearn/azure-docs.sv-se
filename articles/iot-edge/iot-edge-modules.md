---
title: Lär dig hur moduler köras logik på dina enheter – Azure IoT Edge | Microsoft Docs
description: Azure IoT Edge-moduler är behållare enheter som kan distribueras och hanteras via fjärranslutning så att du kan köra affärslogik på IoT Edge enheter
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/21/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 744a901c6b0260f4fc14a2f06b88dfb36973b0f8
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456580"
---
# <a name="understand-azure-iot-edge-modules"></a>Förstå Azure IoT Edge-moduler

Med Azure IoT Edge kan du distribuera och hantera affärs logik på kanten i form av *moduler*. Azure IoT Edge-moduler är den minsta beräkningsenhet hanteras av IoT Edge och kan innehålla Azure-tjänster (till exempel Azure Stream Analytics) eller din egen Lösningsspecifika kod. För att förstå hur moduler utvecklas, distribueras och underhålls, är det bra att tänka på de fyra koncept elementen i en modul:

* En **modul avbildning** är ett paket som innehåller den program vara som definierar en modul.
* En **modul instans** är den enhet av beräkning som kör-modulens avbildning på en IoT Edge enhet. Modul-instansen har startats med IoT Edge-körningen.
* En **modul identitet** är en del av informationen (inklusive säkerhets referenser) som lagras i IoT Hub, som är kopplad till varje instans i modulen.
* En **modul med dubbla** är ett JSON-dokument som lagras i IoT Hub, som innehåller Tillståndsinformation för en modulreferens, inklusive metadata, konfigurationer och villkor. 

## <a name="module-images-and-instances"></a>Modulen avbildningar och instanser

IoT Edge-modulen avbildningar innehåller program som drar nytta av hantering, säkerhet och kommunikation funktionerna i IoT Edge-körningen. Du kan utveckla dina egna avbildningar för modulen eller exportera en från en stöds Azure-tjänst, till exempel Azure Stream Analytics.
Bilderna finns i molnet och de kan uppdateras, ändras och distribueras i olika lösningar. Exempelvis kan finns en modul som använder maskininlärning att förutsäga produktionslinje utdata som en separat bild än en modul som används för visuellt innehåll för att styra en drönare. 

Varje gång en avbildning av en modul har distribuerats till en enhet och startas av IoT Edge-körningen skapas en ny instans av modulen. Två enheter i olika delar av världen kan använda samma modul-avbildning. Varje enhet skulle dock ha sin egen modul-instans när modulen startas på enheten. 

![Diagram - modulen bilder i molnet, modulen instanser på enheter](./media/iot-edge-modules/image_instance.png)

Moduler bilder finns som behållaravbildningar i en databas i implementering och modulen instanser är behållare på enheter. 

<!--
As use cases for Azure IoT Edge grow, new types of module images and instances will be created. For example, resource constrained devices cannot run containers so may require module images that exist as dynamic link libraries and instances that are executables. 
-->

## <a name="module-identities"></a>Modulen identiteter

När en ny modulinstans skapas av IoT Edge-körningen, är instansen associerad med en motsvarande modul-identitet. Modulen identiteten lagras i IoT Hub och används som omfång för adressering och säkerhet för alla lokala och molnet kommunikation för den specifika modul-instansen.

Identiteten som är associerade med en modulinstans beror på identiteten för enheten på-instansen körs vilket namn som du tillhandahåller denna modul i din lösning. Om du till exempel anropar `insight` en modul som använder en Azure Stream Analytics och distribuerar den på en enhet som kallas `Hannover01`, skapar IoT Edge runtime en motsvarande modul-ID som kallas `/devices/Hannover01/modules/insight`.

Uppenbarligen i scenarier kan när du behöver distribuera en modul bild flera gånger på samma enhet, du distribuera samma avbildning flera gånger med olika namn.

![Diagram - modulen identiteter är unika inom enheter och mellan enheter](./media/iot-edge-modules/identity.png)

## <a name="module-twins"></a>Modultvillingar

Varje modulinstans har också en motsvarande modultvilling som du kan använda för att konfigurera modulinstans. Instansen och läsningen är associerade med varandra via modulen identiteten. 

En modultvilling är ett JSON-dokument som lagrar egenskaper för information och konfiguration av modulen. Det här konceptet parallellt av [enhetens dubbla](../iot-hub/iot-hub-devguide-device-twins.md) koncept från IoT Hub. Strukturen i en modul är på samma sätt som en enhet, med dubbla. API: er som används för att interagera med båda typerna av twins är också desamma. Den enda skillnaden mellan två är den identitet som används för att skapa en instans av klient-SDK. 

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

Azure IoT Edge moduler kan köras offline i oändlighet efter synkronisering med IoT Hub minst en gång. IoT Edge-enheter kan också utöka den här offline-funktionen till andra IoT-enheter. Mer information finns i [förstå utökade offline-funktioner för IoT Edge enheter, moduler och underordnade enheter](offline-capabilities.md).

## <a name="next-steps"></a>Nästa steg
 - [Förstå kraven och verktygen för att utveckla IoT Edge moduler](module-development.md)
 - [Förstå Azure IoT Edge Runtime och dess arkitektur](iot-edge-runtime.md)

