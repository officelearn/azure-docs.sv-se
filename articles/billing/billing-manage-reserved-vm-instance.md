---
title: Hantera Azure reserverade instanser - Azure fakturering | Microsoft Docs
description: Lär dig hur du kan ändra omfång för prenumeration och hantera åtkomst för Azure reserverade VM-instanser.
services: billing
documentationcenter: ''
author: vikramdesai01
manager: vikramdesai01
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2018
ms.author: vikdesai
ms.openlocfilehash: fc473906be9c572e6d6549c85f9faa8fe7566b86
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2018
---
# <a name="manage-reserved-instances"></a>Hantera reserverade instanser

När du köper en Azure-reserverade VM-instans kan du vill tillämpa den reserverade instansen till en annan prenumeration än den som angetts under inköp. Om matchande virtuella datorer körs i flera prenumerationer, kan du också vill ändra omfattningen för reserverade delad. Om du vill maximera reserverade rabatt, se till att antalet instanser som du har köpt matchar attribut och antalet virtuella datorer som du har körs. Läs mer om Azure reserverade instanser i [spara pengar genom före betalar för virtuella datorer i Azure](https://go.microsoft.com/fwlink/?linkid=862121).

## <a name="change-the-scope-for-a-reserved-instance"></a>Ändra omfattningen för en reserverad instans
 Reserverade rabatt gäller för virtuella datorer som matchar din reserverade instans och körs reserverade scope. Omfånget för en reserverad instans kan vara enda prenumeration eller alla prenumerationer i din faktureringsadress kontext. Om du ställer in omfånget för en enda prenumeration matchas reserverade instans till virtuella datorer som körs i den valda prenumerationen. Om du ställer in omfånget till delade, Azure matchar reserverade instans till virtuella datorer som körs i alla prenumerationer inom ramen för fakturering. Fakturering kontexten är beroende av den prenumeration som används för att köpa reserverade instansen. Läs mer i [före betala för virtuella datorer med reserverade instanser](https://go.microsoft.com/fwlink/?linkid=861721).

Så här uppdaterar omfånget för en reserverad instans: 
1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **alla tjänster** > **reservationer**.
3. Välj den reserverade instansen.
4. Välj **inställningar** > **Configuration**.
5. Ändra omfattningen. Om du ändrar från delad till ett enda scope, kan du bara välja prenumerationer där du är ägare. Endast prenumerationer inom samma fakturering kontext som den reserverade instansen kan väljas. Kontexten fakturering bestäms av den prenumeration som du valde när reserverade instansen har köpt. Området gäller endast betala MS-AZR - 0003P prenumerationer och Enterprise erbjudande MS-AZR - 0017P prenumerationer. Utveckling och testning prenumerationer är inte rätt att få rabatt för reserverade för enterprise-avtal.

## <a name="split-a-single-reserved-instance-into-two-reserved-instances"></a>Dela en enda reserverade instans i två reserverade instanser
 När du köper fler än en instans kan du vill tilldela olika prenumerationer instanser inom ett reserverat instans. Som standard är alla instanser (antal anges vid köpet) har ett scope - antingen enda prenumeration eller delad. Till exempel du köpt 10 Standard D2 VMs och ange omfånget för prenumerationen A. Du kan nu vill ändra omfattningen för sju reserverade instanser till prenumeration A och återstående 3 till prenumeration B. dela en reserverad instans kan du distribuera instanser för detaljerade omfattningen management. Du kan förenkla tilldelning till prenumerationer genom att välja delade scope. Men du kan allokera kvantiteter till specifika prenumerationer för kostnad hanterings- eller budget är.

 Du kan dela en instans som är reserverade i två reserverade instanser om PowerShell, CLI eller via API: et.

### <a name="split-a-reserved-instance-by-using-powershell"></a>Dela en reserverad instans med hjälp av PowerShell
1. Hämta ordning reserverade instans-ID genom att köra följande kommando:

    ```powershell
    # Get the Reserved Instance orders you have access to
    Get-AzureRmReservationOrder
    ```
2. Hämta information om reserverade instans:

    ```powershell
    Get-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a
    ```
3. Dela upp den reserverade instans på två och distribuera instanser:

    ```powershell
    # Split the Reserved Instance. The sum of the Reserved Instances, the quantity, must equal the total number of instances in the Reserved Instance that you're splitting.
    Split-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a -Quantity 3,2
    ```
1. Du kan uppdatera omfång genom att köra följande kommando:

    ```powershell
    Update-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId 5257501b-d3e8-449d-a1ab-4879b1863aca -AppliedScopeType Single -AppliedScope /subscriptions/15bb3be0-76d5-491c-8078-61fe3468d414
    ```

## <a name="add-or-change-users-who-can-manage-a-reserved-instance"></a>Lägga till eller ändra användare som kan hantera en reserverad instans
Du kan delegera hanteringen av ett reserverat instans genom att lägga till personer roller på reserverade-instansen. Som standard har den person som har köpt den instans som är reserverade och kontoadministratören rollen ägare på reserverade-instansen. 

Du kan hantera åtkomst till reserverade instanser oberoende från prenumerationer som får reserverade rabatt. När du ge någon behörighet att hantera en reserverad-instans som inte ger dem behörighet att hantera prenumerationen. Och om du ge någon behörighet att hantera en prenumeration inom omfånget för den reserverade instansen, som inte ge dem behörighet att hantera den reserverade instansen.
 
Att delegera hantering för en reserverad instans: 
1.  Logga in på [Azure-portalen](https://portal.azure.com).
2.  Välj **alla tjänster** > **Reservation** listan reserverade instanser som du har åtkomst till.
3.  Välj den reserverade instans som du vill delegera åtkomst till andra användare.
4.  Välj **Access Control (IAM)** på menyn.
5.  Välj **Lägg till** > **rollen** > **ägare** (eller en annan roll om du vill ge begränsad åtkomst). 
6. Skriv e-postadressen för den användare som du vill lägga till som ägare. 
7. Välj användaren och välj sedan **spara**.

## <a name="next-steps"></a>Nästa steg
Mer information om Azure reserverade instanser finns i följande artiklar:

- [Spara pengar på virtuella datorer med Azure reserverade instanser](billing-save-compute-costs-reservations.md)
- [Förskottsbetala virtuella datorer med reserverade instanser](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Förstå hur de reserverade rabatterna används](billing-understand-vm-reservation-charges.md)
- [Förstå reserverade användning för din prenumeration med användningsbaserad betalning](billing-understand-reserved-instance-usage.md)
- [Förstå reserverade användning för Enterprise-registrering](billing-understand-reserved-instance-usage-ea.md)
- [Windows programvarukostnader ingår inte i reserverade instanser](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du fortfarande har fler frågor, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) få snabbt lösa problemet.
