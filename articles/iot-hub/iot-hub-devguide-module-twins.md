---
title: Förstå modultvillingar för Azure IoT Hub | Microsoft Docs
description: Utvecklarguide – Använd modultvillingar att synkronisera tillstånd och konfiguration data mellan IoT Hub och dina enheter
author: chrissie926
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: menchi
ms.openlocfilehash: 0df1170079e66bda95e38bcf17dcce738269eeb0
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47039391"
---
# <a name="understand-and-use-module-twins-in-iot-hub"></a>Förstå och använda modultvillingar i IoT Hub

Den här artikeln förutsätter att du har läst [förstå och använda enhetstvillingar i IoT Hub] [ lnk-devguide-device-twins] första. Under varje enhetsidentitet i IoT Hub kan skapa upp till 20 modulen identiteter. Varje modul identitet genererar implicit en modultvilling. Mycket lik enhetstvillingar, modultvillingar är JSON-dokument som lagrar tillstånd modulinformation inklusive metadata, konfigurationer och villkor. Azure IoT Hub underhåller en modultvillingen för varje modul som du ansluter till IoT Hub. 

SDK: er för IoT Hub-enheter på enheten kan du skapa moduler där varje öppnar en oberoende anslutning till IoT Hub. På så sätt kan du använda separata namnområden för olika komponenter på din enhet. Exempelvis kan ha du en Varuautomat som har tre olika sensorer. Varje sensor styrs av olika avdelningar i ditt företag. Du kan skapa en modul för varje sensor. På så sätt kan varje avdelning kan bara skicka jobb eller direkta metoder till sensorn som de styr undvika konflikter och användarfel.

 Modultvilling för identitets- och modulen innehåller samma funktioner som enhetsidentitet och en enhetstvilling men med en bättre precision. Den här finare granularitet gör kompatibla enheter som operativsystemet baserat enheter eller inbyggd programvara hantering av flera komponenter, om du vill isolera konfiguration och villkor för var och en av dessa komponenter. Modulen identitets- och modultvillingar ger en management-inkapsling av problem när du arbetar med IoT-enheter som har modulära programvarukomponenter. Vi sträva efter att ge support för alla enheter twin funktioner på modulen twin nivå med modulen twin allmänt tillgängliga. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Den här artikeln beskrivs:

* Strukturen för modultvillingen: *taggar*, *önskade* och *rapporterade egenskaper*.
* De åtgärder som moduler och serverdelar kan utföra på modultvillingar.

Referera till [enhet till molnet kommunikation vägledning] [ lnk-d2c-guidance] anvisningar om hur du använder rapporterade egenskaper, meddelanden från enheten till molnet eller ladda upp filen.
Referera till [moln till enhet kommunikation vägledning] [ lnk-c2d-guidance] för hjälp med att använda önskade egenskaper, direkta metoder eller meddelanden från molnet till enheten.

## <a name="module-twins"></a>Modultvillingar
Modultvillingar lagra modul-relaterad information som:

* Moduler på enheten och IoT Hub kan använda för att synkronisera modulen villkor och konfiguration.
* Lösningens serverdel kan använda för att fråga och mål långvariga åtgärder.

Livscykeln för en modultvilling är länkad till motsvarande [modulen identitet][lnk-identity]. Moduler twins skapas implicit och tas bort när en modul identitet skapas eller tas bort i IoT Hub.

En modultvilling är ett JSON-dokument som innehåller:

* **Taggar**. En del av JSON-dokument som lösningens serverdel kan läsa från och skriva till. Taggar visas inte för moduler på enheten. Taggar är inställda för att fråga syfte.
* **Önskade egenskaper**. Används tillsammans med rapporterade egenskaper för att synkronisera modulkonfigurationen eller villkor. Modulen appen kan läsa dem lösningens serverdel kan ange önskade egenskaper. Modulen appen kan också ta emot meddelanden om ändringar i de önskade egenskaperna.
* **Rapporterade egenskaper**. Används tillsammans med önskade egenskaper för att synkronisera modulkonfigurationen eller villkor. Modulen appen kan ange rapporterade egenskaper och lösningens serverdel kan läsa och skicka frågor mot dem.
* **Modulen identitetsegenskaper**. Roten av modulen twin JSON-dokumentet innehåller skrivskyddade egenskaper från den motsvarande modul identiteten som lagras i den [identitetsregistret][lnk-identity].

![][img-module-twin]

I följande exempel visas en modultvilling JSON-dokument:

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

