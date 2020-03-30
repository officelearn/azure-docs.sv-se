---
title: Begrepp – Säkerhet i Azure Kubernetes Services (AKS)
description: Lär dig mer om säkerhet i Azure Kubernetes Service (AKS), inklusive huvud- och nodkommunikation, nätverksprinciper och Kubernetes hemligheter.
services: container-service
ms.topic: conceptual
ms.date: 03/01/2019
ms.openlocfilehash: 7238e6cd7ab3625e2953a4408c82802d43372256
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595951"
---
# <a name="security-concepts-for-applications-and-clusters-in-azure-kubernetes-service-aks"></a>Säkerhetsbegrepp för program och kluster i Azure Kubernetes Service (AKS)

För att skydda dina kunddata när du kör programarbetsbelastningar i Azure Kubernetes Service (AKS) är säkerheten för klustret en viktig faktor. Kubernetes innehåller säkerhetskomponenter som *nätverksprinciper* och *hemligheter*. Azure lägger sedan till i komponenter som nätverkssäkerhetsgrupper och iscensatta klusteruppgraderingar. Dessa säkerhetskomponenter kombineras för att hålla AKS-klustret igång de senaste os-säkerhetsuppdateringarna och Kubernetes-utgåvorna och med säker pod-trafik och åtkomst till känsliga autentiseringsuppgifter.

Den här artikeln introducerar de grundläggande begreppen som skyddar dina program i AKS:

