---
title: "Azure messaging-tjänster jämförelse"
description: "Jämför Azure händelse rutnätet Händelsehubbar och Service Bus. Rekommenderar vilken tjänst som ska användas för olika scenarier."
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 03/16/2018
ms.author: tomfitz
ms.openlocfilehash: 30bbe7442cac96a1dcf6959cac2abedd61454a29
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/17/2018
---
# <a name="choose-between-azure-services-that-deliver-messages"></a>Välj mellan Azure-tjänster som levererar meddelanden

Azure erbjuder tre tjänster som hjälp med att leverera meddelanden om händelser i en lösning. Följande tjänster är:

* [Händelsen rutnätet](/azure/event-grid/)
* [Event Hubs](/azure/event-hubs/)
* [Service Bus](/azure/service-bus-messaging/)

Även om de har vissa likheter är varje tjänst utformad för särskilda scenarier. Den här artikeln beskrivs skillnaderna mellan dessa tjänster och förstå vilken de ska välja för ditt program. I många fall meddelandetjänsten kompletterar och kan användas tillsammans.

## <a name="event-vs-message-services"></a>Händelsen kontra message-tjänster

Det är en viktig skillnad att notera mellan tjänster som levererar en händelse och tjänster som levererar ett meddelande.

### <a name="event"></a>Händelse

En händelse är ett enkelt meddelande om ett villkor eller en ändring. Utgivaren av händelsen har inga förväntan om hur händelsen hanteras. Konsumenter av händelsen avgör vad du gör med meddelandet. Händelser kan vara separata enheter eller en del av en serie.

Diskreta händelser rapportera tillståndsändring och är tillämplig. Om du vill gå vidare endast måste veta att något hände. Händelsedata som innehåller information om vad som hänt men har inte de data som utlöste händelsen. Till exempel meddela en händelse konsumenter att en fil har skapats. Det kan innehålla allmän information om filen, men det innehåller inte själva filen. Diskreta händelser är idealisk för serverlösa lösningar som behöver för att skala.

Serie händelser rapportera ett villkor och är analyzable. Händelser är sorterade tid och relaterade. Konsumenten måste den sekvenserade serien händelser att analysera vad hände.

### <a name="message"></a>Meddelande

Ett meddelande är rådata som genereras av en tjänst som ska förbrukas eller lagrats någon annanstans. Meddelandet innehåller data som utlöste meddelandet pipeline. Utgivaren av meddelandet har en förväntan om hur konsumenten hanterar meddelandet. Det finns ett avtal mellan de två sidorna. Utgivaren skickar ett meddelande med rådata och förväntar sig konsumenten att skapa en fil från dessa data och skicka ett svar när arbetet utförs.

## <a name="comparison-of-services"></a>Jämförelse av tjänster

| Tjänst | Syfte | Typ | När du ska använda detta |
| ------- | ------- | ---- | ----------- |
| Event Grid | Reaktiv programmering | Händelsedistribution (diskreta) | Ta hänsyn till status ändras |
| Händelsehubbar | Stordata pipeline | Händelsen streaming (serie) | Telemetri och distribuerade data strömning |
| Service Bus | Högt värde enterprise-meddelanden | Meddelande | Bearbetningen och finansiella transaktioner |

### <a name="event-grid"></a>Event Grid

Händelsen rutnätet är en eventing bakplan som möjliggör händelsedriven, reaktiv programmering. Den använder ett Publicera-prenumerera modellen. Utgivare genererar händelser, men har ingen förväntan om vilka händelser som ska hanteras. Prenumeranter bestämmer vilka händelser som de vill hantera.

Händelsen rutnätet är djupt integrerad med Azure-tjänster och kan integreras med tjänster från tredje part. Det förenklar händelsekonsumtion och sänker kostnaderna genom att eliminera behovet av konstant avsökning. Händelsen rutnätet dirigerar effektivt och tillförlitligt händelser från Azure och Azure-resurser. Den distribuerar händelser till registrerade prenumeranten slutpunkter. Händelsemeddelandet innehåller den information du behöver ta hänsyn till ändringar i tjänster och program. Händelsen rutnätet är inte en data-pipeline och leverera inte det faktiska objektet har uppdaterats.

