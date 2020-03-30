---
title: ta med fil
description: ta med fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/15/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: cb959b94807678187363d3132ece273584f13a0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77590781"
---
Spara på din Azure Disk Storage-användning med reserverad kapacitet. Azure Disk Storage-reservationer i kombination med Azure Reserved Virtual Machine-instanser gör att du kan sänka dina totala kostnader för virtuella datorer.Azure Disk Storage reservations combined with Azure Reserved Virtual Machine Instances let you lower your total virtual machine (VM) costs. Reservationsrabatten tillämpas automatiskt på matchande diskar i det valda reservationsomfånget. På grund av det här automatiska programmet behöver du inte tilldela en reservation till en hanterad disk för att få rabatterna.

Rabatter tillämpas varje timme beroende på diskanvändningen. Outnyttjad reserverad kapacitet förs inte över. Reservationsrabatter för Azure Disk Storage gäller inte för ohanterade diskar, ultradiskar eller sidblobförbrukning.

## <a name="determine-your-storage-needs"></a>Bestäm dina lagringsbehov

Innan du köper en bokning bestämmer du dina lagringsbehov. För närvarande är Azure Disk Storage-reservationer endast tillgängliga för utvalda Azure premium SSD SKU:er. SKU för en premium SSD bestämmer diskens storlek och prestanda.

När du bestämmer dina lagringsbehov ska du inte tänka på diskar baserat på bara kapacitet. Du kan till exempel inte ha en reservation för en P40-disk och använda den för att betala för två mindre P30-diskar. När du köper en reservation köper du bara en reservation för det totala antalet diskar per SKU.

En diskreservation görs per disk SKU. Därför baseras reservationsförbrukningen på enheten för diskSKU:erna i stället för den angivna storleken.

