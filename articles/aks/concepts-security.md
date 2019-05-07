---
title: Begrepp - säkerhet i Azure Kubernetes-tjänster (AKS)
description: Läs mer om säkerhet i Azure Kubernetes Service (AKS), inklusive master och nod-kommunikation, nätverksprinciper och Kubernetes hemligheter.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: iainfou
ms.openlocfilehash: 2e655627267546d88f76a2487817bca3153ee91d
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074024"
---
# <a name="security-concepts-for-applications-and-clusters-in-azure-kubernetes-service-aks"></a>Säkerhetsbegrepp för program och -kluster i Azure Kubernetes Service (AKS)

För att skydda din kundinformation som du kör arbetsbelastningar för program i Azure Kubernetes Service (AKS) är en nyckelfaktor i säkerheten för ditt kluster. Kubernetes innehåller säkerhetskomponenter *nätverksprinciper* och *hemligheter*. Azure kan du sedan lägger till i komponenter, till exempel nätverkssäkerhetsgrupper och dirigerad klusteruppgradering. Dessa säkerhetskomponenter kombineras för att hålla ditt AKS-kluster som kör de senaste säkerhetsuppdateringarna för OS och Kubernetes-versioner och med säkra åtkomst till känsliga autentiseringsuppgifter och pod-trafik.

Den här artikeln innehåller grundläggande begrepp som skyddar dina program i AKS:

