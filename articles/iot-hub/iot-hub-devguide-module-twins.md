---
title: Förstå Azure IoT Hub-modultvillingar | Microsoft-dokument
description: Utvecklarguide – använd modultvillingar för att synkronisera tillstånds- och konfigurationsdata mellan IoT Hub och dina enheter
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/01/2020
ms.author: menchi
ms.openlocfilehash: 5ef6c4de288a764abbe434c5d84fc99e154f7492
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303604"
---
# <a name="understand-and-use-module-twins-in-iot-hub"></a>Förstå och använda modultvillingar i IoT Hub

Den här artikeln förutsätter att du har läst [Förstå och använda enhetstvillingar i IoT Hub](iot-hub-devguide-device-twins.md) först. I IoT Hub, under varje enhetsidentitet, kan du skapa upp till 20 modulidentiteter. Varje modulidentitet genererar implicit en modultvilling. I likhet med enhetstvillingar är modultvillingar JSON-dokument som lagrar information om modultillstånd, inklusive metadata, konfigurationer och villkor. Azure IoT Hub underhåller en modultvilling för varje modul som du ansluter till IoT Hub. 

På enhetssidan kan du med IoT Hub-enhetens SDK:er skapa moduler där var och en öppnar en oberoende anslutning till IoT Hub. Med den här funktionen kan du använda separata namnområden för olika komponenter på enheten. Till exempel har du en varuautomat som har tre olika sensorer. Varje sensor styrs av olika avdelningar i ditt företag. Du kan skapa en modul för varje sensor. På så sätt kan varje avdelning bara skicka jobb eller direkta metoder till sensorn som de styr, undvika konflikter och användarfel.

 Modulidentitet och modultvilling ger samma funktioner som enhetsidentitet och enhetstvilling men med en finare granularitet. Den här finare granulariteten gör det möjligt för kompatibla enheter, till exempel operativsystembaserade enheter eller enheter för inbyggd programvara som hanterar flera komponenter, att isolera konfiguration och villkor för var och en av dessa komponenter. Modul identitet och tvillingar modul ger en hantering separation av problem när du arbetar med IoT-enheter som har modulära programvarukomponenter. Vi strävar efter att stödja alla enhetens dubbla funktionalitet på modultvillingnivå med modul twin allmän tillgänglighet. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

I den här artikeln beskrivs:

* Strukturen på modultvillingen: *taggar*, *önskade* och *rapporterade egenskaper*.
* De åtgärder som modulerna och backändarna kan utföra på modultvillingar.

Se [kommunikationsvägledning](iot-hub-devguide-d2c-guidance.md) för enhet till moln för vägledning om hur du använder rapporterade egenskaper, meddelanden från enhet till moln eller filöverföring.

Se [kommunikationsvägledning](iot-hub-devguide-c2d-guidance.md) från molnet till enhet för vägledning om hur du använder önskade egenskaper, direkta metoder eller meddelanden från molnet till enheten.

## <a name="module-twins"></a>Modul tvillingar

Modultvillingar lagrar modulrelaterad information som:

* Moduler på enheten och IoT Hub kan användas för att synkronisera modulvillkor och konfiguration.

* Lösningens baksida kan användas för att fråga och rikta långvariga åtgärder.

Livscykeln för en modultvilling är länkad till motsvarande [modulidentitet](iot-hub-devguide-identity-registry.md). Moduler tvillingar skapas implicit och tas bort när en modul identitet skapas eller tas bort i IoT Hub.

En modultvilling är ett JSON-dokument som innehåller:

* **Taggar**. Ett avsnitt i JSON-dokumentet som lösningens baksida kan läsa från och skriva till. Taggar är inte synliga för moduler på enheten. Taggar anges för frågesyfte.

* **Önskade egenskaper**. Används tillsammans med rapporterade egenskaper för att synkronisera modulkonfiguration eller villkor. Lösningens baksida kan ange önskade egenskaper och modulappen kan läsa dem. Modulappen kan också ta emot meddelanden om ändringar i önskade egenskaper.

* **Rapporterade egenskaper**. Används tillsammans med önskade egenskaper för att synkronisera modulkonfiguration eller villkor. Modulappen kan ange rapporterade egenskaper och lösningens baksida kan läsa och fråga dem.

* **Egenskaper för modulidentitet**. Roten till modultvillingenS JSON-dokument innehåller skrivskyddade egenskaper från motsvarande modulidentitet som lagras i [identitetsregistret](iot-hub-devguide-identity-registry.md).

![Arkitektonisk representation av enhetstvilling](./media/iot-hub-devguide-device-twins/module-twin.jpg)

I följande exempel visas ett modultvilling-JSON-dokument:

```json
{
    "deviceId": "devA",
    "moduleId": "moduleA",
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

I rotobjektet finns modulens identitetsegenskaper `tags` och `reported` `desired` behållarobjekt för och båda och egenskaper. Behållaren `properties` innehåller vissa skrivskyddade`$metadata` `$etag`element `$version`( , och ) som beskrivs i avsnitten [Modultvill metadata](iot-hub-devguide-module-twins.md#module-twin-metadata) och [Optimistisk samtidighet.](iot-hub-devguide-device-twins.md#optimistic-concurrency)

### <a name="reported-property-example"></a>Exempel på rapporterad egenskap

I föregående exempel innehåller modultvillingen en `batteryLevel` egenskap som rapporteras av modulappen. Den här egenskapen gör det möjligt att fråga och använda moduler baserat på den senast rapporterade batterinivån. Andra exempel är modulapprapporteringsmodulens funktioner eller anslutningsalternativ.

> [!NOTE]
> Rapporterade egenskaper förenklar scenarier där lösningens backend är intresserad av det senast kända värdet för en egenskap. Använd meddelanden från enhet till moln om [lösningens](iot-hub-devguide-messages-d2c.md) serverdel behöver bearbeta modultelemetri i form av sekvenser av tidsstämplade händelser, till exempel tidsserier.

### <a name="desired-property-example"></a>Exempel på önskad egenskap

I föregående exempel `telemetryConfig` används modulet twin önskade och rapporterade egenskaper av lösningens serverdel och modulappen för att synkronisera telemetrikonfigurationen för den här modulen. Ett exempel:

1. Lösningens serverdel anger önskad egenskap med önskat konfigurationsvärde. Här är den del av dokumentet med önskad egenskapsuppsättning:

    ```json
    ...
    "desired": {
        "telemetryConfig": {
            "sendFrequency": "5m"
        },
        ...
    },
    ...
    ```

2. Modulappen meddelas om ändringen omedelbart om den är ansluten eller vid den första återanslutningen. Modulappen rapporterar sedan den uppdaterade konfigurationen (eller ett feltillstånd med hjälp av egenskapen). `status` Här är den del av de rapporterade egenskaperna:

    ```json
    "reported": {
        "telemetryConfig": {
            "sendFrequency": "5m",
            "status": "success"
        }
        ...
    }
    ```

3. Lösningens serverdel kan spåra resultatet av konfigurationsåtgärden i många moduler genom [att fråga](iot-hub-devguide-query-language.md) modultvillingar.

> [!NOTE]
> De föregående kodavsnitten är exempel, optimerade för läsbarhet, på ett sätt att koda en modulkonfiguration och dess status. IoT Hub innehåller inte ett specifikt schema för de önskade och rapporterade egenskaperna för modultvillingarna.
> 
> 

## <a name="back-end-operations"></a>Backend-verksamhet
Lösningens backdel fungerar på modultvillingen med hjälp av följande atomoperationer, exponeras via HTTPS:

* **Hämta modultvillingen efter ID**. Den här åtgärden returnerar modultvillingdokumentet, inklusive taggar och önskade och rapporterade systemegenskaper.

* **Delvis uppdatera modul tvilling**. Med den här åtgärden kan lösningens backend delvis uppdatera taggarna eller önskade egenskaper i en modultvilling. Den partiella uppdateringen uttrycks som ett JSON-dokument som lägger till eller uppdaterar en egenskap. Egenskaper som `null` ska tas bort. I följande exempel skapas en `{"newProperty": "newValue"}`ny önskad egenskap med `existingProperty` värde, skriver över det befintliga värdet med `"otherNewValue"`och tar bort `otherOldProperty`. Inga andra ändringar görs i befintliga önskade egenskaper eller taggar:

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

* **Ta emot dubbla meddelanden**. Med den här åtgärden kan lösningens backend meddelas när tvillingen ändras. För att göra det måste din IoT-lösning skapa en väg och ange datakällan som är lika *med twinChangeEvents*. Som standard skickas inga dubbla meddelanden, det vill än inga sådana vägar som finns i förfår. Om förändringshastigheten är för hög eller av andra orsaker, till exempel interna fel, kan IoT Hub bara skicka ett meddelande som innehåller alla ändringar. Om ditt program behöver tillförlitlig granskning och loggning av alla mellanliggande tillstånd bör du därför använda meddelanden från enhet till moln. Det dubbla meddelandemeddelandet innehåller egenskaper och brödtext.

  - Egenskaper

    | Namn | Värde |
    | --- | --- |
    $content typ | application/json |
    $iothub-enqueuedtime |  Tid när meddelandet skickades |
    $iothub-meddelande-källa | twinChangeEvents |
    $content-kodning | utf-8 |
    deviceId | ID för enheten |
    moduleId (modulId) | ID för modulen |
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

Alla föregående åtgärder stöder [optimistisk samtidighet](iot-hub-devguide-device-twins.md#optimistic-concurrency) och kräver **ServiceConnect-behörigheten,** enligt definitionen i artikeln [Kontrollåtkomst till IoT Hub.](iot-hub-devguide-security.md)

Utöver dessa åtgärder kan lösningens serverdel fråga modultvillingarna med hjälp av frågespråket SQL-liknande [IoT Hub](iot-hub-devguide-query-language.md).

## <a name="module-operations"></a>Modulverksamhet

Modulappen fungerar på modultvillingen med hjälp av följande atomära operationer:

* **Hämta modultvilling**. Den här åtgärden returnerar modultvillingdokumentet (inklusive taggar och önskade och rapporterade systemegenskaper) för den modul som för närvarande är ansluten.

* **Delvis uppdatera rapporterade egenskaper**. Den här åtgärden möjliggör partiell uppdatering av de rapporterade egenskaperna för den anslutna modulen. Den här åtgärden använder samma JSON-uppdateringsformat som lösningens back end använder för en partiell uppdatering av önskade egenskaper.

* **Observera önskade egenskaper**. Den modul som för närvarande är ansluten kan välja att meddelas om uppdateringar av önskade egenskaper när de inträffar. Modulen får samma form av uppdatering (partiell eller fullständig ersättning) som utförs av lösningens backend.

Alla föregående åtgärder kräver **behörigheten ModuleConnect,** enligt definitionen i artikeln [Kontrollåtkomst till IoT Hub.](iot-hub-devguide-security.md)

[Azure IoT-enhetenSDK:er](iot-hub-devguide-sdks.md) gör det enkelt att använda de föregående åtgärderna från många språk och plattformar.

## <a name="tags-and-properties-format"></a>Taggar och egenskapsformat

Taggar, önskade egenskaper och rapporterade egenskaper är JSON-objekt med följande begränsningar:

* **Nycklar:** Alla tangenter i JSON-objekt är skiftlägeskänsliga 64 byte UTF-8 UNICODE-strängar. Tillåtna tecken utesluter UNICODE-kontrolltecken (segment C0 och C1) och `.`, SP och `$`.

* **Värden:** Alla värden i JSON-objekt kan vara av följande JSON-typer: boolesk, tal, sträng, objekt. Matriser är inte tillåtna.

    * Heltal kan ha ett minimivärde på -4503599627370496 och ett högsta värde på 4503599627370495.

    * Strängvärden är UTF-8 kodade och kan ha en maximal längd på 512 byte.

* **Djup**: Alla JSON-objekt i taggar, önskade och rapporterade egenskaper kan ha ett maximalt djup på 5. Följande objekt är till exempel giltigt:

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

## <a name="module-twin-size"></a>Modul twin storlek

IoT Hub tillämpar en storleksgräns på `tags`8 kB för värdet för och `properties/desired` en `properties/reported`storleksgräns på 32 kB för värdet och . Dessa summor är exklusiva för skrivskyddade element som `$etag`, `$version`och `$metadata/$lastUpdated`.

Twin storlek beräknas på följande sätt:

* För varje egenskap i JSON-dokumentet beräknar IoT Hub kumulativt och lägger till längden på egenskapens nyckel och värde.

* Egenskapsnycklar betraktas som UTF8-kodade strängar.

* Enkla egenskapsvärden betraktas som UTF8-kodade strängar, numeriska värden (8 byte) eller booleskvärden (4 byte).

* Storleken på UTF8-kodade strängar beräknas genom att räkna alla tecken, exklusive UNICODE-kontrolltecken (segment C0 och C1).

* Komplexa egenskapsvärden (kapslade objekt) beräknas baserat på den sammanlagda storleken på egenskapsnycklarna och egenskapsvärdena som de innehåller.

IoT Hub avvisar med ett fel alla åtgärder som skulle öka storleken på dessa dokument över gränsen.

## <a name="module-twin-metadata"></a>Modul dubbla metadata

IoT Hub underhåller tidsstämpeln för den senaste uppdateringen för varje JSON-objekt i modultvilling önskade och rapporterade egenskaper. Tidsstämplarna är i UTC och kodade i `YYYY-MM-DDTHH:MM:SS.mmmZ` [ISO8601-format](https://en.wikipedia.org/wiki/ISO_8601) .
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

Modultvilling önskade och rapporterade egenskaper har `$version` inte ETags, men har ett värde som garanterat är inkrementellt. På samma sätt som en ETag kan versionen användas av uppdateringsparten för att framtvinga konsekvens av uppdateringar. Till exempel en modulapp för en rapporterad egenskap eller lösningens backend för en önskad egenskap.

Versioner är också användbara när en observationsagent (till exempel modulappen som observerar önskade egenskaper) måste stämma av kapplöpningar mellan resultatet av en hämtningsåtgärd och ett uppdateringsmeddelande. Avsnittet [Enhetsanslutningsflödet](iot-hub-devguide-device-twins.md#device-reconnection-flow) ger mer information. 

## <a name="next-steps"></a>Nästa steg

Information om hur du provar några av de begrepp som beskrivs i den här artikeln finns i följande IoT Hub-självstudier:

* [Komma igång med IoT Hub-modulidentitet och modultvilling med .NET-backend och .NET-enhet](iot-hub-csharp-csharp-module-twin-getstarted.md)