Anta till exempel att du reserverar en P40-disk som har 2 TiB med etablerad lagringskapacitet. Anta också att du bara allokerar två P30-diskar. P40-bokningen i så fall tar inte hänsyn till P30-förbrukning, och du betalar betala per dur på P30-diskarna.
<br/>
<br/>
[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

## <a name="purchase-considerations"></a>Köpeskillingar

Vi rekommenderar följande metoder när du överväger köp av diskreservation:

- Analysera din användningsinformation för att avgöra vilka reservationer du ska köpa. Se till att du spårar användningen i diskSKU:er i stället för etablerad eller använd diskkapacitet.
- Undersök din diskreservation tillsammans med din VM-reservation. Vi rekommenderar starkt att göra reservationer för både VM-användning och diskanvändning för maximalbesparingar. Du kan börja med att bestämma rätt VM-reservation och sedan utvärdera diskreservationen. I allmänhet har du en standardkonfiguration för var och en av dina arbetsbelastningar. En SQL Server-server kan till exempel ha två P40-datadiskar och en P30-operativsystemdisk.
  
  Den här typen av mönster kan hjälpa dig att bestämma det reserverade belopp du kan köpa. Den här metoden kan förenkla utvärderingsprocessen och se till att du har en justerad plan för både den virtuella datorn och diskarna. Planen innehåller överväganden som prenumerationer eller regioner.

## <a name="purchase-restrictions"></a>Begränsningar för inköp

Reservationsrabatter är för närvarande inte tillgängliga för följande:

- Ohanterat diskar eller sidblobar.
- Standard-SSD-enheter eller vanliga hårddiskar.
- Premium SSD SKU mindre än P30: P1, P2, P3, P4, P6, P10, P15 och P20 SSD SKU: er.
- Diskar i Azure Government, Azure Germany eller Azure China regioner.

I sällsynta fall begränsar Azure inköp av nya reservationer till en delmängd av diskSKU:er på grund av låg kapacitet i en region.

## <a name="buy-a-disk-reservation"></a>Köpa en diskreservation

Du kan köpa Azure Disk Storage-reservationer via [Azure-portalen](https://portal.azure.com/). Du kan betala för bokningen antingen i förskott eller med månatliga betalningar. Mer information om hur du köper med månatliga betalningar finns i [Köpereservationer med månatliga betalningar](../articles/cost-management-billing/reservations/monthly-payments-reservations.md).

Så här köper du reserverad kapacitet:

1. Gå till fönstret [Inköpsreservationer](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand) i Azure-portalen.

1. Välj **Azure Managed Disks** för att köpa en reservation.

    ![Fönsterruta för inköp av bokningar](media/disks-reserved-capacity/disks-reserved-purchase-reservation.png) 

1. Ange de värden som krävs som beskrivs i följande tabell:

   |Element  |Beskrivning  |
   |---------|---------|
   |**Omfång**   |  Hur många prenumerationer kan använda faktureringsförmånen som är kopplad till reservationen. Det här värdet anger också hur reservationen tillämpas på specifika prenumerationer. <br/><br/> Om du väljer **Delad**tillämpas reservationsrabatten på Azure Storage-kapacitet i varje prenumeration i din faktureringskontext. Faktureringskontexten baseras på hur du registrerade dig för Azure. För företagskunder är det delade omfånget registreringen och inkluderar alla prenumerationer i registreringen. För kunder med användningsbaserad betalning omfattar det delade scopet alla enskilda prenumerationer med priser för användningsbaserad betalning som skapats av kontoadministratören.  <br/><br/>  Om du väljer **En prenumeration**tillämpas reservationsrabatten på Azure Storage-kapacitet i den valda prenumerationen. <br/><br/> Om du väljer **En resursgrupp**tillämpas reservationsrabatten på Azure Storage-kapacitet i den valda prenumerationen och i den prenumerationens valda resursgrupp. <br/><br/> Du kan ändra reservationsomfånget när du har köpt reservationen.  |
   |**Prenumeration**  | Prenumerationen som du använder för att betala för Azure Storage-reservationen. Betalningsmetoden för den valda prenumerationen används för att debitera kostnaderna. Prenumerationen måste vara någon av följande typer:<br/><ul><li> Enterprise Agreement (erbjudandenummer MS-AZR-0017P och MS-AZR-0148P). För en Enterprise-prenumeration dras avgifterna från registreringens ekonomiska åtagandesaldo eller debiteras som överfall.</li><br/><li>Individuell prenumeration med abonnemang per användning (erbjudandenummer MS-AZR-0003P och MS-AZR-0023P). För en enskild prenumeration med användningsbaserad betalning debiteras avgifterna till kreditkorts- eller fakturabetalningsmetoden för prenumerationen.</li></ul>    |
   | **Diskar** | Den SKU som du vill skapa. |
   | **Regionen** | Den region där reservationen gäller. |
   | **Faktureringsfrekvens** | Hur ofta kontot faktureras för bokningen. Alternativen inkluderar **månads-** och **upfront.** |

    ![Fönsterruta för att välja den produkt som du vill köpa.png](media/disks-reserved-capacity/premium-ssd-reserved-purchase-selection.png)

1. När du har angett värdena för din bokning visar Azure-portalen kostnaden. Portalen visar också rabattprocenten jämfört med betalning per betalning.Portal also shows the discount percentage over pay-as-you-go billing. Välj **Nästa** om du vill fortsätta till fönstret **Inköpsreservationer.**

1. I fönstret **Köpbokningar** kan du namnge din bokning och välja den totala kvantiteten reservationer som du vill göra. Antalet reservationer kartor till antalet diskar. Om du till exempel vill reservera hundra diskar anger du **kvantitetsvärdet** **100**.

1. Granska den totala kostnaden för reservationen.

    ![Fönstret Inköpsreservationer](media/disks-reserved-capacity/premium-ssd-reserved-selecting-sku-total-purchase.png)

När du har köpt en reservation tillämpas den automatiskt på befintliga disklagringsresurser som matchar bokningsvillkoren. Om du inte har skapat några disklagringsresurser ännu gäller reservationen när du skapar en resurs som matchar reservationsvillkoren. I båda fallen börjar bokningsperioden omedelbart efter ett lyckat köp.

## <a name="cancel-exchange-or-refund-reservations"></a>Avbryta, byta ut eller återbetala reservationer

Du kan avboka, byta eller återbetala bokningar inom vissa begränsningar. Läs mer i [Byten och återbetalning för Azure-reservationer via självbetjäning](https://docs.microsoft.com/azure/cost-management-billing/reservations/exchange-and-refund-azure-reservations).

## <a name="expiration-of-a-reservation"></a>Utgången av en reservation

När en reservation går ut debiteras all Azure Disk Storage-kapacitet som du använder under den reservationen till användningsbaserad betalning. Reservationer förnyas inte automatiskt.

Du får ett e-postmeddelande 30 dagar före reservationens utgång och igen på utgångsdatumet. Om du vill fortsätta dra nytta av kostnadsbesparingarna som en reservation ger förnyar du den senast utgångsdatumet.

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

- [Vad är Azure-reservationer?](../articles/cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Förstå hur reservationsrabatten tillämpas på Azure-disklagring](../articles/cost-management-billing/reservations/understand-disk-reservations.md)
