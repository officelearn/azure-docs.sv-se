---
title: Azure Monitor-integrering för Azure Red Hat OpenShift 4,3
description: Lär dig hur du aktiverar Azure Monitor på ditt Microsoft Azure Red Hat OpenShift-kluster.
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/06/2020
ms.openlocfilehash: a784fc070400995c56d16a3bc264d589bcb1f64e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "79082854"
---
# <a name="azure-monitor-integration-for-azure-red-hat-openshift-43"></a>Azure Monitor-integrering för Azure Red Hat OpenShift 4,3

> [!IMPORTANT] 
> Observera att Azure Red Hat OpenShift 4,3 är för närvarande endast tillgängligt i privat för hands version i USA, östra. Godkännande av privat för hands version är endast efter inbjudan. Var noga med att registrera din prenumeration innan du försöker aktivera den här funktionen: [Azure Red Hat OpenShift, privat för hands registrering](https://aka.ms/aro-preview-register)

> [!NOTE]
> För hands versions funktionerna är självbetjäning och tillhandahålls och är tillgängliga och omfattas inte av service nivå avtalet (SLA) och begränsad garanti. Därför är funktionerna inte avsedda att användas för produktion.

Den här artikeln beskriver hur du aktiverar den privata förhands granskningen av Azure Monitor för behållare för OpenShift 4,3-kluster som finns på lokal eller i valfri moln miljö. Samma instruktioner gäller även för att aktivera övervakning av Azure Red Hat OpenShift (ARO) 4,3-kluster.  

## <a name="prerequisites"></a>Krav

- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Helm 3](https://helm.sh/docs/intro/install/)
- Åtkomst till kubeconfig för Kubernetes-klustret
- Åtkomst till en Azure-prenumeration
- Åtkomst till OpenShift 4,3-klustret för att installera Azure Monitor for containers Helm-diagram
- Minsta deltagar behörighet för RBAC-rollen för Azure-prenumerationen  
- Övervaknings agenten kräver följande utgående portar – och domäner för att skicka övervaknings data till Azure Monitor Server del (om de blockeras av proxy/brand vägg):
  - *. ods.opinsights.azure.com 443
  - *. oms.opinsights.azure.com 443
  - *. blob.core.windows.net 443
  - dc.services.visualstudio.com 443

## <a name="onboarding"></a>Onboarding

> [!TIP]
> Skriptet använder bash 4-funktioner, så se till att din bash är uppdaterad. Du kan kontrol lera den aktuella versionen `bash --version`med.

### <a name="download-the-onboarding-script"></a>Hämta onboarding-skriptet

```bash
curl -LO  https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/openshiftV4/onboarding_azuremonitor_for_containers.sh
```

Kör följande skript med azureSubscriptionId, arbets ytans region, kluster namn och kontext för Kubernetes-klustret.

```bash
bash onboarding_azuremonitor_for_containers.sh <azureSubscriptionId> <azureRegionforLogAnalyticsWorkspace> <clusterName> <kubeconfigContextNameOftheCluster>
```

Ett exempel:

```bash
 bash onboarding_azuremonitor_for_containers.sh 27ac26cf-a9f0-4908-b300-9a4e9a0fb205 eastus myocp42 admin 
```

## <a name="configure-agent-data-collection"></a>Konfigurera agent data insamling

Som standard samlar övervaknings agenten till {stdout; stderr}-behållar loggarna för alla behållare som körs i alla namn områden utom Kube-system.  Om du vill konfigurera behållar logg samlingen som är specifik för viss namnrymd eller namnrymder kan du referera till [agent konfigurationen för container Insights](../azure-monitor/insights/container-insights-agent-config.md). Här kan du konfigurera övervaknings agenten med önskade data insamlings inställningar med hjälp av config Map.

## <a name="configure-scraping-of-prometheus-metrics"></a>Konfigurera kasse ring av Prometheus-mått

Azure Monitor för behållare tar över Prometheus-mått och inmatningar till Azure Monitor-backend-servern. Information om hur du konfigurerar Prometheus-kassationer hittar du i [Prometheus-konfigurationen för container Insights](../azure-monitor/insights/container-insights-prometheus-integration.md) .

När registreringen är klar navigerar du till [hybrid övervakning](https://aka.ms/azmon-containers-hybrid) och väljer miljö som **"alla"** för att visa ditt nyregistrerade OpenShift v4-kluster.

## <a name="disable-monitoring"></a>Inaktivera övervakning

Om du vill inaktivera övervakning kan du ta bort Azure Monitor för containers Helm-diagram med hjälp av följande kommando för att sluta samla in och mata in övervaknings data till Azure Monitor för behållarens Server del.

``` bash
helm del azmon-containers-release-1
```

## <a name="update-monitoring"></a>Uppdaterings övervakning

Kör onboarding-skriptet på nytt enligt beskrivningen i avsnittet [onboarding](#onboarding) med samma parameter för att uppdatera till det senaste Helm-diagrammet.

## <a name="after-successful-onboarding"></a>Efter lyckad onboarding

Navigera till [hybrid övervakning](https://aka.ms/azmon-containers-hybrid)och du kan se ditt nyligen aktiverade OpenShift/Aro v4-kluster med hälso status på fliken **övervakade kluster** . Där kan du få djupare insikter, till exempel statistik, inventering och loggar genom att klicka på **kluster** kolumnen.

## <a name="supported-features"></a>Funktioner som stöds

Mer information om vilka funktioner och funktioner som stöds finns i [Översikt över container Insights](../azure-monitor/insights/container-insights-overview.md).

Kontakta oss via askcoin@microsoft.com för feedback och frågor.

## <a name="next-steps"></a>Nästa steg

Mer information om övervakning finns i:
- [Översikt över container Insights](../azure-monitor/insights/container-insights-overview.md)

- [Översikt över logg frågor](../azure-monitor/log-query/log-query-overview.md)
