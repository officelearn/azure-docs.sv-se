---
title: Hur du integrera Azure Monitor för behållare | Microsoft Docs
description: Den här artikeln beskrivs hur du publicerar och konfigurera Azure Monitor för behållare så att du kan förstå hur din behållare fungerar och vilka prestanda-relaterade problem har identifierats.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/25/2019
ms.author: magoedte
ms.openlocfilehash: 10b80a9749c5698195ac5d3493ac3b07fd6e24e1
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65073316"
---
# <a name="how-to-onboard-azure-monitor-for-containers"></a>Hur du integrera Azure Monitor för behållare  

Den här artikeln innehåller en översikt över de tillgängliga alternativ för att konfigurera Azure Monitor för behållare för att övervaka prestanda för arbetsbelastningar som distribueras till Kubernetes-miljöer och finns på [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/).

Azure Monitor för behållare kan aktiveras för nya eller stöd för en eller flera befintliga distributioner av AKS med hjälp av följande metoder:

* Från Azure-portalen, Azure PowerShell eller med Azure CLI
* Med hjälp av [Terraform och AKS](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Förutsättningar 
Innan du börjar bör du kontrollera att du har följande:

- **En Log Analytics-arbetsyta.** Du kan skapa den när du aktiverar övervakning av din nya AKS-klustret eller låta publiceringsupplevelsen skapa en standardarbetsyta i standardresursgruppen för prenumerationen för AKS-kluster. Om du väljer att skapa den själv, kan du skapa den via [Azure Resource Manager](../platform/template-workspace-configuration.md), via [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json), eller i den [Azure-portalen](../learn/quick-create-workspace.md).
- Du är medlem i den **Log Analytics deltagarrollen** att aktivera behållarövervakning. Läs mer om hur du styr åtkomst till en Log Analytics-arbetsyta, [hantera arbetsytor](../platform/manage-access.md).
- Du är medlem i den **[ägare](../../role-based-access-control/built-in-roles.md#owner)** -rollen på AKS-klusterresursen. 

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

## <a name="components"></a>Komponenter 

Du kan övervaka prestanda är beroende av en behållare Log Analytics-agenten för Linux som har utvecklats specifikt för Azure Monitor för behållare. Den här specialiserade agenten samlar in prestanda- och händelsedata från alla noder i klustret och agenten automatiskt har distribuerat och registrerat med den angivna Log Analytics-arbetsytan under distributionen. Agentversionen är microsoft / oms:ciprod04202018 eller senare, och representeras av ett datum i följande format: *mmddyyyy*. 

>[!NOTE]
>Med förhandsversionen av Windows Server-stöd för AKS, ett AKS-kluster med Windows Server-noder inte har någon agent installerad för att samla in data och vidarebefordra dem till Azure Monitor. I stället en Linux-nod som distribueras automatiskt i klustret som en del av standarddistributionen samlar in och vidarebefordrar data till Azure Monitor åt alla Windows-noder i klustret.  
>

När en ny version av agenten släpps, uppgraderas den automatiskt på dina hanterade Kubernetes-kluster som finns på Azure Kubernetes Service (AKS). Om du vill följa de versioner släpps, se [agenten lanseringsmeddelanden](https://github.com/microsoft/docker-provider/tree/ci_feature_prod). 

>[!NOTE] 
>Om du redan har distribuerat ett AKS-kluster kan aktivera du övervakning genom att använda Azure CLI eller en angiven Azure Resource Manager-mall, som visas längre fram i den här artikeln. Du kan inte använda `kubectl` om du vill uppgradera, ta bort, omdistribuera eller distribuera agenten. Mallen måste distribueras i samma resursgrupp som klustret.

Du kan aktivera Azure Monitor för behållare med någon av följande metoder som beskrivs i följande tabell.

| Distributionstillstånd | Metod | Beskrivning | 
|------------------|--------|-------------| 
| Ett AKS-kluster | [Skapa kluster med Azure CLI](../../aks/kubernetes-walkthrough.md#create-aks-cluster)| Du kan aktivera övervakning av ett nytt AKS-kluster som du skapar med Azure CLI. | 
| | [Skapa kluster med Terraform](container-insights-enable-new-cluster.md#enable-using-terraform)| Du kan aktivera övervakning av ett AKS-kluster som du skapar med verktyg för öppen källkod Terraform. | 
| Befintliga AKS-kluster | [Aktivera med hjälp av Azure CLI](container-insights-enable-existing-clusters.md#enable-using-azure-cli) | Du kan aktivera övervakning av ett AKS-kluster som redan har distribuerats med Azure CLI. | 
| |[Aktivera med hjälp av Terraform](container-insights-enable-existing-clusters.md#enable-using-terraform) | Du kan aktivera övervakning av ett AKS-kluster som redan har distribuerats med hjälp av verktyg för öppen källkod Terraform. | 
| | [Aktivera från Azure Monitor](container-insights-enable-existing-clusters.md#enable-from-azure-monitor-in-the-portal)| Du kan aktivera övervakning av ett eller flera AKS-kluster som redan har distribuerats från sidan AKS flera kluster i Azure Monitor. | 
| | [Aktivera från AKS-kluster](container-insights-enable-existing-clusters.md#enable-directly-from-aks-cluster-in-the-portal)| Du kan aktivera övervakning direkt från ett AKS-kluster i Azure-portalen. | 
| | [Aktivera med hjälp av en Azure Resource Manager-mall](container-insights-enable-existing-clusters.md#enable-using-an-azure-resource-manager-template)| Du kan aktivera övervakning av ett AKS-kluster med en förkonfigurerad Azure Resource Manager-mall. | 

## <a name="next-steps"></a>Nästa steg

* Med övervakning aktiverat för att avbilda hälsomått för både noder i AKS och poddar är dessa health-mått tillgängliga i Azure-portalen. Läs hur du använder Azure Monitor för behållare i [visa Azure Kubernetes Service health](container-insights-analyze.md).
