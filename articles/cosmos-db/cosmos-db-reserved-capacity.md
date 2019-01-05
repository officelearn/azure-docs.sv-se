---
title: Optimera kostnaden för Azure Cosmos DB-resurser med reserverad kapacitet
description: Lär dig hur du köper Azure Cosmos DB reserverad kapacitet för att spara dina beräkningskostnader.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: b4f36abb2136223be8d4eec67935317041a41b93
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54044288"
---
# <a name="optimize-cost-of-azure-cosmos-db-resources-with-reserved-capacity"></a>Optimera kostnaden för Azure Cosmos DB-resurser med reserverad kapacitet

Azure Cosmos DB reserverad kapacitet kan du spara pengar genom att betala förväg för Azure Cosmos DB-resurserna för antingen ett eller tre år. Du kan få en rabatt på dataflödet som tillhandahållits för Cosmos DB-resurser med Azure Cosmos DB reserverad kapacitet. Exempel på resurser är databaser och behållare (tabeller, samlingar och diagram).

Azure Cosmos DB reserverad kapacitet kan avsevärt minska dina kostnader för Cosmos DB&mdash;upp till 65 procent på vanliga priser med en ettårig eller tre år i förskott. Reserverad kapacitet ger en rabatt på fakturering och påverkar inte runtime-tillståndet för dina Azure Cosmos DB-resurser.

Azure Cosmos DB reserverad kapacitet täcker dataflödet som etableras för dina resurser. Det täcker inte lagring och nätverk avgifter. När du köper en reservation går dataflöde avgifterna som matchar reservationen attribut är inte längre debiteras enligt användningsbaserad-som-du priser. Mer information om reservationer finns i den [Azure reservationer](../billing/billing-save-compute-costs-reservations.md) artikeln. 

