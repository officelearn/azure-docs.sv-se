---
title: Översikt över Azure IoT Hub meddelande enrichments
description: Översikt över meddelande enrichments för Azure IoT Hub-meddelanden
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: robinsh
ms.openlocfilehash: 2de945d2ef1d87b47ea81aac034916466d3c9bda
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66258856"
---
# <a name="message-enrichments-for-device-to-cloud-iot-hub-messages-preview"></a>Meddelandet enrichments för meddelanden från enhet till moln IoT Hub (förhandsversion)

*Meddelandet enrichments* möjligt för IoT-hubben till *stämpel* meddelanden med ytterligare information innan meddelandena skickas till den angivna slutpunkten. Ett skäl till att använda meddelande enrichments är att ta med data som kan användas för att förenkla bearbetningen nedströms. Till exempel kan berikas enhetsmeddelanden telemetri med en twin enhetstagg minska belastningen på kunder för att se enhetstvillingen API-anrop för den här informationen.

![Meddelandet enrichments flöde](./media/iot-hub-message-enrichments-overview/message-enrichments-flow.png)

Ett meddelande berikande har tre viktiga element:

* Berikande namn eller nyckel

* Ett värde

* En eller flera [slutpunkter](iot-hub-devguide-endpoints.md) för vilket funktioner som ska användas.

Nyckeln kan vara valfri sträng.

Värdet kan vara något av följande exempel:

* En statisk sträng. Dynamiska värden, till exempel villkor, logik, åtgärder och funktioner är inte tillåtna. Om du utvecklar ett SaaS-program som används av flera kunder, kan du tilldela en identifierare för varje kund och göra den identifieraren som är tillgängliga i programmet. När programmet körs, kommer IoT Hub stämpel enheten telemetrimeddelanden med kundens ID, vilket gör det möjligt att bearbeta meddelanden på olika sätt för varje kund.

* Information från enhetstvillingen, till exempel dess sökväg. Exempel skulle kunna vara *$twin.tags.field* och *$twin.tags.latitude*.

* Namnet på IoT-hubben som meddelandet skickades. Det här värdet är *$iothubname*.

## <a name="applying-enrichments"></a>Tillämpa enrichments

Meddelanden kan komma från alla datakällor som stöds av [IoT Hub meddelanderoutning](iot-hub-devguide-messages-d2c.md), inklusive i följande exempel:

* enhetstelemetri, till exempel temperatur eller tryck
* enheten twin ändringsmeddelanden – ändringar i enhetstvillingen
* enhetens livscykel händelser, t.ex när enheten skapas eller tas bort

Du kan lägga till enrichments meddelanden som kommer till den inbyggda slutpunkten för en IoT-hubb eller meddelanden som dirigeras till anpassade slutpunkter som Azure Blob storage, Service Bus-kö eller ett Service Bus-ämne.

Enrichments tillämpas per slutpunkt. Om du anger fem enrichments stämplas för en viss slutpunkt för är alla meddelanden som skickas till denna slutpunkt stämplad med samma fem enrichments.

Om du vill se hur du kan prova att använda meddelande enrichments kan se den [meddelande enrichments självstudien](tutorial-message-enrichments.md)

## <a name="limitations"></a>Begränsningar

* Du kan lägga till upp till 10 enrichments per IoT Hub för dessa hubbar på standard eller basic-nivån. Du kan lägga till upp till 2 enrichments för IoT-hubbar i den kostnadsfria nivån.

* I vissa fall, om du tillämpar en berikande med ett värde som angetts till en tagg eller egenskap i enhetstvillingen, kommer värdet stämplas som ett strängvärde. Till exempel om ett berikande värde har angetts till $twin.tags.field kommer meddelanden stämplas med strängen ”$twin.tags.field” i stället för värdet för fältet från läsningen. Detta sker i följande fall:

   * IoT-hubben är på nivån basic. Basic-nivån IoT-hubbar stöder inte enhetstvillingar.

   * IoT-hubben är på standard-nivån, men den enhet som skickar meddelandet har inga enhetstvillingen.

   * IoT-hubben är på standard-nivån, men enheten twin sökvägen som används för värdet för berikande finns inte. Till exempel om berikande-värdet är inställt på $twin.tags.location och enhetstvillingen har inte någon plats-egenskap under taggar, är meddelandet stämplad med strängen ”$twin.tags.location”. 

* Uppdateringar av en enhetstvilling kan ta upp till fem minuter återspeglas i motsvarande berikande värde.

* Totalt antal meddelandelagringsstorlek inklusive enrichments, får inte överskrida 256 KB. Om en meddelandestorlek överskrider 256 KB, att IoT-hubben minska meddelandet. Du kan använda [IoT Hub mått](iot-hub-metrics.md) att identifiera och felsöka fel när meddelanden tas bort. Du kan till exempel övervaka d2c.telemetry.egress.invalid.

## <a name="pricing"></a>Prissättning

Meddelandet enrichments är tillgängliga för utan extra kostnad. Du debiteras för närvarande när du skickar ett meddelande till en IoT-hubb. Du debiteras bara en gång för meddelandet, även om meddelandet går till flera slutpunkter.

## <a name="availability"></a>Tillgänglighet

Den här funktionen är tillgänglig som förhandsversion och är tillgänglig i alla regioner utom östra USA, västra USA, Västeuropa, [Azure Government](/azure-government/documentation-government-welcome.md), [Azure Kina 21Vianet](/azure/china/china-welcome.md), och [Azure Germany](https://azure.microsoft.com/global-infrastructure/germany/).

## <a name="next-steps"></a>Nästa steg

Kolla in de här artiklarna för mer information om överföring av meddelanden till IoT Hub:

* [Använd IoT Hub meddelanderoutning för att skicka enhet-till-moln-meddelanden till olika slutpunkter](iot-hub-devguide-messages-d2c.md)

* [Självstudie: IoT Hub-Routning](tutorial-routing.md)