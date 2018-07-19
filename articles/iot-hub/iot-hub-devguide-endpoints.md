---
title: Förstå Azure IoT Hub-slutpunkter | Microsoft Docs
description: Utvecklarguide – information om IoT Hub enheten kund- och service-riktade slutpunkter.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: dobett
ms.openlocfilehash: bf23046b8a80b02bc1667f647cb1d475503a8feb
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39125784"
---
# <a name="reference---iot-hub-endpoints"></a>Referens – IoT Hub-slutpunkter

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="iot-hub-names"></a>IoT Hub-namn

Du kan hitta värdnamnet för IoT-hubben som är värd för dina slutpunkter i portalen på din hubb **översikt** sidan. Som standard är DNS-namnet på en IoT-hubb ser ut som: `{your iot hub name}.azure-devices.net`.

Du kan använda Azure DNS för att skapa ett anpassat DNS-namn för din IoT hub. Mer information finns i [Använda Azure DNS för att skapa inställningar för anpassad domän för en Azure-tjänst](../dns/dns-custom-domain.md).

## <a name="list-of-built-in-iot-hub-endpoints"></a>Lista över inbyggda IoT Hub-slutpunkter

Azure IoT Hub är en tjänst för flera innehavare som innehåller dess funktioner till olika aktörer. I följande diagram visas de olika slutpunkterna exponerar IoT Hub.

![IoT Hub-slutpunkter][img-endpoints]

I följande lista beskrivs slutpunkterna:

* **Resursprovidern**. Resursprovidern IoT Hub Exponerar en [Azure Resource Manager] [ lnk-arm] gränssnitt. Det här gränssnittet gör det möjligt för Azure-prenumerationsägare att skapa och ta bort IoT-hubbar och för att uppdatera egenskaper för IoT hub. IoT Hub egenskaper styr [principer för säkerhet på radnivå hub][lnk-accesscontrol], till skillnad från på enhetsnivå access control och funktionella alternativ för moln-till-enhet och enhet till moln-meddelanden. Resursprovidern IoT Hub kan du också [exportera enhetsidentiteter][lnk-importexport].
* **Identitetshantering för enheten**. Varje IoT-hubb Exponerar en uppsättning HTTPS REST-slutpunkter för att hantera enhetsidentiteter (skapa, hämta, uppdatera och ta bort). [Enhetsidentiteter] [ lnk-device-identities] används för autentisering och åtkomstkontroll för enheten.
* **Enhetshantering twin**. Varje IoT-hubb innehåller en rad för tjänst för webbservergrupper på HTTPS-REST-slutpunkt för fråge- och [enhetstvillingar] [ lnk-twins] (uppdatering taggar och egenskaper).
* **Jobb management**. Varje IoT-hubb Exponerar en uppsättning service webbservergrupper HTTPS REST-slutpunkt fråga och hantera [jobb][lnk-jobs].
* **Enheten slutpunkter**. För varje enhet i identitetsregistret exponerar IoT-hubb en uppsättning slutpunkter:

  * *Skicka meddelanden från enheten till molnet*. En enhet använder den här slutpunkten till [skicka meddelanden från enheten till molnet][lnk-d2c].
  * *Ta emot meddelanden från moln till enhet*. En enhet använder den här slutpunkten för att ta emot riktade [meddelanden från moln till enhet][lnk-c2d].
  * *Initierar filöverföringar*. En enhet använder den här slutpunkten för att ta emot ett Azure Storage SAS-URI från IoT Hub och [ladda upp en fil][lnk-upload].
  * *Hämta och uppdatera tvillingegenskaper*. En enhet använder den här slutpunkten för att få åtkomst till dess [enhetstvillingen][lnk-twins]'s egenskaper.
  * *Ta emot förfrågningar för direkt metod*. En enhet använder den här slutpunkten för att lyssna efter [direktmetod][lnk-methods]'s begäranden.

    De här slutpunkterna blir tillgängliga via [MQTT v3.1.1][lnk-mqtt], HTTPS 1.1 och [AMQP 1.0] [ lnk-amqp] protokoll. AMQP är också tillgänglig via [WebSockets] [ lnk-websockets] på port 443.

* **Tjänstslutpunkter**. Varje IoT-hubb Exponerar en uppsättning slutpunkter för lösningens backend-servrar att kommunicera med dina enheter. Med ett undantag slutpunkterna är bara tillgängliga via den [AMQP] [ lnk-amqp] protokoll. Metoden anrop slutpunkten exponeras via HTTPS-protokollet.
  
  * *Ta emot meddelanden från enheten till molnet*. Den här slutpunkten är kompatibel med [Azure Event Hubs][lnk-event-hubs]. En backend tjänst kan använda den för att läsa den [meddelanden från enheten till molnet] [ lnk-d2c] skickas av dina enheter. Du kan skapa anpassade slutpunkter i IoT-hubben förutom den här inbyggda slutpunkten.
  * *Skicka meddelanden från moln till enhet och ta emot bekräftelser för leverans*. De här slutpunkterna aktivera lösningens backend-servrar att skicka tillförlitlig [meddelanden från moln till enhet][lnk-c2d], samt för att få motsvarande leverans- eller förfallodatum bekräftelser.
  * *Ta emot meddelanden i filen*. Den här slutpunkten för meddelanden kan du ta emot meddelanden om när dina enheter har överför en fil. 
  * *Dirigera metodanropet*. Den här slutpunkten kan en backend-tjänst för att anropa en [direktmetod] [ lnk-methods] på en enhet.
  * *Ta emot händelser för åtgärdsövervakning*. Den här slutpunkten kan du ta emot händelseövervakning om IoT-hubben har konfigurerats för att generera dem. Mer information finns i [IoT Hub åtgärdsövervakning][lnk-operations-mon].

