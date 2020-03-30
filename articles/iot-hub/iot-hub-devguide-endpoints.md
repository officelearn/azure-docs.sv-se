---
title: Förstå Azure IoT Hub-slutpunkter | Microsoft-dokument
description: Utvecklarguide – referensinformation om IoT Hub-enhetsinriktade och tjänstinriktade slutpunkter.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: da6d17e42407048b7ecbcacade67ef48046d7fe1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284647"
---
# <a name="reference---iot-hub-endpoints"></a>Referens - Slutpunkter för IoT Hub

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="iot-hub-names"></a>IoT Hub-namn

Du hittar värdnamnet för IoT-hubben som är värd för dina slutpunkter i portalen på hubbens **översiktssida.** Som standard ser DNS-namnet på en IoT-hubb ut som: `{your iot hub name}.azure-devices.net`.

## <a name="list-of-built-in-iot-hub-endpoints"></a>Lista över inbyggda IoT Hub-slutpunkter

Azure IoT Hub är en tjänst med flera innehavare som exponerar dess funktioner för olika aktörer. I följande diagram visas de olika slutpunkter som IoT Hub exponerar.

![IoT Hub-slutpunkter](./media/iot-hub-devguide-endpoints/endpoints.png)

I följande lista beskrivs slutpunkterna:

* **Resursprovider**. IoT Hub-resursprovidern visar ett [Azure Resource Manager-gränssnitt.](../azure-resource-manager/management/overview.md) Det här gränssnittet gör det möjligt för Azure-prenumerationsägare att skapa och ta bort IoT-hubbar och uppdatera IoT-hubbegenskaper. IoT [Hub-egenskaper styr säkerhetsprinciper på navnivå](iot-hub-devguide-security.md#access-control-and-permissions), i motsats till åtkomstkontroll på enhetsnivå och funktionella alternativ för moln-till-enhet och enhet-till-moln-meddelanden. IoT Hub-resursprovidern gör det också möjligt för dig att [exportera enhetsidentiteter](iot-hub-devguide-identity-registry.md#import-and-export-device-identities).

* **Hantering av enhetsidentitet**. Varje IoT-hubb visar en uppsättning HTTPS REST-slutpunkter för att hantera enhetsidentiteter (skapa, hämta, uppdatera och ta bort). [Enhetsidentiteter](iot-hub-devguide-identity-registry.md) används för enhetsautentisering och åtkomstkontroll.

* **Hantering av enhetstvilling**. Varje IoT-hubb exponerar en uppsättning tjänstinriktade HTTPS REST-slutpunkt för att fråga och uppdatera [enhetstvillingar](iot-hub-devguide-device-twins.md) (uppdateringstaggar och egenskaper).

* **Jobbhantering**. Varje IoT-hubb exponerar en uppsättning tjänstinriktade HTTPS REST-slutpunkt för att fråga och hantera [jobb](iot-hub-devguide-jobs.md).

* **Enhetsslutpunkter**. För varje enhet i identitetsregistret exponerar IoT Hub en uppsättning slutpunkter:

  * *Skicka meddelanden från enhet till moln*. En enhet använder den här slutpunkten för att [skicka meddelanden från enhet till moln](iot-hub-devguide-messages-d2c.md).

  * *Ta emot meddelanden från molnet till enheten*. En enhet använder den här slutpunkten för att ta emot riktade [meddelanden från molnet till enheten](iot-hub-devguide-messages-c2d.md).

  * *Initiera filuppladdningar*. En enhet använder den här slutpunkten för att ta emot en Azure Storage SAS URI från IoT Hub för att [ladda upp en fil](iot-hub-devguide-file-upload.md).

  * *Hämta och uppdatera enhetstvillingegenskaper*. En enhet använder den här slutpunkten för att komma åt dess egenskaper för [enhetstvillingen.](iot-hub-devguide-device-twins.md)

  * *Ta emot direkt metodbegäranden*. En enhet använder den här slutpunkten för att lyssna efter [direktmetodens](iot-hub-devguide-direct-methods.md)begäranden.

    Dessa slutpunkter visas med [MQTT v3.1.1-](https://mqtt.org/)HTTPS 1.1- och [AMQP 1.0-protokoll.](https://www.amqp.org/) AMQP finns även över [WebSockets](https://tools.ietf.org/html/rfc6455) på port 443.

* **Tjänstens slutpunkter**. Varje IoT-hubb visar en uppsättning slutpunkter för att lösningens baksida ska kunna kommunicera med dina enheter. Med ett undantag visas dessa slutpunkter endast med [AMQP-protokollet.](https://www.amqp.org/) Metodutkallningsslutpunkten exponeras över HTTPS-protokollet.
  
  * *Ta emot meddelanden från enhet till moln*. Den här slutpunkten är kompatibel med [Azure Event Hubs](https://azure.microsoft.com/documentation/services/event-hubs/). En backend-tjänst kan använda den för att läsa de meddelanden som skickas från dina enheter [från enhet till moln.](iot-hub-devguide-messages-d2c.md) Du kan skapa anpassade slutpunkter på din IoT-hubb utöver den här inbyggda slutpunkten.
  
  * *Skicka meddelanden från molnet till enheten och ta emot leveransbekräftelser*. Dessa slutpunkter gör det möjligt för lösningens serverdel att skicka tillförlitliga [meddelanden från molnet till enheten](iot-hub-devguide-messages-c2d.md)och för att ta emot motsvarande leverans- eller förfallodatumbekräftelser.
  
  * *Ta emot filmeddelanden*. Med den här slutpunkten för meddelanden kan du ta emot meddelanden om när dina enheter har laddat upp en fil. 
  
  * *Direkt metodtrop .* Med den här slutpunkten kan en backend-tjänst anropa en [direkt metod](iot-hub-devguide-direct-methods.md) på en enhet.
  
  * *Ta emot åtgärder som övervakar händelser*. Med den här slutpunkten kan du ta emot åtgärder som övervakar händelser om din IoT-hubb har konfigurerats för att avge dem. Mer information finns i [övervakning av IoT Hub-åtgärder](iot-hub-operations-monitoring.md).

I artikeln [Azure IoT SDK:er](iot-hub-devguide-sdks.md) beskriver de olika sätten att komma åt dessa slutpunkter.

Alla IoT Hub-slutpunkter använder [TLS-protokollet](https://tools.ietf.org/html/rfc5246) och ingen slutpunkt exponeras någonsin på okrypterade/osäkra kanaler.

## <a name="custom-endpoints"></a>Anpassade slutpunkter

Du kan länka befintliga Azure-tjänster i din prenumeration till din IoT-hubb för att fungera som slutpunkter för meddelanderoutning. Dessa slutpunkter fungerar som tjänstslutpunkter och används som mottagare för meddelandevägar. Enheter kan inte skriva direkt till ytterligare slutpunkter. Läs mer om [meddelanderoutning](../iot-hub/iot-hub-devguide-messages-d2c.md).

IoT Hub stöder för närvarande följande Azure-tjänster som ytterligare slutpunkter:

* Azure Storage-behållare
* Händelsehubbar
* Service Bus-köer
* Avsnitt om Service Bus

För begränsningar för antalet slutpunkter som du kan lägga till finns [i Kvoter och begränsning](iot-hub-devguide-quotas-throttling.md).

Du kan använda REST API [Get Endpoint Health](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) för att få hälsostatus för slutpunkterna. Vi rekommenderar att du använder [IoT Hub-måtten](iot-hub-metrics.md) som är relaterade till routningsmeddelandesvarstid för att identifiera och felsöka fel när slutpunktshälsan är död eller felaktig, eftersom vi förväntar oss att svarstiden är högre när slutpunkten är i ett av dessa tillstånd.

|Hälsostatus|Beskrivning|
|---|---|
|Friska|Slutpunkten accepterar meddelanden som förväntat.|
|Ohälsosamma|Slutpunkten accepterar inte meddelanden som förväntat och IoT Hub försöker skicka data till den här slutpunkten. Statusen för en felaktig slutpunkt uppdateras till felfri när IoT Hub har etablerat ett så småningom konsekvent hälsotillstånd.|
|okänd|IoT Hub har inte upprättat någon anslutning till slutpunkten. Inga meddelanden har levererats till eller avvisats från den här slutpunkten.|
|Döda|Slutpunkten accepterar inte meddelanden, efter att IoT Hub har skickat meddelanden för den nya perioden.|

## <a name="field-gateways"></a>Fältgateways

I en IoT-lösning finns en *fältgateway* mellan dina enheter och dina IoT Hub-slutpunkter. Den finns vanligtvis nära dina enheter. Dina enheter kommunicerar direkt med fältgatewayen med hjälp av ett protokoll som stöds av enheterna. Fältgatewayen ansluter till en IoT Hub-slutpunkt med hjälp av ett protokoll som stöds av IoT Hub. En fältgateway kan vara en dedikerad maskinvaruenhet eller en dator med låg effekt som kör anpassad gatewayprogramvara.

Du kan använda [Azure IoT Edge](/azure/iot-edge/) för att implementera en fältgateway. IoT Edge erbjuder funktioner som multiplexeringskommunikation från flera enheter till samma IoT Hub-anslutning.

## <a name="next-steps"></a>Nästa steg

Andra referensavsnitt i den här utvecklarhandboken för IoT Hub är:

* [IoT Hub-frågespråk för enhetstvillingar, jobb och meddelanderoutning](iot-hub-devguide-query-language.md)
* [Kvoter och begränsningar](iot-hub-devguide-quotas-throttling.md)
* [Support för IoT Hub MQTT](iot-hub-mqtt-support.md)
* [Förstå din IP-adress för IoT-hubb](iot-hub-understand-ip-address.md)