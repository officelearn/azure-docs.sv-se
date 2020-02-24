---
title: Vad är Azure Reservations?
description: Lär dig mer om Azure-reservationer och priser för att spara kostnader för dina virtuella datorer, SQL-databaser, Azure Cosmos DB och andra resurser.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: banders
ms.openlocfilehash: c6a8547235c302f52aacd0e6ae4a8fbf08b538b8
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2020
ms.locfileid: "77443643"
---
# <a name="what-are-azure-reservations"></a>Vad är Azure Reservations?

Med Azure-reservationer kan du spara pengar genom abonnemang på ett år eller tre år med virtuella datorer, Azure Blob Storage eller Azure Data Lake Storage Gen2, SQL-databasberäkningskapacitet, Azure-disklagring, Azure Cosmos DB-dataflöde eller andra Azure-resurser. Åtagande gör att du kan få rabatt på de resurser du använder. Reservationer kan betydlig minska dina resurskostnader med upp till 72 % på Betala per användning-priser. Reservation ger en rabatt och påverkar inte resursernas körningsstatus.

Du kan betala för en reservation i förväg eller månadsvis. Totalbeloppet för reservationer är detsamma oavsett om du betalar i förväg eller per månad. Det tillkommer inte några extra avgifter när du väljer månadsbetalning. Månatlig betalning är tillgänglig för Azure-reservationer, inte produkter från tredje part.

