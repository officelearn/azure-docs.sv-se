---
title: Ansluta till ett peer-nätverk i Azure Lab Services | Microsoft Docs
description: Lär dig hur du ansluter ditt labb nätverk till ett annat nätverk som en peer. Du kan till exempel ansluta ditt lokala skol-/University-nätverk med Labbets virtuella nätverk i Azure.
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
ms.openlocfilehash: d3f6acef7491a07f94eec0b2c3b2f3bcd9c01a33
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74701672"
---
# <a name="connect-your-labs-network-with-a-peer-virtual-network-in-azure-lab-services"></a>Anslut ditt labb nätverk till ett peer-virtuellt nätverk i Azure Lab Services 
Den här artikeln innehåller information om peering The Labs Network med ett annat nätverk. 

## <a name="overview"></a>Översikt
Med peering för virtuella nätverk kan du sömlöst ansluta virtuella Azure-nätverk. När de virtuella nätverken har peer-kopplats visas de som ett nätverk för anslutningsändamål. Trafiken mellan virtuella datorer i peer-kopplat virtuella nätverk dirigeras via Microsoft stamnät-infrastrukturen, ungefär som trafik dirigeras mellan virtuella datorer i samma virtuella nätverk, via privata IP-adresser. Mer information finns i [peering för virtuella nätverk](../../virtual-network/virtual-network-peering-overview.md).

Du kan behöva ansluta ditt labbs nätverk till ett peer-virtuellt nätverk i vissa scenarier, inklusive följande:

- De virtuella datorerna i labbet har program vara som ansluter till lokala licens servrar för att erhålla licens
- De virtuella datorerna i labbet behöver åtkomst till data uppsättningar (eller andra filer) på Universitys nätverks resurser. 

Vissa lokala nätverk är anslutna till Azure Virtual Network antingen via [ExpressRoute](../../expressroute/expressroute-introduction.md) eller [Virtual Network Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md). Dessa tjänster måste konfigureras utanför Azure Lab Services. Mer information om hur du ansluter ett lokalt nätverk till Azure med hjälp av ExpressRoute finns i [Översikt över ExpressRoute](../../expressroute/expressroute-introduction.md). För lokal anslutning med en Virtual Network Gateway måste gatewayen, det angivna virtuella nätverket och labb kontot vara i samma region.

## <a name="configure-at-the-time-of-lab-account-creation"></a>Konfigurera när labb kontot skapas
När du skapar ett nytt labb konto kan du välja ett befintligt virtuellt nätverk som visas i list rutan **peer Virtual Network** . Det valda virtuella nätverket är anslutet (peer-kopplat) till labb som skapats under labb kontot. Alla virtuella datorer i labb som skapas när den här ändringen har gjorts har till gång till resurserna i det peer-virtuella nätverket. 

![Välj VNet till peer](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer.png)

> [!NOTE]
> Detaljerade steg-för-steg-instruktioner för att skapa ett labb konto finns i [Konfigurera ett labb konto](tutorial-setup-lab-account.md)


## <a name="configure-after-the-lab-is-created"></a>Konfigurera när labbet har skapats
Du kan aktivera samma egenskap på fliken labb **konfiguration** på sidan **labb konto** om du inte har konfigurerat ett peer-nätverk när du skapade labb kontot. Ändringar som görs i den här inställningen gäller bara för de labb som skapas efter ändringen. Som du kan se i avbildningen kan du aktivera eller inaktivera ett **virtuellt dator nätverk** för labb i labb kontot. 

![Aktivera eller inaktivera VNet-peering när labbet har skapats](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer-existing-lab.png) 

När du väljer ett virtuellt nätverk för det **virtuella peer-nätverket** är alternativet **Tillåt att labb skaparen att välja labb plats** inaktiverat. Det beror på att labb i labb kontot måste finnas i samma region som labb kontot för att de ska kunna ansluta till resurser i det virtuella peer-nätverket. 

> [!IMPORTANT]
> Den här inställningen gäller bara för labb som skapas när ändringen har gjorts, inte i de befintliga labben. 


## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- [Skapa och hantera labbkonton som administratör](how-to-manage-lab-accounts.md)
- [Skapa och hantera labb som labbägare](how-to-manage-classroom-labs.md)
- [Konfigurera och publicera mallar som labbägare](how-to-create-manage-template.md)
- [Som labb användare, åtkomst till klass rum labb](how-to-use-classroom-lab.md)

