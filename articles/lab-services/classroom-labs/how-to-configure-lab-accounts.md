---
title: Konfigurera labb konton i Azure Lab Services | Microsoft Docs
description: Den här artikeln beskriver hur du skapar ett labb konto, visar alla labb konton eller tar bort ett labb konto i Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: fa9dba62b3b58687ec6a2bfc29e8722f7016b679
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79284309"
---
# <a name="configure-lab-accounts-in-azure-lab-services"></a>Konfigurera labb konton i Azure Lab Services 
I Azure Lab Services är ett labb konto en behållare för hanterade labb typer, till exempel klass rums labb. En administratör konfigurerar ett labb konto med Azure Lab Services och ger till gång till labb ägare som kan skapa labb i kontot. 

Den här artikeln visar hur du utför följande uppgifter: 

- Ange ett adress intervall för virtuella datorer i labbet
- Konfigurera automatisk avstängning av virtuella datorer vid från koppling

## <a name="specify-an-address-range-for-vms-in-the-lab"></a>Ange ett adress intervall för virtuella datorer i labbet
Följande procedur innehåller steg för att ange ett adress intervall för virtuella datorer i labbet. Om du uppdaterar intervallet som du har angett gäller det ändrade adress intervallet endast för virtuella datorer som skapas när ändringen har gjorts. 

Här följer några begränsningar när du anger adress intervallet som du bör ha i åtanke. 

- Prefixet måste vara mindre än eller lika med 23. 
- Om ett virtuellt nätverk är peer-kopplat till labb kontot kan det angivna adress intervallet inte överlappa med adress intervallet från det peer-kopplade virtuella nätverket.

1. På sidan **labb konto** väljer du **labb inställningar** på den vänstra menyn.
2. I fältet **adress intervall** anger du adress intervall för virtuella datorer som ska skapas i labbet. Adress intervallet bör vara i CIDR-notation (Classless Inter-Domain routing) (exempel: 10.20.0.0/23). Virtuella datorer i labbet skapas i det här adress intervallet.
3. Välj **Spara** i verktygsfältet. 

    ![Konfigurera adress intervall](../media/how-to-manage-lab-accounts/labs-configuration-page-address-range.png)


## <a name="automatic-shutdown-of-vms-on-disconnect"></a>Automatisk avstängning av virtuella datorer vid från koppling
Du kan aktivera eller inaktivera automatisk avstängning av virtuella Windows Lab-datorer (mall eller student) när en anslutning till fjärr skrivbord är frånkopplad. Du kan också ange hur länge de virtuella datorerna ska vänta tills användaren ansluter igen innan den stängs av automatiskt.

![Inställning för automatisk avstängning i labb konto](../media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)

Den här inställningen gäller för alla labb som skapats i labb kontot. En labb ägare kan åsidosätta den här inställningen på labb nivån. Ändringen av den här inställningen på labb kontot påverkar bara labb som skapas när ändringen har gjorts.

Information om hur en labb ägare kan konfigurera den här inställningen på labb nivån finns i [den här artikeln](how-to-enable-shutdown-disconnect.md)

## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- [Tillåt labb skapare att välja labb plats](allow-lab-creator-pick-lab-location.md)
- [Anslut ditt labb nätverk till ett peer-virtuellt nätverk](how-to-connect-peer-virtual-network.md)
- [Koppla ett delat avbildnings galleri till ett labb](how-to-attach-detach-shared-image-gallery.md)
- [Lägg till en användare som labb ägare](how-to-add-user-lab-owner.md)
- [Visa brand Väggs inställningar för ett labb](how-to-configure-firewall-settings.md)
