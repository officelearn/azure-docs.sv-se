> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-twin-how-to-configure.md)
> * [C#/Node.js](../articles/iot-hub/iot-hub-csharp-node-twin-how-to-configure.md)
> * [C#](../articles/iot-hub/iot-hub-csharp-csharp-twin-how-to-configure.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-twin-how-to-configure.md)
> * [Python](../articles/iot-hub/iot-hub-python-python-twin-how-to-configure.md)
> 
> 

## <a name="introduction"></a>Introduktion

I [Kom igång med IoT-hubb enheten twins][lnk-twin-tutorial], du har lärt dig hur du ställer in enheter metadata med hjälp av *taggar*. Du har fått enheten villkor från en enhet app med *rapporterade egenskaper*, och sedan efterfrågas informationen med hjälp av en SQL-liknande språk.

Den här självstudiekursen beskriver hur du använder enheten-dubbla *önskade egenskaper* och *rapporterade egenskaper* att konfigurera appar för enheter. Rapporterade och önskade egenskaper i en enhet dubbla aktivera en konfiguration med flera steg i ett enhetsprogram och synliggör status för den här åtgärden på alla enheter. Du hittar mer information om rollen enhetskonfigurationer i [översikt över hantering av enheter med IoT-hubben][lnk-dm-overview].

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

Med enheten twins kan lösningens serverdel så anger du önskad konfiguration för hanterade enheter, i stället för att skicka vissa kommandon på en hög nivå. Enheten är ansvarig för att konfigurera det bästa sättet att uppdatera konfigurationen (viktigt i IoT-scenarier där enheten påverka möjligheten att utföra omedelbart specifika kommandon) när kontinuerligt reporting aktuella tillstånd och potentiella felvillkor för uppdateringsprocessen. Det här mönstret är instrumentella till hantering av stora mängder av enheter, som den ger lösning backend-full insyn i staten där konfigurationen på alla enheter.

> [!TIP]
> I scenarier där enheter kontrolleras på ett mer interaktiva sätt (till exempel aktivera fläktar från en användare-kontrollerade app), Överväg att använda [direkt metoder][lnk-methods].

I den här självstudiekursen ändrar lösningens serverdel telemetri konfigurationen av en målenhet så att enheten appen gäller konfigurationen uppdaterades. Konfigurationen uppdaterades får alltså kräver programvara modulen startas om, som den här självstudiekursen simulerar med en enkel fördröjning.

Lösningens serverdel lagras konfigurationen enhet-dubbla egenskaper på följande sätt:

        {
            ...
            "properties": {
                ...
                "desired": {
                    "telemetryConfig": {
                        "configId": "{id of the configuration}",
                        "sendFrequency": "{config}"
                    }
                }
                ...
            }
            ...
        }

Eftersom konfigurationer kan vara komplexa objekt, tilldelas de unika ID: N (hashvärden eller [GUID][lnk-guid]).


Appen enheten rapporterar den aktuella konfigurationen spegling egenskapen önskade **telemetryConfig** i rapporterade egenskaperna:

        {
            "properties": {
                ...
                "reported": {
                    "telemetryConfig": {
                        "configId": "{id of the current configuration}",
                        "sendFrequency": "{current configuration}",
                        "status": "Success",
                    }
                }
                ...
            }
        }

Observera hur rapporterad **telemetryConfig** har en annan egenskap **status**, som används för att rapportera status för uppdateringsprocessen konfiguration.

När en ny önskad konfiguration tas emot rapporterar appen enheten en väntande konfiguration genom att ändra status:

        {
            "properties": {
                ...
                "reported": {
                    "telemetryConfig": {
                        "configId": "{id of the current configuration}",
                        "sendFrequency": "{current configuration}",
                        "status": "Pending",
                        "pendingConfig": {
                            "configId": "{id of the pending configuration}",
                            "sendFrequency": "{pending configuration}"
                        }
                    }
                }
                ...
            }
        }

Sedan vid ett senare tillfälle rapporterar appen enheten lyckats eller misslyckats i den här åtgärden genom att uppdatera egenskapen. Lösningens serverdel kan fråga status för konfigurationen på alla enheter när som helst.

I den här självstudiekursen lär du dig att:

* Skapa en simulerad enhetsapp som tar emot konfigurationsuppdateringar från lösningens serverdel och rapporterar flera uppdateringar som *rapporterade egenskaper* på konfigurationen uppdateringsprocessen.
* Skapa en backend-app som uppdaterar du önskad konfiguration för en enhet och frågar konfigurationsprocessen för uppdateringen.

<!-- links -->

[lnk-methods]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
[lnk-dm-overview]: ../articles/iot-hub/iot-hub-device-management-overview.md
[lnk-twin-tutorial]: ../articles/iot-hub/iot-hub-node-node-twin-getstarted.md
[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier
