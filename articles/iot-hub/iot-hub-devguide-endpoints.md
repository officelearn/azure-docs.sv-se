---
title: Förstå Azure IoT-hubbslutpunkter | Microsoft Docs
description: Utvecklarhandbok - referensinformation om IoT-hubb riktade enheten och tjänsten riktade slutpunkter.
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 57ba52ae-19c6-43e4-bc6c-d8a5c2476e95
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: d9378a084d8ff421539a445986a5a18d073f3ce6
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="reference---iot-hub-endpoints"></a>Referens - slutpunkter för IoT-hubb

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="iot-hub-names"></a>IoT-hubb namn

Du hittar namnet på IoT-hubb som är värd för dina slutpunkter i portalen på den **översikt** bladet. Som standard är DNS-namnet för en IoT-hubb ser ut som: `{your iot hub name}.azure-devices.net`.

Du kan använda Azure DNS för att skapa en anpassad DNS-namn för din IoT-hubb. Mer information finns i [Använd Azure DNS för att ange inställningar för anpassad domän för en Azure-tjänst](../dns/dns-custom-domain.md).

## <a name="list-of-built-in-iot-hub-endpoints"></a>Lista över inbyggda IoT-hubb-slutpunkter

Azure IoT-hubb är en tjänst för flera innehavare som visar dess funktioner till olika aktörer. I följande diagram visas de olika slutpunkterna som visar IoT-hubb.

![IoT Hub-slutpunkter][img-endpoints]

I följande lista beskrivs slutpunkterna:

* **Resursprovidern**. IoT-hubb resursprovidern Exponerar en [Azure Resource Manager] [ lnk-arm] gränssnitt. Det här gränssnittet aktiverar Azure-prenumeration ägare att skapa och ta bort IoT-hubbar och för att uppdatera IoT-hubb egenskaper. IoT-hubb egenskaper styr [hubb nivå säkerhetsprinciper][lnk-accesscontrol], till skillnad från enheten på användarnivå och funktionella alternativ för moln till enhet och enheten till molnet. Resursprovidern IoT-hubb kan du också [exportera enheten identiteter][lnk-importexport].
* **Enheten Identitetshantering**. Varje IoT-hubb uppvisar en uppsättning HTTPS REST-slutpunkter för att hantera identiteter för enheten (skapa, hämta, uppdatera och ta bort). [Enheten identiteter] [ lnk-device-identities] används för autentisering och åtkomstkontroll för enheten.
* **Dubbla enhetshantering**. Varje IoT-hubb uppvisar en uppsättning service-riktade HTTPS REST-slutpunkt för frågor och uppdaterar [enhet twins] [ lnk-twins] (uppdatering taggar och egenskaper).
* **Jobb management**. Varje IoT-hubb uppvisar en uppsättning service-riktade HTTPS REST-slutpunkt för att fråga efter och hantera [jobb][lnk-jobs].
* **Enheten slutpunkter**. För varje enhet i identitetsregistret visar IoT-hubb en uppsättning slutpunkter:

  * *Skicka meddelanden från enhet till moln*. En enhet använder den här slutpunkten till [skicka meddelanden från enhet till moln][lnk-d2c].
  * *Ta emot meddelanden moln till enhet*. En enhet använder den här slutpunkten för att ta emot riktade [moln till enhet meddelanden][lnk-c2d].
  * *Initierar filöverföringar*. En enhet använder den här slutpunkten för att ta emot en Azure Storage SAS URI från IoT-hubb för att [överför en fil][lnk-upload].
  * *Hämta och uppdatera egenskaper för enhet dubbla*. En enhet använder den här slutpunkten för åtkomst till dess [enheten dubbla][lnk-twins]'s egenskaper.
  * *Ta emot begäranden om direkta metoden*. En enhet använder den här slutpunkten för att lyssna efter [direkt metod][lnk-methods]'s begäranden.

    Dessa slutpunkter som exponeras med hjälp av [MQTT v3.1.1][lnk-mqtt], HTTPS 1.1 och [AMQP 1.0] [ lnk-amqp] protokoll. AMQP är också tillgänglig via [WebSockets] [ lnk-websockets] på port 443.

* **Tjänstens slutpunkter**. Varje IoT-hubb uppvisar en uppsättning slutpunkter för din lösningens serverdel att kommunicera med dina enheter. Med ett undantag dessa slutpunkter är bara tillgängliga med den [AMQP] [ lnk-amqp] protokoll. Metoden anrop slutpunkten exponeras över HTTPS-protokoll.
  
  * *Ta emot meddelanden från enhet till moln*. Den här slutpunkten är kompatibel med [Azure Event Hubs][lnk-event-hubs]. En backend tjänst kan använda den för att läsa den [meddelanden från enhet till moln] [ lnk-d2c] skickas av dina enheter. Du kan skapa anpassade slutpunkter på din IoT-hubb förutom den här inbyggda slutpunkten.
  * *Skicka meddelanden moln till enhet och ta emot bekräftelser för leverans av*. Dessa slutpunkter aktivera din lösningens serverdel ska skickas pålitlig [moln till enhet meddelanden][lnk-c2d], samt för att få motsvarande leverans eller upphör att gälla bekräftelser.
  * *Ta emot meddelanden i filen*. Den här asynkrona slutpunkten kan du ta emot meddelanden om när dina enheter har överfört en fil. 
  * *Dirigera metodanropet*. Den här slutpunkten kan en backend-tjänst att anropa en [direkt metod] [ lnk-methods] på en enhet.
  * *Ta emot operations händelseövervakning*. Den här slutpunkten kan du få övervaka händelser om din IoT-hubb har konfigurerats för att generera dessa åtgärder. Mer information finns i [IoT-hubb operations övervakning][lnk-operations-mon].

Den [Azure IoT SDK] [ lnk-sdks] beskrivs olika sätt att få åtkomst till dessa slutpunkter.

Alla IoT-hubbslutpunkter använder den [TLS] [ lnk-tls] protokoll och ingen slutpunkt exponeras aldrig på okrypterade/oskyddad kanaler.

## <a name="custom-endpoints"></a>Anpassade slutpunkter

Du kan länka befintliga Azure-tjänster i din prenumeration för din IoT-hubb som fungerar som slutpunkter för routning av meddelanden. Dessa slutpunkter fungerar som slutpunkter och används som sänkor för meddelandevägar. Enheter kan inte skriva direkt till ytterligare slutpunkter. Mer information om meddelandevägar Se developer guide även [skicka och ta emot meddelanden med IoT-hubb][lnk-devguide-messaging].

IoT-hubb stöder för närvarande följande Azure-tjänster som ytterligare slutpunkter:

* Azure Storage-behållare
* Event Hubs
* Service Bus-köer
* Avsnitt om Service Bus

IoT-hubb måste ha skrivbehörighet till dessa Tjänsteslutpunkter för meddelanderoutning för att fungera. Om du konfigurerar dina slutpunkter via Azure portal, läggs behörigheterna som krävs för dig. Kontrollera att du konfigurerar dina tjänster för att stödja det förväntade genomflödet. När du först konfigurera din IoT-lösning kan du behöva övervaka dina ytterligare slutpunkter och gör eventuella ändringar för den faktiska belastningen.

Om ett meddelande matchar flera vägar som alla pekar till samma slutpunkt, levererar IoT-hubb meddelandet till denna slutpunkt bara en gång. Därför behöver du inte konfigurera deduplicering på din Service Bus-kö eller ett ämne. I den partitionerade köer garanterar partition tillhörighet meddelandet ordning.

Begränsningar för antalet slutpunkter som du kan lägga till finns [kvoter och begränsning][lnk-devguide-quotas].

### <a name="when-using-azure-storage-containers"></a>När du använder Azure Storage-behållare

IoT-hubben har bara stöd för skrivning av data till Azure Storage-behållare som blobar i den [Apache Avro](http://avro.apache.org/) format. IoT-hubb batchar meddelanden och skriver data till en blobb när:

* Batchen når en viss storlek.
* Eller mängden tid har gått ut.

IoT-hubb skriver till en tom blob om det finns inga data att skriva.

IoT-hubb som standard följande namngivningskonvention för filen:

```
{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}
```

Du kan använda oavsett fil namngivningskonvention som du vill, men du måste ha alla listade token.

### <a name="when-using-service-bus-queues-and-topics"></a>När du använder Service Bus-köer och ämnen

Service Bus-köer och ämnen som används som IoT-hubbslutpunkter inte får ha **sessioner** eller **dubblettidentifiering** aktiverat. Om något av dessa alternativ är aktiverade ändpunkt som **inte åtkomlig** i Azure-portalen.

## <a name="field-gateways"></a>Fältet gateways

I en IoT-lösningen en *fältet gateway* placeras mellan dina enheter och slutpunkter för din IoT-hubb. Det finns normalt sett nära dina enheter. Dina enheter kommunicerar direkt med fältet gateway med hjälp av ett protokoll som stöds av enheter. Fältet gatewayen ansluter till en IoT-hubb slutpunkt med ett protokoll som stöds av IoT-hubb. En gateway för fältet kan vara en särskild maskinvaruenhet eller en låg energiförbrukning-dator som kör anpassade gateway-programvaran.

Du kan använda [Azure IoT kant] [ lnk-iot-edge] att implementera en gateway för fältet. IoT-Edge erbjuder funktioner, till exempel multiplexering kommunikation från flera enheter på samma IoT-hubb-anslutning.

## <a name="next-steps"></a>Nästa steg

Andra referensavsnitten i den här IoT-hubb Utvecklarhandbok inkluderar:

* [IoT-hubb frågespråk för enheten twins, jobb och meddelanderoutning][lnk-devguide-query]
* [Kvoter och begränsning][lnk-devguide-quotas]
* [IoT-hubb MQTT stöd][lnk-devguide-mqtt]

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
