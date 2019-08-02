---
title: Förstå Azure IoT Hub-slutpunkter | Microsoft Docs
description: Guide för utvecklare – referens information om IoT Hub enhets-och slut punkter som riktas mot slutanvändare.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: 689a702863dda53870f775bd8520d5dd406d242f
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68640597"
---
# <a name="reference---iot-hub-endpoints"></a>Referens – IoT Hub slut punkter

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="iot-hub-names"></a>IoT Hub namn

Du kan hitta värd namnet för IoT-hubben som är värd för dina slut punkter i portalen på din Hubbs översikts sida. DNS-namnet för en IoT-hubb ser som standard ut så `{your iot hub name}.azure-devices.net`här:.

## <a name="list-of-built-in-iot-hub-endpoints"></a>Lista över inbyggda IoT Hub slut punkter

Azure IoT Hub är en tjänst för flera innehavare som visar sina funktioner för olika aktörer. Följande diagram visar de olika slut punkter som IoT Hub visar.

![IoT Hub-slutpunkter](./media/iot-hub-devguide-endpoints/endpoints.png)

I följande lista beskrivs slut punkterna:

* **Resurs leverantör**. IoT Hub resurs leverantören exponerar ett [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) -gränssnitt. Det här gränssnittet gör det möjligt för Azure-prenumerations ägare att skapa och ta bort IoT-hubbar och uppdatera IoT Hub-egenskaper. IoT Hub egenskaper styr [säkerhets principer](iot-hub-devguide-security.md#access-control-and-permissions)på hubbnivå, i stället för åtkomst kontroll på enhets nivå, och funktionella alternativ för meddelande hantering från moln till enhet och enhet till moln. Med IoT Hub Resource Provider kan du också [Exportera enhets identiteter](iot-hub-devguide-identity-registry.md#import-and-export-device-identities).

* **Hantering av enhets identitet**. Varje IoT-hubb exponerar en uppsättning HTTPS REST-slutpunkter för att hantera enhets identiteter (skapa, Hämta, uppdatera och ta bort). [Enhets identiteter](iot-hub-devguide-identity-registry.md) används för enhetsautentisering och åtkomst kontroll.

* **Enhetens dubbla hantering**. Varje IoT-hubb exponerar en uppsättning service Facing HTTPS REST-slutpunkt för att fråga och uppdatera [enhetens dubbla](iot-hub-devguide-device-twins.md) (uppdatera Taggar och egenskaper).

* **Jobb hantering**. Varje IoT-hubb exponerar en uppsättning service Facing HTTPS REST-slutpunkt för att fråga och hantera [jobb](iot-hub-devguide-jobs.md).

* **Enhets slut punkter**. För varje enhet i identitets registret visar IoT Hub en uppsättning slut punkter:

  * *Skicka meddelanden från enheten till molnet*. En enhet använder slut punkten för att [skicka meddelanden från enheten till molnet](iot-hub-devguide-messages-d2c.md).

  * *Ta emot meddelanden från molnet till enheten*. En enhet använder slut punkten för att ta emot riktade [meddelanden från molnet till enheten](iot-hub-devguide-messages-c2d.md).

  * *Initiera fil överföringar*. En enhet använder den här slut punkten för att ta emot en Azure Storage SAS-URI från IoT Hub för att [Ladda upp en fil](iot-hub-devguide-file-upload.md).

  * *Hämta och uppdatera enhetens dubbla egenskaper*. En enhet använder den här slut punkten för att få åtkomst till [enhetens dubbla](iot-hub-devguide-device-twins.md)egenskaper.

  * *Ta emot begär Anden om direkta metoder*. En enhet använder slut punkten för att lyssna efter [direkta metod](iot-hub-devguide-direct-methods.md)begär Anden.

    Dessa slut punkter exponeras med hjälp av [MQTT v 3.1.1](https://mqtt.org/)-, https 1,1-och [AMQP 1,0](https://www.amqp.org/) -protokoll. AMQP är också tillgängligt via [](https://tools.ietf.org/html/rfc6455) WebSockets på port 443.

* **Tjänst slut punkter**. Varje IoT-hubb exponerar en uppsättning slut punkter för lösningens Server del för att kommunicera med dina enheter. Med ett undantag exponeras de här slut punkterna endast med [AMQP](https://www.amqp.org/) -protokollet. Slut punkten för metod anropet exponeras via HTTPS-protokollet.
  
  * *Ta emot meddelanden från enheten till molnet*. Den här slut punkten är kompatibel med [Azure Event Hubs](https://azure.microsoft.com/documentation/services/event-hubs/). En backend-tjänst kan använda den för att läsa [meddelanden från enheten till molnet](iot-hub-devguide-messages-d2c.md) som skickas av dina enheter. Du kan skapa anpassade slut punkter i IoT Hub förutom den här inbyggda slut punkten.
  
  * *Skicka meddelanden från moln till enhet och få leverans bekräftelser*. Med de här slut punkterna kan Server delen av lösningen skicka Reliable [-meddelanden](iot-hub-devguide-messages-c2d.md)från molnet till enheten och ta emot motsvarande leverans-eller förfallo bekräftelser.
  
  * *Ta emot fil meddelanden*. Med den här meddelande slut punkten kan du få meddelanden om när enheterna har laddat upp en fil. 
  
  * *Direkt metod anrop*. Den här slut punkten gör det möjligt för en server dels tjänst att anropa en [direkt metod](iot-hub-devguide-direct-methods.md) på en enhet.
  
  * *Ta emot övervaknings händelser för åtgärder*. Med den här slut punkten kan du ta emot övervaknings händelser för operationer om din IoT Hub har kon figurer ATS för att generera dem. Mer information finns i [IoT Hub Operations Monitoring](iot-hub-operations-monitoring.md).

Artikeln om [Azure IoT-SDK](iot-hub-devguide-sdks.md) : er beskriver de olika sätten att komma åt dessa slut punkter.

Alla IoT Hub-slutpunkter använder [TLS](https://tools.ietf.org/html/rfc5246) -protokollet och ingen slut punkt exponeras aldrig på okrypterade/oskyddade kanaler.

## <a name="custom-endpoints"></a>Anpassade slutpunkter

Du kan länka befintliga Azure-tjänster i din prenumeration till din IoT Hub för att agera som slut punkter för meddelanderoutning. Dessa slut punkter fungerar som tjänst slut punkter och används som mottagare för meddelande vägar. Enheter kan inte skriva direkt till de ytterligare slut punkterna. Lär dig mer [om meddelanderoutning](../iot-hub/iot-hub-devguide-messages-d2c.md).

IoT Hub stöder för närvarande följande Azure-tjänster som ytterligare slut punkter:

* Azure Storage-containrar
* Event Hubs
* Service Bus-köer
* Avsnitt om Service Bus

Begränsningarna för antalet slut punkter som du kan lägga till finns i [kvoter och begränsning](iot-hub-devguide-quotas-throttling.md).

Du kan använda REST API [Hämta slut punkts hälsa](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) för att få hälso status för slut punkterna. Vi rekommenderar att du använder [IoT Hub mått](iot-hub-metrics.md) som rör svars tiden för routning av meddelanden för att identifiera och felsöka fel när slut punktens hälso tillstånd är död eller ohälsosamt, eftersom fördröjningen blir högre när slut punkten är i något av dessa tillstånd.

|Hälsostatus|Beskrivning|
|---|---|
|healthy|Slut punkten accepterar meddelanden som förväntat.|
|fel tillstånd|Slut punkten accepterar inte meddelanden som förväntat och IoT Hub försöker skicka data till den här slut punkten igen. Statusen för en felaktig slut punkt kommer att uppdateras till felfri när IoT Hub har upprättat en konsekvent hälso status.|
|okänt|IoT Hub har inte upprättat en anslutning till slut punkten. Inga meddelanden har levererats till eller avvisats från den här slut punkten.|
|meddelanden|Slut punkten accepterar inte meddelanden, efter att IoT Hub försökt att skicka meddelanden för utvärderings perioden.|

## <a name="field-gateways"></a>Fält-gatewayer

I en IoT-lösning är en *fält-Gateway* placerad mellan dina enheter och dina IoT Hub slut punkter. Den finns vanligt vis nära dina enheter. Enheterna kommunicerar direkt med fält-gatewayen med hjälp av ett protokoll som stöds av enheterna. Fält-gatewayen ansluter till en IoT Hub-slutpunkt med ett protokoll som stöds av IoT Hub. En fält-Gateway kan vara en dedikerad maskin varu enhet eller en dator med låg strömförbrukning som kör anpassad Gateway-programvara.

Du kan använda [Azure IoT Edge](/azure/iot-edge/) för att implementera en fält-Gateway. IoT Edge erbjuder funktioner som multiplexering av kommunikation från flera enheter till samma IoT Hub anslutning.

## <a name="next-steps"></a>Nästa steg

Andra referens ämnen i den här IoT Hub Developer Guide är:

* [IoT Hub frågespråk för enhets dubbla, jobb och meddelanderoutning](iot-hub-devguide-query-language.md)
* [Kvoter och begränsningar](iot-hub-devguide-quotas-throttling.md)
* [IoT Hub MQTT-stöd](iot-hub-mqtt-support.md)
* [Förstå IP-adressen för IoT Hub](iot-hub-understand-ip-address.md)