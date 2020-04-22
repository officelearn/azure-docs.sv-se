---
title: Förstå Azure IoT Hub-identitetsregistret | Microsoft-dokument
description: Utvecklarguide – beskrivning av IoT Hub-identitetsregistret och hur du använder det för att hantera dina enheter. Innehåller information om import och export av enhetsidentiteter i bulk.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/29/2018
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 2ef259bf76815fdf8672b696d2260fe6a143b798
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81730174"
---
# <a name="understand-the-identity-registry-in-your-iot-hub"></a>Förstå identitetsregistret i IoT-hubben

Varje IoT-hubb har ett identitetsregister som lagrar information om de enheter och moduler som tillåts ansluta till IoT-hubben. Innan en enhet eller modul kan ansluta till en IoT-hubb måste det finnas en post för den enheten eller modulen i IoT-hubbens identitetsregister. En enhet eller modul måste också autentisera med IoT-hubben baserat på autentiseringsuppgifter som lagras i identitetsregistret.

Enhets- eller modul-ID som lagras i identitetsregistret är skiftlägeskänsligt.

På en hög nivå är identitetsregistret en REST-kompatibel samling av enhets- eller modulidentitetsresurser. När du lägger till en post i identitetsregistret skapar IoT Hub en uppsättning resurser per enhet, till exempel kön som innehåller moln-till-enhet-meddelanden under flygning.

Använd identitetsregistret när du behöver:

* Etablera enheter eller moduler som ansluter till din IoT-hubb.
* Styr åtkomsten per enhet/per modul till navets enhet eller modulvända slutpunkter.

> [!NOTE]
> * Identitetsregistret innehåller inga programspecifika metadata.
> * Modulidentitet och modultvilling är i offentlig förhandsversion. Nedanstående funktion kommer att stödjas på modulidentitet när den är allmänt tillgänglig.
>

## <a name="identity-registry-operations"></a>Identitetsregisteråtgärder

IoT Hub-identitetsregistret visar följande åtgärder:

* Skapa enhets- eller modulidentitet
* Uppdatera enhets- eller modulidentitet
* Hämta enhets- eller modulidentitet efter ID
* Ta bort enhets- eller modulidentitet
* Lista upp till 1000 identiteter
* Exportera enhetsidentiteter till Azure-bloblagring
* Importera enhetsidentiteter från Azure blob storage

