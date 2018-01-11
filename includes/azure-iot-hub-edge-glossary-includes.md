## <a name="iot-edge"></a>IoT Edge
Azure IoT-Edge gör det möjligt för moln-driven distribution av Azure-tjänster och lösningen-specifik kod till lokala enheter. IoT-gränsenheterna kan aggregera data från andra enheter att utföra datoranvändning och analytics innan data skickas till molnet. Mer information finns [Azure IoT kant](https://docs.microsoft.com/en-us/azure/iot-edge/).

## <a name="iot-edge-agent"></a>IoT-Edge-agent
Del av körningsmiljön IoT kant ansvarar för att distribuera och övervaka moduler.

## <a name="iot-edge-device"></a>IoT Edge-enhet
IoT-gränsenheterna har IoT kanten runtime installerad och har flaggats som ”IoT gränsenheten” i information om enheten. Lär dig hur du [distribuera Azure IoT kanten på en simulerad enhet i Linux - Förhandsgranska](https://docs.microsoft.com/en-us/azure/iot-edge/tutorial-simulate-device-linux).

## <a name="iot-edge-deployment"></a>IoT-Edge-distribution
En IoT-Edge distribution konfigurerar en uppsättning IoT-gränsenheterna och köra en uppsättning IoT kant moduler mål. Varje distribution säkerställer kontinuerligt att alla enheter som matchar dess målvillkoren kör den angivna uppsättningen moduler, även när nya enheter skapas eller ändras för att matcha målvillkoren. Varje IoT-enhet får bara högsta prioritet distributionen vars mål skick uppfyller. Lär dig mer om [IoT kant distribution](https://docs.microsoft.com/en-us/azure/iot-edge/module-deployment-monitoring).

## <a name="iot-edge-deployment-manifest"></a>Distributionsmanifestet IoT kant
Json-dokument som innehåller information som ska kopieras i en eller flera IoT kant enheternas modulen twin(s) att distribuera en uppsättning moduler, vägar och associerade modulen önskade egenskaper.

## <a name="iot-edge-gateway-device"></a>IoT-Edge gateway-enhet
En IoT insticksenhet med underordnade enhet. Underordnade enheten kan vara IoT kant eller inte IoT-enhet.

## <a name="iot-edge-hub"></a>Gräns för IoT-hubb
En del av körningsmiljön IoT kant ansvarar för modulen modulen kommunikation överordnade (mot IoT-hubb) och underordnade (bort från IoT-hubb) kommunikation. 

## <a name="iot-edge-leaf-device"></a>Gränsenheten lägsta IoT
En IoT insticksenhet med några underordnade enhet. 

## <a name="iot-edge-module"></a>IoT-Edge-modul
En IoT-Edge-modul är en dockerbehållare som du kan distribuera till enheter som IoT. Den utför en viss uppgift, till exempel vill föra in ett meddelande från en enhet, omvandla ett meddelande eller skicka ett meddelande till en IoT-hubb. Den kommunicerar med andra moduler och skickar data till IoT kant-körningsmiljön. [Förstå de krav och verktyg för att utveckla IoT kant moduler](https://docs.microsoft.com/en-us/azure/iot-edge/module-development).

## <a name="iot-edge-module-identity"></a>IoT-Edge modulen identitet
En post i IoT-hubb modulen identitetsregistret med autentiseringsuppgifterna som används av en modul för att autentisera med en kant nav eller IoT-hubb förekomst och säkerhet.

## <a name="iot-edge-module-image"></a>IoT-Edge modulen bild
Docker-bild som används av IoT kant-körning för att skapa modulen instanser.

## <a name="iot-edge-module-twin"></a>IoT-Edge modulen dubbla
Json-dokument beständig i IoT-hubb som lagrar statusinformation för en modulinstans.

## <a name="iot-edge-priority"></a>IoT-Edge prioritet
När två IoT kant-distributioner riktade till samma enhet, används i distributionen med högre prioritet. Om båda distributionerna har samma prioritet, tillämpas distributionen med senare skapandedatum. Lär dig mer om [prioritet](https://docs.microsoft.com/en-us/azure/iot-edge/module-deployment-monitoring#priority).

## <a name="iot-edge-runtime"></a>IoT Edge-körning
IoT-Edge runtime innehåller allt som Microsoft distribuerar för att installeras på en IoT-enhet. Det med kant-agent, Edge hubb och Edge CTL-verktyget.

## <a name="iot-edge-set-modules-to-a-single-device"></a>IoT-Edge ange moduler på en enhet
En åtgärd som att kopiera innehållet i ett manifest för IoT kanten på en enhet som modulen dubbla. Underliggande API är generisk gäller konfiguration, som bara tar ett manifest för IoT kant som indata.

## <a name="iot-edge-target-condition"></a>IoT-Edge målvillkoren
I en IoT-Edge-distribution är målvillkoren booleskt villkor på enheten twins taggar om du vill markera målenheterna som för distributionen, t.ex ”. tag.environment = prod”. Målvillkoren utvärderas alltid att inkludera nya enheter som uppfyller kraven eller ta bort enheter som inte längre. Lär dig mer om [mål villkor](https://docs.microsoft.com/en-us/azure/iot-edge/module-deployment-monitoring#target-condition)