---
title: Hantera Azure reserverade virtuella instanser | Microsoft Docs
description: "Lär dig hur du kan ändra omfång för prenumeration och hantera åtkomst för Azure reserverade VM-instanser."
services: billing
documentationcenter: 
author: vikramdesai01
manager: vikramdesai01
editor: 
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/06/2017
ms.author: vikdesai
ms.openlocfilehash: f3f5f974630c4bf1c68599e26612ed729b55bcfc
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="manage-reserved-virtual-machine-instances"></a>Hantera reserverade virtuella Datorinstanser

När du köper en Azure-reserverade VM-instans kan du vill använda reservationen till en annan prenumeration än den som angetts under inköp. Om matchande virtuella datorer körs i flera prenumerationer, kan du också vill ändra omfattningen för reservation delad. Om du vill maximera reservation rabatt, se till att antalet instanser som du har köpt matchar attribut och antalet virtuella datorer som du har körs. Mer information om reserverade virtuella instanser finns [spara pengar genom före betalar för virtuella datorer i Azure](https://go.microsoft.com/fwlink/?linkid=862121).

## <a name="change-the-scope-for-a-reservation"></a>Ändra omfattningen för en reservation
 Reservation-lågpristaxa gäller för virtuella datorer som matchar din reservation och kör inom omfånget för reservation. Omfånget för en reservation kan vara enda prenumeration eller alla prenumerationer i din faktureringsadress kontext. Om du ställer in omfånget för en enda prenumeration matchas reservationen till virtuella datorer som körs i den valda prenumerationen. Om du ställer in omfånget till delade, Azure matchar reservationen virtuella datorer som körs i alla prenumerationer inom ramen för fakturering. Fakturering kontexten är beroende av den prenumeration som används för att köpa reservationen. Läs mer i [före betala för virtuella datorer med VM-instanser som är reserverade](https://go.microsoft.com/fwlink/?linkid=861721).

Så här uppdaterar omfånget för en reservation: 
1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **alla tjänster** > **reservationer**.
3. Välj reservationen.
4. Välj **inställningar** > **Configuration**.
5. Ändra omfattningen. Om du ändrar från delad till ett enda scope, kan du bara välja prenumerationer där du är ägare. Endast prenumerationer inom samma fakturering kontext som reservationen, kan väljas. Kontexten fakturering bestäms av den prenumeration som du valde när reservationen köptes. Området gäller endast betala MS-AZR - 0003P prenumerationer och Enterprise erbjudande MS-AZR - 0017P prenumerationer. Utveckling och testning prenumerationer är inte rätt att få rabatt för reservation för enterprise-avtal.

## <a name="split-a-single-reservation-into-two-reservations"></a>Dela en enda reservation i två reservationer
 När du köper fler än en instans kan du vill tilldela instanser inom en reservation för olika prenumerationer. Som standard är alla instanser (antal anges vid köpet) har ett scope - antingen enda prenumeration eller delad. Till exempel du köpt 10 Standard D2 VMs och ange omfånget för prenumerationen A. Du nu vill ändra omfång för 7 reserverade VM-instanser till prenumeration A och den återstående 3 till prenumeration B. dela en reservation kan du distribuera instanser för detaljerade omfångshantering. Du kan förenkla tilldelning till prenumerationer genom att välja delade scope. Men du kan allokera kvantiteter till specifika prenumerationer för kostnad hanterings- eller budget är.

 Du kan dela en reservation i två reservationer om PowerShell, CLI eller via API: et.

### <a name="split-a-reservation-by-using-powershell"></a>Dela en reservation med hjälp av PowerShell
1. Hämta reservation order-ID genom att köra följande kommando:

    ```powershell
    # Get the reservation orders you have access to
    Get-AzureRmReservationOrder
    ```
2. Hämta information om en reservation:

    ```powershell
    Get-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a
    ```
3. Dela reservationen i två och distribuera instanser:

    ```powershell
    # Split the reservation. The sum of the Reserved VM instances, the quantity, must equal the total number of instances in the reservation that you're splitting.
    Split-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a -Quantity 3,2
    ```
1. Du kan uppdatera omfång genom att köra följande kommando:

    ```powershell
    Update-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId 5257501b-d3e8-449d-a1ab-4879b1863aca -AppliedScopeType Single -AppliedScope /subscriptions/15bb3be0-76d5-491c-8078-61fe3468d414
    ```

## <a name="add-or-change-users-who-can-manage-a-reservation"></a>Lägga till eller ändra användare som kan hantera en reservation
Du kan delegera hanteringen av en reservation genom att lägga till användare till roller på reservationen. Som standard personen som har köpt reservationen och kontoadministratören har rollen som ägare på reservationen. 

Du kan hantera åtkomst till reservationer oberoende från prenumerationer som får rabatt för reservation. När du ge någon behörighet att hantera en reservation som inte ger dem behörighet att hantera prenumerationen. Och om du ge någon behörighet att hantera en prenumeration inom den reservationen omfång, som inte ge dem behörighet att hantera reservationen.
 
Att delegera hantering för en reservation: 
1.  Logga in på [Azure-portalen](https://portal.azure.com).
2.  Välj **alla tjänster** > **Reservation** till listan reservationer som du har åtkomst till.
3.  Välj reservationen som du vill delegera åtkomst till andra användare.
4.  Välj **Access Control (IAM)** på menyn.
5.  Välj **Lägg till** > **rollen** > **ägare** (eller en annan roll om du vill ge begränsad åtkomst). 
6. Skriv e-postadressen för den användare som du vill lägga till som ägare. 
7. Välj användaren och välj sedan **spara**.

## <a name="next-steps"></a>Nästa steg
Mer information om reserverade instanser för virtuella datorer, finns i följande artiklar.

- [Förskottsbetala virtuella datorer med reserverade VM-instanser](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Spara pengar på virtuella datorer med den reserverade virtuella instanser](billing-save-compute-costs-reservations.md)
- [Förstå hur reserverade virtuella datorinstans rabatt används](billing-understand-vm-reservation-charges.md)
- [Förstå reserverade användning för din prenumeration med användningsbaserad betalning](billing-understand-reserved-instance-usage.md)
- [Förstå reserverade användning för Enterprise-registrering](billing-understand-reserved-instance-usage-ea.md)
- [Windows programvarukostnader ingår inte i reserverade instanser](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du fortfarande har fler frågor, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) få snabbt lösa problemet.
