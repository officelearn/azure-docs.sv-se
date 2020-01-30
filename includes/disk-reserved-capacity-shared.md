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
ms.openlocfilehash: 89586f932ee358664a2869c87ced72594336b404
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76847139"
---
Spara på din Premium SSD-användning med reserverad kapacitet, kombinerat med reserverade instanser av virtuella datorer du kan minska dina totala kostnader för virtuella datorer. Reservations rabatten tillämpas automatiskt på de matchande diskarna i det valda reservations omfånget. du behöver inte tilldela någon reservation till en hanterad disk för att få rabatterna. Rabatter tillämpas per timme för disk användning och eventuell oanvänd reserverad kapacitet som inte överförts. Den hanterade disk reservations rabatten gäller inte för ohanterade diskar, Ultra disks eller Page BLOB-förbrukning.

## <a name="determine-your-storage-needs"></a>Fastställ dina lagrings behov

Innan du köper en reservation bör du fastställa vad dina lagrings behov är. För närvarande är disk reservation endast tillgängligt för utvalda Premium SSD-SKU: er. SKU: n för en Premium SSD fastställer diskens storlek och prestanda. När du fastställer dina lagrings behov rekommenderar vi inte att du tänker på diskar precis som en total kapacitet, du kan inte använda en reservation för en större disk (t. ex. en P40) och använda den för att betala för två mindre diskar (P30). När du köper en reservation köper du bara det totala antalet diskar per SKU.

Disk reservation görs per disk-SKU, och därför baseras reservations förbrukningen på enheten för disk-SKU: er i stället för den angivna storleken. Om du till exempel har reserverat 1 P40 av 2 TiB-allokerad kapacitet men bara allokerade 2 P30 diskar debiteras inte de två P30-förbrukningen för P40 reservation och du betalar priset för betala per användning.