Du kan köpa Azure Cosmos DB reserverad kapacitet från den [Azure-portalen](https://portal.azure.com). Köpa reserverad kapacitet:

* Du måste vara i rollen ägare för minst en Enterprise eller användningsbaserad betalning.  
* För Enterprise-prenumerationer Azure reservation inköp måste aktiveras i den [EA-portalen](https://ea.azure.com/).  
* Efter programmet Cloud Solution Provider (CSP), kan endast admin-agenter eller försäljning agents köpa Azure Cosmos DB reserverad kapacitet.

## <a name="determine-the-required-throughput-before-purchase"></a>Fastställa nödvändiga dataflödet innan du köper

Storleken på reservationen ska baseras på den totala mängden dataflöde som Azure Cosmos DB-resurserna befintliga eller snart-till--distribueras ska använda. Du kan fastställa nödvändiga dataflödet på följande sätt:

* Hämta historiska data för totalt etablerat dataflöde i Azure Cosmos DB-konton, databaser och samlingar i alla regioner. Exempel: du kan utvärdera det dagliga genomsnittliga dataflöden genom att ladda ned din dagliga användningsinformation från `https://account.azure.com`.

* Om du är kund med Enterprise Agreement (EA) kan hämta du din användningsfil information för Azure Cosmos DB-dataflöde. Referera till den **tjänsttyp** värde i den **ytterligare information** i användningsfilen.

* Du kan summeras genomsnittlig genomströmning för alla arbetsbelastningar på ditt Azure Cosmos DB-konton som du förväntar dig att köra i bredvid ett eller tre år. Du kan sedan använda den kvantiteten för reservationen.

## <a name="buy-azure-cosmos-db-reserved-capacity"></a>Köp Azure Cosmos DB reserverad kapacitet

1. Logga in på [Azure Portal](https://portal.azure.com).  

2. Välj **alla tjänster** > **reservationer** > **Lägg till**.  

3. Från den **Välj produkttyp** fönstret Välj **Azure Cosmos DB** > **Välj** att köpa en ny reservation.  

4. Fyll i de obligatoriska fälten som beskrivs i följande tabell:

   ![Fyll i formuläret med reserverad kapacitet](./media/cosmos-db-reserved-capacity/fill_reserved_capacity_form.png) 

   |Fält  |Beskrivning  |
   |---------|---------|
   |Namn   |    Namn på reservationen. Det här fältet fylls i automatiskt med `CosmosDB_Reservation_<timeStamp>`. Du kan ange ett annat namn när du skapar reservationen. Eller du kan byta namn på den när reservationen har skapats.      |
   |Prenumeration  |   Prenumeration som används för att betala för Azure Cosmos DB reserverad kapacitet. Den aktuella betalningsmetoden på den valda prenumerationen används i debitera några startkostnader. Prenumerationstypen måste vara något av följande: <br/><br/>  [Enterprise-avtal](https://azure.microsoft.com/pricing/enterprise-agreement/) (erbjudandets nummer: MS-AZR-0017P): För en prenumeration på Enterprise, är kostnaderna som dras av från den registrering saldo för åtagandebelopp eller debiteras som överanvändning. <br/><br/> [Betala per användning](https://azure.microsoft.com/offers/ms-azr-0003p/) (erbjudandets nummer: MS-AZR-0003P): För användningsbaserad betalning debiteras avgifterna till betalningsmetod kreditkort eller faktura för prenumerationen.    |
   |Scope   |   Alternativ som styr hur många prenumerationer kan använda faktureringsförmånen som är associerade med reservationen. Den styr hur reservationen ska gälla för specifika prenumerationer.   <br/><br/>  Om du väljer **enstaka prenumeration**, reservationsrabatten tillämpas på Azure Cosmos DB-instanser i den valda prenumerationen. <br/><br/>  Om du väljer **delad**, reservationsrabatten tillämpas på Azure Cosmos DB-instanser som körs i alla prenumerationer i din faktureringskontexten. Kontexten fakturering baseras på hur du registrerat dig för Azure. För företagskunder, den delade omfattningen registreringen och innehåller alla prenumerationer (utom prenumerationer för utveckling och testning) i registreringen. För kunder med användningsbaserad betalning är den delade omfattningen alla betala per användning-prenumerationer som skapas av kontoadministratören.  <br/><br/> Du kan ändra omfång för reservation när du köper reserverad kapacitet.  |
   |Typ av reserverad kapacitet   |  Dataflödet som etableras som enheter för programbegäran.|
   |Enheter för reserverad kapacitet  |      Mängden dataflöde som du vill reservera. Du kan beräkna det här värdet genom att fastställa det dataflöde som krävs för alla dina Cosmos DB-resurser (till exempel databaser eller behållare) per region. Du sedan multiplicerar den med antalet regioner som ska associeras med din Cosmos DB-databas.  <br/><br/> Exempel: Om du har fem regioner med 1 miljon RU/sek i varje region, väljer du 5 miljoner RU/sek för reservationsköp för kapacitet.    |
   |Period  |   Ett eller tre år.   |

5. Granska rabatten och priset för reservationen i den **kostnader** avsnittet. Den här reservationen priset gäller för Azure Cosmos DB-resurser med dataflöde etablerat i alla regioner.  

6. Välj **Köp**. Följande sida visas när köpet har slutförts: 

   ![Fyll i formuläret med reserverad kapacitet](./media/cosmos-db-reserved-capacity/reserved_capacity_successful.png) 

När du köper en reservation tillämpas omedelbart på alla befintliga Azure Cosmos DB-resurser som matchar villkoren i reservationen. Om du inte har några befintliga resurser i Azure Cosmos DB, gäller reservationen när du distribuerar en ny Cosmos DB-instans som matchar villkoren i reservationen. I båda fallen startas perioden för reservationen direkt efter en lyckad inköp. 

När din reservationen går ut dina Azure Cosmos DB-instanser fortsätter att köras och debiteras enligt de användningsbaserad betalning.

## <a name="next-steps"></a>Nästa steg

Reservationsrabatten tillämpas automatiskt på Azure Cosmos DB-resurserna som matchar reservationsomfånget och attribut. Du kan uppdatera reservationens via Azure portal, PowerShell, Azure CLI eller API: et omfång.

*  Läs hur reserverad kapacitet rabatter till Azure Cosmos DB i [förstå Azure rabatten](../billing/billing-understand-cosmosdb-reservation-charges.md).

* Om du vill veta mer om Azure reservationer, finns i följande artiklar:

   * [Vad är Azure reservationer?](../billing/billing-save-compute-costs-reservations.md)  
   * [Hantera Azure-reservationer](../billing/billing-manage-reserved-vm-instance.md)  
   * [Förstå användningen av reserverade för din Enterprise-registrering](../billing/billing-understand-reserved-instance-usage-ea.md)  
   * [Förstå användningen av reservation för prenumerationen med användningsbaserad betalning](../billing/billing-understand-reserved-instance-usage.md)
   * [Azure reservationer i Partner Center CSP-programmet](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

