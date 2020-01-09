---
title: Så här aktiverar du Azure Monitor för behållare | Microsoft Docs
description: I den här artikeln beskrivs hur du aktiverar och konfigurerar Azure Monitor för behållare så att du kan förstå hur din behållare presterar och vilka prestandarelaterade problem som har identifierats.
ms.topic: conceptual
ms.date: 11/18/2019
ms.openlocfilehash: fce2699c18f0fe426b85c165656100c097e69598
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75404326"
---
# <a name="how-to-enable-azure-monitor-for-containers"></a>Aktivera Azure Monitor för behållare

Den här artikeln innehåller en översikt över tillgängliga alternativ för att konfigurera Azure Monitor för behållare för att övervaka prestanda för arbets belastningar som distribueras till Kubernetes-miljöer och som finns på:

- [Azure Kubernetes service](https://docs.microsoft.com/azure/aks/) (AKS)

- AKS-motorn på [Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908) eller Kubernetes distribuerad lokalt.

- [Azure Red Hat OpenShift](../../openshift/intro-openshift.md)

Azure Monitor för behållare kan aktive ras för nya eller en eller flera befintliga distributioner av Kubernetes med hjälp av följande metoder:

- Från Azure Portal, Azure PowerShell eller med Azure CLI

- Med hjälp av [Terraform och AKS](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Krav

Innan du börjar bör du kontrollera att du har följande:

- **En Log Analytics-arbetsyta.**

    Azure Monitor for containers stöder en Log Analytics arbets yta i de regioner som anges i Azure- [produkter efter region](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor).

    Du kan skapa en arbets yta när du aktiverar övervakning av ditt nya AKS-kluster eller låta onboarding-upplevelsen skapa en standard arbets yta i standard resurs gruppen för AKS-kluster prenumerationen. Om du väljer att skapa den själv, kan du skapa den via [Azure Resource Manager](../platform/template-workspace-configuration.md), via [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json), eller i den [Azure-portalen](../learn/quick-create-workspace.md). En lista över de mappnings par som stöds för standard arbets ytan finns i [region mappning för Azure Monitor för behållare](container-insights-region-mapping.md).

- Du är medlem i **rollen Log Analytics Contributor** för att aktivera övervakning av behållare. Läs mer om hur du styr åtkomst till en Log Analytics-arbetsyta, [hantera arbetsytor](../platform/manage-access.md).

- Du är medlem i **[ägar](../../role-based-access-control/built-in-roles.md#owner)** rollen på AKS-klusterresursen.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

* Prometheus mått samlas inte in som standard. Innan du [konfigurerar agenten](container-insights-prometheus-integration.md) för att samla in dem är det viktigt att du läser Prometheus- [dokumentationen](https://prometheus.io/) för att förstå vad som kan vara inkasserat och vilka metoder som stöds.

## <a name="supported-configurations"></a>Konfigurationer som stöds

Följande stöds officiellt i Azure Monitor for containers.

- Miljöer: Azure Red Hat OpenShift, Kubernetes on-premises och AKS Engine på Azure och Azure Stack. Mer information finns i [AKS-motorn på Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908).
- Versioner av Kubernetes och support policy är samma som versioner av [AKS som stöds](../../aks/supported-kubernetes-versions.md). 

## <a name="network-firewall-requirements"></a>Krav på brandvägg

Informationen i följande tabell visar den konfigurations information för proxy och brand vägg som krävs för att agenten ska kunna kommunicera med Azure Monitor för behållare. All nätverks trafik från agenten är utgående till Azure Monitor.

|Agentresurs|Portar |
|--------------|------|
| *.ods.opinsights.azure.com | 443 |  
| *.oms.opinsights.azure.com | 443 | 
| *.blob.core.windows.net | 443 |
| dc.services.visualstudio.com | 443 |
| *.microsoftonline.com | 443 |
| *.monitoring.azure.com | 443 |
| login.microsoftonline.com | 443 |

Informationen i följande tabell visar konfigurations information för proxy och brand vägg för Azure Kina.

|Agentresurs|Portar |Beskrivning | 
|--------------|------|-------------|
| *. ods.opinsights.azure.cn | 443 | Datainhämtning |
| *. oms.opinsights.azure.cn | 443 | OMS-onboarding |
| *.blob.core.windows.net | 443 | Används för övervakning av utgående anslutning. |
| Microsoft.com | 80 | Används för nätverks anslutning. Detta krävs endast om agent avbildnings versionen är ciprod09262019 eller tidigare. |
| dc.services.visualstudio.com | 443 | För för agent-telemetri med Azures offentliga moln Application Insights. |

Informationen i följande tabell visar konfigurations information för proxy och brand vägg för Azure amerikanska myndigheter.

|Agentresurs|Portar |Beskrivning | 
|--------------|------|-------------|
| *.ods.opinsights.azure.us | 443 | Datainhämtning |
| *.oms.opinsights.azure.us | 443 | OMS-onboarding |
| *.blob.core.windows.net | 443 | Används för övervakning av utgående anslutning. |
| Microsoft.com | 80 | Används för nätverks anslutning. Detta krävs endast om agent avbildnings versionen är ciprod09262019 eller tidigare. |
| dc.services.visualstudio.com | 443 | För att kunna använda agenten i Azures offentliga moln Application Insights. |

## <a name="components"></a>Komponenter

Din möjlighet att övervaka prestanda är beroende av en container Log Analytics-agent för Linux som är särskilt utvecklad för Azure Monitor för behållare. Den här specialiserade agenten samlar in prestanda- och händelsedata från alla noder i klustret och agenten automatiskt har distribuerat och registrerat med den angivna Log Analytics-arbetsytan under distributionen. Agentversionen är microsoft / oms:ciprod04202018 eller senare, och representeras av ett datum i följande format: *mmddyyyy*.

>[!NOTE]
>I för hands versionen av Windows Server-stöd för AKS har ett AKS-kluster med Windows Server-noder inte någon agent installerad för att samla in data och vidarebefordra till Azure Monitor. I stället distribueras en Linux-nod automatiskt i klustret som en del av standard distributionen för att samla in och vidarebefordra data till Azure Monitor å alla Windows-noder i klustret.  
>

När en ny version av agenten släpps, uppgraderas den automatiskt på dina hanterade Kubernetes-kluster som finns på Azure Kubernetes Service (AKS). Om du vill följa de versioner släpps, se [agenten lanseringsmeddelanden](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).

>[!NOTE]
>Om du redan har distribuerat ett AKS-kluster kan aktivera du övervakning genom att använda Azure CLI eller en angiven Azure Resource Manager-mall, som visas längre fram i den här artikeln. Du kan inte använda `kubectl` om du vill uppgradera, ta bort, omdistribuera eller distribuera agenten.
>Mallen måste distribueras i samma resursgrupp som klustret.

Du aktiverar Azure Monitor för behållare genom att använda någon av följande metoder som beskrivs i följande tabell.

| Distributions tillstånd | Metod | Beskrivning |
|------------------|--------|-------------|
| Nytt Kubernetes-kluster | [Skapa AKS-kluster med Azure CLI](../../aks/kubernetes-walkthrough.md#create-aks-cluster)| Du kan aktivera övervakning av ett nytt AKS-kluster som du skapar med Azure CLI. |
| | [Skapa AKS-kluster med terraform](container-insights-enable-new-cluster.md#enable-using-terraform)| Du kan aktivera övervakning av ett nytt AKS-kluster som du skapar med hjälp av terraform med öppen källkod. |
| | [Skapa ett OpenShift-kluster med en Azure Resource Manager mall](container-insights-azure-redhat-setup.md#enable-for-a-new-cluster-using-an-azure-resource-manager-template) | Du kan aktivera övervakning av ett nytt OpenShift-kluster som du skapar med en förkonfigurerad Azure Resource Manager-mall. |
| Befintligt Kubernetes-kluster | [Aktivera för AKS-kluster med Azure CLI](container-insights-enable-existing-clusters.md#enable-using-azure-cli) | Du kan aktivera övervakning av ett AKS-kluster som redan har distribuerats med Azure CLI. |
| |[Aktivera för AKS-kluster med terraform](container-insights-enable-existing-clusters.md#enable-using-terraform) | Du kan aktivera övervakning av ett AKS-kluster som redan har distribuerats med hjälp av terraform med öppen källkod. |
| | [Aktivera för AKS-kluster från Azure Monitor](container-insights-enable-existing-clusters.md#enable-from-azure-monitor-in-the-portal)| Du kan aktivera övervakning av ett eller flera AKS-kluster som redan har distribuerats från sidan med flera kluster i Azure Monitor. |
| | [Aktivera från AKS-kluster](container-insights-enable-existing-clusters.md#enable-directly-from-aks-cluster-in-the-portal)| Du kan aktivera övervakning direkt från ett AKS-kluster i Azure Portal. |
| | [Aktivera för AKS-kluster med hjälp av en Azure Resource Manager mall](container-insights-enable-existing-clusters.md#enable-using-an-azure-resource-manager-template)| Du kan aktivera övervakning av ett AKS-kluster med en förkonfigurerad Azure Resource Manager-mall. |
| | [Aktivera för Hybrid Kubernetes-kluster](container-insights-hybrid-setup.md) | Du kan aktivera övervakning av en AKS-motor som finns i Azure Stack eller för Kubernetes som finns lokalt. |
| | [Aktivera för OpenShift-kluster med en Azure Resource Manager mall](container-insights-azure-redhat-setup.md#enable-using-an-azure-resource-manager-template) | Du kan aktivera övervakning av ett befintligt OpenShift-kluster med en förkonfigurerad Azure Resource Manager-mall. |
| | [Aktivera för OpenShift-kluster från Azure Monitor](container-insights-azure-redhat-setup.md#from-the-azure-portal) | Du kan aktivera övervakning av ett eller flera OpenShift-kluster som redan har distribuerats från sidan med flera kluster i Azure Monitor. |

## <a name="next-steps"></a>Nästa steg

- När övervakning är aktiverat kan du börja analysera prestanda för dina Kubernetes-kluster som finns i Azure Kubernetes service (AKS), Azure Stack eller annan miljö. Information om hur du använder Azure Monitor för behållare finns i [Visa Kubernetes kluster prestanda](container-insights-analyze.md).
