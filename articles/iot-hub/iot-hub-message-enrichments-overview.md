---
title: Översikt över Azure IoT Hub-meddelandets anrikning
description: Översikt över meddelande anrikninger för Azure IoT Hub-meddelanden
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: robinsh
ms.openlocfilehash: 40ce5c4f73daac2b88d3a59e46d43dba10db0954
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177999"
---
# <a name="message-enrichments-for-device-to-cloud-iot-hub-messages-preview"></a>Meddelande anrikning för IoT Hub-meddelanden från enhet till moln (för hands version)

*Meddelande anrikning* är möjligheten för IoT Hub att *stämpla* meddelanden med ytterligare information innan meddelandena skickas till den angivna slut punkten. En anledning till att använda meddelande berikare är att inkludera data som kan användas för att förenkla bearbetningen i den underordnade bearbetningen. För att till exempel kunna identifiera enhets telemetri med en enhets rik tagg kan kunderna minska belastningen på kunder för att göra enhetens dubbla API-anrop för den här informationen.

![Flöde för meddelande anrikning](./media/iot-hub-message-enrichments-overview/message-enrichments-flow.png)

En meddelande anrikning har tre viktiga element:

* Namn eller nyckel för anrikning

* Ett värde

* En eller flera [slut punkter](iot-hub-devguide-endpoints.md) som berikning ska användas för.

**Nyckeln** är en sträng. En nyckel får bara innehålla alfanumeriska tecken eller följande specialtecken: bindestreck (`-`), under streck (`_`) och punkt (`.`).

**Värdet** kan vara något av följande exempel:

* En statisk sträng. Dynamiska värden som villkor, logik, åtgärder och funktioner är inte tillåtna. Om du t. ex. utvecklar ett SaaS-program som används av flera kunder, kan du tilldela en identifierare till varje kund och göra den identifieraren tillgänglig i programmet. När programmet körs stämplar IoT Hub enheten telemetri-meddelanden med kundens identifierare, vilket gör det möjligt att bearbeta meddelandena på olika sätt för varje kund.

* Namnet på IoT-hubben som skickar meddelandet. Det här värdet är *$iothubname*.

* Information från enheten, till exempel dess sökväg. Exempel är *$Twin. taggar. Field* och *$Twin. Tags. latitud*.

   > [!NOTE]
   > För närvarande är det bara $iothubname, $twin. taggar, $twin. Properties. Reporting, och $twin. Properties. rapporterade variabler som stöds för meddelande anrikning.

Meddelande berikare läggs till som program egenskaper till meddelanden som skickas till valda slut punkter.  

## <a name="applying-enrichments"></a>Använda omfattande funktioner

Meddelandena kan komma från alla data källor som stöds av [IoT Hub](iot-hub-devguide-messages-d2c.md)meddelanderoutning, inklusive följande exempel:

* telemetri för enheter, till exempel temperatur eller tryck
* enhets dubbla ändrings meddelanden – förändringar i enheten, dubbla
* enhetens livs cykel händelser, till exempel när enheten har skapats eller tagits bort

Du kan lägga till anrikninger i meddelanden som kommer till den inbyggda slut punkten för en IoT Hub eller meddelanden som dirigeras till anpassade slut punkter som Azure Blob Storage, en Service Bus kö eller ett Service Bus ämne.

Du kan också lägga till anrikninger i meddelanden som publiceras till Event Grid genom att välja slut punkten som Event Grid. Mer information finns i [IoT Hub och event Grid](iot-hub-event-grid.md).

Anrikninger tillämpas per slut punkt. Om du anger att fem berikningar ska stämplas för en speciell slut punkt, stämplas alla meddelanden som skickas till den slut punkten med samma fem-berikare.

Information om hur du provar meddelande förvarningar finns i [själv studie kursen om meddelande förhands rik](tutorial-message-enrichments.md) information

## <a name="limitations"></a>Begränsningar

* Du kan lägga till upp till 10-rika steg per IoT Hub för dessa hubbar på standard-eller Basic-nivån. För IoT-hubbar på den kostnads fria nivån kan du lägga till upp till 2-berikningar.

* I vissa fall, om du tillämpar en anrikning med ett värde som är inställt på en tagg eller egenskap i enheten, så stämplas värdet som ett sträng värde. Om till exempel ett anriknings värde är inställt på $twin. Tagss. Field, kommer meddelandena att stämplas med strängen "$twin. Tagss. Field" i stället för värdet i fältet från den dubbla. Detta inträffar i följande fall:

   * Din IoT Hub finns på Basic-nivån. IoT-hubbar på Basic-nivå har inte stöd för enhets dubbla.

   * Din IoT Hub är i standard-nivån, men enheten som skickar meddelandet har ingen enhets nummer.

   * Din IoT Hub är på standard-nivån, men den enhets dubbla sökvägen som används för värdet för anrikningen finns inte. Om till exempel värdet för anrikning är inställt på $twin. taggar. location, och enheten med dubbla inte har egenskapen location under Taggar, så stämplas meddelandet med strängen "$twin. Tags. location". 

* Det kan ta upp till fem minuter innan uppdateringar av enheten visas i motsvarande anriknings värde.

* Den totala meddelande storleken, inklusive berikarna, får inte överstiga 256 KB. Om en meddelande storlek överskrider 256 KB ignoreras meddelandet av IoT Hub. Du kan använda [IoT Hub mått](iot-hub-metrics.md) för att identifiera och felsöka fel när meddelanden släpps. Du kan till exempel övervaka D2C. telemetri. utgående. ogiltig.

* Meddelande anrikninger gäller inte för digitala dubbla ändrings händelser (en del av [IoT plug and Play offentlig för hands version](../iot-pnp/overview-iot-plug-and-play.md)).

## <a name="pricing"></a>Prissättning

Meddelande anrikninger är tillgängliga utan extra kostnad. För närvarande debiteras du när du skickar ett meddelande till en IoT Hub. Du debiteras bara en gång för det meddelandet, även om meddelandet går till flera slut punkter.

## <a name="availability"></a>Tillgänglighet

Den här funktionen är tillgänglig i för hands version och är tillgänglig i alla regioner utom östra USA, västra USA, Västeuropa, [Azure Government](/azure/azure-government/documentation-government-welcome), [Azure Kina 21Vianet](/azure/china)och [Azure Germany](https://azure.microsoft.com/global-infrastructure/germany/).

## <a name="next-steps"></a>Nästa steg

Ta en titt på de här artiklarna om du vill ha mer information om routning av meddelanden till en IoT Hub:

* [Själv studie kurs om meddelande anrikning](tutorial-message-enrichments.md)

* [Använd IoT Hub meddelanderoutning för att skicka meddelanden från enheten till molnet till olika slut punkter](iot-hub-devguide-messages-d2c.md)

* [Självstudiekurs: IoT Hub routning @ no__t-0
