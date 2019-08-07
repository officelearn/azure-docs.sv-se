---
title: Hantera Azure Reservations
description: Lär dig hur du kan hantera Azure Reservations.
ms.service: billing
author: bandersmsft
manager: yashesvi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/06/2019
ms.author: banders
ms.openlocfilehash: b161fc7cd4faa75dd87613c297c12f1edd862510
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840024"
---
# <a name="manage-reservations-for-azure-resources"></a>Hantera reservationer för Azure-resurser

När du har köpt en Azure-reservation kan du behöva tillämpa reservationen på en annan prenumeration, ändra vem som kan hantera reservationen eller ändra omfånget för reservationen. Du kan också dela upp en reservation i två reservationer för att tillämpa några av instanserna som du köpte till en annan prenumeration.

Om du har köpt Azure Reserved Virtual Machine Instances kan du ändra optimerings inställningen för reservationen. Reservations rabatten kan tillämpas på virtuella datorer i samma serie, eller så kan du reservera Data Center kapacitet för en angiven VM-storlek. Och du bör försöka optimera reservationer så att de används fullt ut.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="reservation-order-and-reservation"></a>Reservations order och reservation

När du köper en reservation skapas två objekt: **Reservations order** och **reservation**.

Vid inköps tillfället har en reservations order en reservation under den. Åtgärder som Split, merge, del betalning eller Exchange skapar nya reservationer under **reservations ordern**.

Om du vill visa en reservations ordning går du till **reservationer** > väljer reservationen och klickar sedan på **reservations orderns ID**.

![Exempel på information om reservations order som visar reservations order-ID ](./media/billing-manage-reserved-vm-instance/reservation-order-details.png)

En reservation ärver behörigheter från sin reservations ordning.

## <a name="change-the-reservation-scope"></a>Ändra reservations omfånget

 Reservations rabatten gäller för virtuella datorer, SQL-databaser, Azure Cosmos DB eller andra resurser som matchar din reservation och körs i reservations omfånget. Fakturerings kontexten är beroende av den prenumeration som används för att köpa reservationen.

Så här uppdaterar du omfånget för en reservation:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **alla tjänster** > **reservationer**.
3. Välj reservationen.
4. Välj **Inställningar** > **Konfiguration**.
5. Ändra omfånget.

Om du ändrar från delat till ett enda omfång kan du bara välja prenumerationer där du är ägare. Endast prenumerationer i samma faktureringskontext som reservationen kan väljas.

Omfånget gäller bara för enskilda prenumerationer med priser enligt principen betala per användning (erbjuder MS-AZR-0003P eller MS-AZR-0023P), företags erbjudande MS-AZR-0017P eller MS-AZR-0148P, eller CSP-prenumerations typer.

## <a name="add-or-change-users-who-can-manage-a-reservation"></a>Lägga till eller ändra användare som kan hantera en reservation

Du kan delegera reservations hantering genom att lägga till personer till roller på reservations ordern eller reservationen. Som standard har den person som placerar reservations ordern och konto administratören ägar rollen på reservations ordern och reservationen.

Du kan hantera åtkomst till boknings order och reservationer oberoende av prenumerationerna som erhåller reservations rabatten. När du ger någon behörighet att hantera en reservations order eller reservationen ger den inte behörighet att hantera prenumerationen. Om du ger någon behörighet att hantera en prenumeration i reservationens omfattning ger den inte behörighet att hantera reservations ordern eller reservationen.

För att utföra ett utbyte eller en åter betalning måste användaren ha åtkomst till reservations ordern. När du beviljar någon behörighet är det bäst att bevilja behörighet till reservations ordern, inte reservationen.


Så här delegerar du åtkomst hantering för en reservation:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **alla tjänster** > **reservation** för att lista de reservationer som du har åtkomst till.
3. Välj den reservation som du vill delegera åtkomst till andra användare.
4. Välj **åtkomstkontroll (IAM)** .
5. Välj **Lägg till roll tilldelning** > **roll** > **ägare**. Du kan också välja en annan roll om du vill ge begränsad åtkomst.
6. Ange e-postadressen för den användare som du vill lägga till som ägare.
7. Välj användaren och sedan **Spara**.

