---
title: Använd enheter offline – Azure IoT Edge | Microsoft Docs
description: Förstå hur IoT Edge-enheter och moduler kan användas utan internet-anslutning för längre tid och hur IoT Edge kan aktivera vanliga IoT-enheter att fungera offline för.
author: kgremban
ms.author: kgremban
ms.date: 11/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: ba64dcdadc5fa670c4502a7d8d92cb35e3b0cacd
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2019
ms.locfileid: "74924859"
---
# <a name="understand-extended-offline-capabilities-for-iot-edge-devices-modules-and-child-devices"></a>Förstå utökade offline-funktioner för IoT Edge enheter, moduler och underordnade enheter

Azure IoT Edge stöder utökade offline-åtgärder på IoT Edge enheter och aktiverar offline-åtgärder på icke-IoT Edge underordnade enheter. Så länge en IoT Edge enhet har haft en möjlighet att ansluta till IoT Hub, kan den enheten och underordnade enheter fortsätta att fungera med tillfälliga eller ingen Internet anslutning.

## <a name="how-it-works"></a>Så här fungerar det

När en IoT Edge enhet försätts i offlineläge, använder IoT Edge Hub tre roller. Först lagras alla meddelanden som skulle gå överordnade och de sparas tills enheten ska återansluta. Den fungerar dessutom för IoT Hub för att autentisera moduler och underordnade enheter så att de kan fortsätta att fungera. Det tredje möjliggör den kommunikation mellan underordnade enheter som normalt skulle gå via IoT Hub. 

I följande exempel visas hur en IoT Edge-scenariot fungerar i offline-läge:

1. **Konfigurera enheter**

   IoT Edge-enheter har automatiskt offline möjliggörs. Om du vill utöka den här funktionen till andra IoT-enheter, måste du deklarera en överordnad-underordnad-relation mellan enheterna i IoT Hub. Sedan konfigurerar du de underordnade enheterna så att de litar på den tilldelade överordnade enheten och dirigerar enhets-till-moln-kommunikationen genom den överordnade som en gateway. 

2. **Synkronisera med IoT Hub**

   Minst en gång efter installation av IoT Edge-körningen IoT Edge-enheten måste vara online för att synkronisera med IoT Hub. I den här synkroniseringen hämtar information om alla underordnade enheter som har tilldelats för IoT Edge-enhet. IoT Edge-enhet uppdaterar den lokala cachen om du vill aktivera drift offline också på ett säkert sätt och hämtar inställningar för lokal lagring av telemetrimeddelanden. 

3. **Gå offline**

   Under frånkoppling från IoT Hub, kan IoT Edge-enhet, dess distribuerade moduler och alla underordnade IoT-enheter fungera på obestämd tid. Moduler och underordnade enheter kan starta och starta om genom att autentisera med IoT Edge hubben offline. Telemetri bunden uppströms till IoT Hub lagras lokalt. Kommunikation mellan moduler eller mellan underordnade IoT-enheter underhålls via direkta metoder eller meddelanden. 

4. **Återanslut och omsynkronisera med IoT Hub**

   När anslutningen med IoT Hub återställs synkroniserar IoT Edge-enheten igen. Lokalt lagrade meddelanden levereras till IoT Hub direkt, men beror på anslutningens hastighet, IoT Hub svars tid och relaterade faktorer. De levereras i samma ordning som de lagrades i.

   Eventuella skillnader mellan önskade och rapporterade egenskaper för moduler och enheter har stämts av. IoT Edge-enhet uppdaterar alla ändringar till en uppsättning tilldelade underordnade IoT-enheter.

## <a name="restrictions-and-limits"></a>Begränsningar och gränser

