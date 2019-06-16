---
title: Använd enheter offline – Azure IoT Edge | Microsoft Docs
description: Förstå hur IoT Edge-enheter och moduler kan användas utan internet-anslutning för längre tid och hur IoT Edge kan aktivera vanliga IoT-enheter att fungera offline för.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/04/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 4f3e5c1566271573b43e24a1749b42daa7530555
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67051961"
---
# <a name="understand-extended-offline-capabilities-for-iot-edge-devices-modules-and-child-devices"></a>Förstå utökade offlinefunktionerna för IoT Edge-enheter, moduler och underordnade enheter

Azure IoT Edge stöder utökade offline åtgärder på IoT Edge-enheter och möjliggör offline åtgärder på IoT Edge underordnade enheter för. Så länge som en IoT Edge-enhet har haft en möjlighet att ansluta till IoT Hub, kan den och alla underordnade enheter fortsätta att funktionen med tillfälliga eller Ingen Internetanslutning. 


## <a name="how-it-works"></a>Hur det fungerar

När en IoT Edge-enheten försätts i offlineläge, tar IoT Edge hub på tre roller. Först lagras alla meddelanden som skulle gå överordnade och de sparas tills enheten ska återansluta. Den fungerar dessutom för IoT Hub för att autentisera moduler och underordnade enheter så att de kan fortsätta att fungera. Det tredje möjliggör den kommunikation mellan underordnade enheter som normalt skulle gå via IoT Hub. 

I följande exempel visas hur en IoT Edge-scenariot fungerar i offline-läge:

1. **Konfigurera enheter**

   IoT Edge-enheter har automatiskt offline möjliggörs. Om du vill utöka den här funktionen till andra IoT-enheter, måste du deklarera en överordnad-underordnad-relation mellan enheterna i IoT Hub. Sedan kan konfigurera du de underordnade enheterna att lita på deras tilldelade överordnade enheten och skicka enhet-till-molnet kommunikation via överordnat som en gateway. 

2. **Synkronisering med IoT Hub**

   Minst en gång efter installation av IoT Edge-körningen IoT Edge-enheten måste vara online för att synkronisera med IoT Hub. I den här synkroniseringen hämtar information om alla underordnade enheter som har tilldelats för IoT Edge-enhet. IoT Edge-enhet uppdaterar den lokala cachen om du vill aktivera drift offline också på ett säkert sätt och hämtar inställningar för lokal lagring av telemetrimeddelanden. 

3. **Arbeta offline**

   Under frånkoppling från IoT Hub, kan IoT Edge-enhet, dess distribuerade moduler och alla underordnade IoT-enheter fungera på obestämd tid. Moduler och underordnade enheter kan starta och starta om genom att autentisera med IoT Edge hub när offline. Telemetri bunden uppströms till IoT Hub lagras lokalt. Kommunikation mellan moduler eller mellan underordnade IoT-enheter underhålls via direkta metoder eller meddelanden. 

4. **Anslut och synkronisera om med IoT Hub**

   När anslutningen med IoT Hub återställs synkroniserar IoT Edge-enheten igen. Lokalt lagrade meddelanden levereras i samma ordning som de har lagrats. Eventuella skillnader mellan önskade och rapporterade egenskaper för moduler och enheter har stämts av. IoT Edge-enhet uppdaterar alla ändringar till en uppsättning tilldelade underordnade IoT-enheter.

## <a name="restrictions-and-limits"></a>Begränsningar och gränser

