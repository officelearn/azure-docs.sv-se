---
title: Vad är Azure Reservations?
description: Lär dig mer om Azure Reservations och priser för att spara på dina virtuella datorer, SQL-databaser, Azure Cosmos DB och andra resurs kostnader.
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: banders
ms.openlocfilehash: bb90a9dec161746356b8c13df448718c53626684
ms.sourcegitcommit: b8578b14c8629c4e4dea4c2e90164e42393e8064
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/09/2019
ms.locfileid: "70806340"
---
# <a name="what-are-azure-reservations"></a>Vad är Azure Reservations?

Azure Reservations hjälpa dig att spara pengar genom att göra ett år eller tre års virtuella datorer, SQL Database beräknings kapacitet, Azure Cosmos DB data flöde eller andra Azure-resurser. Med hjälp av kan du få rabatt på de resurser du använder. Reservationer kan avsevärt minska din virtuella dator, SQL Database Compute, Azure Cosmos DB eller andra resurs kostnader upp till 72% på priserna för betala per användning. Reservationer ger en fakturerings rabatt och påverkar inte körnings statusen för dina resurser.

Du kan betala för en reservation fram eller månads vis. Den totala kostnaden för upp-och månads reservationer är samma och du betalar inte några extra avgifter när du väljer att betala varje månad. Månatlig betalning är tillgänglig för Azure-reservationer, inte produkter från tredje part.

