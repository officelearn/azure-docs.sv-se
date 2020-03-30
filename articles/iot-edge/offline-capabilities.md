---
title: Använda enheter offline – Azure IoT Edge | Microsoft-dokument
description: Förstå hur IoT Edge-enheter och-moduler kan fungera utan internetanslutning under längre tidsperioder och hur IoT Edge kan aktivera vanliga IoT-enheter för att fungera offline också.
author: kgremban
ms.author: kgremban
ms.date: 11/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 55512491121aee28404ab5f85b4223c67a2f0e1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80236063"
---
# <a name="understand-extended-offline-capabilities-for-iot-edge-devices-modules-and-child-devices"></a>Förstå utökade offlinefunktioner för IoT Edge-enheter, moduler och underordnade enheter

Azure IoT Edge stöder utökade offlineåtgärder på dina IoT Edge-enheter och aktiverar offlineåtgärder även på underordnade enheter som inte är IoT Edge. Så länge en IoT Edge-enhet har haft en möjlighet att ansluta till IoT Hub, kan den enheten och alla underordnade enheter fortsätta att fungera med intermittent eller ingen internetanslutning.

## <a name="how-it-works"></a>Hur det fungerar

När en IoT Edge-enhet går in i offlineläge tar IoT Edge-hubben på tre roller. Först lagrar den alla meddelanden som skulle gå uppströms och sparar dem tills enheten återansluter. För det andra fungerar den på uppdrag av IoT Hub för att autentisera moduler och underordnade enheter så att de kan fortsätta att fungera. För det tredje möjliggör det kommunikation mellan underordnade enheter som normalt skulle gå igenom IoT Hub.

I följande exempel visas hur ett IoT Edge-scenario fungerar i offlineläge:

1. **Konfigurera enheter**

   IoT Edge-enheter har automatiskt offlinefunktioner aktiverade. Om du vill utöka den funktionen till andra IoT-enheter måste du deklarera en överordnad och underordnad relation mellan enheterna i IoT Hub. Sedan konfigurerar du de underordnade enheterna för att lita på deras tilldelade överordnade enhet och dirigera kommunikationen mellan enheten och molnet via den överordnade som en gateway.

2. **Synkronisera med IoT Hub**

   Minst en gång efter installationen av IoT Edge-körningen måste IoT Edge-enheten vara online för att synkronisera med IoT Hub. I den här synkroniseringen får IoT Edge-enheten information om alla underordnade enheter som tilldelats den. IoT Edge-enheten uppdaterar också sin lokala cache på ett säkert sätt för att aktivera offlineåtgärder och hämtar inställningar för lokal lagring av telemetrimeddelanden.

3. **Gå offline**

   När den är frånkopplad från IoT Hub kan IoT Edge-enheten, dess distribuerade moduler och alla underordnade IoT-enheter fungera på obestämd tid. Moduler och underordnade enheter kan starta och starta om genom att autentisera med IoT Edge-hubben när du är offline. Telemetri som är bunden uppströms till IoT Hub lagras lokalt. Kommunikation mellan moduler eller mellan underordnade IoT-enheter upprätthålls genom direkta metoder eller meddelanden.

4. **Återanslut och synkronisera igen med IoT Hub**

   När anslutningen till IoT Hub har återställts synkroniseras IoT Edge-enheten igen. Lokalt lagrade meddelanden levereras till IoT Hub direkt, men är beroende av anslutningens hastighet, IoT Hub-svarstid och relaterade faktorer. De levereras i samma ordning som de förvarades i.

   Eventuella skillnader mellan de önskade och rapporterade egenskaperna för modulerna och enheterna är avstämda. IoT Edge-enheten uppdaterar alla ändringar i uppsättningen av tilldelade underordnade IoT-enheter.

## <a name="restrictions-and-limits"></a>Begränsningar och begränsningar

