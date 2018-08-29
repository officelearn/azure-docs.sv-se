---
title: Jämför Event Grid, routning för IoT Hub | Microsoft Docs
description: IoT Hub ger en egen tjänst för händelsedirigering meddelandet, men är integrerat med Event Grid för publicering av händelser. Jämför de två funktionerna.
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/30/2018
ms.author: kgremban
ms.openlocfilehash: af03f737c082a7fda90104303e018f7b417729b9
ms.sourcegitcommit: a1140e6b839ad79e454186ee95b01376233a1d1f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2018
ms.locfileid: "43143801"
---
# <a name="compare-message-routing-and-event-grid-for-iot-hub"></a>Jämför meddelanderoutning och Event Grid för IoT Hub

Azure IoT Hub ger möjlighet att strömma data från dina anslutna enheter och integrera data i dina affärsprogram. IoT Hub har två metoder för att integrera IoT-händelser i andra Azure-tjänster eller program. Den här artikeln beskriver de två funktionerna som ger den här funktionen så att du kan välja vilket alternativ som passar bäst för ditt scenario.

* **IoT Hub meddelanderoutning**: den här IoT Hub-funktionen kan användare [dirigera meddelanden från enheten till molnet](iot-hub-devguide-messages-read-custom.md) till tjänstslutpunkter som Azure Storage-behållare, Event Hubs, Service Bus-köer och Service Bus-ämnen. Routningsregler ger flexibilitet för att utföra frågebaserade vägar. De ger också [kritiska aviseringar](iot-hub-devguide-messages-d2c.md) att utlösa åtgärder via frågor och kan baseras på meddelanderubriker och brödtext. 
* **IoT Hub-integrering med Event Grid**: Azure Event Grid är en fullständigt hanterad tjänst för händelsedirigering som använder en Publicera-prenumerera modellen. IoT Hub och Event Grid arbetar tillsammans för att [integrera IoT Hub-händelser i Azure och Azure-tjänster](iot-hub-event-grid.md), i nära realtid. 

## <a name="similarities-and-differences"></a>Likheter och skillnader

Aktivera varningskonfigurationen meddelanderoutning och Event Grid, finns men det några huvudsakliga skillnader mellan två. Se tabellen nedan för mer information:

| Funktion | IoT Hub meddelanderoutning | IoT Hub-integrering med Event Grid |
| ------- | --------------- | ---------- |
| **Meddelanden från enhet** | Ja, meddelanderoutning användas för telemetridata. | Nej, Event Grid kan endast användas för icke-telemetri IoT Hub-händelser. |
| **Händelsetyp** | Ja, meddelanderoutning kan rapportera twin ändringar och Livscykelhändelser för enhet. | Ja, Event Grid kan rapportera när enheter skapas, tas bort, ansluten och kopplas bort från IoT Hub |
| **Ordning** | Ja, sorteringen av händelser underhålls.  | Nej, ordningen för händelser är inte säkert. | 
| **Största meddelandestorlek** | 256 KB enhet-till-moln | 64 kB |
| **Filtrering** | Omfattande filtrering via SQL-liknande språk stöder filtrering på meddelandehuvudena och organ. Exempel finns i [IoT Hub-frågespråk](iot-hub-devguide-query-language.md). | Filtrering baserad på suffix/prefixet enhets-ID, vilket fungerar bra för hierarkisk tjänster som lagring. |
| **Slutpunkter** | <ul><li>Event Hubs</li> <li>Lagringsblob</li> <li>Service Bus-kö</li> <li>Service Bus-ämnen</li></ul><br>Betalda IoT Hub SKU: er (S1, S2 och S3) är begränsade till 10 anpassade slutpunkter. 100 vägar kan skapas per IoT Hub. | <ul><li>Azure Functions</li> <li>Azure Automation</li> <li>Event Hubs</li> <li>Logic Apps</li> <li>Storage Blob</li> <li>Anpassade ämnen</li> <li>Tjänster från tredje part via WebHooks</li></ul><br>Den senaste listan över slutpunkter, se [Event Grid händelsehanterare](../event-grid/overview.md#event-handlers). |
| **Kostnad** | Det finns ingen avgift för meddelanderoutning. Endast inkommande telemetri till IoT Hub debiteras. Om du har ett meddelande som dirigeras till tre olika slutpunkter kan debiteras du till exempel för bara ett meddelande. | Det är kostnadsfritt från IoT Hub. Event Grid erbjuder de första 100 000 åtgärderna per månad utan kostnad, och sedan $0,60 per miljon åtgärder efter det. |

Meddelanderoutning i IoT Hub och Event Grid har likheter för, vilket beskrivs i följande tabell:

| Funktion | IoT Hub meddelanderoutning | IoT Hub-integrering med Event Grid |
| ------- | --------------- | ---------- |
| **Tillförlitlighet** | Hög: Ger varje meddelande till minst en gång för varje väg-slutpunkten. Upphör att gälla på alla meddelanden som inte levereras inom en timme. | Hög: Levererar meddelandena till webhooken minst en gång för varje prenumeration. Upphör att gälla alla händelser som inte levereras inom 24 timmar. | 
| **Skalbarhet** | Hög: Optimerad för att ge stöd för miljontals simultant kopplade enheter som skickar miljarder meddelanden. | Hög: Kompatibla routning 10 000 000 händelser per sekund per region. |
| **Svarstid** | Låg: Nästan i realtid. | Låg: Nästan i realtid. |
| **Skicka till flera slutpunkter** | Ja, skicka ett enda meddelande till flera slutpunkter. | Ja, skicka ett enda meddelande till flera slutpunkter.  | 
| **Säkerhet** | IOT Hub innehåller identitet per enhet och kan återkallas access control. Mer information finns i den [IoT Hub-åtkomstkontroll](iot-hub-devguide-security.md). | Event Grid ger verifiering på tre platser: händelseprenumerationer, publicering av händelser och webhook-händelseleverans. Mer information finns i [Event Grid säkerhet och autentisering](../event-grid/security-authentication.md). |

## <a name="how-to-choose"></a>Välj så här

Meddelanderoutning i IoT Hub och IoT Hub-integrering med Event Grid kan du utföra olika åtgärder för att få liknande resultat. De både tar information från din lösning för IoT Hub och skickar den så att andra tjänster kan reagera. Hur avgör du vilken jag ska använda? Utöver data från föregående avsnitt, kan du använda följande frågor som hjälper dig: 

* **Vilken typ av data du skickar till slutpunkterna?**

   Använd IoT Hub meddelanderoutning när du behöver skicka telemetridata till andra tjänster. Meddelanderoutning även aktiverar frågor till meddelandehuvudena och meddelandetexten. 

   IoT Hub-integrering med Event Grid fungerar med händelser som inträffar i IoT Hub-tjänsten. Dessa IoT Hub-händelser är bland annat enhet skapas, tas bort, ansluten och kopplas från. 

* **Vilka slutpunkter måste ta emot den här informationen?**

   IoT Hub meddelanderoutning stöder begränsad slutpunkter, men du kan skapa anslutningar för att omdirigera data och händelser till ytterligare slutpunkter. En fullständig lista över slutpunkter som stöds finns i tabellen i föregående avsnitt. 

   IoT Hub-integrering med Event Grid har stöd för fler slutpunkter. Den fungerar även med webhooks för att utöka routning utanför ekosystemet för Azure-tjänst och till program från tredje part. 

* **Spelar det någon roll om dina data tas emot i ordning?**

   Meddelanderoutning i IoT Hub underhåller den ordning som meddelanden skickas, så att de tas emot på samma sätt.

   Event Grid garanterar inte att slutpunkter visas händelser i samma ordning som de inträffade. Händelseschemat innehåller en tidsstämpel som kan användas för att identifiera ordningen efter händelserna tas emot på slutpunkten. 

## <a name="next-steps"></a>Nästa steg

* Läs mer om [IoT Hub meddelanderoutning](iot-hub-devguide-messages-d2c.md) och [IoT Hub-slutpunkter](iot-hub-devguide-endpoints.md).

* Läs mer om [Azure Event Grid](../event-grid/overview.md)

* Prova att använda Event Grid-integrationen av [skicka e-postaviseringar om Azure IoT Hub-händelser med hjälp av Logic Apps](../event-grid/publish-iot-hub-events-to-logic-apps.md)