---
title: Använd enheter offline – Azure IoT Edge | Microsoft Docs
description: Förstå hur IoT Edge-enheter och moduler kan användas utan internet-anslutning för längre tid och hur IoT Edge kan aktivera vanliga IoT-enheter att fungera offline för.
author: kgremban
ms.author: kgremban
ms.date: 08/04/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 6d82b353f8b485b4441853b7ff8e70e7d69f4d6a
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2019
ms.locfileid: "68986975"
---
# <a name="understand-extended-offline-capabilities-for-iot-edge-devices-modules-and-child-devices"></a>Förstå utökade offline-funktioner för IoT Edge enheter, moduler och underordnade enheter

Azure IoT Edge stöder utökade offline-åtgärder på IoT Edge enheter och aktiverar offline-åtgärder på icke-IoT Edge underordnade enheter. Så länge som en IoT Edge-enhet har haft en möjlighet att ansluta till IoT Hub, kan den och alla underordnade enheter fortsätta att funktionen med tillfälliga eller Ingen Internetanslutning. 


## <a name="how-it-works"></a>Hur det fungerar

När en IoT Edge enhet försätts i offlineläge, använder IoT Edge Hub tre roller. Först lagras alla meddelanden som skulle gå överordnade och de sparas tills enheten ska återansluta. Den fungerar dessutom för IoT Hub för att autentisera moduler och underordnade enheter så att de kan fortsätta att fungera. Det tredje möjliggör den kommunikation mellan underordnade enheter som normalt skulle gå via IoT Hub. 

I följande exempel visas hur en IoT Edge-scenariot fungerar i offline-läge:

1. **Konfigurera enheter**

   IoT Edge-enheter har automatiskt offline möjliggörs. Om du vill utöka den här funktionen till andra IoT-enheter, måste du deklarera en överordnad-underordnad-relation mellan enheterna i IoT Hub. Sedan konfigurerar du de underordnade enheterna så att de litar på den tilldelade överordnade enheten och dirigerar enhets-till-moln-kommunikationen genom den överordnade som en gateway. 

2. **Synkronisera med IoT Hub**

   Minst en gång efter installation av IoT Edge-körningen IoT Edge-enheten måste vara online för att synkronisera med IoT Hub. I den här synkroniseringen hämtar information om alla underordnade enheter som har tilldelats för IoT Edge-enhet. IoT Edge-enhet uppdaterar den lokala cachen om du vill aktivera drift offline också på ett säkert sätt och hämtar inställningar för lokal lagring av telemetrimeddelanden. 

3. **Gå offline**

   Under frånkoppling från IoT Hub, kan IoT Edge-enhet, dess distribuerade moduler och alla underordnade IoT-enheter fungera på obestämd tid. Moduler och underordnade enheter kan starta och starta om genom att autentisera med IoT Edge hubben offline. Telemetri bunden uppströms till IoT Hub lagras lokalt. Kommunikation mellan moduler eller mellan underordnade IoT-enheter underhålls via direkta metoder eller meddelanden. 

4. **Återanslut och omsynkronisera med IoT Hub**

   När anslutningen med IoT Hub återställs synkroniserar IoT Edge-enheten igen. Lokalt lagrade meddelanden levereras i samma ordning som de har lagrats. Eventuella skillnader mellan önskade och rapporterade egenskaper för moduler och enheter har stämts av. IoT Edge-enhet uppdaterar alla ändringar till en uppsättning tilldelade underordnade IoT-enheter.

## <a name="restrictions-and-limits"></a>Begränsningar och gränser

