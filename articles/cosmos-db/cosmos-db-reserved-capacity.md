---
title: Betala i förskott för Azure Cosmos DB-resurser för att spara pengar | Microsoft Docs
description: Lär dig hur du köper Azure Cosmos DB reserverad kapacitet för att spara dina beräkningskostnader.
services: cosmos-db
author: rimman
manager: kfile
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 1be2d67d8a1ee51c4883ae1f50b80ad3a9691c2d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46981975"
---
# <a name="prepay-for-azure-cosmos-db-resources-with-reserved-capacity"></a>Betala i förskott för Azure Cosmos DB-resurser med reserverad kapacitet

Azure Cosmos DB reserverad kapacitet kan du spara pengar genom att betala förväg för Azure Cosmos DB-resurser under ett år eller tre år. Azure Cosmos DB reserverad kapacitet kan du få rabatt på dataflödet som tillhandahållits för Cosmos DB-resurser, till exempel databaser, behållare (tabeller/samlingar/diagram). Azure Cosmos DB reserverad kapacitet kan avsevärt minska dina kostnader för Cosmos DB – upp till 65 procent på vanliga priser – med ett eller tre år i förskott. Reserverad kapacitet ger en rabatt på fakturering och påverkar inte körtiden för Cosmos DB-resurser.

Azure Cosmos DB reserverad kapacitet omfattar dataflödet som etableras för dina resurser, de täcker inte avgifter för lagring och nätverk. När du köper en reservation går dataflöde avgifterna som matchar reservationen attribut är inte längre debiteras enligt användningsbaserad-som-du priser. Läs mer på reservationer [Azure reservationer](../billing/billing-save-compute-costs-reservations.md) artikeln. 