Det har följande egenskaper:

* dynamiskt utbyggbara
* Låg kostnad
* Serverlösa

### <a name="event-hubs"></a>Händelsehubbar

Händelsehubbar i Azure är en pipeline för stordata. Det underlättar capture, kvarhållning och omsändning av telemetri och händelse data i dataströmmen. Data kan komma från många samtidiga källor. Händelsehubbar kan telemetri och händelse data kan göras tillgängliga till en mängd olika tjänster för bearbetning av dataströmmen infrastrukturer och analyser. Det är tillgängligt som dataströmmar eller paketerade händelsebatchar. Den här tjänsten tillhandahåller en enkel lösning som möjliggör snabb datahämtning för realtidsbearbetning samt upprepade omsändning av lagrade rådata. Det kan samla in strömmande data till en fil för bearbetning och analys.

Det har följande egenskaper:

* låg latens
* kan ta emot och bearbetning av miljontals händelser per sekund

### <a name="service-bus"></a>Service Bus

Service Bus är avsedd för traditionella företagsprogram. Dessa företagsprogram kräver transaktioner, ordning, dubblettidentifiering och omedelbar konsekvenskontroll. Service Bus gör det möjligt för moln-intern program att tillhandahålla tillförlitlig övergången tillståndshantering för affärsprocesser. När du arbetar med högt värde meddelanden som inte kan tappas bort eller dubbletter, Använd Azure Service Bus. Service Bus också underlättar mycket säker kommunikation över hybridlösningar för molnet och befintliga lokala system kan ansluta till molnlösningar.

Service Bus är en asynkrona meddelanden. Den lagrar meddelanden i en ”broker” (till exempel en kö) tills konsumentparten är redo att ta emot meddelanden.

Det har följande egenskaper:

* tillförlitliga asynkron sändning av meddelande (enterprise-meddelanden som en tjänst) som kräver avsökning
* avancerade meddelandefunktioner som FIFO, batchbearbetning/sessioner, transaktioner, dead-lettering, Tidsbestämd kontroll, Routning och filtrering och dubblettidentifiering

## <a name="use-the-services-together"></a>Använda tjänsterna tillsammans

I vissa fall kan använda du tjänster sida vid sida för att uppfylla olika roller. En e-handel plats kan använda Service Bus för att bearbeta ordningen Händelsehubbar att samla in telemetri för platsen, och händelsen rutnätet för att svara på händelser som ett objekt som har levererats.

I andra fall kan länka du dem tillsammans för att skapa en händelse och data pipeline. Du kan använda händelsen rutnätet för att svara på händelser i andra tjänster. Ett exempel på hur händelsen rutnät med Händelsehubbar för att migrera data till ett datalager, se [strömma stordata i ett data warehouse](event-grid-event-hubs-integration.md). Följande bild visar arbetsflödet för strömmande data.

![Översikt över data i dataströmmen](./media/compare-messaging-services/overview.png)

## <a name="next-steps"></a>Nästa steg

* Mer information om de Azure-tjänsterna som finns i blogginlägget [händelser, datapunkter och meddelanden - att välja rätt Azure messaging-tjänsten för dina data](https://azure.microsoft.com/blog/events-data-points-and-messages-choosing-the-right-azure-messaging-service-for-your-data/).
* En introduktion till händelse rutnätet finns [om händelsen rutnätet](overview.md).
* Om du vill komma igång med händelsen rutnätet, se [skapa och flöde anpassade händelser med Azure händelse rutnätet](custom-event-quickstart.md).
* Kom igång med Händelsehubbar finns [skapa ett namnområde för Händelsehubbar och en händelsehubb med hjälp av Azure portal](../event-hubs/event-hubs-create.md).
* Om du vill komma igång med Service Bus finns [skapa ett namnområde för Service Bus med hjälp av Azure portal](../service-bus-messaging/service-bus-create-namespace-portal.md).