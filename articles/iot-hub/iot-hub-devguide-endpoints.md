---
title: Förstå Azure IoT Hub-slutpunkter | Microsoft Docs
description: Utvecklarguide – information om IoT Hub enheten kund- och service-riktade slutpunkter.
author: robinsh
manager: philmea
ms.author: robin.shahan
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/18/2018
ms.openlocfilehash: 085a4ffbe0b615408bfd8aa70c027013e16f0136
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58201440"
---
# <a name="reference---iot-hub-endpoints"></a>Referens – IoT Hub-slutpunkter

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="iot-hub-names"></a>IoT Hub-namn

Du kan hitta värdnamnet för IoT-hubben som är värd för dina slutpunkter i portalen på din hubb **översikt** sidan. Som standard är DNS-namnet på en IoT-hubb ser ut som: `{your iot hub name}.azure-devices.net`.

## <a name="list-of-built-in-iot-hub-endpoints"></a>Lista över inbyggda IoT Hub-slutpunkter

Azure IoT Hub är en tjänst för flera innehavare som innehåller dess funktioner till olika aktörer. I följande diagram visas de olika slutpunkterna exponerar IoT Hub.

![IoT Hub-slutpunkter](./media/iot-hub-devguide-endpoints/endpoints.png)

I följande lista beskrivs slutpunkterna:

