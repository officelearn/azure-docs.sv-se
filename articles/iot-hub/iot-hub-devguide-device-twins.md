---
title: Förstå Azure IoT Hub-enhetstvillingar | Microsoft-dokument
description: Utvecklarguide – använd enhetstvillingar för att synkronisera tillstånds- och konfigurationsdata mellan IoT Hub och dina enheter
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/01/2020
ms.openlocfilehash: 51e58de92f111c8854add613a299f2b8ccec0503
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79285245"
---
# <a name="understand-and-use-device-twins-in-iot-hub"></a>Förstå och använda enhetstvillingar i IoT Hub

*Enhetstvillingar* är JSON-dokument som lagrar enhetstillståndsinformation, inklusive metadata, konfigurationer och villkor. Azure IoT Hub lagrar en enhetstvilling för varje enhet som du ansluter till IoT Hub. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

I den här artikeln beskrivs:

* Enhetens tvillingstruktur: *taggar*, *önskade* och *rapporterade egenskaper*.
* De åtgärder som enhetsappar och bakåtänddar kan utföra på enhetstvillingar.

Använd enhetstvillingar för att:

* Lagra enhetsspecifika metadata i molnet. Distributionsplatsen för en varuautomat.

* Rapportera aktuell tillståndsinformation, till exempel tillgängliga funktioner och villkor från enhetsappen. En enhet är till exempel ansluten till din IoT-hubb via mobilnät eller WiFi.

* Synkronisera tillståndet för tidskrävande arbetsflöden mellan enhetsapp och backend-app. Till exempel när lösningen back end anger den nya firmware-versionen att installera, och enhetsappen rapporterar de olika stadierna i uppdateringsprocessen.

* Fråga enhetens metadata, konfiguration eller tillstånd.

Se [kommunikationsvägledning](iot-hub-devguide-d2c-guidance.md) för enhet till moln för vägledning om hur du använder rapporterade egenskaper, meddelanden från enhet till moln eller filöverföring.

Se [kommunikationsvägledning](iot-hub-devguide-c2d-guidance.md) från molnet till enhet för vägledning om hur du använder önskade egenskaper, direkta metoder eller meddelanden från molnet till enheten.

## <a name="device-twins"></a>Enhetstvillingar

Enhetstvillingar lagrar enhetsrelaterad information som:

* Enhets- och serverdelar kan användas för att synkronisera enhetsvillkor och konfiguration.

* Lösningens baksida kan användas för att fråga och rikta långvariga åtgärder.

Livscykeln för en enhetstvilling är länkad till motsvarande [enhetsidentitet](iot-hub-devguide-identity-registry.md). Enhetstvillingar skapas implicit och tas bort när en enhetsidentitet skapas eller tas bort i IoT Hub.

En enhetstvilling är ett JSON-dokument som innehåller:

* **Taggar**. Ett avsnitt i JSON-dokumentet som lösningens baksida kan läsa från och skriva till. Taggar är inte synliga för enhetsappar.

* **Önskade egenskaper**. Används tillsammans med rapporterade egenskaper för att synkronisera enhetens konfiguration eller villkor. Lösningens baksida kan ange önskade egenskaper och enhetsappen kan läsa dem. Enhetsappen kan också ta emot meddelanden om ändringar i de önskade egenskaperna.

* **Rapporterade egenskaper**. Används tillsammans med önskade egenskaper för att synkronisera enhetens konfiguration eller villkor. Enhetsappen kan ange rapporterade egenskaper och lösningens baksida kan läsa och fråga dem.

* **Egenskaper för enhetsidentitet**. Roten till enhetstvillingenS JSON-dokument innehåller skrivskyddade egenskaper från motsvarande enhetsidentitet som lagras i [identitetsregistret](iot-hub-devguide-identity-registry.md).

![Skärmbild av enhetstvillingegenskaper](./media/iot-hub-devguide-device-twins/twin.png)

I följande exempel visas ett enhetstvilling-JSON-dokument:

```json
{
    "deviceId": "devA",
    "etag": "AAAAAAAAAAc=", 
    "status": "enabled",
    "statusReason": "provisioned",
    "statusUpdateTime": "0001-01-01T00:00:00",
    "connectionState": "connected",
    "lastActivityTime": "2015-02-30T16:24:48.789Z",
    "cloudToDeviceMessageCount": 0, 
    "authenticationType": "sas",
    "x509Thumbprint": {     
        "primaryThumbprint": null, 
        "secondaryThumbprint": null 
    }, 
    "version": 2, 
    "tags": {
        "$etag": "123",
        "deploymentLocation": {
            "building": "43",
            "floor": "1"
        }
    },
    "properties": {
        "desired": {
            "telemetryConfig": {
                "sendFrequency": "5m"
            },
            "$metadata" : {...},
            "$version": 1
        },
        "reported": {
            "telemetryConfig": {
                "sendFrequency": "5m",
                "status": "success"
            },
            "batteryLevel": 55,
            "$metadata" : {...},
            "$version": 4
        }
    }
}
```

