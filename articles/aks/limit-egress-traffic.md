---
title: Begränsa utgående trafik i Azure Kubernetes Service (AKS)
description: Lär dig vilka portar och adresser som krävs för att styra utgående trafik i Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 03/10/2020
ms.openlocfilehash: 2cd7aeea272d22615d3ba3d3db6acc2c84d22cca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79080181"
---
# <a name="control-egress-traffic-for-cluster-nodes-in-azure-kubernetes-service-aks"></a>Styra utgående trafik för klusternoder i Azure Kubernetes Service (AKS)

Som standard har AKS-kluster obegränsad utgående (utgående) internetåtkomst. Med den här nivån av nätverksåtkomst kan noder och tjänster som du kör komma åt externa resurser efter behov. Om du vill begränsa utgående trafik måste ett begränsat antal portar och adresser vara tillgängliga för att upprätthålla felfria klusterunderhållsuppgifter. Klustret är konfigurerat som standard för att endast använda bassystembehållareavbildningar från MCR (Microsoft Container Registry) eller Azure Container Registry (ACR). Konfigurera dina önskade brandväggs- och säkerhetsregler så att dessa nödvändiga portar och adresser tillåts.

I den här artikeln beskrivs vilka nätverksportar och fullständigt kvalificerade domännamn (FQDN) som krävs och är valfria om du begränsar utgående trafik i ett AKS-kluster.

> [!IMPORTANT]
> Det här dokumentet beskriver bara hur du låser trafiken och lämnar AKS-undernätet. AKS har inga ingresskrav.  Det går inte att blockera intern undernätstrafik med hjälp av nätverkssäkerhetsgrupper (NSG) och brandväggar. Om du vill styra och blockera trafiken i klustret använder du [Nätverksprinciper][network-policy].

## <a name="before-you-begin"></a>Innan du börjar

Du behöver Azure CLI version 2.0.66 eller senare installerad och konfigurerad. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

## <a name="egress-traffic-overview"></a>Översikt över utgående trafik

För hantering och drift måste noder i ett AKS-kluster komma åt vissa portar och fullständigt kvalificerade domännamn (FQDN). Dessa åtgärder kan vara att kommunicera med API-servern, eller att hämta och sedan installera centrala Kubernetes klusterkomponenter och nod säkerhetsuppdateringar. Som standard är egress (utgående) internettrafik inte begränsad för noder i ett AKS-kluster. Klustret kan hämta bassystembehållareavbildningar från externa databaser.

Om du vill öka säkerheten för AKS-klustret kanske du vill begränsa utgående trafik. Klustret är konfigurerat för att hämta bassystembehållareavbildningar från MCR eller ACR. Om du låser utgående trafik på det här sättet definierar du specifika portar och FQDN:er så att AKS-noderna kan kommunicera korrekt med nödvändiga externa tjänster. Utan dessa auktoriserade portar och FQDN kan aks-noder inte kommunicera med API-servern eller installera kärnkomponenter.

Du kan använda [Azure-brandväggen][azure-firewall] eller en brandväggsinstallation från tredje part för att skydda din utgående trafik och definiera dessa nödvändiga portar och adresser. AKS skapar inte automatiskt dessa regler åt dig. Följande portar och adresser är som referens när du skapar lämpliga regler i nätverksbrandväggen.

> [!IMPORTANT]
> När du använder Azure Firewall för att begränsa utgående trafik och skapa en användardefinierad väg (UDR) för att tvinga all utgående trafik, se till att du skapar en lämplig DNAT-regel i brandväggen för att korrekt tillåta inkommande trafik. Om du använder Azure-brandväggen med en UDR bryts ingressinställningen på grund av asymmetrisk routning. (Problemet uppstår om AKS-undernätet har en standardväg som går till brandväggens privata IP-adress, men du använder en offentlig belastningsutjämnare - ingående eller Kubernetes tjänst av typen: LoadBalancer). I det här fallet tas den inkommande belastningsutjämnartrafiken emot via sin offentliga IP-adress, men retursökvägen går genom brandväggens privata IP-adress. Eftersom brandväggen är tillståndskänslig, tappar den det returnerande paketet eftersom brandväggen inte är medveten om en etablerad session. Mer information om hur du integrerar Azure-brandväggen med din ingående eller tjänstbelastningsutjämnare finns i [Integrera Azure-brandväggen med Azure Standard Load Balancer](https://docs.microsoft.com/azure/firewall/integrate-lb).
> Du kan låsa trafiken för TCP-port 9000, TCP-port 22 och UDP-port 1194 med hjälp av en nätverksregel mellan utgående arbetarnoden IP och IP för API-servern.

I AKS finns det två uppsättningar portar och adresser:

* De [portar och den adress som krävs för AKS-kluster](#required-ports-and-addresses-for-aks-clusters) beskriver minimikraven för auktoriserad utgående trafik.
* De [valfria rekommenderade adresserna och portarna för AKS-kluster](#optional-recommended-addresses-and-ports-for-aks-clusters) krävs inte för alla scenarier, men integrering med andra tjänster som Azure Monitor fungerar inte korrekt. Granska den här listan över valfria portar och FQDN:er och auktorisera alla tjänster och komponenter som används i AKS-klustret.

> [!NOTE]
> Att begränsa utgående trafik fungerar bara på nya AKS-kluster. För befintliga kluster utför du en `az aks upgrade` [klusteruppgraderingsåtgärd][aks-upgrade] med kommandot innan du begränsar utgående trafik.

## <a name="required-ports-and-addresses-for-aks-clusters"></a>Portar och adresser som behövs för AKS-kluster

Följande utgående portar/nätverksregler krävs för ett AKS-kluster:

* TCP-port *443*
* TCP [IPAddrOfYourAPIServer]:443 krävs om du har en app som behöver prata med API-servern.  Den här ändringen kan ställas in när klustret har skapats.
* TCP-port *9000*, TCP-port *22* och UDP-port *1194* för tunnelfrontenheten att kommunicera med tunneländen på API-servern.
    * För att få mer specifik, se **.hcp.\< plats\>.azmk8s.io* och **.tun.\< plats\>.azmk8s.io* adresser i följande tabell.
* UDP-port *123* för NTP-tidssynkronisering (Network Time Protocol) (Linux-noder).
* UDP-port *53* för DNS krävs också om du har poddar som har direkt åtkomst till API-servern.

Följande FQDN/ applikationsregler krävs:

> [!IMPORTANT]
> ***.blob.core.windows.net och aksrepos.azurecr.io** krävs inte längre FQDN-regler för låsning av utgående.  För befintliga kluster utför du en `az aks upgrade` [klusteruppgraderingsåtgärd][aks-upgrade] med kommandot för att ta bort dessa regler.

> [!IMPORTANT]
> *.cdn.mscr.io har ersatts av *.data.mcr.microsoft.com för Azure public cloud-regionerna. Uppgradera dina befintliga brandväggsregler för att ändringarna ska börja gälla.

- Azure Global

| FQDN                       | Port      | Användning      |
|----------------------------|-----------|----------|
| *.hcp. \<plats\>.azmk8s.io | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | Den här adressen krävs för nod <-> API-serverkommunikation. Ersätt * \<\> plats* med den region där AKS-klustret distribueras. |
| *.tun. \<plats\>.azmk8s.io | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | Den här adressen krävs för nod <-> API-serverkommunikation. Ersätt * \<\> plats* med den region där AKS-klustret distribueras. |
| *.cdn.mscr.io       | HTTPS:443 | Den här adressen krävs för MCR-lagring som backas upp av AZURE Content Delivery Network (CDN). |
| mcr.microsoft.com          | HTTPS:443 | Den här adressen krävs för att komma åt avbildningar i MCR (Microsoft Container Registry). Det här registret innehåller avbildningar/diagram från första part (till exempel moby osv.) som krävs för att klustret ska fungera under uppgradering och skala av klustret |
| *.data.mcr.microsoft.com             | HTTPS:443 | Den här adressen krävs för MCR-lagring som backas upp av Azure-innehållsleveransnätverket (CDN). |
| management.azure.com       | HTTPS:443 | Den här adressen krävs för Kubernetes GET/PUT-åtgärder. |
| login.microsoftonline.com  | HTTPS:443 | Den här adressen krävs för Azure Active Directory-autentisering. |
| ntp.ubuntu.com             | UDP:123   | Den här adressen krävs för NTP-tidssynkronisering på Linux-noder. |
| packages.microsoft.com     | HTTPS:443 | Den här adressen är Microsoft-paketdatabasen som används för cachelagrade *apt-get-åtgärder.*  Exempelpaket är Moby, PowerShell och Azure CLI. |
| acs-mirror.azureedge.net      | HTTPS:443 | Den här adressen är för den databas som krävs för att installera obligatoriska binärfiler som kubenet och Azure CNI. |

- Azure Kina 21Vianet

| FQDN                       | Port      | Användning      |
|----------------------------|-----------|----------|
| *.hcp. \<plats\>.cx.prod.service.azk8s.cn | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | Den här adressen krävs för nod <-> API-serverkommunikation. Ersätt * \<\> plats* med den region där AKS-klustret distribueras. |
| *.tun. \<plats\>.cx.prod.service.azk8s.cn | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | Den här adressen krävs för nod <-> API-serverkommunikation. Ersätt * \<\> plats* med den region där AKS-klustret distribueras. |
| *.azk8s.cn        | HTTPS:443 | Den här adressen krävs för att hämta nödvändiga binärfiler och bilder|
| mcr.microsoft.com          | HTTPS:443 | Den här adressen krävs för att komma åt avbildningar i MCR (Microsoft Container Registry). Det här registret innehåller avbildningar/diagram från första part (till exempel moby osv.) som krävs för att klustret ska fungera under uppgradering och skala av klustret |
| *.cdn.mscr.io       | HTTPS:443 | Den här adressen krävs för MCR-lagring som backas upp av AZURE Content Delivery Network (CDN). |
| *.data.mcr.microsoft.com             | HTTPS:443 | Den här adressen krävs för MCR-lagring som backas upp av Azure-innehållsleveransnätverket (CDN). |
| management.chinacloudapi.cn       | HTTPS:443 | Den här adressen krävs för Kubernetes GET/PUT-åtgärder. |
| login.chinacloudapi.cn  | HTTPS:443 | Den här adressen krävs för Azure Active Directory-autentisering. |
| ntp.ubuntu.com             | UDP:123   | Den här adressen krävs för NTP-tidssynkronisering på Linux-noder. |
| packages.microsoft.com     | HTTPS:443 | Den här adressen är Microsoft-paketdatabasen som används för cachelagrade *apt-get-åtgärder.*  Exempelpaket är Moby, PowerShell och Azure CLI. |

- Azure Government

| FQDN                       | Port      | Användning      |
|----------------------------|-----------|----------|
| *.hcp. \<plats\>.cx.aks.containerservice.azure.us | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | Den här adressen krävs för nod <-> API-serverkommunikation. Ersätt * \<\> plats* med den region där AKS-klustret distribueras. |
| *.tun. \<plats\>.cx.aks.containerservice.azure.us | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | Den här adressen krävs för nod <-> API-serverkommunikation. Ersätt * \<\> plats* med den region där AKS-klustret distribueras. |
| mcr.microsoft.com          | HTTPS:443 | Den här adressen krävs för att komma åt avbildningar i MCR (Microsoft Container Registry). Det här registret innehåller avbildningar/diagram från första part (till exempel moby osv.) som krävs för att klustret ska fungera under uppgradering och skala av klustret |
|*.cdn.mscr.io              | HTTPS:443 | Den här adressen krävs för MCR-lagring som backas upp av AZURE Content Delivery Network (CDN). |
| *.data.mcr.microsoft.com             | HTTPS:443 | Den här adressen krävs för MCR-lagring som backas upp av Azure-innehållsleveransnätverket (CDN). |
| management.usgovcloudapi.net       | HTTPS:443 | Den här adressen krävs för Kubernetes GET/PUT-åtgärder. |
| login.microsoftonline.us  | HTTPS:443 | Den här adressen krävs för Azure Active Directory-autentisering. |
| ntp.ubuntu.com             | UDP:123   | Den här adressen krävs för NTP-tidssynkronisering på Linux-noder. |
| packages.microsoft.com     | HTTPS:443 | Den här adressen är Microsoft-paketdatabasen som används för cachelagrade *apt-get-åtgärder.*  Exempelpaket är Moby, PowerShell och Azure CLI. |
| acs-mirror.azureedge.net      | HTTPS:443 | Den här adressen är för den databas som krävs för att installera obligatoriska binärfiler som kubenet och Azure CNI. |

## <a name="optional-recommended-addresses-and-ports-for-aks-clusters"></a>Valfria rekommenderade adresser och portar för AKS-kluster

Följande utgående portar/nätverksregler är valfria för ett AKS-kluster:

Följande FQDN/ applikationsregler rekommenderas för AKS-kluster att fungera korrekt:

| FQDN                                    | Port      | Användning      |
|-----------------------------------------|-----------|----------|
| security.ubuntu.com, azure.archive.ubuntu.com, changelogs.ubuntu.com | HTTP:80   | Med den här adressen kan Linux-klusternoderna hämta nödvändiga säkerhetskorrigeringar och uppdateringar. |

## <a name="required-addresses-and-ports-for-gpu-enabled-aks-clusters"></a>Nödvändiga adresser och portar för GPU-aktiverade AKS-kluster

Följande FQDN/-programregler krävs för AKS-kluster som har GPU aktiverat:

| FQDN                                    | Port      | Användning      |
|-----------------------------------------|-----------|----------|
| nvidia.github.io | HTTPS:443 | Den här adressen används för korrekt drivrutinsinstallation och drift på GPU-baserade noder. |
| us.download.nvidia.com | HTTPS:443 | Den här adressen används för korrekt drivrutinsinstallation och drift på GPU-baserade noder. |
| apt.dockerproject.org | HTTPS:443 | Den här adressen används för korrekt drivrutinsinstallation och drift på GPU-baserade noder. |

## <a name="required-addresses-and-ports-with-azure-monitor-for-containers-enabled"></a>Obligatoriska adresser och portar med Azure Monitor för behållare aktiverade

Följande FQDN/-programregler krävs för AKS-kluster som har Azure Monitor för behållare aktiverade:

| FQDN                                    | Port      | Användning      |
|-----------------------------------------|-----------|----------|
| dc.services.visualstudio.com | HTTPS:443    | Detta är för korrekta mått och övervakning av telemetri med Azure Monitor. |
| *.ods.opinsights.azure.com    | HTTPS:443    | Detta används av Azure Monitor för intag av logganalysdata. |
| *.oms.opinsights.azure.com | HTTPS:443 | Den här adressen används av omsagent, som används för att autentisera logganalystjänsten. |
|*.microsoftonline.com | HTTPS:443 | Detta används för att autentisera och skicka mått till Azure Monitor. |
|*.monitoring.azure.com | HTTPS:443 | Detta används för att skicka måttdata till Azure Monitor. |

## <a name="required-addresses-and-ports-with-azure-dev-spaces-enabled"></a>Obligatoriska adresser och portar med Azure Dev Spaces aktiverat

Följande FQDN/-programregler krävs för AKS-kluster som har Azure Dev Spaces aktiverat:

| FQDN                                    | Port      | Användning      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | HTTPS:443 | Den här adressen används för att hämta linux alpine och andra Azure Dev Spaces-avbildningar |
| gcr.io | HTTP:443 | Den här adressen används för att dra helm/rorkultbilder |
| storage.googleapis.com | HTTP:443 | Den här adressen används för att dra helm/rorkultbilder |
| azds-\<guid\>. \<plats\>.azds.io | HTTPS:443 | Så här kommunicerar du med Azure Dev Spaces-backend-tjänster för din styrenhet. Den exakta FQDN finns i "dataplaneFqdn" i %USERPROFILE%\.azds\settings.json |

## <a name="required-addresses-and-ports-for-aks-clusters-with-azure-policy-in-public-preview-enabled"></a>Obligatoriska adresser och portar för AKS-kluster med Azure Policy (i offentlig förhandsversion) aktiverade

> [!CAUTION]
> Några av funktionerna nedan är i förhandsgranskning.  Förslagen i den här artikeln kan komma att ändras när funktionen flyttas till offentliga förhandsgransknings- och framtida utgivningsfaser.

Följande FQDN/-programregler krävs för AKS-kluster som har Azure-principen aktiverad.

| FQDN                                    | Port      | Användning      |
|-----------------------------------------|-----------|----------|
| gov-prod-policy-data.trafficmanager.net | HTTPS:443 | Den här adressen används för korrekt drift av Azure Policy. (för närvarande i förhandsversion i AKS) |
| raw.githubusercontent.com | HTTPS:443 | Den här adressen används för att hämta de inbyggda principerna från GitHub för att säkerställa korrekt drift av Azure Policy. (för närvarande i förhandsversion i AKS) |
| *.gk. \<plats\>.azmk8s.io | HTTPS:443    | Azure-principtillägg som talar med Gatekeeper-granskningsslutpunkten som körs i huvudservern för att få granskningsresultaten. |
| dc.services.visualstudio.com | HTTPS:443 | Azure principtillägg som skickar telemetridata till slutpunkten för programinsikter. |

## <a name="required-by-windows-server-based-nodes-in-public-preview-enabled"></a>Krävs av Windows Server-baserade noder (i offentlig förhandsversion) aktiverade

> [!CAUTION]
> Några av funktionerna nedan är i förhandsgranskning.  Förslagen i den här artikeln kan komma att ändras när funktionen flyttas till offentliga förhandsgransknings- och framtida utgivningsfaser.

Följande FQDN/-programregler krävs för Windows Server-baserade AKS-kluster:

| FQDN                                    | Port      | Användning      |
|-----------------------------------------|-----------|----------|
| onegetcdn.azureedge.net, winlayers.blob.core.windows.net, winlayers.cdn.mscr.io, go.microsoft.com | HTTPS:443 | Så här installerar du windowsrelaterade binärfiler |
| mp.microsoft.com, www<span></span>.msftconnecttest.com, ctldl.windowsupdate.com | HTTP:80 | Så här installerar du windowsrelaterade binärfiler |
| kms.core.windows.net | TCP:1688 | Så här installerar du windowsrelaterade binärfiler |


## <a name="next-steps"></a>Nästa steg

I den här artikeln fick du veta vilka portar och adresser som ska tillåtas om du begränsar utgående trafik för klustret. Du kan också definiera hur poddar själva kan kommunicera och vilka begränsningar de har i klustret. Mer information finns i [Säker trafik mellan poddar med hjälp av nätverksprinciper i AKS][network-policy].

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[network-policy]: use-network-policies.md
[azure-firewall]: ../firewall/overview.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[aks-upgrade]: upgrade-cluster.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
