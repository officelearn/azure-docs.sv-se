---
title: "Förstå Azure IoT Hub-enhet twins | Microsoft Docs"
description: "Utvecklarhandbok - Använd enhet twins att synkronisera och konfigurationsändringar data mellan IoT-hubb och dina enheter"
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: 
ms.assetid: 8a3da072-a5bf-46e5-8de4-24cdbb2a03fa
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/19/2017
ms.author: elioda
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: afadedf72562452e4d57d4545efe59cd8d37c907
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2017
---
# <a name="understand-and-use-device-twins-in-iot-hub"></a>Förstå och använda enheten twins i IoT-hubb

*Enheten twins* är JSON-dokument som lagrar tillstånd enhetsinformation inklusive metadata, konfigurationer och villkor. Azure IoT-hubb upprätthåller en enhet dubbla för varje enhet som du ansluter till IoT-hubb. Den här artikeln beskrivs:

* Strukturen för enheten dubbla: *taggar*, *önskade* och *rapporterade egenskaper*.
* De åtgärder som appar för enheter och -servrar kan utföra på enheten twins.

Använd enhet twins till:

* Lagra enhetsspecifika metadata i molnet. Till exempel distributionsplatsen för en Varuautomat.
* Rapportera aktuell statusinformation, till exempel tillgängliga funktioner och villkor från din enhet. Till exempel en enhet är ansluten till din IoT-hubb över mobil- eller WiFi.
* Synkronisera tillståndet för tidskrävande arbetsflöden mellan enhetsapp och backend-app. Till exempel när lösningen tillbaka slutet anger den nya versionen på inbyggd programvara ska installeras och appen enheter rapporterar de olika stegen för uppdateringsprocessen.
* Fråga din enhetsmetadata, konfiguration eller tillstånd.

Referera till [enhet till moln kommunikation vägledning] [ lnk-d2c-guidance] anvisningar om hur du använder rapporterade egenskaper, meddelanden från enhet till moln eller ladda upp filen.
Referera till [moln till enhet kommunikation vägledning] [ lnk-c2d-guidance] anvisningar om hur du använder egenskaper, direkt metoder eller moln till enhet meddelanden.

## <a name="device-twins"></a>Enheten twins
Enheten twins lagra enhetsrelaterade information som:

* Enhet och tillbaka slutar använda för att synkronisera enheten villkor och konfiguration.
* Lösningens serverdel kan använda för att fråga och mål långvariga åtgärder.

Livscykeln för en enhet dubbla är kopplad till motsvarande [enhetsidentitet][lnk-identity]. Enheten twins implicit skapas och tas bort när en enhetsidentitet skapas eller tas bort i IoT-hubb.

En enhet dubbla är en JSON-dokument som innehåller:

* **Taggar**. En del av JSON-dokumentet som lösningens serverdel kan läsa från och skriva till. Taggar visas inte för appar för enheter.
* **Egenskaper för Desired**. Används tillsammans med rapporterade egenskaper för att synkronisera enhetskonfigurationen eller villkor. Appen enheten kan läsa dem lösningens serverdel kan ange egenskaper. Appen enheten kan också ta emot meddelanden om ändringar i egenskaperna.
* **Rapporterade egenskaper**. Används tillsammans med egenskaper för att synkronisera enhetskonfigurationen eller villkor. Enheten appen kan ange rapporterade egenskaper och lösningens serverdel kan läsa och fråga dem.

Dessutom roten på enhet dubbla JSON-dokumentet innehåller skrivskyddade egenskaper från motsvarande enhetens identitet lagras i den [identitetsregistret][lnk-identity].

![][img-twin]

I följande exempel visas en enhet dubbla JSON-dokumentet:

        {
            "deviceId": "devA",
            "generationId": "123",
            "status": "enabled",
            "statusReason": "provisioned",
            "connectionState": "connected",
            "connectionStateUpdatedTime": "2015-02-28T16:24:48.789Z",
            "lastActivityTime": "2015-02-30T16:24:48.789Z",

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
                    }
                    "batteryLevel": 55,
                    "$metadata" : {...},
                    "$version": 4
                }
            }
        }

I rotobjektet Systemegenskaper, och behållarobjekt för `tags` och båda `reported` och `desired` egenskaper. Den `properties` behållaren innehåller vissa skrivskyddad element (`$metadata`, `$etag`, och `$version`) beskrivs i den [enhetens dubbla metadata] [ lnk-twin-metadata] och [Optimistisk samtidighet] [ lnk-concurrency] avsnitt.