Du kan köpa Azure Cosmos DB reserverad kapacitet från den [Azure-portalen](https://portal.azure.com). Du köper en reserverad kapacitet:

* Du måste vara i rollen ägare för minst en Enterprise eller användningsbaserad betalning.  
* För Enterprise-prenumerationer Azure reservation inköp måste aktiveras i den [EA-portalen.](https://ea.azure.com/)  
* Endast administratören agenter eller försäljning agenter kan köpa Azure Cosmos DB reserverad kapacitet för Cloud Solution Provider (CSP) program.

## <a name="determine-the-required-throughput-before-purchase"></a>Fastställa nödvändiga dataflödet innan du köper

Storleken på reservationen ska baseras på den totala mängden dataflöde som används av de befintliga eller snart-till--distribueras Azure Cosmos DB-resurserna (till exempel databaser eller behållare - samlingar, tabeller, diagram). Du kan fastställa nödvändiga dataflödet på följande sätt:

* Gå till [Azure-portalen](https://portal.azure.com), hitta ditt Azure Cosmos DB-konto, öppnar du bladet mått och få information genomsnittligt dataflöde per sekund från den **dataflöde** fliken under en period på 3 – 6 månader. Ange den här storleken som reserverad kapacitetsenheter när du köper.

Om du är en Enterprise Agreement (EA) kan du också hämta din användningsfil och referera till **tjänsttyp** värde i den **ytterligare information** i användningsfilen för att hämta Azure Cosmos DB dataflöde information.

Du kan också summera genomsnittlig genomströmning för alla arbetsbelastningar på Azure Cosmos DB-konton som du förväntar körs för bredvid ett eller tre år och Använd den kvantiteten för reservation.

## <a name="buy-azure-cosmos-db-reserved-capacity"></a>Köp Azure Cosmos DB reserverad kapacitet

1. Logga in på [Azure Portal](https://portal.azure.com).  

2. Välj **alla tjänster** > **reservationer** > **Lägg till**.  

3. Från den **Välj produkttyp** fönstret Välj **Azure Cosmos DB**, och sedan **Välj** att köpa en ny reservation.  

4. Fyll i de obligatoriska fälten som beskrivs i tabellen nedan:

   ![Fyll i formuläret med reserverad kapacitet](./media/cosmos-db-reserved-capacity/fill_reserved_capacity_form.png) 

   |Fält  |Beskrivning  |
   |---------|---------|
   |Namn   |    Namn på reservationen. Det här fältet fylls i automatiskt med den `CosmosDB_Reservation_<timeStamp>`. Du kan ange ett annat namn när du skapar reservationen eller Byt namn på den när reservationen har skapats.      |
   |Prenumeration  |   Den prenumeration som används för att betala för Azure Cosmos DB reserverad kapacitet. Den aktuella betalningsmetoden på den valda prenumerationen används i debitera några startkostnader. Prenumerationstypen måste vara något av följande: <br/><br/>  [Enterprise-avtal](https://azure.microsoft.com/pricing/enterprise-agreement/) (erbjudandets nummer: MS-AZR - 0017 P) – för en prenumeration på enterprise avgifterna dras av från den registrering saldo för åtagandebelopp eller debiteras som överanvändning. <br/><br/> [Betala per användning](https://azure.microsoft.com/offers/ms-azr-0003p/) (erbjudandets nummer: MS-AZR - 0003 P)-prenumeration med användningsbaserad betalning avgifterna faktureras till betalningsmetod kreditkort eller faktura för prenumerationen.    |
   |Omfång   |   Den reservationsomfånget styr hur många prenumerationer kan dra nytta av faktureringsförmånen som är associerade med reservationen och den styr hur reservationen ska gälla för specifika prenumerationer. En Reservation har ett scope på enskild eller delade prenumeration. Om du väljer:   <br/><br/>  **Enstaka prenumeration** -rabatten gäller för Azure Cosmos DB-instanser i den valda prenumerationen. <br/><br/>  **Delade** – reservationsrabatten tillämpas på Azure Cosmos DB-instanser som körs på alla prenumerationer i din faktureringskontexten. Fakturering kontexten fastställs baserat på hur du registrerat dig för Azure. För företagskunder, den delade omfattningen registreringen och innehåller alla prenumerationer (utom prenumerationer för utveckling och testning) i registreringen. För kunder med användningsbaserad betalning är den delade omfattningen alla betala per användning-prenumerationer som skapas av kontoadministratören.  <br/><br/> Du kan ändra omfång för reservation när du har köpt reserverad kapacitet.  |
   |Typ av reserverad kapacitet   |  Typen av reserverad kapacitet är dataflödet som tillhandahållits i begäransenheter.|
   |Reserverade kapacitetsenheter  |      Mängden dataflöde som du vill reservera. Du kan beräkna det här värdet genom att fastställa det dataflöde som krävs för alla dina Cosmos DB-resurser (till exempel databaser eller behållare) per region och sedan multiplicerar den med antalet regioner som ska associeras med din Cosmos DB-databas.  <br/> Till exempel: Om du har fem regioner med 1 miljon RU/sek i varje region och välj sedan 5 miljoner RU/sek för reservationsköp för kapacitet.    |
   |Period  |   Ett eller tre år.   |

5. Granska rabatten och priset för reservationen i den **kostnader** avsnittet. Den här reservationen priset gäller för Azure Cosmos DB-resurser med dataflöde etablerat i alla regioner.  

6. Välj **Köp**. Du kan se följande skärmbild när köpet har slutförts. 

   ![Fyll i formuläret med reserverad kapacitet](./media/cosmos-db-reserved-capacity/reserved_capacity_successful.png) 

När du köper en reservation, tillämpas den omedelbart på några befintliga Azure Cosmos DB-resurser som matchar villkoren i reservationen. Om du inte har några befintliga resurser i Azure Cosmos DB, gäller reservationen när du distribuerar en ny Cosmos DB-instans som matchar villkoren i reservationen. I båda fallen startas perioden för reservationen direkt efter en lyckad inköp. 

När din reservationen går ut dina Azure Cosmos DB-instanser fortsätter att köras och debiteras enligt de användningsbaserad betalning.

## <a name="next-steps"></a>Nästa steg

Reservationsrabatten tillämpas automatiskt på Azure Cosmos DB-resurserna som matchar reservationsomfånget och attribut. Du kan uppdatera reservationens omfång den via Azure-portalen, PowerShell, CLI eller API: et.

*  Läs hur reserverad kapacitet rabatter till Azure Cosmos DB i [förstå Azure reservationer rabatt](../billing/billing-understand-cosmosdb-reservation-charges.md)

* Om du vill veta mer om Azure reservationer, finns i följande artiklar:

   * [Vad är Azure reservationer?](../billing/billing-save-compute-costs-reservations.md)  
   * [Hantera Azure reservationer](../billing/billing-manage-reserved-vm-instance.md).  
   * [Förstå användningen av reserverade för din Enterprise-registrering](../billing/billing-understand-reserved-instance-usage-ea.md)  
   * [Förstå användningen av reservation för prenumerationen med användningsbaserad betalning](../billing/billing-understand-reserved-instance-usage.md)
   * [Azure reservationer i programmet för Partner Center Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du har fler frågor, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att lösa problemet snabbt.

