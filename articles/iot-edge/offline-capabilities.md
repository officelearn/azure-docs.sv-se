---
title: Azure IoT Edge-offlinefunktionerna | Microsoft Docs
description: Förstå hur IoT Edge-enheter och moduler kan köras offline under längre tid och hur IoT Edge kan aktivera vanliga IoT-enheter att fungera offline för.
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 09/20/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: f4afad753da4a314ade3fb7433c6be3e489e05b0
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47033693"
---
# <a name="understand-extended-offline-capabilities-for-iot-edge-devices-modules-and-child-devices-preview"></a>Förstå utökade offlinefunktionerna för IoT Edge-enheter, moduler och underordnade enheter (förhandsversion)

Azure IoT Edge stöder utökade offline åtgärder på IoT Edge-enheter och möjliggör offline åtgärder på icke-Edge underordnade enheter för. Så länge som en IoT Edge-enhet har haft en möjlighet att ansluta till IoT Hub, kan den och alla underordnade enheter fortsätta att funktionen med tillfälliga eller Ingen Internetanslutning. 

>[!NOTE]
>Offline stöd för IoT Edge är [förhandsversion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="how-it-works"></a>Hur det fungerar

När en IoT Edge-enheten försätts i offlineläge, tar Edge hub på tre roller. Först lagras alla meddelanden som skulle gå överordnade och de sparas tills enheten ska återansluta. Den fungerar dessutom för IoT Hub för att autentisera moduler och underordnade enheter så att de kan fortsätta att fungera. Det tredje möjliggör den kommunikation mellan underordnade enheter som normalt skulle gå via IoT Hub. 

I följande exempel visas hur en IoT Edge-scenariot fungerar i offline-läge:

1. **Konfigurera en IoT Edge-enhet.**

   IoT Edge-enheter har automatiskt offline möjliggörs. Om du vill utöka den här funktionen till andra IoT-enheter, måste du deklarera en överordnad-underordnad-relation mellan enheterna i IoT Hub. 

2. **Synkronisering med IoT Hub.**

   Minst en gång efter installation av IoT Edge-körningen IoT Edge-enheten måste vara online för att synkronisera med IoT Hub. I den här synkroniseringen hämtar information om alla underordnade enheter som har tilldelats för IoT Edge-enhet. IoT Edge-enhet uppdaterar den lokala cachen om du vill aktivera drift offline också på ett säkert sätt och hämtar inställningar för lokal lagring av telemetrimeddelanden. 

3. **Frånkopplas.**

   Under frånkoppling från IoT Hub, kan IoT Edge-enhet, dess distribuerade moduler och alla underordnade IoT-enheter fungera på obestämd tid. Moduler och underordnade enheter kan starta och starta om genom att autentisera med Edge hub när offline. Telemetri bunden uppströms till IoT Hub lagras lokalt. Kommunikation mellan moduler eller mellan underordnade IoT-enheter underhålls via direkta metoder eller meddelanden. 

4. **Återanslut och synkroniserar med IoT Hub.**

   När anslutningen med IoT Hub återställs synkroniserar IoT Edge-enheten igen. Lokalt lagrade meddelanden levereras i samma ordning som de har lagrats. Eventuella skillnader mellan önskade och rapporterade egenskaper för moduler och enheter har stämts av. IoT Edge-enhet uppdaterar alla ändringar till en uppsättning tilldelade underordnade IoT-enheter.

## <a name="restrictions-and-limits"></a>Begränsningar och gränser

De utökade offline funktioner som beskrivs i den här artikeln är tillgängliga i [IoT Edge version 1.0.2 eller högre](https://github.com/Azure/azure-iotedge/releases). Tidigare versioner har en delmängd av Offlinefunktioner. Befintliga IoT Edge-enheter som inte har utökat offlinefunktionerna kan inte uppgraderas genom att ändra körningsversion, men måste konfigureras med en ny enhetsidentitet i IoT Edge att få dessa funktioner. 

Utökad offline support är tillgänglig i alla regioner där IoT Hub inte är tillgängligt, östra USA och Västeuropa. 

Icke - Edge IoT-enheter kan läggas till som underordnade enheter. 

IoT Edge-enheter och deras tilldelade underordnade enheter kan fungera offline under obestämd tid efter den första, enstaka synkroniseringen. Lagring av meddelanden beror dock på time to live (TTL) inställningen och allt tillgängligt diskutrymme för att lagra meddelanden. 

## <a name="set-up-an-edge-device"></a>Konfigurera en Edge-enhet

Konfigurera IoT Edge-körningen att kommunicera över MQTT för alla IoT Edge-enheter som du vill utföra under en längre period som offline och. 

För IoT Edge-enhet att utöka funktionerna för utökade offline underordnade IoT-enheter, måste du deklarera de överordnade och underordnade relationerna i Azure-portalen.

### <a name="set-the-upstream-protocol-to-mqtt"></a>Ställ in överordnade protokollet till MQTT

Konfigurera både Edge hub och Edge-agenten kan kommunicera med MQTT som överordnad protokoll. Det här protokollet deklareras med miljövariabler i manifestet distribution. 

I Azure-portalen, du kan komma åt Edge hub- och moduldefinitioner för Edge-agenten genom att välja den **konfigurera avancerade Edge-körningsinställningar** knappen när du ställer in moduler för en distribution. Skapa en miljövariabel som heter för båda modulerna **UpstreamProtocol** och ange värdet **MQTT**. 

I distributionsmallen JSON deklareras miljövariabler som visas i följande exempel: 

```json
"edgeHub": {
    "type": "docker",
    "settings": {
        "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
        "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}]}}}"
    },
    "env": {
        "UpstreamProtocol": {
            "value": "MQTT"
        }
    },
    "status": "running",
    "restartPolicy": "always"
}
```

### <a name="assign-child-devices"></a>Tilldela underordnade enheter

Underordnade enheter kan vara vilken-Edge-enhet som registrerats till samma IoT Hub. Du kan hantera överordnad-underordnad-relation om hur du skapar en ny enhet eller från sidan med enhetsinformation för antingen överordnat IoT Edge-enhet eller underordnade IoT-enheter. 

   ![Hantera underordnade enheter från sidan IoT Edge-enhet](./media/offline-capabilities/manage-child-devices.png)

Överordnade enheter kan ha flera underordnade enheter, men en underordnad enhet kan bara ha en överordnad.

## <a name="optional-offline-settings"></a>Valfria inställningar för offline

Om du räknar med dina enheter att uppleva länge offline perioder, efter vilken du vill samla in alla meddelanden som har skapats kan du konfigurera Edge hub så att den kan lagra alla meddelanden. Det finns två ändringar att du kan göra att Edge hub att aktivera långsiktig lagring av meddelanden. Först öka time to live inställningen och sedan lägga till ytterligare diskutrymme för att lagra meddelanden. 

### <a name="time-to-live"></a>Time to live

Time to live inställningen är hur lång tid (i sekunder) som ett meddelande kan vänta som ska levereras innan den upphör. Standardvärdet är 7200 sekunder (två timmar). 

Den här inställningen är en önskad egenskap för Edge hub, som lagras i modultvillingen. Du kan konfigurera det i Azure-portalen i den **konfigurera avancerade Edge-körningsinställningar** avsnittet eller direkt i distributionen manifest. 

```json
"$edgeHub": {
    "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {},
        "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
        }
    }
}
```

### <a name="additional-offline-storage"></a>Ytterligare offlinelagringsplats

Som standard lagras meddelanden i Edge hub behållare filsystem. Om den mängden lagringsutrymme som inte är tillräckligt för behoven offline, kan du tilldela lokal lagring på IoT Edge-enhet. Du behöver skapa en miljövariabel för Edge hub som pekar på en mapp i behållaren. Använd sedan skapa-alternativen för att binda den storage-mappen till en mapp på värddatorn. 

Du kan konfigurera miljövariabler och skapa alternativen för Edge hub-modul i Azure-portalen i den **konfigurera avancerade Edge-körningsinställningar** avsnittet. Eller du kan konfigurera det direkt i manifestet distribution. 

```json
"edgeHub": {
    "type": "docker",
    "settings": {
        "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
        "createOptions": "{\"HostConfig\":{\"Binds\":[\"C:\\\\HostStoragePath:C:\\\\ModuleStoragePath\"],\"PortBindings\":{\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}]}}}"
    },
    "env": {
        "storageFolder": {
            "value": "C:\\\\ModuleStoragePath"
        }
    },
    "status": "running",
    "restartPolicy": "always"
}
```

## <a name="next-steps"></a>Nästa steg

Aktivera utökad offline åtgärder i dina transparent gatewayscenarier för [Linux](how-to-create-transparent-gateway-linux.md) eller [Windows](how-to-create-transparent-gateway-windows.md) enheter. 