### <a name="reported-property-example"></a>Rapporterat egenskapen exempel
I det förra exemplet, enhet dubbla innehåller en `batteryLevel` egenskap som rapporteras av appen enhet. Den här egenskapen gör det möjligt att fråga efter och fungerar på enheter utifrån den senaste rapporterade batterinivån. Andra exempel är enheten app reporting enhetsfunktioner eller alternativ för nätverksanslutning.

> [!NOTE]
> Rapporterat egenskaper förenkla scenarier där lösningens serverdel är intresserad av det senaste kända värdet för en egenskap. Använd [meddelanden från enhet till moln] [ lnk-d2c] om lösningens serverdel behöver för att bearbeta enhetstelemetrin i form av tidsstämplad händelser, till exempel tidsserier.

### <a name="desired-property-example"></a>Exempel på önskade egenskapen
I föregående exempel är den `telemetryConfig` enheten dubbla önskad och rapporterade egenskaper används av lösningens serverdel och enheter appen synkronisera telemetri konfigurationen för den här enheten. Exempel:

1. Lösningens serverdel egenskapen önskade med önskad konfiguration-värde. Här är del av dokumentet med de önskade egenskapen:
   
        ...
        "desired": {
            "telemetryConfig": {
                "sendFrequency": "5m"
            },
            ...
        },
        ...
2. Enheten appen meddelas ändra omedelbart om ansluten, eller vid första återanslutning. Appen enheten rapporterar den uppdaterade konfigurationen (eller ett fel villkor med hjälp av den `status` egenskap). Här är del av rapporterade egenskaper:
   
        ...
        "reported": {
            "telemetryConfig": {
                "sendFrequency": "5m",
                "status": "success"
            }
            ...
        }
        ...
3. Lösningens serverdel kan spåra resultatet av åtgärden configuration mellan många olika enheter, av [frågar] [ lnk-query] twins för enheten.

> [!NOTE]
> Föregående kodfragment är exempel, optimerade för läsbarhet på ett sätt att koda en enhetskonfiguration och dess status. IoT-hubb medför inte ett visst schema för enheten dubbla önskad och rapporterade egenskaper i twins för enheten.
> 
> 

Du kan använda twins för att synkronisera långvariga åtgärder, t.ex uppdateringar av inbyggd programvara. Mer information om hur du använder egenskaper för att synkronisera och spåra långvarig åtgärd mellan enheter finns [Använd önskad egenskaper att konfigurera enheter][lnk-twin-properties].

## <a name="back-end-operations"></a>Backend-åtgärder
Lösningens serverdel körs på den enheten dubbla med hjälp av följande atomiska åtgärder exponeras via HTTPS:

* **Hämta enheten dubbla med id**. Den här åtgärden Returnerar enheten dubbla dokument, inklusive taggar och önskade och rapporterade Systemegenskaper.
* **Delvis uppdatera enheten dubbla**. Den här åtgärden kan lösningens serverdel delvis uppdaterar taggar eller önskade egenskaper i en delad enhet. Delvis uppdatering uttrycks som ett JSON-dokument som läggs till eller uppdaterar en egenskap. Ange egenskaper `null` tas bort. I följande exempel skapas en ny önskad egenskap med värdet `{"newProperty": "newValue"}`, skriver över det befintliga värdet av `existingProperty` med `"otherNewValue"`, och tar bort `otherOldProperty`. Några andra ändringar har gjorts i befintliga egenskaper eller taggar:
   
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
* **Ersätt egenskaper**. Den här åtgärden aktiverar lösningens serverdel att skriva över alla befintliga egenskaper och ersätta ett nytt JSON-dokument för `properties/desired`.
* **Ersätt taggar**. Den här åtgärden aktiverar lösningens serverdel att skriva över alla befintliga taggar och ersätta ett nytt JSON-dokument för `tags`.
* **Ta emot meddelanden med dubbla**. Den här åtgärden kan lösningens serverdel ska meddelas när dubbla ändras. Om du vill göra det, IoT-lösningen behöver skapa en väg och datakällan ska vara lika med *twinChangeEvents*. Inga dubbla meddelanden skickas som standard, som är det inför finns ingen sådan vägar. Om ändringshastigheten är för hög eller av andra orsaker, till exempel internt fel IoT-hubben kan skicka endast ett meddelande som innehåller alla ändringar. Så om ditt program måste tillförlitliga granskning och loggning av alla mellanliggande tillstånd, sedan fortfarande rekommenderas att du använder D2C meddelanden. Dubbla meddelandet innehåller egenskaperna och innehållet.

    - Egenskaper

    | Namn | Värde |
    | --- | --- |
    $content-typ | application/json |
    $iothub-enqueuedtime |  Tidpunkt som meddelandet skickades |
    $iothub-meddelande-källa | twinChangeEvents |
    $content-kodning | UTF-8 |
    deviceId | ID för enheten |
    hubName | Namnet på IoT-hubb |
    operationTimestamp | [ISO8601] tidsstämpeln för åtgärden |
    iothub-meddelande-schema | deviceLifecycleNotification |
    opType | ”replaceTwin” eller ”updateTwin” |

    Meddelandet Systemegenskaper föregås av `'$'` symbolen.

    - Innehåll
        
    Det här avsnittet innehåller dubbla ändringarna i en JSON-format. Samma format används som en korrigering, med skillnaden att den kan innehålla alla två avsnitt: taggar, properties.reported, properties.desired och att den innehåller ”$metadata”-element. Exempel:
    ```
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

Stöd för alla föregående operationer [Optimistisk samtidighet] [ lnk-concurrency] och kräver den **ServiceConnect** behörighet, som definieras i den [säkerhet] [ lnk-security] artikel.
 
Förutom dessa åtgärder kan lösningens serverdel

* Fråga enhet-twins med hjälp av SQL-liknande [IoT-hubb frågespråket][lnk-query].
* Utföra åtgärder på stora mängder enheten twins med [jobb][lnk-jobs].

## <a name="device-operations"></a>Åtgärder för enhet
Enheten appen körs på den enheten dubbla med hjälp av följande atomiska åtgärder:

* **Hämta enheten dubbla**. Den här åtgärden Returnerar enheten dubbla dokumentet (inklusive taggar och önskade och rapporterade Systemegenskaper) för den anslutna enheten.
* **Uppdatera delvis rapporterade egenskaper**. Den här åtgärden aktiverar deluppdatering rapporterade egenskaper för den anslutna enheten. Den här åtgärden används samma JSON update format-att lösningen tillbaka för en deluppdatering av egenskaper.
* **Se egenskaper**. Den anslutna enheten kan välja att aviseras om uppdateringar av egenskaperna när de inträffar. Enheten tar emot uppdateringen (eller delvis ersättning) som körs av lösningens serverdel samma formulär.

Alla föregående operationer kräver den **DeviceConnect** behörighet, som definieras i den [säkerhet] [ lnk-security] artikel.

Den [Azure IoT-enhet SDK] [ lnk-sdks] gör det lättare att använda föregående åtgärder från många språk och plattformar. Mer information om information om IoT-hubb primitiver för egenskaper synkronisering finns [enheten återanslutning flödet][lnk-reconnection].

## <a name="tags-and-properties-format"></a>Taggar och egenskaper format
Taggar, önskade egenskaper och rapporterade egenskaper är JSON-objekt med följande begränsningar:

* Alla nycklar i JSON-objekt är skiftlägeskänsliga 64 byte UTF-8, UNICODE-strängar. Tillåtna tecken undanta Unicode-kontrolltecken (segment C0 och C1) och `'.'`, `' '`, och `'$'`.
* Alla värden i JSON-objekt kan vara följande typer av JSON: boolean, nummer, sträng, objekt. Matriser är inte tillåtna. Det maximala värdet för heltal är 4503599627370495 och det lägsta värdet för heltal är-4503599627370496.
* JSON-objekt i taggar, önskade och rapporterade egenskaper kan ha högst 5. Följande objekt är till exempel giltig:

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

* Alla strängvärden kan vara högst 4 KB längd.

## <a name="device-twin-size"></a>Enheten dubbla storlek
IoT-hubb tillämpar en begränsning på 8KB storlek på de totala värdena för `tags`, `properties/desired`, och `properties/reported`, exklusive skrivskyddade element.
Storleken beräknas genom att räkna alla tecken, förutom Unicode-kontrolltecken (segment C0 och C1) och blanksteg som befinner sig utanför strängkonstanter.
IoT-hubb avvisar alla åtgärder som kan öka storleken på dessa dokument än gränsen med ett fel.

## <a name="device-twin-metadata"></a>Enhetens dubbla metadata
IoT-hubb underhåller tidsstämpel för den senaste uppdateringen för varje JSON-objekt i enheten dubbla önskad och rapporterade egenskaper. Tidsstämplar i UTC och kodats i den [ISO8601] format `YYYY-MM-DDTHH:MM:SS.mmmZ`.
Exempel:

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
                    }
                    "batteryLevel": "55%",
                    "$metadata": {
                        "telemetryConfig": {
                            "sendFrequency": "5m",
                            "status": {
                                "$lastUpdated": "2016-03-31T16:35:48.789Z"
                            },
                            "$lastUpdated": "2016-03-31T16:35:48.789Z"
                        }
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

Denna information lagras på varje nivå (inte bara löv i JSON-strukturen) för att spara uppdateringar som tar bort objektnycklar.

## <a name="optimistic-concurrency"></a>Optimistisk samtidighet
Taggar, önskad och rapporterade egenskaper alla stöd för Optimistisk samtidighet.
Taggar har en ETag enligt [RFC7232], som representerar den tagg JSON-representation. Du kan använda ETags i villkorlig uppdateringsåtgärder från lösningens serverdel för att säkerställa konsekvens.

Enheten dubbla önskad och rapporterade egenskaper har inte ETags, men har en `$version` värde som garanterat inkrementell. På liknande sätt till en ETag kan versionen användas av uppdatering part vill använda konsekvent av uppdateringar. Till exempel en enhetsapp för en rapporterade egenskap eller lösningens serverdel för en önskad egenskap.

Versioner är också användbart när en observing agent (till exempel appen enhet observerar du egenskaperna) måste stämma lopp mellan resultatet av en hämta och ett uppdateringsmeddelande. Avsnittet [enheten återanslutning flödet] [ lnk-reconnection] innehåller mer information.

## <a name="device-reconnection-flow"></a>Enheten återanslutning flöde
IoT-hubb bevaras inte egenskaper uppdateringsmeddelanden för frånkopplade enheter. Följer att en enhet som ansluter måste hämta dokumentet med fullständig önskade egenskaper, utöver att prenumerera på meddelanden om uppdateringar. Möjlighet lopp mellan uppdateringsmeddelanden och fullständig hämtning, måste följande flöde säkerställas:

1. Enheten appen ansluter till en IoT-hubb.
2. Enhetsapp prenumererar för önskade egenskaper meddelanden om uppdateringar.
3. Enhetsapp hämtar det fullständiga dokumentet för egenskaper.

Appen enheten kan ignorera alla meddelanden med `$version` mindre än eller lika med versionen av det fullständiga hämtade dokumentet. Den här metoden är möjligt eftersom IoT-hubb garanterar att det alltid öka versioner.

> [!NOTE]
> Den här logiken har redan implementerats i den [Azure IoT-enhet SDK][lnk-sdks]. Den här beskrivningen är användbart om enheten appen kan inte använda någon av Azure IoT-enhet SDK: er och programmet måste gränssnittet MQTT direkt.
> 
> 

## <a name="additional-reference-material"></a>Ytterligare referensmaterialet
Andra referensavsnitten i utvecklarhandboken för IoT-hubben är:

* Den [IoT-hubbslutpunkter] [ lnk-endpoints] artikeln beskriver de olika slutpunkter som varje IoT-hubb visar för körning och hanteringsåtgärder.
* Den [begränsning och kvoter] [ lnk-quotas] artikeln kvoterna som gäller för IoT-hubb-tjänsten och bandbreddsbegränsning beteende som händer när du använder tjänsten.
* Den [Azure IoT-enheten och tjänsten SDK] [ lnk-sdks] artikeln innehåller olika språk SDK: er som du kan använda när du utvecklar appar för både enheten och tjänsten som interagerar med IoT-hubben.
* Den [IoT-hubb frågespråk för enheten twins, jobb och meddelanderoutning] [ lnk-query] IoT-hubb frågespråk som du kan använda för att hämta information från IoT-hubb om enheten twins och jobb för artikeln.
* Den [IoT-hubb MQTT stöd] [ lnk-devguide-mqtt] artikeln innehåller mer information om IoT-hubb stöd för protokollet MQTT.

## <a name="next-steps"></a>Nästa steg
Nu har du fått veta om enheten twins du vill ha i följande IoT-hubb developer guide avsnitt:

* [Anropa en metod som är direkt på en enhet][lnk-methods]
* [Schema-jobb på flera enheter][lnk-jobs]

Om du vill testa vissa av de begrepp som beskrivs i den här artikeln får du är intresserad av IoT-hubb följande kurser:

* [Hur du använder enheten dubbla][lnk-twin-tutorial]
* [Hur du använder identiska enhetsegenskaper][lnk-twin-properties]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-identity]: iot-hub-devguide-identity-registry.md
[lnk-d2c]: iot-hub-devguide-messages-d2c.md
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-security]: iot-hub-devguide-security.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md

[ISO8601]: https://en.wikipedia.org/wiki/ISO_8601
[RFC7232]: https://tools.ietf.org/html/rfc7232
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-twin-tutorial]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-properties]: iot-hub-node-node-twin-how-to-configure.md
[lnk-twin-metadata]: iot-hub-devguide-device-twins.md#device-twin-metadata
[lnk-concurrency]: iot-hub-devguide-device-twins.md#optimistic-concurrency
[lnk-reconnection]: iot-hub-devguide-device-twins.md#device-reconnection-flow

[img-twin]: media/iot-hub-devguide-device-twins/twin.png
