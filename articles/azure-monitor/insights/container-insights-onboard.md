---
title: Aktivera Azure Monitor för behållare | Microsoft-dokument
description: I den här artikeln beskrivs hur du aktiverar och konfigurerar Azure Monitor för behållare så att du kan förstå hur din behållare fungerar och vilka prestandarelaterade problem som har identifierats.
ms.topic: conceptual
ms.date: 11/18/2019
ms.openlocfilehash: 7aad7e7dd5ec2569377f9276c2e4793c7afd631a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275313"
---
# <a name="how-to-enable-azure-monitor-for-containers"></a>Aktivera Azure Monitor för behållare

Den här artikeln innehåller en översikt över de alternativ som är tillgängliga för att konfigurera Azure Monitor för behållare för att övervaka prestanda för arbetsbelastningar som distribueras till Kubernetes-miljöer och som finns på:

- [Azure Kubernetes-tjänsten](https://docs.microsoft.com/azure/aks/) (AKS)

- Självhanterade Kubernetes-kluster som finns på Azure med [AKS Engine](https://github.com/Azure/aks-engine).

- Självhanterade Kubernetes-kluster som finns på [Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1910) eller lokalt med AKS Engine.

- [Azure Red Hat OpenShift](../../openshift/intro-openshift.md)

Azure Monitor för behållare kan aktiveras för nya eller en eller flera befintliga distributioner av Kubernetes med hjälp av följande metoder som stöds:

- Från Azure-portalen, Azure PowerShell eller med Azure CLI

- Använda [Terraform och AKS](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Krav

Innan du börjar kontrollerar du att du har följande:

- **En Log Analytics-arbetsyta.**

    Azure Monitor för behållare stöder en Log Analytics-arbetsyta i de regioner som anges i [Azure-produkter efter region](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor).

    Du kan skapa en arbetsyta när du aktiverar övervakning av ditt nya AKS-kluster eller låter introduktionsupplevelsen skapa en standardarbetsyta i standardresursgruppen för AKS-klusterprenumerationen. Om du väljer att skapa den själv kan du skapa den via [Azure Resource Manager,](../platform/template-workspace-configuration.md)via [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)eller i [Azure-portalen](../learn/quick-create-workspace.md). En lista över de mappningspar som stöds som används för standardarbetsytan finns i [Regionmappning för Azure Monitor för behållare](container-insights-region-mapping.md).

- Du är medlem i **rollen Log Analytics-deltagare** för att aktivera behållarövervakning. Mer information om hur du styr åtkomsten till en Log Analytics-arbetsyta finns i [Hantera arbetsytor](../platform/manage-access.md).

- Du är medlem i **[rollen Ägare](../../role-based-access-control/built-in-roles.md#owner)** på AKS-klusterresursen.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

* Prometheus-mätvärden samlas inte in som standard. Innan [du konfigurerar agenten](container-insights-prometheus-integration.md) för att samla in dem, är det viktigt att du granskar Prometheus-dokumentationen för att förstå vad som kan skrapas och metoder som stöds. [documentation](https://prometheus.io/)

## <a name="supported-configurations"></a>Konfigurationer som stöds

Följande stöds officiellt med Azure Monitor för behållare.

- Miljöer: Azure Red Hat OpenShift, Kubernetes lokalt och AKS-motor på Azure och Azure Stack. Mer information finns i [AKS Engine på Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908).
- Versioner av Kubernetes och supportprincipen är desamma som versioner av [AKS som stöds](../../aks/supported-kubernetes-versions.md). 

## <a name="network-firewall-requirements"></a>Krav på nätverksbrandvägg

Informationen i följande tabell visar den proxy- och brandväggskonfigurationsinformation som krävs för att den behållaren för att kommunicera med Azure Monitor för behållare. All nätverkstrafik från agenten är utgående till Azure Monitor.

|Agentresurs|Portar |
|--------------|------|
| *.ods.opinsights.azure.com | 443 |  
| *.oms.opinsights.azure.com | 443 | 
| *.blob.core.windows.net | 443 |
| dc.services.visualstudio.com | 443 |
| *.microsoftonline.com | 443 |
| *.monitoring.azure.com | 443 |
| login.microsoftonline.com | 443 |

Informationen i följande tabell visar proxy- och brandväggskonfigurationsinformationen för Azure China.

|Agentresurs|Portar |Beskrivning | 
|--------------|------|-------------|
| *.ods.opinsights.azure.cn | 443 | Datainhämtning |
| *.oms.opinsights.azure.cn | 443 | OMS ombord |
| *.blob.core.windows.net | 443 | Används för att övervaka utgående anslutning. |
| microsoft.com | 80 | Används för nätverksanslutning. Detta krävs endast om agentbildversionen är ciprod09262019 eller tidigare. |
| dc.services.visualstudio.com | 443 | För agenttelemetri med Azure Public Cloud Application Insights. |

Informationen i följande tabell visar proxy- och brandväggskonfigurationsinformationen för Azure US Government.

|Agentresurs|Portar |Beskrivning | 
|--------------|------|-------------|
| *.ods.opinsights.azure.us | 443 | Datainhämtning |
| *.oms.opinsights.azure.us | 443 | OMS ombord |
| *.blob.core.windows.net | 443 | Används för att övervaka utgående anslutning. |
| microsoft.com | 80 | Används för nätverksanslutning. Detta krävs endast om agentbildversionen är ciprod09262019 eller tidigare. |
| dc.services.visualstudio.com | 443 | För agenttelemetri med Azure Public Cloud Application Insights. |

## <a name="components"></a>Komponenter

Din förmåga att övervaka prestanda är beroende av en containeriserad Log Analytics-agent för Linux som är särskilt utvecklad för Azure Monitor för behållare. Den här specialiserade agenten samlar in prestanda- och händelsedata från alla noder i klustret och agenten distribueras automatiskt och registreras med den angivna Log Analytics-arbetsytan under distributionen. Agentversionen är microsoft/oms:ciprod04202018 eller senare, och representeras av ett datum i följande format: *mmddyyyy*.

>[!NOTE]
>Med förhandsversionen av Windows Server-stöd för AKS har ett AKS-kluster med Windows Server-noder inte en agent installerad för att samla in data och vidarebefordra till Azure Monitor. I stället samlar en Linux-nod automatiskt i klustret som en del av standarddistributionen in och vidarebefordrar data till Azure Monitor för alla Windows-noder i klustret.  
>

När en ny version av agenten släpps uppgraderas den automatiskt på dina hanterade Kubernetes-kluster som finns på Azure Kubernetes Service (AKS). Information om hur du följer de versioner som har släppts finns i [meddelanden om agentrelease](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).

>[!NOTE]
>Om du redan har distribuerat ett AKS-kluster aktiverar du övervakning med hjälp av antingen Azure CLI eller en medföljande Azure Resource Manager-mall, vilket visas senare i den här artikeln. Du kan `kubectl` inte använda för att uppgradera, ta bort, distribuera om eller distribuera agenten.
>Mallen måste distribueras i samma resursgrupp som klustret.

Du aktiverar Azure Monitor för behållare med någon av följande metoder som beskrivs i följande tabell.

| Driftsättningstillstånd | Metod | Beskrivning |
|------------------|--------|-------------|
| Nytt AKS Kubernetes-kluster | [Skapa AKS-kluster med Azure CLI](../../aks/kubernetes-walkthrough.md#create-aks-cluster)| Du kan aktivera övervakning av ett nytt AKS-kluster som du skapar med Azure CLI. |
| | [Skapa AKS-kluster med Terraform](container-insights-enable-new-cluster.md#enable-using-terraform)| Du kan aktivera övervakning av ett nytt AKS-kluster som du skapar med terraform med öppen källkod. |
| | [Skapa OpenShift-kluster med hjälp av en Azure Resource Manager-mall](container-insights-azure-redhat-setup.md#enable-for-a-new-cluster-using-an-azure-resource-manager-template) | Du kan aktivera övervakning av ett nytt OpenShift-kluster som du skapar med en förkonfigurerad Azure Resource Manager-mall. |
| | [Skapa OpenShift-kluster med Azure CLI](https://docs.microsoft.com/cli/azure/openshift?view=azure-cli-latest#az-openshift-create) | Du kan aktivera övervakning när du distribuerar ett nytt OpenShift-kluster med Azure CLI. |
| Befintligt AKS Kubernetes-kluster | [Aktivera för AKS-kluster med Azure CLI](container-insights-enable-existing-clusters.md#enable-using-azure-cli) | Du kan aktivera övervakning av ett AKS-kluster som redan har distribuerats med Azure CLI. |
| |[Aktivera för AKS-kluster med Terraform](container-insights-enable-existing-clusters.md#enable-using-terraform) | Du kan aktivera övervakning av ett AKS-kluster som redan har distribuerats med terraform med öppen källkod. |
| | [Aktivera för AKS-kluster från Azure Monitor](container-insights-enable-existing-clusters.md#enable-from-azure-monitor-in-the-portal)| Du kan aktivera övervakning av ett eller flera AKS-kluster som redan har distribuerats från multiklustersidan i Azure Monitor. |
| | [Aktivera från AKS-kluster](container-insights-enable-existing-clusters.md#enable-directly-from-aks-cluster-in-the-portal)| Du kan aktivera övervakning direkt från ett AKS-kluster i Azure-portalen. |
| | [Aktivera för AKS-kluster med hjälp av en Azure Resource Manager-mall](container-insights-enable-existing-clusters.md#enable-using-an-azure-resource-manager-template)| Du kan aktivera övervakning av ett AKS-kluster med en förkonfigurerad Azure Resource Manager-mall. |
| | [Aktivera för hybridkuberneteskluster](container-insights-hybrid-setup.md) | Du kan aktivera övervakning av en AKS-motor som finns i Azure Stack eller för Kubernetes som finns lokalt. |
| | [Aktivera för OpenShift-kluster med hjälp av en Azure Resource Manager-mall](container-insights-azure-redhat-setup.md#enable-using-an-azure-resource-manager-template) | Du kan aktivera övervakning av ett befintligt OpenShift-kluster med en förkonfigurerad Azure Resource Manager-mall. |
| | [Aktivera för OpenShift-kluster från Azure Monitor](container-insights-azure-redhat-setup.md#from-the-azure-portal) | Du kan aktivera övervakning av ett eller flera OpenShift-kluster som redan har distribuerats från multiklustersidan i Azure Monitor. |

## <a name="next-steps"></a>Nästa steg

- Med övervakning aktiverat kan du börja analysera prestanda för dina Kubernetes-kluster som finns på Azure Kubernetes Service (AKS), Azure Stack eller annan miljö. Mer information om hur du använder Azure Monitor för behållare finns i [Visa Kubernetes klusterprestanda](container-insights-analyze.md).
