---
title: Så här aktiverar du Azure Monitor för behållare | Microsoft Docs
description: I den här artikeln beskrivs hur du aktiverar och konfigurerar Azure Monitor för behållare så att du kan förstå hur din behållare presterar och vilka prestandarelaterade problem som har identifierats.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 07/12/2019
ms.openlocfilehash: 44cdc2d6b93ac9a62f96875ca6c679fbb97d85a9
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555395"
---
# <a name="how-to-enable-azure-monitor-for-containers"></a>Aktivera Azure Monitor för behållare

Den här artikeln innehåller en översikt över tillgängliga alternativ för att konfigurera Azure Monitor för behållare för att övervaka prestanda för arbets belastningar som distribueras till Kubernetes-miljöer och som finns i [Azure Kubernetes-tjänsten](https://docs.microsoft.com/azure/aks/).

Azure Monitor för behållare kan aktive ras för nya eller en eller flera befintliga distributioner av AKS med hjälp av följande metoder:

* Från Azure Portal, Azure PowerShell eller med Azure CLI
* Använda [terraform och AKS](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Krav
Kontrol lera att du har följande innan du börjar:

* **En Log Analytics-arbetsyta.**

    Azure Monitor for containers stöder en Log Analytics arbets yta i de regioner som anges i Azure- [produkter efter region](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor).

    Du kan skapa en arbets yta när du aktiverar övervakning av ditt nya AKS-kluster eller låta onboarding-upplevelsen skapa en standard arbets yta i standard resurs gruppen för AKS-kluster prenumerationen. Om du väljer att skapa den själv kan du skapa den via [Azure Resource Manager](../platform/template-workspace-configuration.md), via [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)eller i [Azure Portal](../learn/quick-create-workspace.md). En lista över de mappnings par som stöds för standard arbets ytan finns i [region mappning för Azure Monitor för behållare](container-insights-region-mapping.md).

* Du är medlem i **rollen Log Analytics Contributor** för att aktivera övervakning av behållare. Mer information om hur du styr åtkomsten till en Log Analytics arbets yta finns i [hantera arbets ytor](../platform/manage-access.md).

* Du är medlem i **[ägar](../../role-based-access-control/built-in-roles.md#owner)** rollen på AKS-klusterresursen.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

* Prometheus mått samlas inte in som standard. Innan du [konfigurerar agenten](container-insights-agent-config.md) för att samla in dem är det viktigt att du läser Prometheus- [dokumentationen](https://prometheus.io/) för att förstå vad du kan definiera.

## <a name="components"></a>Komponenter

Din möjlighet att övervaka prestanda är beroende av en container Log Analytics-agent för Linux som är särskilt utvecklad för Azure Monitor för behållare. Den här specialiserade agenten samlar in prestanda-och händelse data från alla noder i klustret och agenten distribueras automatiskt och registreras med den angivna Log Analytics arbets ytan under distributionen. Agent versionen är Microsoft/OMS: ciprod04202018 eller senare, och representeras av ett datum i följande format: *mmddyyyy*.

>[!NOTE]
>I för hands versionen av Windows Server-stöd för AKS har ett AKS-kluster med Windows Server-noder inte någon agent installerad för att samla in data och vidarebefordra till Azure Monitor. I stället distribueras en Linux-nod automatiskt i klustret som en del av standard distributionen för att samla in och vidarebefordra data till Azure Monitor å alla Windows-noder i klustret.  
>

När en ny version av agenten släpps, uppgraderas den automatiskt i hanterade Kubernetes-kluster som finns i Azure Kubernetes service (AKS). Information om hur du följer de versioner som lanseras finns i [agent release-meddelanden](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).

>[!NOTE]
>Om du redan har distribuerat ett AKS-kluster aktiverar du övervakning med hjälp av antingen Azure CLI eller en angiven Azure Resource Manager mall, som det visas längre fram i den här artikeln. Du kan inte använda `kubectl` för att uppgradera, ta bort, distribuera om eller distribuera agenten.
>Mallen måste distribueras i samma resurs grupp som klustret.

Du aktiverar Azure Monitor för behållare genom att använda någon av följande metoder som beskrivs i följande tabell.

| Distributions tillstånd | Metod | Beskrivning |
|------------------|--------|-------------|
| Nytt AKS-kluster | [Skapa kluster med Azure CLI](../../aks/kubernetes-walkthrough.md#create-aks-cluster)| Du kan aktivera övervakning av ett nytt AKS-kluster som du skapar med Azure CLI. |
| | [Skapa kluster med terraform](container-insights-enable-new-cluster.md#enable-using-terraform)| Du kan aktivera övervakning av ett nytt AKS-kluster som du skapar med hjälp av terraform med öppen källkod. |
| Befintligt AKS-kluster | [Aktivera med hjälp av Azure CLI](container-insights-enable-existing-clusters.md#enable-using-azure-cli) | Du kan aktivera övervakning av ett AKS-kluster som redan har distribuerats med Azure CLI. |
| |[Aktivera med terraform](container-insights-enable-existing-clusters.md#enable-using-terraform) | Du kan aktivera övervakning av ett AKS-kluster som redan har distribuerats med hjälp av terraform med öppen källkod. |
| | [Aktivera från Azure Monitor](container-insights-enable-existing-clusters.md#enable-from-azure-monitor-in-the-portal)| Du kan aktivera övervakning av ett eller flera AKS-kluster som redan har distribuerats från sidan AKS flera kluster i Azure Monitor. |
| | [Aktivera från AKS-kluster](container-insights-enable-existing-clusters.md#enable-directly-from-aks-cluster-in-the-portal)| Du kan aktivera övervakning direkt från ett AKS-kluster i Azure Portal. |
| | [Aktivera med hjälp av en Azure Resource Manager mall](container-insights-enable-existing-clusters.md#enable-using-an-azure-resource-manager-template)| Du kan aktivera övervakning av ett AKS-kluster med en förkonfigurerad Azure Resource Manager-mall. |

## <a name="next-steps"></a>Nästa steg

* När övervakning har Aktiver ATS för att fånga hälso mått för både AKS-klusternoder och poddar är dessa hälso mått tillgängliga i Azure Portal. Information om hur du använder Azure Monitor för behållare finns i [Visa Azure Kubernetes service Health](container-insights-analyze.md).
