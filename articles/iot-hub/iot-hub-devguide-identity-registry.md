---
title: Förstå Azure IoT Hub-identitetsregistret | Microsoft Docs
description: Utvecklarguide – beskrivning av IoT Hub-identitetsregistret och hur du använder den för att hantera dina enheter. Innehåller information om import och export av enhetsidentiteter gruppvis.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/29/2018
ms.openlocfilehash: 935635c474190413545d1a2731c367a691bfa56d
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2019
ms.locfileid: "57010268"
---
# <a name="understand-the-identity-registry-in-your-iot-hub"></a>Förstå identitetsregistret i IoT hub

Varje IoT-hubb har ett identitetsregister som lagrar information om vilka enheter och moduler som tillåts att ansluta till IoT hub. Innan en enhet eller en modul kan ansluta till en IoT-hubb, måste det finnas en post för enheten, eller modulen i IoT-hubbens identitetsregister. En enhet eller modulen måste också autentisera med IoT hub utifrån autentiseringsuppgifter som lagras i identitetsregistret.

Enhet eller modul-ID lagras i identitetsregistret är skiftlägeskänsliga.

På en hög nivå är en REST-kompatibla samling enhet eller modulen identitet resurser i identitetsregistret. När du lägger till en post i identitetsregistret skapar en uppsättning resurser för varje enhet, till exempel den kö som innehåller relä meddelanden från molnet till enheten i IoT Hub.

Använd identitetsregistret när du behöver:

* Etablera enheter eller modulerna som ansluter till din IoT-hubb.
* Kontrollera per enhet/per-zakazuje åtkomsten till din hubb enhet eller modulen-slutpunkter.

> [!NOTE]
> * ID-registret innehåller inte några programspecifika metadata.
> * Modultvilling för identitets- och modulen finns i offentlig förhandsversion. Nedan funktionen kommer att stödjas på modulen identitet när den är allmänt tillgängliga.
>

## <a name="identity-registry-operations"></a>Identitetsregisteråtgärder

IoT Hub-identitetsregistret exponerar följande åtgärder:

* Skapa enhet eller modulen identitet
* Uppdatera enheten eller modulen identitet
* Hämta enhet eller modulen identitet med hjälp av ID
* Ta bort enheten eller modulen identitet
* Lista över upp till 1 000 identiteter
* Exportera enhetsidentiteter till Azure blob storage
* Importera enhetsidentiteter från Azure blob storage