I rotobjektet finns enhetsidentitetsegenskaperna `reported` och `desired` behållarobjekt för `tags` och båda och egenskaper. Behållaren `properties` innehåller vissa skrivskyddade`$metadata` `$etag`element `$version`( , och ) som beskrivs i avsnitten [Enhetstvilling och](iot-hub-devguide-device-twins.md#device-twin-metadata) [Optimistisk samtidighet.](iot-hub-devguide-device-twins.md#optimistic-concurrency)

### <a name="reported-property-example"></a>Exempel på rapporterad egenskap

I föregående exempel innehåller enhetstvillingen en `batteryLevel` egenskap som rapporteras av enhetsappen. Den här egenskapen gör det möjligt att fråga efter och använda enheter baserat på den senast rapporterade batterinivån. Andra exempel är enhetsappens rapporteringsenhetsfunktioner eller anslutningsalternativ.

> [!NOTE]
> Rapporterade egenskaper förenklar scenarier där lösningens backend är intresserad av det senast kända värdet för en egenskap. Använd meddelanden från enhet till moln om [lösningens](iot-hub-devguide-messages-d2c.md) serverdel behöver bearbeta enhetstelemetri i form av sekvenser av tidsstämplade händelser, till exempel tidsserier.

### <a name="desired-property-example"></a>Exempel på önskad egenskap

I föregående exempel `telemetryConfig` används enhetstvillingen önskade och rapporterade egenskaper av lösningens serverdel och enhetsappen för att synkronisera telemetrikonfigurationen för den här enheten. Ett exempel:

1. Lösningens serverdel anger önskad egenskap med önskat konfigurationsvärde. Här är den del av dokumentet med önskad egenskapsuppsättning:

   ```json
   "desired": {
       "telemetryConfig": {
           "sendFrequency": "5m"
       },
       ...
   },
   ```

2. Enhetsappen meddelas om ändringen omedelbart om den är ansluten eller vid den första återanslutningen. Enhetsappen rapporterar sedan den uppdaterade konfigurationen (eller ett feltillstånd med hjälp av egenskapen). `status` Här är den del av de rapporterade egenskaperna:

   ```json
   "reported": {
       "telemetryConfig": {
           "sendFrequency": "5m",
           "status": "success"
       }
       ...
   }
   ```

3. Lösningens serverdel kan spåra resultatet av konfigurationsåtgärden på många enheter genom [att fråga](iot-hub-devguide-query-language.md) enhetstvillingar.

> [!NOTE]
> De föregående kodavsnitten är exempel, optimerade för läsbarhet, på ett sätt att koda en enhetskonfiguration och dess status. IoT Hub innehåller inte ett specifikt schema för de enhetstvillingar som önskas och rapporteras i enhetstvillingarna.
> 

Du kan använda tvillingar för att synkronisera tidskrävande åtgärder, till exempel uppdateringar av den inbyggda programvaran. Mer information om hur du använder egenskaper för att synkronisera och spåra en tidskrävande åtgärd på olika enheter finns i [Använda önskade egenskaper för att konfigurera enheter](tutorial-device-twins.md).

## <a name="back-end-operations"></a>Backend-verksamhet

Lösningens backdel fungerar på enhetstvillingen med hjälp av följande atomoperationer, som exponeras via HTTPS:

* **Hämta enhetstvillingen efter ID**. Den här åtgärden returnerar enhetstvillingdokumentet, inklusive taggar och önskade och rapporterade systemegenskaper.

* **Delvis uppdatera enhetstvillingen**. Med den här åtgärden kan lösningens backend delvis uppdatera taggarna eller önskade egenskaper i en enhetstvilling. Den partiella uppdateringen uttrycks som ett JSON-dokument som lägger till eller uppdaterar en egenskap. Egenskaper som `null` ska tas bort. I följande exempel skapas en `{"newProperty": "newValue"}`ny önskad egenskap med `existingProperty` värde, skriver över det befintliga värdet med `"otherNewValue"`och tar bort `otherOldProperty`. Inga andra ändringar görs i befintliga önskade egenskaper eller taggar:

   ```json
   {
       "properties": {
           "desired": {
               "newProperty": {
                   "nestedProperty": "newValue"
               },
               "existingProperty": "otherNewValue",
               "otherOldProperty": null
           }
       }
   }
   ```

* **Ersätt önskade egenskaper**. Den här åtgärden gör det möjligt för lösningens backend att helt `properties/desired`skriva över alla befintliga önskade egenskaper och ersätta ett nytt JSON-dokument för .

* **Ersätt taggar**. Med den här åtgärden kan lösningens back end helt skriva över alla `tags`befintliga taggar och ersätta ett nytt JSON-dokument för .

* **Ta emot dubbla meddelanden**. Med den här åtgärden kan lösningens backend meddelas när tvillingen ändras. För att göra det måste din IoT-lösning skapa en väg och ange datakällan som är lika *med twinChangeEvents*. Som standard finns inga sådana vägar i föra-ägda, så inga dubbla meddelanden skickas. Om förändringshastigheten är för hög eller av andra orsaker, till exempel interna fel, kan IoT Hub bara skicka ett meddelande som innehåller alla ändringar. Om ditt program behöver tillförlitlig granskning och loggning av alla mellanliggande tillstånd bör du därför använda meddelanden från enhet till moln. Det dubbla meddelandemeddelandet innehåller egenskaper och brödtext.

  - Egenskaper

    | Namn | Värde |
    | --- | --- |
    $content typ | application/json |
    $iothub-enqueuedtime |  Tid när meddelandet skickades |
    $iothub-meddelande-källa | twinChangeEvents |
    $content-kodning | utf-8 |
    deviceId | ID för enheten |
    hubName (hubName) | Namn på IoT Hub |
    operationTimestamp | [ISO8601](https://en.wikipedia.org/wiki/ISO_8601) tidsstämpel för drift |
    iothub-meddelande-schema | twinChangeNotification |
    opType (olika) | "replaceTwin" eller "updateTwin" |

    Meddelandesystemegenskaper föregås av `$` symbolen.

  - Innehåll
        
    Det här avsnittet innehåller alla dubbla ändringar i ett JSON-format. Den använder samma format som en korrigeringsfil, med skillnaden att den kan innehålla alla dubbla avsnitt: taggar, properties.reported, properties.desired och att den innehåller "$metadata"-elementen. Exempel:

    ```json
    {
      "properties": {
          "desired": {
              "$metadata": {
                  "$lastUpdated": "2016-02-30T16:24:48.789Z"
              },
              "$version": 1
          },
          "reported": {
              "$metadata": {
                  "$lastUpdated": "2016-02-30T16:24:48.789Z"
              },
              "$version": 1
          }
      }
    }
    ```

Alla föregående åtgärder stöder [optimistisk samtidighet](iot-hub-devguide-device-twins.md#optimistic-concurrency) och kräver **ServiceConnect-behörigheten,** enligt definitionen i [Kontrollåtkomst till IoT Hub](iot-hub-devguide-security.md).

Utöver dessa åtgärder kan lösningens baksida:

* Fråga enhetstvillingarna med hjälp av frågespråket SQL-liknande [IoT Hub](iot-hub-devguide-query-language.md).

* Utför åtgärder på stora uppsättningar enhetstvillingar med [jobb](iot-hub-devguide-jobs.md).

## <a name="device-operations"></a>Enhetsåtgärder

Enhetsappen fungerar på enhetstvillingen med hjälp av följande atomära åtgärder:

* **Hämta enhetstvilling**. Den här åtgärden returnerar enhetstvillingdokumentet (inklusive önskade och rapporterade systemegenskaper) för den anslutna enheten. (Taggar är inte synliga för enhetsappar.)

* **Delvis uppdatera rapporterade egenskaper**. Den här åtgärden möjliggör partiell uppdatering av de rapporterade egenskaperna för den anslutna enheten. Den här åtgärden använder samma JSON-uppdateringsformat som lösningens back end använder för en partiell uppdatering av önskade egenskaper.

* **Observera önskade egenskaper**. Den anslutna enheten kan välja att meddelas om uppdateringar av önskade egenskaper när de inträffar. Enheten får samma form av uppdatering (partiell eller fullständig ersättning) som utförs av lösningens backdel.

Alla föregående åtgärder kräver **behörigheten DeviceConnect,** enligt definitionen i [Kontrollåtkomst till IoT Hub](iot-hub-devguide-security.md).

[Azure IoT-enhetenSDK:er](iot-hub-devguide-sdks.md) gör det enkelt att använda de föregående åtgärderna från många språk och plattformar. Mer information om information om IoT Hub-primitiver för önskad egenskapssynkronisering finns i [Enhetsåteranslutningsflöde](iot-hub-devguide-device-twins.md#device-reconnection-flow).

## <a name="tags-and-properties-format"></a>Taggar och egenskapsformat

Taggar, önskade egenskaper och rapporterade egenskaper är JSON-objekt med följande begränsningar:

* **Nycklar**: Alla nycklar i JSON-objekt är UTF-8-kodade, skiftlägeskänsliga och upp till 1 KB långa. Tillåtna tecken utesluter UNICODE-kontrolltecken (segment C0 och C1) och `.`, `$`och SP.

* **Värden:** Alla värden i JSON-objekt kan vara av följande JSON-typer: boolesk, tal, sträng, objekt. Matriser är inte tillåtna.

    * Heltal kan ha ett minimivärde på -4503599627370496 och ett högsta värde på 4503599627370495.

    * Strängvärden är UTF-8 kodade och kan ha en maximal längd på 4 KB.

* **Djup**: Det maximala djupet för JSON-objekt i taggar, önskade egenskaper och rapporterade egenskaper är 10. Följande objekt är till exempel giltigt:

   ```json
   {
       ...
       "tags": {
           "one": {
               "two": {
                   "three": {
                       "four": {
                           "five": {
                               "six": {
                                   "seven": {
                                       "eight": {
                                           "nine": {
                                               "ten": {
                                                   "property": "value"
                                               }
                                           }
                                       }
                                   }
                               }
                           }
                       }
                   }
               }
           }
       },
       ...
   }
   ```

## <a name="device-twin-size"></a>Enhetens dubbla storlek

IoT Hub tillämpar en storleksgräns på `tags`8 kB för värdet för och `properties/desired` en `properties/reported`storleksgräns på 32 kB för värdet och . Dessa summor är exklusiva för skrivskyddade element som `$etag`, `$version`och `$metadata/$lastUpdated`.

Twin storlek beräknas på följande sätt:

* För varje egenskap i JSON-dokumentet beräknar IoT Hub kumulativt och lägger till längden på egenskapens nyckel och värde.

* Egenskapsnycklar betraktas som UTF8-kodade strängar.

* Enkla egenskapsvärden betraktas som UTF8-kodade strängar, numeriska värden (8 byte) eller booleskvärden (4 byte).

* Storleken på UTF8-kodade strängar beräknas genom att räkna alla tecken, exklusive UNICODE-kontrolltecken (segment C0 och C1).

* Komplexa egenskapsvärden (kapslade objekt) beräknas baserat på den sammanlagda storleken på egenskapsnycklarna och egenskapsvärdena som de innehåller.

IoT Hub avvisar med ett fel alla åtgärder `tags` `properties/desired`som `properties/reported` skulle öka storleken på , eller dokument över gränsen.

## <a name="device-twin-metadata"></a>Metadata för enhetstvilling

IoT Hub underhåller tidsstämpeln för den senaste uppdateringen för varje JSON-objekt i enhetstvilling önskade och rapporterade egenskaper. Tidsstämplarna är i UTC och kodade i `YYYY-MM-DDTHH:MM:SS.mmmZ` [ISO8601-format](https://en.wikipedia.org/wiki/ISO_8601) .

Ett exempel:

```json
{
    ...
    "properties": {
        "desired": {
            "telemetryConfig": {
                "sendFrequency": "5m"
            },
            "$metadata": {
                "telemetryConfig": {
                    "sendFrequency": {
                        "$lastUpdated": "2016-03-30T16:24:48.789Z"
                    },
                    "$lastUpdated": "2016-03-30T16:24:48.789Z"
                },
                "$lastUpdated": "2016-03-30T16:24:48.789Z"
            },
            "$version": 23
        },
        "reported": {
            "telemetryConfig": {
                "sendFrequency": "5m",
                "status": "success"
            },
            "batteryLevel": "55%",
            "$metadata": {
                "telemetryConfig": {
                    "sendFrequency": "5m",
                    "status": {
                        "$lastUpdated": "2016-03-31T16:35:48.789Z"
                    },
                    "$lastUpdated": "2016-03-31T16:35:48.789Z"
                },
                "batteryLevel": {
                    "$lastUpdated": "2016-04-01T16:35:48.789Z"
                },
                "$lastUpdated": "2016-04-01T16:24:48.789Z"
            },
            "$version": 123
        }
    }
    ...
}
```

Denna information hålls på alla nivåer (inte bara bladen på JSON-strukturen) för att bevara uppdateringar som tar bort objektnycklar.

## <a name="optimistic-concurrency"></a>Optimistisk samtidighet

Taggar, önskade och rapporterade egenskaper stöder alla optimistisk samtidighet.
Taggar har en ETag, enligt [RFC7232](https://tools.ietf.org/html/rfc7232), som representerar taggens JSON-representation. Du kan använda ETags i villkorsstyrda uppdateringsåtgärder från lösningens serverdel för att säkerställa konsekvens.

Enhetstvilling önskade och rapporterade egenskaper har `$version` inte ETags, men har ett värde som garanterat är inkrementellt. På samma sätt som en ETag kan versionen användas av uppdateringsparten för att framtvinga konsekvens av uppdateringar. Till exempel en enhetsapp för en rapporterad egenskap eller lösningens backend för en önskad egenskap.

Versioner är också användbara när en observationsagent (till exempel enhetsappen som observerar de önskade egenskaperna) måste stämma av kapplöpningar mellan resultatet av en hämtningsåtgärd och ett uppdateringsmeddelande. Avsnittet [Flödesflöde för enheten ger](iot-hub-devguide-device-twins.md#device-reconnection-flow) mer information.

## <a name="device-reconnection-flow"></a>Återanslutningsflöde för enheten

IoT Hub bevarar inte önskade egenskaper uppdateringsmeddelanden för frånkopplade enheter. Härav följer att en enhet som ansluter måste hämta det fullständiga önskade egenskapsdokumentet, förutom att prenumerera för uppdateringsmeddelanden. Med tanke på möjligheten till tävlingar mellan uppdateringsmeddelanden och fullständig hämtning måste följande flöde säkerställas:

1. Enhetsappen ansluter till en IoT-hubb.
2. Enhetsapp prenumererar på önskade egenskaper uppdatering meddelanden.
3. Enhetsappen hämtar hela dokumentet för önskade egenskaper.

Enhetsappen kan ignorera `$version` alla meddelanden med mindre eller lika med den version av det fullständiga hämtade dokumentet. Den här metoden är möjlig eftersom IoT Hub garanterar att versioner alltid ökar.

> [!NOTE]
> Den här logiken har redan implementerats i [Azure IoT-enhetenSDK:er](iot-hub-devguide-sdks.md). Den här beskrivningen är bara användbar om enhetsappen inte kan använda någon av Azure IoT-enhetSDK:er och måste programmera MQTT-gränssnittet direkt.
> 

## <a name="additional-reference-material"></a>Ytterligare referensmaterial

Andra referensavsnitt i utvecklarhandboken för IoT Hub är:

* [IoT Hub-slutpunkter](iot-hub-devguide-endpoints.md) beskriver de olika slutpunkter som varje IoT-hubb exponerar för körnings- och hanteringsåtgärder.

* I artikeln [Begränsning och kvoter](iot-hub-devguide-quotas-throttling.md) beskrivs de kvoter som gäller för IoT Hub-tjänsten och begränsningsbeteendet som du kan förvänta dig när du använder tjänsten.

* [IoT-enheten och tjänsten SDK:er](iot-hub-devguide-sdks.md) innehåller en lista över de olika språk-SDK:er som du kan använda när du utvecklar både enhets- och tjänstappar som interagerar med IoT Hub.

* [Frågespråket IoT Hub för enhetstvillingar, jobb och meddelanderoutning](iot-hub-devguide-query-language.md) beskriver det IoT Hub-frågespråk som du kan använda för att hämta information från IoT Hub om dina enhetstvillingar och jobb.

* [IoT Hub MQTT-supportartikeln](iot-hub-mqtt-support.md) innehåller mer information om IoT Hub-stöd för MQTT-protokollet.

## <a name="next-steps"></a>Nästa steg

Nu har du lärt dig om enhetstvillingar, du kan vara intresserad av följande IoT Hub-utvecklarguide ämnen:

* [Förstå och använda modultvillingar i IoT Hub](iot-hub-devguide-module-twins.md)
* [Anropa en direkt metod på en enhet](iot-hub-devguide-direct-methods.md)
* [Schemalägga jobb på flera enheter](iot-hub-devguide-jobs.md)

Information om hur du provar några av de begrepp som beskrivs i den här artikeln finns i följande IoT Hub-självstudier:

* [Så här använder du enhetens tvilling](iot-hub-node-node-twin-getstarted.md)
* [Så här använder du enhetstvillingegenskaper](tutorial-device-twins.md)
* [Enhetshantering med Azure IoT-verktyg för VS Code](iot-hub-device-management-iot-toolkit.md)