De utökade offline-funktionerna som beskrivs i den här artikeln är tillgängliga i [IoT Edge version 1.0.7 eller senare](https://github.com/Azure/azure-iotedge/releases). Tidigare versioner har en delmängd av Offlinefunktioner. Befintliga IoT Edge-enheter som inte har utökat offlinefunktionerna kan inte uppgraderas genom att ändra körningsversion, men måste konfigureras med en ny enhetsidentitet i IoT Edge att få dessa funktioner. 

Utökad offline support är tillgänglig i alla regioner där IoT Hub är tillgänglig, **utom** östra USA.

Endast icke-IoT Edge enheter kan läggas till som underordnade enheter. 

IoT Edge enheter och deras tilldelade underordnade enheter kan fungera oändligt offline efter den första synkroniseringen vid ett tillfälle. Lagring av meddelanden beror dock på inställningen för TTL (Time to Live) och tillgängligt disk utrymme för att lagra meddelandena. 

## <a name="set-up-parent-and-child-devices"></a>Konfigurera överordnade och underordnade enheter

För att en IoT Edge enhet ska kunna utöka sina utökade offline-funktioner till underordnade IoT-enheter måste du utföra två steg. Deklarera först de överordnade och underordnade relationerna i Azure Portal. Skapa sedan en förtroende relation mellan den överordnade enheten och eventuella underordnade enheter och konfigurera sedan enhet-till-moln-kommunikation för att gå igenom den överordnade som en gateway. 

### <a name="assign-child-devices"></a>Tilldela underordnade enheter

Underordnade enheter kan vara en icke-IoT Edge enhet som är registrerad på samma IoT Hub. Överordnade enheter kan ha flera underordnade enheter, men en underordnad enhet har bara en överordnad. Det finns tre alternativ för att ange underordnade enheter till en Edge-enhet: via Azure Portal, med hjälp av Azure CLI eller med hjälp av IoT Hub service SDK. 

I följande avsnitt finns exempel på hur du kan deklarera den överordnade/underordnade relationen i IoT Hub för befintliga IoT-enheter. Om du skapar nya enhets identiteter för dina underordnade enheter läser du [autentisera en underordnad enhet till Azure IoT Hub](how-to-authenticate-downstream-device.md) för mer information.

#### <a name="option-1-iot-hub-portal"></a>Alternativ 1: IoT Hub Portal

Du kan deklarera den överordnade-underordnade relationen när du skapar en ny enhet. Eller för befintliga enheter kan du deklarera relationen från sidan enhets information i antingen den överordnade IoT Edge-enheten eller den underordnade IoT-enheten. 

   ![Hantera underordnade enheter från sidan IoT Edge-enhet](./media/offline-capabilities/manage-child-devices.png)


#### <a name="option-2-use-the-az-command-line-tool"></a>Alternativ 2: Använd kommando rads verktyget `az`

Med hjälp av [Azures kommando rads gränssnitt](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) med [IoT-tillägget](https://github.com/azure/azure-iot-cli-extension) (v 0.7.0 eller senare) kan du hantera överordnade underordnade relationer med under kommandona [enhets identitet](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) . Exemplet nedan använder en fråga för att tilldela alla icke-IoT Edge enheter i hubben som underordnade enheter till en IoT Edge enhet. 

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

#### <a name="option-3-use-iot-hub-service-sdk"></a>Alternativ 3: Använd IoT Hub service SDK 

Slutligen kan du hantera överordnade underordnade relationer genom att använda antingen C#Java eller Node. js IoT Hub service SDK. Här är C# ett [exempel på hur du tilldelar en underordnad enhet](https://aka.ms/set-child-iot-device-c-sharp) med hjälp av SDK.

### <a name="set-up-the-parent-device-as-a-gateway"></a>Konfigurera den överordnade enheten som en gateway

Du kan tänka på en överordnad/underordnad-relation som en transparent Gateway, där den underordnade enheten har sin egen identitet i IoT Hub men kommunicerar via molnet via dess överordnade. För säker kommunikation måste den underordnade enheten kunna verifiera att den överordnade enheten kommer från en betrodd källa. I annat fall kan tredje parter konfigurera skadliga enheter för att personifiera föräldrar och avlyssna kommunikation. 

Ett sätt att skapa den här förtroende relationen beskrivs i detalj i följande artiklar: 
* [Konfigurera en IoT Edge-enhet till att fungera som en transparent gateway](how-to-create-transparent-gateway.md)
* [Ansluta en underordnad enhet (underordnad) till en Azure IoT Edge-Gateway](how-to-connect-downstream-device.md)

## <a name="specify-dns-servers"></a>Ange DNS-servrar 

För att förbättra stabiliteten rekommenderar vi starkt att du anger de DNS-serveradresser som används i din miljö. Om du vill ställa in DNS-servern för IoT Edge kan du läsa mer i lösningen för [Edge-agenten i kontinuerlig rapport ' Tom konfigurations fil ' och inga moduler startar på enheten](troubleshoot.md#edge-agent-module-continually-reports-empty-config-file-and-no-modules-start-on-the-device) i fel söknings artikeln.

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

Information om meddelanden och modulens tillstånd lagras i IoT Edge hubbens lokala behållare som standard. För bättre tillförlitlighet, särskilt när du arbetar offline, kan du också dedikera lagring på värden IoT Edge enheten. Mer information finns i [ge moduler åtkomst till en enhets lokala lagring](how-to-access-host-storage-from-module.md)

## <a name="next-steps"></a>Nästa steg

Läs mer om hur du konfigurerar en transparent Gateway för dina överordnade/underordnade enhets anslutningar: 

* [Konfigurera en IoT Edge-enhet till att fungera som en transparent gateway](how-to-create-transparent-gateway.md)
* [Autentisera en underordnad enhet till Azure IoT Hub](how-to-authenticate-downstream-device.md)
* [Ansluta en underordnad enhet till en Azure IoT Edge-gateway](how-to-connect-downstream-device.md)
