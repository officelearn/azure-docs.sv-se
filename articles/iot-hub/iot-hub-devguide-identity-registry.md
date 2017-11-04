---
title: "Förstå Azure IoT Hub identitetsregistret | Microsoft Docs"
description: "Utvecklarhandbok - beskrivning av IoT-hubb identitetsregistret och hur du använder den för att hantera dina enheter. Innehåller information om import och export av enheten identiteter gruppvis."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 0706eccd-e84c-4ae7-bbd4-2b1a22241147
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/19/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 32e63b250467f5733b2e691614fe52f96f2f9d91
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2017
---
# <a name="understand-the-identity-registry-in-your-iot-hub"></a>Förstå identitetsregistret i din IoT-hubb

Alla IoT-hubben har en identitetsregistret som lagrar information om de enheter som får ansluta till IoT-hubben. Innan en enhet kan ansluta till en IoT-hubb kan finnas det en post för enheten i IoT-hubben identitetsregistret. En enhet måste också autentisera med IoT-hubben utifrån autentiseringsuppgifter som lagras i identitetsregistret.

Enhets-ID som lagras i identitetsregistret är skiftlägeskänslig.

På en hög nivå är identitetsregistret en REST-kompatibla samling enhetsresurser identitet. När du lägger till en post i identitetsregistret skapar IoT-hubb en uppsättning resurser per enhet, till exempel den kö som innehåller relä moln till enhet meddelanden.

Använd identitetsregistret när du behöver:

* Etablera enheter som ansluter till din IoT-hubb.
* Kontrollera per enhet åtkomst till din hubb enheten riktade slutpunkter.

> [!NOTE]
> Identitetsregistret innehåller inte några programspecifika metadata.

## <a name="identity-registry-operations"></a>Identitetsregisteråtgärder

IoT-hubb identitetsregistret visar följande åtgärder:

* Skapa enhetsidentitet
* Uppdatera enhetsidentitet
* Hämta enhetsidentitet-ID: t
* Ta bort enhetens identitet
* Visa en lista med upp till 1 000 identiteter
* Exportera alla identiteter till Azure blob storage
* Importera identiteter från Azure blob storage

Dessa åtgärder kan använda Optimistisk samtidighet som anges i [RFC7232][lnk-rfc7232].

> [!IMPORTANT]
> Det enda sättet att hämta alla identiteter i en IoT-hubb identitetsregistret är att använda den [exportera] [ lnk-export] funktioner.

En IoT-hubb identitetsregistret:

* Innehåller inte några metadata för program.
* Kan användas som en ordlista med hjälp av den **deviceId** som nyckel.
* Stöder inte lättfattliga frågor.

En IoT-lösning har vanligtvis ett separat Arkiv för Lösningsspecifika som innehåller programspecifika metadata. Arkivet Lösningsspecifika i en lösning för smart byggnad skulle till exempel registrera den plats där en temperatursensor distribueras.

> [!IMPORTANT]
> Använd endast identitetsregistret för enhetshantering och etablering av åtgärder. Hög genomströmning åtgärder vid körning bör inte beroende utför åtgärder i identitetsregistret. Till exempel är kontrollerar anslutningsstatus för en enhet innan du skickar ett kommando inte ett mönster som stöds. Se till att markera den [begränsning priser] [ lnk-quotas] för identitetsregistret och [enheten pulsslag] [ lnk-guidance-heartbeat] mönster.

## <a name="disable-devices"></a>Inaktivera enheter

Du kan inaktivera enheter genom att uppdatera den **status** -egenskapen för en identitet i identitetsregistret. Normalt använder du den här egenskapen i två scenarier:

* Under en allokering orchestration-process. Mer information finns i [Enhetsetableringen][lnk-guidance-provisioning].
* Om du av någon anledning du anser att en enhet har komprometterats eller blivit obehörig.

## <a name="import-and-export-device-identities"></a>Importera och exportera enheten identiteter

Du kan exportera enheten identiteter i grupp från en IoT-hubb identitetsregistret med hjälp av asynkrona åtgärder på den [IoT-hubb resurs leverantörsslutpunkt][lnk-endpoints]. Export är långvariga jobb som använder en kundens blob-behållare för att spara enhetens identitetsdata läses från identitetsregistret.

Du kan importera enheten identiteter gruppvis till identitetsregistret för en IoT-hubb med hjälp av asynkrona åtgärder på den [IoT-hubb resurs leverantörsslutpunkt][lnk-endpoints]. Import är långvariga jobb som använder data i en kundens blob-behållare för att skriva data på enheten identitet till identitetsregistret.

Detaljerad information om import och export API: er finns [IoT-hubb resursprovidern REST API: er][lnk-resource-provider-apis]. Om du vill veta mer om hur du kör import och exportera jobben, se [Massredigera hantering av identiteter för IoT-hubb enheten][lnk-bulk-identity].

