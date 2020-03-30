---
title: Förskottsbetalning för Azure Data Explorer-uppmärkning för att spara pengar
description: Lär dig hur du köper reserverad azure data explorer-kapacitet för att spara på dina Azure Data Explorer-kostnader.
author: avneraa
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/03/2019
ms.openlocfilehash: bbb2108967353b851a0fa0ee610ec30380e3fc50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75969273"
---
# <a name="prepay-for-azure-data-explorer-markup-units-with-azure-data-explorer-reserved-capacity"></a>Förskottsbetalning för Azure Data Explorer-uppmärkningsenheter med reserverad azure data explorer-kapacitet

Spara pengar med Azure Data Explorer genom att betala för förskott för uppmärkningsenheterna jämfört med priser för betalning per steg. Med reserverad azure data explorer-kapacitet gör du ett åtagande för Azure Data Explorer-användning under en period av ett eller tre år för att få en betydande rabatt på Azure Data Explorer-uppmärkningskostnaderna. Om du vill köpa reserverad azure data explorer-kapacitet behöver du bara ange termen, den gäller för alla distributioner av Azure Data Explorer i alla regioner.

Genom att köpa en bokning betalar du för uppmärkningskostnaderna under en period av ett eller tre år. Så fort du köper en reservation debiteras inte längre azure data explorer-markeringsavgifter som matchar reservationsattributen enligt priserna för er betalning. Azure Data Explorer-kluster som redan körs eller de som nyligen har distribuerats får automatiskt förmånen. Den här reservationen täcker inte beräknings-, nätverks- eller lagringsavgifter som är associerade med kluster. Reserverad kapacitet för dessa resurser måste köpas separat. I slutet av bokningsperioden upphör faktureringsförmånen och Azure Data Explorer-uppmärkningsenheterna faktureras till priset för betalning. Bokningar förnyas inte automatiskt. Prisinformation finns på [prissidan för Azure Data Explorer](https://azure.microsoft.com/pricing/details/data-explorer/).

Du kan köpa reserverad azure data explorer-kapacitet i [Azure-portalen](https://portal.azure.com). Så här köper du reserverad kapacitet för Azure Data Explorer:

* Du måste vara ägare till minst en Enterprise- eller Pay-As-You-Go-prenumeration.
* För Enterprise-prenumerationer måste **Lägg till reserverade instanser** vara aktiverat i [EA-portalen](https://ea.azure.com). Om den inställningen är inaktiverad måste du också vara EA-administratör för prenumerationen.
* För CSP-programmet (Cloud Solution Provider) kan endast administratörsagenter eller försäljningsagenter köpa reserverad azure data explorer-kapacitet.

Mer information om hur företagskunder och pay-as-you-go-kunder debiteras för bokningsköp finns i:
* [Förstå Azure-reservationsanvändning för din Enterprise-registrering](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) 
* [Förstå Azure-reservationsanvändning för din prenumeration på användningsbaserad betalning](../cost-management-billing/reservations/understand-reserved-instance-usage.md).

## <a name="determine-the-right-markup-usage-before-purchase"></a>Bestäm rätt markeringsanvändning före köp

Storleken på reservationen bör baseras på det totala antalet Azure Data Explorer-uppmärkningsenheter som används av befintliga eller snart distribuerade Azure Data Explorer-kluster. Antalet påläggsenheter är lika med antalet Azure Data Explorer-motorklusterkärnor i produktion (exklusive dev/test SKU). 

## <a name="buy-azure-data-explorer-reserved-capacity"></a>Köpa reserverad azure data explorer-kapacitet

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **Alla tjänster** > **Bokningar** > Köp**nu**. Välj **Lägg till**
1. I fönstret **Välj produkttyp** väljer du **Azure Data Explorer** för att köpa en ny reservation för Azure Data Explorer-markeringsenheter. 
1. Välj **Köp**
1. Fyll i de obligatoriska fälten. 

    ![Köpsida](media/pricing-reserved-capacity/purchase-page.png)

1. Granska kostnaden för azure data explorer-uppmärkningsreserverad kapacitetsreservation i avsnittet **Kostnader.**
1. Välj **Köp**.
1. Välj **Visa den här reservationen** om du vill se status för ditt köp.

## <a name="cancellations-and-exchanges"></a>Annulleringar och utbyten

Om du behöver avbryta din reserverade azure data explorer-reservation kan det finnas en avgift på 12 % förtida uppsägning. Återbetalningarna baseras på det lägsta priset på ditt inköpspris eller det aktuella priset för bokningen. Återbetalningar begränsas till 50 000 USD per år. Din återbetalning består av det återstående proportionellt beräknade saldot minus uppsägningsavgiften på 12 %. Om du vill begära en avbokning går du till reservationen i Azure-portalen och väljer **Återbetalning** för att skapa en supportbegäran.

Om du behöver ändra reservationen för reserverad kapacitet i Azure Data Explorer till en annan term kan du byta ut den mot en annan reservation som är av samma eller högre värde. Startdatumet för den nya reservationens överförs inte från den utbytte reservationen. Den 1 eller 3-åriga terminen börjar från när du skapar den nya reservationen. Om du vill begära ett utbyte går du till reservationen i Azure-portalen och väljer **Exchange** för att skapa en supportbegäran.

Mer information om hur du byter eller återbetalar bokningar finns i [Bokningsutbyten och återbetalningar](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="manage-your-reserved-capacity-reservation"></a>Hantera din reservation för reserverad kapacitet

Förbehållrabatten för Azure Data Explorer-uppslagsenheter tillämpas automatiskt på antalet påläggsenheter som matchar Azure Data Explorer reserverade kapacitetsreservationsomfattningar och attribut. 


> [!NOTE]
> * Du kan uppdatera omfattningen av azure data explorer reserverad kapacitet reservation via [Azure-portalen](https://portal.azure.com), PowerShell, CLI eller via API.
> * Mer information om hur du hanterar reserverad kapacitetsreservation för Azure Data Explorer finns i [hantera reserverad kapacitet för Azure Data Explorer](../cost-management-billing/reservations/understand-azure-data-explorer-reservation-charges.md).

## <a name="next-steps"></a>Nästa steg

Du kan läsa mer om Azure-reservationer i följande artiklar:

* [Vad är Azure-reservationer?](../cost-management-billing/reservations/save-compute-costs-reservations.md)
* [Hantera Azure-reservationer](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
* [Förstå reservationsrabatter i Azure](../cost-management-billing/reservations/understand-reservation-charges.md)
* [Förstå reservationsanvändning för din Betala per användning-prenumeration](../cost-management-billing/reservations/understand-reserved-instance-usage.md)
* [Förstå reservationsanvändning för din Enterprise-registrering](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
* [Azure-reservationer i ett CSP-program (Cloud Solution Provider) i Partnercenter](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
