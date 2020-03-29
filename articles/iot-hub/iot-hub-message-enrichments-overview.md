---
title: Översikt över Azure IoT Hub-meddelandeberikande
description: Den här artikeln visar meddelandeberikanden, som ger IoT Hub möjlighet att stämpla meddelanden med ytterligare information innan meddelandena skickas till den angivna slutpunkten.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: robinsh
ms.openlocfilehash: c3dbd01faf61c164c88f09b0da03c07be4abd187
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75429112"
---
# <a name="message-enrichments-for-device-to-cloud-iot-hub-messages"></a>Meddelandeberikande för IoT Hub-meddelanden från enhet till moln

*Meddelandeberikningar* är IoT-hubbens förmåga att *stämpla* meddelanden med ytterligare information innan meddelandena skickas till den angivna slutpunkten. En anledning till att använda meddelandeberikande är att inkludera data som kan användas för att förenkla nedströmsbearbetning. Om du till exempel kan berika telekommunikationsmeddelanden för enheter med en enhetstvillingtagg minska belastningen på kunder för att ringa enhetstvilling-API-anrop för den här informationen.

![Flöde av meddelandeanrikningar](./media/iot-hub-message-enrichments-overview/message-enrichments-flow.png)

En meddelandeberikning har tre huvudelement:

* Namn eller nyckel för anrikning

* Ett värde

* En eller flera [slutpunkter](iot-hub-devguide-endpoints.md) för vilka berikningen ska tillämpas.

**Nyckeln** är en sträng. En tangent kan bara innehålla alfanumeriska tecken`-`eller följande`_`specialtecken:`.`bindestreck ( ), understreck ( ) och punkt ( ).

**Värdet** kan vara något av följande exempel:

* Alla statiska strängar. Dynamiska värden som villkor, logik, åtgärder och funktioner är inte tillåtna. Om du till exempel utvecklar ett SaaS-program som används av flera kunder kan du tilldela en identifierare till varje kund och göra identifieraren tillgänglig i programmet. När programmet körs stämplar IoT Hub enhetens telemetrimeddelanden med kundens identifierare, vilket gör det möjligt att bearbeta meddelandena på olika sätt för varje kund.

* Namnet på IoT-hubben som skickar meddelandet. Det här värdet är *$iothubname*.

* Information från enhetstvillingen, till exempel dess sökväg. Exempel på detta är *$twin.tags.field* och *$twin.tags.latitude*.

   > [!NOTE]
   > För närvarande stöds endast $iothubname, $twin.tags, $twin.properties.desired och $twin.properties.reported variabler för meddelandeanrikning.

Meddelandeanrikningar läggs till som programegenskaper i meddelanden som skickas till valda slutpunkter.  

## <a name="applying-enrichments"></a>Tillämpa berikningar

Meddelandena kan komma från alla datakällor som stöds av [IoT Hub-meddelanderoutning,](iot-hub-devguide-messages-d2c.md)inklusive följande exempel:

* telekommunikationsmetri, till exempel temperatur eller tryck
* meddelanden om ändring av enheter – ändringar i enhetstvillingen
* enhetens livscykelhändelser, till exempel när enheten skapas eller tas bort

Du kan lägga till enrichments i meddelanden som går till den inbyggda slutpunkten för en IoT-hubb eller meddelanden som dirigeras till anpassade slutpunkter som Azure Blob-lagring, en servicebusskö eller ett Service Bus-ämne.

Du kan lägga till enrichments i meddelanden som publiceras i Event Grid genom att välja slutpunkten som händelserutnät. Vi skapar en standardväg i IoT Hub till enhetstelemetri, baserat på din Event Grid-prenumeration. Den här enskilda vägen kan hantera alla dina Event Grid-prenumerationer. Du kan konfigurera enrichments för slutpunkten för händelserutnätet när du har skapat prenumerationen på händelserutnät till enhetstelemetri. Mer information finns i [Iot Hub och Event Grid](iot-hub-event-grid.md).

Enrichments används per slutpunkt. Om du anger fem anrikningar som ska stämplas för en viss slutpunkt stämplas alla meddelanden som går till den slutpunkten med samma fem enrichments.

Enrichments kan konfigureras med hjälp av följande metoder:

| **Metod** | **Kommandot** |
| ----- | -----| 
| Portalen | [Azure-portal](https://portal.azure.com) | Se [guiden för meddelandeberikning](tutorial-message-enrichments.md) | 
| Azure CLI   | [az iot hub meddelande-anrikning](https://docs.microsoft.com/cli/azure/iot/hub/message-enrichment?view=azure-cli-latest) |
| Azure PowerShell | [Add-AzIotHubMessageEnrichment](https://docs.microsoft.com/powershell/module/az.iothub/add-aziothubmessageenrichment?view=azps-2.8.0) |

Om du lägger till meddelandeberikaden ökar inte svarstiden i meddelanderoutningen.

Om du vill prova meddelandeberikanden läser du [självstudien för meddelandeberikande](tutorial-message-enrichments.md)

## <a name="limitations"></a>Begränsningar

* Du kan lägga till upp till 10 enrichments per IoT Hub för dessa nav på standard- eller grundnivå. För IoT Hubs på den kostnadsfria nivån kan du lägga till upp till 2 berikande.

* I vissa fall, om du använder en anrikning med ett värde inställt på en tagg eller egenskap i enhetstvillingen, stämplas värdet som ett strängvärde. Om till exempel ett anrikningsvärde är inställt på $twin.tags.field, stämplas meddelandena med strängen "$twin.tags.field" i stället för värdet för det fältet från tvillingen. Detta händer i följande fall:

   * Din IoT Hub är på den grundläggande nivån. Grundläggande nivå IoT-hubbar stöder inte enhetstvillingar.

   * Din IoT Hub är på standardnivån, men enheten som skickar meddelandet har ingen enhetstvilling.

   * IoT-hubben ligger på standardnivån, men enhetstvillingsökvägen som används för värdet av anrikningen finns inte. Om anrikningsvärdet till exempel är inställt på $twin.tags.location och enhetstvillingen inte har någon platsegenskap under taggar, stämplas meddelandet med strängen "$twin.tags.location". 

* Det kan ta upp till fem minuter att uppdatera uppdateringar av en enhetstvilling som ska återspeglas i motsvarande anrikningsvärde.

* Den totala meddelandestorleken, inklusive anrikningsmåtten, får inte överstiga 256 kB. Om en meddelandestorlek överstiger 256 kB släpper IoT Hub meddelandet. Du kan använda [IoT Hub-mått](iot-hub-metrics.md) för att identifiera och felsöka fel när meddelanden tas bort. Du kan till exempel övervaka d2c.telemetry.egress.invalid.

* Meddelandeberikande gäller inte för digitala dubbla ändringshändelser (en del av den offentliga förhandsversionen av [IoT Plug and Play).](../iot-pnp/overview-iot-plug-and-play.md)

## <a name="pricing"></a>Prissättning

Meddelandeberikning är tillgängliga utan extra kostnad. För närvarande debiteras du när du skickar ett meddelande till en IoT Hub. Du debiteras bara en gång för det meddelandet, även om meddelandet går till flera slutpunkter.

## <a name="next-steps"></a>Nästa steg

I de här artiklarna finns mer information om routning av meddelanden till en IoT Hub:

* [Självstudiekurs för meddelandeanrikningar](tutorial-message-enrichments.md)

* [Använda IoT Hub-meddelanderoutning för att skicka meddelanden från enhet till moln till olika slutpunkter](iot-hub-devguide-messages-d2c.md)

* [Självstudiekurs: IoT Hub-routning](tutorial-routing.md)
