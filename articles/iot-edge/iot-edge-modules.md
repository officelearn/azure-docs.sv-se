---
title: "Förstå Azure IoT kant moduler | Microsoft Docs"
description: "Lär dig mer om Azure IoT kant-moduler och hur de konfigureras"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 10/05/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 726bbafa9e4ba35cfa4a9cbf4d89056d52fe7963
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2017
---
# <a name="understand-azure-iot-edge-modules---preview"></a>Förstå Azure IoT kant moduler – förhandsgranskning

Azure IoT-gräns kan du distribuera och hantera affärslogik på gränsen i form av *moduler*. Azure IoT kant-moduler är den minsta enheten av beräkning som hanteras av IoT kant och kan innehålla Azure-tjänster (till exempel Azure Stream Analytics) eller din egen lösning-specifik kod. För att förstå hur moduler har utvecklats, distribueras och hanteras, hjälper det för att tänka på fyra konceptuella delarna som utgör en modul:

* En **modulen avbildningen** är ett paket som innehåller programvaran som definierar en modul.
* En **modulinstans** är en specifik enhet beräkning körs modulen avbildningen på en IoT-enhet. Modul-instansen har startats av IoT kant-körningen.
* En **modulen identitet** är en typ av information (inklusive säkerhetsreferenser) lagras i IoT-hubb som är kopplad till varje modulinstans.
* En **modulen dubbla** är en JSON-dokument som lagras i IoT-hubb som innehåller tillståndsinformation om för en modulinstans, inklusive metadata, konfigurationer och villkor. 

## <a name="module-images-and-instances"></a>Modulen avbildningar och instanser

IoT-Edge modulen bilder innehåller program som utnyttjar hantering, säkerhet och funktioner för kommunikation av körningsmiljön IoT kant. Du kan utveckla modulen bilder eller exportera ett från en stöds Azure-tjänst, till exempel Azure Stream Analytics.
Bilderna finns i molnet och de kan uppdateras, ändras och distribueras i olika lösningar. En modul som använder maskininlärning att förutsäga produktionen utdata finns till exempel som en separat avbildning än en modul som använder datorn vision för att styra en drone. 

Varje gång en modul-avbildning distribueras till en enhet och startas av IoT kant-körningsmiljön skapas en ny instans av modulen. Två enheter i olika delar av världen kan använda samma modul bilden. varje skulle men har sina egna modulinstans när modulen har startats på enheten. 

![Modulen bilder i cloud - modulen instanser på enheter][1]

Moduler avbildningar finns som behållare bilder i en databas i implementering och modulen instanser är behållare på enheter. Eftersom användningsområden för Azure IoT kant växer, skapas nya typer av modulen avbildningar och instanser. Till exempel kan inte resursen begränsad enheter köra behållare så måste modulen avbildningar som finns som dynamiska länkbibliotek och instanser som är körbara filer. 

## <a name="module-identities"></a>Modulen identiteter

När en ny modulinstans skapas av IoT kant körningen är instansen associerad med en motsvarande modulen identitet. Modulen identitet lagras i IoT-hubb och anställda som omfång för adressering och säkerhet för alla lokala och molnet kommunikation för modul instansen.
Identiteten som är associerad med en modulinstans beroende på användarens identitet till enheten där instansen körs och namnet du anger för att modulen i din lösning. Till exempel om du anropar `insight` en modul som använder en Azure Stream Analytics och distribuera den på en enhet som kallas `Hannover01`, IoT kant runtime skapar en motsvarande modulen identitet som kallas `/devices/Hannover01/modules/insight`.

Tydligt i situationer kan när du behöver distribuera en modul bild flera gånger på samma enhet, du distribuera samma avbildning flera gånger med olika namn.

![Modulen identiteter är unikt][2]

## <a name="module-twins"></a>Modulen twins

Varje modulinstans har även en motsvarande modulen dubbla som du kan använda för att konfigurera modulinstans. Instansen och dubbla är kopplade till varandra via module-identitet. 

En modul dubbla är ett JSON-dokument som innehåller modulen och konfigurationsinformation egenskaper. Detta begrepp parallels den [enheten dubbla] [ lnk-device-twin] konceptet från IoT-hubb. Struktur för en modul dubbla är exakt detsamma som en enhet dubbla. API: er som används för att interagera med båda typerna av twins är också samma. Den enda skillnaden mellan två är det identitet som används för att skapa en instans av klient-SDK. 

```
// Create a DeviceClient object. This DeviceClient will act on behalf of a 
// module since it is created with a module’s connection string instead 
// of a device connection string. 
DeviceClient client = new DeviceClient.CreateFromConnectionString(moduleConnectionString, settings); 
await client.OpenAsync(); 
 
// Get the model twin 
Twin twin = await client.GetTwinAsync(); 
```

## <a name="next-steps"></a>Nästa steg
 - [Förstå Azure IoT kant-runtime och dess arkitektur][lnk-runtime]

<!-- Images -->
[1]: ./media/iot-edge-modules/image_instance.png
[2]: ./media/iot-edge-modules/identity.png

<!-- Links -->
[lnk-device-identity]: ../iot-hub/iot-hub-devguide-identity-registry.md
[lnk-device-twin]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-runtime]: iot-edge-runtime.md