* **Resursprovidern**. Resursprovidern IoT Hub Exponerar en [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) gränssnitt. Det här gränssnittet gör det möjligt för Azure-prenumerationsägare att skapa och ta bort IoT-hubbar och för att uppdatera egenskaper för IoT hub. IoT Hub egenskaper styr [principer för säkerhet på radnivå hub](iot-hub-devguide-security.md#access-control-and-permissions), till skillnad från på enhetsnivå access control och funktionella alternativ för moln-till-enhet och enhet till moln-meddelanden. Resursprovidern IoT Hub kan du också [exportera enhetsidentiteter](iot-hub-devguide-identity-registry.md#import-and-export-device-identities).

* **Identitetshantering för enheten**. Varje IoT-hubb Exponerar en uppsättning HTTPS REST-slutpunkter för att hantera enhetsidentiteter (skapa, hämta, uppdatera och ta bort). [Enhetsidentiteter](iot-hub-devguide-identity-registry.md) används för autentisering och åtkomstkontroll för enheten.

* **Enhetshantering twin**. Varje IoT-hubb innehåller en rad för tjänst för webbservergrupper på HTTPS-REST-slutpunkt för fråge- och [enhetstvillingar](iot-hub-devguide-device-twins.md) (uppdatering taggar och egenskaper).

* **Jobb management**. Varje IoT-hubb Exponerar en uppsättning service webbservergrupper HTTPS REST-slutpunkt fråga och hantera [jobb](iot-hub-devguide-jobs.md).

* **Enheten slutpunkter**. För varje enhet i identitetsregistret exponerar IoT-hubb en uppsättning slutpunkter:

  * *Skicka meddelanden från enheten till molnet*. En enhet använder den här slutpunkten till [skicka meddelanden från enheten till molnet](iot-hub-devguide-messages-d2c.md).

  * *Ta emot meddelanden från moln till enhet*. En enhet använder den här slutpunkten för att ta emot riktade [meddelanden från moln till enhet](iot-hub-devguide-messages-c2d.md).

  * *Initierar filöverföringar*. En enhet använder den här slutpunkten för att ta emot ett Azure Storage SAS-URI från IoT Hub och [ladda upp en fil](iot-hub-devguide-file-upload.md).

  * *Hämta och uppdatera tvillingegenskaper*. En enhet använder den här slutpunkten för att få åtkomst till dess [enhetstvillingen](iot-hub-devguide-device-twins.md)'s egenskaper.

  * *Ta emot förfrågningar för direkt metod*. En enhet använder den här slutpunkten för att lyssna efter [direktmetod](iot-hub-devguide-direct-methods.md)'s begäranden.

    De här slutpunkterna blir tillgängliga via [MQTT v3.1.1](https://mqtt.org/), HTTPS 1.1 och [AMQP 1.0](https://www.amqp.org/) protokoll. AMQP är också tillgänglig via [WebSockets](https://tools.ietf.org/html/rfc6455) på port 443.

* **Tjänstslutpunkter**. Varje IoT-hubb Exponerar en uppsättning slutpunkter för lösningens backend-servrar att kommunicera med dina enheter. Med ett undantag slutpunkterna är bara tillgängliga via den [AMQP](https://www.amqp.org/) protokoll. Metoden anrop slutpunkten exponeras via HTTPS-protokollet.
  
  * *Ta emot meddelanden från enheten till molnet*. Den här slutpunkten är kompatibel med [Azure Event Hubs](https://azure.microsoft.com/documentation/services/event-hubs/). En backend tjänst kan använda den för att läsa den [meddelanden från enheten till molnet](iot-hub-devguide-messages-d2c.md) skickas av dina enheter. Du kan skapa anpassade slutpunkter i IoT-hubben förutom den här inbyggda slutpunkten.
  
  * *Skicka meddelanden från moln till enhet och ta emot bekräftelser för leverans*. De här slutpunkterna aktivera lösningens backend-servrar att skicka tillförlitlig [meddelanden från moln till enhet](iot-hub-devguide-messages-c2d.md), samt för att få motsvarande leverans- eller förfallodatum bekräftelser.
  
  * *Ta emot meddelanden i filen*. Den här slutpunkten för meddelanden kan du ta emot meddelanden om när dina enheter har överför en fil. 
  
  * *Dirigera metodanropet*. Den här slutpunkten kan en backend-tjänst för att anropa en [direktmetod](iot-hub-devguide-direct-methods.md) på en enhet.
  
  * *Ta emot händelser för åtgärdsövervakning*. Den här slutpunkten kan du ta emot händelseövervakning om IoT-hubben har konfigurerats för att generera dem. Mer information finns i [IoT Hub åtgärdsövervakning](iot-hub-operations-monitoring.md).

Den [Azure IoT SDK: er](iot-hub-devguide-sdks.md) artikeln beskrivs olika sätt att få åtkomst till dessa slutpunkter.

Alla slutpunkter i IoT Hub använder den [TLS](https://tools.ietf.org/html/rfc5246) protokollet och ingen slutpunkt exponeras någonsin via okrypterade/oskyddad kanaler.

## <a name="custom-endpoints"></a>Anpassade slutpunkter

Du kan länka befintliga Azure-tjänster i din prenumeration till din IoT hub så att den fungerar som slutpunkter för meddelanderoutning. De här slutpunkterna fungerar som slutpunkter och används som mottagare för meddelandevägar. Enheter kan inte skrivas direkt till ytterligare slutpunkter. Läs mer om [meddelanderoutning](../iot-hub/iot-hub-devguide-messages-d2c.md).

IoT Hub stöder för närvarande följande Azure-tjänster som ytterligare slutpunkter:

* Azure Storage-containrar
* Event Hubs
* Service Bus-köer
* Avsnitt om Service Bus

Gränser för antalet slutpunkter som du kan lägga till, se [kvoter och begränsningar](iot-hub-devguide-quotas-throttling.md).

Du kan använda REST-API [hämta Slutpunktshälsa](https://docs.microsoft.com/de-de/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) att hämta hälsostatus för slutpunkter. Vi rekommenderar att du använder den [IoT Hub mått](iot-hub-metrics.md) rör Routning meddelande svarstid för att identifiera och felsöka fel när slutpunktshälsa är döda eller är skadad.

|Hälsostatus|Beskrivning|
|---|---|
|felfri|Slutpunkten som tar emot meddelanden som förväntat.|
|Feltillstånd|Slutpunkten tar inte emot meddelanden som förväntat och IoT-hubben försöker på nytt för att skicka data till den här slutpunkten. Status för en defekt slutpunkt uppdateras till felfritt läge när IoT Hub har etablerat en konsekvent hälsotillstånd.|
|okänt|IoT Hub har inte upprätta en anslutning med slutpunkten. Inga meddelanden har levereras till eller avvisas från den här slutpunkten.|
|dead|Slutpunkten är inte ta emot meddelanden, efter att IoT Hub göras skicka meddelanden under retrial.|

## <a name="field-gateways"></a>Fält-gateways

I en IoT-lösning kan en *fältgateway* är placerad mellan dina enheter och IoT Hub-slutpunkter. Det finns normalt nära dina enheter. Dina enheter kommunicerar direkt med fält-gateway med hjälp av ett protokoll som stöds av enheter. Fält-gateway ansluter till en IoT Hub-slutpunkt med ett protokoll som stöds av IoT Hub. En fält-gateway kan vara en särskild maskinvaruenhet eller en låg-dator som kör anpassade gateway-programvaran.

Du kan använda [Azure IoT Edge](/azure/iot-edge/) att implementera en fält-gateway. IoT Edge erbjuder funktioner som multiplexering kommunikation från flera enheter på samma IoT Hub-anslutning.

## <a name="next-steps"></a>Nästa steg

Andra referensavsnitten i det här utvecklarhandboken för IoT Hub är:

* [IoT Hub-frågespråk för enhetstvillingar, jobb och meddelanderoutning](iot-hub-devguide-query-language.md)
* [Kvoter och begränsningar](iot-hub-devguide-quotas-throttling.md)
* [IoT Hub MQTT-support](iot-hub-mqtt-support.md)
