---
title: Konfigurera vRealize-åtgärder för Azure VMware-lösning (AVS)
description: ''
ms.topic: how-to
ms.date: 08/06/2020
ms.openlocfilehash: 57b513b681c4d2522b1c92946aab80fe8ba95746
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2020
ms.locfileid: "87476198"
---
# <a name="set-up-vrealize-operations-for-azure-vmware-solution-avs"></a>Konfigurera vRealize-åtgärder för Azure VMware-lösning (AVS)


vRealize Operations Manager är en plattform för drift hantering som gör att VMware Infrastructure-administratörer kan övervaka system resurser. De här system resurserna kan vara program nivå eller infrastruktur nivå (både fysiska och virtuella) objekt. De flesta VMware-administratörer har tidigare använt vRealize-åtgärder för att övervaka och hantera VMwares privata moln komponenter – vCenter, ESXi, NSX-T, virtuellt San och hybrid moln tillägg (HCX). Varje AVS-privat moln tillhandahålls med en dedikerad vCenter-, NSX-T-, virtuellt San-och HCX-distribution. 

Granska noga [innan du börjar](#before-you-begin) och [förutsättningarna](#prerequisites) först. Sedan vägleder vi dig genom de två typiska distributions topologierna för vRealize Operations Manager med AVS:

> [!div class="checklist"]
> * [Lokala vRealize-åtgärder hantera AVS-distribution](#on-premises-vrealize-operations-managing-avs-deployment)
> * [vRealize-åtgärder som körs på AVS-distribution](#vrealize-operations-running-on-avs-deployment)

## <a name="before-you-begin"></a>Innan du börjar
* Läs [dokumentationen om vRealize Operations Manager Product](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) för att lära dig mer om att distribuera VRealize-åtgärder. 
* Gå igenom [självstudien](tutorial-network-checklist.md)för den grundläggande SDDC-serien (definitions paket).
* Du kan också gå igenom produkt dokumentationen för [Fjärrkontrollanten för vRealize](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-263F9219-E801-4383-8A59-E84F3D01ED6B.html) för den lokala vRealize driften hanterings alternativ. 



## <a name="prerequisites"></a>Krav
* VPN-eller Azure-ExpressRoute ska konfigureras mellan lokala och AVS-SDDC.
* Ett privat AVS-moln har distribuerats i Azure.



## <a name="on-premises-vrealize-operations-managing-avs-deployment"></a>Lokala vRealize-åtgärder hantera AVS-distribution
De flesta kunder har en befintlig lokal distribution av vRealize-åtgärder som används för att hantera en eller flera lokala vCenter-domäner. När kunderna etablerar ett nytt moln privat moln i Azure, ansluter de vanligt vis sin lokala miljö med AVS med antingen en Azure-ExpressRoute eller med hjälp av en VPN-lösning på Layer 3 – som visas nedan.   

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-1.png" alt-text="Lokala vRealize-åtgärder hantera AVS-distribution"  border="false":::

Om du vill utöka vRealize-funktionerna till det privata AVS-molnet skapar du en kort[ instans för molnets privata moln resurser](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.config.doc/GUID-640AD750-301E-4D36-8293-1BFEB67E2600.html) – för att samla in data från det privata moln molnet och ta den till lokala VRealize-åtgärder. Den lokala vRealize Operations Manager-instansen kan ansluta direkt till vCenter-och NSX-T-hanteraren i AVS eller så kan du också distribuera en vRealize Operations-fjärrsamlare på det privata molnet i AVS-molnet. En vRealize Operations-fjärrsamlare komprimerar och krypterar data som samlas in från det molnbaserade privata molnet innan det skickas över ExpressRoute eller VPN-nätverket till vRealize Operations Manager som körs lokalt. 

> [!TIP]
> I VMware- [dokumentationen](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) hittar du steg-för-steg-instruktioner för att installera vRealize Operations Manager. 



## <a name="vrealize-operations-running-on-avs-deployment"></a>vRealize-åtgärder som körs på AVS-distribution

Ett annat distributions alternativ är att distribuera en instans av vRealize Operations Manager på ett av vSphere-klustren i molnets privata moln – som visas nedan. 

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-2.png" alt-text="vRealize-åtgärder som körs på AVS" border="false":::

När du har distribuerat AVS-instansen av vRealize-åtgärder kan du konfigurera vRealize-åtgärder för att samla in data från vCenter, ESXi, NSX-T, virtuellt San och HCX. 

> [!TIP]
> I VMware- [dokumentationen](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) hittar du steg-för-steg-instruktioner för att installera vRealize Operations Manager.




<!-- LINKS - external -->


<!-- LINKS - internal -->