Den [Azure IoT SDK: er] [ lnk-sdks] artikeln beskrivs olika sätt att få åtkomst till dessa slutpunkter.

Alla slutpunkter i IoT Hub använder den [TLS] [ lnk-tls] protokollet och ingen slutpunkt exponeras någonsin via okrypterade/oskyddad kanaler.

## <a name="custom-endpoints"></a>Anpassade slutpunkter

Du kan länka befintliga Azure-tjänster i din prenumeration till din IoT hub så att den fungerar som slutpunkter för meddelanderoutning. De här slutpunkterna fungerar som slutpunkter och används som mottagare för meddelandevägar. Enheter kan inte skrivas direkt till ytterligare slutpunkter. Läs mer om meddelandevägar i posten developer guide på [skicka och ta emot meddelanden med IoT hub][lnk-devguide-messaging].

IoT Hub stöder för närvarande följande Azure-tjänster som ytterligare slutpunkter:

* Azure Storage-behållare
* Event Hubs
* Service Bus-köer
* Avsnitt om Service Bus

IoT Hub måste ha skrivbehörighet för dessa tjänstslutpunkter för meddelanderoutning för att fungera. Om du konfigurerar dina slutpunkter via Azure portal, läggs behörigheterna som krävs för dig. Kontrollera att du konfigurerar dina tjänster för att stödja det förväntade dataflödet. När du först konfigurera din IoT-lösning kan du behöva övervaka din ytterligare slutpunkter och gör eventuella ändringar för den faktiska belastningen.

Om ett meddelande matchar flera vägar som alla pekar mot samma slutpunkt, IoT-hubb levererar meddelanden till denna slutpunkt bara en gång. Därför behöver du inte konfigurera deduplicering på din Service Bus-kö eller ämne. I partitionerade köer garanterar partition tillhörighet ordningsföljd för meddelanden.

Gränser för antalet slutpunkter som du kan lägga till, se [kvoter och begränsningar][lnk-devguide-quotas].

### <a name="when-using-azure-storage-containers"></a>När du använder Azure Storage-behållare

IoT Hub stöder endast skriva data till Azure Storage-behållare som blobbar i den [Apache Avro](http://avro.apache.org/) format. IoT Hub slår ihop meddelanden och skriver data till en blob när:

* Batch når en viss storlek.
* Eller en viss tidsperiod har gått ut.

IoT Hub skriver till en tom blob om det finns inga data att skriva.

IoT Hub som standard följande namngivningskonvention för filen:

```
{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}
```

Du kan använda oavsett fil naming convention som du vill, men du måste ha alla listade token.

### <a name="when-using-service-bus-queues-and-topics"></a>När du använder Service Bus-köer och ämnen

Service Bus-köer och ämnen som används som IoT Hub-slutpunkter inte får ha **sessioner** eller **dubblettidentifiering** aktiverat. Om något av dessa alternativ är aktiverade ändpunkt som **tillbaka** i Azure-portalen.

## <a name="field-gateways"></a>Fält-gateways

I en IoT-lösning kan en *fältgateway* är placerad mellan dina enheter och IoT Hub-slutpunkter. Det finns normalt nära dina enheter. Dina enheter kommunicerar direkt med fält-gateway med hjälp av ett protokoll som stöds av enheter. Fält-gateway ansluter till en IoT Hub-slutpunkt med ett protokoll som stöds av IoT Hub. En fält-gateway kan vara en särskild maskinvaruenhet eller en låg-dator som kör anpassade gateway-programvaran.

Du kan använda [Azure IoT Edge] [ lnk-iot-edge] att implementera en fält-gateway. IoT Edge erbjuder funktioner som multiplexering kommunikation från flera enheter på samma IoT Hub-anslutning.

## <a name="next-steps"></a>Nästa steg

Andra referensavsnitten i det här utvecklarhandboken för IoT Hub är:

* [IoT Hub-frågespråk för enhetstvillingar, jobb och meddelanderoutning][lnk-devguide-query]
* [Kvoter och begränsningar][lnk-devguide-quotas]
* [IoT Hub MQTT-support][lnk-devguide-mqtt]

[lnk-iot-edge]: https://github.com/Azure/iot-edge

[img-endpoints]: ./media/iot-hub-devguide-endpoints/endpoints.png
[lnk-amqp]: https://www.amqp.org/
[lnk-mqtt]: http://mqtt.org/
[lnk-websockets]: https://tools.ietf.org/html/rfc6455
[lnk-arm]: ../azure-resource-manager/resource-group-overview.md
[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/

[lnk-tls]: https://tools.ietf.org/html/rfc5246


[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-accesscontrol]: iot-hub-devguide-security.md#access-control-and-permissions
[lnk-importexport]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities
[lnk-d2c]: iot-hub-devguide-messages-d2c.md
[lnk-device-identities]: iot-hub-devguide-identity-registry.md
[lnk-upload]: iot-hub-devguide-file-upload.md
[lnk-c2d]: iot-hub-devguide-messages-c2d.md
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-jobs]: iot-hub-devguide-jobs.md

[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[lnk-operations-mon]: iot-hub-operations-monitoring.md
