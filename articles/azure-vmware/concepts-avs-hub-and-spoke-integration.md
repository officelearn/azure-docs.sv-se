---
title: Koncept – integrera en Azure VMware-lösning distribution i en hubb och eker-arkitektur
description: Lär dig mer om rekommendationer för att integrera en Azure VMware-lösnings distribution i en befintlig eller ny hubb och eker-arkitektur på Azure.
ms.topic: conceptual
ms.date: 08/20/2020
ms.openlocfilehash: deb2756f7e83250ff58836098dc4954ec482fbda
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684525"
---
# <a name="integrate-azure-vmware-solution-in-a-hub-and-spoke-architecture"></a>Integrera Azure VMware-lösningen i en hubb och eker-arkitektur

I den här artikeln ger vi rekommendationer för att integrera en Azure VMware-lösnings distribution i en befintlig eller ny [hubb och eker-arkitektur](/azure/architecture/reference-architectures/hybrid-networking/shared-services) på Azure. 

NAV-och eker-scenariot förutsätter en hybrid moln miljö med arbets belastningar på:

* Inbyggd Azure med IaaS-eller PaaS-tjänster
* Azure VMware Solution 
* vSphere lokalt

## <a name="architecture"></a>Arkitektur

*Hubben* är en Azure-Virtual Network som fungerar som en central punkt för anslutningen till ditt lokala och Azure VMware-lösningens privata moln. *Ekrarna* är virtuella nätverk som peer-kopplas med hubben för att aktivera kommunikation mellan virtuella nätverk.

Trafik mellan det lokala data centret, Azure VMware-lösningens privata moln och navet går genom ExpressRoute-anslutningar. Eker-virtuella nätverk innehåller vanligt vis IaaS-baserade arbets belastningar, men kan ha PaaS-tjänster som [App Service-miljön](../app-service/environment/intro.md), som har direkt integrering med Virtual Network eller andra PaaS-tjänster med [Azure Private Link](../private-link/index.yml) aktive rad. 

Diagrammet visar ett exempel på en distribution av hubb och ekrar i Azure som är ansluten till lokal och Azure VMware-lösning via ExpressRoute.

:::image type="content" source="./media/hub-spoke/avs-hub-and-spoke-deployment.png" alt-text="Azure VMware-lösning hubb och eker-integrering" border="false":::

Arkitekturen har följande huvud komponenter:

-   **Lokal plats:** Kundens lokala data Center (er) anslutna till Azure via en ExpressRoute-anslutning.

-   **Privat moln i Azure VMware-lösning:** Azure VMware-lösningen SDDC utformad av ett eller flera vSphere-kluster, var och en med högst 16 noder.

-   **ExpressRoute-Gateway:** Möjliggör kommunikationen mellan Azure VMware-lösningen privat moln, lokalt nätverk, delade tjänster på hubb virtuellt nätverk och arbets belastningar som körs på ekrar virtuella nätverk.

    > [!NOTE]
    > **Överväganden för S2S VPN:** För produktions distributioner av Azure VMware-lösningar stöds inte Azure S2S på grund av nätverks krav för HCX. För en PoC-eller icke-produktions distribution som inte kräver HCX kan du dock använda den.

-   **Hubb virtuellt nätverk:** Fungerar som central punkt för anslutningen till ditt lokala nätverk och Azure VMware-lösningens privata moln.

-   **Virtuellt nätverk i eker**

    -   **IaaS eker:** En IaaS eker är värd för Azure IaaS-baserade arbets belastningar, inklusive tillgänglighets uppsättningar för virtuella datorer och skalnings uppsättningar för virtuella datorer och motsvarande nätverks komponenter.

    -   **PaaS eker:** En PaaS eker är värd för Azure PaaS-tjänster med hjälp av privat adressering tack vare [privat slut punkt](../private-link/private-endpoint-overview.md) och [privat länk](../private-link/private-link-overview.md).

-   **Azure-brand vägg:** Fungerar som en central del för att segmentera trafik mellan ekrar, lokala och Azure VMware-lösningar.

