---
title: Optimera kostnaderna för Blob-lagring med reserverad kapacitet - Azure Storage
description: Lär dig mer om hur du köper reserverad azure storage-kapacitet för att spara kostnader för blockblobb och Azure Data Lake Storage Gen2-resurser.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: e73686629de8481f6a37e5bfafc9b723206b4853
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351031"
---
# <a name="optimize-costs-for-blob-storage-with-reserved-capacity"></a>Optimera kostnader för blobblagring med reserverad kapacitet

Du kan spara pengar på lagringskostnader för blob-data med reserverad azure storage-kapacitet. Reserverad azure storage-kapacitet ger dig rabatt på kapacitet för blockblobar och för Azure Data Lake Storage Gen2-data i standardlagringskonton när du genomför en reservation för antingen ett år eller tre år. En reservation ger en fast mängd lagringskapacitet för bokningsperioden.

Reserverad azure storage-kapacitet kan avsevärt minska dina kapacitetskostnader för blockblobar och Azure Data Lake Storage Gen2-data. Vilka kostnadsbesparingar som uppnås beror på bokningens varaktighet, den totala kapacitet du väljer att reservera och åtkomstnivån och typen av redundans som du har valt för ditt lagringskonto. Reserverad kapacitet ger en faktureringsrabatt och påverkar inte tillståndet för dina Azure Storage-resurser.

