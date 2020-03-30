---
title: Jämför händelserutnät, routning för IoT Hub | Microsoft-dokument
description: IoT Hub erbjuder en egen meddelanderoutningstjänst, men integreras också med Event Grid för händelsepublicering. Jämför de två funktionerna.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.openlocfilehash: 877467b65d346c871dd93f4b3f96b2c1664fa4b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73906784"
---
# <a name="compare-message-routing-and-event-grid-for-iot-hub"></a>Jämföra meddelanderoutning och händelserutnät för IoT Hub

Azure IoT Hub ger möjlighet att strömma data från dina anslutna enheter och integrera dessa data i dina affärsprogram. IoT Hub erbjuder två metoder för att integrera IoT-händelser i andra Azure-tjänster eller affärsprogram. I den här artikeln beskrivs de två funktioner som ger den här funktionen, så att du kan välja vilket alternativ som är bäst för ditt scenario.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

**[IoT Hub-meddelanderoutning](iot-hub-devguide-messages-d2c.md)**: Med den här IoT Hub-funktionen kan användare dirigera meddelanden från enhet till moln till tjänstslutpunkter som Azure Storage-behållare, eventhubbar, servicebussköer och servicebussavsnitt. Routning ger också en frågefunktion för att filtrera data innan du dirigerar dem till slutpunkterna. Förutom enhetstelemetridata kan du också skicka [icke-telemetrihändelser](iot-hub-devguide-messages-d2c.md#non-telemetry-events) som kan användas för att utlösa åtgärder. 

**IoT Hub-integrering med Event Grid:** Azure Event Grid är en fullständigt hanterad händelseroutningstjänst som använder en publiceringsprenumerera-modell. IoT Hub och Event Grid arbetar tillsammans för att [integrera IoT Hub-händelser i Azure och icke-Azure-tjänster](iot-hub-event-grid.md)i nästan realtid. IoT Hub publicerar både [enhetshändelser](iot-hub-event-grid.md#event-types) och telemetrihändelser.

## <a name="differences"></a>Skillnader

Medan både meddelanderoutning och Event Grid aktiverar aviseringskonfiguration, finns det några viktiga skillnader mellan de två. Mer information finns i följande tabell:

| Funktion | IoT Hub-meddelanderoutning | IoT Hub-integrering med Event Grid |
| ------- | --------------- | ---------- |
| **Enhetsmeddelanden och -händelser** | Ja, meddelanderoutning kan användas för telemetridata, rapportera enhetsstvillingändringar, enhetslivscykelhändelser och digitala twin change-händelser (en del av den offentliga förhandsversionen av [IoT Plug and Play).](../iot-pnp/overview-iot-plug-and-play.md) | Ja, Event Grid kan användas för telemetridata men kan också rapportera när enheter skapas, tas bort, ansluts och kopplas från IoT Hub |
| **Ordning** | Ja, ordning och reda upprätthålls.  | Nej, händelseordning garanteras inte. | 
| **Filtrering** | Omfattande filtrering av egenskaper för meddelandeprogram, egenskaper för meddelandesystem, meddelandetext, enhetstvillingtaggar och enhetstvillingegenskaper. Filtrering tillämpas inte på digitala dubbla ändringshändelser. Exempel finns i [Syntax för fråga om meddelanderoutning](iot-hub-devguide-routing-query-syntax.md). | Filtrering baserat på händelsetyp, ämnestyp och attribut i varje händelse. Exempel finns i [Förstå filtrering av händelser i prenumerationer på eventrutnät](../event-grid/event-filtering.md). När du prenumererar på telemetrihändelser kan du använda ytterligare filter på data för att filtrera på meddelandeegenskaper, meddelandetext och enhetstvilling i IoT-hubben innan du publicerar till Event Grid. Se [hur du filtrerar händelser](../iot-hub/iot-hub-event-grid.md#filter-events). |
| **Slutpunkter** | <ul><li>Händelsehubbar</li> <li>Azure Blob Storage</li> <li>Service Bus-kö</li> <li>Service Bus ämnen</li></ul><br>Betalda IoT Hub SKU:er (S1, S2 och S3) är begränsade till 10 anpassade slutpunkter. 100 vägar kan skapas per IoT Hub. | <ul><li>Azure Functions</li> <li>Azure Automation</li> <li>Händelsehubbar</li> <li>Logic Apps</li> <li>Storage Blob</li> <li>Anpassade ämnen</li> <li>Queue Storage</li> <li>Microsoft Flow</li> <li>Tjänster från tredje part via WebHooks</li></ul><br>500 slutpunkter per IoT Hub stöds. Den senaste listan över slutpunkter finns i [händelserutnätshanterare](../event-grid/overview.md#event-handlers). |
| **Kostnad** | Det finns ingen separat avgift för meddelanderoutning. Endast telemetri till IoT Hub debiteras. Om du till exempel har ett meddelande dirigerat till tre olika slutpunkter faktureras du bara för ett meddelande. | Det finns ingen avgift från IoT Hub. Event Grid erbjuder de första 100 000 operationerna per månad gratis och sedan 0,60 USD per miljon operationer efteråt. |

## <a name="similarities"></a>Likheter

IoT Hub-meddelanderoutning och Event Grid har också likheter, varav vissa beskrivs i följande tabell:

| Funktion | IoT Hub-meddelanderoutning | IoT Hub-integrering med Event Grid |
| ------- | --------------- | ---------- |
| **Maximal meddelandestorlek** | 256 KB, enhet till moln | 256 KB, enhet till moln |
| **Tillförlitlighet** | Hög: Levererar varje meddelande till slutpunkten minst en gång för varje rutt. Förfaller alla meddelanden som inte levereras inom en timme. | Hög: Levererar varje meddelande till webhooken minst en gång för varje prenumeration. Förfaller alla händelser som inte levereras inom 24 timmar. | 
| **Skalbarhet** | Hög: Optimerad för att stödja miljontals samtidigt anslutna enheter som skickar miljarder meddelanden. | Hög: Kan dirigera 10 000 000 händelser per sekund per region. |
| **Svarstid** | Låg: Nära realtid. | Låg: Nära realtid. |
| **Skicka till flera slutpunkter** | Ja, skicka ett enda meddelande till flera slutpunkter. | Ja, skicka ett enda meddelande till flera slutpunkter.  
| **Säkerhet** | Iot Hub tillhandahåller identitet per enhet och återkavbar åtkomstkontroll. Mer information finns i [IoT Hub-åtkomstkontrollen](iot-hub-devguide-security.md). | Event Grid ger validering på tre punkter: händelseprenumerationer, händelsepublicering och webhook-händelseleverans. Mer information finns i [Säkerhet och autentisering av Händelserutnät](../event-grid/security-authentication.md). |

## <a name="how-to-choose"></a>Så här väljer du

IoT Hub-meddelanderoutning och IoT Hub-integrering med Event Grid utför olika åtgärder för att uppnå liknande resultat. De tar båda information från din IoT Hub-lösning och vidarebefordrar den så att andra tjänster kan reagera. Så hur bestämmer du vilken som ska användas? Tänk på följande frågor som hjälper dig att styra ditt beslut: 

* **Vilken typ av data skickar du till slutpunkterna?**

   Använd IoT Hub-meddelanderoutning när du måste skicka telemetridata till andra tjänster. Meddelanderoutning möjliggör också fråga meddelandeprogram och systemegenskaper, meddelandetext, enhetstvillingtaggar och enhetstvillingegenskaper.

   IoT Hub-integreringen med Event Grid fungerar med händelser som inträffar i IoT Hub-tjänsten. Dessa IoT Hub-händelser omfattar telemetridata, enhet som skapats, tagits bort, anslutits och kopplats från. När du prenumererar på telemetrihändelser kan du använda ytterligare filter på data för att filtrera på meddelandeegenskaper, meddelandetext och enhetstvilling i IoT-hubben innan du publicerar till Event Grid. Se [hur du filtrerar händelser](../iot-hub/iot-hub-event-grid.md#filter-events).

* **Vilka slutpunkter behöver för att få den här informationen?**

   IoT Hub-meddelanderoutning stöder begränsat antal unika slutpunkter och slutpunktstyper, men du kan skapa kopplingar för att dirigera om data och händelser till ytterligare slutpunkter. En fullständig lista över slutpunkter som stöds finns i tabellen i föregående avsnitt. 

   IoT Hub-integreringen med Event Grid stöder 500 slutpunkter per IoT Hub och ett större antal slutpunktstyper. Den integreras inbyggt med Azure Functions, Logic Apps, Storage and Service Bus köer och fungerar även med webhooks för att utöka skicka data utanför Azure-tjänstekosystemet och till affärsprogram från tredje part.

* **Spelar det någon roll om dina uppgifter kommer i ordning?**

   IoT Hub-meddelanderoutning upprätthåller den ordning i vilken meddelanden skickas, så att de kommer fram på samma sätt.

   Event Grid garanterar inte att slutpunkter kommer att ta emot händelser i samma ordning som de inträffade. I de fall där absolut ordning på meddelanden är betydande och/eller där en konsument behöver en tillförlitlig unik identifierare för meddelanden rekommenderar vi att du använder meddelanderoutning. 

## <a name="next-steps"></a>Nästa steg

* Läs mer om [IoT Hub Message Routing](iot-hub-devguide-messages-d2c.md) och [IoT Hub-slutpunkter](iot-hub-devguide-endpoints.md).
* Läs mer om [Azure Event Grid](../event-grid/overview.md).
* Mer information om hur du skapar meddelandevägar finns i [process-IoT Hub-meddelanden från enhet till moln med hjälp av](../iot-hub/tutorial-routing.md) självstudiekurs för rutter.
* Prova integreringen av Event Grid genom [att skicka e-postmeddelanden om Azure IoT Hub-händelser med Hjälp av Logic Apps](../event-grid/publish-iot-hub-events-to-logic-apps.md).
