---
title: Konfigurera nätverk för Azure Dev Spaces i olika nätverkstopologier
services: azure-dev-spaces
ms.date: 03/17/2020
ms.topic: conceptual
description: Beskriver nätverkskraven för att köra Azure Dev Spaces i Azure Kubernetes Services
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, behållare, CNI, kubenet, SDN, nätverk
ms.openlocfilehash: 3e344576caf276ae7cb5fe00395c84810a4e7d32
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262051"
---
# <a name="configure-networking-for-azure-dev-spaces-in-different-network-topologies"></a>Konfigurera nätverk för Azure Dev Spaces i olika nätverkstopologier

Azure Dev Spaces körs på AKS-kluster (Azure Dev Spaces) med standardkonfigurationen för nätverk. Om du vill ändra nätverkskonfigurationen för AKS-klustret, till exempel lägga klustret bakom en brandvägg, använda nätverkssäkerhetsgrupper eller använda nätverksprinciper, måste du ta ytterligare hänsyn till att köra Azure Dev Spaces.

![Konfiguration av virtuellt nätverk](media/configure-networking/virtual-network-clusters.svg)

## <a name="virtual-network-or-subnet-configurations"></a>Konfigurationer för virtuellt nätverk eller undernät

AKS-klustret kan ha en annan virtuell nätverks- eller undernätskonfiguration för att begränsa inkommande eller utgående trafik för AKS-klustret. Klustret kan till exempel ligga bakom en brandvägg, till exempel Azure-brandväggen, eller så kan du använda nätverkssäkerhetsgrupper eller anpassade roller för att begränsa nätverkstrafiken. Du hittar en exempelnätverkskonfiguration i [exempeldatabasen Azure Dev Spaces på GitHub][sample-repo].

Azure Dev Spaces har vissa krav för *ingående och utgående* nätverkstrafik samt *endast inträngningstrafik.* Om du använder Azure Dev Spaces i ett AKS-kluster med ett virtuellt nätverk eller en undernätskonfiguration som begränsar trafiken för AKS-klustret måste du följa följande ingående och endast ingående och utgående trafikkrav för att Azure Dev Spaces ska fungera korrekt.

### <a name="ingress-and-egress-network-traffic-requirements"></a>Krav på in- och utgående nätverkstrafik

Azure Dev Spaces behöver inkommande och utgående trafik för följande FQDN:

| FQDN                       | Port       | Användning      |
|----------------------------|------------|----------|
| cloudflare.docker.com      | HTTPS: 443 | Så här hämtar du dockeravbildningar för Azure Dev Spaces |
| gcr.io                     | HTTPS: 443 | Så här hämtar du helmavbildningar för Azure Dev Spaces |
| storage.googleapis.com     | HTTPS: 443 | Så här hämtar du helmavbildningar för Azure Dev Spaces |
| azds-*.azds.io             | HTTPS: 443 | Så här kommunicerar du med Azure Dev Spaces-backend-tjänster för Azure Dev Spaces-styrenheten. Den exakta FQDN finns i *dataplaneFqdn* i`USERPROFILE\.azds\settings.json` |

Uppdatera brandväggen eller säkerhetskonfigurationen så att nätverkstrafiken till och från alla ovanstående FQDN:er. Om du till exempel använder en brandvägg för att skydda nätverket bör ovanstående FQDN läggas till i brandväggens programregel så att trafik till och från dessa domäner tillåts.

### <a name="ingress-only-network-traffic-requirements"></a>Endast krav på nätverkstrafik inträngning

Azure Dev Spaces tillhandahåller Kubernetes namnområdesdirigering samt offentlig åtkomst till tjänster med egen FQDN. För båda dessa funktioner att fungera uppdaterar du brandväggen eller nätverkskonfigurationen så att offentliga inträngningar till den externa IP-adressen för Azure Dev Spaces-ingress-styrenheten i klustret. Du kan också skapa en [intern belastningsutjämnare][aks-internal-lb] och lägga till en NAT-regel i brandväggen för att översätta brandväggens offentliga IP till IP-adressen för den interna belastningsutjämnaren. Du kan också använda [traefik][traefik-ingress] eller [NGINX][nginx-ingress] för att skapa en anpassad ingående styrenhet.

## <a name="aks-cluster-network-requirements"></a>AKS klusternätverkskrav

MED AKS kan du använda [nätverksprinciper][aks-network-policies] för att styra inkommande och utgående trafik mellan poddar i ett kluster samt utgående trafik från en pod. Azure Dev Spaces har vissa krav för *ingående och utgående* nätverkstrafik samt *endast inträngningstrafik.* Om du använder Azure Dev Spaces i ett AKS-kluster med AKS-nätverksprinciper måste du följa följande endast ingående och ingående och utgående trafikkrav för att Azure Dev Spaces ska fungera korrekt.

### <a name="ingress-and-egress-network-traffic-requirements"></a>Krav på in- och utgående nätverkstrafik

Med Azure Dev Spaces kan du kommunicera direkt med en pod i ett utvecklingsutrymme i klustret för felsökning. Lägg till en nätverksprincip som tillåter inkommande och utgående kommunikation till IP-adresserna för Azure Dev Spaces-infrastrukturen, som [varierar beroende på region][dev-spaces-ip-auth-range-regions].

### <a name="ingress-only-network-traffic-requirements"></a>Endast krav på nätverkstrafik inträngning

Azure Dev Spaces tillhandahåller routning mellan poddar över namnområden. Namnområden med Azure Dev Spaces aktiverat kan till exempel ha en överordnad/underordnad relation, vilket gör att nätverkstrafik kan dirigeras mellan poddar över de överordnade och underordnade namnområdena. Azure Dev Spaces exponerar också tjänstslutpunkter med hjälp av sin egen FQDN. Om du vill konfigurera olika sätt att exponera tjänster och hur det påverkar routning av namnområdesnivå finns [i Använda olika slutpunktsalternativ][endpoint-options].

## <a name="using-azure-cni"></a>Använda Azure CNI

Som standard är AKS-kluster konfigurerade för att använda [kubenet][aks-kubenet] för nätverk, som fungerar med Azure Dev Spaces. Du kan också konfigurera AKS-klustret så att det använder [CNI (Azure Container Networking Interface).][aks-cni] Om du vill använda Azure Dev Spaces med Azure CNI i AKS-klustret tillåter du att ditt virtuella nätverk och undernätsadressutrymmen med upp till 10 privata IP-adresser för poddar som distribueras av Azure Dev Spaces. Mer information om hur du tillåter privata IP-adresser finns i [AKS Azure CNI-dokumentationen][aks-cni-ip-planning].

## <a name="using-api-server-authorized-ip-ranges"></a>Använda API-serverauktoriserade IP-intervall

MED AKS-kluster kan du konfigurera ytterligare säkerhet som begränsar vilken IP-adress som kan interagera med dina kluster, till exempel med hjälp av anpassade virtuella nätverk eller [skydda åtkomsten till API-servern med hjälp av auktoriserade IP-intervall][aks-ip-auth-ranges]. Om du vill använda Azure Dev Spaces när du använder den här extra säkerheten när du [skapar][aks-ip-auth-range-create] [klustret][dev-spaces-ip-auth-range-regions]måste du tillåta ytterligare intervall baserat på din region . Du kan också [uppdatera][aks-ip-auth-range-update] ett befintligt kluster för att tillåta dessa ytterligare intervall. Du måste också tillåta IP-adressen för alla utvecklingsdatorer som ansluter till AKS-klustret för felsökning för att ansluta till din API-server.

## <a name="using-aks-private-clusters"></a>Använda AKS privata kluster

För närvarande stöds inte Azure Dev Spaces med [AKS-privata kluster][aks-private-clusters].

## <a name="using-different-endpoint-options"></a>Använda olika slutpunktsalternativ

Azure Dev Spaces har möjlighet att exponera slutpunkter för dina tjänster som körs på AKS. När du aktiverar Azure Dev Spaces i klustret har du följande alternativ för att konfigurera slutpunktstypen för klustret:

* En *offentlig* slutpunkt, som är standard, distribuerar en ingående styrenhet med en offentlig IP-adress. Den offentliga IP-adressen är registrerad på klustrets DNS, vilket ger allmänheten tillgång till dina tjänster med hjälp av en URL. Du kan visa `azds list-uris`den här webbadressen med .
* En *privat* slutpunkt distribuerar en ingående styrenhet med en privat IP-adress. Med en privat IP-adress är belastningsutjämnaren för klustret endast tillgänglig inifrån klustrets virtuella nätverk. Belastningsutjämnarens privata IP-adress registreras på klustrets DNS så att tjänster i klustrets virtuella nätverk kan nås med hjälp av en URL. Du kan visa `azds list-uris`den här webbadressen med .
* Om *du anger ingen* för slutpunktsalternativet kan ingen ingående styrenhet distribueras. Utan ingående styrenhet distribueras fungerar inte [routningsfunktionerna för Azure Dev Spaces.][dev-spaces-routing] Du kan också implementera din egen lösning för ingående styrenhet med [traefik][traefik-ingress] eller [NGINX][nginx-ingress], vilket gör att routningsfunktionerna kan fungera igen.

Om du vill konfigurera slutpunktsalternativet använder du *-e* eller *--endpoint* när du aktiverar Azure Dev Spaces i klustret. Ett exempel:

> [!NOTE]
> Slutpunktsalternativet kräver att du kör Azure CLI version 2.2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS -e private
```

## <a name="client-requirements"></a>Klientkrav

Azure Dev Spaces använder verktyg på klientsidan, till exempel AZURE Dev Spaces CLI-tillägget, Visual Studio-kodtillägget och Visual Studio-tillägget, för att kommunicera med AKS-klustret för felsökning. Om du vill använda azure dev spaces-verktyg på klientsidan tillåter du trafik från utvecklingsdatorerna till *domänen azds-\*.azds.io.* Se *dataplaneFqdn* in `USERPROFILE\.azds\settings.json` för den exakta FQDN. Om du använder [API-serveraukuktoriserade IP-intervall][auth-range-section]måste du också tillåta IP-adressen för alla utvecklingsdatorer som ansluter till AKS-klustret för felsökning för att ansluta till din API-server.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur Azure Dev Spaces hjälper dig att utveckla mer komplexa program över flera behållare och hur du kan förenkla samarbete genom att arbeta med olika versioner eller grenar av koden i olika utrymmen.

> [!div class="nextstepaction"]
> [Teamutveckling i Azure Dev Spaces][team-quickstart]

[aks-cni]: ../aks/configure-azure-cni.md
[aks-cni-ip-planning]: ../aks/configure-azure-cni.md#plan-ip-addressing-for-your-cluster
[aks-kubenet]: ../aks/configure-kubenet.md
[aks-internal-lb]: ../aks/internal-lb.md
[aks-ip-auth-ranges]: ../aks/api-server-authorized-ip-ranges.md
[aks-ip-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-ip-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-network-policies]: ../aks/use-network-policies.md
[aks-private-clusters]: ../aks/private-clusters.md
[auth-range-section]: #using-api-server-authorized-ip-ranges
[azure-cli-install]: /cli/azure/install-azure-cli
[dev-spaces-ip-auth-range-regions]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[dev-spaces-routing]: how-dev-spaces-works-routing.md
[endpoint-options]: #using-different-endpoint-options
[traefik-ingress]: how-to/ingress-https-traefik.md
[nginx-ingress]: how-to/ingress-https-nginx.md
[sample-repo]: https://github.com/Azure/dev-spaces/tree/master/advanced%20networking
[team-quickstart]: quickstart-team-development.md