---
title: Begränsa utgående trafik i Azure Kubernetes service (AKS)
description: Ta reda på vilka portar och adresser som krävs för att styra utgående trafik i Azure Kubernetes service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 06/06/2019
ms.author: mlearned
ms.openlocfilehash: 9476290669606f6eb6c56b51497f3026b9613698
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034947"
---
# <a name="preview---limit-egress-traffic-for-cluster-nodes-and-control-access-to-required-ports-and-services-in-azure-kubernetes-service-aks"></a>För hands version – begränsa utgående trafik för klusternoder och kontrol lera åtkomst till nödvändiga portar och tjänster i Azure Kubernetes service (AKS)

Som standard har AKS-kluster obegränsad utgående Internet åtkomst. Den här nivån av nätverks åtkomst tillåter noder och tjänster som du kör för att få åtkomst till externa resurser efter behov. Om du vill begränsa utgångs trafik måste ett begränsat antal portar och adresser vara tillgängliga för att upprätthålla felfria kluster underhålls uppgifter. Klustret konfigureras sedan för att endast använda behållare avbildningar för bas system från Microsoft Container Registry (MCR) eller Azure Container Registry (ACR), inte externa offentliga lagrings platser. Du måste konfigurera önskade brand Väggs-och säkerhets regler för att tillåta dessa obligatoriska portar och adresser.

Den här artikeln beskriver vilka nätverks portar och fullständigt kvalificerade domän namn (FQDN) som krävs och valfritt om du begränsar utgående trafik i ett AKS-kluster.  Den här funktionen är för närvarande en förhandsversion.

> [!IMPORTANT]
> AKS för hands versions funktioner är självbetjänings deltagande. För hands versioner tillhandahålls "i befintligt skick" och "som tillgängliga" och undantas från service nivå avtalen och den begränsade garantin. AKS för hands versionerna omfattas delvis av kund supporten på bästa möjliga sätt. Dessa funktioner är därför inte avsedda att användas för produktion. Om du vill ha ytterligare information kan du läsa följande artiklar om support:
>
> * [Support principer för AKS][aks-support-policies]
> * [Vanliga frågor och svar om support för Azure][aks-faq]

## <a name="before-you-begin"></a>Innan du börjar

Du behöver Azure CLI-versionen 2.0.66 eller senare installerad och konfigurerad. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

Om du vill skapa ett AKS-kluster som kan begränsa utgående trafik måste du först aktivera en funktions flagga i din prenumeration. Med den här funktions registreringen konfigureras alla AKS-kluster som du skapar för att använda behållare avbildningar för bas system från MCR eller ACR. Registrera funktions flaggan *AKSLockingDownEgressPreview* genom att använda kommandot [AZ Feature register][az-feature-register] , som visas i följande exempel:

> [!CAUTION]
> När du registrerar en funktion på en prenumeration kan du för närvarande inte avregistrera funktionen. När du har aktiverat vissa för hands versions funktioner kan standarderna användas för alla AKS-kluster och sedan skapas i prenumerationen. Aktivera inte för hands versions funktioner för produktions prenumerationer. Använd en separat prenumeration för att testa för hands versions funktionerna och samla in feedback.

```azurecli-interactive
az feature register --name AKSLockingDownEgressPreview --namespace Microsoft.ContainerService
```

