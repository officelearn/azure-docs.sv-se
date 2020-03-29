---
title: Reserverad kapacitet i Azure Cosmos DB för att optimera kostnaden
description: Lär dig hur du köper Azure Cosmos DB reserverad kapacitet för att spara på dina beräkningskostnader.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: tisande
ms.reviewer: sngun
ms.openlocfilehash: 16e8f770445218e10ab7e7645a81325d11be55da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77505966"
---
# <a name="optimize-cost-with-reserved-capacity-in-azure-cosmos-db"></a>Optimera kostnaden för reserverad kapacitet i Azure Cosmos DB

Reserverad Azure Cosmos DB-kapacitet hjälper dig att spara pengar genom att allokera till en reservation för Azure Cosmos DB-resurser i ett år eller tre år. Med reserverad Azure Cosmos DB-kapacitet kan du få rabatt på det dataflöde som tillhandahålls för Cosmos DB-resurser. Exempel på resurser är databaser och containrar (tabeller, samlingar och diagram).

Azure Cosmos DB reserverad kapacitet kan&mdash;avsevärt minska dina Cosmos DB-kostnader med upp till 65 procent på ordinarie priser med ett års eller tre års förskottsåtagande. Reserverad kapacitet ger en faktureringsrabatt och påverkar inte körningstillståndet för dina Azure Cosmos DB-resurser.

Azure Cosmos DB reserverad kapacitet täcker dataflöde som etablerats för dina resurser. Den omfattar inte avgifter för lagring och nätverk. Så fort du köper en bokning debiteras inte längre de dataflödesavgifter som matchar bokningsattributen enligt priserna för att betala i taget. Mer information om reservationer finns i artikeln [Azure reservations.](../cost-management-billing/reservations/save-compute-costs-reservations.md)

Du kan köpa reserverad azure cosmos DB-kapacitet från [Azure-portalen](https://portal.azure.com). Betala för reservationen [i förväg eller via månadsbetalningar](../cost-management-billing/reservations/monthly-payments-reservations.md). Så här köper du reserverad kapacitet:

* Du måste vara i rollen Ägare för minst ett företag eller en enskild prenumeration med priser för användningsbaserad betalning.  
* För Enterprise-prenumerationer måste **Lägg till reserverade instanser** vara aktiverat i [EA-portalen](https://ea.azure.com). Om den inställningen är inaktiverad måste du också vara EA-administratör för prenumerationen.
* För CSP-programmet (Cloud Solution Provider) kan endast administratörsagenter eller försäljningsagenter köpa reserverad azure Cosmos DB-kapacitet.

## <a name="determine-the-required-throughput-before-purchase"></a>Bestäm önskat dataflöde före köp

Storleken på det reserverade kapacitetsinköpet bör baseras på den totala mängden dataflöde som de befintliga eller snart distribuerade Azure Cosmos DB-resurserna kommer att använda per timme. Till exempel: Köp 30 000 RU/s reserverad kapacitet om det är ditt konsekventa användningsmönster per timme. I det här exemplet debiteras alla etablerade dataflöde över 30 000 RU/s med hjälp av din användningsbaserad betalning. Om etablerat dataflöde är lägre än 30 000 RU/s på en timme, kommer den extra reserverade kapaciteten för den timmen att slösas bort.

Vi beräknar köprekommendationer baserat på ditt användningsmönster per timme. Användning under de senaste 7, 30 och 60 dagarna analyseras och inköp av reserverad kapacitet som maximerar dina besparingar rekommenderas. Du kan visa rekommenderade reservationsstorlekar i Azure-portalen med hjälp av följande steg:

1. Logga in på [Azure-portalen](https://portal.azure.com).  

2. Välj **Alla tjänster** > **Reservation Lägg** > **till**.

3. Välj Azure Cosmos DB i fönstret **Inköpsreservationer**. **Purchase reservations**

4. Välj fliken **Rekommenderad** om du vill visa rekommenderade reservationer:

Du kan filtrera rekommendationer efter följande attribut:

- **Löptid** (1 år eller 3 år)
- **Faktureringsfrekvens** (månads- eller i förskott)
- **Dataflödestyp** (RU:s vs Multi-master RU:s)

Dessutom kan du begränsa rekommendationer för att vara inom en enda resursgrupp, enstaka prenumeration eller hela din Azure-registrering. 

Här är ett exempel rekommendation:

![Rekommendationer för reserverad kapacitet](./media/cosmos-db-reserved-capacity/reserved-capacity-recommendation.png)

Denna rekommendation att köpa en 30.000 RU / s reservation visar att, bland 3 års bokningar, en 30.000 RU / s reservation storlek kommer att maximera besparingar. I det här fallet beräknas rekommendationen baserat på de senaste 30 dagarna av Azure Cosmos DB-användning. Om den här kunden förväntar sig att de senaste 30 dagarna av Azure Cosmos DB-användning är representativa för framtida användning, skulle de maximera besparingarna genom att köpa en 30 000 RU/s-reservation.

## <a name="buy-azure-cosmos-db-reserved-capacity"></a>Köp reserverad azure cosmos DB-kapacitet

1. Logga in på [Azure-portalen](https://portal.azure.com).  

2. Välj **Alla tjänster** > **Reservation Lägg** > **till**.  

3. Välj Azure Cosmos DB i fönstret **Inköpsreservationer** för att köpa en ny reservation. **Azure Cosmos DB**  

4. Fyll i de obligatoriska fälten enligt beskrivningen i följande tabell:

   ![Fyll i formuläret reserverad kapacitet](./media/cosmos-db-reserved-capacity/fill-reserved-capacity-form.png)

   |Field  |Beskrivning  |
   |---------|---------|
   |Omfång   |   Alternativ som styr hur många prenumerationer som kan använda faktureringsförmånen som är kopplad till reservationen. Den styr också hur reservationen tillämpas på specifika prenumerationer. <br/><br/>  Om du väljer **Delad**tillämpas reservationsrabatten på Azure Cosmos DB-instanser som körs i en prenumeration i din faktureringskontext. Faktureringskontexten baseras på hur du registrerade dig för Azure. För företagskunder är det delade omfånget registreringen och inkluderar alla prenumerationer i registreringen. För kunder med användningsbaserad betalning är det delade scopet alla enskilda prenumerationer med priser för användningsbaserad betalning som skapats av kontoadministratören.  <br/><br/>  Om du väljer **En prenumeration**tillämpas reservationsrabatten på Azure Cosmos DB-instanser i den valda prenumerationen. <br/><br/> Om du väljer **En resursgrupp**tillämpas reservationsrabatten på Azure Cosmos DB-instanser i den valda prenumerationen och den valda resursgruppen i den prenumerationen. <br/><br/> Du kan ändra reservationsomfattningen när du har köpt den reserverade kapaciteten.  |
   |Prenumeration  |   Prenumeration som används för att betala för Azure Cosmos DB reserverad kapacitet. Betalningsmetoden för den valda prenumerationen används för att debitera kostnaderna. Prenumerationen måste vara någon av följande typer: <br/><br/>  Enterprise Agreement (erbjudandenummer: MS-AZR-0017P eller MS-AZR-0148P): För en Enterprise-prenumeration dras avgifterna från registreringens ekonomiska åtagandesaldo eller debiteras som överfall. <br/><br/> Individuell prenumeration med abonnemang med abonnemang per användning (erbjudandenummer: MS-AZR-0003P eller MS-AZR-0023P): För en enskild prenumeration med abonnemang som gäller debiteras avgifterna till kreditkorts- eller fakturabetalningsmetoden för prenumerationen.    |
   | Resursgrupp | Resursgrupp som rabatten för reserverad kapacitet tillämpas på. |
   |Period  |   Ett år eller tre år.   |
   |Typ av dataflöde   |  Dataflöde etableras som begäranheter. Du kan köpa en reservation för det etablerade dataflödet för båda inställningarna - skriver en region samt flera regionskrivningar. Dataflödestypen har två värden att välja mellan: 100 RU/s per timme och 100 MULTI-master RU/s per timme.|
   | Enheter med reserverad kapacitet| Mängden dataflöde som du vill reservera. Du kan beräkna det här värdet genom att bestämma vilket dataflöde som behövs för alla cosmos DB-resurser (till exempel databaser eller behållare) per region. Du multiplicerar det sedan med antalet regioner som du ska associera med Cosmos-databasen. Till exempel: Om du har fem regioner med 1 miljon RU/sek i varje region väljer du 5 miljoner RU/sek för inköp av bokningskapacitet. |


5. När du har fyllt i formuläret beräknas det pris som krävs för att köpa den reserverade kapaciteten. Utdata visar också hur stor procentandel rabatt du får med de valda alternativen. Klicka på Markera nästa **gång**

6. Granska rabatten och priset för reservationen i fönstret **Inköpsreservation.** Det här reservationspriset gäller azure cosmos DB-resurser med dataflöde som etablerats i alla regioner.  

   ![Sammanfattning av reserverad kapacitet](./media/cosmos-db-reserved-capacity/reserved-capacity-summary.png)

7. Välj **Granska + köp** och köp sedan **nu**. Du ser följande sida när köpet lyckas:

När du har köpt en reservation tillämpas den omedelbart på befintliga Azure Cosmos DB-resurser som matchar villkoren för reservationen. Om du inte har några befintliga Azure Cosmos DB-resurser gäller reservationen när du distribuerar en ny Cosmos DB-instans som matchar villkoren för reservationen. I båda fallen börjar bokningsperioden omedelbart efter ett lyckat köp.

När din bokning går ut fortsätter dina Azure Cosmos DB-instanser att köras och faktureras enligt de vanliga priserna för användningsbaserad betalning.

## <a name="cancel-exchange-or-refund-reservations"></a>Avbryta, byta ut eller återbetala reservationer

Du kan avbryta, byta ut och återbetala reservationer med vissa begränsningar. Läs mer i [Byten och återbetalning för Azure-reservationer via självbetjäning](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="next-steps"></a>Nästa steg

Reservationsrabatten tillämpas automatiskt på Azure Cosmos DB-resurser som matchar reservationsomfattningen och attributen. Du kan uppdatera omfattningen av reservationen via Azure-portalen, PowerShell, Azure CLI eller API:et.

*  Mer information om hur reserverade kapacitetsrabatter tillämpas på Azure Cosmos DB finns [i Förstå Azure-reservationsrabatten](../cost-management-billing/reservations/understand-cosmosdb-reservation-charges.md).

* Du kan läsa mer om Azure-reservationer i följande artiklar:

   * [Vad är Azure-reservationer?](../cost-management-billing/reservations/save-compute-costs-reservations.md)  
   * [Hantera Azure-reservationer](../cost-management-billing/reservations/manage-reserved-vm-instance.md)  
   * [Förstå reservationsanvändning för din Enterprise-registrering](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)  
   * [Förstå reservationsanvändning för din Betala per användning-prenumeration](../cost-management-billing/reservations/understand-reserved-instance-usage.md)
   * [Azure-reservationer i Partner Center CSP-programmet](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
