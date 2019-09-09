---
title: Köp Azure-reservationer med fram-eller månads betalningar
description: Lär dig hur du kan köpa Azure-reservationer med fram-eller månads betalningar.
services: billing
author: bandersmsft
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: banders
ms.openlocfilehash: d211334ad2aa760cd63b98c6827fb2512811a1d3
ms.sourcegitcommit: b8578b14c8629c4e4dea4c2e90164e42393e8064
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/09/2019
ms.locfileid: "70806953"
---
# <a name="purchase-reservations-with-monthly-payments"></a>Köp reservationer med månads betalningar

Fram till nu är Azure-reservationer som kräver betalning fram. Nu kan du betala för reservationer med månads betalningar. Till skillnad från ett första inköp där du betalar hela beloppet, delar alternativet för månatlig betalning upp den totala kostnaden för reservationen jämnt över varje månad. Den totala kostnaden för upp-och månads reservationer är samma och du betalar inte några extra avgifter när du väljer att betala varje månad.

Ditt månatliga betalnings belopp kan variera beroende på den aktuella månadens marknads växelkurs för din lokala valuta.

Månads betalningar är tillgängliga för:

- Virtuella datorer
- SQL Database
- SQL Data Warehouse
- Cosmos DB
- App Service Stamp-avgift

Köp reservationer i [Azure Portal](https://portal.azure.com/?Microsoft_Azure_Reservations_EnableMultiCart=true&amp;paymentPlan=true#blade/Microsoft_Azure_Reservations/CreateBlade).

![Exempel som visar reservations köp](./media/billing-monthly-payments-reservations/purchase-reservation.png)

När du gör ett reservations köp kan du Visa betalnings planen. Klicka på **Visa fullständigt betalnings schema**.

![Exempel som visar reservations betalnings schema](./media/billing-monthly-payments-reservations/prepurchase-schedule.png)

Om du vill visa betalnings planen efter köpet väljer du en reservation, klickar på **reservations orderns ID**och klickar sedan på fliken **betalningar** .

## <a name="view-payments-made"></a>Visa betalningar som gjorts

Du kan visa betalningar som gjorts med hjälp av API: er, användnings data och i kostnads analys. För reservationer som betalas för varje månad visas frekvens svärdet som **återkommande** i API för användnings data och reservations avgifter. För reservationer som betalas fram visas värdet som **Databasmigrering**.

Kostnads analys visar månatliga inköp i standardvyn. Använd **inköps** filtret för **debiterings typ** och **återkommande** för **frekvens** för att se alla inköp. Om du bara vill visa reservationer ska du använda ett filter för **reservation**.

![Exempel som visar reservations inköps kostnader i kostnads analys](./media/billing-monthly-payments-reservations/cost-analysis.png)

## <a name="switch-to-monthly-payments-at-renewal"></a>Växla till månatliga betalningar vid förnyelse

När du förnyar en reservation kan du ändra fakturerings frekvensen till varje månad.

## <a name="exchange-and-refunds"></a>Exchange och åter betalning

Precis som andra reservationer kan du återbetala eller byta ut reservationer med månatlig fakturering. För närvarande kan du skicka en support förfrågan om att starta ett utbyte eller en åter betalning för en reservation som köpts med månatlig fakturering.

När du utbyter en reservation som betalas för varje månad bör den totala livstids kostnaden för det nya köpet vara större än de överblivna betalningar som avbrutits för den returnerade reservationen. Det finns inga andra gränser eller avgifter för utbyten. Du kan byta ut en reservation som betalas fram för att köpa en ny reservation som faktureras per månad. Men livstid svärdet för den nya reservationen ska vara större än det beräknade värdet för den reservation som returneras.

Om du avbryter en reservation som betalas ut för varje månad kan Microsoft tillämpa en uppsägnings avgift för framtida genomförda betalningar som annulleras. Återstående genomförda betalningar påförs mot bidrags gränsen $50 000 USD.

För ytterligare information om Exchange och åter betalning, se [självbetjänings utbyten och åter betalningar för Azure reservations](billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

F. Erbjuder Azure en del upp-front-reservationer?<br>
A. Nej. Eftersom kostnaderna för reservationer som betalas fram och månads vis är desamma, har Microsoft inte stöd för partiella betalningar.

F. Är månads betalningar tillgängliga för CSP-programmet (Microsoft Cloud Solution Provider)?<br>
A. Ja, partner kan köpa reservationer för sina CSP-kunder i Azure Portal. Möjligheten att köpa reservationer med månatlig fakturering är inte tillgänglig i Partner Center.

F. Jag är en US Azure Government-kund, kan jag betala månads vis för reservations köp?<br>
A. Inte för närvarande.

F. När kan jag utbyta eller återbetala mig själv i Azure Portal, i stället för att skapa ett support ärende?<br>
A. Inte för närvarande. Förfrågningar till Exchange-och bidrags reservationer med månads betalningar hanteras av Azure-supporten.

## <a name="next-steps"></a>Nästa steg

- Läs mer om reservationer i [Vad är Azure reservations?](billing-save-compute-costs-reservations.md)
- Information om vilka uppgifter du bör utföra innan du köper en reservation finns i [bestämma rätt VM-storlek innan du köper](../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy)