-   **Application Gateway:** Exponerar och skyddar webbappar som körs antingen på Azure IaaS/PaaS eller virtuella datorer i Azure VMware-lösningen. Den integreras med andra tjänster som API Management.

## <a name="network-and-security-considerations"></a>Nätverks-och säkerhets aspekter

ExpressRoute-anslutningar möjliggör trafik som flödar mellan lokala Azure VMware-lösningar och Azures nätverks infrastruktur resurs. Azure VMware-lösningen använder [ExpressRoute Global Reach](../expressroute/expressroute-global-reach.md) för att implementera den här anslutningen.

Lokal anslutning kan använda ExpressRoute Global Reach även, men det är inte obligatoriskt.

* **Trafik flöde för lokala Azure VMware-lösningar**

  :::image type="content" source="media/hub-spoke/on-prem-to-avs-traffic-flow.png" alt-text="Trafik flöde för lokala Azure VMware-lösningar" border="false":::


* **Azure VMware-lösning till hubben för VNET-trafikflöde**

  :::image type="content" source="media/hub-spoke/avs-to-hub-vnet-traffic-flow.png" alt-text="Azure VMware-lösning för hubb av virtuellt nätverks trafikflöde" border="false":::


Du hittar mer information om Azure VMware-lösningen nätverk och interanslutnings koncept i [produkt dokumentationen för Azure VMware-lösningen](./concepts-networking.md).

### <a name="traffic-segmentation"></a>Trafik segmentering

[Azure Firewall](../firewall/index.yml) är den centrala delen av topologin hubb och eker, distribuerad på det virtuella hubb nätverket. Använd Azure-brandväggen eller någon annan Azure-baserad virtuell nätverks installation för att upprätta trafik regler och segmentera kommunikationen mellan olika ekrar, lokala och Azure VMware-lösningar arbets belastningar.

Skapa routningstabeller för att dirigera trafiken till Azure-brandväggen.  För de eker-virtuella nätverken skapar du en väg som anger standard vägen till det interna gränssnittet i Azure Firewall, på det här sättet när en arbets belastning i Virtual Network måste komma åt Azure VMware-lösningens adress utrymme. brand väggen kan utvärdera den och tillämpa motsvarande trafik regel för att antingen tillåta eller neka den.  

:::image type="content" source="media/hub-spoke/create-route-table-to-direct-traffic.png" alt-text="Skapa routningstabeller för att dirigera trafik till Azure-brandväggen":::


> [!IMPORTANT]
> En väg med adressprefixet 0.0.0.0/0 i **GatewaySubnet** -inställningen stöds inte.

Ange vägar för vissa nätverk i motsvarande routningstabell. Till exempel kan vägar för att uppnå Azure VMware lösnings hantering och arbets belastningar IP-prefix från lokala platser och vice versa, routning all trafik från lokala nätverk till Azure VMware-lösningen privat moln via Azure-brandväggen.

:::image type="content" source="media/hub-spoke/specify-gateway-subnet-for-route-table.png" alt-text="Ange vägar för vissa nätverk i motsvarande routningstabell":::

En andra nivå av trafik segmentering som använder nätverks säkerhets grupper i ekrar och hubben för att skapa en mer detaljerad trafik princip. 


### <a name="application-gateway"></a>Application Gateway

Azure Application Gateway v1 och v2 har testats med webbappar som körs på virtuella datorer i Azure VMware-lösningen som en backend-pool. Application Gateway är för närvarande den enda metoden som stöds för att exponera webbappar som körs på virtuella datorer i Azure VMware-lösningen till Internet. Det kan också exponera appar till interna användare på ett säkert sätt.

:::image type="content" source="media/hub-spoke/avs-second-level-traffic-segmentation.png" alt-text="Sekundär nivå för trafik segmentering med nätverks säkerhets grupper" border="false":::


### <a name="jumpbox-and-azure-bastion"></a>Hopp och Azure-skydds

Få åtkomst till Azure VMwares lösnings miljö med hopp, som är en virtuell Windows 10-eller Windows Server-dator som har distribuerats i det delade tjänst under nätet i det virtuella hubb nätverket.