- [Säkerhet för huvudkomponenter](#master-security)
- [Nodsäkerhet](#node-security)
- [Klusteruppgraderingar](#cluster-upgrades)
- [Nätverkssäkerhet](#network-security)
- [Kubernetes-hemligheter](#kubernetes-secrets)

## <a name="master-security"></a>Huvudsäkerhet

I AKS är Kubernetes huvudkomponenter en del av den hanterade tjänsten som tillhandahålls av Microsoft. Varje AKS-kluster har sin egen en-innehavare, dedikerade Kubernetes master för att tillhandahålla API Server, Scheduler, etc. Den här hanteraren hanteras och underhålls av Microsoft.

Som standard använder Kubernetes API-server en offentlig IP-adress och ett fullständigt kvalificerat domännamn (FQDN). Du kan styra åtkomsten till API-servern med hjälp av Kubernetes rollbaserade åtkomstkontroller och Azure Active Directory. Mer information finns i [Azure AD-integrering med AKS][aks-aad].

## <a name="node-security"></a>Nodsäkerhet

AKS-noder är virtuella Azure-datorer som du hanterar och underhåller. Linux-noder kör en optimerad Ubuntu-distribution med Moby-behållarens körning. Windows Server-noder (för närvarande i förhandsversion i AKS) kör en optimerad Windows Server 2019-version och använder även Moby-behållarens körning. När ett AKS-kluster skapas eller skalas upp distribueras noderna automatiskt med de senaste operativsystemets säkerhetsuppdateringar och konfigurationer.

Azure-plattformen tillämpar automatiskt OS-säkerhetskorrigeringar på Linux-noder på en nattlig basis. Om en linux OS-säkerhetsuppdatering kräver en omstart av värden utförs inte omstarten automatiskt. Du kan manuellt starta om Linux-noderna, eller en gemensam metod är att använda [Kured][kured], en omstartsdemon för Kubernetes med öppen källkod. Kured körs som en [DaemonSet][aks-daemonsets] och övervakar varje nod för förekomsten av en fil som anger att en omstart krävs. Omstarter hanteras över klustret med samma [avspärrning och tömningsprocess](#cordon-and-drain) som en klusteruppgradering.

För Windows Server-noder (för närvarande i förhandsversion i AKS) körs inte windows update automatiskt och de senaste uppdateringarna. På ett regelbundet schema runt windows update-utgivningscykeln och din egen valideringsprocess bör du utföra en uppgradering på Windows Server-nodpoolerna i AKS-klustret. Den här uppgraderingsprocessen skapar noder som kör den senaste Windows Server-avbildningen och korrigeringsfilerna och tar sedan bort de äldre noderna. Mer information om den här processen finns [i Uppgradera en nodpool i AKS][nodepool-upgrade].

Noder distribueras till ett privat virtuellt nätverksundernät, utan att några offentliga IP-adresser har tilldelats. För felsökning och hantering är SSH aktiverat som standard. Den här SSH-åtkomsten är endast tillgänglig med den interna IP-adressen.

För att tillhandahålla lagring använder noderna Azure Managed Disks. För de flesta VM-nodstorlekar är dessa Premium-diskar som backas upp av högpresterande SSD-enheter. Data som lagras på hanterade diskar krypteras automatiskt i vila inom Azure-plattformen. För att förbättra redundansen replikeras även dessa diskar på ett säkert sätt i Azure-datacentret.

Kubernetes miljöer, i AKS eller någon annanstans, är för närvarande inte helt säkra för fientlig användning av flera innehavare. Ytterligare säkerhetsfunktioner som *Pod Security Policies* eller mer detaljerade rollbaserade åtkomstkontroller (RBAC) för noder gör det svårare att utnyttja. Men för verklig säkerhet när du kör fientliga arbetsbelastningar med flera innehavare är en hypervisor den enda säkerhetsnivå som du bör lita på. Säkerhetsdomänen för Kubernetes blir hela klustret, inte en enskild nod. För dessa typer av fientliga arbetsbelastningar med flera innehavare bör du använda fysiskt isolerade kluster. Mer information om hur du isolerar arbetsbelastningar finns [i Metodtips för klusterisolering i AKS][cluster-isolation],

## <a name="cluster-upgrades"></a>Klusteruppgraderingar

För säkerhet och efterlevnad, eller för att använda de senaste funktionerna, tillhandahåller Azure verktyg för att dirigera uppgraderingen av ett AKS-kluster och komponenter. Den här uppgraderingsorkestreringen innehåller både Kubernetes-huvud- och agentkomponenter. Du kan visa en [lista över tillgängliga Kubernetes-versioner](supported-kubernetes-versions.md) för AKS-klustret. Om du vill starta uppgraderingsprocessen anger du en av dessa tillgängliga versioner. Azure spärrar och tömmer sedan varje AKS-nod säkert och utför uppgraderingen.

### <a name="cordon-and-drain"></a>Avspärrning och avlopp

Under uppgraderingsprocessen är AKS-noder individuellt avspärrade från klustret så att nya poddar inte schemaläggs på dem. Noderna töms sedan och uppgraderas enligt följande:

- En ny nod distribueras till nodpoolen. Den här noden kör den senaste OS-avbildningen och patcharna.
- En av de befintliga noderna identifieras för uppgradering. Poddar på den här noden avslutas graciöst och schemaläggs på de andra noderna i nodpoolen.
- Den här befintliga noden tas bort från AKS-klustret.
- Nästa nod i klustret är avspärrad och dräneras med samma process tills alla noder har ersatts som en del av uppgraderingsprocessen.

Mer information finns i [Uppgradera ett AKS-kluster][aks-upgrade-cluster].

## <a name="network-security"></a>Nätverkssäkerhet

För anslutning och säkerhet med lokala nätverk kan du distribuera AKS-klustret till befintliga Azure-virtuella nätverksundernät. Dessa virtuella nätverk kan ha en Azure Site-to-Site VPN- eller Express Route-anslutning tillbaka till ditt lokala nätverk. Kubernetes ingress-styrenheter kan definieras med privata, interna IP-adresser så att tjänster endast är tillgängliga via den här interna nätverksanslutningen.

### <a name="azure-network-security-groups"></a>Azure-nätverkssäkerhetsgrupper

För att filtrera trafikflödet i virtuella nätverk använder Azure regler för nätverkssäkerhetsgrupper. Dessa regler definierar käll- och mål-IP-intervall, portar och protokoll som tillåts eller nekas åtkomst till resurser. Standardregler skapas för att tillåta TLS-trafik till Kubernetes API-server. När du skapar tjänster med belastningsutjämnare, portmappningar eller ingående vägar ändrar AKS automatiskt nätverkssäkerhetsgruppen så att trafiken kan flöda på rätt sätt.

## <a name="kubernetes-secrets"></a>Kubernetes-hemligheter

En Kubernetes *Secret* används för att injicera känsliga data i poddar, till exempel åtkomstreferenser eller nycklar. Du skapar först en hemlighet med kubernetes-API:et. När du definierar din pod eller distribution kan en viss hemlighet begäras. Hemligheter tillhandahålls endast till noder som har en schemalagd pod som kräver det, och hemligheten lagras i *tmpfs*, inte skriven till disk. När den sista podden på en nod som kräver en hemlighet tas bort tas hemligheten bort från nodens tmpfs. Hemligheter lagras inom ett visst namnområde och kan endast nås av poddar inom samma namnområde.

Användningen av hemligheter minskar den känsliga information som definieras i YAML-pod- eller tjänst-YAML-manifestet. I stället begär du hemligheten som lagras i Kubernetes API Server som en del av YAML-manifestet. Den här metoden ger endast den specifika pod-åtkomsten till hemligheten. Observera: raw hemliga manifestfiler innehåller hemliga data i base64-format (se den [officiella dokumentationen][secret-risks] för mer information). Därför bör den här filen behandlas som känslig information och aldrig åtagit sig att källkontroll.

## <a name="next-steps"></a>Nästa steg

Mer om du vill komma igång med att skydda DINA AKS-kluster finns i [Uppgradera ett AKS-kluster][aks-upgrade-cluster].

För tillhörande metodtips finns i [Metodtips för klustersäkerhet och uppgraderingar i AKS][operator-best-practices-cluster-security] och [metodtips för pod-säkerhet i AKS][developer-best-practices-pod-security].

Mer information om kubernetes och AKS-huvudbegrepp finns i följande artiklar:

- [Kubernetes / AKS kluster och arbetsbelastningar][aks-concepts-clusters-workloads]
- [Kubernetes / AKS-identitet][aks-concepts-identity]
- [Kubernetes / AKS virtuella nätverk][aks-concepts-network]
- [Kubernetes / AKS lagring][aks-concepts-storage]
- [Kubernetes / AKS skala][aks-concepts-scale]

<!-- LINKS - External -->
[kured]: https://github.com/weaveworks/kured
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[secret-risks]: https://kubernetes.io/docs/concepts/configuration/secret/#risks

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
[developer-best-practices-pod-security]:developer-best-practices-pod-security.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
