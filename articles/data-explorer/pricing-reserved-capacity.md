---
title: Förskotts betalning för Azure Datautforskaren markering för att spara pengar
description: Lär dig hur du köper Azure Datautforskaren reserverad kapacitet för att spara pengar på dina Azure Datautforskaren-kostnader.
author: avnera
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/03/2019
ms.openlocfilehash: 66c5644df7d796669105693d08788548334ae93a
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681631"
---
# <a name="prepay-for-azure-data-explorer-markup-units-with-azure-data-explorer-reserved-capacity"></a>Förskottsbetala för Azure Datautforskaren Markup-enheter med Azure Datautforskaren reserverad kapacitet

Spara pengar med Azure Datautforskaren genom att förbetala för påläggs enheter jämfört med priserna för betala per användning. Med Azure Datautforskaren reserverad kapacitet gör du ett åtagande för Azure Datautforskaren-användning under en period av en eller tre år för att få en betydande rabatt på kostnaderna för Azure Datautforskaren-pålägg. Om du vill köpa reserverad Azure-Datautforskaren behöver du bara ange villkoret så att det gäller alla distributioner av Azure-Datautforskaren i alla regioner.

Genom att köpa en reservation debiteras du för påläggs kostnader under en period på en eller tre år. Så snart du köper en reservation debiteras inte längre Azure Datautforskaren Markup-avgifterna som matchar reservations attributen enligt priserna för betala per användning. Azure Datautforskaren-kluster som redan körs eller som nyligen distribueras får automatiskt förmånen. Den här reservationen behandlar inte beräknings-, nätverks-eller lagrings kostnader som är associerade med klustren. Reserverad kapacitet för dessa resurser måste köpas separat. I slutet av reservations perioden upphör fakturerings förmånen och Azure Datautforskaren Markup-enheterna debiteras enligt priset betala per användning. Reservationer förnyas inte automatiskt. Pris information finns på sidan med [priser för Azure datautforskaren](https://azure.microsoft.com/pricing/details/data-explorer/).

Du kan köpa Azure Datautforskaren reserverad kapacitet i [Azure Portal](https://portal.azure.com). För att köpa Azure Datautforskaren reserverad kapacitet:

* Du måste vara ägare till minst en prenumeration på företag eller betala per användning.
* För Enterprise-prenumerationer måste **Lägg till reserverade instanser** vara aktiverat i [EA-portalen](https://ea.azure.com). Om inställningen är inaktive rad måste du dessutom vara en EA-administratör i prenumerationen.
* För ett CSP-program (Cloud Solution Provider) kan endast administratörs agenter eller försäljnings agenter köpa Azure Datautforskaren reserverad kapacitet.

Mer information om hur företags kunder och kunder som betalar per användning debiteras för reservations köp finns i:
* [Förstå Azure reservation-användning för företagets registrering](../billing/billing-understand-reserved-instance-usage-ea.md) 
* [Förstå Azure reservation-användning för din prenumeration enligt principen betala per](../billing/billing-understand-reserved-instance-usage.md)användning.

## <a name="determine-the-right-markup-usage-before-purchase"></a>Fastställ rätt märknings användning före köp

Reservationens storlek bör baseras på det totala antalet Azure Datautforskaren Markup-enheter som används av befintliga eller närmast distribuerade Azure Datautforskaren-kluster. Antalet kod enheter är detsamma som antalet Azure Datautforskaren-motor kluster kärnor i produktion (exklusive utvecklings-/test-SKU). 

## <a name="buy-azure-data-explorer-reserved-capacity"></a>Köp Azure Datautforskaren reserverad kapacitet

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Välj **alla tjänster** > **reservationer** > **Köp nu**. Välj **Lägg till**
1. I fönstret **Välj produkt typ** väljer du **Azure datautforskaren** för att köpa en ny reservation för Azure datautforskaren Markup-enheter. 
1. Välj **köp**
1. Fyll i de obligatoriska fälten. 

    ![Sidan köp](media/pricing-reserved-capacity/purchase-page.png)

1. Granska kostnaden för den reserverade kapacitets reservationen för Azure Datautforskaren Markup i avsnittet **kostnader** .
1. Välj **Köp**.
1. Välj **Visa den här reservationen** för att se inköpets status.

## <a name="cancellations-and-exchanges"></a>Annulleringar och utbyten

Om du behöver avbryta din Azure-Datautforskaren reserverade kapacitets reservationen kan det finnas 12% tidig uppsägnings avgift. Åter betalningar baseras på det lägsta priset för inköps priset eller det aktuella priset för reservationen. Återbetalningar begränsas till 50 000 USD per år. Din återbetalning består av det återstående proportionellt beräknade saldot minus uppsägningsavgiften på 12 %. Om du vill begära en annullering går du till reservationen i Azure Portal och väljer **åter betalning** för att skapa en supportbegäran.

Om du behöver ändra din Azure Datautforskaren reserverad kapacitet reservation till en annan period kan du utbyta den för en annan reservation som är lika med eller högre. Startdatumet för den nya reservationens överförs inte från den utbytte reservationen. Villkoret 1 eller 3 år börjar från när du skapar den nya reservationen. Om du vill begära ett utbyte går du till reservationen i Azure Portal och väljer **Exchange** för att skapa en support förfrågan.

Mer information om hur du utbyter eller återbetalar reservationer finns i [reservations utbyte och åter betalningar](../billing/billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="manage-your-reserved-capacity-reservation"></a>Hantera din reserverade kapacitets reservation

Reservations rabatten för Azure Datautforskaren Markup-enheter tillämpas automatiskt på antalet kod enheter som matchar omfattningen och attributen för reserverad kapacitet för Azure Datautforskaren. 


> [!NOTE]
> * Du kan uppdatera omfånget för Azure Datautforskaren reserverad kapacitets reservation via [Azure Portal](https://portal.azure.com), POWERSHELL, CLI eller via API: et.
> * Information om hur du hanterar reservationen för reserverad Azure Datautforskaren finns i [Hantera Azure-datautforskaren reserverad kapacitet](../billing/billing-understand-kusto-azuredataexplorer-reservation-charges.md).

## <a name="next-steps"></a>Nästa steg

Du kan läsa mer om Azure-reservationer i följande artiklar:

* [Vad är Azure-reservationer?](../billing/billing-save-compute-costs-reservations.md)
* [Hantera Azure Reservations](../billing/billing-manage-reserved-vm-instance.md)
* [Förstå reservationsrabatter i Azure](../billing/billing-understand-reservation-charges.md)
* [Förstå reservationsanvändning för din Betala per användning-prenumeration](../billing/billing-understand-reserved-instance-usage.md)
* [Förstå reservationsanvändning för din Enterprise-registrering](../billing/billing-understand-reserved-instance-usage-ea.md)
* [Azure-reservationer i ett CSP-program (Cloud Solution Provider) i Partnercenter](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