Av säkerhets skäl bör du distribuera [Microsoft Azure skydds](../bastion/index.yml) -tjänsten i det virtuella hubb nätverket. Azure skydds ger sömlös RDP-och SSH-åtkomst till virtuella datorer som distribueras i Azure utan att behöva etablera offentliga IP-adresser till dessa resurser. När du har etablerat Azure skydds-tjänsten kan du komma åt den valda virtuella datorn från Azure Portal. När du har upprättat anslutningen öppnas en ny flik med hjälp av Skriv bordet och från det Skriv bordet kan du komma åt Azure VMware-lösningens privata moln hanterings plan.

> [!IMPORTANT]
> Ge inte en offentlig IP-adress till den virtuella datorn i hoppet eller exponera port 3389/TCP på det offentliga Internet. 


:::image type="content" source="media/hub-spoke/azure-bastion-hub-vnet.png" alt-text="Virtuellt nätverk för Azure skydds Hub" border="false":::


## <a name="azure-dns-resolution-considerations"></a>Azure DNS lösnings överväganden

För Azure DNS upplösning finns det två tillgängliga alternativ:

-   Använd de Azure Active Directory-domänkontrollanter (Azure AD) som distribueras på hubben (beskrivs i [identitets överväganden](#identity-considerations)) som namnservrar.

-   Distribuera och konfigurera en Azure DNS privat zon.

Det bästa sättet är att kombinera båda för att tillhandahålla tillförlitlig namn matchning för Azure VMware-lösning, lokalt och Azure.

Som en allmän rekommendation använder du den befintliga Azure DNS-infrastrukturen (i det här fallet Active Directory-integrerad DNS) som distribueras till minst två virtuella Azure-datorer som distribuerats i det virtuella Hubbs nätverket och konfigureras i de virtuella eker-nätverken för att använda dessa Azure DNS-servrar i DNS-inställningarna.

Azure Privat DNS kan fortfarande användas där Azure Privat DNS-zonen är länkad till de virtuella nätverken, och DNS-servrar används som hybrid lösningar med villkorlig vidarebefordran till lokal/Azure VMware-lösning som kör DNS och utnyttjar kund Azure Privat DNS-infrastruktur.

Det finns flera saker att tänka på för Azure DNS privata zoner:

* Automatisk registrering måste vara aktiverat för att Azure DNS ska kunna hantera livs cykeln för DNS-poster för de virtuella datorer som distribueras i ekrar virtuella nätverk.
* Det maximala antalet privata DNS-zoner som ett virtuellt nätverk kan länkas till med autoregistrering aktiverat är bara ett.
* Det maximala antalet privata DNS-zoner som ett virtuellt nätverk kan länkas till är 1000 utan att autoregistrering aktive ras.

Lokala och Azure VMware Solution-servrar kan konfigureras med villkorliga vidarebefordrare för att matcha virtuella datorer i Azure för Azure Privat DNS-zonen.

## <a name="identity-considerations"></a>Identitets överväganden

För identitets syfte är det bästa sättet att distribuera minst en AD-domänkontrollant på hubben med hjälp av det delade tjänst under nätet, helst två av dem i zon-distribuerat eller en tillgänglighets uppsättning för virtuella datorer. Se [Azure Architecture Center](/azure/architecture/reference-architectures/identity/adds-extend-domain) för att utöka din lokala AD-domän till Azure.

Dessutom kan du distribuera en annan domänkontrollant på Azure VMware-lösningen för att agera som identitet och DNS-källa i vSphere-miljön.

För vCenter och SSO ställer du in en identitets källa i Azure Portal på **hantera \> identitets \> identitets källor**.

Som rekommenderad praxis integrerar du [AD-domänen med Azure Active Directory](/azure/architecture/reference-architectures/identity/azure-ad).

<!-- LINKS - external -->
[Azure Architecture Center]: /azure/architecture/

[Hub & Spoke topology]: /azure/architecture/reference-architectures/hybrid-networking/hub-spoke

[Azure networking documentation]: ../networking/index.yml

<!-- LINKS - internal -->