I rotobjektet är modulen identitetsegenskaper och behållarobjekt för `tags` och både `reported` och `desired` egenskaper. Den `properties` behållaren innehåller vissa skrivskyddade element (`$metadata`, `$etag`, och `$version`) som beskrivs i den [modulen twin metadata] [ lnk-module-twin-metadata] och [ Optimistisk samtidighet] [ lnk-concurrency] avsnitt.

### <a name="reported-property-example"></a>Rapporterad egenskap-exempel
I exemplet ovan modultvillingen innehåller en `batteryLevel` egenskap som rapporteras av appen modulen. Den här egenskapen gör det möjligt att fråga och arbeta med moduler baserat på senaste rapporterade batterinivå. Andra exempel är modulen app modulen rapporteringsfunktioner eller anslutningsalternativ.

> [!NOTE]
> Rapporterade egenskaper förenkla scenarier där lösningens backend-server är intresserad av det senaste kända värdet för en egenskap. Använd [meddelanden från enheten till molnet] [ lnk-d2c] om lösningens backend-server som behöver bearbeta modulen telemetri i form av tidsstämplad händelser, till exempel tidsserier.

### <a name="desired-property-example"></a>Exempel på önskad egenskap
I exemplet ovan den `telemetryConfig` modultvilling önskad och rapporterade egenskaper som används av lösningens backend-server och den modul app för att synkronisera konfigurationen av telemetri för den här modulen. Exempel:

1. Lösningens serverdel anger önskad egenskap med det önskade värdet. Här är del av dokumentet med önskad egenskap:

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

2. Modulen appen meddelas för ändringen direkt om du är ansluten eller i den första reconnect. Modulen appen rapporterar den uppdaterade konfigurationen (eller ett fel villkor med hjälp av den `status` egenskapen). Här är del av rapporterade egenskaper:

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

3. Lösningens serverdel kan spåra resultatet av konfigurationsåtgärden mellan flera moduler av [fråga] [ lnk-query] modultvillingar.

> [!NOTE]
> Föregående kodfragment är exempel som optimerats för läsbarhet på ett sätt att koda ett Modulkonfiguration och dess status. IoT Hub medför inte ett visst schema för modultvillingen önskad och rapporterade egenskaper i modultvillingar.
> 
> 

## <a name="back-end-operations"></a>Backend-åtgärder
Lösningens backend-server körs på modultvillingen med hjälp av följande atomiska operationer exponeras via HTTPS:

* **Hämta modultvilling efter ID**. Den här åtgärden returnerar modulen twin dokument, inklusive taggar och önskade och rapporterade egenskaper.
* **Delvis uppdatera modultvilling**. Den här åtgärden gör att lösningens backend-server att uppdatera taggar och önskade egenskaper i en modultvilling delvis. Deluppdatering uttrycks som ett JSON-dokument som läggs till eller uppdaterar en egenskap. Egenskaper som har angetts till `null` tas bort. I följande exempel skapas en ny önskad egenskap med värdet `{"newProperty": "newValue"}`, skriver över det befintliga värdet för `existingProperty` med `"otherNewValue"`, och tar bort `otherOldProperty`. Inga andra ändringar har gjorts i befintliga önskade egenskaper eller taggar:

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
* **Ta emot meddelanden twin**. Den här åtgärden gör att lösningens serverdel kan meddelas när läsningen ändras. Gör din IoT-lösning behöver du skapar en väg och datakällan ska vara lika med *twinChangeEvents*. Som standard inga dubbla meddelanden skickas, dvs, inga sådana vägar redan finnas. Om ändringsfrekvensen är för hög eller av andra orsaker, till exempel interna fel IoT-hubben kan skicka endast ett meddelande som innehåller alla ändringar. Om ditt program behöver tillförlitlig granskning och loggning av alla mellanliggande tillstånd, bör du därför använda meddelanden från enheten till molnet. Meddelandet twin innehåller egenskaperna och brödtext.

    - Egenskaper

    | Namn | Värde |
    | --- | --- |
    $content-typ | application/json |
    $iothub-enqueuedtime |  Tid när meddelandet skickades |
    $iothub-meddelande-källa | twinChangeEvents |
    $content-kodning | UTF-8 |
    deviceId | ID för enheten |
    moduleId | ID för modulen |
    HubName | Namnet på IoT Hub |
    operationTimestamp | [ISO8601] tidsstämpeln för åtgärden |
    iothub-meddelande-schema | deviceLifecycleNotification |
    opType | ”replaceTwin” eller ”updateTwin” |

    Meddelandet Systemegenskaper föregås den `'$'` symbolen.

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

