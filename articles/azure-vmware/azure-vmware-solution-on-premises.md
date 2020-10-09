---
title: Anslut Azure VMware-lösningen till din lokala miljö
description: Lär dig hur du ansluter Azure VMware-lösningen till din lokala miljö.
ms.topic: tutorial
ms.date: 10/02/2020
ms.openlocfilehash: 2a0cb641df00f3e580e87e38aff382d8e8101fc7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91583423"
---
# <a name="connect-azure-vmware-solution-to-your-on-premises-environment"></a>Anslut Azure VMware-lösningen till din lokala miljö

I den här artikeln fortsätter du med den [information som samlats in under planeringen](production-ready-deployment-steps.md) för att ansluta Azure VMware-lösningen till din lokala miljö.

Innan du börjar måste du ha två krav för att ansluta Azure VMware-lösningen till din lokala miljö:

- En ExpressRoute-krets från din lokala miljö till Azure.
- Ett/29 icke-överlappande nätverks adress block för ExpressRoute Global Reach-peering, som du definierade som en del av [planerings fasen](production-ready-deployment-steps.md).

>[!NOTE]
> Du kan ansluta via VPN, men det är utanför omfånget för det här snabb starts dokumentet.

## <a name="establish-an-expressroute-global-reach-connection"></a>Upprätta en ExpressRoute Global Reach anslutning

Om du vill upprätta lokal anslutning till ditt privata moln i Azure VMware-lösningen med hjälp av ExpressRoute Global Reach, följer du själv studie kursen [om peer-datorer i ett privat moln](tutorial-expressroute-global-reach-private-cloud.md) .



## <a name="verify-on-premises-network-connectivity"></a>Verifiera lokal nätverks anslutning

Du bör nu se i din **lokala Edge-router** där EXPRESSROUTE ansluter NSX-T-datasegmenten och Azure VMware lösnings hanterings segmenten.

>[!NOTE]
>Alla har en annan miljö och vissa måste tillåta att dessa vägar sprids tillbaka till det lokala nätverket.  

Vissa miljöer kommer att ha brand väggar som skyddar ExpressRoute-kretsen.  Om inga brand väggar och ingen väg rensning inträffar kan du pinga din Azure VMware-lösning vCenter-Server eller en [virtuell dator](deploy-azure-vmware-solution.md#add-a-vm-on-the-nsx-t-network-segment) på NSX-T-segmentet från din lokala miljö.

Från den virtuella datorn i NSX-T-segmentet kan du dessutom komma åt resurser i din lokala miljö.

## <a name="next-steps"></a>Nästa steg

Fortsätt till nästa avsnitt för att distribuera och konfigurera VMware-HCX

> [!div class="nextstepaction"]
> [Distribuera och konfigurera VMware HCX](tutorial-deploy-vmware-hcx.md)