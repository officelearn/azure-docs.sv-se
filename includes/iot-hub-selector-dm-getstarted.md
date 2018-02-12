> [!div class="op_single_selector"]
> * [Device: Node.js Service: Node.js](../articles/iot-hub/iot-hub-node-node-device-management-get-started.md)
> * [Device: Node.js Service: C#](../articles/iot-hub/iot-hub-csharp-node-device-management-get-started.md)
> * [Enhet: C#-tjänsten: C#](../articles/iot-hub/iot-hub-csharp-csharp-device-management-get-started.md)
> * [Enhet: Java-tjänsten: Java](../articles/iot-hub/iot-hub-java-java-device-management-getstarted.md)
> * [Enhet: Python-tjänsten: Python](../articles/iot-hub/iot-hub-python-python-device-management-get-started.md)

Backend-appar kan använda Azure IoT Hub primitiver t.ex [enheten dubbla] [ lnk-devtwin] och [direkt metoder][lnk-c2dmethod], för att starta och övervaka enheten via fjärranslutning Management-åtgärder på enheterna. Den här kursen visar hur en backend-app och en enhetsapp kan fungera tillsammans för att initiera och övervaka fjärranslutna enheten startas om med IoT-hubb.

Använd en direkt metod för att initiera hanteringsåtgärder för enheten (till exempel omstart fabriksåterställning och firmware-uppdatering) från en backend-app i molnet. Enheten är ansvarig för:

* Hanterar metoden-begäran som skickats från IoT-hubb.
* Initiera motsvarande enhetsspecifika-åtgärd på enheten.
* Tillhandahåller statusuppdateringar via *rapporterade egenskaper* till IoT-hubben.

Du kan använda en backend-app i molnet för att köra enheten dubbla frågor att rapportera om förloppet för hanteringsåtgärder för enheten.

[lnk-devtwin]: ../articles/iot-hub/iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