De här åtgärderna kan använda Optimistisk samtidighet som anges i [RFC7232](https://tools.ietf.org/html/rfc7232).

> [!IMPORTANT]
> Det enda sättet att hämta alla identiteter i en IoT-hubbens identitetsregister är att använda den [exportera](iot-hub-devguide-identity-registry.md#import-and-export-device-identities) funktioner.

En IoT Hub-identitetsregistret:

* Innehåller inte alla metadata som programmet.
* Kan användas som en ordlista med hjälp av den **deviceId** eller **moduleId** som nyckel.
* Stöder inte lättfattliga frågor.

En IoT-lösning har vanligtvis ett annat Lösningsspecifika lager som innehåller programspecifika metadata. Arkivet Lösningsspecifika i en smart byggnad lösning skulle till exempel registrera rummet där en temperatursensor distribueras.

> [!IMPORTANT]
> Använd endast identitetsregistret för hantering av enheter och etablera åtgärder. Högt dataflöde åtgärder vid körning bör inte bero på att utföra åtgärder i identitetsregistret. Kontrollera anslutningsstatus för en enhet innan du skickar ett kommando är exempelvis inte ett mönster som stöds. Se till att kontrollera den [begränsning priserna](iot-hub-devguide-quotas-throttling.md) för identity-registry och [enheten pulsslag](iot-hub-devguide-identity-registry.md#device-heartbeat) mönster.

## <a name="disable-devices"></a>Inaktivera enheter

Du kan inaktivera enheter genom att uppdatera den **status** egenskapen för en identitet i identitetsregistret. Normalt använder du den här egenskapen i två situationer:

* Under en etableringsprocessen för orkestrering. Mer information finns i [Device Provisioning](iot-hub-devguide-identity-registry.md#device-provisioning).

* Om du av någon anledning tror du en enhet är skadad eller har blivit obehörig.

Den här funktionen är inte tillgänglig för moduler.

## <a name="import-and-export-device-identities"></a>Importera och exportera enhetsidentiteter

Använd asynkrona åtgärder på den [leverantörsslutpunkt för IoT Hub-resurs](iot-hub-devguide-endpoints.md) att exportera enhetsidentiteter gruppvis från en IoT-hubbens identitetsregister. Export finns långvariga jobb som använder en kundens blob-behållare för att spara enhetens identitetsdata läses från identitetsregistret.

Använd asynkrona åtgärder på den [leverantörsslutpunkt för IoT Hub-resurs](iot-hub-devguide-endpoints.md) importera enhetsidentiteter gruppvis till en IoT-hubbens identitetsregister. Import är långvariga jobb som använder data i en blobbehållare som kunden anger att enheten identitetsdata i identitetsregistret.

Mer information om import och export API: er finns i [resursprovidern i IoT Hub REST API: er](/rest/api/iothub/iothubresource). Om du vill lära dig mer om hur du kör import och exportera jobb, se [Bulk hantering av IoT Hub-enhetsidentiteter](iot-hub-bulk-identity-mgmt.md).

## <a name="device-provisioning"></a>Enhetsetablering

Enhetsdata som lagras i en viss IoT-lösning är beroende av de specifika kraven för lösningen. Men minst en lösning lagrar enhtesidentiteter och autentiseringsnycklar. Azure IoT Hub tillhandahåller även ett identitetsregister som kan lagra värdena för varje enhet, till exempel-ID: N och autentiseringsnycklar statuskoder. En lösning kan använda andra Azure-tjänster, till exempel tabellagring, bloblagring och Cosmos DB för att lagra alla ytterligare enhetsdata.

*Enhetsetablering* är processen att lägga till inledande enhetens data till datalager i din lösning. Om du vill aktivera en ny enhet att ansluta till din hubb, måste du lägga till ett enhets-ID och nycklar IoT Hub-identitetsregistret. Som en del av etableringen, kan du behöva initiera enhetsspecifika data i andra lösningen butiker. Du kan också använda Azure IoT Hub Device Provisioning-tjänsten för att aktivera zero-touch, just-in-time-etablering till en eller flera IoT-hubbar utan mänsklig inblandning. Mer information finns i den [provisioning service-dokumentationen](https://azure.microsoft.com/documentation/services/iot-dps).

## <a name="device-heartbeat"></a>Enheten pulsslag

IoT Hub-identitetsregistret innehåller ett fält med namnet **connectionState**. Använd bara den **connectionState** fältet under utveckling och felsökning. IoT-lösningar bör inte fråga fältet vid körning. Till exempel fråga inte den **connectionState** fält för att kontrollera om en enhet är ansluten innan du skickar ett meddelande för moln-till-enhet eller ett SMS. Vi rekommenderar att prenumerera på den [ **enheten är frånkopplad** händelse](iot-hub-event-grid.md#event-types) på Event Grid för att få aviseringar och övervaka enhetens anslutning tillstånd. Använd det här [självstudien](iot-hub-how-to-order-connection-state-events.md) att lära dig hur du integrerar enheten ansluten och enheten frånkopplad händelser från IoT Hub i din IoT-lösning.

Om din IoT-lösning behöver veta om en enhet är ansluten kan du implementera den *pulsslag mönstret*.
I mönstret pulsslag skickar enheten meddelanden från enheten till molnet minst en gång var fast mängd tid (t.ex, minst en gång i timmen). Därför även om en enhet inte har några data att skicka skickar fortfarande den ett tomt enhet-till-moln-meddelande (vanligtvis med en egenskap som identifierar den som ett pulsslag). Lösningen innehåller en karta på serversidan, med den senaste pulsslag har tagits emot för varje enhet. Om lösningen inte får ett heartbeat-meddelande inom den förväntade tiden från enheten, förutsätter att det finns ett problem med enheten.

En mer komplex implementering kan innehålla information från [Azure Monitor](../azure-monitor/index.yml) och [Azure Resource Health](../service-health/resource-health-overview.md) för att identifiera enheter som försöker ansluta eller kommunicera men misslyckas, kontrollera [Övervaka med diagnostik](iot-hub-monitor-resource-health.md) guide. När du implementerar mönstret pulsslag, se till att kontrollera [IoT Hub-kvoter och begränsningar](iot-hub-devguide-quotas-throttling.md).

> [!NOTE]
> Om en IoT-lösning använder anslutningsstatus enbart för att avgöra om du vill skicka meddelanden från moln till enhet och meddelanden inte skickas ut till stora mängder enheter, bör du använda den enklare *kort förfallotid* mönster. Det här mönstret ger samma resultat som underhålla en anslutning tillstånd enhetsregistret med hjälp av mönstret pulsslag samtidigt som det är mer effektivt. Om du begär meddelande bekräftelser, kan IoT Hub meddela dig om vilka enheter som kan ta emot meddelanden och som inte är.

## <a name="device-and-module-lifecycle-notifications"></a>Enheten och modulen livscykelmeddelanden

IoT Hub kan meddela din IoT-lösning när en identitet skapas eller tas bort genom att skicka livscykelmeddelanden. Gör din IoT-lösning behöver du skapar en väg och datakällan ska vara lika med *DeviceLifecycleEvents* eller *ModuleLifecycleEvents*. Som standard inga livscykelmeddelanden skickas, dvs, inga sådana vägar redan finnas. Meddelandet innehåller egenskaperna och brödtext.

Egenskaper: Meddelandet Systemegenskaper föregås den `$` symbolen.

Meddelande för enheten:

| Namn | Värde |
| --- | --- |
|$content-typ | application/json |
|$iothub-enqueuedtime |  Tid när meddelandet skickades |
|$iothub-message-source | deviceLifecycleEvents |
|$content-encoding | utf-8 |
|opType | **createDeviceIdentity** eller **deleteDeviceIdentity** |
|HubName | Namnet på IoT Hub |
|deviceId | ID för enheten |
|operationTimestamp | ISO8601 tidsstämpeln för åtgärden |
|iothub-message-schema | deviceLifecycleNotification |

Brödtext: Det här avsnittet är i JSON-format och representerar tvilling av skapade enhetens identitet. Exempel:

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
Meddelande för modulen:

| Namn | Värde |
| --- | --- |
$content-typ | application/json |
$iothub-enqueuedtime |  Tid när meddelandet skickades |
$iothub-message-source | moduleLifecycleEvents |
$content-encoding | utf-8 |
opType | **createModuleIdentity** eller **deleteModuleIdentity** |
HubName | Namnet på IoT Hub |
moduleId | ID för modulen |
operationTimestamp | ISO8601 tidsstämpeln för åtgärden |
iothub-message-schema | moduleLifecycleNotification |

Brödtext: Det här avsnittet är i JSON-format och representerar läsningen av skapade modulen identitet. Exempel:

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

## <a name="device-identity-properties"></a>Egenskaper för enhet-identitet

Enhetsidentiteter representeras som JSON-dokument med följande egenskaper:

| Egenskap  | Alternativ | Beskrivning |
| --- | --- | --- |
| deviceId |krävs, skrivskyddad på uppdateringar |En skiftlägeskänslig sträng (upp till 128 tecken) med ASCII 7 bitar alfanumeriska tecken samt vissa specialtecken: `- . + % _ # * ? ! ( ) , = @ $ '`. |
| generationId |krävs, skrivskyddad |En IoT hub-genererade, skiftlägeskänsligt sträng högst 128 tecken. Det här värdet används för att skilja mellan enheter med samma **deviceId**, när de har tagits bort och återskapas. |
| etag |krävs, skrivskyddad |En sträng som representerar en svag ETag för enhetens identitet, enligt [RFC7232](https://tools.ietf.org/html/rfc7232). |
| auth |valfri |Ett sammansatt objekt som innehåller information och säkerhet material för autentisering. |
| auth.symkey |valfri |Ett sammansatt objekt som innehåller en primär och en sekundär nyckel som är lagrad i base64-format. |
| status |obligatorisk |En åtkomst-indikator. Kan vara **aktiverad** eller **inaktiverad**. Om **aktiverad**, enheten kan ansluta. Om **inaktiverad**, den här enheten har inte åtkomst till valfri enhet-riktade slutpunkt. |
| statusReason |valfri |En 128 tecken lång sträng som lagrar statusorsaken för enhetens identitet. Alla UTF-8-tecken tillåts. |
| statusUpdateTime |skrivskyddad |En temporal indikator som visar datum och tid för senaste statusuppdatering. |
| connectionState |skrivskyddad |Ett fält som anger status för användaranslutning: antingen **ansluten** eller **frånkopplad**. Det här fältet visar vyn IoT hubb för enhetens anslutningsstatus. **Viktiga**: Det här fältet bör användas endast för utveckling/felsökning. Anslutningsstatus uppdateras endast för enheter med hjälp av MQTT eller AMQP. Dessutom den baseras på på protokollnivå pingar (MQTT pingar eller AMQP-ping) och den kan ha en maximal fördröjning på endast 5 minuter. Därmed behöver kan det finnas falska positiva identifieringar, t.ex. enheter rapporteras som är anslutna, men som inte är ansluten. |
| connectionStateUpdatedTime |skrivskyddad |En temporal indikator som visar datum och tid för senaste status för anslutningen har uppdaterats. |
| lastActivityTime |skrivskyddad |En temporal indikator som visar datum och tid för senaste enheten är ansluten, tas emot eller skickat ett meddelande. |

> [!NOTE]
> Anslutningsläge kan bara representera IoT Hub-vy över statusen för anslutningen. Uppdateringar till det här tillståndet kan fördröjas beroende på nätverkets tillstånd och konfigurationer.

> [!NOTE]
> För närvarande enheten SDK: er stöder inte användning av den `+` och `#` tecken i den **deviceId**.

## <a name="module-identity-properties"></a>Modulen identitetsegenskaper

Modulen identiteter representeras som JSON-dokument med följande egenskaper:

| Egenskap  | Alternativ | Beskrivning |
| --- | --- | --- |
| deviceId |krävs, skrivskyddad på uppdateringar |En skiftlägeskänslig sträng (upp till 128 tecken) med ASCII 7 bitar alfanumeriska tecken samt vissa specialtecken: `- . + % _ # * ? ! ( ) , = @ $ '`. |
| moduleId |krävs, skrivskyddad på uppdateringar |En skiftlägeskänslig sträng (upp till 128 tecken) med ASCII 7 bitar alfanumeriska tecken samt vissa specialtecken: `- . + % _ # * ? ! ( ) , = @ $ '`. |
| generationId |krävs, skrivskyddad |En IoT hub-genererade, skiftlägeskänsligt sträng högst 128 tecken. Det här värdet används för att skilja mellan enheter med samma **deviceId**, när de har tagits bort och återskapas. |
| etag |krävs, skrivskyddad |En sträng som representerar en svag ETag för enhetens identitet, enligt [RFC7232](https://tools.ietf.org/html/rfc7232). |
| auth |valfri |Ett sammansatt objekt som innehåller information och säkerhet material för autentisering. |
| auth.symkey |valfri |Ett sammansatt objekt som innehåller en primär och en sekundär nyckel som är lagrad i base64-format. |
| status |obligatorisk |En åtkomst-indikator. Kan vara **aktiverad** eller **inaktiverad**. Om **aktiverad**, enheten kan ansluta. Om **inaktiverad**, den här enheten har inte åtkomst till valfri enhet-riktade slutpunkt. |
| statusReason |valfri |En 128 tecken lång sträng som lagrar statusorsaken för enhetens identitet. Alla UTF-8-tecken tillåts. |
| statusUpdateTime |skrivskyddad |En temporal indikator som visar datum och tid för senaste statusuppdatering. |
| connectionState |skrivskyddad |Ett fält som anger status för användaranslutning: antingen **ansluten** eller **frånkopplad**. Det här fältet visar vyn IoT hubb för enhetens anslutningsstatus. **Viktiga**: Det här fältet bör användas endast för utveckling/felsökning. Anslutningsstatus uppdateras endast för enheter med hjälp av MQTT eller AMQP. Dessutom den baseras på på protokollnivå pingar (MQTT pingar eller AMQP-ping) och den kan ha en maximal fördröjning på endast 5 minuter. Därmed behöver kan det finnas falska positiva identifieringar, t.ex. enheter rapporteras som är anslutna, men som inte är ansluten. |
| connectionStateUpdatedTime |skrivskyddad |En temporal indikator som visar datum och tid för senaste status för anslutningen har uppdaterats. |
| lastActivityTime |skrivskyddad |En temporal indikator som visar datum och tid för senaste enheten är ansluten, tas emot eller skickat ett meddelande. |

> [!NOTE]
> För närvarande enheten SDK: er stöder inte användning av den `+` och `#` tecken i den **deviceId** och **moduleId**.

## <a name="additional-reference-material"></a>Ytterligare referensmaterial

Andra referensavsnitten i IoT Hub developer guide inkluderar:

* [IoT Hub-slutpunkter](iot-hub-devguide-endpoints.md) beskriver de olika slutpunkter som varje IoT-hubb exponerar för körning och hanteringsåtgärder.

* [Begränsning och kvoter](iot-hub-devguide-quotas-throttling.md) beskriver kvoter och begränsningar beteenden som gäller för IoT Hub-tjänsten.

* [Azure IoT-enheten och tjänsten SDK: er](iot-hub-devguide-sdks.md) visar en lista över olika språk SDK: er som du kan använda när du utvecklar appar för både enheten och tjänsten som interagerar med IoT Hub.

* [IoT Hub-frågespråk](iot-hub-devguide-query-language.md) beskriver frågespråk som du kan använda för att hämta information från IoT Hub om enhetstvillingar och jobb.

* [IoT Hub MQTT-support](iot-hub-mqtt-support.md) innehåller mer information om IoT Hub-stöd för MQTT-protokollet.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du använder IoT Hub-identitetsregistret, kanske du är intresserad av i följande avsnitt för IoT Hub developer guide:

* [Styra åtkomst till IoT Hub](iot-hub-devguide-security.md)

* [Använda enhetstvillingar för att synkronisera tillstånd och konfigurationer](iot-hub-devguide-device-twins.md)

* [Anropa en direkt metod på en enhet](iot-hub-devguide-direct-methods.md)

* [Schemalägga jobb på flera enheter](iot-hub-devguide-jobs.md)

Se följande självstudie för IoT Hub för att prova några av de koncept som beskrivs i den här artikeln:

* [Kom igång med Azure IoT Hub](quickstart-send-telemetry-dotnet.md)

Utforska använder IoT Hub Device Provisioning-tjänsten för att aktivera zero-touch och just-in-time-etablering, se: 

* [Azure IoT Hub Device Provisioning-tjänst](https://azure.microsoft.com/documentation/services/iot-dps)
