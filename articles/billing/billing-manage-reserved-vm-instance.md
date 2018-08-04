---
title: Hantera Azure reserverade VM-instanser | Microsoft Docs
description: Lär dig hur du kan ändra omfång för prenumeration och hantera åtkomst för reserverade VM-instanser.
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
ms.date: 07/31/2018
ms.author: yashesvi
ms.openlocfilehash: 589afc736faaa210fdcd5cf6c79d10af4af3c0e9
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2018
ms.locfileid: "39502533"
---
# <a name="manage-reserved-instances-in-azure"></a>Hantera reserverade instanser i Azure

När du köper en reserverad VM-instans för Azure kan du tillämpa den reserverade instansen till en annan prenumeration än den som angetts vid inköp. Om matchande virtuella datorer körs i flera prenumerationer, kan du också vill ändra reserverade instansens omfattning som delas. För att maximera rabatten för reserverad instans, se till att antalet instanser som du har köpt matchar de attribut och antalet virtuella datorer som du har som körs. Läs mer om Azure reserverade instanser i [spara pengar genom att betala förväg för Azure virtual machines](https://go.microsoft.com/fwlink/?linkid=862121).

## <a name="change-the-scope-for-a-reserved-instance"></a>Ändra omfånget för en reserverad instans
 Din reserverade instans-rabatten gäller för virtuella datorer som matchar din reserverade instans och körs på reserverade instansens omfattning. Omfattningen för en reserverad instans kan vara enskild prenumeration eller i alla prenumerationer i din faktureringskontexten. Om du ställer in omfånget för enskild prenumeration matchade den reserverade instansen för virtuella datorer som körs i den valda prenumerationen. Om du anger omfång till delade, Azure matchar den reserverade instansen för virtuella datorer som körs i alla prenumerationer i kontexten för fakturering. Fakturering kontexten är beroende av den prenumeration som används för att köpa den reserverade instansen. Mer information finns i [förskottsbetalning för virtuella datorer med reserverade instanser](https://go.microsoft.com/fwlink/?linkid=861721).

Så här uppdaterar omfånget för en reserverad instans: 
1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **alla tjänster** > **reservationer**.
3. Välj den reserverade instansen.
4. Välj **Inställningar** > **Konfiguration**.
5. Ändra omfattningen. Om du ändrar från delade till ett enda scope, kan du bara välja prenumerationer där du är ägare. Du kan välja endast prenumerationer i samma fakturering kontext som den reserverade instansen. Faktureringskontexten bestäms av den prenumeration du valde när du har köpt den reserverade instansen. Området gäller endast för erbjudandet om användningsbaserad betalning MS-AZR - 0003P prenumerationer och Enterprise erbjudandet MS-AZR - 0017P-prenumerationer. Utveckling/testning-prenumerationer är inte berättigade att hämta rabatten för reserverad instans för enterprise-avtal.

## <a name="add-or-change-users-who-can-manage-a-reserved-instance"></a>Lägga till eller ändra användare som kan hantera en reserverad instans
Du kan delegera hantering av en reserverad instans genom att lägga till användare till roller på den reserverade instansen. Som standard personen som har köpt den reserverade instansen och kontoadministratören ha rollen ägare på den reserverade instansen. 

Du kan hantera åtkomst till reserverade instanser oberoende av de prenumerationer som får rabatten för reserverad instans. När du ger någon behörighet att hantera en reserverad instans som inte ger dem behörighet att hantera prenumerationen. Och om du ger någon behörighet att hantera en prenumeration inom omfånget för den reserverade instansen, som inte ge dem behörighet att hantera den reserverade instansen.
 
Att delegera åtkomsthantering för en reserverad instans: 
1.  Logga in på [Azure Portal](https://portal.azure.com).
2.  Välj **alla tjänster** > **Reservation** visa en lista över reserverade instanser som du har åtkomst till.
3.  Välj den reserverade instansen som du vill delegera åtkomst till andra användare.
4.  Välj **åtkomstkontroll (IAM)** på menyn.
5.  Välj **Lägg till** > **rollen** > **ägare** (eller en annan roll om du vill ge begränsad åtkomst). 
6. Ange e-postadressen för den användare som du vill lägga till som ägare. 
7. Välj användaren och välj sedan **spara**.

## <a name="optimize-reserved-vm-instance-for-vm-size-flexibility-or-capacity-priority"></a>Optimera reserverade VM-instanser för VM-storlek flexibilitet eller kapacitet prioritet
 Flexibilitet för Virtuella datorinstanser tillämpas reservationsrabatten på andra virtuella datorer i samma [VM storleksgruppen](https://aka.ms/RIVMGroups). Som standard när den reservationens omfång delas flexibilitet för instans-storlek är på och datacenterkapacitet inte är prioriterad för VM-distributioner. Du kan optimera reservationen kapacitetsprioritet i stället för flexibilitet för Virtuella datorinstanser storlek för reservationer som där omfånget är enkel. Prioritet för datacenterkapacitet reserverar datacentrets kapacitet för dina distributioner, erbjuder ytterligare förtroende i din möjlighet att starta VM-instanser när så behövs.

Så här uppdaterar omfånget för en reserverad instans: 
1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **alla tjänster** > **reservationer**.
3. Välj den reserverade instansen.
4. Välj **Inställningar** > **Konfiguration**.
5. Ändra Optimera för inställningen.

## <a name="split-a-single-reserved-instance-into-two-reserved-instances"></a>Dela en reserverad instans i två reserverade instanser
 När du köper fler än en instans kan du tilldela olika prenumerationer instanser inom en reserverad instans. Som standard alla instanser (kvantitet angavs vid köpet) har ett scope - antingen enstaka prenumeration eller delat. Exempel: du har köpt 10 Standard D2 virtuella datorer och angav omfång till prenumeration A. Du kan nu vill ändra omfattningen för sju reserverade instanser för prenumeration A och återstående 3 till B. dela en reserverad instans-prenumeration kan du distribuera instanser för hantering av detaljerade säkerhetsomfattning. Du kan förenkla allokering till prenumerationer genom att välja delade omfattningen. Men för cost management eller budgetering, kan du allokera kvantiteter till specifika prenumerationer.

 Du kan dela upp en reserverad instans i två reserverade instanser om PowerShell, CLI, eller via API: et.

### <a name="split-a-reserved-instance-by-using-powershell"></a>Dela en reserverad instans med hjälp av PowerShell
1. Hämta order-ID för reserverad instans genom att köra följande kommando:

    ```powershell
    # Get the reserved instance orders you have access to
    Get-AzureRmReservationOrder
    ```

2. Hämta information om en reserverad instans:

    ```powershell
    Get-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a
    ```

3. Dela upp den reserverade instansen i två och distribuera instanser:

    ```powershell
    # Split the reserved instance. The sum of the reserved instances, the quantity, must equal the total number of instances in the reserved instance that you're splitting.
    Split-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a -Quantity 3,2
    ```

1. Du kan uppdatera omfattningen genom att köra följande kommando:

    ```powershell
    Update-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId 5257501b-d3e8-449d-a1ab-4879b1863aca -AppliedScopeType Single -AppliedScope /subscriptions/15bb3be0-76d5-491c-8078-61fe3468d414
    ```

## <a name="next-steps"></a>Nästa steg
Mer information om reserverade instanser för Azure finns i följande artiklar:

- [Vad är Azure Reserved VM Instances?](billing-save-compute-costs-reservations.md)
- [Förskottsbetala för virtuella datorer med Azure reserverade VM-instanser](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Förstå tillämpningen av rabatter för Reserved Instances](billing-understand-vm-reservation-charges.md)
- [Förstå användningen av reserverade instanser för din Betala per användning-prenumeration](billing-understand-reserved-instance-usage.md)
- [Förstå användningen av reserverade instanser för Enterprise-registrering](billing-understand-reserved-instance-usage-ea.md)
- [Kostnader för Windows-programvara ingår inte i reserverade instanser](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du har fler frågor, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att lösa problemet snabbt.