De utökade offline funktioner som beskrivs i den här artikeln är tillgängliga i [IoT Edge version 1.0.4 eller högre](https://github.com/Azure/azure-iotedge/releases). Tidigare versioner har en delmängd av Offlinefunktioner. Befintliga IoT Edge-enheter som inte har utökat offlinefunktionerna kan inte uppgraderas genom att ändra körningsversion, men måste konfigureras med en ny enhetsidentitet i IoT Edge att få dessa funktioner. 

Utökad offline support är tillgänglig i alla regioner där IoT Hub är tillgänglig, **utom** östra USA.

IoT Edge-enheter kan läggas till som underordnade enheter. 

IoT Edge-enheter och deras tilldelade underordnade enheter kan fungera offline under obestämd tid efter den första, enstaka synkroniseringen. Lagring av meddelanden beror dock på time to live (TTL) inställningen och allt tillgängligt diskutrymme för att lagra meddelanden. 

## <a name="set-up-parent-and-child-devices"></a>Konfigurera överordnade och underordnade enheter

För IoT Edge-enhet att utöka funktionerna för utökade offline underordnade IoT-enheter, måste du utföra två steg. Först måste deklarera överordnade och underordnade relationer i Azure-portalen. Sedan skapar en förtroenderelation mellan den överordnade enheten och alla underordnade enheter och sedan konfigurera enheten till molnet kommunikation gå igenom överordnat som en gateway. 

### <a name="assign-child-devices"></a>Tilldela underordnade enheter

Underordnade enheter kan vara vilken IoT Edge-enhet som registrerats till samma IoT Hub. Överordnade enheter kan ha flera underordnade enheter, men en underordnad enhet har endast en överordnad. Det finns tre alternativ att ange underordnade enheterna till en edge-enhet: med Azure CLI via Azure-portalen eller med tjänsten IoT Hub SDK. 

Följande avsnitt innehåller exempel på hur du kan deklarera överordnad/underordnad relation i IoT Hub för befintliga IoT-enheter. Om du skapar nya enhetsidentiteter för ditt barn enheter, se [autentisera en underordnad enhet på Azure IoT Hub](how-to-authenticate-downstream-device.md) för mer information.

#### <a name="option-1-iot-hub-portal"></a>Alternativ 1: IoT Hub-portalen

Du kan deklarera överordnad-underordnad-relation när du skapar en ny enhet. Eller för befintliga enheter måste du deklarera relation från sidan med enhetsinformation för antingen överordnat IoT Edge-enhet eller underordnade IoT-enheter. 

   ![Hantera underordnade enheter från sidan IoT Edge-enhet](./media/offline-capabilities/manage-child-devices.png)


#### <a name="option-2-use-the-az-command-line-tool"></a>Alternativ 2: Använd den `az` kommandoradsverktyget

Med hjälp av den [kommandoradsgränssnittet](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) med [IoT-tillägget](https://github.com/azure/azure-iot-cli-extension) (v0.7.0 eller senare), kan du hantera överordnade och underordnade objekt med den [enhetsidentitet](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) underkommandon. I exemplet nedan används en fråga för att tilldela alla IoT Edge-enheter i hubben vara underordnade enheter av en IoT Edge-enhet. 

```shell
# Set IoT Edge parent device
egde_device="edge-device1"

# Get All IoT Devices
device_list=$(az iot hub query \
        --hub-name replace-with-hub-name \
        --subscription replace-with-sub-name \
        --resource-group replace-with-rg-name \
        -q "SELECT * FROM devices WHERE capabilities.iotEdge = false" \
        --query 'join(`, `, [].deviceId)' -o tsv)

# Add all IoT devices to IoT Edge (as child)
az iot hub device-identity add-children \
  --device-id $egde_device \
  --child-list $device_list \
  --hub-name replace-with-hub-name \
  --resource-group replace-with-rg-name \
  --subscription replace-with-sub-name 
```

Du kan ändra den [fråga](../iot-hub/iot-hub-devguide-query-language.md) att välja en annan Undergrupp enheter. Kommandot kan ta några sekunder om du anger ett stort antal enheter.

#### <a name="option-3-use-iot-hub-service-sdk"></a>Alternativ 3: Använd IoT Hub Service SDK 

Slutligen kan du hantera överordnade och underordnade objekt via programmering med hjälp av antingen C#, Java eller Node.js IoT Hub Service SDK. Här är en [exempel hur du tilldelar en underordnad enhet](https://aka.ms/set-child-iot-device-c-sharp) med hjälp av den C# SDK.

### <a name="set-up-the-parent-device-as-a-gateway"></a>Konfigurera den överordnade enheten som en gateway

Du kan tänka dig en överordnad/underordnad relation som en transparent gateway där underordnade enheten har sin egen identitet i IoT Hub men kommunicerar via molnet via dess överordnade objekt. Underordnad-enhet måste kunna verifiera att den överordnade enheten kommer från en betrodd källa för säker kommunikation. I annat fall kan tredje part ställa in skadliga enheter att personifiera föräldrar och intercept kommunikation. 

Ett sätt att skapa den här förtroenderelationen beskrivs i detalj i följande artiklar: 
* [Konfigurera en IoT Edge-enhet kan fungera som en transparent gateway](how-to-create-transparent-gateway.md)
* [Ansluta en underordnad (underordnad)-enhet till en Azure IoT Edge-gateway](how-to-connect-downstream-device.md)

## <a name="specify-dns-servers"></a>Ange DNS-servrar 

Förbättrad stabilitet, rekommenderas du anger DNS-serveradresser som används i din miljö. Se två alternativ för att [ange DNS-servern i felsökningsartikeln](troubleshoot.md#resolution-7).

## <a name="optional-offline-settings"></a>Valfria inställningar för offline

Om dina enheter går offline, lagrar alla meddelanden från enheten till molnet i överordnade IoT Edge-enheten tills anslutningen återupprättas. Modulen IoT Edge hub hanterar lagring och vidarebefordran av offline-meddelanden. Optimera prestanda genom att konfigurera två IoT Edge hub-inställningar för enheter som kan kopplas från under längre tid. 

Först bättre time to live inställningen så att IoT Edge hub behåller meddelanden som är tillräckligt länge för enheten för att återansluta. Sedan kan lägga till ytterligare diskutrymme för att lagra meddelanden. 

### <a name="time-to-live"></a>Time to live

Time to live inställningen är hur lång tid (i sekunder) som ett meddelande kan vänta som ska levereras innan den upphör. Standardvärdet är 7200 sekunder (två timmar). Det maximala värdet begränsas endast av det högsta värdet för en heltalsvariabel som är cirka 2 miljarder. 

Den här inställningen är en önskad egenskap för IoT Edge-hubben, som lagras i modultvillingen. Du kan konfigurera det i Azure-portalen eller direkt i manifestet distribution. 

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

Till exempel `"Binds":["/etc/iotedge/storage/:/iotedge/storage/"]` innebär att katalogen **/etc/iotedge/storage** på din värd system mappas till katalogen **/iotedge/storage/** för behållaren. Eller ett annat exempel för Windows-System, `"Binds":["C:\\temp:C:\\contemp"]` innebär att katalogen **C:\\temp** på din värd system mappas till katalogen **C:\\contemp** för behållaren. 

Du kan också hitta mer information om alternativ från för att skapa [docker docs](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate).

## <a name="next-steps"></a>Nästa steg

Läs mer om hur du ställer in en transparent gateway för överordnad/underordnad enhet: 

* [Konfigurera en IoT Edge-enhet kan fungera som en transparent gateway](how-to-create-transparent-gateway.md)
* [Autentisera en underordnad enhet på Azure IoT Hub](how-to-authenticate-downstream-device.md)
* [En underordnad ansluts till en Azure IoT Edge-gateway](how-to-connect-downstream-device.md)
