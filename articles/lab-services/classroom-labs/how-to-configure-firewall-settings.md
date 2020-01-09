---
title: Brand Väggs inställningar för Azure Lab Services
description: Lär dig hur du fastställer den offentliga IP-adressen och port nummer intervallet för virtuella datorer i ett labb så att information kan läggas till i brand Väggs regler.
author: emaher
ms.author: enewman
ms.date: 12/12/2019
ms.topic: article
ms.service: lab-services
ms.openlocfilehash: da1614e4a3e02ed91ef2d3c59ac4eb3eac0dcc7c
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/07/2020
ms.locfileid: "75692776"
---
# <a name="firewall-settings-for-azure-lab-services"></a>Brand Väggs inställningar för Azure Lab Services

Varje organisation eller skola kommer att konfigurera sina egna nätverk på ett sätt som bäst passar deras behov.  Ibland ingår det att ställa in brand Väggs regler som blockerar Remote Desktop Protocol (RDP) eller SSH-anslutningar (Secure Shell) till datorer utanför deras egna nätverk.  Eftersom Azure Lab Services körs i det offentliga molnet, kan en del extra konfiguration behöva göra det möjligt för studenter att komma åt sin virtuella dator vid anslutning från campus-nätverket.

Varje labb använder en enskild offentlig IP-adress och flera portar.  Alla virtuella datorer, både den virtuella datorn och elevens virtuella datorer, kommer att använda den här offentliga IP-adressen.  Den offentliga IP-adressen ändras inte under Labbets livs längd.  Varje virtuell dator kommer dock att ha ett annat port nummer.  Port numren sträcker sig från 49152 till 65535.  Kombinationen av den offentliga IP-adressen och port numret används för att ansluta lärare och studenter till rätt virtuell dator.  Den här artikeln beskriver hur du hittar den enskilda offentliga IP-adressen som används av ett labb.  Den informationen kan användas för att uppdatera inkommande och utgående brand Väggs regler så att eleverna kan komma åt sina virtuella datorer.

>[!IMPORTANT]
>Varje labb kommer att ha en annan offentlig IP-adress.

## <a name="find-public-ip-for-a-lab"></a>Hitta en offentlig IP-adress för ett labb

De offentliga IP-adresserna för varje labb visas på bladet **alla** labb i labb tjänster Lab-kontot.  Anvisningar om hur du hittar bladet **alla labb** finns i [Hantera labb i ett labb konto](how-to-manage-lab-accounts.md#view-and-manage-labs-in-the-lab-account).  

> [!div class="mx-imgBorder"]
> ![alla labb blad](../media/how-to-configure-firewall-settings/all-labs-properties.png)

>[!NOTE]
>Du ser inte den offentliga IP-adressen om mallens dator för ditt labb inte har publicerats ännu.

## <a name="conclusion"></a>Slutsats

Nu vet vi den offentliga IP-adressen för labbet.  Regler för inkommande och utgående trafik kan skapas för organisationens brand vägg för den offentliga IP-adressen och port intervallet 49152-65535.  När reglerna har uppdaterats kan studenter komma åt sina virtuella datorer utan att nätverks brand väggen blockerar åtkomsten.