Information om Azure Storage-reservationspriser finns i [Prissättning för blockblobar](https://azure.microsoft.com/pricing/details/storage/blobs/) och [Azure Data Lake Storage Gen 2-prissättning](https://azure.microsoft.com/pricing/details/storage/data-lake/).

## <a name="reservation-terms-for-azure-storage"></a>Bokningsvillkor för Azure Storage

I följande avsnitt beskrivs villkoren för en Azure Storage-reservation.

### <a name="reservation-capacity"></a>Bokningskapacitet

Du kan köpa reserverad azure storage-kapacitet i enheter på 100 TB och 1 PB per månad under en ettårs- eller treårsperiod.

### <a name="reservation-scope"></a>Reservation omfattning

Reserverad azure storage-kapacitet är tillgänglig för en enskild prenumeration eller för flera prenumerationer (delat scope). När den begränsas till en enskild prenumeration tillämpas reservationsrabatten endast på den valda prenumerationen. När du är begränsad till flera prenumerationer delas reservationsrabatten mellan dessa prenumerationer inom kundens faktureringskontext.

När du köper reserverad azure storage-kapacitet kan du använda din reservation för både block blob- och Azure Data Lake Storage Gen2-data. En reservation tillämpas på din användning inom det inköpta scopet och kan inte begränsas till ett visst lagringskonto, en behållare eller ett objekt i prenumerationen. Det går inte att dela upp en reservation mellan flera prenumerationer.

En Azure Storage-reservation täcker endast mängden data som lagras i en prenumeration eller delad resursgrupp. Avgifter för tidig borttagning, åtgärder, bandbredd och dataöverföring ingår inte i reservationen. Så fort du köper en bokning debiteras de kapacitetsavgifter som matchar bokningsattributen till rabatterade priser i stället för till priserna för att betala per dun. Mer information om Azure-reservationer finns i [Vad är Azure-reservationer?](/azure/billing/billing-save-compute-costs-reservations)

### <a name="supported-account-types-tiers-and-redundancy-options"></a>Kontotyper, nivåer och redundansalternativ som stöds

Reserverad azure storage-kapacitet är tillgänglig för resurser i standardlagringskonton, inklusive allmänna v2-konton (GPv2) och Blob-lagring.

Alla åtkomstnivåer (hot, cool och arkiv) stöds för reservationer. Mer information om åtkomstnivåer finns i [Azure Blob storage: hot, cool och archive access tiers](storage-blob-storage-tiers.md).

Alla typer av redundans stöds för reservationer. Mer information om redundansalternativ finns i [Azure Storage redundans](../common/storage-redundancy.md).

> [!NOTE]
> Reserverad azure storage-kapacitet är inte tillgänglig för premiumlagringskonton, allmänt v1-lagringskonton (GPv1), Azure Data Lake Storage Gen1, sidblobar, Azure-kölagring, Azure Table storage eller Azure-filer.  

### <a name="security-requirements-for-purchase"></a>Säkerhetskrav för inköp

Så här köper du reserverad kapacitet:

- Du måste vara i **rollen Ägare** för minst ett företag eller en enskild prenumeration med priser för användningsbaserad betalning.
- För Enterprise-prenumerationer måste **Lägg till reserverade instanser** vara aktiverat i EA-portalen. Om den inställningen är inaktiverad måste du också vara EA-administratör för prenumerationen.
- För CSP-programmet (Cloud Solution Provider) kan endast administratörsagenter eller försäljningsagenter köpa reserverad azure blob-lagringskapacitet.

## <a name="determine-required-capacity-before-purchase"></a>Bestäm önskad kapacitet före inköp

När du köper en Azure Storage-reservation måste du välja alternativet region, åtkomstnivå och redundans för reservationen. Din reservation gäller endast för data som lagras i den regionen, åtkomstnivå och redundansnivå. Anta till exempel att du köper en reservation för data i US West för den aktiva nivån med hjälp av zonundant lagring (ZRS). Du kan inte använda samma reservation för data i USA Öst, data på arkivnivån eller data i geoupptundsslad lagring (GRS). Du kan dock köpa en ny bokning för dina ytterligare behov.  

Bokningar finns idag för 100 TB eller 1 PB block, med högre rabatter för 1 PB block. När du köper en reservation i Azure-portalen kan Microsoft ge dig rekommendationer baserat på din tidigare användning för att avgöra vilken reservation du ska köpa.

## <a name="purchase-azure-storage-reserved-capacity"></a>Inköp av reserverad azure-lagringskapacitet

Du kan köpa reserverad azure storage-kapacitet via [Azure-portalen](https://portal.azure.com). Betala för reservationen i förväg eller via månadsbetalningar. Mer information om hur du köper med månatliga betalningar finns i [Köpa Azure-reservationer med förskottsbetalningar eller månatliga betalningar](/azure/billing/billing-monthly-payments-reservations).

Mer information om hur du identifierar de bokningsvillkor som är rätt för ditt scenario finns i [Förstå rabatten för reserverad azure storage-kapacitet](../../cost-management-billing/reservations/understand-storage-charges.md).

Så här köper du reserverad kapacitet:

1. Navigera till fönstret [Inköpsreservationer](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand) i Azure-portalen.  
1. Välj **Azure Blob Storage** för att köpa en ny reservation.  
1. Fyll i de obligatoriska fälten enligt beskrivningen i följande tabell:

    ![Skärmbild som visar hur du köper reserverad kapacitet](media/storage-blob-reserved-capacity/select-reserved-capacity.png)

   |Field  |Beskrivning  |
   |---------|---------|
   |**Omfång**   |  Anger hur många prenumerationer som kan använda faktureringsförmånen som är kopplad till reservationen. Den styr också hur reservationen tillämpas på specifika prenumerationer. <br/><br/> Om du väljer **Delad**tillämpas reservationsrabatten på Azure Storage-kapacitet i alla prenumerationer i din faktureringskontext. Faktureringskontexten baseras på hur du registrerade dig för Azure. För företagskunder är det delade omfånget registreringen och inkluderar alla prenumerationer i registreringen. För kunder med användningsbaserad betalning omfattar det delade scopet alla enskilda prenumerationer med priser för användningsbaserad betalning som skapats av kontoadministratören.  <br/><br/>  Om du väljer **En prenumeration**tillämpas reservationsrabatten på Azure Storage-kapacitet i den valda prenumerationen. <br/><br/> Om du väljer **En resursgrupp**tillämpas reservationsrabatten på Azure Storage-kapacitet i den valda prenumerationen och den valda resursgruppen i den prenumerationen. <br/><br/> Du kan ändra reservationsomfånget när du har köpt reservationen.  |
   |**Prenumeration**  | Prenumerationen som används för att betala för Azure Storage-reservationen. Betalningsmetoden för den valda prenumerationen används för att debitera kostnaderna. Prenumerationen måste vara någon av följande typer: <br/><br/>  Enterprise Agreement (erbjudandenummer: MS-AZR-0017P eller MS-AZR-0148P): För en Enterprise-prenumeration dras avgifterna från registreringens ekonomiska åtagandesaldo eller debiteras som överfall. <br/><br/> Individuell prenumeration med abonnemang med abonnemang per användning (erbjudandenummer: MS-AZR-0003P eller MS-AZR-0023P): För en enskild prenumeration med abonnemang som gäller debiteras avgifterna till kreditkorts- eller fakturabetalningsmetoden för prenumerationen.    |
   | **Regionen** | Den region där reservationen gäller. |
   | **Åtkomstnivå** | Åtkomstnivån där reservationen är i kraft. Alternativen inkluderar *Hot*, *Cool*eller *Arkiv*. Mer information om åtkomstnivåer finns i [Azure Blob storage: hot, cool och archive access tiers](storage-blob-storage-tiers.md). |
   | **Redundans** | Alternativet för redundans för reservationen. Alternativen inkluderar *LRS,* *ZRS,* *GRS*och *RA-GZRS*. Mer information om redundansalternativ finns i [Azure Storage redundans](../common/storage-redundancy.md). |
   | **Faktureringsfrekvens** | Anger hur ofta kontot faktureras för reservationen. Alternativen inkluderar *månads-* eller *upfront.* |
   | **Storlek** | Den region där reservationen gäller. |
   |**Benämna**  | Ett år eller tre år.   |

1. När du har valt parametrarna för din bokning visar Azure-portalen kostnaden. Portalen visar också rabattprocenten jämfört med betalning per betalning.Portal also shows the discount percentage over pay-as-you-go billing.

1. Granska den totala kostnaden för reservationen i fönstret **Inköpsreservation.** Du kan också ange ett namn för bokningen.

    ![Skärmbild som visar hur du köper en reservation](media/storage-blob-reserved-capacity/purchase-reservations.png)

När du har köpt en reservation tillämpas den automatiskt på alla befintliga Azure Storage-blockblob eller Azure Data Lake Storage Gen2-resurser som matchar villkoren för reservationen. Om du inte har skapat några Azure Storage-resurser ännu gäller reservationen när du skapar en resurs som matchar villkoren för reservationen. I båda fallen börjar bokningsperioden omedelbart efter ett lyckat köp.

## <a name="exchange-or-refund-a-reservation"></a>Byt ut eller återbetala en bokning

Du kan byta eller återbetala en bokning, med vissa begränsningar. Dessa begränsningar beskrivs i följande avsnitt.

Om du vill byta eller återbetala en reservation navigerar du till bokningsinformationen i Azure-portalen. Välj **Exchange** eller **Refund**och följ instruktionerna för att skicka en supportbegäran. När begäran har behandlats skickar Microsoft ett e-postmeddelande till dig för att bekräfta att begäran har slutförts.

Mer information om Azure-principer finns i [Självbetjäningsutbyten och återbetalningar för Azure-bokningar](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

### <a name="exchange-a-reservation"></a>Byt en reservation

Genom att byta ut en bokning kan du få en proportionell återbetalning baserat på den oanvända delen av bokningen. Du kan sedan tillämpa återbetalningen på inköpspriset för en ny Azure Storage-reservation.

Det finns ingen gräns för hur många utbyten du kan göra. Dessutom finns det ingen avgift i samband med ett utbyte. Den nya reservationen som du köper måste vara av samma eller större värde än den proportionella krediten från den ursprungliga reservationen. En Azure Storage-reservation kan endast bytas ut mot en annan Azure Storage-reservation och inte för en reservation för någon annan Azure-tjänst.

### <a name="refund-a-reservation"></a>Återbetala en bokning

Du kan avbryta en Azure Storage-reservation när som helst. När du avbokar får du en proportionell återbetalning baserat på den återstående perioden av bokningen, minus en avgift på 12 procent i förtid. Den maximala återbetalningen per år är $ 50.000.

Avbokning av en bokning avslutar omedelbart bokningen och returnerar de återstående månaderna till Microsoft. Resterande proportionellt saldo, minus avgiften, återbetalas till din ursprungliga inköpsform.

## <a name="expiration-of-a-reservation"></a>Utgången av en reservation

När en reservation går ut debiteras all Azure Storage-kapacitet som du använder under den reservationen till priset för användningsbaserad betalning. Reservationer förnyas inte automatiskt.

Du kommer att få ett e-postmeddelande 30 dagar före utgången av reservationen, och igen på utgångsdatumet. Om du vill fortsätta dra nytta av kostnadsbesparingarna som en reservation ger förnyar du den senast utgångsdatumet.

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

- [Vad är Azure-reservationer?](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Förstå hur reservationsrabatten tillämpas för Azure Storage](../../cost-management-billing/reservations/understand-storage-charges.md)
