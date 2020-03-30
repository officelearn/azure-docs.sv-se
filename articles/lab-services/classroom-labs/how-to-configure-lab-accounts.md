---
title: Konfigurera labbkonton i Azure Lab Services | Microsoft-dokument
description: I den här artikeln beskrivs hur du skapar ett labbkonto, visar alla labbkonton eller tar bort ett labbkonto i Azure Lab Services.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284309"
---
# <a name="configure-lab-accounts-in-azure-lab-services"></a>Konfigurera labbkonton i Azure Lab Services 
I Azure Lab Services är ett labbkonto en behållare för hanterade labbtyper som klassrumslabb. En administratör skapar ett labbkonto med Azure Lab Services och ger åtkomst till labbägare som kan skapa labb i kontot. 

Den här artikeln visar hur du utför följande uppgifter: 

- Ange ett adressintervall för virtuella datorer i labbet
- Konfigurera automatisk avstängning av virtuella datorer vid frånkoppling

## <a name="specify-an-address-range-for-vms-in-the-lab"></a>Ange ett adressintervall för virtuella datorer i labbet
Följande procedur har steg för att ange ett adressintervall för virtuella datorer i labbet. Om du uppdaterar det intervall som du tidigare angav gäller det ändrade adressintervallet endast för virtuella datorer som skapas när ändringen gjordes. 

Här är några begränsningar när du anger det adressintervall som du bör tänka på. 

- Prefixet måste vara mindre än eller lika med 23. 
- Om ett virtuellt nätverk är peer-peered till labbkontot, kan det angivna adressintervallet inte överlappa med adressintervall från peered virtuellt nätverk.

1. På sidan **Labbkonto** väljer du **Labs-inställningar** på den vänstra menyn.
2. För fältet **Adressintervall** anger du adressintervallet för virtuella datorer som ska skapas i labbet. Adressintervallet ska finnas i CIDR-notationen (Classless inter-domain routing) (exempel: 10.20.0.0/23). Virtuella datorer i labbet skapas i det här adressintervallet.
3. Välj **Spara** i verktygsfältet. 

    ![Konfigurera adressintervall](../media/how-to-manage-lab-accounts/labs-configuration-page-address-range.png)


## <a name="automatic-shutdown-of-vms-on-disconnect"></a>Automatisk avstängning av virtuella datorer vid frånkoppling
Du kan aktivera eller inaktivera automatisk avstängning av virtuella datorer i Windows lab (mall eller elev) när en fjärrskrivbordsanslutning har kopplats från. Du kan också ange hur länge de virtuella datorerna ska vänta på att användaren ska återansluta innan den stängs av automatiskt.

![Inställningen för automatisk avstängning på labbkonto](../media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)

Den här inställningen gäller för alla labb som har skapats i labbkontot. En labbägare kan åsidosätta den här inställningen på labbnivå. Ändringen av den här inställningen på labbkontot påverkar bara labb som skapas när ändringen har gjorts.

Mer information om hur en labbägare kan konfigurera den här inställningen på labbnivå finns i den [här artikeln](how-to-enable-shutdown-disconnect.md)

## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- [Tillåt labbets skapare att välja plats för labbet](allow-lab-creator-pick-lab-location.md)
- [Ansluta labbets nätverk med ett virtuellt peer-nätverk](how-to-connect-peer-virtual-network.md)
- [Bifoga ett delat bildgalleri i ett labb](how-to-attach-detach-shared-image-gallery.md)
- [Lägga till en användare som labbägare](how-to-add-user-lab-owner.md)
- [Visa brandväggsinställningar för ett labb](how-to-configure-firewall-settings.md)