[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

## <a name="purchase-considerations"></a>Köp överväganden

Vi rekommenderar följande metod tips när du överväger disk reservations köp:

- Analysera användnings informationen för att avgöra vilka reservationer du bör köpa. Se till att du spårar användningen i disk-SKU: er i stället för en disk kapacitet som har allokerats eller använts. 
- Undersök disk reservationen tillsammans med din VM-reservation. Vi rekommenderar starkt att du gör reservationer för både VM och disk användning för maximalt sparande. Du kan börja med att fastställa rätt VM-reservation och sedan utvärdera disk reservationen på motsvarande sätt. Normalt har du en standard konfiguration för var och en av dina arbets belastningar, till exempel kan en SQL Server ha två P40-datadiskar och en P30 OS-disk. Den här typen av mönster kan hjälpa dig att avgöra hur många reservationer du kan köpa. Den här metoden kan under lätta utvärderings processen och se till att du har en justerad plan för både VM och diskar med avseende på prenumerationer, regioner och andra. 

## <a name="purchase-restrictions"></a>Köp begränsningar

Reservations rabatter är för närvarande inte tillgängliga för följande diskar:
- Ohanterade diskar/sid-blobar
- Standard SSD eller standard-HDD
- Premium SSD SKU: er som är mindre än P30 – reservationer är inte tillgängliga för P1/P2/P3/P4/P6/P10/p15/P20 Premium SSD SKU: er.
- Moln – reservationer är inte tillgängliga för köp i Azure gov-, Tyskland-eller Kina-regioner.
- Kapacitets begränsningar – i sällsynta fall begränsar Azure köpet av nya reservationer för en delmängd disk SKU: er, på grund av låg kapacitet i en region.

## <a name="buy-a-disk-reservation"></a>Köp en disk reservation

Du kan köpa Azure disk-reservationer via [Azure Portal](https://portal.azure.com/). Du kan antingen betala för reservationen fram eller med månads betalningar. Mer information om hur du köper med månads betalningar finns i [inköps reservationer med månads betalningar](../articles/cost-management-billing/reservations/monthly-payments-reservations.md).

Följ dessa steg för att köpa reserverad kapacitet:

1. Gå till bladet med [inköps reservationer](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand) i Azure Portal.
1. Välj **Azure-Managed disks** för att köpa en reservation.

    ![disks-Reserved-Purchase-reservation. png](media/disks-reserved-capacity/disks-reserved-purchase-reservation.png)    

1. Fyll i de obligatoriska fälten enligt beskrivningen i följande tabell:

   |Field  |Beskrivning  |
   |---------|---------|
   |**Omfång**   |  Anger hur många prenumerationer som kan använda den fakturerings förmån som är kopplad till reservationen. Den styr också hur reservationen tillämpas på vissa prenumerationer. <br/><br/> Om du väljer **delad**tillämpas reservations rabatten på Azure Storage kapacitet i en prenumeration i din fakturerings kontext. Fakturerings kontexten baseras på hur du registrerade dig för Azure. För företags kunder är det delade omfånget registreringen och innehåller alla prenumerationer i registreringen. För kunder som betalar per användning inkluderar det delade omfånget alla enskilda prenumerationer med priser enligt principen betala per användning som har skapats av konto administratören.  <br/><br/>  Om du väljer **enskild prenumeration**tillämpas reservations rabatten på Azure Storage kapacitet i den valda prenumerationen. <br/><br/> Om du väljer **enskild resurs grupp**tillämpas reservations rabatten på Azure Storage kapacitet i den valda prenumerationen och den valda resurs gruppen i den prenumerationen. <br/><br/> Du kan ändra reservations omfånget när du har köpt reservationen.  |
   |**Prenumeration**  | Den prenumeration som används för att betala för Azure Storage reservationen. Betalnings metoden för den valda prenumerationen används för att debitera kostnaderna. Prenumerationen måste vara någon av följande typer: <br/><br/>  Enterprise-avtal (erbjudande nummer: MS-AZR-0017P eller MS-AZR-0148P): för en företags prenumeration dras avgifterna från registreringen av betalnings åtagande belopp eller debiteras som överanvändning. <br/><br/> Individuell prenumeration med priser enligt principen betala per användning (erbjudande nummer: MS-AZR-0003P eller MS-AZR-0023P): för en enskild prenumeration med taxan betala per användning debiteras avgifterna till kredit kortet eller faktura betalnings metoden för prenumerationen.    |
   | **Diskar** | Den SKU som du vill skapa. |
   | **Region** | Den region där reservationen gäller. |
   | **Fakturerings frekvens** | Anger hur ofta kontot debiteras för reservationen. Alternativen omfattar *varje månad* eller i *förskott*. |

    ![Premium-SSD-Reserved-Purchase-Selection. png](media/disks-reserved-capacity/premium-ssd-reserved-purchase-selection.png)

1. När du har valt parametrar för din reservation, visar Azure Portal kostnaden. Portalen visar också rabatt procenten för betalning per användning. Välj **Nästa** för att fortsätta till bladet **köp bokningar** .

1. I fönstret **inköps reservationer** kan du namnge din reservation och välja den totala mängden reservationer som du vill göra. Antalet reservationer mappar till antalet diskar. Om du till exempel vill reservera hundra diskar ändrar du **antalet** till 100.
1. Granska den totala kostnaden för reservationen.

    ![Premium-SSD-Reserved-selecting-SKU-total-Purchase. png](media/disks-reserved-capacity/premium-ssd-reserved-selecting-sku-total-purchase.png)

När du har köpt en reservation tillämpas den automatiskt på alla befintliga Azure disk Storage-resurser som matchar villkoren i reservationen. Om du inte har skapat några Azure disk Storage-resurser än, kommer reservationen att gälla när du skapar en resurs som matchar villkoren i reservationen. I båda fallen börjar reservations perioden omedelbart efter ett lyckat köp.

## <a name="exchange-or-refund-a-reservation"></a>Exchange eller återbetala en reservation

Med vissa begränsningar kan du byta ut eller återbetala en reservation.

Om du vill byta ut eller återbetala en reservation navigerar du till reservations informationen i Azure Portal. Välj **Exchange eller åter betalning**och följ anvisningarna för att skicka en support förfrågan. När begäran har bearbetats skickar Microsoft ett e-postmeddelande till dig för att bekräfta att begäran har slutförts.

Mer information om Azure Reservations-principer finns i [självbetjänings utbyte och åter betalningar för Azure reservations](../articles/cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

### <a name="exchange-a-reservation"></a>Utbyte av en reservation

Genom att byta en reservation kan du få en proportionell åter betalning som baseras på den outnyttjade delen av reservationen. Du kan sedan tillämpa åter betalningen på inköps priset för en ny Azure disk-reservation.
Det finns ingen gräns för antalet byten. Det finns inte heller någon avgift för byten. Den nya reservationen som du köper måste vara lika med eller större än den proportionella krediten från den ursprungliga reservationen. En Azure disk-reservation kan bara bytas ut för en annan Azure disk reservation och inte för en reservation för någon annan Azure-tjänst.

### <a name="refund-a-reservation"></a>Återbetala en reservation

Du kan när som helst avbryta en Azure disk-reservation. Om du avbryter får du en proportionell åter betalning som baseras på den återstående reservations perioden, minus en avgift för tidig uppsägning på 12 procent. Den högsta bidrags satsen per år är $50 000.
Om du avbryter en reservation avslutas omedelbart reservationen och resten av månaderna returneras till Microsoft. Det återstående proportionella saldot minus avgiften återbetalas till din ursprungliga köp form.

## <a name="expiration-of-a-reservation"></a>Förfallo datum för en reservation

När en reservation går ut debiteras all Azure disk-kapacitet som du använder under den reservationen enligt priset för betala per användning. Reservationer förnyas inte automatiskt.
Du får ett e-postmeddelande 30 dagar innan reservationen upphör att gälla och sedan på förfallo datumet. Om du vill fortsätta att dra nytta av de kostnads besparingar som en reservation ger, förnyar du den inte senare än förfallo datumet.

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

- [Vad är Azure-reservationer?](../articles/cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Förstå hur reservations rabatten tillämpas på Azure-disklagring](../articles/cost-management-billing/reservations/understand-disk-reservations.md)