---
title: Ansluta till ett peer-nätverk i Azure Lab Services | Microsoft Docs
description: Lär dig hur du ansluter ditt labbnätverk med ett annat nätverk som peer. Till exempel ansluta ditt lokala skola/universitet nätverk med övningen virtuellt nätverk i Azure.
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
ms.date: 05/07/2019
ms.author: spelluru
ms.openlocfilehash: c9b305beae1b385d4714e3a80e6843c7e76a4f60
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65411005"
---
# <a name="connect-your-labs-network-with-a-peer-virtual-network-in-azure-lab-services"></a>Ansluta ditt labb nätverk med en peer virtuellt nätverk i Azure Lab Services 
Den här artikeln innehåller information om peering labs nätverket med ett annat nätverk. 

## <a name="overview"></a>Översikt
Virtuell nätverkspeering kan du smidigt ansluta virtuella Azure-nätverk. När de virtuella nätverken har peer-kopplats visas de som ett nätverk för anslutningsändamål. Trafiken mellan virtuella datorer i peerkopplade virtuella nätverk dirigeras via Microsoft-stamnätsinfrastrukturen, nästan samma sätt som trafik dirigeras mellan virtuella datorer i samma virtuella nätverk genom endast privata IP-adresser. Mer information finns i [peerkoppling av virtuella nätverk](../../virtual-network/virtual-network-peering-overview.md).

Du kan behöva ansluta ditt labb nätverk med ett peer virtuellt nätverk i vissa scenarier, inklusive följande:

- De virtuella datorerna i laboratoriet har program som ansluter till lokala servrar för fjärrskrivbordslicenser att förvärva licens
- De virtuella datorerna i labbet behöver åtkomst till datauppsättningar (eller andra filer) på university's nätverksresurser. 

Vissa lokala nätverk är anslutna till Azure Virtual Network antingen via [ExpressRoute](../../expressroute/expressroute-introduction.md) eller [virtuell nätverksgateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md). De här tjänsterna måste ställas in utanför Azure Lab Services. Mer information om hur du ansluter ett lokalt nätverk till Azure med ExpressRoute, finns i [översikt över ExpressRoute]) (.. /expressroute/expressroute-Introduction.MD). För lokal anslutning med hjälp av en virtuell nätverksgateway, gateway, angivna virtuella nätverket och labb-kontot måste vara i samma region.

## <a name="configure-at-the-time-of-lab-account-creation"></a>Konfigurera vid tidpunkten för labb har skapats
När nya lab kontot skapas kan du välja ett befintligt virtuellt nätverk som visas i den **Peerkopplade virtuella nätverket** listrutan. Det valda virtuella nätverket är connected(peered) till övningar som skapats under kontot labb. Alla virtuella datorer i labb som skapas efter att den här ändringen ha åtkomst till resurser i det peer-kopplade virtuella nätverket. 

![Välj virtuellt nätverk som peer-koppla](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer.png)

> [!NOTE]
> Detaljerade stegvisa instruktioner för att skapa ett labbkonto finns i [konfigurera ett labbkonto](tutorial-setup-lab-account.md)


## <a name="configure-after-the-lab-is-created"></a>Konfigurera när labbet har skapats
Samma egenskap kan aktiveras från den **Labs configuration** fliken den **Labbkonto** sidan om du inte har ställt in en peer-nätverket vid tidpunkten för labb har skapats. Ändringar som gjorts i den här inställningen gäller enbart för labb som skapas efter ändringen. Som du ser i bilden, du kan aktivera eller inaktivera **Peerkopplade virtuella nätverket** för labb i labbkonto. 

![Aktivera eller inaktivera VNet-peering när labbet har skapats](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer-existing-lab.png) 

När du väljer ett virtuellt nätverk för den **Peerkopplade virtuella nätverket** fält, de **Tillåt labbskaparen att välja lab platsen** alternativet är inaktiverat. Det beror på att labs i labb-kontot måste finnas i samma region som labbkonto att ansluta till resurser i det peerkopplade virtuella nätverket. 

> [!IMPORTANT]
> Den här ändringen gäller enbart för labb som skapas efter att ändringen görs inte till de befintliga övningarna. 


## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- [Skapa och hantera labbkonton som administratör](how-to-manage-lab-accounts.md)
- [Skapa och hantera labb som labbägare](how-to-manage-classroom-labs.md)
- [Konfigurera och publicera mallar som labbägare](how-to-create-manage-template.md)
- [Som en lab-användare åtkomst till labb för klassrum](how-to-use-classroom-lab.md)

