---
title: Introduktion till Azure Kubernetes Service
description: Med Azure Kubernetes Service kan du enkelt distribuera och hantera behållarbaserade program i Azure.
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: overview
ms.date: 06/13/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: fdff52a1fa34fa2033db19690d20a1c2f417e646
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2018
ms.locfileid: "36307799"
---
# <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)

Azure Kubernetes Service (AKS) gör det enkelt att distribuera ett hanterat Kubernetes-kluster i Azure. AKS minskar komplexiteten och arbetet med att hantera Kubernetes genom att avlasta en stor del av det ansvaret till Azure. Som Kubernetes-värdtjänst hanterar Azure viktiga uppgifter som övervakning av hälsotillstånd och underhåll åt dig. Dessutom är tjänsten kostnadsfri, du betalar bara för agentnoder i dina kluster, inte för de överordnade noder.

Det här dokumentet innehåller en översikt över funktioner i Azure Kubernetes Service (AKS).

## <a name="flexible-deployment-options"></a>Flexibla distributionsalternativ

Azure Kubernetes Service erbjuder portal-, kommandorads- och malldrivna distributionsalternativ (Resource Manager-mallar och Terraform). När du distribuerar ett AKS-kluster styr Kubernetes och alla noder distribueras och konfigureras för dig. Ytterligare funktioner, till exempel avancerade nätverk, Azure Active Directory-integrering och övervakning kan också konfigureras under distributionsprocessen.

Mer information finns i både [Snabbstart för AKS portal][aks-portal] eller [Snabbstart för AKS CLI][aks-cli].

## <a name="identity-and-security-management"></a>Identitets- och säkerhetshantering

AKS-kluster har stöd för [rollbaserad åtkomstkontroll (RBAC)][kubernetes-rbac]. Ett AKS-kluster kan också konfigureras för att integrera med Azure Active Directory. I den här konfigurationen kan du konfigurera Kubernetes-åtkomst baserat på Azure Active Directory-identitet och gruppmedlemskap.

Mer information finns i, [integrera Azure Active Directory med AKS][aks-aad].

## <a name="integrated-logging-and-monitoring"></a>Integrerad loggning och övervakning

Hälsotillstånd för behållare ger prestandasynlighet för att samla in minnes- och processormått från behållare, noder och domänkontrollanter. Behållarloggar samlas också. Dessa data lagras i Log Analytics-arbetsytan och är tillgänglig via Azure Portal, Azure CLI eller en REST-slutpunkt.

Mer information finns i [Övervaka Azure Kubernetes Service hälsotillstånd för behållaren][container-health].

## <a name="cluster-node-scaling"></a>Klusternodsskalning

När efterfrågan av resurser ökar kan noder i ett AKS-kluster skalas ut så att de matchar. Om resursefterfrågan sjunker kan noder tas bort genom att skala ner klustret. AKS-skalningsåtgärder kan utföras med hjälp av Azure Portal eller Azure CLI.

Mer information finns i [Skala ett kluster i Azure Kubernetes Service (AKS)][aks-scale].

## <a name="cluster-node-upgrades"></a>Uppgraderingar av klusternod

Azure Kubernetes Service erbjuder flera Kubernetes-versioner. När nya versioner blir tillgängliga i AKS uppgraderas klustret med hjälp av Azure Portal eller Azure CLI. Under uppgraderingen blir noderna noggrant avspärrade och tömda för att minimera störningar i program som körs.

Mer information finns i [Uppgradera ett kluster i Azure Kubernetes Service (AKS)][aks-upgrade].

## <a name="http-application-routing"></a>Routning av HTTP-program

HTTP-programroutningen gör det enkelt att komma åt program som distribueras till AKS-klustret. När aktiverad, konfigurerar HTTP-programroutningen en ingångskontroll i AKS-klustret. Då program distribueras konfigureras offentligt tillgängliga DNS-namn automatiskt.

Mer information finns i [HTTP-programroutning][aks-http-routing].

## <a name="gpu-enabled-nodes"></a>GPU-aktiverade noder

AKS stöder skapandet av GPU-aktiverade nodpooler. Azure tillhandahåller för närvarande enskilda eller flera GPU-aktiverade virtuella datorer. GPU-aktiverade virtuella datorer är utformade för beräkningsintensiva, grafikintensiva och visualiseringar av arbetsbelastningar.