Det tar några minuter för statusen att visa *registrerad*. Du kan kontrol lera registrerings statusen med hjälp av kommandot [AZ feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSLockingDownEgressPreview')].{Name:name,State:properties.state}"
```

När du är klar uppdaterar du registreringen av resurs leverantören *Microsoft. container service* med hjälp av kommandot [AZ Provider register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="egress-traffic-overview"></a>Översikt över utgående trafik

I hanterings-och drift syfte måste noder i ett AKS-kluster ha åtkomst till vissa portar och fullständiga domän namn (FQDN). De här åtgärderna kan vara till för att kommunicera med API-servern eller ladda ned och installera kärn Kubernetes-kluster komponenter och nodens säkerhets uppdateringar. Som standard är utgående (utgående) Internet trafik inte begränsad för noder i ett AKS-kluster. Klustret kan hämta behållar avbildningar för bas system från externa databaser.

Om du vill öka säkerheten för ditt AKS-kluster kan du vilja begränsa utgående trafik. Klustret har kon figurer ATS för hämtning av bas system behållar avbildningar från MCR eller ACR. Om du låser utgående trafik på det här sättet måste du definiera vissa portar och FQDN för att tillåta att AKS-noderna kommunicerar med nödvändiga externa tjänster på rätt sätt. Utan dessa auktoriserade portar och FQDN kan dina AKS-noder inte kommunicera med API-servern eller installera kärn komponenter.

Du kan använda [Azure-brandväggen][azure-firewall] eller en brand Väggs enhet från tredje part för att skydda den utgående trafiken och definiera dessa nödvändiga portar och adresser. AKS skapar inte dessa regler automatiskt åt dig. Följande portar och adresser används som referens när du skapar lämpliga regler i nätverks brand väggen.

> [!IMPORTANT]
> När du använder Azure-brandväggen för att begränsa utgående trafik och skapar en användardefinierad väg (UDR) för att tvinga all utgående trafik, se till att du skapar en lämplig DNAT-regel i brand väggen för att tillåta inträngande trafik korrekt. Genom att använda Azure-brandväggen med en UDR avbryter du ingångs inställningen på grund av asymmetrisk routning. (Problemet beror på att AKS-undernätet har en standard väg som går till brand väggens privata IP-adress, men du använder en offentlig belastningsutjämnare – ingress eller Kubernetes-tjänst av typen: LoadBalancer). I det här fallet tas den inkommande belastnings Utjämnings trafiken emot via dess offentliga IP-adress, men retur vägen går genom brand väggens privata IP-adress. Eftersom brand väggen är tillstånds känslig, släpps det returnerade paketet eftersom brand väggen inte är medveten om en etablerad session. Information om hur du integrerar Azure-brandväggen med ingångs-eller tjänst belastnings utjämning finns i [integrera Azure-brandväggen med azure standard Load Balancer](https://docs.microsoft.com/en-us/azure/firewall/integrate-lb).
>

I AKS finns det två uppsättningar portar och adresser:

* De [portar och den adress som krävs för AKS-kluster](#required-ports-and-addresses-for-aks-clusters) beskriver minimi kraven för godkänd utgående trafik.
* De [valfria rekommenderade adresserna och portarna för AKS-kluster](#optional-recommended-addresses-and-ports-for-aks-clusters) krävs inte för alla scenarier, men integrering med andra tjänster som Azure Monitor fungerar inte korrekt. Granska listan med valfria portar och FQDN och auktorisera alla tjänster och komponenter som används i ditt AKS-kluster.

> [!NOTE]
> Att begränsa utgående trafik fungerar bara på nya AKS-kluster som skapas när du har aktiverat funktions flagga registreringen. För befintliga kluster utför du [en kluster uppgraderings åtgärd][aks-upgrade] med `az aks upgrade` kommandot innan du begränsar den utgående trafiken.

## <a name="required-ports-and-addresses-for-aks-clusters"></a>Obligatoriska portar och adresser för AKS-kluster

Följande utgående portar/nätverks regler krävs för ett AKS-kluster:

* TCP-port *443*
* TCP-port *9000* och TCP-port *22* för tunnelns front-Pod för att kommunicera med tunnel slut på API-servern.
    * Mer information finns i * *. HCP.\< location\>. azmk8s.io* och * *. tun.\< location\>. azmk8s.io* -adresser i följande tabell.

Följande FQDN/program-regler krävs:

| FQDN                       | Port      | Användning      |
|----------------------------|-----------|----------|
| *.hcp.\<location\>.azmk8s.io | HTTPS:443, TCP:22, TCP:9000 | Den här adressen är API-serverns slut punkt. *Ersätt\<plats\>* med den region där ditt AKS-kluster har distribuerats. |
| *.tun.\<location\>.azmk8s.io | HTTPS:443, TCP:22, TCP:9000 | Den här adressen är API-serverns slut punkt. *Ersätt\<plats\>* med den region där ditt AKS-kluster har distribuerats. |
| aksrepos.azurecr.io        | HTTPS:443 | Den här adressen krävs för att komma åt avbildningar i Azure Container Registry (ACR). Det här registret innehåller bilder/diagram från tredje part (till exempel mått Server, kärn-DNS osv.) som krävs för att klustret ska fungera under uppgradering och skalning av klustret|
| *.blob.core.windows.net    | HTTPS:443 | Den här adressen är Server dels arkivet för avbildningar som lagras i ACR. |
| mcr.microsoft.com          | HTTPS:443 | Den här adressen krävs för att komma åt avbildningar i Microsoft Container Registry (MCR). Det här registret innehåller bilder/diagram från första part (till exempel Moby osv.) som krävs för att klustret ska fungera under uppgradering och skalning av klustret |
| *.cdn.mscr.io              | HTTPS:443 | Den här adressen krävs för MCR-lagring som backas upp av Azure Content Delivery Network (CDN). |
| management.azure.com       | HTTPS:443 | Den här adressen krävs för Kubernetes GET/tag-åtgärder. |
| login.microsoftonline.com  | HTTPS:443 | Den här adressen krävs för Azure Active Directory autentisering. |
| ntp.ubuntu.com             | UDP:123   | Den här adressen krävs för NTP-tidssynkronisering på Linux-noder. |

## <a name="optional-recommended-addresses-and-ports-for-aks-clusters"></a>Valfria rekommenderade adresser och portar för AKS-kluster

* UDP-port *53* för DNS

Följande FQDN/program-regler rekommenderas för att AKS-kluster ska fungera korrekt:

| FQDN                                    | Port      | Användning      |
|-----------------------------------------|-----------|----------|
| security.ubuntu.com, azure.archive.ubuntu.com, changelogs.ubuntu.com                           | HTTP:80   | Den här adressen låter Linux-klusternoderna hämta de nödvändiga säkerhets korrigeringarna och uppdateringarna. |
| packages.microsoft.com                  | HTTPS:443 | Den här adressen är Microsoft packages-lagringsplatsen som används för cachelagrat *apt-get-* åtgärder. |
| dc.services.visualstudio.com            | HTTPS:443 | Rekommenderas för att korrigera mått och övervakning med hjälp av Azure Monitor. |
| *.opinsights.azure.com                  | HTTPS:443 | Rekommenderas för att korrigera mått och övervakning med hjälp av Azure Monitor. |
| *.monitoring.azure.com                  | HTTPS:443 | Rekommenderas för att korrigera mått och övervakning med hjälp av Azure Monitor. |
| gov-prod-policy-data.trafficmanager.net | HTTPS:443 | Den här adressen används för korrekt drift av Azure Policy (för närvarande i för hands version i AKS). |
| apt.dockerproject.org                   | HTTPS:443 | Den här adressen används för korrekt driv rutins installation och åtgärd på GPU-baserade noder. |
| nvidia.github.io                        | HTTPS:443 | Den här adressen används för korrekt driv rutins installation och åtgärd på GPU-baserade noder. |

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