De utökade offlinefunktionerna som beskrivs i den här artikeln är tillgängliga i [IoT Edge version 1.0.7 eller senare](https://github.com/Azure/azure-iotedge/releases). Tidigare versioner har en delmängd av offlinefunktioner. Befintliga IoT Edge-enheter som inte har utökade offlinefunktioner kan inte uppgraderas genom att ändra körningsversionen, men måste konfigureras om med en ny IoT Edge-enhetsidentitet för att få dessa funktioner.

Endast enheter som inte är IoT Edge-enheter kan läggas till som underordnade enheter.

IoT Edge-enheter och deras tilldelade underordnade enheter kan fungera på obestämd tid offline efter den första engångssynkroniseringen. Lagring av meddelanden beror dock på hur länge du ska live (TTL) och det tillgängliga diskutrymmet för lagring av meddelandena.

## <a name="set-up-parent-and-child-devices"></a>Konfigurera överordnade och underordnade enheter

För att en IoT Edge-enhet ska kunna utöka sina utökade offlinefunktioner till underordnade IoT-enheter måste du slutföra två steg. Deklarera först överordnade och underordnade relationer i Azure-portalen. För det andra skapar du en förtroenderelation mellan den överordnade enheten och alla underordnade enheter och konfigurerar sedan kommunikation mellan enheter och moln för att gå igenom den överordnade som en gateway.

### <a name="assign-child-devices"></a>Tilldela underordnade enheter

Underordnade enheter kan vara alla enheter som inte är IoT Edge-enheter registrerade på samma IoT Hub. Överordnade enheter kan ha flera underordnade enheter, men en underordnad enhet har bara en överordnad enhet. Det finns tre alternativ för att ställa in underordnade enheter till en kantenhet: via Azure-portalen, med hjälp av Azure CLI eller använda IoT Hub-tjänsten SDK.

I följande avsnitt finns exempel på hur du kan deklarera den överordnade/underordnade relationen i IoT Hub för befintliga IoT-enheter. Om du skapar nya enhetsidentiteter för dina underordnade enheter läser du [Autentisera en nedströmsenhet till Azure IoT Hub](how-to-authenticate-downstream-device.md) för mer information.

#### <a name="option-1-iot-hub-portal"></a>Alternativ 1: IoT Hub Portal

Du kan deklarera den överordnade och underordnade relationen när du skapar en ny enhet. Eller för befintliga enheter kan du deklarera relationen från enhetsinformationssidan för antingen den överordnade IoT Edge-enheten eller den underordnade IoT-enheten.

   ![Hantera underordnade enheter från informationssidan för IoT Edge-enheten](./media/offline-capabilities/manage-child-devices.png)

#### <a name="option-2-use-the-az-command-line-tool"></a>Alternativ 2: `az` Använd kommandoradsverktyget

Med hjälp av [Azure-kommandoradsgränssnittet](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) med [IoT-tillägg](https://github.com/azure/azure-iot-cli-extension) (v0.7.0 eller nyare) kan du hantera överordnade underordnade relationer med underkommandona för [enhetsidentitet.](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) I exemplet nedan används en fråga för att tilldela alla enheter som inte är IoT Edge-enheter i navet för att vara underordnade enheter till en IoT Edge-enhet.

```azurecli
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

Du kan ändra [frågan](../iot-hub/iot-hub-devguide-query-language.md) för att välja en annan delmängd av enheter. Kommandot kan ta flera sekunder om du anger en stor uppsättning enheter.

#### <a name="option-3-use-iot-hub-service-sdk"></a>Alternativ 3: Använd IoT Hub Service SDK

Slutligen kan du hantera överordnade underordnade relationer programmässigt med hjälp av C#, Java eller Node.js IoT Hub Service SDK. Här är ett [exempel på att tilldela en underordnad enhet](https://aka.ms/set-child-iot-device-c-sharp) med hjälp av C# SDK.

### <a name="set-up-the-parent-device-as-a-gateway"></a>Konfigurera den överordnade enheten som en gateway

Du kan se en överordnad/underordnad relation som en transparent gateway, där den underordnade enheten har sin egen identitet i IoT Hub men kommunicerar via molnet via den överordnade. För säker kommunikation måste den underordnade enheten kunna verifiera att den överordnade enheten kommer från en betrodd källa. Annars kan tredje part konfigurera skadliga enheter för att personifiera föräldrar och avlyssna kommunikation.

Ett sätt att skapa den här förtroenderelationen beskrivs i detalj i följande artiklar:

* [Konfigurera en IoT Edge-enhet till att fungera som en transparent gateway](how-to-create-transparent-gateway.md)
* [Ansluta en underordnad enhet nedströms (underordnad) till en Azure IoT Edge-gateway](how-to-connect-downstream-device.md)

## <a name="specify-dns-servers"></a>Ange DNS-servrar

För att förbättra robustheten rekommenderar vi starkt att du anger de DNS-serveradresser som används i din miljö. Om du vill ange DNS-servern för IoT Edge läser du upplösningen för [Edge Agent-modulen rapporterar kontinuerligt "tom konfigurationsfil" och inga moduler startar på enheten](troubleshoot.md#edge-agent-module-continually-reports-empty-config-file-and-no-modules-start-on-the-device) i felsökningsartikeln.

## <a name="optional-offline-settings"></a>Valfria offlineinställningar

Om dina enheter kopplas från lagrar den överordnade IoT Edge-överordnade enheten alla meddelanden från enheten till molnet tills anslutningen återupprättas. IoT Edge-hubbmodulen hanterar lagring och vidarebefordran av offlinemeddelanden. Optimera prestanda för enheter som kan vara offline under en längre tid genom att konfigurera två IoT Edge-hubbinställningar.

Öka först tiden för att live-inställningen så att IoT Edge-hubben behåller meddelanden tillräckligt länge för att enheten ska kunna återansluta. Lägg sedan till ytterligare diskutrymme för meddelandelagring.

### <a name="time-to-live"></a>Time to live

Tiden till live-inställningen är den tid (i sekunder) som ett meddelande kan vänta på att levereras innan det går ut. Standardvärdet är 7200 sekunder (två timmar). Det maximala värdet begränsas endast av det maximala värdet för en heltalsvariabel, vilket är cirka 2 miljarder.

Den här inställningen är en önskad egenskap för IoT Edge-hubben, som lagras i modultvillingen. Du kan konfigurera den i Azure-portalen eller direkt i distributionsmanifestet.

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

### <a name="host-storage-for-system-modules"></a>Värdlagring för systemmoduler

Meddelanden och modultillståndsinformation lagras som standard i IoT Edge-hubbens lokala behållarfilsystem. För förbättrad tillförlitlighet, särskilt när du använder offline, kan du också ägna lagring på värd-IoT Edge-enheten. Mer information finns i [Ge moduler åtkomst till en enhets lokala lagring](how-to-access-host-storage-from-module.md)

## <a name="next-steps"></a>Nästa steg

Läs mer om hur du konfigurerar en transparent gateway för dina överordnade/underordnade enhetsanslutningar:

* [Konfigurera en IoT Edge-enhet till att fungera som en transparent gateway](how-to-create-transparent-gateway.md)
* [Autentisera en underordnad enhet på Azure IoT Hub](how-to-authenticate-downstream-device.md)
* [Ansluta en underordnad enhet till en Azure IoT Edge-gateway](how-to-connect-downstream-device.md)