De utökade offline-funktionerna som beskrivs i den här artikeln är tillgängliga i [IoT Edge version 1.0.7 eller senare](https://github.com/Azure/azure-iotedge/releases). Tidigare versioner har en delmängd av Offlinefunktioner. Befintliga IoT Edge-enheter som inte har utökat offlinefunktionerna kan inte uppgraderas genom att ändra körningsversion, men måste konfigureras med en ny enhetsidentitet i IoT Edge att få dessa funktioner. 

Utökad offline support är tillgänglig i alla regioner där IoT Hub är tillgänglig, **utom** östra USA.

Endast icke-IoT Edge enheter kan läggas till som underordnade enheter. 

IoT Edge-enheter och deras tilldelade underordnade enheter kan fungera offline under obestämd tid efter den första, enstaka synkroniseringen. Lagring av meddelanden beror dock på time to live (TTL) inställningen och allt tillgängligt diskutrymme för att lagra meddelanden. 

## <a name="set-up-parent-and-child-devices"></a>Konfigurera överordnade och underordnade enheter

För att en IoT Edge enhet ska kunna utöka sina utökade offline-funktioner till underordnade IoT-enheter måste du utföra två steg. Deklarera först de överordnade och underordnade relationerna i Azure Portal. Skapa sedan en förtroende relation mellan den överordnade enheten och eventuella underordnade enheter och konfigurera sedan enhet-till-moln-kommunikation för att gå igenom den överordnade som en gateway. 

### <a name="assign-child-devices"></a>Tilldela underordnade enheter

Underordnade enheter kan vara en icke-IoT Edge enhet som är registrerad på samma IoT Hub. Överordnade enheter kan ha flera underordnade enheter, men en underordnad enhet har bara en överordnad. Det finns tre alternativ för att ange underordnade enheter till en Edge-enhet: via Azure Portal, med hjälp av Azure CLI eller med hjälp av IoT Hub service SDK. 

I följande avsnitt finns exempel på hur du kan deklarera den överordnade/underordnade relationen i IoT Hub för befintliga IoT-enheter. Om du skapar nya enhets identiteter för dina underordnade enheter läser du [autentisera en underordnad enhet till Azure IoT Hub](how-to-authenticate-downstream-device.md) för mer information.

#### <a name="option-1-iot-hub-portal"></a>Alternativ 1: IoT Hub Portal

Du kan deklarera den överordnade-underordnade relationen när du skapar en ny enhet. Eller för befintliga enheter kan du deklarera relationen från sidan enhets information i antingen den överordnade IoT Edge-enheten eller den underordnade IoT-enheten. 

   ![Hantera underordnade enheter från sidan IoT Edge-enhet](./media/offline-capabilities/manage-child-devices.png)


#### <a name="option-2-use-the-az-command-line-tool"></a>Alternativ 2: `az` Använda kommando rads verktyget

Med hjälp av [Azures kommando rads gränssnitt](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) med [IoT-tillägget](https://github.com/azure/azure-iot-cli-extension) (v 0.7.0 eller senare) kan du hantera överordnade underordnade relationer med under kommandona enhets [identitet](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) . Exemplet nedan använder en fråga för att tilldela alla icke-IoT Edge enheter i hubben som underordnade enheter till en IoT Edge enhet. 

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

Du kan ändra [frågan](../iot-hub/iot-hub-devguide-query-language.md) om du vill välja en annan delmängd av enheter. Kommandot kan ta flera sekunder om du anger en stor uppsättning enheter.

#### <a name="option-3-use-iot-hub-service-sdk"></a>Alternativ 3: Använda IoT Hub service SDK 

Slutligen kan du hantera överordnade underordnade relationer genom att använda antingen C#Java eller Node. js IoT Hub service SDK. Här är ett [exempel på](https://aka.ms/set-child-iot-device-c-sharp) hur du C# tilldelar en underordnad enhet med hjälp av SDK.

### <a name="set-up-the-parent-device-as-a-gateway"></a>Konfigurera den överordnade enheten som en gateway

Du kan tänka på en överordnad/underordnad-relation som en transparent Gateway, där den underordnade enheten har sin egen identitet i IoT Hub men kommunicerar via molnet via dess överordnade. För säker kommunikation måste den underordnade enheten kunna verifiera att den överordnade enheten kommer från en betrodd källa. I annat fall kan tredje parter konfigurera skadliga enheter för att personifiera föräldrar och avlyssna kommunikation. 

Ett sätt att skapa den här förtroende relationen beskrivs i detalj i följande artiklar: 
* [Konfigurera en IoT Edge-enhet kan fungera som en transparent gateway](how-to-create-transparent-gateway.md)
* [Ansluta en underordnad enhet (underordnad) till en Azure IoT Edge-Gateway](how-to-connect-downstream-device.md)

## <a name="specify-dns-servers"></a>Ange DNS-servrar 

För att förbättra stabiliteten rekommenderar vi starkt att du anger de DNS-serveradresser som används i din miljö. Se två alternativ för att [ställa in DNS-servern i fel söknings artikeln](troubleshoot.md#resolution-7).

## <a name="optional-offline-settings"></a>Valfria inställningar för offline

Om enheterna är offline lagrar den IoT Edge överordnade enheten alla meddelanden från enheten till molnet tills anslutningen har återupprättats. IoT Edge Hub-modulen hanterar lagring och vidarebefordran av offline-meddelanden. Optimera prestanda genom att konfigurera två IoT Edge Hub-inställningar för enheter som kan vara offline under längre tids perioder. 

Börja med att öka tiden till Live-inställningen så att IoT Edge hubben håller meddelandena tillräckligt länge för att enheten ska kunna ansluta igen. Lägg sedan till ytterligare disk utrymme för lagring av meddelanden. 

### <a name="time-to-live"></a>Time to live

Time to live inställningen är hur lång tid (i sekunder) som ett meddelande kan vänta som ska levereras innan den upphör. Standardvärdet är 7200 sekunder (två timmar). Det maximala värdet begränsas bara av det högsta värdet för en heltals variabel, som är cirka 2 000 000 000. 

Den här inställningen är en önskad egenskap för IoT Edge hubben, som lagras i modulernas dubbla. Du kan konfigurera den i Azure Portal eller direkt i distributions manifestet. 

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

### <a name="host-storage-for-system-modules"></a>Värd lagring för systemmoduler

Information om meddelanden och modulens tillstånd lagras i IoT Edge hubbens lokala behållare som standard. För bättre tillförlitlighet, särskilt när du arbetar offline, kan du också dedikera lagring på värden IoT Edge enheten.

Om du vill konfigurera lagring på värd systemet skapar du miljövariabler för IoT Edge Hub och IoT Edge agent som pekar på en lagringsmapp i behållaren. Använd sedan skapa-alternativen för att binda den storage-mappen till en mapp på värddatorn. 

Du kan konfigurera miljövariabler och skapa alternativ för modulen IoT Edge Hub i Azure Portal i avsnittet **Konfigurera avancerade gräns körnings inställningar** . 

1. För både IoT Edge hubb och IoT Edge agent lägger du till en miljö variabel med namnet **storageFolder** som pekar på en katalog i modulen.
1. För både IoT Edge hubb och IoT Edge agent lägger du till bindningar för att ansluta en lokal katalog på värddatorn till en katalog i modulen. Exempel: 

   ![Lägg till skapande alternativ och miljövariabler för lokal lagring](./media/offline-capabilities/offline-storage.png)

Eller så kan du konfigurera den lokala lagringen direkt i distributions manifestet. Exempel: 

```json
"systemModules": {
    "edgeAgent": {
        "settings": {
            "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
            "createOptions": {
                "HostConfig": {
                    "Binds":["<HostStoragePath>:<ModuleStoragePath>"]
                }
            }
        },
        "type": "docker",
        "env": {
            "storageFolder": {
                "value": "<ModuleStoragePath>"
            }
        }
    },
    "edgeHub": {
        "settings": {
            "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
            "createOptions": {
                "HostConfig": {
                    "Binds":["<HostStoragePath>:<ModuleStoragePath"],
                    "PortBindings":{"5671/tcp":[{"HostPort":"5671"}],"8883/tcp":[{"HostPort":"8883"}],"443/tcp":[{"HostPort":"443"}]}}}
        },
        "type": "docker",
        "env": {
            "storageFolder": {
                "value": "<ModuleStoragePath>"
            }
        },
        "status": "running",
        "restartPolicy": "always"
    }
}
```

Ersätt `<HostStoragePath>` och`<ModuleStoragePath>` med din lagrings Sök väg för värd och modul; båda värdena måste vara en absolut sökväg. 

`"Binds":["/etc/iotedge/storage/:/iotedge/storage/"]` Innebär till exempel att katalogen **/etc/iotedge/Storage** på värd systemet är mappad till katalogen **/iotedge/Storage/** på behållaren. Eller ett annat exempel för Windows- `"Binds":["C:\\temp:C:\\contemp"]` system, innebär att katalogen **c:\\Temp** på värd systemet är mappad till katalogen **c\\: Temp** på behållaren. 

På Linux-enheter ser du till att IoT Edge hubbens användar profil, UID 1000, har Läs-, skriv-och körnings behörighet till värd system katalogen. Dessa behörigheter är nödvändiga så att IoT Edge Hub kan lagra meddelanden i katalogen och hämta dem senare. (IoT Edge agenten fungerar som rot, så du behöver inte ytterligare behörigheter.) Det finns flera sätt att hantera katalog behörigheter på Linux-system, inklusive `chown` att använda för att ändra katalogens `chmod` ägare och sedan ändra behörigheterna. Exempel:

```bash
sudo chown 1000 <HostStoragePath>
sudo chmod 700 <HostStoragePath>
```

Du hittar mer information om att skapa alternativ från [Docker-dokument](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate).

## <a name="next-steps"></a>Nästa steg

Läs mer om hur du konfigurerar en transparent Gateway för dina överordnade/underordnade enhets anslutningar: 

* [Konfigurera en IoT Edge-enhet kan fungera som en transparent gateway](how-to-create-transparent-gateway.md)
* [Autentisera en underordnad enhet till Azure IoT Hub](how-to-authenticate-downstream-device.md)
* [Ansluta en underordnad enhet till en Azure IoT Edge-Gateway](how-to-connect-downstream-device.md)
