> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/quickstart-control-device-node.md)
> * [C#](../articles/iot-hub/quickstart-control-device-dotnet.md)
> * [Java](../articles/iot-hub/quickstart-control-device-java.md)
> * [Python](../articles/iot-hub/quickstart-control-device-python.md)

Azure IoT Hub är en helt hanterad tjänst som möjliggör tillförlitlig och säker dubbelriktad kommunikation mellan miljontals enheter och som tillhandahåller serverdelen. Tidigare självstudier ([Kom igång med IoT Hub] och [skicka meddelanden från moln till enhet med IoT Hub]) illustrerar de grundläggande funktionerna av enhet till moln och från moln till enhet meddelanden i IoT Hub. IoT Hub ger dig också möjligheten att anropa icke-varaktiga metoder på enheter från molnet. Direkta metoder representerar en begäran / svar-interaktion med en enhet som liknar ett HTTPS-anrop i att de lyckas eller misslyckas omedelbart (efter en användardefinierade tidsgräns) till att de vet statusen för anropet. Artikeln [anropa en direkt metod på en enhet] [ lnk-devguide-methods] beskriver direkta metoder i detalj och ger vägledning om hur du använder direkta metoder i stället för meddelanden från moln till enhet eller önskade egenskaper.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

I den här självstudiekursen lär du dig att:

* Använda Azure-portalen för att skapa en IoT hub och skapa en enhetsidentitet i IoT hub.
* Skapa en app för simulerade enheter som har en direkt metod som kan anropas av molnet.
* Skapa en konsolapp som anropar en direkt metod i den simulerade enhetsappen via din IoT-hubb.


[lnk-devguide-methods]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: ../articles/iot-hub/iot-hub-mqtt-support.md

[Skicka meddelanden från moln till enhet med IoT Hub]: ../articles/iot-hub/iot-hub-csharp-csharp-c2d.md
[Kom igång med IoT Hub]: ../articles/iot-hub/quickstart-send-telemetry-node.md