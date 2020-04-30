---
title: Begränsa utgående trafik i Azure Kubernetes service (AKS)
description: Ta reda på vilka portar och adresser som krävs för att styra utgående trafik i Azure Kubernetes service (AKS)
services: container-service
ms.topic: article
ms.date: 03/10/2020
ms.openlocfilehash: d723f7b1e7331e65d17dca5873b891ec46d76c0e
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82207181"
---
# <a name="control-egress-traffic-for-cluster-nodes-in-azure-kubernetes-service-aks"></a>Styra utgående trafik för klusternoder i Azure Kubernetes service (AKS)

Som standard har AKS-kluster obegränsad utgående Internet åtkomst. Den här nivån av nätverks åtkomst tillåter noder och tjänster som du kör för att få åtkomst till externa resurser efter behov. Om du vill begränsa utgångs trafik måste ett begränsat antal portar och adresser vara tillgängliga för att upprätthålla felfria kluster underhålls uppgifter. Klustret är konfigurerat som standard för att endast använda behållare avbildningar för bas system från Microsoft Container Registry (MCR) eller Azure Container Registry (ACR). Konfigurera önskade brand Väggs-och säkerhets regler för att tillåta dessa obligatoriska portar och adresser.

Den här artikeln beskriver vilka nätverks portar och fullständigt kvalificerade domän namn (FQDN) som krävs och valfritt om du begränsar utgående trafik i ett AKS-kluster.

> [!IMPORTANT]
> Det här dokumentet beskriver bara hur du låser trafiken som lämnar AKS-undernätet. AKS har inga ingress-krav.  Det går inte att blockera intern under näts trafik med hjälp av nätverks säkerhets grupper (NSG: er) och brand väggar. Använd [nätverks principer][network-policy]för att styra och blockera trafiken i klustret.

## <a name="before-you-begin"></a>Innan du börjar

Du behöver Azure CLI-versionen 2.0.66 eller senare installerad och konfigurerad. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

## <a name="egress-traffic-overview"></a>Översikt över utgående trafik

I hanterings-och drift syfte måste noder i ett AKS-kluster ha åtkomst till vissa portar och fullständiga domän namn (FQDN). De här åtgärderna kan vara till för att kommunicera med API-servern eller ladda ned och installera kärn Kubernetes-kluster komponenter och nodens säkerhets uppdateringar. Som standard är utgående (utgående) Internet trafik inte begränsad för noder i ett AKS-kluster. Klustret kan hämta behållar avbildningar för bas system från externa databaser.

Om du vill öka säkerheten för ditt AKS-kluster kan du vilja begränsa utgående trafik. Klustret har kon figurer ATS för hämtning av bas system behållar avbildningar från MCR eller ACR. Om du låser ut utgående trafik på det här sättet definierar du vissa portar och FQDN så att AKS-noderna kan kommunicera med de nödvändiga externa tjänsterna. Utan dessa auktoriserade portar och FQDN kan dina AKS-noder inte kommunicera med API-servern eller installera kärn komponenter.

Du kan använda [Azure-brandväggen][azure-firewall] eller en brand Väggs enhet från tredje part för att skydda den utgående trafiken och definiera dessa nödvändiga portar och adresser. AKS skapar inte dessa regler automatiskt åt dig. Följande portar och adresser används som referens när du skapar lämpliga regler i nätverks brand väggen.

