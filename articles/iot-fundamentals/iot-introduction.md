---
title: Introduktion till Sakernas Internet (IoT) i Azure
description: Introduktionen förklarar grunderna i Azure IoT och IoT-tjänster, och innehåller exempel som kan illustrera användningen av IoT.
author: dominicbetts
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: overview
ms.date: 01/15/2020
ms.author: dobett
ms.openlocfilehash: c79f18669e1b13f79491e98658107221b43f3ff5
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77046181"
---
# <a name="what-is-azure-internet-of-things-iot"></a>Vad är Azure Sakernas Internet (IoT)?

Azure Sakernas Internet (IoT) är en samling Microsoft-hanterade molntjänster som ansluter, övervakar och styr miljontals IoT-resurser. Enklare termer består en IoT-lösning av en eller flera IoT-enheter som kommunicerar med en eller flera backend-tjänster som finns i molnet. 

## <a name="iot-devices"></a>IoT-enheter

En IoT-enhet består vanligtvis av ett kretskort med sensorer anslutna som använder WiFi för att ansluta till Internet. Ett exempel:

* En trycksensor på en fjärroljepump.
* Temperatur- och fuktighetsgivare i en luftkonditioneringsenhet.
* En accelerometer i en hiss.
* Närvarosensorer i ett rum.

Det finns ett brett utbud av enheter tillgängliga från olika tillverkare för att bygga din lösning. En lista över enheter som är certifierade för att fungera med Azure IoT Hub finns i [Azure Certified for IoT-enhetskatalogen](https://catalog.azureiotsolutions.com/alldevices). För prototyper kan du använda enheter som en [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) eller en [Raspberry Pi](https://www.raspberrypi.org/). Devkit har inbyggda sensorer för temperatur, tryck, luftfuktighet och gyroskop, accelerometer och magnetometer. Raspberry Pi låter dig fästa många olika typer av sensor. 

Microsoft tillhandahåller [enhetsdYkar](../iot-hub/iot-hub-devguide-sdks.md) med öppen källkod som du kan använda för att skapa de appar som körs på dina enheter. Dessa [SDK förenklar och påskyndar](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/) utvecklingen av dina IoT-lösningar.

## <a name="communication"></a>Kommunikation

Vanligtvis skickar IoT-enheter telemetri från sensorerna till backend-tjänster i molnet. Andra typer av kommunikation är dock möjliga, till exempel en backend-tjänst som skickar kommandon till dina enheter. Följande är några exempel på kommunikation mellan enheter och molnet till enheten:

* En mobil kylbil skickar temperatur var 5:e minut till en IoT Hub. 

* Backend-tjänsten skickar ett kommando till en enhet för att ändra hur ofta den skickar telemetri för att diagnostisera ett problem. 

* En enhet skickar aviseringar baserat på de värden som läs avst läs från dess sensorer. En enhet som övervakar en batchreaktor i en kemisk anläggning skickar till exempel en varning när temperaturen överstiger ett visst värde.

* Dina enheter skickar information för att visas på en instrumentpanel för visning av mänskliga operatörer. Ett kontrollrum i ett raffinaderi kan till exempel visa temperatur-, tryck- och flödesvolymerna i varje rör, vilket gör det möjligt för operatörerna att övervaka anläggningen. 

[IoT-enhetSDK:er](../iot-hub/iot-hub-devguide-sdks.md) och IoT Hub stöder vanliga [kommunikationsprotokoll](../iot-hub/iot-hub-devguide-protocols.md) som HTTP, MQTT och AMQP.

IoT-enheter har olika egenskaper jämfört med andra klienter som webbläsare och mobilappar. Enhetens SDK:er hjälper dig att hantera utmaningarna med att ansluta enheter på ett säkert och tillförlitligt sätt till din backend-tjänst.  IoT-enheter är mer specifikt:

* Ofta inbyggda system utan mänskliga operatörer (till skillnad från en telefon).
* Kan distribueras på avlägsna platser, där fysisk åtkomst är dyr.
* Kan i vissa fall enbart nås via lösningens serverdel.
* Kan ha begränsade ström- och bearbetningsresurser.
* Kan ha oregelbunden, långsam eller dyr nätverksanslutning.
* Kan behöva använda privatägda, anpassade eller branschspecifika programprotokoll.

## <a name="back-end-services"></a>Serverdelstjänster 

I en IoT-lösning tillhandahåller backend-tjänsten funktioner som:

* Ta emot stora mängder telemetri från dina enheter och bestämma hur telemetridata bearbetas och lagras.
* Analysera telemetri för att ge insikter, oavsett om de skickas i realtid eller i efterhand.
* Skicka kommandon från molnet till en specifik enhet. 
* Etablera enheter och styra vilka enheter som kan ansluta till din infrastruktur.
* Styra tillståndet för dina enheter och övervaka deras aktiviteter.
* Hantera den inbyggda programvaran som är installerad på dina enheter.

I en fjärrövervakningslösning för en oljepumpstation använder molnbakdelen telemetri från pumparna för att identifiera avvikande beteende. När backend-tjänsten identifierar en avvikelse kan den automatiskt skicka ett kommando tillbaka till enheten för att vidta en korrigerande åtgärd. Den här processen skapar en automatisk feedback-loop mellan enheten och molnet som ökar effektiviteten för lösningen dramatiskt.

## <a name="azure-iot-examples"></a>Azure IoT-exempel

För verkliga exempel på hur organisationer använder Azure IoT finns i [Microsoft Technical Case Studies for IoT](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured). 

## <a name="next-steps"></a>Nästa steg

För verkliga affärsfall och vilken arkitektur som använts finns i avsnittet om [Microsoft Azure IoT:s tekniska fallstudier](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured).

Vissa exempelprojekt som du kan prova med en IoT DevKit finns i [IoT DevKit-katalogen](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/). 

En mer omfattande förklaring av de olika tjänsterna och hur de används finns i [Azure IoT-tjänster och -tekniker](iot-services-and-technologies.md).

En detaljerad beskrivning av IoT-arkitekturen finns i [Microsoft Azure IoT Reference Architecture](https://aka.ms/iotrefarchitecture) (Referensarkitektur för Microsoft Azure IoT).
