> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-twin-getstarted.md)
> * [C#/node.js](../articles/iot-hub/iot-hub-csharp-node-twin-getstarted.md)
> * [C#](../articles/iot-hub/iot-hub-csharp-csharp-twin-getstarted.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-twin-getstarted.md)

Enhetstvillingar är JSON-dokument som lagrar information om enhetstillstånd (metadata, konfigurationer och villkor). IoT-hubb kvarstår en enhet dubbla för varje enhet som ansluter till den.

Använd enhet twins till:

* Lagra metadata om enheter från din lösningens serverdel.
* Rapportera aktuell statusinformation, till exempel tillgängliga funktioner och villkor (till exempel anslutning metoden används) från din enhet.
* Synkronisera tillståndet för tidskrävande arbetsflöden (till exempel uppdateringarna av inbyggd och konfiguration) mellan en enhetsapp och en backend-app.
* Fråga din enhetsmetadata, konfiguration eller tillstånd.

> [!NOTE]
> Enheten twins är utformade för synkronisering och för att fråga efter enhetskonfigurationer och villkor. Mer information om när enheten twins finns i [förstå enheten twins][lnk-twins].

Enheten twins lagras i en IoT-hubb och innehålla:

* *taggar*, enhetens metadata endast kan nås av lösningens serverdel;
* *Egenskaper för Desired*, JSON-objekt som kan ändras av lösningen tillbaka slutet och observeras via appen för enheter, och
* *rapporterade egenskaper*, JSON-objekt kan ändras via appen för enheter och läsas av lösningens serverdel. Taggar och egenskaper får inte innehålla matriser, men kan vara kapslade objekt.

![][img-twin]

Dessutom kan lösningens serverdel fråga enheten twins baserat på data som ovan.
Referera till [förstå enheten twins] [ lnk-twins] för mer information om enheten twins och den [IoT-hubb frågespråket] [ lnk-query] referera för fråga.


I den här självstudiekursen lär du dig att:

* Skapa en backend-app som lägger till *taggar* dubbla en enhet och en simulerad enhetsapp som rapporterar sin anslutning kanal som en *rapporterade egenskapen* på enheten dubbla.
* Fråga enheter från en backend-app med hjälp av filter på taggar och egenskaper som tidigare har skapat.

<!-- images -->
[img-twin]: media/iot-hub-selector-twin-get-started/twin.png

<!-- links -->
[lnk-query]: ../articles/iot-hub/iot-hub-devguide-query-language.md
[lnk-twins]: ../articles/iot-hub/iot-hub-devguide-device-twins.md
[lnk-d2c]: ../articles/iot-hub/iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: ../articles/iot-hub/iot-hub-mqtt-support.md