> [!IMPORTANT]
> När du använder Azure-brandväggen för att begränsa utgående trafik och skapar en användardefinierad väg (UDR) för att tvinga all utgående trafik, se till att du skapar en lämplig DNAT-regel i brand väggen för att tillåta inträngande trafik korrekt. Genom att använda Azure-brandväggen med en UDR avbryter du ingångs inställningen på grund av asymmetrisk routning. (Problemet uppstår om AKS-undernätet har en standard väg som går till brand väggens privata IP-adress, men du använder en offentlig belastningsutjämnare – ingress eller Kubernetes-tjänst av typen: LoadBalancer). I det här fallet tas den inkommande belastnings Utjämnings trafiken emot via dess offentliga IP-adress, men retur vägen går genom brand väggens privata IP-adress. Eftersom brand väggen är tillstånds känslig, släpps det returnerade paketet eftersom brand väggen inte är medveten om en etablerad session. Information om hur du integrerar Azure-brandväggen med ingångs-eller tjänst belastnings utjämning finns i [integrera Azure-brandväggen med azure standard Load Balancer](https://docs.microsoft.com/azure/firewall/integrate-lb).
> Du kan låsa trafiken för TCP-port 9000, TCP-port 22 och UDP-port 1194 med en nätverks regel mellan utgående arbetsnodens IP (er) och IP-adressen för API-servern.

I AKS finns det två uppsättningar portar och adresser:

* De [portar och den adress som krävs för AKS-kluster](#required-ports-and-addresses-for-aks-clusters) beskriver minimi kraven för godkänd utgående trafik.
* De [valfria rekommenderade adresserna och portarna för AKS-kluster](#optional-recommended-addresses-and-ports-for-aks-clusters) krävs inte för alla scenarier, men integrering med andra tjänster som Azure Monitor fungerar inte korrekt. Granska listan med valfria portar och FQDN och auktorisera alla tjänster och komponenter som används i ditt AKS-kluster.

> [!NOTE]
> Det går bara att begränsa utgående trafik på nya AKS-kluster. För befintliga kluster utför du [en kluster uppgraderings åtgärd][aks-upgrade] med `az aks upgrade` kommandot innan du begränsar den utgående trafiken.

## <a name="required-ports-and-addresses-for-aks-clusters"></a>Portar och adresser som behövs för AKS-kluster

Följande utgående portar/nätverks regler krävs för ett AKS-kluster:

* TCP-port *443*
* TCP [IPAddrOfYourAPIServer]: 443 krävs om du har en app som behöver kommunicera med API-servern.  Den här ändringen kan anges efter att klustret har skapats.
* TCP-port *9000*, TCP-port *22* och UDP-port *1194* för tunnelns front-Pod för att kommunicera med tunnel slut på API-servern.
    * Mer information finns i **. HCP.\< Location\>. azmk8s.io* och **. tun.\< Location\>. azmk8s.io* -adresser i följande tabell.
* UDP-port *123* för NTP-tidssynkronisering (Network Time Protocol) (Linux-noder).
* UDP-port *53* för DNS krävs också om du har poddar direkt åtkomst till API-servern.

Följande FQDN/program-regler krävs:

> [!IMPORTANT]
> ***. blob.Core.Windows.net och aksrepos.azurecr.io** är inte längre obligatoriska FQDN-regler för utgående låsning.  För befintliga kluster utför du [en uppgraderings åtgärd för klustret][aks-upgrade] med `az aks upgrade` hjälp av kommandot för att ta bort dessa regler.

> [!IMPORTANT]
> *. cdn.mscr.io har ersatts av *. data.mcr.microsoft.com för Azures offentliga moln regioner. Uppgradera dina befintliga brand Väggs regler för att ändringarna ska börja gälla.

- Azure Global

| FQDN                       | Port      | Användning      |
|----------------------------|-----------|----------|
| *.hcp. \<location\>. azmk8s.io | HTTPS: 443, TCP: 22, TCP: 9000, UDP: 1194 | Den här adressen krävs för nodens <-> API-server kommunikation. Ersätt * \<plats\> * med den region där ditt AKS-kluster har distribuerats. |
| *.tun. \<location\>. azmk8s.io | HTTPS: 443, TCP: 22, TCP: 9000, UDP: 1194 | Den här adressen krävs för nodens <-> API-server kommunikation. Ersätt * \<plats\> * med den region där ditt AKS-kluster har distribuerats. |
| *. cdn.mscr.io       | HTTPS: 443 | Den här adressen krävs för MCR-lagring som backas upp av Azure-Content Delivery Network (CDN). |
| mcr.microsoft.com          | HTTPS: 443 | Den här adressen krävs för att komma åt avbildningar i Microsoft Container Registry (MCR). Det här registret innehåller bilder/diagram från första part (till exempel Moby osv.) som krävs för att klustret ska fungera under uppgradering och skalning av klustret |
| *. data.mcr.microsoft.com             | HTTPS: 443 | Den här adressen krävs för MCR-lagring som backas upp av Azure Content Delivery Network (CDN). |
| management.azure.com       | HTTPS: 443 | Den här adressen krävs för Kubernetes GET/tag-åtgärder. |
| login.microsoftonline.com  | HTTPS: 443 | Den här adressen krävs för Azure Active Directory autentisering. |
| ntp.ubuntu.com             | UDP: 123   | Den här adressen krävs för NTP-tidssynkronisering på Linux-noder. |
| packages.microsoft.com     | HTTPS: 443 | Den här adressen är Microsoft packages-lagringsplatsen som används för cachelagrat *apt-get-* åtgärder.  Exempel paket är Moby, PowerShell och Azure CLI. |
| acs-mirror.azureedge.net      | HTTPS: 443 | Den här adressen är för den lagrings plats som krävs för att installera nödvändiga binärfiler som Kubernetes och Azure CNI. |

- Azure Kina 21Vianet

| FQDN                       | Port      | Användning      |
|----------------------------|-----------|----------|
| *.hcp. \<location\>. CX.prod.service.azk8s.cn | HTTPS: 443, TCP: 22, TCP: 9000, UDP: 1194 | Den här adressen krävs för nodens <-> API-server kommunikation. Ersätt * \<plats\> * med den region där ditt AKS-kluster har distribuerats. |
| *.tun. \<location\>. CX.prod.service.azk8s.cn | HTTPS: 443, TCP: 22, TCP: 9000, UDP: 1194 | Den här adressen krävs för nodens <-> API-server kommunikation. Ersätt * \<plats\> * med den region där ditt AKS-kluster har distribuerats. |
| *. azk8s.cn        | HTTPS: 443 | Den här adressen krävs för att ladda ned nödvändiga binärfiler och avbildningar|
| mcr.microsoft.com          | HTTPS: 443 | Den här adressen krävs för att komma åt avbildningar i Microsoft Container Registry (MCR). Det här registret innehåller bilder/diagram från första part (till exempel Moby osv.) som krävs för att klustret ska fungera under uppgradering och skalning av klustret |
| *. cdn.mscr.io       | HTTPS: 443 | Den här adressen krävs för MCR-lagring som backas upp av Azure-Content Delivery Network (CDN). |
| *. data.mcr.microsoft.com             | HTTPS: 443 | Den här adressen krävs för MCR-lagring som backas upp av Azure Content Delivery Network (CDN). |
| management.chinacloudapi.cn       | HTTPS: 443 | Den här adressen krävs för Kubernetes GET/tag-åtgärder. |
| login.chinacloudapi.cn  | HTTPS: 443 | Den här adressen krävs för Azure Active Directory autentisering. |
| ntp.ubuntu.com             | UDP: 123   | Den här adressen krävs för NTP-tidssynkronisering på Linux-noder. |
| packages.microsoft.com     | HTTPS: 443 | Den här adressen är Microsoft packages-lagringsplatsen som används för cachelagrat *apt-get-* åtgärder.  Exempel paket är Moby, PowerShell och Azure CLI. |

- Azure Government

| FQDN                       | Port      | Användning      |
|----------------------------|-----------|----------|
| *.hcp. \<location\>. CX.AKS.Containerservice.Azure.us | HTTPS: 443, TCP: 22, TCP: 9000, UDP: 1194 | Den här adressen krävs för nodens <-> API-server kommunikation. Ersätt * \<plats\> * med den region där ditt AKS-kluster har distribuerats. |
| *.tun. \<location\>. CX.AKS.Containerservice.Azure.us | HTTPS: 443, TCP: 22, TCP: 9000, UDP: 1194 | Den här adressen krävs för nodens <-> API-server kommunikation. Ersätt * \<plats\> * med den region där ditt AKS-kluster har distribuerats. |
| mcr.microsoft.com          | HTTPS: 443 | Den här adressen krävs för att komma åt avbildningar i Microsoft Container Registry (MCR). Det här registret innehåller bilder/diagram från första part (till exempel Moby osv.) som krävs för att klustret ska fungera under uppgradering och skalning av klustret |
|*. cdn.mscr.io              | HTTPS: 443 | Den här adressen krävs för MCR-lagring som backas upp av Azure-Content Delivery Network (CDN). |
| *. data.mcr.microsoft.com             | HTTPS: 443 | Den här adressen krävs för MCR-lagring som backas upp av Azure Content Delivery Network (CDN). |
| management.usgovcloudapi.net       | HTTPS: 443 | Den här adressen krävs för Kubernetes GET/tag-åtgärder. |
| login.microsoftonline.us  | HTTPS: 443 | Den här adressen krävs för Azure Active Directory autentisering. |
| ntp.ubuntu.com             | UDP: 123   | Den här adressen krävs för NTP-tidssynkronisering på Linux-noder. |
| packages.microsoft.com     | HTTPS: 443 | Den här adressen är Microsoft packages-lagringsplatsen som används för cachelagrat *apt-get-* åtgärder.  Exempel paket är Moby, PowerShell och Azure CLI. |
| acs-mirror.azureedge.net      | HTTPS: 443 | Den här adressen är för den lagrings plats som krävs för att installera nödvändiga binärfiler som Kubernetes och Azure CNI. |

## <a name="optional-recommended-addresses-and-ports-for-aks-clusters"></a>Valfria rekommenderade adresser och portar för AKS-kluster

Följande utgående portar/nätverks regler är valfria för ett AKS-kluster:

Följande FQDN/program-regler rekommenderas för att AKS-kluster ska fungera korrekt:

| FQDN                                    | Port      | Användning      |
|-----------------------------------------|-----------|----------|
| security.ubuntu.com, azure.archive.ubuntu.com, changelogs.ubuntu.com | HTTP: 80   | Den här adressen låter Linux-klusternoderna hämta de nödvändiga säkerhets korrigeringarna och uppdateringarna. |

## <a name="required-addresses-and-ports-for-gpu-enabled-aks-clusters"></a>Obligatoriska adresser och portar för GPU-aktiverade AKS-kluster

Följande FQDN/program-regler krävs för AKS-kluster med aktiverat GPU: er:

| FQDN                                    | Port      | Användning      |
|-----------------------------------------|-----------|----------|
| nvidia.github.io | HTTPS: 443 | Den här adressen används för korrekt driv rutins installation och åtgärd på GPU-baserade noder. |
| us.download.nvidia.com | HTTPS: 443 | Den här adressen används för korrekt driv rutins installation och åtgärd på GPU-baserade noder. |
| apt.dockerproject.org | HTTPS: 443 | Den här adressen används för korrekt driv rutins installation och åtgärd på GPU-baserade noder. |

## <a name="required-addresses-and-ports-with-azure-monitor-for-containers-enabled"></a>Obligatoriska adresser och portar med Azure Monitor för behållare aktiverade

Följande FQDN/program-regler krävs för AKS-kluster som har Azure Monitor för behållare aktiverade:

| FQDN                                    | Port      | Användning      |
|-----------------------------------------|-----------|----------|
| dc.services.visualstudio.com | HTTPS: 443    | Detta är för korrekta mått och övervakning av telemetri med hjälp av Azure Monitor. |
| *.ods.opinsights.azure.com    | HTTPS: 443    | Detta används av Azure Monitor för att mata in Log Analytics-data. |
| *.oms.opinsights.azure.com | HTTPS: 443 | Den här adressen används av omsagent, som används för att autentisera Log Analytics-tjänsten. |
|*.microsoftonline.com | HTTPS: 443 | Detta används för att autentisera och skicka mått till Azure Monitor. |
|*. monitoring.azure.com | HTTPS: 443 | Detta används för att skicka mått data till Azure Monitor. |

## <a name="required-addresses-and-ports-with-azure-dev-spaces-enabled"></a>Obligatoriska adresser och portar med aktiverade Azure dev Spaces

Följande FQDN/program-regler krävs för AKS-kluster som har Azure dev-utrymmen aktiverade:

| FQDN                                    | Port      | Användning      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | HTTPS: 443 | Den här adressen används för att hämta Linux Alpine och andra Azure dev Spaces-bilder |
| gcr.io | HTTP: 443 | Den här adressen används för att hämta Helm/till-avbildningar |
| storage.googleapis.com | HTTP: 443 | Den här adressen används för att hämta Helm/till-avbildningar |
| azds-\<GUID\>. \<location\>. azds.io | HTTPS: 443 | För att kommunicera med Azure dev Spaces-backend-tjänster för din kontrollant. Du hittar exakt FQDN i "dataplaneFqdn" i% USERPROFILE%\.azds\settings.JSON |

## <a name="required-addresses-and-ports-for-aks-clusters-with-azure-policy-in-public-preview-enabled"></a>Obligatoriska adresser och portar för AKS-kluster med Azure Policy (i offentlig för hands version) aktiverat

> [!CAUTION]
> Några av funktionerna nedan finns i för hands version.  Förslagen i den här artikeln kan komma att ändras när funktionen flyttas till den offentliga för hands versionen och framtida versions steg.

Följande FQDN/program-regler krävs för AKS-kluster som har Azure Policy aktiverat.

| FQDN                                    | Port      | Användning      |
|-----------------------------------------|-----------|----------|
| gov-prod-policy-data.trafficmanager.net | HTTPS: 443 | Adressen används för att Azure Policy ska fungera korrekt. (för närvarande i för hands version i AKS) |
| raw.githubusercontent.com | HTTPS: 443 | Den här adressen används för att hämta de inbyggda principerna från GitHub för att säkerställa korrekt drift av Azure Policy. (för närvarande i för hands version i AKS) |
| *. GK. \<location\>. azmk8s.io | HTTPS: 443    | Azure Policy tillägg som pratar med Gatekeeper audit-slutpunkten som körs på huvud servern för att hämta gransknings resultatet. |
| dc.services.visualstudio.com | HTTPS: 443 | Azure Policy tillägg som skickar telemetridata till program insikter-slutpunkten. |

## <a name="required-by-windows-server-based-nodes-enabled"></a>Krävs av Windows Server-baserade noder aktiverade

Följande FQDN/applikations regler krävs för att använda Windows Server-baserade Node-pooler:

| FQDN                                    | Port      | Användning      |
|-----------------------------------------|-----------|----------|
| onegetcdn.azureedge.net, winlayers.blob.core.windows.net, winlayers.cdn.mscr.io, go.microsoft.com | HTTPS: 443 | Installera Windows-relaterade binärfiler |
| mp.microsoft.com, www<span></span>. msftconnecttest.com, ctldl.windowsupdate.com | HTTP: 80 | Installera Windows-relaterade binärfiler |
| kms.core.windows.net | TCP: 1688 | Installera Windows-relaterade binärfiler |

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig vilka portar och adresser som tillåts om du begränsar utgående trafik för klustret. Du kan också definiera hur poddar själva kan kommunicera och vilka begränsningar de har i klustret. Mer information finns i [skydda trafik mellan poddar med hjälp av nätverks principer i AKS][network-policy].

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
