---
title: Konfigurera vRealize-åtgärder för Azure VMware-lösning
description: Lär dig hur du konfigurerar vRealize-åtgärder för ditt privata moln i Azure VMware-lösningen.
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 9e512d107ddc4d9bca28323658d09f4b4b378dc3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91580680"
---
# <a name="set-up-vrealize-operations-for-azure-vmware-solution"></a>Konfigurera vRealize-åtgärder för Azure VMware-lösning


vRealize Operations Manager är en plattform för drift hantering som gör att VMware Infrastructure-administratörer kan övervaka system resurser. De här system resurserna kan vara program nivå eller infrastruktur nivå (både fysiska och virtuella) objekt. De flesta VMware-administratörer har tidigare använt vRealize-åtgärder för att övervaka och hantera VMwares privata moln komponenter – vCenter, ESXi, NSX-T, virtuellt San och hybrid moln tillägg (HCX). Varje privat moln i Azure VMware-lösningen tillhandahålls med en dedikerad vCenter-, NSX-T-, virtuellt San-och HCX-distribution. 

Granska noga [innan du börjar](#before-you-begin) och [förutsättningarna](#prerequisites) först. Sedan vägleder vi dig genom de två typiska distributions topologierna för vRealize Operations Manager med Azure VMware-lösningen:

> [!div class="checklist"]
> * [Lokala vRealize-åtgärder hantering av Azure VMware Solution Deployment](#on-premises-vrealize-operations-managing-azure-vmware-solution-deployment)
> * [vRealize-åtgärder som körs på Azure VMware-distribution av VMware-lösningar](#vrealize-operations-running-on-azure-vmware-solution-deployment)

## <a name="before-you-begin"></a>Innan du börjar
* Läs [dokumentationen om vRealize Operations Manager Product](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) för att lära dig mer om att distribuera VRealize-åtgärder. 
* Läs igenom [självstudien](tutorial-network-checklist.md)om Azure VMware-lösning med programdefinierad Data Center (SDDC).
* Du kan också gå igenom produkt dokumentationen för [Fjärrkontrollanten för vRealize](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-263F9219-E801-4383-8A59-E84F3D01ED6B.html) för den lokala vRealize driften hanterings alternativ för Azure VMware-lösningen. 



## <a name="prerequisites"></a>Förutsättningar
* VPN-eller Azure-ExpressRoute ska konfigureras mellan lokala och Azure VMware-lösningar SDDC.
* Ett privat moln i Azure VMware-lösningen har distribuerats i Azure.



## <a name="on-premises-vrealize-operations-managing-azure-vmware-solution-deployment"></a>Lokala vRealize-åtgärder hantering av Azure VMware Solution Deployment
De flesta kunder har en befintlig lokal distribution av vRealize-åtgärder som används för att hantera en eller flera lokala vCenter-domäner. När kunderna tillhandahåller ett nytt privat moln i Azure VMware-lösningen i Azure, ansluter de vanligt vis sin lokala miljö med en Azure VMware-lösning med hjälp av en Azure-ExpressRoute eller med hjälp av en VPN-lösning på Layer 3 – som visas nedan.   

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-1.png" alt-text="Lokala vRealize-åtgärder hantering av Azure VMware Solution Deployment"  border="false":::

För att utöka vRealize-funktioner till Azure VMware-lösningen privat moln, skapar du en kort [instans för Azure VMware-lösningen privata moln resurser](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.config.doc/GUID-640AD750-301E-4D36-8293-1BFEB67E2600.html) – för att samla in data från Azure VMware-lösningen privat moln och ta den till lokala VRealize-åtgärder. Den lokala vRealize Operations Manager-instansen kan ansluta direkt till vCenter-och NSX-T-hanteraren på Azure VMware-lösningen eller så kan du distribuera en vRealize-fjärrcollector på Azure VMware-lösningen privat moln. En vRealize Operations-fjärrsamlare komprimerar och krypterar data som samlas in från Azure VMware-lösningens privata moln innan det skickas via ExpressRoute eller VPN-nätverket till vRealize Operations Manager som körs lokalt. 

> [!TIP]
> I VMware- [dokumentationen](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) hittar du steg-för-steg-instruktioner för att installera vRealize Operations Manager. 



## <a name="vrealize-operations-running-on-azure-vmware-solution-deployment"></a>vRealize-åtgärder som körs på Azure VMware-distribution av VMware-lösningar

Ett annat distributions alternativ är att distribuera en instans av vRealize Operations Manager på ett av vSphere-klustren i det privata molnet för Azure VMware-lösningar – som visas nedan. 

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-2.png" alt-text="Lokala vRealize-åtgärder hantering av Azure VMware Solution Deployment" border="false":::

När du har distribuerat Azure VMware Solution-instansen av vRealize-åtgärder kan du konfigurera vRealize-åtgärder för att samla in data från vCenter, ESXi, NSX-T, virtuellt San och HCX. 

> [!TIP]
> I VMware- [dokumentationen](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) hittar du steg-för-steg-instruktioner för att installera vRealize Operations Manager.


## <a name="known-limitations"></a>Kända begränsningar

- **cloudadmin@vsphere.local**Användaren i Azure VMware-lösningen har [begränsade privilegier](concepts-role-based-access-control.md). Minnes insamling i gästen med hjälp av VMware-verktyg stöds inte med virtuella datorer i Azure VMware-lösningen. Aktiva och förbrukade minnes användningar fortsätter att fungera i det här fallet.
- Arbets belastnings optimering för värdbaserade affärs syften fungerar inte eftersom Azure VMware-lösningar hanterar klusterkonfigurationer, inklusive DRS-inställningar.
- Arbets belastnings optimering för kors kluster placering i SDDC med det klusterbaserade affärs syftet stöds fullt ut med vRealize Operations Manager 8,0 och senare. Arbets belastnings optimeringen är dock inte medveten om resurspooler och placerar de virtuella datorerna på kluster nivå. En användare kan manuellt korrigera detta i Azure VMware-lösningen vCenter Server gränssnittet.
- Det går inte att logga in på vRealize Operations Manager att använda din Azure VMware-lösning vCenter Server autentiseringsuppgifter. 
- Azure VMware-lösningen stöder inte plugin-programmet vRealize Operations Manager.

När du ansluter Azure VMware-lösningen vCenter till vRealize Operations Manager med ett vCenter Server moln konto kommer du att stöta på följande varning:

:::image type="content" source="./media/vrealize-operations-manager/warning-adapter-instance-creation-succeeded.png" alt-text="Lokala vRealize-åtgärder hantering av Azure VMware Solution Deployment":::

Varningen beror på att **cloudadmin@vsphere.local** användaren i Azure VMware-lösningen inte har tillräcklig behörighet för att utföra alla vCenter Server åtgärder som krävs för registrering. Behörigheterna är dock tillräckliga för att kort instansen ska kunna utföra data insamling, som visas nedan:

:::image type="content" source="./media/vrealize-operations-manager/adapter-instance-to-perform-data-collection.png" alt-text="Lokala vRealize-åtgärder hantering av Azure VMware Solution Deployment":::

Mer information finns i [behörigheter som krävs för att konfigurera en instans av vCenter-kort](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.core.doc/GUID-3BFFC92A-9902-4CF2-945E-EA453733B426.html).

<!-- LINKS - external -->


<!-- LINKS - internal -->