## <a name="device-provisioning"></a>Enhetsetableringen

Enhetsdata som lagras i en viss IoT-lösningen är beroende av de särskilda kraven i lösningen. Men minst en lösning måste lagra enheten identiteter och autentiseringsnycklar. Azure IoT-hubb innehåller en identitetsregistret som kan lagra värdena för varje enhet, t.ex ID, autentiseringsnycklar och statuskoder. En lösning kan använda andra Azure-tjänster, till exempel tabellagring eller blob-lagring Cosmos-databas för att lagra alla övriga data.

*Enhetsetableringen* är processen att lägga till den första enhetsdata lagras i din lösning. Om du vill aktivera en ny enhet att ansluta till din hubb, måste du lägga till en enhets-ID och nycklar identitetsregistret IoT-hubb. Som en del av etableringsprocessen kan behöva du initiera enhetsspecifika data i andra lösning Arkiv.

## <a name="device-heartbeat"></a>Enheten pulsslag

IoT-hubb identitetsregistret innehåller ett fält med namnet **connectionState**. Använd bara den **connectionState** fältet under utveckling och felsökning. IoT-lösningar bör inte att fråga fältet vid körning. Till exempel fråga inte den **connectionState** fält som du vill kontrollera om en enhet är ansluten innan du skickar ett moln till enhet eller ett SMS.

Om din IoT-lösningen behöver veta om en enhet är ansluten, bör du implementera den *pulsslag mönster*.

I mönstret pulsslag skickar enheten meddelanden från enhet till moln minst en gång var fast tidsperiod (t.ex, minst en gång i timmen). Därför även om en enhet inte har några data att skicka skickar fortfarande den ett tomt meddelande enhet till moln (vanligtvis med en egenskap som identifierar den som ett pulsslag). På tjänstsidan upprätthåller en karta med det sista pulsslaget togs emot för varje enhet i lösningen. Om lösningen inte får ett heartbeat-meddelande inom den förväntade tiden från enheten, förutsätter att det finns ett problem med enheten.

En mer komplex implementering kan inkludera information från [operations övervakning] [ lnk-devguide-opmon] att identifiera enheter som försöker ansluta eller kommunicera men misslyckas. När du implementerar heartbeat-mönster, se till att markera [IoT-hubb kvoter och begränsningar][lnk-quotas].

> [!NOTE]
> Om en IoT-lösningen använder anslutningsstatus enbart för att avgöra om du ska skicka moln till enhet meddelanden och meddelanden skickas inte till stora mängder enheter, bör du använda den enklare *kort förfallotid* mönster. Det här mönstret ger samma resultat som du underhålla enheten anslutning tillstånd registret med hjälp av heartbeat-mönster, samtidigt som det är mer effektivt. Om du begär meddelande bekräftelser kan IoT-hubb meddela dig om vilka enheter som kan ta emot meddelanden och som inte är.

## <a name="device-lifecycle-notifications"></a>Enheten livscykelmeddelanden

IoT-hubb kan meddela din IoT-lösning när en enhetsidentitet skapas eller tas bort genom att skicka meddelanden för enheten livscykel. Om du vill göra det, IoT-lösningen behöver skapa en väg och datakällan ska vara lika med *DeviceLifecycleEvents*. Inga livscykelmeddelanden skickas som standard, som är det inför finns ingen sådan vägar. Meddelandet innehåller egenskaperna och innehållet.

Egenskaper: Meddelandet Systemegenskaper föregås av `'$'` symbolen.

| Namn | Värde |
| --- | --- |
$content-typ | application/json |
$iothub-enqueuedtime |  Tidpunkt som meddelandet skickades |
$iothub-meddelande-källa | deviceLifecycleEvents |
$content-kodning | UTF-8 |
opType | **createDeviceIdentity** eller **deleteDeviceIdentity** |
hubName | Namnet på IoT-hubb |
deviceId | ID för enheten |
operationTimestamp | ISO8601 tidsstämpeln för åtgärden |
iothub-meddelande-schema | deviceLifecycleNotification |

Body: Det här avsnittet är i JSON-format och representerar dubbla av skapade enhetens identitet. Exempel:

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

## <a name="device-identity-properties"></a>Egenskaper för enhet identitet

Enheten identiteter representeras som JSON-dokument med följande egenskaper:

