---
title: Använd enheter offline – Azure IoT Edge | Microsoft Docs
description: Förstå hur IoT Edge-enheter och moduler kan användas utan internet-anslutning för längre tid och hur IoT Edge kan aktivera vanliga IoT-enheter att fungera offline för.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/30/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 7bf672715b45233807ab848c78aeb1bed2d352e9
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2019
ms.locfileid: "55699354"
---
# <a name="understand-extended-offline-capabilities-for-iot-edge-devices-modules-and-child-devices-preview"></a>Förstå utökade offlinefunktionerna för IoT Edge-enheter, moduler och underordnade enheter (förhandsversion)

Azure IoT Edge stöder utökade offline åtgärder på IoT Edge-enheter och möjliggör offline åtgärder på icke-Edge underordnade enheter för. Så länge som en IoT Edge-enhet har haft en möjlighet att ansluta till IoT Hub, kan den och alla underordnade enheter fortsätta att funktionen med tillfälliga eller Ingen Internetanslutning. 

>[!NOTE]
>Offline stöd för IoT Edge är [förhandsversion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="how-it-works"></a>Hur det fungerar

När en IoT Edge-enheten försätts i offlineläge, tar IoT Edge hub på tre roller. Först lagras alla meddelanden som skulle gå överordnade och de sparas tills enheten ska återansluta. Den fungerar dessutom för IoT Hub för att autentisera moduler och underordnade enheter så att de kan fortsätta att fungera. Det tredje möjliggör den kommunikation mellan underordnade enheter som normalt skulle gå via IoT Hub. 

I följande exempel visas hur en IoT Edge-scenariot fungerar i offline-läge:

1. **Konfigurera en IoT Edge-enhet.**

   IoT Edge-enheter har automatiskt offline möjliggörs. Om du vill utöka den här funktionen till andra IoT-enheter, måste du deklarera en överordnad-underordnad-relation mellan enheterna i IoT Hub. 

2. **Synkronisering med IoT Hub.**

   Minst en gång efter installation av IoT Edge-körningen IoT Edge-enheten måste vara online för att synkronisera med IoT Hub. I den här synkroniseringen hämtar information om alla underordnade enheter som har tilldelats för IoT Edge-enhet. IoT Edge-enhet uppdaterar den lokala cachen om du vill aktivera drift offline också på ett säkert sätt och hämtar inställningar för lokal lagring av telemetrimeddelanden. 

3. **Frånkopplas.**

   Under frånkoppling från IoT Hub, kan IoT Edge-enhet, dess distribuerade moduler och alla underordnade IoT-enheter fungera på obestämd tid. Moduler och underordnade enheter kan starta och starta om genom att autentisera med IoT Edge hub när offline. Telemetri bunden uppströms till IoT Hub lagras lokalt. Kommunikation mellan moduler eller mellan underordnade IoT-enheter underhålls via direkta metoder eller meddelanden. 

4. **Anslut och synkronisera om med IoT Hub.**

   När anslutningen med IoT Hub återställs synkroniserar IoT Edge-enheten igen. Lokalt lagrade meddelanden levereras i samma ordning som de har lagrats. Eventuella skillnader mellan önskade och rapporterade egenskaper för moduler och enheter har stämts av. IoT Edge-enhet uppdaterar alla ändringar till en uppsättning tilldelade underordnade IoT-enheter.

## <a name="restrictions-and-limits"></a>Begränsningar och gränser

De utökade offline funktioner som beskrivs i den här artikeln är tillgängliga i [IoT Edge version 1.0.4 eller högre](https://github.com/Azure/azure-iotedge/releases). Tidigare versioner har en delmängd av Offlinefunktioner. Befintliga IoT Edge-enheter som inte har utökat offlinefunktionerna kan inte uppgraderas genom att ändra körningsversion, men måste konfigureras med en ny enhetsidentitet i IoT Edge att få dessa funktioner. 

Utökad offline support är tillgänglig i alla regioner där IoT Hub är tillgänglig, **utom** östra USA.

Icke - Edge IoT-enheter kan läggas till som underordnade enheter. 

IoT Edge-enheter och deras tilldelade underordnade enheter kan fungera offline under obestämd tid efter den första, enstaka synkroniseringen. Lagring av meddelanden beror dock på time to live (TTL) inställningen och allt tillgängligt diskutrymme för att lagra meddelanden. 

## <a name="set-up-an-iot-edge-device"></a>Konfigurera en IoT Edge-enhet

För IoT Edge-enhet att utöka funktionerna för utökade offline underordnade IoT-enheter, måste du deklarera de överordnade och underordnade relationerna i Azure-portalen.

### <a name="assign-child-devices"></a>Tilldela underordnade enheter

Underordnade enheter kan vara vilken-Edge-enhet som registrerats till samma IoT Hub. Du kan hantera överordnad-underordnad-relation om hur du skapar en ny enhet eller från sidan med enhetsinformation för antingen överordnat IoT Edge-enhet eller underordnade IoT-enheter. 

   ![Hantera underordnade enheter från sidan IoT Edge-enhet](./media/offline-capabilities/manage-child-devices.png)

Överordnade enheter kan ha flera underordnade enheter, men en underordnad enhet kan bara ha en överordnad.

### <a name="specifying-dns-servers"></a>Ange DNS-servrar 

Förbättrad stabilitet, bör du ange DNS-serveradresser som används i din miljö. Till exempel på Linux, uppdatera **/etc/docker/daemon.json** (du kan behöva skapa filen) att inkludera:

```json
{
    "dns": [“1.1.1.1”]
}
```

Om du använder en lokal DNS-server, ersätter du 1.1.1.1 med IP-adressen för den lokala DNS-servern. Starta om docker-tjänsten för att ändringarna ska börja gälla.


## <a name="optional-offline-settings"></a>Valfria inställningar för offline

Om du förväntar dig att samla in alla meddelanden som dina enheter som genererar under lång tid offline perioder, konfigurera IoT Edge hub så att den kan lagra alla meddelanden. Det finns två ändringar att du kan göra till IoT Edge hub att aktivera långsiktig lagring av meddelanden. Först öka time to live inställningen. Sedan kan lägga till ytterligare diskutrymme för att lagra meddelanden. 

### <a name="time-to-live"></a>Time to live

Time to live inställningen är hur lång tid (i sekunder) som ett meddelande kan vänta som ska levereras innan den upphör. Standardvärdet är 7200 sekunder (två timmar). 

Den här inställningen är en önskad egenskap för IoT Edge-hubben, som lagras i modultvillingen. Du kan konfigurera det i Azure-portalen i den **konfigurera avancerade Edge-körningsinställningar** avsnittet eller direkt i distributionen manifest. 

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

Meddelanden lagras som standard i IoT Edge-hubben behållare filsystem. Om den mängden lagringsutrymme som inte är tillräckligt för behoven offline, kan du tilldela lokal lagring på IoT Edge-enhet. Skapa en miljövariabel för IoT Edge-hubben som pekar på en mapp i behållaren. Använd sedan skapa-alternativen för att binda den storage-mappen till en mapp på värddatorn. 

Du kan konfigurera miljövariabler och skapa alternativen för IoT Edge hub-modul i Azure-portalen i den **konfigurera avancerade Edge-körningsinställningar** avsnittet. Eller du kan konfigurera det direkt i manifestet distribution. 

```json
"edgeHub": {
    "type": "docker",
    "settings": {
        "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
        "createOptions": {
            "HostConfig": {
                "Binds": ["<HostStoragePath>:<ModuleStoragePath>"],
                "PortBindings": {
                    "8883/tcp": [{"HostPort":"8883"}],
                    "443/tcp": [{"HostPort":"443"}],
                    "5671/tcp": [{"HostPort":"5671"}]
                }
            }
        }
    },
    "env": {
        "storageFolder": {
            "value": "<ModuleStoragePath>"
        }
    },
    "status": "running",
    "restartPolicy": "always"
}
```

Ersätt `<HostStoragePath>` och `<ModuleStoragePath>` med värden och modulen lagringen sökväg; både värden och modulen lagringssökväg måste vara en absolut sökväg. Binda lagringssökvägar värden och modulen tillsammans i Skapa-alternativen. Skapa sedan en miljövariabel som pekar på lagringssökväg modulen.  

Till exempel `"Binds":["/etc/iotedge/storage/:/iotedge/storage/"]` innebär att katalogen **/etc/iotedge/storage** på din värd system mappas till katalogen **/iotedge/storage/** för behållaren. Eller ett annat exempel för Windows-System, `"Binds":["C:\\temp:C:\\contemp]"` innebär att katalogen **C:\\temp** på din värd system mappas till katalogen **C:\\contemp** för behållaren. 

Du kan också hitta mer information om alternativ från för att skapa [docker docs](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate).

## <a name="next-steps"></a>Nästa steg

Aktivera utökad offline åtgärder i din [transparent gateway](how-to-create-transparent-gateway.md) scenarier.
