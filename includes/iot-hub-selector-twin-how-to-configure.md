> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-twin-how-to-configure.md)
> * [C#/node.js](../articles/iot-hub/iot-hub-csharp-node-twin-how-to-configure.md)
> * [C#](../articles/iot-hub/iot-hub-csharp-csharp-twin-how-to-configure.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-twin-how-to-configure.md)
> 
> 

## <a name="introduction"></a>Introduktion

I [Kom igång med IoT-hubb enheten twins][lnk-twin-tutorial], du har lärt dig hur du ställer in enhetens metadata från din lösning serverdel med *taggar*, rapportera enheten villkor från en enhetsapp med hjälp av *rapporterade egenskaper*, och fråga efter den här informationen med hjälp av en SQL-liknande språk.

I kursen får du lära dig hur du använder enheten-dubbla *önskade egenskaper* tillsammans med *rapporterade egenskaper*, att konfigurera appar för enheter. Mer specifikt den här kursen visar hur en enhet dubbla rapporterade och egenskaper aktivera en konfiguration med flera steg i ett enhetsprogram och synliggör till lösningens serverdel statusen för den här åtgärden på alla enheter. Du hittar mer information om rollen enhetskonfigurationer i [översikt över hantering av enheter med IoT-hubben][lnk-dm-overview].

Med enheten twins kan lösningens serverdel så anger du önskad konfiguration för hanterade enheter, i stället för att skicka vissa kommandon på en hög nivå. Detta placerar enheten ansvarig för att konfigurera det bästa sättet att uppdatera konfigurationen (viktigt i IoT-scenarier där enheten påverka möjligheten att utföra omedelbart specifika kommandon) när kontinuerligt rapporterar till lösningens serverdel aktuell status och potentiella felvillkor för uppdateringsprocessen. Det här mönstret är instrumentella till hantering av stora mängder av enheter, som kan lösningens serverdel ha full insyn i staten där konfigurationen på alla enheter.

> [!NOTE]
> I scenarier där enheter kontrolleras på ett mer interaktiva sätt (aktivera en fläkt från en användare-kontrollerade app), Överväg att använda [direkt metoder][lnk-methods].
> 
> 

I den här självstudiekursen lösningens serverdel ändrar telemetri konfigurationen av en målenhet och som ett resultat av att appen enheten följer en process i flera steg för att tillämpa en uppdatering för configuration (till exempel kräver programvara modulen startas om, där den här självstudiekursen simulerar med en enkel fördröjning).

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

> [!NOTE]
> Eftersom konfigurationer kan vara komplexa objekt, tilldelas de unika ID: N (hashvärden eller [GUID][lnk-guid]) att förenkla sina jämförelser.
> 
> 

Appen enheten rapporterar den aktuella konfigurationen spegling egenskapen önskade **telemetryConfig** i rapporterade egenskaperna:

        {
            "properties": {
                ...
                "reported": {
                    "telemetryConfig": {
                        "changeId": "{id of the current configuration}",
                        "sendFrequency": "{current configuration}",
                        "status": "Success",
                    }
                }
                ...
            }
        }

Observera hur rapporterad **telemetryConfig** har en annan egenskap **status**, som används för att rapportera status för uppdateringsprocessen konfiguration.

När en ny önskad konfiguration tas emot rapporterar appen enheten en väntande konfiguration genom att ändra informationen:

        {
            "properties": {
                ...
                "reported": {
                    "telemetryConfig": {
                        "changeId": "{id of the current configuration}",
                        "sendFrequency": "{current configuration}",
                        "status": "Pending",
                        "pendingConfig": {
                            "changeId": "{id of the pending configuration}",
                            "sendFrequency": "{pending configuration}"
                        }
                    }
                }
                ...
            }
        }

Sedan vid ett senare tillfälle rapporterar appen enheten lyckats eller misslyckats i den här åtgärden genom att uppdatera egenskapen ovan.
Observera hur lösningens serverdel kan, när som helst för att fråga status för konfigurationen på alla enheter.

I den här självstudiekursen lär du dig att:

* Skapa en simulerad enhetsapp som tar emot konfigurationsuppdateringar från lösningens serverdel och rapporterar flera uppdateringar som *rapporterade egenskaper* på konfigurationen uppdateringsprocessen.
* Skapa en backend-app som uppdaterar du önskad konfiguration för en enhet och frågar konfigurationsprocessen för uppdateringen.

<!-- links -->

[lnk-methods]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
[lnk-dm-overview]: ../articles/iot-hub/iot-hub-device-management-overview.md
[lnk-twin-tutorial]: ../articles/iot-hub/iot-hub-node-node-twin-getstarted.md
[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier
