---
title: Introduktion till Azure Kubernetes Service
description: Läs mer om funktionerna och fördelarna med att använda Azure Kubernetes Service för att distribuera och hantera containerbaserade program i Azure.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: overview
ms.date: 03/05/2019
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 0960977a17925ffd922e75fa03847b7023241c4e
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58201508"
---
# <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)

Azure Kubernetes Service (AKS) gör det enkelt att distribuera ett hanterat Kubernetes-kluster i Azure. AKS minskar komplexiteten och arbetet med att hantera Kubernetes genom att avlasta en stor del av det ansvaret till Azure. Som Kubernetes-värdtjänst hanterar Azure viktiga uppgifter som övervakning av hälsotillstånd och underhåll åt dig. Kubernetes-huvudservrar hanteras av Azure. Du kan endast hantera och underhålla agentnoderna. Som hanterade Kubernetes tjänst är AKS kostnadsfri, du betalar bara för agentnoder i dina kluster, inte för de överordnade noder.

Du kan skapa ett AKS-kluster i Azure-portalen med Azure CLI eller ett malldrivet distributionsalternativ, till exempel Resource Manager-mallar och Terraform. När du distribuerar ett AKS-kluster styr Kubernetes och alla noder distribueras och konfigureras för dig. Ytterligare funktioner, till exempel avancerade nätverk, Azure Active Directory-integrering och övervakning kan också konfigureras under distributionsprocessen.

Mer information om grundläggande om Kubernetes finns i [Kubernetes viktiga begrepp för AKS][concepts-clusters-workloads].

Kom igång genom att slutföra snabbstarten om AKS [i Azure-portalen][aks-portal] eller [med Azure CLI][aks-cli].

## <a name="access-security-and-monitoring"></a>Åtkomst, säkerhet och övervakning

För ökad säkerhet och hantering kan AKS integreras med Azure Active Directory och använda Kubernetes rollbaserade åtkomstkontroller. Du kan också övervaka hälsotillståndet för ditt kluster och dina resurser.

### <a name="identity-and-security-management"></a>Identitets- och säkerhetshantering

Om du vill begränsa åtkomsten till klusterresurser har AKS stöd för [Kubernetes rollbaserad åtkomstkontroll (RBAC)][kubernetes-rbac]. Du kan använda RBAC för att kontrollera åtkomst till Kubernetes-resurser och -namnområden samt behörigheter till dessa resurser. Ett AKS-kluster kan också konfigureras för att integrera med Azure Active Directory (AD). Med Azure AD-integrering kan du konfigurera Kubernetes-åtkomst baserat på din befintliga identitet och gruppmedlemskap. Dina befintliga Azure AD-användare och -grupper kan ges åtkomst till AKS resurser och integrerad inloggning.

Läs mer om identitet, [alternativen för åtkomst och identitet för AKS][concepts-identity].

Mer information om att säkra AKS-kluster finns i [integrera Azure Active Directory med AKS][aks-aad].

### <a name="integrated-logging-and-monitoring"></a>Integrerad loggning och övervakning

Azure Monitor för hälsotillstånd för behållare samlar in minne och mått från behållare, noder och styrenheter för att förstå hur dina AKS-kluster och distribuerade program fungerar. Behållarloggar är tillgängliga och du kan också [granska huvudloggarna för Kubernetes][aks-master-logs]. Övervakningsdata lagras i Azure Log Analytics-arbetsytan och är tillgänglig via Azure Portal, Azure CLI eller en REST-slutpunkt.

Mer information finns i [Övervaka Azure Kubernetes Service hälsotillstånd för containrar][container-health].

## <a name="cluster-and-node"></a>Kluster och nod

AKS-noder körs på virtuella Azure-datorer. Du kan ansluta lagringen till noder och poddar, uppgradera klusterkomponenter och använda GPU:er.

### <a name="cluster-node-and-pod-scaling"></a>Klusternoder och poddskalning

När behovet av resurser ändras kan du ändra skalan för klusternoder och poddar på din tjänst uppåt eller nedåt. Du kan använda den vågräta autoskalningen för poddar eller autoskalningen för kluster. Med den här metoden för att skala kan AKS-klustret automatiskt justera till krav och endast köra de resurser som behövs.

Mer information finns i [Skala ett kluster i Azure Kubernetes Service (AKS)][aks-scale].

### <a name="cluster-node-upgrades"></a>Uppgraderingar av klusternod

Azure Kubernetes Service erbjuder flera Kubernetes-versioner. När nya versioner blir tillgängliga i AKS uppgraderas klustret med hjälp av Azure Portal eller Azure CLI. Under uppgraderingen blir noderna noggrant avspärrade och tömda för att minimera störningar i program som körs.

Mer information om livscykelversioner finns i [Kubernetes-versioner som stöds i AKS][aks-supported versions]. Uppgraderingssteg finns i [Uppgradera ett kluster i Azure Kubernetes Service (AKS)][aks-upgrade].

### <a name="gpu-enabled-nodes"></a>GPU-aktiverade noder

AKS stöder skapandet av GPU-aktiverade nodpooler. Azure tillhandahåller för närvarande enskilda eller flera GPU-aktiverade virtuella datorer. GPU-aktiverade virtuella datorer är utformade för beräkningsintensiva, grafikintensiva och visualiseringar av arbetsbelastningar.

Mer information finns i [Använda GPU-kort på AKS][aks-gpu].

### <a name="storage-volume-support"></a>Stöd för lagringsvolym

Du kan montera lagringsvolymer för beständiga data för att stödja arbetsbelastningar för program. Du kan använda både statiska och dynamiska volymer. Beroende på hur många anslutna poddar ska dela lagringen kan du använda lagring med Azure Disk för enkel poddåtkomst eller Azure Files för åtkomst till flera poddar samtidigt.

Mer information finns i [lagringsalternativ för program i AKS][concepts-storage].

Kom igång med dynamisk beständiga volymer med [Azure Disks] [ azure-disk] eller [Azure Files][azure-files].

## <a name="virtual-networks-and-ingress"></a>Virtuella nätverk och ingress

Ett AKS-kluster kan distribueras till ett befintligt virtuellt nätverk. I den här konfigurationen tilldelas varje pod i klustret en IP-adress i det virtuella nätverket och kan kommunicera direkt med andra poddar i klustret och andra noder i det virtuella nätverket. Poddar kan också ansluta till andra tjänster i en peer-kopplad virtuell dator och lokala nätverk via ExpressRoute och plats-till-plats (S2S) VPN-anslutningar.

Mer information finns i den [Network begrepp för program i AKS][aks-networking].

Kom igång med ingående trafik, se [HTTP-programroutning][aks-http-routing].

### <a name="ingress-with-http-application-routing"></a>Ingress with HTTP-programroutning

HTTP-programroutningen gör det enkelt att komma åt program som distribueras till AKS-klustret. När aktiverad, konfigurerar HTTP-programroutningen en ingångskontroll i AKS-klustret. När program distribueras konfigureras offentligt tillgängliga DNS-namn automatiskt. HTTP-program för routning konfigurerar en DNS-zon och integrerar den med AKS-klustret. Du kan sedan distribuera Kubernetes-ingressresurser som vanligt.

Kom igång med ingående trafik, se [HTTP-programroutning][aks-http-routing].

## <a name="development-tooling-integration"></a>Integrering av utvecklingsverktyg

Kubernetes har ett omfattande utbud av utvecklings- och hanteringsverktyg, till exempel Helm, Draft och Kubernetes-tillägget för Visual Studio-koden. Dessa verktyg fungerar sömlöst med AKS.

Vidare ger Azure Dev Spaces ger en snabb, iterativ Kubernetes-utvecklingsupplevelse för team. Du kan köra och felsöka containrar direkt i AKS med minimal konfiguration. Komma igång med [Azure Dev Spaces][azure-dev-spaces].

Azure DevOps-projektet tillhandahåller en enkel lösning för att ta befintlig kod och Git-lagringsplats till Azure. DevOps-projektet skapar automatiskt Azure-resurser som AKS, en versionspipeline i Azure DevOps Services som omfattar en kompileringspipeline för CI och skapar en versionspipeline för CD och skapar sedan en Azure Application Insights-resurs för övervakning.

Mer information finns i [Azure DevOps-projekt][azure-devops].

## <a name="docker-image-support-and-private-container-registry"></a>Stöd för docker-avbildning och privat behållarregister

AKS har stöd för Docker-avbildningsformatet. Integrera AKS med Azure Container Registry (ACR) för privat lagring av Docker-avbildningar.

Om du vill skapa en privat avbildningslagring, se [Azure Container Registry][acr-docs].

## <a name="kubernetes-certification"></a>Kubernetes-certifiering

Azure Kubernetes Service (AKS) har CNCF-certifierats som Kubernetes-överensstämmande.

## <a name="regulatory-compliance"></a>Regelefterlevnad

Azure Kubernetes Service (AKS) är kompatibel med SOC, ISO, PCI DSS och HIPAA. Mer information finns i [översikt över Microsoft Azure-efterlevnad][compliance-doc].

## <a name="next-steps"></a>Nästa steg

Läs mer om att distribuera och hantera AKS med snabbstarten om Azure CLI.

> [!div class="nextstepaction"]
> [AKS-snabbstart][aks-cli]

<!-- LINKS - external -->
[aks-engine]: https://github.com/Azure/aks-engine
[kubectl-overview]: https://kubernetes.io/docs/user-guide/kubectl-overview/
[compliance-doc]: https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942

<!-- LINKS - internal -->
[acr-docs]: ../container-registry/container-registry-intro.md
[aks-aad]: ./aad-integration.md
[aks-cli]: ./kubernetes-walkthrough.md
[aks-gpu]: ./gpu-cluster.md
[aks-http-routing]: ./http-application-routing.md
[aks-networking]: ./concepts-network.md
[aks-portal]: ./kubernetes-walkthrough-portal.md
[aks-scale]: ./tutorial-kubernetes-scale.md
[aks-upgrade]: ./upgrade-cluster.md
[azure-dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/azure-dev-spaces
[azure-devops]: https://docs.microsoft.com/azure/devops-project/overview
[azure-disk]: ./azure-disks-dynamic-pv.md
[azure-files]: ./azure-files-dynamic-pv.md
[container-health]: ../monitoring/monitoring-container-health.md
[aks-master-logs]: view-master-logs.md
[aks-supported versions]: supported-kubernetes-versions.md
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[kubernetes-rbac]: concepts-identity.md#role-based-access-controls-rbac
[concepts-identity]: concepts-identity.md
[concepts-storage]: concepts-storage.md
