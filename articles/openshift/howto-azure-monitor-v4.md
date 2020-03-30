---
title: Azure Monitor-integrering för Azure Red Hat OpenShift 4.3
description: Lär dig hur du aktiverar Azure Monitor i ditt Microsoft Azure Red Hat OpenShift-kluster.
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/06/2020
ms.openlocfilehash: a784fc070400995c56d16a3bc264d589bcb1f64e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79082854"
---
# <a name="azure-monitor-integration-for-azure-red-hat-openshift-43"></a>Azure Monitor-integrering för Azure Red Hat OpenShift 4.3

> [!IMPORTANT] 
> Observera att Azure Red Hat OpenShift 4.3 för närvarande endast är tillgängligt i privat förhandsversion i östra USA. Accepterar privat förhandsgranskning sker endast genom inbjudan. Var noga med att registrera din prenumeration innan du försöker aktivera den här funktionen: [Azure Red Hat OpenShift Private Preview Registration](https://aka.ms/aro-preview-register)

> [!NOTE]
> Förhandsversionsfunktioner är självbetjäning och tillhandahålls i dess fall och är undantagna från servicenivåavtalet (SLA) och begränsad garanti. Därför är funktionerna inte avsedda för produktionsanvändning.

I den här artikeln beskrivs hur du aktiverar den privata förhandsversionen av Azure Monitor för behållare för OpenShift 4.3-kluster som finns på prem eller i en molnmiljö. Samma instruktioner gäller även för att aktivera övervakning för Azure Red Hat OpenShift (ARO) 4.3-kluster.  

## <a name="prerequisites"></a>Krav

- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Helm 3 (3)](https://helm.sh/docs/intro/install/)
- Tillgång till kubeconfig för kubernetes-klustret
- Åtkomst till en Azure-prenumeration
- Tillgång till OpenShift 4.3-klustret för att installera Azure Monitor for Containers Helm-diagrammet
- Lägsta behörighet för RBAC-rollen för deltagare för Azure-prenumerationen  
- Övervakningsagent kräver följande utgående portar - och domäner för att skicka övervakningsdata till Azure Monitor serverdel (Om blockeras av proxy / brandvägg):
  - *.ods.opinsights.azure.com 443
  - *.oms.opinsights.azure.com 443
  - *.blob.core.windows.net 443
  - dc.services.visualstudio.com 443

## <a name="onboarding"></a>Publicering

> [!TIP]
> Skriptet använder bash 4 funktioner, så se till att din bash är uppdaterad. Du kan kontrollera din `bash --version`nuvarande version med .

### <a name="download-the-onboarding-script"></a>Ladda ner introduktionsskriptet

```bash
curl -LO  https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/openshiftV4/onboarding_azuremonitor_for_containers.sh
```

Kör följande skript med azureSubscriptionId, workspace Region, clusterName och kontext för Kubernetes-klustret.

```bash
bash onboarding_azuremonitor_for_containers.sh <azureSubscriptionId> <azureRegionforLogAnalyticsWorkspace> <clusterName> <kubeconfigContextNameOftheCluster>
```

Ett exempel:

```bash
 bash onboarding_azuremonitor_for_containers.sh 27ac26cf-a9f0-4908-b300-9a4e9a0fb205 eastus myocp42 admin 
```

## <a name="configure-agent-data-collection"></a>Konfigurera insamling av agentdata

Som standard samlar övervakningsagenten in behållarloggarna {stdout; stderr} för alla behållare som körs i alla namnområden utom kube-system.  Om du vill konfigurera behållareloggsamlingen som är specifik för vissa namnområden eller namnområden kan du referera till [Container Insights-agentkonfigurationen](../azure-monitor/insights/container-insights-agent-config.md). Här kan du konfigurera övervakningsagent med önskade inställningar för datainsamling med hjälp av konfigurationskartan.

## <a name="configure-scraping-of-prometheus-metrics"></a>Konfigurera skrapning av Prometheus-mått

Azure Monitor för behållare skrapar Prometheus-måtten och matar in på Azure Monitor-backend. Se [Container Insights Prometheus konfiguration](../azure-monitor/insights/container-insights-prometheus-integration.md) för instruktioner hur man konfigurerar Prometheus skrapning.

När du har lyckats med introduktionen navigerar du till [Hybridövervakning](https://aka.ms/azmon-containers-hybrid) och väljer Miljö som **"Alla"** för att visa ditt nyligen inbyggda OpenShift v4-kluster.

## <a name="disable-monitoring"></a>Inaktivera övervakning

Om du vill inaktivera övervakning kan du ta bort Azure Monitor for Containers Helm-diagrammet med följande kommando för att sluta samla in och inta övervakningsdata till Azure Monitor för behållares backend.

``` bash
helm del azmon-containers-release-1
```

## <a name="update-monitoring"></a>Uppdatera övervakning

Kör introduktionsskriptet igen enligt beskrivningen i avsnittet [Onboarding](#onboarding) med samma parameter för att uppdatera till det senaste Helm-diagrammet.

## <a name="after-successful-onboarding"></a>Efter lyckad introduktion

Navigera till [hybridövervakning](https://aka.ms/azmon-containers-hybrid)och du kan se ditt nyligen aktiverade OpenShift/ARO v4-kluster med hälsostatus på fliken **Övervakade kluster.** Där kan du komma in i djupare insikter som mått, lager och loggar genom att klicka på **kolumnen Kluster.**

## <a name="supported-features"></a>Funktioner som stöds

Mer information om funktioner och funktioner som stöds finns i [översikt över Behållarstatistik](../azure-monitor/insights/container-insights-overview.md).

Kontakta oss askcoin@microsoft.com via för feedback och frågor.

## <a name="next-steps"></a>Nästa steg

Mer information om övervakning finns i:
- [Översikt över behållareinsikter](../azure-monitor/insights/container-insights-overview.md)

- [Översikt över loggfråga](../azure-monitor/log-query/log-query-overview.md)