Mer information finns i [Använda GPU-kort på AKS][aks-gpu].

## <a name="development-tooling-integration"></a>Integrering av utvecklingsverktyg

Kubernetes har ett omfattande utbud av utvecklings- och hanteringsverktyg, till exempel Helm, Draft och Kubernetes-tillägget för Visual Studio-koden. Dessa verktyg fungerar sömlöst med Azure Kuberntees Service.

Vidare ger Azure Dev Spaces ger en snabb, iterativ Kubernetes-utvecklingsupplevelse för team. Du kan köra och felsöka behållare direkt i Azure Kubernetes Service (AKS) med minimal konfiguration.

Mer information finns i [Azure Dev Spaces][azure-dev-spaces].

Azure DevOps-projektet tillhandahåller en enkel lösning för att ta befintlig kod och Git-lagringsplats till Azure. DevOps-projektet skapar automatiskt Azure-resurser som AKS, en versionspipeline i VSTS som innehåller en definition av build för CI, ställer in en versionsdefinition för CD och skapar sedan en Azure Application Insights-resurs för övervakning.

Mer information finns i [Azure DevOps-projekt][azure-devops].

## <a name="virtual-network-integration"></a>Virtual Network-integration

Ett AKS-kluster kan distribueras till ett befintligt virtuellt nätverk. I den här konfigurationen tilldelas varje pod i klustret en IP-adress i det virtuella nätverket och kan kommunicera direkt med andra poddar i klustret och andra noder i det virtuella nätverket. Poddar kan också ansluta till andra tjänster i en peer-kopplad virtuell dator och lokala nätverk via ExpressRoute och plats-till-plats (S2S) VPN-anslutningar.

Mer information finns i [AKS-nätverk – översikt][aks-networking].

## <a name="private-container-registry"></a>Privat behållarregister

Integrera med Azure Container Registry (ACR) för privat lagring av Docker-avbildningar.

Mer information finns i [Azure Container Registry (ACR)][acr-docs].

## <a name="storage-volume-support"></a>Stöd för lagringsvolym

Azure Kubernetes Service (AKS) stöder montering av lagringsvolymer för beständig data. AKS-kluster skapas med stöd för Azure Files och Azure Disks.

Mer information finns i [Azure Files][azure-files] och [Azure Disks][azure-disk].

## <a name="docker-image-support"></a>Stöd för Docker image

Azure Kubernetes Service (AKS) stöder avbildningsformatet Docker.

## <a name="kubernetes-certification"></a>Kubernetes-certifiering

Azure Kubernetes Service (AKS) har CNCF-certifierats som Kubernetes-överensstämmande.

## <a name="regulatory-compliance"></a>Regelefterlevnad

Azure Kubernetes Service (AKS) är kompatibel med SOC och ISO/HIPAA/HITRUST.

## <a name="next-steps"></a>Nästa steg

Läs mer om att distribuera och hantera AKS med snabbstarten om AKS.

> [!div class="nextstepaction"]
> [AKS-snabbstart][aks-cli]

<!-- LINKS - external -->
[acs-engine]: https://github.com/Azure/acs-engine
[draft]: https://github.com/Azure/draft
[helm]: https://helm.sh/
[kubectl-overview]: https://kubernetes.io/docs/user-guide/kubectl-overview/
[kubernetes-rbac]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/

<!-- LINKS - internal -->
[acr-docs]: ../container-registry/container-registry-intro.md
[aks-aad]: ./aad-integration.md
[aks-cli]: ./kubernetes-walkthrough.md
[aks-gpu]: ./gpu-cluster.md
[aks-http-routing]: ./http-application-routing.md
[aks-networking]: ./networking-overview.md
[aks-portal]: ./kubernetes-walkthrough-portal.md
[aks-scale]: ./scale-cluster.md
[aks-upgrade]: ./upgrade-cluster.md
[azure-dev-spaces]: https://docs.microsoft.com/en-us/azure/dev-spaces/azure-dev-spaces
[azure-devops]: https://docs.microsoft.com/en-us/vsts/pipelines/actions/azure-devops-project-aks?view=vsts
[azure-disk]: ./azure-disks-dynamic-pv.md
[azure-files]: ./azure-files-dynamic-pv.md
[container-health]: ../monitoring/monitoring-container-health.md