## <a name="split-a-single-reservation-into-two-reservations"></a>Dela en enda reservation i två reservationer

 När du har köpt fler än en resurs instans i en reservation kanske du vill tilldela instanser i den reservationen till olika prenumerationer. Som standard har alla instanser en omfattning – antingen en enskild prenumeration eller delad. Du har till exempel köpt 10 reservations instanser och angett omfånget som ska vara prenumeration A. Du kan nu ändra omfånget för sju reservationer till prenumeration A och de återstående tre prenumerationerna B. om du delar en reservation kan du distribuera instanser för detaljerad omfattnings hantering. Du kan förenkla tilldelningen till prenumerationer genom att välja delad omfattning. Men för kostnads hantering eller budgetering kan du allokera kvantiteter till vissa prenumerationer.

 Du kan dela upp en reservation i två reservationer om PowerShell, CLI eller API.

### <a name="split-a-reservation-by-using-powershell"></a>Dela en reservation med hjälp av PowerShell

1. Hämta reservations orderns ID genom att köra följande kommando:

    ```powershell
    # Get the reservation orders you have access to
    Get-AzReservationOrder
    ```

2. Hämta information om en reservation:

    ```powershell
    Get-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a
    ```

3. Dela reservationen i två och distribuera instanserna:

    ```powershell
    # Split the reservation. The sum of the reservations, the quantity, must equal the total number of instances in the reservation that you're splitting.
    Split-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a -Quantity 3,2
    ```
4. Du kan uppdatera omfattningen genom att köra följande kommando:

    ```powershell
    Update-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId 5257501b-d3e8-449d-a1ab-4879b1863aca -AppliedScopeType Single -AppliedScope /subscriptions/15bb3be0-76d5-491c-8078-61fe3468d414
    ```

## <a name="cancel-exchange-or-refund-reservations"></a>Avbryta, byta ut eller återbetala reservationer

