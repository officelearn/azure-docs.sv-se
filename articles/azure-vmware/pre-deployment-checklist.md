---
title: Check lista för Azure VMware-lösning före distribution
description: Använd den här check listan som en del av planerings processen för för distribution.
ms.topic: how-to
ms.date: 10/02/2020
ms.openlocfilehash: 51480f72d6de6b1f49aa753e006c4683bbd48cc7
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/30/2020
ms.locfileid: "91580533"
---
# <a name="azure-vmware-solution-pre-deployment-checklist"></a>Check lista för Azure VMware-lösning före distribution
Du använder den här check listan för för distribution under [planerings fasen](production-ready-deployment-steps.md).

## <a name="success-criteria"></a>Lyckade kriterier
Definiera vilka tester du ska köra och det förväntade resultatet.

| Grundläggande information behövs | Ditt svar |
| ----------- | ------------- |
| Distribuera SDDC | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Skapa det virtuella nätverket | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Skapa hopp ruta | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Skapa virtuell nätverksgateway | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Anslut ExpressRoute Global Reach | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 
| Logga in i NSX Manager och vCenter | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |



| Primär information krävs | Ditt svar |
| --------| --------------|
| Skapa DHCP-server | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Skapa nätverks segment | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Skala (Lägg till eller ta bort noder) | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Distribuera VMware-HCX | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Skapa virtuella datorer (VM) | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
| Aktivera Internet | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| VM-migrering från lokalt till privat moln | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |



| Ytterligare information behövs | Ditt svar |
| --------| --------------|
| Åtgärder för VM-ögonblicksbild | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 
| Distribuera NSX-T Load Balancer | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Azure-integrering<br><ul><li>Delat innehålls bibliotek</li><li>Säkerhets integrering</li><li>Ladda upp ISO</li><li>Bygg från ISO</li><li>Azure Backup</li></ul> | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Micro-segmentering | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Routning | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |




## <a name="azure-vmware-solution-information"></a>Information om Azure VMware-lösning

#### <a name="azure-subscription"></a>Azure-prenumeration
Ange prenumerations namn och prenumerations-ID för Azure VMware-lösningen. Prenumerationen kan vara ny eller en befintlig prenumeration. Använd inte en dev/test-prenumeration.

| Information som behövs  | Ditt svar |
| ------------| ------------- |
| Prenumeration och ID | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Prenumeration med EA | ☐ Ja &nbsp; &nbsp; ☐ Nej  |
| Resurs grupps namn | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Plats | ☒ USA, östra |
| Azure-administratör<br><br>Ange namn och kontakt för administratören<br>tilldelad för att aktivera tjänsten från Marketplace.<br>Contributor är den lägsta rollen som krävs för att <br>[Registrera resurs leverantören för Azure VMware-lösningen](tutorial-create-private-cloud.md#register-the-resource-provider). | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |



## <a name="on-premises-vmware-information"></a>Lokal VMware-information

| Information som behövs  | Ditt svar |
| ------------| --------------|
| vSphere-version | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| vCenter-version | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 
| vCenter-administratör | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| L2-tillägg<br><br>Om du utökar L2-nätverk med VMware HCX<br>Ange det lokala nätverk som du kommer att utöka. | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 
| Växel<br><br>Ange vilken typ av vSwitch som används i miljön. | ☐ Standard &nbsp; &nbsp; ☐ distribuerad<br><br>Om du använder standard är VMware HCX inte tillgänglig. |
| DNS och DHCP<br><br>En korrekt DNS-och DHCP-infrastruktur krävs. <br>Vi rekommenderar att du använder den DHCP-tjänst som är inbyggd i <br>NSX eller Använd en lokal DHCP-server i det privata molnet <br>i stället för att dirigera broadcast-DHCP-trafik över <br>WAN tillbaka till lokalt. Om du vill ha mer information läser du <br>Se [hur du skapar och hanterar DHCP i Azure VMware-lösningen](manage-dhcp.md). | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |


 

## <a name="networking---azure-vmware-solution-infrastructure"></a>Nätverk – Azure VMware lösnings infrastruktur 
De data som behövs är för att hjälpa dig att möta nätverks behoven i Azure VMware-lösningen för uppstarts-och första nätverks testning. 

| Information som behövs | Ditt svar |
| ----------- | ------------- |
|Azure VMware-lösning CIDR<br><br>Krävs för vSphere-värdar, virtuellt San och-hantering <br>nätverk i Azure VMware-lösning. För mer <br>information finns i [Routning och undernätets överväganden](tutorial-network-checklist.md#routing-and-subnet-considerations). | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Azure VMware Solution-arbets belastning CIDR (valfritt)<br><br>Tilldela ett nätverk som ska användas i Azure VMware<br>Lösning för inledande testning. Virtuella datorer<br>kommer att distribueras för att verifiera nätverks anslutningen <br>från/till Azure VMware-lösning. | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |



## <a name="networking---connect-azure-vmware-solution-to-azure-virtual-network"></a>Nätverk – ansluta Azure VMware-lösning till Azure Virtual Network
De data som krävs för Azure VMware-Stood kan anslutas till Azure via en ExpressRoute-krets, som är en del av Azure VMware Solution service.

| Information som behövs | Ditt svar |
| ------------------ | ------------- |
| Hopp ruta – Windows Server | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Skapa det virtuella nätverket | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Skapa Gateway-undernät | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Skapa virtuell nätverksgateway<br><br>Mer information finns i [skapa den virtuella Nätverksgatewayen](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-virtual-network-gateway). | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |


 

## <a name="networking---connect-azure-vmware-solution-to-an-on-premises-datacenter"></a>Nätverk – ansluta Azure VMware-lösning till ett lokalt Data Center

| Information som behövs | Ditt svar |
| ------------------ | ------------- |
| ExpressRoute peering CIDR<br><br>`/29`CIDR-Adressblock. | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| ExpressRoute-auktoriseringskod och resurs-ID<br><br>Ange en auktoriseringskod och resurs-ID, <br>som genereras från den aktuella ExpressRoute <br>krets som ansluter till det lokala data centret.  | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Riktning för standard väg<br><br>Om de virtuella datorerna i Azure VMware-lösningen <br>få åtkomst till Internet via den Azure VMware-lösning som tillhandahålls <br>Internet eller kom tillbaka över ExpressRoute-kretsen till <br>lokalt för standard vägen? | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Nätverks portar som krävs för att kommunicera med tjänsten<br><br>Mer information finns i de [nätverks portar som krävs](tutorial-network-checklist.md#required-network-ports). | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |



## <a name="networking---vmware-hcx"></a>Nätverk – VMware HCX

| Information som behövs | Ditt svar |
| ------------------ | ------------- |
| Nätverksportar<br><br>Om det finns en brand vägg kontrollerar du att de nödvändiga nätverks portarna <br>öppnas mellan lokala och Azure VMware-lösningar. | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| DNS<br><br>Information om hur du konfigurerar DNS, <br>Se [nätverk – Azure VMware lösnings infrastruktur](#networking---azure-vmware-solution-infrastructure). | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| VMware HCX CIDR<br><br>Du behöver två `/29` CIDR-block som används för <br>VMware HCX-infrastruktur komponenter lokalt.  | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |

