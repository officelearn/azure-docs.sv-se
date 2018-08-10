---
title: Hantera Azure reservationer | Microsoft Docs
description: Lär dig hur du kan ändra omfång för prenumeration och hantera åtkomst för Azure reservationer.
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
ms.author: yashesvi
ms.openlocfilehash: d47c85d4197f45db50f1974b6faea270e6761237
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2018
ms.locfileid: "39628580"
---
# <a name="manage-reservations-for-resources-in-azure"></a>Hantera reservationer för resurser i Azure

När du köper en reservation med Azure kan du tillämpa reservationen till en annan prenumeration än den som du angav vid inköp. Om matchande virtuella datorer, SQL-databaser eller andra resurser som körs i flera prenumerationer, kan du också vill ändra reservationsomfånget som delas. För att maximera reservationsrabatten, se till att antalet instanser som du har köpt matchar de attribut och antalet resurser som du har som körs. Mer information finns i [Azure reservationer](https://go.microsoft.com/fwlink/?linkid=862121).

## <a name="change-the-scope-for-a-reservation"></a>Ändra omfattningen för en reservation

 Din reservationsrabatten gäller för virtuella datorer, SQL-databaser eller andra resurser som matchar din reservation och kör inom reservationsomfånget. En reservationens omfång kan vara enskild prenumeration eller i alla prenumerationer i din faktureringskontexten. Om du ställer in omfånget för enskild prenumeration matchade reservationen att köra resurser i den valda prenumerationen. Om du ställer in omfånget som delas matchar reservationen till resurser som körs i alla prenumerationer i kontexten fakturering i Azure. Fakturering kontexten är beroende av den prenumeration som används för att köpa reservationer.

Så här uppdaterar en reservationens omfång:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **alla tjänster** > **reservationer**.
3. Välj reservationen.
4. Välj **Inställningar** > **Konfiguration**.
5. Ändra omfattningen. Om du ändrar från delade till ett enda scope, kan du bara välja prenumerationer där du är ägare. Endast prenumerationer i samma fakturering kontext som reservationen, kan väljas. Faktureringskontexten bestäms av den prenumeration som du valde när reservationen köptes. Området gäller endast för erbjudandet om användningsbaserad betalning MS-AZR - 0003P prenumerationer och Enterprise erbjudandet MS-AZR - 0017P-prenumerationer. Utveckling/testning-prenumerationer är inte berättigade att hämta rabatten för enterprise-avtal.

## <a name="add-or-change-users-who-can-manage-a-reservation"></a>Lägga till eller ändra användare som kan hantera en reservation

Du kan delegera hantering av en reservation genom att lägga till användare till roller på reservationen. Som standard personen som har köpt reservationen och kontoadministratören ha rollen ägare på reservationen.

Du kan hantera åtkomst till reservationer oberoende av de prenumerationer som får rabatten. När du ger någon behörighet att hantera en reservation som inte ger dem behörighet att hantera prenumerationen. Och om du ger någon behörighet att hantera en prenumeration inom de reservationsomfånget, som inte ge dem behörighet att hantera reservationen.

Att delegera hantering för en reservation:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **alla tjänster** > **Reservation** till listan reservationer som du har åtkomst till.
3. Markerar du den som du vill delegera åtkomst till andra användare.
4. Välj **åtkomstkontroll (IAM)** på menyn.
5. Välj **Lägg till** > **rollen** > **ägare** (eller en annan roll om du vill ge begränsad åtkomst).
6. Ange e-postadressen för den användare som du vill lägga till som ägare. 
7. Välj användaren och sedan **Spara**.

## <a name="optimize-reserved-vm-instance-for-vm-size-flexibility-or-capacity-priority"></a>Optimera reserverade VM-instanser för VM-storlek flexibilitet eller kapacitet prioritet

 Flexibilitet för Virtuella datorinstanser tillämpas reservationsrabatten på andra virtuella datorer i samma [VM storleksgruppen](https://aka.ms/RIVMGroups). Som standard när den reservationens omfång delas flexibilitet för instans-storlek är på och datacenterkapacitet inte är prioriterad för VM-distributioner. Du kan optimera reservationen kapacitetsprioritet i stället för flexibilitet för Virtuella datorinstanser storlek för reservationer som där omfånget är enkel. Prioritet för datacenterkapacitet reserverar datacentrets kapacitet för dina distributioner, erbjuder ytterligare förtroende i din möjlighet att starta VM-instanser när så behövs.

Så här uppdaterar en reservationens omfång:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **alla tjänster** > **reservationer**.
3. Välj reservationen.
4. Välj **Inställningar** > **Konfiguration**.
5. Ändra Optimera för inställningen.

## <a name="split-a-single-reservation-into-two-reservations"></a>Dela en enda reservation i två reservationer

 När du köper fler än en instans kan du tilldela olika prenumerationer instanser inom en reservation. Som standard alla instanser (kvantitet angavs vid köpet) har ett scope - antingen enstaka prenumeration eller delat. Exempel: du har köpt 10 Standard D2 virtuella datorer och angav omfång till prenumeration A. Du kan nu vill ändra omfattningen för sju reservationer i prenumeration A och återstående 3 till B. dela upp en reservation-prenumeration kan du distribuera instanser för hantering av detaljerade säkerhetsomfattning. Du kan förenkla allokering till prenumerationer genom att välja delade omfattningen. Men för cost management eller budgetering, kan du allokera kvantiteter till specifika prenumerationer.

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

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om Azure reservationer, finns i följande artiklar:

- [Vad är Azure reservationer?](billing-save-compute-costs-reservations.md)
- [Förskottsbetala för virtuella datorer med Azure reserverade VM-instanser](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Betala i förskott för beräkningsresurser för SQL-databas med Azure SQL Database reserverad kapacitet](../sql-database/sql-database-reserved-capacity.md)
- [Förstå hur reservationsrabatten tillämpas](billing-understand-vm-reservation-charges.md)
- [Förstå användningen av reservation för prenumerationen med användningsbaserad betalning](billing-understand-reserved-instance-usage.md)
- [Förstå användningen av reserverade för din Enterprise-registrering](billing-understand-reserved-instance-usage-ea.md)
- [Kostnader för Windows-programvara ingår inte i reservationer](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du har fler frågor, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att lösa problemet snabbt.