Du kan avbryta, byta ut eller återbetala reservationer med vissa begränsningar. Mer information finns i självbetjänings [utbyten och åter betalningar för Azure reservations](billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="change-optimize-setting-for-reserved-vm-instances"></a>Ändra optimerings inställning för reserverade VM-instanser

 När du köper en reserverad VM-instans väljer du storleks flexibilitet för instans storlek eller kapacitets prioritet. Flexibilitet för instans storlek tillämpar reservations rabatten på andra virtuella datorer i samma [storleks grupp för virtuella](https://aka.ms/RIVMGroups)datorer. Kapacitets prioritet prioriterar data centrets kapacitet för dina distributioner. Det här alternativet ger ytterligare förtroende för möjligheten att starta de virtuella dator instanserna när du behöver dem.

När reservations omfånget delas används som standard den flexibiliteten för instans storleken. Data Center kapaciteten prioriteras inte för distributioner av virtuella datorer.

För reservationer där omfånget är Single kan du optimera reservationen för kapacitets prioritet istället för storleks flexibilitet för virtuella dator instanser.

Så här uppdaterar du optimerings inställningen för reservationen:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **alla tjänster** > **reservationer**.
3. Välj reservationen.
4. Välj **Inställningar** > **Konfiguration**.
5. Ändra inställningen **optimera för** .

## <a name="optimize-reservation-use"></a>Optimera reservations användningen

Azure reservations besparingar är bara resultatet från varaktig resursanvändning. När du gör ett reservations köp betalar du en startkostnad för vad som i princip är 100% möjligt att använda resursanvändning över ett eller tre år. Försök att maximera reservationen för att få så mycket användning och besparingar som möjligt. I följande avsnitt beskrivs hur du övervakar en reservation och hur du optimerar användningen.

### <a name="view-reservation-use-in-the-azure-portal"></a>Visa reservations användning i Azure Portal

Ett sätt att Visa reservations användningen finns i Azure Portal.

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. Välj **alla tjänster** > [**reservationer**](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) och notera **användningen (%)** för en reservation.  
  ![Bild som visar listan över reservationer](./media/billing-manage-reserved-vm-instance/reservation-list.png)
3. Välj en reservation.
4. Granska reservationens användnings trend över tid.  
  ![Bild som visar reservations användning ](./media/billing-manage-reserved-vm-instance/reservation-utilization-trend.png)

### <a name="view-reservation-use-with-api"></a>Visa reservations användning med API

Om du är en Enterprise-avtal (EA)-kund kan du se hur bokningarna i organisationen används program mässigt. Du får oanvänd reservation via användnings data. När du granskar reservations avgifter bör du tänka på att data delas mellan den faktiska kostnaden och de periodiserade kostnaderna. Faktisk kostnad tillhandahåller data för att stämma av dig per månads faktura. Den har också information om reservations inköp och reservations program. Periodiserad kostnad är som den faktiska kostnaden, förutom att det effektiva priset för reservations användningen beräknas proportionellt. Oanvända reservations timmar visas i periodiserade kostnads data. Mer information om användnings data för EA-kunder finns i [hämta Enterprise-avtal reservations kostnader och användning](billing-understand-reserved-instance-usage-ea.md).

För andra prenumerations typer använder du sammanfattningar av API [-reservationer – lista efter reservations order och reservation](/rest/api/consumption/reservationssummaries/listbyreservationorderandreservation).

### <a name="optimize-your-reservation"></a>Optimera din reservation

Om du upptäcker att organisationens reservationer används:

- Se till att de virtuella datorerna som din organisation skapar matchar den virtuella dator storleken för reservationen.
- Kontrol lera att storleks flexibiliteten för en instans är på. Mer information finns i [hantera reservationer-ändra optimerings inställning för reserverade VM-instanser](#change-optimize-setting-for-reserved-vm-instances).
- Ändra omfånget för reservationen till _delat_ så att det kan användas mer brett. Mer information finns i [ändra omfånget för en reservation](#change-the-reservation-scope).
- Överväg att utbyta den oanvända mängden. Mer information finns i [annulleringar och byten](#cancel-exchange-or-refund-reservations).


## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en support förfrågan](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Reservations finns i följande artiklar:

- [Vad är reservationer för Azure?](billing-save-compute-costs-reservations.md)

Köp en service plan:
- [Förskottsbetala för Virtual Machines med Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Förskottsbetala för SQL Database-beräkningsresurser med reserverad kapacitet för Azure SQL Database](../sql-database/sql-database-reserved-capacity.md)
- [Förskotts betalning för Azure Cosmos DB resurser med Azure Cosmos DB reserverad kapacitet](../cosmos-db/cosmos-db-reserved-capacity.md)

Köp en program plan:
- [Förskottsbetal för Red Hat-programplaner från Azure Reservations](../virtual-machines/linux/prepay-rhel-software-charges.md)
- [Förskottsbetala för SUSE-programvaruplaner från Azure Reservations](../virtual-machines/linux/prepay-suse-software-charges.md)

Förstå rabatt och användning:
- [Förstå hur reservations rabatten för den virtuella datorn tillämpas](billing-understand-vm-reservation-charges.md)
- [Förstå hur rabatten för Red Hat Enterprise Linux program plan tillämpas](../billing/billing-understand-rhel-reservation-charges.md)
- [Ta reda på hur SUSE Linux Enterprise Software plan-rabatten tillämpas](../billing/billing-understand-suse-reservation-charges.md)
- [Förstå hur andra reservations rabatter tillämpas](billing-understand-reservation-charges.md)
- [Förstå reservations användningen för din prenumeration enligt principen betala per användning](billing-understand-reserved-instance-usage.md)
- [Förstå reservations användningen för företags registreringen](billing-understand-reserved-instance-usage-ea.md)
- [Windows-programkostnader som inte ingår i reservationer](billing-reserved-instance-windows-software-costs.md)
