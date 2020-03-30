---
title: Brandväggsinställningarna för Azure Lab Services
description: Lär dig hur du tar reda på den offentliga IP-adressen och portnumret för virtuella datorer i ett labb så att information kan läggas till brandväggsregler.
author: emaher
ms.author: enewman
ms.date: 02/14/2020
ms.topic: article
ms.service: lab-services
ms.openlocfilehash: fbd45af0c9b94f04fdaad9d9b5c8214a91a8db91
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77443471"
---
# <a name="firewall-settings-for-azure-lab-services"></a>Brandväggsinställningarna för Azure Lab Services

Varje organisation eller skola kommer att skapa sitt eget nätverk på ett sätt som bäst passar deras behov.  Ibland ingår att ange brandväggsregler som blockerar anslutningar för Fjärrskrivbordsprotokoll (rdp) eller Secure Shell (ssh) till datorer utanför sitt eget nätverk.  Eftersom Azure Lab Services körs i det offentliga molnet kan det finnas en extra konfiguration för att tillåta deltagare att komma åt sin virtuella dator när de ansluter från campusnätverket.

Varje labb använder en offentlig IP-adress och flera portar.  Alla virtuella datorer, både mallen VM och deltagare virtuella datorer, kommer att använda denna offentliga IP-adress.  Den offentliga IP-adressen ändras inte under labbets livstid.  Varje virtuell dator har dock ett annat portnummer.  Portnumren varierar från 49152 till 65535.  Kombinationen av offentlig IP-adress och portnummer används för att ansluta lärare och deltagare till rätt virtuell dator.  Den här artikeln beskriver hur du hittar den specifika offentliga IP-adress som används av ett labb.  Den informationen kan användas för att uppdatera regler för inkommande och utgående brandvägg så att deltagarna kan komma åt sina virtuella datorer.

>[!IMPORTANT]
>Varje labb kommer att ha en annan offentlig IP-adress.

## <a name="find-public-ip-for-a-lab"></a>Hitta offentlig IP för ett labb

De offentliga IP-adresserna för varje labb visas på sidan **Alla labb** i Lab Services lab-konto.  Mer information om hur du hittar sidan **Alla labb** finns i hur du hanterar labb i [ett labbkonto](how-to-manage-lab-accounts.md#view-and-manage-labs-in-the-lab-account).  

> [!div class="mx-imgBorder"]
> ![Sidan Alla labb](../media/how-to-configure-firewall-settings/all-labs-properties.png)

>[!NOTE]
>Du ser inte den offentliga IP-adressen om mallmaskinen för ditt labb inte har publicerats ännu.

## <a name="conclusion"></a>Slutsats

Nu känner vi till den offentliga IP-adressen för labbet.  Inkommande och utgående regler kan skapas för organisationens brandvägg för den offentliga IP-adressen och portintervallet 49152-65535.  När reglerna har uppdaterats kan deltagarna komma åt sina virtuella datorer utan att nätverksbrandväggen blockerar åtkomsten.

## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- [Tillåt labbets skapare att välja plats för labbet](allow-lab-creator-pick-lab-location.md)
- [Ansluta labbets nätverk med ett virtuellt peer-nätverk](how-to-connect-peer-virtual-network.md)
- [Bifoga ett delat bildgalleri i ett labb](how-to-attach-detach-shared-image-gallery.md)
- [Lägga till en användare som labbägare](how-to-add-user-lab-owner.md)
- [Visa brandväggsinställningar för ett labb](how-to-configure-firewall-settings.md)
- [Konfigurera andra inställningar för ett labb](how-to-configure-lab-accounts.md)
