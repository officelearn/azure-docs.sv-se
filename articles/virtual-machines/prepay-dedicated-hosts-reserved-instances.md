---
title: Förskottsbetalning för Azure Dedikerade värdar för att spara pengar
description: Lär dig hur du köper reserverade Azure-värdar-instanser för att spara på dina beräkningskostnader.
services: virtual-machines
author: yashar
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/28/2020
ms.author: banders
ms.openlocfilehash: 57123abfe7f343a75d264d43afb88f9de1409e8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78207750"
---
# <a name="save-costs-with-a-reserved-instance-of-azure-dedicated-hosts"></a>Spara kostnader med en reserverad instans av Azure Dedikerade värdar

När du åtar dig en reserverad instans av Azure Dedicated Hosts kan du spara pengar. Reservationsrabatten tillämpas automatiskt på antalet löpande dedikerade värdar som matchar reservationsomfånget och attributen. Du behöver inte tilldela en reservation till en dedikerad värd för att få rabatter. Ett inköp av reserverade instanser täcker endast beräkningsdelen av din användning och inkluderar kostnader för programvarulicensiering. Se [översikten över Azure Dedicated Hosts för virtuella datorer](https://docs.microsoft.com/azure/virtual-machines/windows/dedicated-hosts).

## <a name="determine-the-right-dedicated-host-sku-before-you-buy"></a>Bestäm rätt dedikerad värd SKU innan du köper


Innan du köper en bokning bör du bestämma vilken dedikerad värd du behöver. En SKU har definierats för en dedikerad värd som representerar VM-serien och typen. 

Börja med att gå igenom de storlekar som stöds för [Windows virtuella dator](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) eller [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) för att identifiera VM-serien.

Kontrollera sedan om det stöds på Azure Dedicated Hosts. [Azure Dediced Hosts prissida](https://aka.ms/ADHPricing) har en komplett lista över dedikerade värdar SKU: er, deras CPU-information och olika prisalternativ (inklusive reserverade instanser).

Du kan hitta flera SKU:er som stöder din VM-serie (med olika typer). Identifiera den bästa SKU:n genom att jämföra värdens kapacitet (antal virtuella processorer). Observera att du kommer att kunna tillämpa din bokning på flera dedikerade värdar SKU:er som stöder samma VM-serie (till exempel DSv3_Type1 och DSv3_Type2) men inte över olika VM-serier (som DSv3 och ESv3).



## <a name="purchase-restriction-considerations"></a>Köpbegränsningsöverväganden

Reserverade instanser är tillgängliga för de flesta dedikerade värdstorlekar, med vissa undantag.

Bokningsrabatter gäller inte för följande:

- **Moln** - Bokningar är inte tillgängliga för köp i Tyskland eller Kina regioner.

- **Otillräcklig kvot** - En reservation som är begränsad till en enda prenumeration måste ha vCPU-kvot tillgänglig i prenumerationen för den nya reserverade instansen. Om målprenumerationen till exempel har en kvotgräns på 10 virtuella processorer för DSv3-serien kan du inte köpa en dedikerad support för reservationen som stöder den här serien. Kvotkontrollen för reservationer inkluderar de virtuella datorer och dedikerade värdar som redan distribuerats i prenumerationen. Du kan skapa begäran  [om kvotökning](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)för att lösa problemet.

- **Kapacitetsbegränsningar** – I sällsynta fall begränsar Azure inköp av nya reservationer för delmängd av dedikerade värd-SKU:er, på grund av låg kapacitet i en region.

## <a name="buy-a-reservation"></a>Köpa en reservation

Du kan köpa en reserverad instans av en Azure Dedicated Host-instans i [Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D).

Betala för bokningen [i förskott eller med månatliga betalningar.](https://docs.microsoft.com/azure/billing/billing-monthly-payments-reservations) Dessa krav gäller för att köpa en reserverad dedikerad värdinstans:

- Du måste ha en ägarroll för minst en EA-prenumeration eller en prenumeration med en användningsbaserad betalning.

- För EA-prenumerationer måste alternativet **Lägg till reserverade instanser** vara aktiverat i [EA-portalen](https://ea.azure.com/). Om den inställningen är inaktiverad måste du vara EA-administratör för prenumerationen.

- För CSP-programmet (Cloud Solution Provider) kan endast administratörsagenter eller försäljningsagenter köpa reservationer.

Så här köper du en instans:

1. Logga in på  [Azure-portalen](https://portal.azure.com/).

2. Välj \> **Alla** **servicereservationer**.

3. Välj **Lägg till** om du vill köpa en ny reservation och klicka sedan på **Dedikerade värdar**.

4. Fyll i obligatoriska fält. Köra dedikerade hosts-instanser som matchar de attribut du väljer kvalificerar för att få reservationsrabatten. Det faktiska antalet dedikerade värdinstanser som får rabatten beror på omfattningen och kvantiteten som valts.

Om du har ett EA-avtal kan du använda **alternativet** Lägg till fler för att snabbt lägga till ytterligare instanser. Alternativet är inte tillgängligt för andra prenumerationstyper.

| **Field**           | **Beskrivning**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|---------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Prenumeration        | Prenumerationen användes för att betala för bokningen. Betalningsmetoden för prenumerationen debiteras kostnaderna för bokningen. Prenumerationstypen måste vara ett företagsavtal (erbjudandenummer: MS-AZR-0017P eller MS-AZR-0148P) eller Microsoft Kundavtal eller en individuell prenumeration med abonnemang som gäller (erbjudandenummer: MS-AZR-0003P eller MS-AZR-0023P). Avgifterna dras av från det monetära åtagandesaldot, om tillgängligt, eller debiteras som överfall. För en prenumeration med användningsbaserad betalning debiteras avgifterna till kreditkorts- eller fakturabetalningsmetoden för prenumerationen. |
| Omfång               | Reservationens omfattning kan omfatta en prenumeration eller flera prenumerationer (delat scope). Om du väljer:                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| Region              | Den Azure-region som omfattas av reservationen.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| Dedikerad värdstorlek | Storleken på dedikerade värdinstanserna.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| Period                | Ett år eller tre år.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| Kvantitet            | Antalet instanser som köps i reservationen. Kvantiteten är antalet löpande dedikerade värdinstanser som kan få faktureringsrabatten.                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

- **En enskild resursgruppomfång** – Tillämpar reservationsrabatten endast på matchande resurser i den valda resursgruppen.

- **En prenumerationsomfattning** – Tillämpar reservationsrabatten på matchande resurser i den valda prenumerationen.

- **Delat scope** – Tillämpar reservationsrabatten för att matcha resurser i kvalificerade prenumerationer som finns i faktureringskontexten. För EA-kunder är faktureringskontexten registreringen. För enskilda prenumerationer med Betala per användning-priser är faktureringsomfånget alla berättigade prenumerationer som skapats av kontoadministratören.

## <a name="usage-data-and-reservation-utilization"></a>Användningsdata och reservationsanvändning

Dina användningsdata har ett effektivt pris på noll för användning som täcks av reservationen. Du kan se vilken VM-instans som fick reservationsrabatten för varje reservation.

Mer information om hur reservationsrabatter visas i användningsdata finns i [Förstå Azure-reservationsanvändning för din Enterprise-registrering](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) om du är EA-kund. Om du har en enskild prenumeration läser du [Förstå Azure-reservationsanvändning för din prenumeration på användningsbaserad betalning](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage).

## <a name="change-a-reservation-after-purchase"></a>Ändra en reservation efter köpet

Du kan göra följande typer av ändringar i en reservation efter köpet:

- Uppdatera reservationsomfång

- Instansstorleksflexibilitet (om tillämpligt)

- Ägarskap

Du kan också dela upp en reservation i mindre segment och sammanfoga redan delade reservationer. Ingen av ändringarna orsakar en ny affärstransaktion eller ändrar slutdatum för reservationen.

Du kan inte göra följande typer av ändringar efter köpet direkt:

- En befintlig reservation region

- SKU

- Kvantitet

- Varaktighet

Du kan dock *byta* en reservation om du vill göra ändringar.

## <a name="cancel-exchange-or-refund-reservations"></a>Avbryta, byta ut eller återbetala reservationer

Du kan avbryta, byta ut och återbetala reservationer med vissa begränsningar. Mer information finns i [Självbetjäningsutbyten och återbetalningar för Azure Reservations](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund).

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp [skapar du en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Nästa steg

Mer information om hur du hanterar en reservation finns i [Hantera Azure-reservationer](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance).

Du kan läsa mer om Azure-reservationer i följande artiklar:

- [Vad är Azure-reservationer?](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)

- [Använda Azure Dedikerade värdar](https://docs.microsoft.com/azure/virtual-machines/windows/dedicated-hosts)

- [Dedikerad värdprissättning](https://azure.microsoft.com/pricing/details/virtual-machines/dedicated-host/)

- [Hantera reservationer i Azure](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)

- [Förstå hur reservationsrabatten tillämpas](https://docs.microsoft.com/azure/billing/billing-understand-vm-reservation-charges)

- [Förstå reservationsanvändning för en prenumeration med Betala per användning-priser](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)

- [Förstå reservationsanvändning för din Enterprise-registrering](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)

- [Windows-programvarukostnader ingår inte i bokningar](https://docs.microsoft.com/azure/billing/billing-reserved-instance-windows-software-costs)

- [Azure-reservationer i ett CSP-program (Cloud Solution Provider) i Partnercenter](https://docs.microsoft.com/partner-center/azure-reservations)


