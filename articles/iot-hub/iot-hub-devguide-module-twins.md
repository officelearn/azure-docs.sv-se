---
title: Förstå Azure IoT Hub modulen twins | Microsoft Docs
description: Utvecklarhandbok - Använd modulen twins att synkronisera och konfigurationsändringar data mellan IoT-hubb och dina enheter
services: iot-hub
documentationcenter: .net
author: chrissie926
manager: timlt
editor: ''
ms.assetid: 8a3da072-a5bf-46e5-8de4-24cdbb2a03fa
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/26/2018
ms.author: menchi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6fbbce06653e60cd914c2ed4d5990aac78ef53a8
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/03/2018
---
# <a name="understand-and-use-module-twins-in-iot-hub"></a>Förstå och använda modulen twins i IoT-hubb

Den här artikeln förutsätter att du har läst [förstå och använda enheten twins i IoT-hubb] [ lnk-devguide-device-twins] första. Under varje enhetsidentitet i IoT-hubb kan skapa upp till 20 modulen identiteter. Varje modul identitet genererar implicit en modul dubbla. Modulen twins liknar enheten twins, är JSON-dokument som lagrar tillstånd modulinformation inklusive metadata, konfigurationer och villkor. Azure IoT-hubb upprätthåller en modul dubbla för varje modul som du ansluter till IoT-hubb. 

På sidan enheten IoT Hub-enhet SDK kan du skapa moduler som varje öppnar en oberoende anslutning till IoT-hubb. På så sätt kan du använda separata namnområden för olika komponenter på enheten. Du har till exempel en Varuautomat som har tre olika sensorer. Varje sensor styrs av olika avdelningar inom företaget. Du kan skapa en modul för varje sensor. På så sätt kan varje avdelning kan bara skicka jobb eller direkt metoder sensor som de styr undvika konflikter och användarfel.

 Modulen identitets- och modulen dubbla innehåller samma funktioner som enheten identitets- och devic dubbla men på en ökad detaljnivå. Den här ökad detaljnivå kan kompatibla enheter som operativsystemet baserade enheter eller enheter för inbyggd hantering av flera komponenter, om du vill isolera konfiguration och villkor för var och en av dessa komponenter. Modulen identitet och modulen twins ger en hanteringen åtskillnad mellan problem när du arbetar med IoT-enheter som har modulära programkomponenter. Vi syftar till att stödja alla enheten identiska funktioner på modulen dubbla nivå av modulen dubbla allmän tillgänglighet. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Den här artikeln beskrivs:

* Strukturen för modulen dubbla: *taggar*, *önskade* och *rapporterade egenskaper*.
* De åtgärder som moduler och -servrar kan utföra på modulen twins.

Referera till [enhet till moln kommunikation vägledning] [ lnk-d2c-guidance] anvisningar om hur du använder rapporterade egenskaper, meddelanden från enhet till moln eller ladda upp filen.
Referera till [moln till enhet kommunikation vägledning] [ lnk-c2d-guidance] anvisningar om hur du använder egenskaper, direkt metoder eller moln till enhet meddelanden.

## <a name="module-twins"></a>Modulen twins
Modulen twins lagra modul-relaterad information som:

* Moduler på enheten och IoT-hubb kan använda för att synkronisera modulen villkor och konfiguration.
* Lösningens serverdel kan använda för att fråga och mål långvariga åtgärder.

Livscykeln för en modul dubbla är kopplad till motsvarande [modulen identitet][lnk-identity]. Moduler twins implicit skapas och tas bort när en modul identitet skapas eller tas bort i IoT-hubb.

En modul dubbla är en JSON-dokument som innehåller:

* **Taggar**. En del av JSON-dokumentet som lösningens serverdel kan läsa från och skriva till. Taggar visas inte för moduler på enheten. Taggar är inställda för frågor syfte.
* **Egenskaper för Desired**. Används tillsammans med rapporterade egenskaper för att synkronisera Modulkonfiguration eller villkor. Modul-appen kan läsa dem lösningens serverdel kan ange egenskaper. Modul-appen kan också ta emot meddelanden om ändringar i egenskaperna.
* **Rapporterade egenskaper**. Används tillsammans med egenskaper för att synkronisera Modulkonfiguration eller villkor. Modul-appen kan ange rapporterade egenskaper och lösningens serverdel kan läsa och fråga dem.
* **Modulen identitetsegenskaper**. Roten för modulen dubbla JSON-dokumentet innehåller skrivskyddade egenskaper från den motsvarande modul identiteten som lagras i den [identitetsregistret][lnk-identity].

![][img-module-twin]

I följande exempel visas en modul dubbla JSON-dokumentet:

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
            }
            "batteryLevel": 55,
            "$metadata" : {...},
            "$version": 4
        }
    }
}
```

I rotobjektet modulen identitetsegenskaper, och behållarobjekt för `tags` och båda `reported` och `desired` egenskaper. Den `properties` behållaren innehåller vissa skrivskyddad element (`$metadata`, `$etag`, och `$version`) beskrivs i den [modulen dubbla metadata] [ lnk-module-twin-metadata] och [ Optimistisk samtidighet] [ lnk-concurrency] avsnitt.

### <a name="reported-property-example"></a>Rapporterat egenskapen exempel
I föregående exempel är identiska för modulen innehåller en `batteryLevel` egenskap som rapporteras av appen modulen. Den här egenskapen gör det möjligt att fråga efter och fungerar på moduler baserat på den senaste rapporterade batterinivån. Andra exempel är modulen app modulen rapporteringsfunktioner eller alternativ för nätverksanslutning.

> [!NOTE]
> Rapporterat egenskaper förenkla scenarier där lösningens serverdel är intresserad av det senaste kända värdet för en egenskap. Använd [meddelanden från enhet till moln] [ lnk-d2c] om lösningens serverdel behöver för att bearbeta modulen telemetri i form av tidsstämplad händelser, till exempel tidsserier.

### <a name="desired-property-example"></a>Exempel på önskade egenskapen
I föregående exempel är den `telemetryConfig` modulen dubbla önskad och rapporterade egenskaper används av lösningens serverdel och modulen appen synkronisera telemetri konfigurationen för den här modulen. Exempel:

1. Lösningens serverdel egenskapen önskade med önskad konfiguration-värde. Här är del av dokumentet med de önskade egenskapen:

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

2. Modulen appen meddelas ändra omedelbart om ansluten, eller vid första återanslutning. Modulen appen rapporterar den uppdaterade konfigurationen (eller ett fel villkor med hjälp av den `status` egenskap). Här är del av rapporterade egenskaper:

    ```json
    ...
    "reported": {
        "telemetryConfig": {
            "sendFrequency": "5m",
            "status": "success"
        }
        ...
    }
    ...
    ```

3. Lösningens serverdel kan spåra resultatet av åtgärden configuration mellan flera moduler av [frågar] [ lnk-query] modulen twins.

> [!NOTE]
> Föregående kodfragment är exempel, optimerade för läsbarhet på ett sätt att koda en konfiguration och dess status. IoT-hubb medför inte ett visst schema för modulen dubbla önskad och rapporterade egenskaper i modulen twins.
> 
> 

## <a name="back-end-operations"></a>Backend-åtgärder
Lösningens serverdel körs på den modul dubbla med hjälp av följande atomiska åtgärder exponeras via HTTPS:

* **Hämta modul dubbla efter ID**. Den här åtgärden returnerar modulen dubbla dokument, inklusive taggar och önskade och rapporterade Systemegenskaper.
* **Uppdatera delvis modulen dubbla**. Den här åtgärden kan lösningens serverdel delvis uppdaterar taggar eller önskade egenskaper i en modul dubbla. Delvis uppdatering uttrycks som ett JSON-dokument som läggs till eller uppdaterar en egenskap. Ange egenskaper `null` tas bort. I följande exempel skapas en ny önskad egenskap med värdet `{"newProperty": "newValue"}`, skriver över det befintliga värdet av `existingProperty` med `"otherNewValue"`, och tar bort `otherOldProperty`. Några andra ändringar har gjorts i befintliga egenskaper eller taggar:

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

* **Ersätt egenskaper**. Den här åtgärden aktiverar lösningens serverdel att skriva över alla befintliga egenskaper och ersätta ett nytt JSON-dokument för `properties/desired`.
* **Ersätt taggar**. Den här åtgärden aktiverar lösningens serverdel att skriva över alla befintliga taggar och ersätta ett nytt JSON-dokument för `tags`.
* **Ta emot meddelanden med dubbla**. Den här åtgärden kan lösningens serverdel ska meddelas när dubbla ändras. Om du vill göra det, IoT-lösningen behöver skapa en väg och datakällan ska vara lika med *twinChangeEvents*. Inga dubbla meddelanden skickas som standard, som är det inför finns ingen sådan vägar. Om ändringshastigheten är för hög eller av andra orsaker, till exempel internt fel IoT-hubben kan skicka endast ett meddelande som innehåller alla ändringar. Om ditt program måste tillförlitliga granskning och loggning av alla mellanliggande tillstånd, bör du därför använda meddelanden från enhet till moln. Dubbla meddelandet innehåller egenskaperna och innehållet.

    - Egenskaper

    | Namn | Värde |
    | --- | --- |
    $content-typ | application/json |
    $iothub-enqueuedtime |  Tidpunkt som meddelandet skickades |
    $iothub-meddelande-källa | twinChangeEvents |
    $content-kodning | UTF-8 |
    deviceId | ID för enheten |
    moduleId | ID för modulen |
    hubName | Namnet på IoT-hubb |
    operationTimestamp | [ISO8601] tidsstämpeln för åtgärden |
    iothub-meddelande-schema | deviceLifecycleNotification |
    opType | ”replaceTwin” eller ”updateTwin” |

    Meddelandet Systemegenskaper föregås av `'$'` symbolen.

    - Innehåll
        
    Det här avsnittet innehåller dubbla ändringarna i en JSON-format. Samma format används som en korrigering, med skillnaden att den kan innehålla alla två avsnitt: taggar, properties.reported, properties.desired och att den innehåller ”$metadata”-element. Exempel:

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

Stöd för alla föregående operationer [Optimistisk samtidighet] [ lnk-concurrency] och kräver den **ServiceConnect** behörighet, som definieras i den [säkerhet] [ lnk-security] artikel.

Förutom dessa åtgärder kan lösningens serverdel

* Fråga modul-twins med hjälp av SQL-liknande [IoT-hubb frågespråket][lnk-query].

## <a name="module-operations"></a>Modulåtgärder
Modulen appen körs på den modul dubbla med hjälp av följande atomiska åtgärder:

* **Hämta modul dubbla**. Den här åtgärden returnerar modulen dubbla dokumentet (inklusive taggar och önskade och rapporterade Systemegenskaper) för modulen anslutna.
* **Uppdatera delvis rapporterade egenskaper**. Den här åtgärden aktiverar deluppdatering rapporterade egenskaper på modulen som är anslutna. Den här åtgärden används samma JSON update format-att lösningen tillbaka för en deluppdatering av egenskaper.
* **Se egenskaper**. Modulen anslutna kan välja att aviseras om uppdateringar av egenskaperna när de inträffar. Modulen får samma form av uppdateringen (eller delvis ersättning) som körs av lösningens serverdel.

Alla föregående operationer kräver den **ModuleConnect** behörighet, som definieras i den [säkerhet] [ lnk-security] artikel.

Den [Azure IoT-enhet SDK] [ lnk-sdks] gör det lättare att använda föregående åtgärder från många språk och plattformar.

## <a name="tags-and-properties-format"></a>Taggar och egenskaper format
Taggar, önskade egenskaper och rapporterade egenskaper är JSON-objekt med följande begränsningar:

* Alla nycklar i JSON-objekt är skiftlägeskänsliga 64 byte UTF-8, UNICODE-strängar. Tillåtna tecken undanta Unicode-kontrolltecken (segment C0 och C1) och `'.'`, `' '`, och `'$'`.
* Alla värden i JSON-objekt kan vara följande typer av JSON: boolean, nummer, sträng, objekt. Matriser är inte tillåtna. Det maximala värdet för heltal är 4503599627370495 och det lägsta värdet för heltal är-4503599627370496.
* JSON-objekt i taggar, önskade och rapporterade egenskaper kan ha högst 5. Följande objekt är till exempel giltig:

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

* Alla strängvärden kan vara högst 4 KB längd.

## <a name="module-twin-size"></a>Modulen dubbla storlek
IoT-hubb tillämpar en begränsning på 8KB storleken på varje totala värdena i `tags`, `properties/desired`, och `properties/reported`, exklusive skrivskyddade element.
Storleken beräknas genom att räkna alla tecken, förutom Unicode-kontrolltecken (segment C0 och C1) och blanksteg som befinner sig utanför strängkonstanter.
IoT-hubb avvisar alla åtgärder som kan öka storleken på dessa dokument än gränsen med ett fel.

## <a name="module-twin-metadata"></a>Modulen dubbla metadata
IoT-hubb underhåller tidsstämpel för den senaste uppdateringen för varje JSON-objekt i modulen dubbla önskad och rapporterade egenskaper. Tidsstämplar i UTC och kodats i den [ISO8601] format `YYYY-MM-DDTHH:MM:SS.mmmZ`.
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
```