- [Huvudkomponenter säkerhet](#master-security)
- [Nod-säkerhet](#node-security)
- [Klusteruppgradering](#cluster-upgrades)
- [Nätverkssäkerhet](#network-security)
- [Kubernetes-hemligheter](#kubernetes-secrets)

## <a name="master-security"></a>Master säkerhet

I AKS är Kubernetes huvudkomponenter en del av hanterad tjänst som tillhandahålls av Microsoft. Varje AKS-kluster har sina egna enda Backups, dedikerad Kubernetes master ange API-servern, Scheduler, osv. Den här bakgrunden hanteras och underhålls av Microsoft.

Som standard Kubernetes API-servern använder en offentlig IP-adress och namnge med fullständigt kvalificerade domännamnet (FQDN). Du kan styra åtkomst till API-servern med hjälp av Kubernetes rollbaserade åtkomstkontroller och Azure Active Directory. Mer information finns i [Azure AD-integrering med AKS][aks-aad].

## <a name="node-security"></a>Nod-säkerhet

AKS-noder är Azure virtuella datorer som du hanterar och underhåller. Linux-noder som kör en optimerad Ubuntu-distribution med Moby behållare runtime. Windows Server-noder (för närvarande i förhandsversion i AKS) kör en optimerad Windows Server 2019 släpp och även använda Moby behållare runtime. När ett AKS-kluster skapas eller skalas upp, distribueras automatiskt noderna med den senaste OS säkerhetsuppdateringar och konfigurationer.

Azure-plattformen gäller automatiskt OS säkerhetsuppdateringar för Linux-noder på basis av varje natt. Om en säkerhetsuppdatering för Linux-operativsystem måste startas om för värden, utförs inte att starta om automatiskt. Du kan manuellt starta om Linux-noder eller en vanlig metod är att använda [Kured][kured], en omstart för öppen källkod-daemon för Kubernetes. Kured körs som en [DaemonSet] [ aks-daemonsets] och övervakar varje nod för förekomsten av en fil som anger att en omstart krävs. Omstarter hanteras i klustret med samma [här och tömma processen](#cordon-and-drain) som en uppgradering av klustret.

Windows Server-noder (för närvarande i förhandsversion i AKS), Windows Update inte automatiskt köra och gäller för de senaste uppdateringarna. Med jämna mellanrum kring Windows-versionen uppdateringscykeln och egna verifieringsprocessen, bör du utföra en uppgradering på Windows Server-nod lagringspoolerna AKS-klustret. Den här uppgraderingsprocessen skapar noderna som kör den senaste Windows Server-avbildning och de uppdateringar och sedan tar bort äldre noderna. Mer information om den här processen finns i [uppgradera en nodpool i AKS][nodepool-upgrade].

Noder distribueras i ett privat virtuellt nätverksundernät, med inga offentliga IP-adresserna som tilldelats. För felsökning och hantering, är SSH aktiverat som standard. Den här SSH-åtkomst är bara tillgänglig i den interna IP-adressen.

Noderna använder Azure Managed Disks för att tillhandahålla lagring. För de flesta storlekar på VM-nod är dessa premiumdiskar backas upp av SSD för höga prestanda. De data som lagras på hanterade diskar krypteras automatiskt i vila i Azure-plattformen. För att förbättra redundans, replikeras också på ett säkert sätt dessa diskar i Azure-datacentret.

Kubernetes-miljöer i AKS eller någon annanstans, är för närvarande inte helt säkra för fientlig användning med flera innehavare. Ytterligare säkerhetsfunktioner som *Pod säkerhetsprinciper* eller mer detaljerade rollbaserade åtkomstkontroller (RBAC) för noder försvåra kryphål. Men är SANT säkerhet vid körning av fientlig arbetsbelastningar för flera innehavare, ett hypervisor-program endast säkerhetsnivå som du ska lita på. Säkerhetsdomän för Kubernetes blir hela klustret, inte en enskild nod. Du bör använda fysiskt isolerat kluster för dessa typer av fientlig arbetsbelastningar för flera innehavare. Läs mer om sätt att isolera arbetsbelastningar [bästa praxis för isolering av kluster i AKS][cluster-isolation],

## <a name="cluster-upgrades"></a>Klusteruppgradering

Säkerhet och efterlevnad, eller att använda de senaste funktionerna finns tillhandahåller Azure verktyg för att dirigera uppgraderingen av ett AKS-kluster och komponenter. Den här uppgraderingen orchestration omfattar både Kubernetes-huvud- och agentdatorer komponenter. Du kan visa en [lista över tillgängliga Kubernetes-versioner](supported-kubernetes-versions.md) för AKS-klustret. Du anger en av dessa tillgängliga versioner för att starta uppgraderingsprocessen. Azure och sedan på ett säkert sätt cordons och tömmer noderna AKS och utför uppgraderingen.

### <a name="cordon-and-drain"></a>Cordon och drain

Under uppgraderingen avspärrade AKS-noder separat från klustret så att nya poddarna inte schemaläggs på dem. Noder är sedan tömda och uppgraderas enligt följande:

- En ny nod har distribuerats till nodpool. Den här noden kör den senaste OS-avbildning och korrigeringar.
- En av de befintliga noderna identifieras för uppgradering. Poddar på den här noden är ett smidigt sätt avslutas och schemalagda på de andra noderna i poolen för noden.
- Den här befintliga noden tas bort från AKS-klustret.
- Nästa nod i klustret är avspärrade och tömda med samma process tills alla noder är ersatt som en del av uppgraderingsprocessen.

Mer information finns i [uppgradera ett AKS-kluster][aks-upgrade-cluster].

## <a name="network-security"></a>Nätverkssäkerhet

Du kan distribuera AKS-klustret i den befintliga Azure-nätverk undernät för anslutning och säkerhet med lokala nätverk. Dessa virtuella nätverk kan ha en Azure plats-till-plats-VPN eller Expressroute-anslutning tillbaka till ditt lokala nätverk. Kubernetes ingress domänkontrollanter kan definieras med privat, intern IP-adresser så att tjänster är bara tillgänglig via den här interna nätverksanslutning.

### <a name="azure-network-security-groups"></a>Säkerhetsgrupper för Azure-nätverk

Azure använder regler för nätverkssäkerhetsgrupper för att filtrera trafikflödet i virtuella nätverk. Reglerna definierar källa och mål-IP-adressintervall, portar och protokoll som tillåts eller nekas åtkomst till resurser. Standardregler skapas för att tillåta TLS-trafik till Kubernetes API-servern. När du skapar tjänster med belastningsutjämnare, portmappningar eller ingående vägar ändrar nätverkssäkerhetsgruppen för trafik för att flöda automatiskt i AKS.

## <a name="kubernetes-secrets"></a>Kubernetes-hemligheter

En Kubernetes *hemlighet* används för att mata in känsliga data i poddar, till exempel autentiseringsuppgifter eller nycklar. Först skapar du en hemlighet med hjälp av Kubernetes-API. När du definierar din pod eller distribution av kan en särskild hemlighet begäras. Hemligheter ges endast till noder som har en schemalagd pod som kräver och hemligheten lagras i *tmpfs*, inte skriftliga till disk. När den sista pod på en nod som kräver en hemlighet tas bort raderas hemligheten från nodens tmpfs. Hemligheter lagras i ett visst namnområde och kan bara användas av poddar inom samma namnområde.

Användning av hemligheter minskar den känsliga informationen som definieras i pod eller YAML manifest-tjänsten. I stället kan du begära hemligheten som lagras i Kubernetes API-servern som en del av ditt YAML-manifest. Den här metoden ger endast specifika pod-åtkomst till hemligheten.

## <a name="next-steps"></a>Nästa steg

Kom igång med att skydda dina AKS-kluster, se [uppgradera ett AKS-kluster][aks-upgrade-cluster].

Associerade metodtips finns [bästa praxis för Klustersäkerhet och uppgraderingar i AKS][operator-best-practices-cluster-security].

Mer information om core Kubernetes och AKS-begrepp finns i följande artiklar:

- [Kubernetes / AKS-kluster och arbetsbelastningar][aks-concepts-clusters-workloads]
- [Kubernetes / AKS-identiteten][aks-concepts-identity]
- [Kubernetes / AKS virtuella nätverk][aks-concepts-network]
- [Kubernetes / AKS-lagring][aks-concepts-storage]
- [Kubernetes / AKS skala][aks-concepts-scale]

<!-- LINKS - External -->
[kured]: https://github.com/weaveworks/kured
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/

<!-- LINKS - Internal -->
[aks-daemonsets]: concepts-clusters-workloads.md#daemonsets
[aks-upgrade-cluster]: upgrade-cluster.md
[aks-aad]: azure-ad-integration.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[cluster-isolation]: operator-best-practices-cluster-isolation.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