Du kan köpa en reservation i [Azure-portalen](https://ms.portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).

## <a name="why-buy-a-reservation"></a>Varför är det bra att köpa en reservation?

Om du har virtuella datorer, blob-lagringsdata, Azure Cosmos DB eller SQL-databaser som använder betydande kapacitet eller dataflöde, eller som körs under långa tidsperioder, får du det mest kostnadseffektiva alternativet genom att köpa en reservation. När du till exempel kontinuerligt kör fyra instanser av en tjänst utan en reservation debiteras du enligt priserna för betala per användning. När du köper en reservation för dessa resurser får du reservationsrabatten omedelbart. Resurserna debiteras inte längre enligt priserna för betala per användning.

## <a name="charges-covered-by-reservation"></a>Avgifter som omfattas av reservation

Tjänstplaner:

- **Reserverad VM-instans** – en reservation omfattar bara den virtuella datorns beräkningskostnader. Den omfattar inte avgifter för ytterligare programvara, nätverk eller lagring.
- **Reserverad kapacitet i Azure Storage** – En reservation omfattar lagringskapacitet för standardlagringskonton för blob-lagring eller Azure Data Lake Gen2-lagring. Reservationen omfattar inte bandbredds- eller transaktionsavgifter.
- **Azure-disklagringsreservationer** – en reservation täcker endast Premium-SSD med storleken P30 eller större. Den omfattar inte andra disktyper eller storlekar som är mindre än P30.
- **Reserverad Azure Cosmos DB-kapacitet** – en reservation omfattar det dataflöde som allokeras för dina resurser. Den omfattar inte avgifter för lagring och nätverk.
- **SQL Database-reserverad v-kärna** – endast beräkningskostnaderna ingår i en reservation. Licensen faktureras separat.
- **SQL Data Warehouse** – en reservation omfattar cDWU-användning. En reservation omfattar inte lagrings- eller nätverksavgifter som är kopplade till SQL Data Warehouse-användningen.
- **App Service-stämpelavgift** – en reservation omfattar stämpelanvändning. Den gäller inte för arbeten, och därför debiteras andra resurser som är kopplade till stämpeln separat.
- **Azure Databricks** – En reservation täcker endast DBU-användningen. Andra avgifter för till exempel beräkning, lagring och nätverk debiteras separat.
- **Azure Database for MySQL** – Endast beräkningskostnaderna ingår med en reservation. En reservation täcker inte programvaru-, nätverks- eller lagringskostnader som är kopplade till MySQL-databasservern.
- **Azure Database for PostgreSQL** – Endast beräkningskostnaderna ingår med en reservation. En reservation täcker inte programvaru-, nätverks- eller lagringskostnader som är kopplade till PostgreSQL-databasservrarna.
- **Azure Database for MariaDB** – Endast beräkningskostnaderna ingår med en reservation. En reservation täcker inte programvaru-, nätverks- eller lagringskostnader som är kopplade till MariaDB-databasservern.
- **Azure Data Explorer** – En reservation täcker påläggsavgifterna. En reservation täcker inte beräknings-, nätverks- eller lagringskostnader som är kopplade till klustren.
- **Premium SSD Managed Disks** – En reservation görs för en angiven disk-SKU. 

Programvaruplaner:

- **SUSE Linux-** – En reservation täcker kostnaderna för programvaruplanen. Rabatterna gäller endast SUSE-mätare och inte användningen av virtuella datorer.
- **Red Hat-planer** – En reservation täcker kostnaderna för programvaruplanen. Rabatterna gäller endast RedHat-mätare och inte användningen av virtuella datorer.
- **Azure VMware Solution by CloudSimple** – En reservation täcker VMWare CloudSimple-noderna. Ytterligare programvarukostnader kan tillkomma.
- **Azure Red Hat OpenShift** – En reservation gäller för OpenShift-kostnaderna, inte för Azure-infrastrukturkostnader.

För virtuella Windows-datorer och SQL Database kan du täcka licenskostnaderna med [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-benefit/).

## <a name="whos-eligible-to-purchase-a-reservation"></a>Vem är berättigad att köpa en reservation?

Om du vill köpa en plan måste du ha rollen som prenumerationsägare för en Enterprise-prenumeration (MS-AZR-0017P eller MS-AZR-0148P), en Betala per användning-prenumeration (MS-AZR-0003P eller MS-AZR-0023P) eller en MCA-prenumeration (Microsoft-kundavtal). Molnlösningsleverantörer kan använda Azure-portalen eller  [Partnercenter](/partner-center/azure-reservations) för att köpa Azure-reservationer.

Enterprise-avtalskunder (EA) kan begränsa köp till EA-administratörer genom att inaktivera alternativet **Lägg till reserverade instanser** i EA-portalen. EA-administratörer måste vara prenumerationsägare för minst en EA-prenumeration för att kunna köpa en reservation. Alternativet är användbart för företag som vill att ett centraliserat team ska köpa reservationer för olika kostnadsställen. Efter köpet kan centraliserade team lägga till ägare för kostnadsställe till reservationerna. Ägarna kan sedan omfångsanpassa reservationen till sina prenumerationer. Det centrala teamet behöver inte ha åtkomst som prenumerationsägare där reservationen köps.

En reservationsrabatt gäller bara för resurser som är kopplade till prenumerationer som köpts via Enterprise-, CSP-planer (Cloud Solution Provider), Microsoft-kundavtal eller individuella planer med Betala per användning-priser.

## <a name="scope-reservations"></a>Omfångsanpassa reservationer

Du kan omfångsanpassa en reservation till en prenumeration eller till resursgrupper. Valet av omfång för en reservation medför att det ställe där reservationsbesparingen ska gälla väljs. När du omfångsanpassar reservationen till en resursgrupp gäller reservationsrabatten endast för resursgruppen, inte för hela prenumerationen.

### <a name="reservation-scoping-options"></a>Alternativ för att anpassa omfång för reservation

Med omfångsanpassning av en reservation för resursgrupp har du tre alternativ beroende på dina behov:

- **Omfång för enskild resursgrupp** – tillämpar reservationsrabatten på de matchande resurserna enkom i den valda resursgruppen.
- **Omfång för enskild prenumeration** – reservationsrabatten tillämpas på matchande resurser i den valda prenumerationen.
- **Delat omfång** – reservationsrabatten tillämpas på matchande resurser i berättigade prenumerationer i faktureringskontexten. För Enterprise-avtalskunder är det registreringen som är faktureringskontext. För kunder med Microsoft-kundavtal är faktureringsomfånget samma som faktureringsprofilen. För enskilda prenumerationer med betalning per användning är faktureringsomfånget alla berättigade prenumerationer som kontoadministratören har skapat.

När Azure tillämpar reservationsrabatter för din användning bearbetas reservationen i följande ordning:

1. Reservationer som är omfångsanpassade till en resursgrupp
2. Reservationer med enskilt omfång
3. Reservationer med delat omfång

En enda resursgrupp kan få reservationsrabatter från flera reservationer, beroende på hur du omfångsanpassar dina reservationer.

### <a name="scope-a-reservation-to-a-resource-group"></a>Omfångsanpassa en reservation till en resursgrupp

Du kan omfångsanpassa reservationen till en resursgrupp när du köper reservationen, eller så anger du omfånget efter köpet. Du måste vara prenumerationsägare för att omfångsanpassa reservationen till en resursgrupp.

Du anger omfånget genom att gå till sidan [Köp reservation](https://ms.portal.azure.com/#blade/Microsoft\_Azure\_Reservations/CreateBlade/referrer/Browse\_AddCommand) i Azure-portalen. Välj den reservationstyp som du vill köpa. I urvalsformuläret **Välj den produkt som du vill köpa** ändrar du värdet för Omfång till Enskild resursgrupp. Välj sedan en resursgrupp.

![Exempel som visar val av reservationsköp för virtuell dator](./media/save-compute-costs-reservations/select-product-to-purchase.png)

Inköpsrekommendationer för resursgruppen i reservationen av virtuell dator visas. Rekommendationerna beräknas genom analys av din användning under de senaste 30 dagarna. En inköpsrekommendation görs om kostnaden för att köra resurser med reserverade instanser är billigare än kostnaden för att köra resurser med Betala per användning-priser. Mer information om rekommendationer för reservationsköp finns i artikeln om att [hämta inköpsrekommendationer för reserverad instans baserat på användningsmönster](https://azure.microsoft.com/blog/get-usage-based-reserved-instance-recommendations).

Du kan alltid uppdatera omfånget efter att du har köpt en reservation. Det gör du genom att gå till reservationen, klicka på **Konfiguration** och omfångsanpassa reservationen på nytt. En ny omfångsanpassning av en reservation är inte en kommersiell transaktion. Reservationsperioden ändras inte. Mer information om hur du uppdaterar omfånget finns i avsnittet om att [uppdatera omfånget efter att du har köpt en reservation](manage-reserved-vm-instance.md#change-the-reservation-scope).

![Exempel som visar en ändring av reservationens omfång](./media/save-compute-costs-reservations/rescope-reservation-resource-group.png)

### <a name="monitor-and-optimize-reservation-usage"></a>Övervaka och optimera reservationsanvändningen

Du kan övervaka reservationsanvändningen på flera sätt – via Azure-portalen, API:er eller användningsdata. Om du vill se alla reservationer som du har åtkomst till går du till **Reservationer** i Azure-portalen. Reservationsrutnätet visar reservationens senast registrerade användningsprocentandel. Klicka på reservationen om du vill se dess långsiktiga användning.

Du kan även få reservationsanvändning med hjälp av [API:er](reservation-apis.md#see-reservation-usage) och från dina [användningsdata](understand-reserved-instance-usage-ea.md#common-cost-and-usage-tasks) om du är Enterprise-avtalskund eller har ett Microsoft-kundavtal.

Om du märker att användningen av din reservation som omfångsanpassats för resursgrupp är låg kan du uppdatera reservationens omfång till enstaka prenumeration eller dela den över faktureringskontexten. Du kan även dela reservationen och tillämpa de resulterande reservationerna på olika resursgrupper.

### <a name="other-considerations"></a>Andra överväganden

Om du inte har matchande resurser i en resursgrupp kommer reservationen att underutnyttjas. Reservationen gäller inte automatiskt för en annan resursgrupp eller prenumeration där användningen är låg.

Ett reservationsomfång uppdateras inte automatiskt om du flyttar resursgruppen från en prenumeration till en annan. Omfånget uppdateras inte om du tar bort resursgruppen. Du kommer att behöva [omfångsanpassa reservationen på nytt](manage-reserved-vm-instance.md#change-the-reservation-scope). Annars kommer reservationen att underutnyttjas.

## <a name="discounted-subscription-and-offer-types"></a>Rabatterad prenumeration och erbjudandetyper

Reservationsrabatter gäller för följande berättigade prenumerationer och erbjudandetyper.

- Enterprise-avtal (erbjudandenummer: MS-AZR-0017P eller MS-AZR-0148P)
- MCA-prenumerationer (Microsoft-kundavtal).
- Enskilda prenumerationsavtal med priser enligt principen betala per användning (erbjudandenummer: MS-AZR-0003P eller MS-AZR-0023P)
- CSP-prenumerationer

Resurser som körs i en prenumeration med andra erbjudandetyper får inte reservationsrabatten.

## <a name="how-is-a-reservation-billed"></a>Hur faktureras en reservation?

En reservation debiteras enligt den betalningsmetod som är kopplad till prenumerationen. Reservationskostnaden dras av från ditt saldo för ekonomiskt åtagande i förekommande fall. När ditt saldo för ekonomiskt åtagande inte täcker kostnaden för reservationen debiteras du för överförbrukningen. Om du har en prenumeration från en enskild plan med Betala per användning-priser debiteras det kreditkort som du har på ditt konto omedelbart för förskottsköp. Månatliga betalningar visas på din faktura, och ditt kreditkort debiteras månatligen. När du faktureras per faktura visas avgifterna på nästa faktura.

## <a name="how-reservation-discount-is-applied"></a>Så tillämpas reservationsrabatten

Reservationsrabatten gäller för resursanvändning som matchar de attribut som du väljer när du köper reservationen. Bland attributen finns det omfång där matchande virtuella datorer, SQL-databaser, Azure Cosmos DB eller andra resurser körs. Om du till exempel vill ha en reservationsrabatt för fyra virtuella Standard D2-datorer i regionen USA, västra väljer du den prenumeration där de virtuella datorerna körs.

Reservationsrabatter *går förlorade om de inte används*. Om du inte har några matchande resurser för en viss timme förlorar du en reservationskvantitet för den timmen. Det går inte att föra vidare oanvända reserverade timmar.

När du avslutar en resurs tillämpas reservationsrabatten automatiskt på en annan matchande resurs i det angivna omfånget. Om det inte finns några matchande resurser i det angivna omfånget går de reserverade timmarna *förlorade*.

Du kan till exempel skapa en resurs senare och ha en matchande reservation som underutnyttjas. Reservationsrabatten gäller automatiskt för den nya matchande resursen.

Om de virtuella datorerna körs i olika prenumerationer i din registrering/ditt konto väljer du omfånget som delat. Delat omfång gör att reservationsrabatten kan tillämpas mellan prenumerationer. Du kan ändra omfånget efter att du har köpt en reservation. Mer information finns i [Hantera Azure-reservationer](manage-reserved-vm-instance.md).

En reservationsrabatt gäller bara för resurser som är kopplade till Enterprise-avtal, Microsoft-kundavtal, CSP:er eller prenumerationer med Betala per användning-priser. Resurser som körs i en prenumeration med andra erbjudandetyper får inte reservationsrabatten.

## <a name="when-the-reservation-term-expires"></a>När reservationsperioden upphör att gälla

I slutet av reservationsperioden går faktureringsrabatten ut och resurserna debiteras enligt principen betala per användning. Som standard är reservationerna inte inställda på automatisk förnyelse. Du kan välja att aktivera automatisk förnyelse av en reservation genom att välja alternativet i förnyelseinställningarna. Med automatisk förnyelse köps en ersättningsreservation när den befintliga reservationen upphör att gälla. Som standard har ersättningsreservationen samma attribut som reservationen som går ut. Om du vill ändrar du faktureringsfrekvensen, giltighetsperioden eller kvantiteten i förnyelseinställningarna. Alla användare med ägaråtkomst till reservationen och prenumerationen som används för fakturering kan konfigurera förnyelse.  

## <a name="discount-applies-to-different-sizes"></a>Rabatten tillämpas på olika storlekar

När du köper en reservation kan rabatten tillämpas på andra instanser med attribut som är i samma storleksgrupp. Den här funktionen kallas flexibel instansstorlek. Flexibiliteten hos rabattens täckning beror på typen av reservation och de attribut som du väljer när du köper reservationen.

Tjänstplaner:

- Reserverade VM-instanser: När du köper reservationen och väljer **Optimerad för flexibel instansstorlek** beror rabattens täckning på den VM-storlek som du väljer. Reservationen kan tillämpas på de virtuella datorernas (VM) storlekar i seriegrupp med samma storlek. Mer information finns i artikeln om [flexibel storlek för virtuella datorer med reserverade VM-instanser](../../virtual-machines/windows/reserved-vm-instance-size-flexibility.md).
- Reserverad kapacitet i Azure Storage: Du kan köpa reserverad kapacitet för Azure Storage-standardkonton på 100 TiB eller 1 PiB per månad. Reserverad kapacitet i Azure Storage är tillgänglig i alla regioner för alla åtkomstnivåer (frekvent, lågfrekvent eller arkiv) och för alla replikeringsalternativ (LRS, GRS eller ZRS).
- Reserverad SQL Database-kapacitet: Rabattens täckning beror på vilken prestandanivå du väljer. Mer information finns i artikeln om [hur en Azure-reservationsrabatt tillämpas](understand-reservation-charges.md).
- Reserverad Azure Cosmos DB-kapacitet: Rabattens täckning beror på det etablerade dataflödet. Mer information finns i artikeln om [hur en Azure Cosmos DB-reservationsrabatt tillämpas](understand-cosmosdb-reservation-charges.md).

## <a name="reservation-notifications"></a>Reservationsmeddelanden

Beroende på hur du betalar för din Azure-prenumeration skickar vi e-post med reservationsmeddelanden till följande användare i din organisation. Meddelanden skickas för olika händelser, däribland:

- Köp
- Kommande upphörande av reservation
- Upphörande
- Förnyelse
- Annullering
- Omfångsändring

För kunder med EA-prenumerationer:
- Ett inköpsmeddelande skickas till inköparen och EA-meddelandekontakterna.
- Andra meddelanden om reservationslivscykel skickas endast till EA-meddelandekontakterna.
- Användare som läggs till i en reservation med hjälp av RBAC-behörighet (IAM) får inte några e-postaviseringar.

För kunder med enskilda prenumerationer:
- Inköparen får ett inköpsmeddelande.
- Vid inköpstillfället får prenumerationens faktureringskontoägare ett inköpsmeddelande.
- Kontoägaren får alla andra meddelanden.


## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

- Läs mer om Azure-reservationer med följande artiklar:
    - [Hantera Azure Reservations](manage-reserved-vm-instance.md)
    - [Förstå reservationsanvändning för din prenumeration med Betala per användning-priser](understand-reserved-instance-usage.md)
    - [Förstå reservationsanvändning för din Enterprise-registrering](understand-reserved-instance-usage-ea.md)
    - [Kostnader för Windows-programvara ingår inte i reservationer](reserved-instance-windows-software-costs.md)
    - [Azure-reservationer i ett CSP-program (Cloud Solution Provider) i Partnercenter](/partner-center/azure-reservations)

- Läs mer om reservationer för tjänstplaner:
    - [Virtual Machines med Azure Reserved VM Instances](../../virtual-machines/windows/prepay-reserved-vm-instances.md)
    - [Azure Cosmos DB-resurser med reserverad Azure Cosmos DB-kapacitet](../../cosmos-db/cosmos-db-reserved-capacity.md)
    - [SQL Database-beräkningsresurser med reserverad kapacitet för Azure SQL Database](../../sql-database/sql-database-reserved-capacity.md) Läs mer om reservationer för programvaruplaner:
    - [Red Hat-programvaruplaner från Azure-reservationer](../../virtual-machines/linux/prepay-rhel-software-charges.md)
    - [SUSE-programvaruplaner från Azure-reservationer](../../virtual-machines/linux/prepay-suse-software-charges.md)
