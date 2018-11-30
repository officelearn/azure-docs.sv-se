---
title: Hantera Azure reservationer | Microsoft Docs
description: Lär dig hur du kan ändra omfång för prenumeration och hantera åtkomst för Azure-reservationer.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2018
ms.author: cwatson
ms.openlocfilehash: 6a070d9ebd46d117041276436107abcc958d58d4
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/29/2018
ms.locfileid: "52583745"
---
# <a name="manage-reservations-for-azure-resources"></a>Hantera reservationer för Azure-resurser

När du köper en Reservation för Azure kan behöva du använda reservationen i en annan prenumeration, ändra vem som kan hantera reservationen eller ändra omfattningen för reservationen. Du kan också dela upp en reservation i två reservationer att tillämpa vissa av de instanser som du har köpt till en annan prenumeration.

Om du har köpt Azure Reserved Virtual Machine Instances kan du ändra inställningen Optimera för reservationen. Rabatten kan använda för virtuella datorer i samma serie eller reservera datacentrets kapacitet för en specifik VM-storlek.

## <a name="change-the-scope-for-a-reservation"></a>Ändra omfattningen för en reservation

 Din reservationsrabatten gäller för virtuella datorer, SQL-databaser, Azure Cosmos DB eller andra resurser som matchar din reservation och kör inom reservationsomfånget. En reservationens omfång kan vara enskild prenumeration eller i alla prenumerationer i din faktureringskontexten. Om du ställer in omfånget för enskild prenumeration matchade reservationen att köra resurser i den valda prenumerationen. Om du ställer in omfånget som delas matchar reservationen till resurser som körs i alla prenumerationer i kontexten fakturering i Azure. Fakturering kontexten är beroende av den prenumeration som används för att köpa reservationer.

Så här uppdaterar en reservationens omfång:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **alla tjänster** > **reservationer**.
3. Välj reservationen.
4. Välj **Inställningar** > **Konfiguration**.
5. Ändra omfattningen. 

Om du ändrar från delade till ett enda scope, kan du bara välja prenumerationer där du är ägare. Endast prenumerationer i samma fakturering kontext som reservationen, kan väljas.

Området gäller endast för betala per användning-erbjudandet MS-AZR - 0003P, MS-AZR - 0017P erbjudande för Enterprise eller typer av CSP-prenumerationer. Utveckling/testning-prenumerationer är inte berättigade att hämta rabatten för enterprise-avtal.

## <a name="add-or-change-users-who-can-manage-a-reservation"></a>Lägga till eller ändra användare som kan hantera en reservation

Du kan delegera hantering av en reservation genom att lägga till användare till roller på reservationen. Som standard personen som har köpt reservationen och kontoadministratören ha rollen ägare på reservationen.

Du kan hantera åtkomst till reservationer oberoende av de prenumerationer som får rabatten. När du ger någon behörighet att hantera en reservation som inte ger dem behörighet att hantera prenumerationen. Och om du ger någon behörighet att hantera en prenumeration inom de reservationsomfånget, som inte ge dem behörighet att hantera reservationen.

Att delegera hantering för en reservation:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **alla tjänster** > **Reservation** till listan reservationer som du har åtkomst till.
3. Markerar du den som du vill delegera åtkomst till andra användare.
4. Välj **åtkomstkontroll (IAM)**.
5. Välj **lägga till** > **rollen** > **ägare**. Eller, om du vill ge begränsad åtkomst, Välj en annan roll.
6. Ange e-postadressen för den användare som du vill lägga till som ägare.
7. Välj användaren och sedan **Spara**.

## <a name="split-a-single-reservation-into-two-reservations"></a>Dela en enda reservation i två reservationer

 När du köper fler än en resursinstans inom en reservation kan du tilldela instanser inom det reserverade till olika prenumerationer. Som standard alla instanser har ett scope - antingen enstaka prenumeration eller delat. Exempelvis kan du köpt 10 reserverade instanser och angav omfång till prenumeration A. Du kan nu vill ändra omfattningen för 7 reservationer i prenumeration A och återstående 3 till B. dela upp en reservation-prenumeration kan du distribuera instanser för hantering av detaljerade säkerhetsomfattning. Du kan förenkla allokering till prenumerationer genom att välja delade omfattningen. Men för cost management eller budgetering, kan du allokera kvantiteter till specifika prenumerationer.

 Du kan dela upp en reservation i två reservationer och PowerShell, CLI, eller via API: et.