Denna information lagras på varje nivå (inte bara löv i JSON-strukturen) för att spara uppdateringar som tar bort objektnycklar.

## <a name="optimistic-concurrency"></a>Optimistisk samtidighet
Taggar, önskad och rapporterade egenskaper alla stöd för Optimistisk samtidighet.
Taggar har en ETag enligt [RFC7232], som representerar den tagg JSON-representation. Du kan använda ETags i villkorlig uppdateringsåtgärder från lösningens serverdel för att säkerställa konsekvens.

Modulen dubbla önskad och rapporterade egenskaper har inte ETags, men har en `$version` värde som garanterat inkrementell. På liknande sätt till en ETag kan versionen användas av uppdatering part vill använda konsekvent av uppdateringar. Till exempel en modul app för en rapporterade egenskap eller lösningens serverdel för en önskad egenskap.

Versioner är också användbart när en observing agent (till exempel modulen appen sett egenskaperna) måste stämma lopp mellan resultatet av en hämta och ett uppdateringsmeddelande. I avsnittet [enhet återanslutning flow] [lnk-återanslutning] innehåller mer information. 

## <a name="next-steps"></a>Nästa steg
Om du vill prova några av de begrepp som beskrivs i den här artikeln finns i följande kurser för IoT-hubb:

* [Kom igång med IoT-hubb modulen identitets- och modulen dubbla med hjälp av .NET-säkerhetskopiering och .NET-enhet][lnk-module-twin-tutorial]

<!-- links and images -->

[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-identity]: iot-hub-devguide-identity-registry.md
[lnk-d2c]: iot-hub-devguide-messages-d2c.md
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-security]: iot-hub-devguide-security.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md
[ISO8601]: https://en.wikipedia.org/wiki/ISO_8601
[RFC7232]: https://tools.ietf.org/html/rfc7232

[lnk-module-twin-tutorial]: iot-hub-csharp-csharp-module-twin-getstarted.md
[lnk-module-twin-metadata]: iot-hub-devguide-module-twins.md#module-twin-metadata
[lnk-concurrency]: iot-hub-devguide-device-twins.md#optimistic-concurrency
[img-module-twin]: media/iot-hub-devguide-device-twins/module-twin.jpg
