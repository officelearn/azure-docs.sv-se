---
title: Lär dig hur moduler köras logik på dina enheter – Azure IoT Edge | Microsoft Docs
description: Azure IoT Edge-moduler är behållare enheter som kan distribueras och hanteras via fjärranslutning så att du kan köra affärslogik på IoT Edge enheter
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 09/21/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 90fb6eadb2edb92d4516d8565d8c2c2bd5120c05
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53094193"
---
# <a name="understand-azure-iot-edge-modules"></a>Förstå Azure IoT Edge-moduler

Azure IoT Edge kan du distribuera och hantera affärslogik på gränsen i form av *moduler*. Azure IoT Edge-moduler är den minsta beräkningsenhet hanteras av IoT Edge och kan innehålla Azure-tjänster (till exempel Azure Stream Analytics) eller din egen Lösningsspecifika kod. Att förstå hur moduler har utvecklats, distribueras och hanteras, hjälper det för att tänka på fyra konceptuella delar som utgör en modul:

* En **modulen bild** är ett paket som innehåller den programvara som definierar en modul.
* En **modulinstans** är den specifika beräkningsenhet körs modulen avbildningen på en IoT Edge-enhet. Modul-instansen har startats med IoT Edge-körningen.
* En **modulen identitet** är en del av informationen (inklusive säkerhetsreferenser) lagras i IoT Hub som är kopplad till varje modulinstans.
* En **modultvilling** är ett JSON-dokument som lagras i IoT Hub, som innehåller information om tillstånd för en modulinstans, inklusive metadata, konfigurationer och villkor. 

## <a name="module-images-and-instances"></a>Modulen avbildningar och instanser

IoT Edge-modulen avbildningar innehåller program som drar nytta av hantering, säkerhet och kommunikation funktionerna i IoT Edge-körningen. Du kan utveckla dina egna avbildningar för modulen eller exportera en från en stöds Azure-tjänst, till exempel Azure Stream Analytics.
Bilderna finns i molnet och de kan uppdateras, ändras och distribueras i olika lösningar. Exempelvis kan finns en modul som använder maskininlärning att förutsäga produktionslinje utdata som en separat bild än en modul som används för visuellt innehåll för att styra en drönare. 

Varje gång en avbildning av en modul har distribuerats till en enhet och startas av IoT Edge-körningen skapas en ny instans av modulen. Två enheter i olika delar av världen kan använda samma avbildning som modulen; men skulle var och en har sina egna modulinstans när modulen har startats på enheten. 

![Diagram - modulen bilder i molnet, modulen instanser på enheter](./media/iot-edge-modules/image_instance.png)

Moduler bilder finns som behållaravbildningar i en databas i implementering och modulen instanser är behållare på enheter. 

<!--
As use cases for Azure IoT Edge grow, new types of module images and instances will be created. For example, resource constrained devices cannot run containers so may require module images that exist as dynamic link libraries and instances that are executables. 
-->

## <a name="module-identities"></a>Modulen identiteter

När en ny modulinstans skapas av IoT Edge-körningen, är instansen associerad med en motsvarande modul-identitet. Modulen identiteten lagras i IoT Hub och används som omfång för adressering och säkerhet för alla lokala och molnet kommunikation för den specifika modul-instansen.
Identiteten som är associerade med en modulinstans beror på identiteten för enheten på-instansen körs vilket namn som du tillhandahåller denna modul i din lösning. Till exempel om du anropar `insight` en modul som använder en Azure Stream Analytics och distribuera den på en enhet med namnet `Hannover01`, IoT Edge-körningen skapas en motsvarande modulen identitet som kallas `/devices/Hannover01/modules/insight`.

Uppenbarligen i scenarier kan när du behöver distribuera en modul bild flera gånger på samma enhet, du distribuera samma avbildning flera gånger med olika namn.

![Diagram - modulen identiteter är unika inom enheter och mellan enheter](./media/iot-edge-modules/identity.png)

## <a name="module-twins"></a>Modultvillingar

Varje modulinstans har också en motsvarande modultvilling som du kan använda för att konfigurera modulinstans. Instansen och läsningen är associerade med varandra via modulen identiteten. 

En modultvilling är ett JSON-dokument som lagrar egenskaper för information och konfiguration av modulen. Det här konceptet parallels den [enhetstvillingen](../iot-hub/iot-hub-devguide-device-twins.md) konceptet från IoT Hub. Strukturen för en modultvilling är exakt samma som en enhetstvilling. API: er som används för att interagera med båda typerna av twins är också desamma. Den enda skillnaden mellan två är den identitet som används för att skapa en instans av klient-SDK. 

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

Azure IoT Edge stöder offline åtgärder på IoT Edge-enheter. De här funktionerna är begränsade för tillfället. 

IoT Edge-moduler kan vara offline under långa perioder, förutsatt att följande krav är uppfyllda: 

* **Meddelandet time to live (TTL) har inte upphört att gälla**. Standardvärdet för meddelande TTL är två timmar, kan men vara ändrade högre eller lägre i Store och vidarebefordra konfiguration i IoT Edge hub-inställningar. 
* **Moduler som inte behöver autentiseras på nytt med IoT Edge hub när du är offline**. Moduler kan endast autentisera med Edge-hubbar som har en aktiv anslutning till en IoT-hubb. Moduler som behöver autentisera igen om de startas om av någon anledning. Moduler kan fortfarande skicka meddelanden till Edge hub när deras SAS-token har upphört att gälla. När anslutningen återställs Edge hub begär en ny token från modulen och validerar den med IoT hub. Om detta lyckas vidarebefordrar Edge hub modulen meddelanden lagras, även de meddelanden som skickades när modulens token har upphört att gälla. 
* **Den modul som skickade meddelanden när offline fungerar fortfarande när anslutningen återställs**. Edge hub behöver verifiera en ny modul-token (om det tidigare har gått ut) innan den kan vidarebefordra meddelandena som modulen när de återansluter till IoT Hub. Om modulen inte är tillgängliga för att tillhandahålla en ny token kan inte Edge hub fungera på modulens lagrade meddelanden. 
* **Edge hub har ledigt diskutrymme för att lagra meddelanden**. Som standard lagras meddelanden i Edge hub behållarens filsystem. Det finns ett konfigurationsalternativ för att ange en monterad volym för att lagra meddelanden i stället. I båda fallen måste det finnas utrymme att lagra meddelanden för uppskjutna leverans till IoT Hub.  

Ytterligare funktioner som är offline finns i offentlig förhandsversion. Mer information finns i [förstå utökat offlinefunktionerna för IoT Edge-enheter, moduler och underordnade enheter](offline-capabilities.md).

## <a name="next-steps"></a>Nästa steg
 - [Förstå de krav och verktyg för att utveckla IoT Edge-moduler](module-development.md)
 - [Förstå Azure IoT Edge-körningen och dess arkitektur](iot-edge-runtime.md)

<!-- Images -->
[2]: ./media/iot-edge-modules/identity.png