Stöd för alla åtgärder för föregående [Optimistisk samtidighet] [ lnk-concurrency] och kräver den **ServiceConnect** behörighet, som definieras i den [Security] [ lnk-security] artikeln.

Utöver dessa åtgärder kan kan lösningens serverdel:

* Fråga modultvillingar med hjälp av SQL-liknande [IoT Hub-frågespråk][lnk-query].

## <a name="module-operations"></a>Modulåtgärder
Modulen appen körs på modultvillingen med hjälp av följande atomiska åtgärder:

* **Hämta modultvilling**. Den här åtgärden returnerar modulen twin dokumentet (inklusive taggar och önskade och rapporterade Systemegenskaper) för modulen just nu anslutna.
* **Delvis uppdatera rapporterade egenskaper**. Den här åtgärden aktiverar deluppdatering av rapporterade egenskaper från modulen just nu anslutna. Den här åtgärden använder samma JSON update-format dessa lösningen igen för en deluppdatering av önskade egenskaper.
* **Notera önskade egenskaper**. Just nu anslutna modulen kan du meddelas om uppdateringar de önskade egenskaperna när de inträffar. Modulen får samma formulär för uppdatering (partiell eller fullständig ersättning) som körs av lösningens serverdel.

Alla föregående åtgärder kräver den **ModuleConnect** behörighet, som definieras i den [Security] [ lnk-security] artikeln.

Den [SDK: er för Azure IoT-enheter] [ lnk-sdks] gör det enkelt att använda föregående åtgärder från många språk och plattformar.

## <a name="tags-and-properties-format"></a>Format för taggar och egenskaper
Taggar och önskade egenskaper rapporterade egenskaper är JSON-objekt med följande begränsningar:

* Alla nycklar i JSON-objekt är skiftlägeskänsliga 64 byte UTF-8 UNICODE-strängar. Tillåtna tecken undanta Unicode-kontrolltecken (segment C0 och C1), och `'.'`, `' '`, och `'$'`.
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

## <a name="module-twin-size"></a>Modulen twin storlek
IoT Hub tillämpar en begränsning på 8KB storlek på var och en av de totalt värdena i `tags`, `properties/desired`, och `properties/reported`, exklusive skrivskyddade element.
Storleken beräknas genom att räkna alla tecken, förutom Unicode-kontrolltecken (segment C0 och C1) och blanksteg som är utanför strängkonstanter.
IoT Hub avvisar alla åtgärder som kan öka storleken på dessa dokument över gränsen med ett fel.

## <a name="module-twin-metadata"></a>Modulen twin metadata
IoT Hub underhåller tidsstämpel för senaste uppdateringen för varje JSON-objekt i modultvilling önskad och rapporterade egenskaper. Tidsstämplar i UTC och kodas i den [ISO8601] format `YYYY-MM-DDTHH:MM:SS.mmmZ`.
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

Den här informationen sparas på alla nivåer (inte bara löv av JSON-strukturen) för att spara uppdateringar som tar bort objektnycklar.

## <a name="optimistic-concurrency"></a>Optimistisk samtidighet
Taggar och önskade och rapporterade egenskaper alla stöd för Optimistisk samtidighet.
Taggar har en ETag enligt [RFC7232], som representerar den taggen JSON-representation. Du kan använda ETags i villkorlig uppdateringsåtgärder från lösningens backend-server för att garantera konsekvens.

Modultvilling önskad och rapporterade egenskaper har inte ETags, men har en `$version` värdet som garanterat är en inkrementell. På samma sätt till en ETag kan versionen användas av den uppdatera parten för att upprätthålla konsekvens av uppdateringar. Till exempel en modul-app för en rapporterad egenskap eller lösningens backend-server för en önskad egenskap.

Versioner är också användbart när en observing agent (till exempel modulen appen får önskade egenskaper) måste stämma av förenkling mellan resultatet av en hämta-åtgärden och ett meddelande. I avsnittet [enheten återanslutning flöde] [lnk-återanslutning] innehåller mer information. 

## <a name="next-steps"></a>Nästa steg
I följande självstudier får IoT Hub för att prova några av de koncept som beskrivs i den här artikeln:

* [Kom igång med IoT Hub identitets- och modulen modultvilling med hjälp av .NET-serverdel och .NET-enhet][lnk-module-twin-tutorial]

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
