## <a name="automatic-device-management"></a>Automatisk enhetshantering
Automatisk hantering av enheter i Azure IoT Hub automatiserar repetitiva och komplexa uppgifter för att hantera stora enheten fjärranläggning över hela deras livscykler. Med automatisk hantering av enheter du rikta en uppsättning enheter baserat på deras egenskaper, definiera en önskad konfiguration och låta IoT Hub uppdatera enheter när de träder i omfånget.  Består av [automatisk enhetskonfigurationer](../articles/iot-hub/iot-hub-auto-device-config.md) och [automatisk IoT Edge-distributioner](../articles/iot-edge/how-to-deploy-monitor.md).

## <a name="iot-edge"></a>IoT Edge
Azure IoT Edge aktiverar molndriven distribution av Azure-tjänster och lösningsspecifik kod till lokala enheter. IoT Edge-enheter kan samla ihop data från andra enheter att utföra databehandling och analys innan data skickas till molnet. Mer information finns i [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/).

## <a name="iot-edge-agent"></a>IoT Edge-agenten
Del av IoT Edge-körningen ansvarar för att distribuera och övervaka moduler.

## <a name="iot-edge-device"></a>IoT Edge-enhet
IoT Edge-enheter har installerat IoT Edge-körningen och är flaggade som **IoT Edge-enhet** i information om enheten. Lär dig hur du [distribuera Azure IoT Edge på en simulerad enhet i Linux - förhandsversion](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-linux).

## <a name="iot-edge-automatic-deployment"></a>IoT Edge automatisk distribution
En automatisk IoT Edge-distribution konfigurerar en target uppsättning IoT Edge-enheter att köra en uppsättning IoT Edge-moduler. Varje distribution säkerställer kontinuerligt att alla enheter som matchar dess Målvillkor kör den angivna uppsättningen moduler, även när nya enheter skapas eller ändras för att matcha målvillkoret. Varje IoT Edge-enhet får bara högsta prioritet distribution vars Målvillkor den uppfyller. Läs mer om [IoT Edge automatisk distribution](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).

## <a name="iot-edge-deployment-manifest"></a>IoT Edge-distribution manifest
Json-dokument som innehåller information som ska kopieras i en eller flera IoT Edge enheters modulen twin(s) att distribuera en uppsättning moduler, önskade vägar och associerade modulen egenskaper.

## <a name="iot-edge-gateway-device"></a>IoT Edge-gatewayenhet
En IoT Edge-enhet med underordnad enhet. Underordnad enhet kan vara antingen IoT Edge eller inte IoT Edge-enhet.

## <a name="iot-edge-hub"></a>IoT Edge hub
En del av IoT Edge-körningen ansvarar för modulen till modulen kommunikation överordnade (mot IoT Hub) och underordnade (från IoT Hub) kommunikation. 

## <a name="iot-edge-leaf-device"></a>Löv IoT Edge-enhet
En IoT Edge-enhet med ingen underordnad enhet. 

## <a name="iot-edge-module"></a>IoT Edge-modul
En IoT Edge-modul är en dockerbehållare som du kan distribuera till IoT Edge-enheter. Den utför en viss uppgift som matar in ett meddelande från en enhet, omvandla ett meddelande eller skicka ett meddelande till en IoT-hubb. Den kommunicerar med andra moduler och skickar data till IoT Edge-körningen. [Förstå de krav och verktyg för att utveckla IoT Edge-moduler](https://docs.microsoft.com/azure/iot-edge/module-development).

## <a name="iot-edge-module-identity"></a>IoT Edge-modulen identitet
En post i identitetsregistret för IoT Hub-modulen med information om förekomst och security autentiseringsuppgifter som ska användas av en modul för autentisering med en edge hub eller IoT-hubb.

## <a name="iot-edge-module-image"></a>IoT Edge-modul-avbildning
Docker-avbildningen som används av IoT Edge-körningen för att skapa modulen instanser.

## <a name="iot-edge-module-twin"></a>IoT Edge-modultvilling
Ett Json-dokument som sparas i IoT Hub som lagrar statusinformation för en modulinstans.

## <a name="iot-edge-priority"></a>IoT Edge-prioritet
När två IoT Edge-distributioner mot samma enhet, används distributionen med högst prioritet. Om två distributioner har samma prioritet används distributionen med senare skapandedatum. Läs mer om [prioritet](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring#priority).

## <a name="iot-edge-runtime"></a>IoT Edge-körning
IoT Edge-körningen innehåller allt som Microsoft distribuerar för att kunna installeras på en IoT Edge-enhet. Det omfattar Edge-agenten, Edge hub och IoT Edge security daemon.

## <a name="iot-edge-set-modules-to-a-single-device"></a>IoT Edge ange moduler på en enhet
En åtgärd som kopierar innehållet i ett manifest för IoT Edge på en enhet ”modultvilling. Underliggande API är en allmän tillämpa konfigurationen, som bara tar en IoT Edge-manifest som indata.

## <a name="iot-edge-target-condition"></a>IoT Edge Målvillkor
I en IoT Edge-distribution Målvillkor är valfritt booleskt villkor på enhetstvillingar-taggar för att välja målenheter för distributionen, till exempel **tag.environment = prod**. Målvillkoret utvärderas kontinuerligt för att omfatta alla nya enheter som uppfyller kraven eller ta bort enheter som inte längre göra. Läs mer om [rikta villkor](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring#target-condition)