Alla dessa åtgärder kan använda optimistisk samtidighet, enligt vad som anges i [RFC7232](https://tools.ietf.org/html/rfc7232).

> [!IMPORTANT]
> Det enda sättet att hämta alla identiteter i en IoT-hubb identitetsregister är att använda [exportfunktionen.](iot-hub-devguide-identity-registry.md#import-and-export-device-identities)

Ett IoT Hub-identitetsregister:

* Innehåller inga programmetadata.
* Kan nås som en ordlista, med hjälp av **deviceId** eller **moduleId** som nyckel.
* Stöder inte uttrycksfulla frågor.

En IoT-lösning har vanligtvis ett separat lösningsspecifikt arkiv som innehåller programspecifika metadata. Till exempel skulle den lösningsspecifika butiken i en smart bygglösning registrera rummet där en temperatursensor distribueras.

> [!IMPORTANT]
> Använd endast identitetsregistret för enhetshantering och etableringsåtgärder. Avancerade dataflödesåtgärder vid körning bör inte vara beroende av att utföra åtgärder i identitetsregistret. Att till exempel kontrollera en enhets anslutningstillstånd innan ett kommando skickas är inte ett mönster som stöds. Kontrollera [begränsningsfrekvensen](iot-hub-devguide-quotas-throttling.md) för identitetsregistret och [enhetspulsmönstret.](iot-hub-devguide-identity-registry.md#device-heartbeat)

## <a name="disable-devices"></a>Inaktivera enheter

Du kan inaktivera enheter **status** genom att uppdatera statusegenskapen för en identitet i identitetsregistret. Vanligtvis använder du den här egenskapen i två scenarier:

* Under en etableringsorkestreringsprocess. Mer information finns i [Enhetsetablering](iot-hub-devguide-identity-registry.md#device-provisioning).

* Om du av någon anledning tror att en enhet har komprometterats eller har blivit obehörig.

Den här funktionen är inte tillgänglig för moduler.

## <a name="import-and-export-device-identities"></a>Importera och exportera enhetsidentiteter

Använd asynkrona åtgärder på [slutpunkten för IoT Hub-resursprovidern](iot-hub-devguide-endpoints.md) för att exportera enhetsidentiteter i bulk från en IoT-hubbs identitetsregister. Export är tidskrävande jobb som använder en behållen blob-behållare som levereras för att spara enhetsidentitetsdata som lästs från identitetsregistret.

Använd asynkrona åtgärder på [slutpunkten för IoT Hub-resursprovidern](iot-hub-devguide-endpoints.md) för att importera enhetsidentiteter i grupp till ett IoT-navs identitetsregister. Import är tidskrävande jobb som använder data i en kundadumpbehållare för att skriva enhetsidentitetsdata till identitetsregistret.

Mer information om import- och export-API:erna finns i [IoT Hub-resursprovidern REST API:er](/rest/api/iothub/iothubresource). Mer information om hur du kör import- och exportjobb finns i [Masshantering av IoT Hub-enhetsidentiteter](iot-hub-bulk-identity-mgmt.md).

Enhetsidentiteter kan också exporteras och importeras från en IoT Hub via service-API:et via [REST API](/rest/api/iothub/service/jobclient/createimportexportjob) eller en av IoT [Hub-tjänst-SDK:erna](/azure/iot-hub/iot-hub-devguide-sdks#azure-iot-hub-service-sdks).

## <a name="device-provisioning"></a>Etablering av enhet

Enhetsdata som en viss IoT-lösning lagrar beror på de specifika kraven för den lösningen. Men som ett minimum måste en lösning lagra enhetsidentiteter och autentiseringsnycklar. Azure IoT Hub innehåller ett identitetsregister som kan lagra värden för varje enhet som ID, autentiseringsnycklar och statuskoder. En lösning kan använda andra Azure-tjänster som tabelllagring, blob-lagring eller Cosmos DB för att lagra ytterligare enhetsdata.

*Enhetsetablering* är processen att lägga till de första enhetsdata till butikerna i din lösning. Om du vill aktivera en ny enhet för att ansluta till navet måste du lägga till ett enhets-ID och nycklar i IoT Hub-identitetsregistret. Som en del av etableringsprocessen kan du behöva initiera enhetsspecifika data i andra lösningsarkiv. Du kan också använda Azure IoT Hub Device Provisioning Service för att aktivera zero-touch, just-in-time-etablering till en eller flera IoT-hubbar utan att kräva mänsklig inblandning. Mer information finns i dokumentationen till [etableringstjänsten](https://azure.microsoft.com/documentation/services/iot-dps).

## <a name="device-heartbeat"></a>Enhetsslagslag

IoT Hub-identitetsregistret innehåller ett fält som heter **connectionState**. Använd endast **fältet connectionState** under utveckling och felsökning. IoT-lösningar bör inte fråga fältet vid körning. Fråga till exempel inte **fältet connectionState** för att kontrollera om en enhet är ansluten innan du skickar ett meddelande från molnet till enheten eller ett SMS. Vi rekommenderar att du prenumererar på den [ **frånkopplade** händelsen](iot-hub-event-grid.md#event-types) från enheten i Händelseutnätet för att få aviseringar och övervaka enhetens anslutningstillstånd. Använd den här [självstudien](iot-hub-how-to-order-connection-state-events.md) om du vill lära dig hur du integrerar händelser som är anslutna till enheten och frånkopplad enhet från IoT-hubben i IoT-lösningen.

Om IoT-lösningen behöver veta om en enhet är ansluten kan du implementera *pulsslagsmönstret*.
I pulsslagsmönstret skickar enheten meddelanden från enheten till molnet minst en gång varje fast tid (till exempel minst en gång i timmen). Även om en enhet inte har några data att skicka skickas ett tomt meddelande från enhet till moln (vanligtvis med en egenskap som identifierar den som ett pulsslag). På tjänstsidan upprätthåller lösningen en karta med de sista pulsslag som tas emot för varje enhet. Om lösningen inte får ett pulsslagsmeddelande inom den förväntade tiden från enheten förutsätts det att det finns ett problem med enheten.

En mer komplex implementering kan innehålla information från [Azure Monitor](../azure-monitor/index.yml) och Azure [Resource Health](../service-health/resource-health-overview.md) för att identifiera enheter som försöker ansluta eller kommunicera men [misslyckas,](iot-hub-monitor-resource-health.md) kontrollera Monitor med diagnostikguide. När du implementerar pulsslagsmönstret kontrollerar du [att IoT Hub-kvoter och begränsningar](iot-hub-devguide-quotas-throttling.md)ska kontrolleras .

> [!NOTE]
> Om en IoT-lösning använder anslutningstillståndet enbart för att avgöra om du vill skicka meddelanden från molnet till enheten och meddelanden inte sänds till stora enheter bör du överväga att använda det enklare *kortutgångstidsmönstret.* Det här mönstret uppnår samma resultat som att upprätthålla ett enhetsanslutningstillståndsregister med hjälp av pulsslagsmönstret, samtidigt som det blir effektivare. Om du begär meddelandebekräftelser kan IoT Hub meddela dig om vilka enheter som kan ta emot meddelanden och vilka som inte är det.

## <a name="device-and-module-lifecycle-notifications"></a>Livscykelmeddelanden för enheter och moduler

IoT Hub kan meddela din IoT-lösning när en identitet skapas eller tas bort genom att skicka livscykelmeddelanden. För att göra det måste IoT-lösningen skapa en väg och ange datakällan som är lika med *DeviceLifecycleEvents* eller *ModuleLifecycleEvents*. Som standard skickas inga livscykelmeddelanden, det vill än inga sådana vägar som finns i förfår. Meddelandemeddelandet innehåller egenskaper och brödtext.

Egenskaper: Egenskaper för meddelandesystem föregås av symbolen. `$`

Meddelandemeddelande för enhet:

| Namn | Värde |
| --- | --- |
|$content typ | application/json |
|$iothub-enqueuedtime |  Tid när meddelandet skickades |
|$iothub-meddelande-källa | deviceLifecycleEvents |
|$content-kodning | utf-8 |
|opType (olika) | createDeviceIdentity or **deleteDeviceIdentity** **createDeviceIdentity createDeviceIdentity createDeviceIdentity createDeviceIdentity createDevice** |
|hubName (hubName) | Namn på IoT Hub |
|deviceId | ID för enheten |
|operationTimestamp | ISO8601 tidsstämpel för drift |
|iothub-meddelande-schema | deviceLifecycleNotification |

Brödtext: Det här avsnittet är i JSON-format och representerar tvillingen till den skapade enhetsidentiteten. Exempel:

```json
{
    "deviceId":"11576-ailn-test-0-67333793211",
    "etag":"AAAAAAAAAAE=",
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
Meddelandemeddelande för modul:

| Namn | Värde |
| --- | --- |
$content typ | application/json |
$iothub-enqueuedtime |  Tid när meddelandet skickades |
$iothub-meddelande-källa | modulLivscykelHändelser |
$content-kodning | utf-8 |
opType (olika) | **createModuleIdentity** eller **deleteModuleIdentity** |
hubName (hubName) | Namn på IoT Hub |
moduleId (modulId) | ID för modulen |
operationTimestamp | ISO8601 tidsstämpel för drift |
iothub-meddelande-schema | modulLivscykelAnmäla |

Brödtext: Det här avsnittet är i JSON-format och representerar tvillingen till den skapade modulidentiteten. Exempel:

```json
{
    "deviceId":"11576-ailn-test-0-67333793211",
    "moduleId":"tempSensor",
    "etag":"AAAAAAAAAAE=",
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

## <a name="device-identity-properties"></a>Egenskaper för enhetsidentitet

Enhetsidentiteter representeras som JSON-dokument med följande egenskaper:

| Egenskap | Alternativ | Beskrivning |
| --- | --- | --- |
| deviceId |nödvändiga, skrivskyddade på uppdateringar |En skiftlägeskänslig sträng (upp till 128 tecken lång) med ASCII 7-bitars alfanumeriska tecken plus vissa specialtecken: `- . + % _ # * ? ! ( ) , = @ $ '`. |
| generationId |krävs, skrivskyddat |En IoT-hubbgenererad, skiftlägeskänslig sträng upp till 128 tecken lång. Det här värdet används för att särskilja enheter med samma **deviceId**, när de har tagits bort och återskapats. |
| Etag |krävs, skrivskyddat |En sträng som representerar en svag ETag för enhetens identitet, enligt [RFC7232](https://tools.ietf.org/html/rfc7232). |
| Auth |valfri |Ett sammansatt objekt som innehåller autentiseringsinformation och säkerhetsmaterial. |
| auth.symkey (auth.symkey) |valfri |Ett sammansatt objekt som innehåller en primär och en sekundär nyckel som lagras i base64-format. |
| status |krävs |En åtkomstindikator. Kan **aktiveras** eller **inaktiveras**. Om **aktiverad**tillåts enheten att ansluta. Om den här enheten **är inaktiverad**kan den inte komma åt någon enhetsvänd slutpunkt. |
| statusReason |valfri |En 128 teckenlång sträng som lagrar orsaken till enhetens identitetsstatus. Alla UTF-8 tecken är tillåtna. |
| statusUpdateTime |Skrivskyddad |En temporal indikator som visar datum och tid för den senaste statusuppdateringen. |
| connectionState (anslutningsstat) |Skrivskyddad |Ett fält som anger anslutningsstatus: antingen **Ansluten** eller **frånkopplad**. Det här fältet representerar IoT Hub-vyn för enhetens anslutningsstatus. **Viktigt**: Det här fältet ska endast användas för utvecklings-/felsökningsändamål. Anslutningstillståndet uppdateras endast för enheter som använder MQTT eller AMQP. Dessutom är det baserat på ping på protokollnivå (MQTT-pingar eller AMQP-pingar), och det kan ha en maximal fördröjning på bara 5 minuter. Av dessa skäl kan det finnas falska positiva identifieringar, till exempel enheter som rapporteras som anslutna men som är frånkopplade. |
| connectionStateUpdatedTime |Skrivskyddad |En temporal indikator som visar datum och senaste gången anslutningstillståndet uppdaterades. |
| lastActivityTime |Skrivskyddad |En temporal indikator som visar datum och sista gången enheten anslöt, tog emot eller skickade ett meddelande. |

> [!NOTE]
> Anslutningstillståndet kan bara representera IoT Hub-vyn för anslutningens status. Uppdateringar av det här tillståndet kan fördröjas, beroende på nätverksförhållanden och konfigurationer.

> [!NOTE]
> För närvarande stöder enhetens SDK:er inte att använda `+` tecknen och `#` i **deviceId**.

## <a name="module-identity-properties"></a>Egenskaper för modulidentitet

Modulidentiteter representeras som JSON-dokument med följande egenskaper:

| Egenskap | Alternativ | Beskrivning |
| --- | --- | --- |
| deviceId |nödvändiga, skrivskyddade på uppdateringar |En skiftlägeskänslig sträng (upp till 128 tecken lång) med ASCII 7-bitars alfanumeriska tecken plus vissa specialtecken: `- . + % _ # * ? ! ( ) , = @ $ '`. |
| moduleId (modulId) |nödvändiga, skrivskyddade på uppdateringar |En skiftlägeskänslig sträng (upp till 128 tecken lång) med ASCII 7-bitars alfanumeriska tecken plus vissa specialtecken: `- . + % _ # * ? ! ( ) , = @ $ '`. |
| generationId |krävs, skrivskyddat |En IoT-hubbgenererad, skiftlägeskänslig sträng upp till 128 tecken lång. Det här värdet används för att särskilja enheter med samma **deviceId**, när de har tagits bort och återskapats. |
| Etag |krävs, skrivskyddat |En sträng som representerar en svag ETag för enhetens identitet, enligt [RFC7232](https://tools.ietf.org/html/rfc7232). |
| Auth |valfri |Ett sammansatt objekt som innehåller autentiseringsinformation och säkerhetsmaterial. |
| auth.symkey (auth.symkey) |valfri |Ett sammansatt objekt som innehåller en primär och en sekundär nyckel som lagras i base64-format. |
| status |krävs |En åtkomstindikator. Kan **aktiveras** eller **inaktiveras**. Om **aktiverad**tillåts enheten att ansluta. Om den här enheten **är inaktiverad**kan den inte komma åt någon enhetsvänd slutpunkt. |
| statusReason |valfri |En 128 teckenlång sträng som lagrar orsaken till enhetens identitetsstatus. Alla UTF-8 tecken är tillåtna. |
| statusUpdateTime |Skrivskyddad |En temporal indikator som visar datum och tid för den senaste statusuppdateringen. |
| connectionState (anslutningsstat) |Skrivskyddad |Ett fält som anger anslutningsstatus: antingen **Ansluten** eller **frånkopplad**. Det här fältet representerar IoT Hub-vyn för enhetens anslutningsstatus. **Viktigt**: Det här fältet ska endast användas för utvecklings-/felsökningsändamål. Anslutningstillståndet uppdateras endast för enheter som använder MQTT eller AMQP. Dessutom är det baserat på ping på protokollnivå (MQTT-pingar eller AMQP-pingar), och det kan ha en maximal fördröjning på bara 5 minuter. Av dessa skäl kan det finnas falska positiva identifieringar, till exempel enheter som rapporteras som anslutna men som är frånkopplade. |
| connectionStateUpdatedTime |Skrivskyddad |En temporal indikator som visar datum och senaste gången anslutningstillståndet uppdaterades. |
| lastActivityTime |Skrivskyddad |En temporal indikator som visar datum och sista gången enheten anslöt, tog emot eller skickade ett meddelande. |

> [!NOTE]
> För närvarande stöder enhetenSDK:er inte att använda `+` och `#` tecknen i **deviceId** och **moduleId**.

## <a name="additional-reference-material"></a>Ytterligare referensmaterial

Andra referensavsnitt i utvecklarhandboken för IoT Hub är:

* [IoT Hub-slutpunkter](iot-hub-devguide-endpoints.md) beskriver de olika slutpunkter som varje IoT-hubb exponerar för körnings- och hanteringsåtgärder.

* [Begränsning och kvoter](iot-hub-devguide-quotas-throttling.md) beskriver de kvoter och begränsningsbeteenden som gäller för IoT Hub-tjänsten.

* [Azure IoT-enhet och tjänst SDK visar](iot-hub-devguide-sdks.md) de olika språk SDK:er som du kan använda när du utvecklar både enhets- och tjänstappar som interagerar med IoT Hub.

* [IoT Hub-frågespråket](iot-hub-devguide-query-language.md) beskriver det frågespråk som du kan använda för att hämta information från IoT Hub om dina enhetstvillingar och jobb.

* [IoT Hub MQTT-stöd](iot-hub-mqtt-support.md) ger mer information om IoT Hub-stöd för MQTT-protokollet.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du använder IoT Hub-identitetsregistret kan du vara intresserad av följande IoT Hub-utvecklarguide:

* [Styra åtkomst till IoT Hub](iot-hub-devguide-security.md)

* [Använda enhetstvillingar för att synkronisera tillstånd och konfigurationer](iot-hub-devguide-device-twins.md)

* [Anropa en direkt metod på en enhet](iot-hub-devguide-direct-methods.md)

* [Schemalägga jobb på flera enheter](iot-hub-devguide-jobs.md)

Information om hur du provar några av de begrepp som beskrivs i den här artikeln finns i följande IoT Hub-självstudiekurs:

* [Komma igång med Azure IoT Hub](quickstart-send-telemetry-dotnet.md)

Information om hur du använder etableringstjänsten för IoT Hub Device För att aktivera nollpunkts-etablering, just-in-time-etablering finns i: 

* [Azure IoT Hub Device Provisioning-tjänst](https://azure.microsoft.com/documentation/services/iot-dps)
