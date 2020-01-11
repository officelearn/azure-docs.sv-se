---
title: Konfigurera nätverk för Azure dev Spaces i olika nätverkstopologier
services: azure-dev-spaces
ms.date: 01/10/2020
ms.topic: conceptual
description: Beskriver nätverks kraven för att köra Azure dev Spaces i Azure Kubernetes Services
keywords: Azure dev Spaces, dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes service, Containers, CNI, Kubernetes, SDN, Network
ms.openlocfilehash: 51604e2862a4d2ff575906fa2ba480ddd10504ed
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2020
ms.locfileid: "75897928"
---
# <a name="configure-networking-for-azure-dev-spaces-in-different-network-topologies"></a>Konfigurera nätverk för Azure dev Spaces i olika nätverkstopologier

Azure dev Spaces körs på Azure Kubernetes service-kluster (AKS) med standard nätverks konfigurationen. Om du vill ändra nätverks konfigurationen för ditt AKS-kluster, till exempel att placera klustret bakom en brand vägg, använda nätverks säkerhets grupper eller använda nätverks principer, måste du lägga till ytterligare överväganden för att köra Azure dev Spaces.

![Konfiguration av virtuellt nätverk](media/configure-networking/virtual-network-clusters.svg)

## <a name="aks-clusters-with-different-virtual-network-or-subnet-configurations"></a>AKS-kluster med olika inställningar för virtuella nätverk eller undernät

Ditt AKS-kluster kan ha en annan virtuell nätverks-eller under näts konfiguration för att begränsa inkommande eller utgående trafik för ditt AKS-kluster. Klustret kan till exempel ligga bakom en brand vägg, t. ex. Azure-brandvägg, eller så kan du använda nätverks säkerhets grupper eller anpassade roller för att begränsa nätverks trafiken.

Azure dev Spaces har vissa krav för ingångs- *och utgående* nätverks trafik samt *endast* ingångs trafik. Om du använder Azure dev Spaces i ett AKS-kluster med ett virtuellt nätverk eller en under näts konfiguration som begränsar trafiken för ditt AKS-kluster, måste du följa följande ingångs-och ingångs-och utgångs trafik krav för att kunna använda Azure dev Spaces fungerar korrekt.

### <a name="ingress-and-egress-network-traffic-requirements"></a>Krav på inkommande och utgående nätverks trafik

Azure dev Spaces behöver inkommande och utgående trafik för följande FQDN: er:

| FQDN                       | Port       | Användning      |
|----------------------------|------------|----------|
| cloudflare.docker.com      | HTTPS: 443 | Hämta Docker-avbildningar för Azure dev Spaces |
| gcr.io                     | HTTPS: 443 | Hämta Helm-avbildningar för Azure dev Spaces |
| storage.googleapis.com     | HTTPS: 443 | Hämta Helm-avbildningar för Azure dev Spaces |
| azds-*. azds. io             | HTTPS: 443 | För att kommunicera med Azure dev Spaces-backend-tjänster för Azure dev Spaces-styrenheten. Du hittar exakt FQDN i *dataplaneFqdn* i `USERPROFILE\.azds\settings.json` |

Uppdatera brand väggen eller säkerhets konfigurationen för att tillåta nätverks trafik till och från alla ovanstående FQDN: er. Om du till exempel använder en brand vägg för att skydda ditt nätverk, ska ovanstående FQDN-namn läggas till i brand väggens program regel för att tillåta trafik till och från dessa domäner.

### <a name="ingress-only-network-traffic-requirements"></a>Ingress endast nätverks trafik krav

Azure dev Spaces tillhandahåller routning av Kubernetes namn områdes nivå samt offentlig åtkomst till tjänster med hjälp av sitt eget fullständiga domän namn. För att båda funktionerna ska fungera uppdaterar du brand väggen eller nätverks konfigurationen för att tillåta att offentliga ingångar till den externa IP-adressen för Azure dev-platser intränger på klustret. Du kan också skapa en [intern belastningsutjämnare][aks-internal-lb] och lägga till en NAT-regel i brand väggen för att översätta den offentliga IP-adressen för brand väggen till IP-adressen för den interna belastningsutjämnaren. Du kan också använda [traefik][traefik-ingress] eller [nginx][nginx-ingress] för att skapa en anpassad ingångs kontroll.

## <a name="aks-cluster-network-requirements"></a>Nätverks krav för AKS-kluster

Med AKS kan du använda [nätverks principer][aks-network-policies] för att styra inkommande och utgående trafik mellan poddar i ett kluster samt utgående trafik från en pod. Azure dev Spaces har vissa krav för ingångs- *och utgående* nätverks trafik samt *endast* ingångs trafik. Om du använder Azure dev Spaces i ett AKS-kluster med AKS-nätverks principer, måste du följa följande ingångs-och ingångs-och utgångs trafik krav för att Azure dev Spaces ska fungera korrekt.

### <a name="ingress-and-egress-network-traffic-requirements"></a>Krav på inkommande och utgående nätverks trafik

Med Azure dev Spaces kan du kommunicera direkt med en POD i ett dev-utrymme i klustret för fel sökning. För att den här funktionen ska fungera lägger du till en nätverks princip som tillåter inkommande och utgående kommunikation till IP-adresserna för Azure dev Spaces-infrastrukturen, som [varierar beroende på region][dev-spaces-ip-auth-range-regions].

### <a name="ingress-only-network-traffic-requirements"></a>Ingress endast nätverks trafik krav

Azure dev Spaces tillhandahåller routning mellan poddar över namn områden. Namn områden med aktiverade Azure dev-utrymmen kan till exempel ha en överordnad/underordnad-relation, vilket gör att nätverks trafiken kan dirigeras mellan poddar över de överordnade och underordnade namn områdena. För att den här funktionen ska fungera lägger du till en nätverks princip som tillåter trafik mellan namn områden där nätverks trafik dirigeras, t. ex. överordnad/underordnad namnrum. Om ingångs styrenheten har distribuerats till *azds* -namnrymden måste ingångs styrenheten dessutom kommunicera med poddar som hanteras av Azure dev Space i en annan namnrymd. För att ingångs styrenheten ska fungera korrekt måste nätverks trafiken tillåtas från namn området *azds* till det namn område där de instrumenterade poddar körs.

## <a name="using-azure-container-networking-with-azure-dev-spaces"></a>Använda Azure Container Network med Azure dev Spaces

Som standard är AKS-kluster konfigurerade för att använda [Kubernetes][aks-kubenet] för nätverk, vilket fungerar med Azure dev Spaces. Du kan också konfigurera ditt AKS-kluster för att använda [cni (Azure Container Networking Interface)][aks-cni]. Om du vill använda Azure dev Spaces med Azure CNI i ditt AKS-kluster kan du tillåta ditt virtuella nätverk och under nätets adress utrymme upp till 10 privata IP-adresser för poddar som distribueras av Azure dev Spaces. Mer information om hur du tillåter privata IP-adresser finns i [AKS Azure cni-dokumentationen][aks-cni-ip-planning].

## <a name="using-api-server-authorized-ip-ranges-with-azure-dev-spaces"></a>Använda API-servern auktoriserade IP-intervall med Azure dev Spaces

Med AKS-kluster kan du konfigurera ytterligare säkerhet som begränsar vilken IP-adress som kan interagera med dina kluster, till exempel att använda anpassade virtuella nätverk eller [Skydda åtkomsten till API-servern med hjälp av auktoriserade IP-adressintervall][aks-ip-auth-ranges]. Om du vill använda Azure dev Spaces när du använder den här extra säkerheten när du [skapar][aks-ip-auth-range-create] klustret, måste du [tillåta ytterligare intervall baserat på din region][dev-spaces-ip-auth-range-regions]. Du kan också [Uppdatera][aks-ip-auth-range-update] ett befintligt kluster för att tillåta dessa ytterligare intervall. Du måste också tillåta IP-adressen för alla utvecklings datorer som ansluter till ditt AKS-kluster för fel sökning för att kunna ansluta till din API-Server.

## <a name="using-aks-private-clusters-with-azure-dev-spaces"></a>Använda privata AKS-kluster med Azure dev Spaces

För närvarande stöds inte Azure dev Spaces med [privata AKS-kluster][aks-private-clusters].

## <a name="azure-dev-spaces-client-requirements"></a>Klient krav för Azure dev Spaces

Azure dev Spaces använder verktyg på klient sidan, till exempel CLI-tillägget för Azure dev Spaces, Visual Studio Code extension och Visual Studio-tillägget för att kommunicera med ditt AKS-kluster för fel sökning. Om du vill använda verktyget för Azure dev-verktyg på klient sidan kan du tillåta trafik från utvecklings datorerna till *azds-\*. azds.io-* domänen. Se *dataplaneFqdn* i `USERPROFILE\.azds\settings.json` för exakt FQDN. Om du använder [tillåtna IP-intervall för API-Server][auth-range-section]måste du också tillåta IP-adressen för alla utvecklings datorer som ansluter till ditt AKS-kluster för fel sökning för att kunna ansluta till din API-Server.

## <a name="next-steps"></a>Nästa steg

Lär dig hur Azure dev Spaces hjälper dig att utveckla mer komplexa program över flera behållare och hur du kan förenkla samarbets utveckling genom att arbeta med olika versioner eller grenar av koden i olika utrymmen.

> [!div class="nextstepaction"]
> [Grupp utveckling i Azure dev Spaces][team-quickstart]

[aks-cni]: ../aks/configure-azure-cni.md
[aks-cni-ip-planning]: ../aks/configure-azure-cni.md#plan-ip-addressing-for-your-cluster
[aks-kubenet]: ../aks/configure-kubenet.md
[aks-internal-lb]: ../aks/internal-lb.md
[aks-ip-auth-ranges]: ../aks/api-server-authorized-ip-ranges.md
[aks-ip-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-ip-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-network-policies]: ../aks/use-network-policies.md
[aks-private-clusters]: ../aks/private-clusters.md
[auth-range-section]: #using-api-server-authorized-ip-ranges-with-azure-dev-spaces
[dev-spaces-ip-auth-range-regions]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[traefik-ingress]: how-to/ingress-https-traefik.md
[nginx-ingress]: how-to/ingress-https-nginx.md
[team-quickstart]: quickstart-team-development.md