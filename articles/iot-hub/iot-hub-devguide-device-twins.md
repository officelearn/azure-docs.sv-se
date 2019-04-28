---
title: Förstå enhetstvillingar för Azure IoT Hub | Microsoft Docs
description: Utvecklarguide – Använd enhetstvillingar för att synkronisera tillstånd och konfiguration data mellan IoT Hub och dina enheter
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.openlocfilehash: 883e81572218e39d84ad8793423b02468d49d00a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61322552"
---
# <a name="understand-and-use-device-twins-in-iot-hub"></a>Förstå och använda enhetstvillingar i IoT Hub

*Enhetstvillingar* är JSON-dokument som lagrar information om enhetstillstånd, inklusive metadata, konfigurationer och villkor. Azure IoT Hub lagrar en enhetstvilling för varje enhet som du ansluter till IoT Hub. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Den här artikeln beskrivs:

* Strukturen för enhetstvillingen: *taggar*, *önskade* och *rapporterade egenskaper*.
* De åtgärder som appar för enheter och servrar kan utföra på enhetstvillingar.

Använda enhetstvillingar för att:

* Store enhetsspecifika metadata i molnet. Till exempel distributionsplatsen för en Varuautomat.

* Rapportera aktuell statusinformation, till exempel tillgänglig kapacitet och villkor från din enhetsapp. Till exempel en enhet är ansluten till din IoT hub över mobil- eller Wi-Fi.

* Synkronisera tillståndet för långvariga arbetsflöden mellan enhetsapp och serverdelsappen. Exempel: när lösningen tillbaka slutet anger den nya versionen av inbyggd programvara att installera och app för enheter rapporterar de olika stegen för uppdateringsprocessen.

* Fråga din enhetsmetadata, konfiguration eller tillstånd.

Referera till [enhet till molnet kommunikation vägledning](iot-hub-devguide-d2c-guidance.md) anvisningar om hur du använder rapporterade egenskaper, meddelanden från enheten till molnet eller ladda upp filen.

Referera till [moln till enhet kommunikation vägledning](iot-hub-devguide-c2d-guidance.md) för hjälp med att använda önskade egenskaper, direkta metoder eller meddelanden från molnet till enheten.

## <a name="device-twins"></a>Enhetstvillingar

Enhetstvillingar lagra enhetsrelaterade information som:

* Enheten och tillbaka upphör kan använda för att synkronisera enheten villkor och konfiguration.

* Lösningens serverdel kan använda för att fråga och mål långvariga åtgärder.

Livscykeln för en enhetstvilling är länkad till motsvarande [enhetsidentitet](iot-hub-devguide-identity-registry.md). Enhetstvillingar skapas implicit och tas bort när en enhetsidentitet skapas eller tas bort i IoT Hub.

En enhetstvilling är ett JSON-dokument som innehåller:

* **Taggar**. En del av JSON-dokument som lösningens serverdel kan läsa från och skriva till. Taggar visas inte för appar för enheter.

* **Önskade egenskaper**. Används tillsammans med rapporterade egenskaper för att synkronisera enhetskonfiguration eller villkor. App för enheter kan läsa dem lösningens serverdel kan ange önskade egenskaper. App för enheter kan också ta emot meddelanden om ändringar i de önskade egenskaperna.

* **Rapporterade egenskaper**. Används tillsammans med önskade egenskaper för att synkronisera enhetskonfiguration eller villkor. App för enheter kan ange rapporterade egenskaper och lösningens serverdel kan läsa och skicka frågor mot dem.

* **Enheten identitetsegenskaper**. Roten för enheten twin JSON-dokumentet innehåller skrivskyddade egenskaper från motsvarande enhetens identitet som lagras i den [identitetsregistret](iot-hub-devguide-identity-registry.md).

![Skärmbild av tvillingegenskaper](./media/iot-hub-devguide-device-twins/twin.png)

I följande exempel visas en enhetstvilling JSON-dokument:

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

I rotobjektet är enheten identitetsegenskaper och behållarobjekt för `tags` och både `reported` och `desired` egenskaper. Den `properties` behållaren innehåller vissa skrivskyddade element (`$metadata`, `$etag`, och `$version`) som beskrivs i den [twin enhetsmetadata](iot-hub-devguide-device-twins.md#device-twin-metadata) och [Optimistisk samtidighet](iot-hub-devguide-device-twins.md#optimistic-concurrency) avsnitt.

### <a name="reported-property-example"></a>Rapporterad egenskap-exempel

I exemplet ovan enhetstvillingen innehåller en `batteryLevel` egenskap som rapporteras av enhetsappen. Den här egenskapen gör det möjligt att köra frågor och fungerar på enheter baserat på senaste rapporterade batterinivå. Andra exempel är enheten app reporting enhetsfunktioner eller anslutningsalternativ.

> [!NOTE]
> Rapporterade egenskaper förenkla scenarier där lösningens backend-server är intresserad av det senaste kända värdet för en egenskap. Använd [meddelanden från enheten till molnet](iot-hub-devguide-messages-d2c.md) om lösningens serverdel måste bearbeta enhetstelemetri i form av tidsstämplad händelser, till exempel tidsserier.

### <a name="desired-property-example"></a>Exempel på önskad egenskap

I exemplet ovan den `telemetryConfig` enhetstvillingen önskad och rapporterade egenskaper som används av lösningens backend-server och enhetsappen för att synkronisera konfigurationen av telemetri för den här enheten. Exempel:

1. Lösningens serverdel anger önskad egenskap med det önskade värdet. Här är del av dokumentet med önskad egenskap:

   ```json
   "desired": {
       "telemetryConfig": {
           "sendFrequency": "5m"
       },
       ...
   },
   ```

2. App för enheter meddelas för ändringen direkt om du är ansluten eller i den första reconnect. App för enheter rapporterar den uppdaterade konfigurationen (eller ett fel villkor med hjälp av den `status` egenskapen). Här är del av rapporterade egenskaper:

   ```json
   "reported": {
       "telemetryConfig": {
           "sendFrequency": "5m",
           "status": "success"
       }
       ...
   }
   ```

3. Lösningens serverdel kan spåra resultatet av konfigurationsåtgärden mellan många enheter av [fråga](iot-hub-devguide-query-language.md) enhetstvillingar.

> [!NOTE]
> Föregående kodfragment är exempel som optimerats för läsbarhet på ett sätt att koda ett enhetskonfiguration och dess status. IoT Hub medför inte ett visst schema för enhetstvillingen önskad och rapporterade egenskaper i enhetstvillingar.
> 

Du kan använda twins för att synkronisera långvariga åtgärder, till exempel uppdateringar av inbyggd programvara. Mer information om hur du använder egenskaper för att synkronisera och spåra en långvarig åtgärd i enheter finns i [Använd önskade egenskaper för att konfigurera enheter](tutorial-device-twins.md).

## <a name="back-end-operations"></a>Backend-åtgärder

Lösningens backend-server körs på enhetstvillingen med hjälp av följande atomiska operationer exponeras via HTTPS:

* **Hämta enhetstvillingen efter ID**. Den här åtgärden returnerar device twin dokument, inklusive taggar och önskade och rapporterade egenskaper.

* **Delvis uppdatera enhetstvillingen**. Den här åtgärden gör att lösningens backend-server att delvis uppdatera taggar och önskade egenskaper i enhetstvillingen. Deluppdatering uttrycks som ett JSON-dokument som läggs till eller uppdaterar en egenskap. Egenskaper som har angetts till `null` tas bort. I följande exempel skapas en ny önskad egenskap med värdet `{"newProperty": "newValue"}`, skriver över det befintliga värdet för `existingProperty` med `"otherNewValue"`, och tar bort `otherOldProperty`. Inga andra ändringar har gjorts i befintliga önskade egenskaper eller taggar:

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

* **Ersätt önskade egenskaper**. Den här åtgärden gör att lösningens backend-server att skriva över alla befintliga önskade egenskaper och ersätta ett nytt JSON-dokument för `properties/desired`.

* **Ersätt taggar**. Den här åtgärden gör att lösningens backend-server att skriva över alla befintliga taggar och ersätta ett nytt JSON-dokument för `tags`.

* **Ta emot meddelanden twin**. Den här åtgärden gör att lösningens serverdel kan meddelas när läsningen ändras. Gör din IoT-lösning behöver du skapar en väg och datakällan ska vara lika med *twinChangeEvents*. Som standard finnas inga sådana vägar redan, så att inga dubbla meddelanden skickas. Om ändringsfrekvensen är för hög eller av andra orsaker, till exempel interna fel IoT-hubben kan skicka endast ett meddelande som innehåller alla ändringar. Om ditt program behöver tillförlitlig granskning och loggning av alla mellanliggande tillstånd, bör du därför använda meddelanden från enheten till molnet. Meddelandet twin innehåller egenskaperna och brödtext.

  - Egenskaper

    | Namn | Värde |
    | --- | --- |
    $content-typ | application/json |
    $iothub-enqueuedtime |  Tid när meddelandet skickades |
    $iothub-message-source | twinChangeEvents |
    $content-encoding | utf-8 |
    deviceId | ID för enheten |
    HubName | Namnet på IoT Hub |
    operationTimestamp | [ISO8601](https://en.wikipedia.org/wiki/ISO_8601) tidsstämpeln för åtgärden |
    iothub-message-schema | deviceLifecycleNotification |
    opType | ”replaceTwin” eller ”updateTwin” |

    Meddelandet Systemegenskaper föregås den `$` symbolen.

  - Innehåll
        
    Det här avsnittet innehåller alla twin ändringar i JSON-format. Den använder samma format som en korrigeringsfil, med skillnaden att den kan innehålla alla twin avsnitt: taggar, properties.reported, properties.desired och att den innehåller ”$metadata”-element. Exempel:

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

Stöd för alla åtgärder för föregående [Optimistisk samtidighet](iot-hub-devguide-device-twins.md#optimistic-concurrency) och kräver den **ServiceConnect** behörighet, enligt definitionen i [styra åtkomsten till IoT Hub](iot-hub-devguide-security.md).

Utöver dessa åtgärder kan kan lösningens serverdel:

* Fråga efter enhetstvillingar med hjälp av SQL-liknande [IoT Hub-frågespråk](iot-hub-devguide-query-language.md).

* Utföra åtgärder på stora mängder enhetstvillingar med [jobb](iot-hub-devguide-jobs.md).

## <a name="device-operations"></a>Åtgärder

App för enheter som körs på enhetstvillingen med hjälp av följande atomiska åtgärder:

* **Hämta enhetstvillingen**. Den här åtgärden returnerar device twin dokumentet (inklusive taggar och önskade och rapporterade Systemegenskaper) för den anslutna enheten.

* **Delvis uppdatera rapporterade egenskaper**. Den här åtgärden aktiverar deluppdatering av de rapporterade egenskaperna för den anslutna enheten. Den här åtgärden använder samma JSON update-format dessa lösningen igen för en deluppdatering av önskade egenskaper.

* **Notera önskade egenskaper**. Den anslutna enheten kan välja att meddelas om uppdateringar de önskade egenskaperna när de inträffar. Enheten tar emot samma formulär för uppdatering (partiell eller fullständig ersättning) som körs av lösningens serverdel.

Alla föregående åtgärder kräver den **DeviceConnect** behörighet, enligt definitionen i [styra åtkomsten till IoT Hub](iot-hub-devguide-security.md).

Den [SDK: er för Azure IoT-enheter](iot-hub-devguide-sdks.md) gör det enkelt att använda föregående åtgärder från många språk och plattformar. Mer information om information om IoT Hub primitiver för synkronisering av önskade egenskaper finns i [enheten återanslutning flow](iot-hub-devguide-device-twins.md#device-reconnection-flow).

## <a name="tags-and-properties-format"></a>Format för taggar och egenskaper

Taggar och önskade egenskaper rapporterade egenskaper är JSON-objekt med följande begränsningar:

* Alla nycklar i JSON-objekt är skiftlägeskänsliga 64 byte UTF-8 UNICODE-strängar. Tillåtna tecken undanta Unicode-kontrolltecken (segment C0 och C1), och `.`, `$`, och SP

* Alla värden i JSON-objekt kan vara följande typer av JSON: booleskt värde, tal, string, object. Matriser tillåts inte. Det högsta värdet för Integer-värden är 4503599627370495 och det lägsta värdet för Integer-värden är-4503599627370496.

* Alla JSON-objekt i taggar, önskad och rapporterade egenskaper kan ha högst 5. Följande objekt är exempelvis giltig:

   ```json
   {
       ...
       "tags": {
           "one": {
               "two": {
                   "three": {
                       "four": {
                           "five": {
                               "property": "value"
                           }
                       }
                   }
               }
           }
       },
       ...
   }
   ```

* Alla strängvärden får vara högst 512 byte i längd.

## <a name="device-twin-size"></a>Enheten twin storlek

IoT Hub tillämpar en begränsning på 8KB storlek på var och en av de totalt värdena i `tags`, `properties/desired`, och `properties/reported`, exklusive skrivskyddade element.

Storleken beräknas genom att räkna alla tecken, förutom Unicode-kontrolltecken (segment C0 och C1) och blanksteg som är utanför strängkonstanter.

IoT Hub avvisar alla åtgärder som kan öka storleken på dessa dokument över gränsen med ett fel.

## <a name="device-twin-metadata"></a>Twin enhetsmetadata

IoT Hub underhåller tidsstämpel för senaste uppdateringen för varje JSON-objekt i enhetstvilling önskad och rapporterade egenskaper. Tidsstämplar i UTC och kodas i den [ISO8601](https://en.wikipedia.org/wiki/ISO_8601) format `YYYY-MM-DDTHH:MM:SS.mmmZ`.

Exempel:

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

Den här informationen sparas på alla nivåer (inte bara löv av JSON-strukturen) för att spara uppdateringar som tar bort objektnycklar.

## <a name="optimistic-concurrency"></a>Optimistisk samtidighet

Taggar och önskade och rapporterade egenskaper alla stöd för Optimistisk samtidighet.
Taggar har en ETag enligt [RFC7232](https://tools.ietf.org/html/rfc7232), som representerar den taggen JSON-representation. Du kan använda ETags i villkorlig uppdateringsåtgärder från lösningens backend-server för att garantera konsekvens.

Enhetstvillingen önskad och rapporterade egenskaper har inte ETags, men har en `$version` värdet som garanterat är en inkrementell. På samma sätt till en ETag kan versionen användas av den uppdatera parten för att upprätthålla konsekvens av uppdateringar. Exempelvis kan en app för enheter för en rapporterad egenskap eller lösningens backend-server för en önskad egenskap.

Versioner är också användbart när en observing agent (till exempel app för enheter får de önskade egenskaperna) måste stämma av förenkling mellan resultatet av en hämta-åtgärden och ett meddelande. Den [enhet återanslutning flödet kontrollerar](iot-hub-devguide-device-twins.md#device-reconnection-flow) innehåller mer information.

## <a name="device-reconnection-flow"></a>Enheten återanslutning flöde

IoT Hub bevaras inte meddelanden om uppdateringar för önskade egenskaper för frånkopplade enheter. Det följer att en enhet som ansluter måste hämta dokumentet fullständig önskade egenskaper, förutom att prenumerera på meddelanden om uppdateringar. Möjlighet för förenkling mellan meddelanden om uppdateringar och fullständig hämtning, måste följande flöde säkerställas:

1. App för enheter som ansluter till en IoT-hubb.
2. App för enheter som prenumererar för önskade egenskaper meddelanden om uppdateringar.
3. Enhetsapp hämtar det fullständiga dokumentet för önskade egenskaper.

App för enheter kan ignorera alla meddelanden med `$version` mindre än eller lika med versionen av det fullständiga hämtade dokumentet. Den här metoden är möjligt eftersom IoT Hub garanterar att det alltid öka versioner.

> [!NOTE]
> Den här logiken används redan i den [SDK: er för Azure IoT-enheter](iot-hub-devguide-sdks.md). Den här beskrivningen är användbart om enhetsappen kan inte använda någon av SDK: er för Azure IoT-enheter och program måste MQTT-gränssnittet direkt.
> 

## <a name="additional-reference-material"></a>Ytterligare referensmaterial

Andra referensavsnitten i IoT Hub developer guide inkluderar:

* Den [IoT Hub-slutpunkter](iot-hub-devguide-endpoints.md) artikeln beskrivs de olika slutpunkter som varje IoT-hubb exponerar för körning och hanteringsåtgärder.

* Den [begränsning och kvoter](iot-hub-devguide-quotas-throttling.md) artikeln beskrivs kvoter som gäller för IoT Hub-tjänsten och beteendet som händer när du använder tjänsten.

* Den [Azure IoT SDK: er med enheten och tjänsten](iot-hub-devguide-sdks.md) artikeln innehåller olika språk SDK: er som du kan använda när du utvecklar appar för både enheten och tjänsten som interagerar med IoT Hub.

* Den [IoT Hub-frågespråk för enhetstvillingar, jobb och meddelanderoutning](iot-hub-devguide-query-language.md) artikeln IoT Hub-frågespråk som du kan använda för att hämta information från IoT Hub om enhetstvillingar och jobb.

* Den [IoT Hub MQTT-support](iot-hub-mqtt-support.md) artikeln innehåller mer information om IoT Hub-stöd för MQTT-protokollet.

## <a name="next-steps"></a>Nästa steg

Nu har du lärt dig om enhetstvillingar, du kanske är intresserad av i följande avsnitt för IoT Hub developer guide:

* [Förstå och använda modultvillingar i IoT Hub](iot-hub-devguide-module-twins.md)
* [Anropa en direkt metod på en enhet](iot-hub-devguide-direct-methods.md)
* [Schemalägga jobb på flera enheter](iot-hub-devguide-jobs.md)

I följande självstudier får IoT Hub för att prova några av de koncept som beskrivs i den här artikeln:

* [Hur du använder enhetstvillingen](iot-hub-node-node-twin-getstarted.md)
* [Så här använder du tvillingegenskaper](tutorial-device-twins.md)
* [Enhetshantering med Azure IoT-verktyg för VS Code](iot-hub-device-management-iot-toolkit.md)