Du kan köpa en reservation i [Azure Portal](https://ms.portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).

## <a name="why-buy-a-reservation"></a>Varför köpa en reservation?

Om du har virtuella datorer, Azure Cosmos DB eller SQL-databaser som körs under långa tids perioder får du det mest kostnads effektiva alternativet genom att köpa en reservation. När du till exempel kontinuerligt kör fyra instanser av en tjänst utan en reservation debiteras du enligt priserna för betala per användning. När du köper en reservation för dessa resurser får du reservations rabatten direkt. Resurserna debiteras inte längre enligt priserna för betala per användning.

## <a name="charges-covered-by-reservation"></a>Avgifter som omfattas av reservation

Service planer:

- **Reserverad virtuell dator instans** – en reservation täcker bara den virtuella datorns beräknings kostnader. Det tar inte upp ytterligare program vara, nätverk eller lagrings kostnader.
- **Azure Cosmos DB reserverad kapacitet** – en reservation täcker data flöde som har allokerats för dina resurser. Det behandlar inte lagrings-och nätverks avgifterna.
- **SQL Database reserverad vCore** – endast beräknings kostnaderna ingår i en reservation. Licensen debiteras separat.
- **SQL Data Warehouse** – en reservation täcker cDWU-användning. Den behandlar inte lagrings-eller nätverks kostnader som är kopplade till SQL Data Warehouse användning.
- **App Service Stamp-avgift** – en reservation omfattar Stamp-användning. Den gäller inte för arbetare, så andra resurser som är kopplade till stämpeln debiteras separat.

För virtuella Windows-datorer och SQL Database kan du försäkra licens kostnaderna med [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-benefit/).

## <a name="whos-eligible-to-purchase-a-reservation"></a>Vem är berättigad att köpa en reservation?

Om du vill köpa en plan måste du ha en prenumerations ägar roll i ett företag (MS-AZR-0017P eller MS-AZR-0148P) eller prenumerationen betala per användning (MS-AZR-0003P eller MS-AZR-0023P). Moln lösnings leverantörer kan använda Azure Portal eller [partner Center](/partner-center/azure-reservations) för att köpa Azure reservations.

Enterprise-avtal-kunder (EA) kan begränsa köp till EA-administratörer genom att inaktivera alternativet **Lägg till reserverade instanser** i EA-portalen. EA-administratörer måste vara en prenumerations ägare för minst en EA-prenumeration för att köpa en reservation. Alternativet är användbart för företag som vill att ett centraliserat team ska köpa reservationer för olika kostnads ställen. Efter köpet kan centraliserade team lägga till kostnads ställe ägare till reservationerna. Ägare kan sedan begränsa reservationen till sina prenumerationer. Det centrala teamet behöver inte ha prenumerations ägar åtkomst där reservationen köps.

En reservations rabatt gäller bara för resurser som är kopplade till prenumerationer som köpts via Enterprise, Cloud Solution Provider (CSP) och enskilda planer med priser enligt principen betala per användning.

## <a name="scope-reservations"></a>Omfångs reservationer

Du kan begränsa en reservation till en prenumeration eller resurs grupp. Om du anger omfånget för en reservation väljs var reservations besparingarna gäller. När du omfångerar reservationen till en resurs grupp gäller reservations rabatten endast för resurs gruppen, inte hela prenumerationen.

### <a name="reservation-scoping-options"></a>Alternativ för reservations omfång

Med resurs grupp omfånget finns det tre alternativ för att omfånget för en reservation, beroende på dina behov:

- **Definitions område för enskild resurs grupp** – tillämpar reservations rabatten på de matchande resurserna enbart i den valda resurs gruppen.
- **Enda prenumerations omfång** – tillämpar reservations rabatten på de matchande resurserna i den valda prenumerationen.
- **Delat omfång** – tillämpar reservations rabatten på matchande resurser i berättigade prenumerationer som finns i fakturerings kontexten. För Enterprise-avtal kunder är fakturerings kontexten registreringen. För enskilda prenumerationer med priser enligt principen betala per användning är fakturerings omfånget alla berättigade prenumerationer som skapats av konto administratören.

När du använder reservations rabatter för din användning, bearbetar Azure reservationen i följande ordning:

1. Reservationer som är begränsade till en resurs grupp
2. Enkla omfångs reservationer
3. Delade omfångs reservationer

En enda resurs grupp kan hämta reservations rabatter från flera reservationer, beroende på hur du omfångerar dina reservationer.

### <a name="scope-a-reservation-to-a-resource-group"></a>Omfånget av en reservation till en resurs grupp

Du kan begränsa reservationen till en resurs grupp när du köper reservationen, eller så ställer du in omfånget efter köpet. Du måste vara prenumerations ägare för att omfånget av reservationen till en resurs grupp.

Om du vill ange omfånget går du till sidan [köp reservation](https://ms.portal.azure.com/#blade/Microsoft\_Azure\_Reservations/CreateBlade/referrer/Browse\_AddCommand) i Azure Portal. Välj den reservations typ som du vill köpa. I **Välj den produkt som du vill köpa** urvals formuläret ändrar du värdet för omfattning till enskild resurs grupp. Välj sedan en resurs grupp.

![Exempel som visar köp val för virtuella datorer](./media/billing-save-compute-costs-reservations/select-product-to-purchase.png)

Inköps rekommendationer för resurs gruppen i den virtuella dator reservationen visas. Rekommendationerna beräknas genom att analysera din användning under de senaste 30 dagarna. En inköps rekommendation görs om kostnaden för att köra resurser med reserverade instanser är billigare än kostnaden för att köra resurser med taxan betala per användning. Mer information om rekommendationer för reservations köp finns i [Hämta reserverade instanser köp rekommendationer baserat på användnings mönster](https://azure.microsoft.com/blog/get-usage-based-reserved-instance-recommendations).

Du kan alltid uppdatera omfånget när du har köpt en reservation. Det gör du genom att gå till reservationen, klicka på **konfiguration**och omfång reservationen. Omdefiniering av en reservation är inte en kommersiell transaktion. Reservations perioden har inte ändrats. Mer information om hur du uppdaterar omfattningen finns i [Uppdatera omfånget när du har köpt en reservation](billing-manage-reserved-vm-instance.md#change-the-reservation-scope).

![Exempel som visar en ändring av reservations omfånget](./media/billing-save-compute-costs-reservations/rescope-reservation-resource-group.png)

### <a name="monitor-and-optimize-reservation-usage"></a>Övervaka och optimera reservations användningen

Du kan övervaka reservations användningen på flera sätt – via Azure Portal, via API: er eller via användnings data. Om du vill se alla reservationer som du har åtkomst till, går du till **reservationer** i Azure Portal. Boknings rutnätet visar den senast inloggade användnings procenten för reservationen. Klicka på reservationen om du vill se långsiktig användning av reservationen.

Du kan också få reservations användning med [API: er](billing-reservation-apis.md#see-reservation-usage) och [användnings data](billing-understand-reserved-instance-usage-ea.md#common-cost-and-usage-tasks) om du är kund för Enterprise-avtal.

Om du märker att användningen av den omslutna reservationen för resurs gruppen är låg kan du uppdatera reservations omfånget till en enskild prenumeration eller dela den över fakturerings kontexten. Du kan också dela reservationen och tillämpa de resulterande reservationerna på olika resurs grupper.

### <a name="other-considerations"></a>Annat att tänka på

Om du inte har matchande resurser i en resurs grupp kommer reservationen att underutnyttjas. Reservationen gäller inte automatiskt för en annan resurs grupp eller prenumeration där det är ont om användning.

En reservations omfattning uppdateras inte automatiskt om du flyttar resurs gruppen från en prenumeration till en annan. Omfånget uppdateras inte om du tar bort resurs gruppen. Du kommer att behöva [omfånget för reservationen](billing-manage-reserved-vm-instance.md#change-the-reservation-scope). Annars kommer reservationen att underutnyttjas.

## <a name="discounted-subscription-and-offer-types"></a>Rabatterat abonnemang och erbjudande typer

Reservationsrabatter gäller för följande berättigade prenumerationer och erbjudandetyper.

- Enterprise-avtal (erbjudandenummer: MS-AZR-0017P eller MS-AZR-0148P)
- Enskilda prenumerationsavtal med priser enligt principen betala per användning (erbjudandenummer: MS-AZR-0003P eller MS-AZR-0023P)
- CSP-prenumerationer

Resurser som körs i en prenumeration med andra erbjudandetyper får inte reservationsrabatten.

## <a name="how-is-a-reservation-billed"></a>Hur faktureras en reservation?

Reservationen debiteras till betalnings metoden som är kopplad till prenumerationen. Om du har en Enterprise-prenumeration dras reservations kostnaden från ditt åtagande belopp. När saldot för ditt åtagande belopp inte påverkar kostnaden för reservationen debiteras du överanvändning. Om du har en prenumeration från en enskild plan med priser enligt principen betala per användning debiteras det kredit kort som du har på ditt konto omedelbart för första inköp. Månads betalningarna visas på din faktura och ditt kredit kort debiteras månads vis. När du faktureras per faktura visas avgifterna på nästa faktura.

## <a name="how-reservation-discount-is-applied"></a>Så här tillämpas reservations rabatten

Reservations rabatten gäller för resursanvändning som matchar de attribut som du väljer när du köper reservationen. Attribut är det omfång där matchande virtuella datorer, SQL-databaser, Azure Cosmos DB eller andra resurser körs. Om du till exempel vill ha en reservations rabatt för fyra standard D2-virtuella datorer i regionen Västra USA väljer du den prenumeration där de virtuella datorerna körs.

En reservations rabatt är "*Använd-IT-eller-förlorad-IT*". Om du inte har några matchande resurser i någon timme förlorar du en reservations kvantitet för den timmen. Det går inte att överföra oanvända reserverade timmar.

När du stänger av en resurs tillämpas reservations rabatten automatiskt på en annan matchande resurs i det angivna omfånget. Om det inte finns några matchande resurser i det angivna omfånget går de reserverade timmarna *förlorade*.

Du kan till exempel skapa en resurs senare och ha en matchande reservation som underutnyttjas. Reservations rabatten gäller automatiskt för den nya matchande resursen.

Om de virtuella datorerna körs i olika prenumerationer i registreringen/kontot väljer du omfånget som delad. Delad omfattning gör att reservations rabatten kan tillämpas på alla prenumerationer. Du kan ändra omfånget när du har köpt en reservation. Mer information finns i [hantera Azure reservations](billing-manage-reserved-vm-instance.md).

En reservations rabatt gäller bara för resurser som är kopplade till företag, CSP eller prenumerationer med betala per användning-pris. Resurser som körs i en prenumeration med andra erbjudandetyper får inte reservationsrabatten.

## <a name="when-the-reservation-term-expires"></a>När reservations perioden upphör att gälla

I slutet av reservations perioden går fakturerings rabatten ut. Den virtuella datorn, SQL-databasen, Azure Cosmos DB eller någon annan resurs debiteras enligt priset betala per användning. Azure Reservations förnyas inte automatiskt. Om du vill fortsätta att få fakturerings rabatten måste du köpa en ny reservation för berättigade tjänster och program vara.

## <a name="discount-applies-to-different-sizes"></a>Rabatten gäller för olika storlekar

När du köper en reservation kan rabatten gälla för andra instanser med attribut i samma storleks grupp. Den här funktionen kallas flexibilitet för instans storlek. Flexibiliteten i rabatten beror på typen av reservation och de attribut som du väljer när du köper reservationen.

Service planer:

- Reserverade VM-instanser: När du köper reservationen och väljer **optimerad för flexibilitet för instans storlek**beror rabatt täckningen på den virtuella dator storleken som du väljer. Reservationen kan tillämpas på de virtuella datorernas (VM) storlekar i samma storleks serie grupp. Mer information finns i [storleks flexibilitet för virtuella datorer med reserverade VM-instanser](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md).
- Reserverad kapacitet för SQL Database: Rabatt täckningen beror på vilken prestanda nivå du väljer. Mer information finns i [förstå hur en Azure reservations rabatt](billing-understand-reservation-charges.md)används.
- Reserverad kapacitet för Azure Cosmos DB: Rabatt täckningen beror på det etablerade data flödet. Mer information finns i [förstå hur en Azure Cosmos DB reservations rabatt tillämpas](billing-understand-cosmosdb-reservation-charges.md).

## <a name="reservation-notifications"></a>Reservations meddelanden

Beroende på hur du betalar för din Azure-prenumeration skickar vi e-post till följande användare i din organisation. Meddelanden skickas för olika händelser, inklusive:

- Köp
- Kommande reservations förfallo datum
- Expiry
- Längs
- Annullering
- Omfattnings ändring

För kunder med EA-prenumerationer:
- Ett inköps meddelande skickas till inköparen och funktionen för EA-aviseringar.
- Andra meddelanden om reservations-livscykel skickas endast till meddelanden med EA-aviseringar.
- Användare som läggs till i en reservation som använder RBAC (IAM)-behörighet får inte några e-postaviseringar.

För kunder med enskilda prenumerationer:
- Inköparen får ett inköps meddelande.
- Vid inköps tillfället får prenumerationens fakturerings konto ägare ett inköps meddelande.
- Konto ägaren tar emot alla andra meddelanden.


## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en support förfrågan](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

- Läs mer om Azure Reservations med följande artiklar:
    - [Hantera Azure Reservations](billing-manage-reserved-vm-instance.md)
    - [Förstå reservations användningen för din prenumeration med priser enligt principen betala per användning](billing-understand-reserved-instance-usage.md)
    - [Förstå reservationsanvändning för din Enterprise-registrering](billing-understand-reserved-instance-usage-ea.md)
    - [Windows-programkostnader som inte ingår i reservationer](billing-reserved-instance-windows-software-costs.md)
    - [Azure Reservations i programmet Partner Center Cloud Solution Provider (CSP)](/partner-center/azure-reservations)

- Läs mer om reservationer för Service planer:
    - [Virtual Machines med Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
    - [Azure Cosmos DB resurser med Azure Cosmos DB reserverad kapacitet](../cosmos-db/cosmos-db-reserved-capacity.md)
    - [SQL Database beräknings resurser med Azure SQL Database reserverad kapacitet](../sql-database/sql-database-reserved-capacity.md) Läs mer om reservationer för program varu planer:
    - [Red Hat-programplaner från Azure Reservations](../virtual-machines/linux/prepay-rhel-software-charges.md)
    - [SUSE program varu planer från Azure Reservations](../virtual-machines/linux/prepay-suse-software-charges.md)