### <a name="split-a-reservation-by-using-powershell"></a>Dela upp en reservation med hjälp av PowerShell

1. Hämta reservationsbeställnings-ID genom att köra följande kommando:

    ```powershell
    # Get the reservation orders you have access to
    Get-AzureRmReservationOrder
    ```

2. Hämta information om en reservation:

    ```powershell
    Get-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a
    ```

3. Dela upp reservationen i två och distribuera instanser:

    ```powershell
    # Split the reservation. The sum of the reservations, the quantity, must equal the total number of instances in the reservation that you're splitting.
    Split-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a -Quantity 3,2
    ```
4. Du kan uppdatera omfattningen genom att köra följande kommando:

    ```powershell
    Update-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId 5257501b-d3e8-449d-a1ab-4879b1863aca -AppliedScopeType Single -AppliedScope /subscriptions/15bb3be0-76d5-491c-8078-61fe3468d414
    ```

## <a name="cancellations-and-exchanges"></a>Avbokningar och utbyten

Beroende på reservationstyp av kan du annullera eller byta en reservation. Mer information finns i annullering och utbyten avsnitt i följande avsnitt:

- [Förskottsbetala för Virtual Machines med Azure Reserved VM Instances](..//virtual-machines/windows/prepay-reserved-vm-instances.md#cancellations-and-exchanges)
- [Förskottsbetala för SUSE-programvaruplaner från Azure Reservations](../virtual-machines/linux/prepay-suse-software-charges.md#cancellation-and-exchanges-not-allowed)
- [Förskottsbetala för SQL Database-beräkningsresurser med reserverad kapacitet för Azure SQL Database](../sql-database/sql-database-reserved-capacity.md#cancellations-and-exchanges)

## <a name="change-optimize-setting-for-reserved-vm-instances"></a>Ändra optimera inställning för reserverade VM-instanser

 När du köper en reserverad VM-instans kan välja du instans storlek flexibilitet eller kapacitetsprioritet. Instans storlek flexibilitet gäller reservationsrabatten på andra virtuella datorer i samma [VM storleksgruppen](https://aka.ms/RIVMGroups). Prioritet för datacenterkapacitet prioriterar datacentrets kapacitet för dina distributioner. Det här alternativet erbjuder ytterligare förtroende på din möjlighet att starta VM-instanser när du behöver dem.

När den reservationens omfång delas som standard är flexibilitet för datorinstanser storlek på. Datacentrets kapacitet inte är prioriterad för VM-distributioner.

Du kan optimera reservationen kapacitetsprioritet i stället för flexibilitet för Virtuella datorinstanser storlek för reservationer som där omfånget är enkel.

Så här uppdaterar du optimera inställningen för reservationen:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **alla tjänster** > **reservationer**.
3. Välj reservationen.
4. Välj **Inställningar** > **Konfiguration**.
5. Ändra den **Optimera för** inställningen.

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om Azure reservationer, finns i följande artiklar:

- [Vad är Azure reservationer?](billing-save-compute-costs-reservations.md)
- [Förskottsbetala för Virtual Machines med Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Förskottsbetala för SQL Database-beräkningsresurser med reserverad kapacitet för Azure SQL Database](../sql-database/sql-database-reserved-capacity.md)
- [Betala i förskott för Azure Cosmos DB-resurser med Azure Cosmos DB reserverad kapacitet](../cosmos-db/cosmos-db-reserved-capacity.md)
- [Förskottsbetala för SUSE-programvaruplaner från Azure Reservations](../virtual-machines/linux/prepay-suse-software-charges.md)
- [Förstå hur VM-reservationsrabatten tillämpas](billing-understand-vm-reservation-charges.md)
- [Förstå hur SUSE Linux Enterprise software plan rabatten](../billing/billing-understand-suse-reservation-charges.md)
- [Förstå hur andra reservationen rabatter](billing-understand-reservation-charges.md)
- [Förstå användningen av reservation för prenumerationen med användningsbaserad betalning](billing-understand-reserved-instance-usage.md)
- [Förstå användningen av reserverade för din Enterprise-registrering](billing-understand-reserved-instance-usage-ea.md)
- [Kostnader för Windows-programvara ingår inte i reservationer](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