| Egenskap | Alternativ | Beskrivning |
| --- | --- | --- |
| deviceId |krävs, skrivskyddad på uppdateringar |En skiftlägeskänslig sträng (upp till 128 tecken) av ASCII-7-bitars alfanumeriska tecken samt vissa specialtecken: `- : . + % _ # * ? ! ( ) , = @ ; $ '`. |
| ID för virtuella datorer |krävs, skrivskyddad |En IoT hub-genererade, skiftlägeskänsliga sträng upp till 128 tecken. Det här värdet används för att skilja mellan enheter med samma **deviceId**, när de har tagits bort och återskapas. |
| ETag |krävs, skrivskyddad |En sträng som representerar en svag ETag för enhetens identitet enligt [RFC7232][lnk-rfc7232]. |
| auth |Valfria |En sammansatt objekt som innehåller information och säkerhet material för autentisering. |
| auth.symkey |Valfria |En sammansatt objekt som innehåller en primär och en sekundär nyckel lagrad i base64-format. |
| status |Krävs |En åtkomst-indikator. Kan vara **aktiverad** eller **inaktiverade**. Om **aktiverad**, enheten kan ansluta. Om **inaktiverad**, den här enheten har inte åtkomst till valfri enhet riktade slutpunkt. |
| statusReason |Valfria |En 128 tecken lång sträng som lagrar Statusanledning för enhetens identitet. Alla UTF-8-tecken tillåts. |
| statusUpdateTime |Skrivskyddad |En temporal indikator som visar datum och tid för senaste status för uppdateringen. |
| connectionState |Skrivskyddad |Ett fält som anger status för användaranslutning: antingen **ansluten** eller **frånkopplad**. Det här fältet visar vyn IoT-hubb för enhetens anslutningsstatus. **Viktiga**: det här fältet bör användas endast för utveckling/felsökning. Om anslutningens status uppdateras bara för enheter med hjälp av MQTT eller AMQP. Dessutom baseras på protokollnivå pingar (MQTT pingar eller AMQP ping) och den kan ha en maximal fördröjning på endast 5 minuter. Därmed behöver kan det finnas falska positiva identifieringar, t.ex enheter rapporteras som är ansluten men som inte är ansluten. |
| connectionStateUpdatedTime |Skrivskyddad |En temporal indikator som visar datum och tid för senaste status för anslutningen har uppdaterats. |
| lastActivityTime |Skrivskyddad |En temporal indikator visar datum och tid för senaste enheten ansluten, tas emot eller skickade ett meddelande. |

> [!NOTE]
> Status för anslutningen kan endast representerar vyn IoT-hubb i status för anslutningen. Uppdateringar till det här tillståndet kan fördröjas beroende på nätverkets tillstånd och konfigurationer.

## <a name="additional-reference-material"></a>Ytterligare referensmaterialet

Andra referensavsnitten i utvecklarhandboken för IoT-hubben är:

* [IoT-hubbslutpunkter] [ lnk-endpoints] beskriver de olika slutpunkter som varje IoT-hubb visar för körning och hanteringsåtgärder.
* [Begränsning och kvoter] [ lnk-quotas] beskriver kvoter och begränsning beteenden som tillämpas på tjänsten IoT-hubb.
* [Azure IoT-enheten och tjänsten SDK] [ lnk-sdks] Listar olika språk SDK: er som du kan använda när du utvecklar appar för både enheten och tjänsten som interagerar med IoT-hubben.
* [IoT-hubb frågespråket] [ lnk-query] beskriver frågespråk som du kan använda för att hämta information från IoT-hubb om enheten twins och jobb.
* [Stöd för IoT-hubb MQTT] [ lnk-devguide-mqtt] ger mer information om stöd för IoT-hubb för MQTT-protokollet.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du använder identitetsregistret IoT-hubb, kan du är intresserad av i följande avsnitt för IoT-hubb developer-guide:

* [Kontrollera åtkomsten till IoT-hubb][lnk-devguide-security]
* [Använd twins för enheten för att synkronisera tillstånd och konfigurationer][lnk-devguide-device-twins]
* [Anropa en metod som är direkt på en enhet][lnk-devguide-directmethods]
* [Schema-jobb på flera enheter][lnk-devguide-jobs]

Om du vill testa vissa av de begrepp som beskrivs i den här artikeln får du är intresserad av följande IoT-hubb kursen:

* [Kom igång med Azure IoT-hubb][lnk-getstarted-tutorial]

<!-- Links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-guidance-provisioning]: iot-hub-devguide-identity-registry.md#device-provisioning
[lnk-guidance-heartbeat]: iot-hub-devguide-identity-registry.md#device-heartbeat
[lnk-rfc7232]: https://tools.ietf.org/html/rfc7232
[lnk-bulk-identity]: iot-hub-bulk-identity-mgmt.md
[lnk-export]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities
[lnk-devguide-opmon]: iot-hub-operations-monitoring.md

[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md

[lnk-getstarted-tutorial]: iot-hub-csharp-csharp-getstarted.md
