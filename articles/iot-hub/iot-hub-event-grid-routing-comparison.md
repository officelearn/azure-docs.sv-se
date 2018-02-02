---
title: "Jämför händelse rutnätet routning för IoT-hubb | Microsoft Docs"
description: "IoT-hubb erbjuder ett eget meddelande routningstjänsten, men också integreras med händelsen rutnät för publicering av händelsen. Jämför de två funktionerna."
services: iot-hub
documentationcenter: 
author: kgremban
manager: timlt
editor: 
ms.service: iot-hub
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2018
ms.author: kgremban
ms.openlocfilehash: 5a0a97ccf033b2981ba13be455482146ba212228
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="compare-message-routing-and-event-grid-for-iot-hub"></a>Jämför meddelanderoutning och händelsen rutnät för IoT-hubb

Azure IoT-hubb ger möjlighet att strömma data från dina anslutna enheter och integrera data i dina affärsprogram. IoT-hubb erbjuder två metoder för att integrera IoT händelser i andra Azure-tjänster eller program. Den här artikeln beskrivs de två funktionerna som ger den här funktionen så att du kan välja vilket alternativ som är bäst för ditt scenario.

* **IoT-hubb meddelanderoutning**: funktionen för den här IoT-hubb kan användare [vidarebefordra meddelanden från enhet till moln](iot-hub-devguide-messages-read-custom.md) till slutpunkter som Azure Storage-behållare, Event Hubs, Service Bus-köer och Service Bus-ämnen. Regler för routning ger flexibilitet för att utföra frågebaserade vägar. De ger också [kritiska aviseringar](iot-hub-devguide-messages-d2c.md) som utlöser åtgärder via frågor och kan baseras på meddelandehuvuden och brödtext. 
* **IoT-hubb integrering med händelsen rutnätet**: Azure händelse rutnätet är en helt hanterad händelse routning tjänst som använder ett Publicera-prenumerera modellen. IoT-hubb och händelsen rutnät arbetar tillsammans för att [integrera IoT-hubb händelser i Azure och Azure-tjänster](iot-hub-event-grid.md), i nära realtid. 

## <a name="similarities-and-differences"></a>Likheter och skillnader

Aktivera varningskonfigurationen meddelanderoutning och händelsen rutnätet, finns men det några viktiga skillnader mellan två. Se tabellen nedan för mer information:

| Funktion | IoT-hubb meddelanderoutning | IoT-hubb integrering med händelsen rutnätet |
| ------- | --------------- | ---------- |
| **Meddelanden** | Ja, meddelanderoutning användas för telemetridata. | Nej, händelse rutnätet kan endast användas för icke telemetri IoT-hubb händelser. |
| **Händelsetyp** | Ja, meddelanderoutning kan rapportera dubbla ändringar och device Livscykelhändelser. | Ja, händelse rutnätet kan rapportera när enheter är registrerade på en IoT-hubb och när enheter tas bort. |
| **Ordning** | Ja, sorteringen av händelser bevaras.  | Nej, garanteras inte ordningen för händelser. | 
| **Maximal meddelandestorlek** | 256 KB enhet till moln | 64 kB |
| **Filtrering** | Omfattande filtrering via SQL-liknande språk stöder filtrering på meddelandehuvuden och organ. Exempel finns i [IoT-hubb frågespråket](iot-hub-devguide-query-language.md). | Filtrering baserat på suffix/prefixet enhets-ID som fungerar bra för hierarkiska tjänster som lagring. |
| **Slutpunkter** | <ul><li>Händelsehubb</li> <li>Lagringsblob</li> <li>Service Bus-kö</li> <li>Service Bus-ämnen</li></ul><br>Betald IoT-hubb SKU: er (S1 S2 och S3) är begränsade till 10 anpassade slutpunkter. 100 vägar kan skapas per IoT-hubb. | <ul><li>Azure Functions</li> <li>Azure Automation</li> <li>Händelsehubb</li> <li>Logic Apps</li> <li>Microsoft Flow</li> <li>Tjänster från tredje part via WebHooks</li></ul><br>Den senaste listan över slutpunkter, se [händelse rutnätet händelsehanterare](../event-grid/overview.md#event-handlers). |
| **Kostnad** | Det finns ingen särskild avgift för meddelanderoutning. Endast ingång av telemetri i IoT-hubb debiteras. Om du har ett meddelande som dirigeras till tre olika slutpunkter kan debiteras du till exempel för endast ett meddelande. | Det är gratis från IoT-hubb. Händelsen rutnätet erbjuder de första 100 000 åtgärderna per månad gratis och sedan $0,60 per miljoner åtgärder efter. |

IoT-hubb meddelanderoutning och händelsen rutnätet har likheter för, vilket beskrivs i följande tabell:

| Funktion | IoT-hubb meddelanderoutning | IoT-hubb integrering med händelsen rutnätet |
| ------- | --------------- | ---------- |
| **Tillförlitlighet** | Hög: Levererar varje meddelande till slutpunkten minst en gång för varje flöde. Går ut alla meddelanden som inte levereras inom en timme. | Hög: Levererar varje meddelande till webhooken minst en gång för varje prenumeration. Upphör att gälla alla händelser som inte levereras inom 24 timmar. | 
| **Skalbarhet** | Hög: Optimerat stöd för miljontals samtidigt anslutna enheter skickar miljarder meddelanden. | Hög: Kan routning 10 000 000 händelser per sekund per region. |
| **Svarstid** | Låg: Nära realtid. | Låg: Nära realtid. |
| **Skicka till flera slutpunkter** | Ja, skicka ett meddelande till flera slutpunkter. | Ja, skicka ett meddelande till flera slutpunkter.  | 
| **Säkerhet** | IOT-hubb ger identitet per enhet och återkallningsbar åtkomstkontroll. Mer information finns i [IoT-hubb åtkomstkontroll](iot-hub-devguide-security.md). | Händelsen rutnätet innehåller validering på tre ställen: händelseprenumerationer, händelse publicering och webhook händelse leverans. Mer information finns i [händelse rutnätet säkerhets- och autentiseringstjänster](../event-grid/security-authentication.md). |

## <a name="how-to-choose"></a>Välj så här

IoT-hubb meddelanderoutning och IoT-hubb-integrering med händelsen rutnätet utför olika åtgärder för att uppnå samma resultat. De både ta information från din lösning för IoT-hubb och skickar den så att andra tjänster kan reagera. Hur du för att avgöra vilket som ska användas? Utöver data från föregående avsnitt, kan du använda följande frågor hjälper dig: 

* **Vilken typ av data du skickar till slutpunkterna?**

   Använd IoT-hubb meddelanderoutning när du vill skicka telemetridata till andra tjänster. Meddelanderoutning även aktiverar frågar meddelandehuvuden och meddelandetext. 

   IoT-hubb-integrering med händelsen rutnätet fungerar med händelser som inträffar i IoT-hubb-tjänsten. De här IoT-hubb-händelser innehåller enhet skapas eller tas bort. 

* **Vilka slutpunkter behöver ta emot den här informationen?**

   IoT-hubb meddelanderoutning stöder begränsad slutpunkter, men du kan skapa kopplingar för att omdirigera data och händelser till ytterligare slutpunkter. En fullständig lista över slutpunkter som stöds finns i tabellen i föregående avsnitt. 

   IoT-hubb-integrering med händelsen rutnätet har stöd för fler slutpunkter. Den fungerar även med webhooks att utöka routning utanför ekosystemet för Azure-tjänster och program från tredje part. 

* **Det ingen roll om dina data anländer i ordning?**

   IoT-hubb meddelanderoutning underhåller den ordning som meddelanden skickas, så att de tas emot på samma sätt.

   Händelsen rutnätet garanterar inte att slutpunkter får händelser i samma ordning som de inträffade. Händelseschemat innehåller en tidsstämpel som kan användas för att identifiera vilken ordning efter händelser anländer till slutpunkten. 

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [IoT-hubb meddelanderoutning](iot-hub-devguide-messages-d2c.md) och [IoT-hubbslutpunkter](iot-hub-devguide-endpoints.md).

* Lär dig mer om [Azure händelse rutnätet](../event-grid/overview.md)

* Testa händelse rutnätet integrering av [skicka e-postmeddelanden om Azure IoT Hub-händelser med hjälp av Logic Apps](../event-grid/publish-iot-hub-events-to-logic-apps.md)