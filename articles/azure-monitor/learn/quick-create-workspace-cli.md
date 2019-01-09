---
title: Skapa en Log Analytics-arbetsyta med hjälp av Azure CLI | Microsoft Docs
description: Lär dig hur du skapar en Log Analytics-arbetsyta om du vill aktivera hantering av lösningar och datainsamling från ditt moln och lokala miljöer med Azure CLI.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/02/2018
ms.author: magoedte
ms.openlocfilehash: a8fc517ba233c16fbe98ed42498b272cf4422107
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54105703"
---
# <a name="create-a-log-analytics-workspace-with-azure-cli-20"></a>Skapa en Log Analytics-arbetsyta med Azure CLI 2.0

Azure CLI 2.0 används till att skapa och hantera Azure-resurser från kommandoraden eller i skript. Den här snabbstarten visar hur du använder Azure CLI 2.0 för att distribuera en Log Analytics-arbetsyta i Azure, vilket är en unik miljö med en egen databas, datakällor och lösningar.  Stegen som beskrivs i den här artikeln krävs om du avser att samla in data från följande källor:

* Azure-resurser i din prenumeration  
* Lokala datorer som övervakas av System Center Operations Manager  
* Enhetssamlingar från System Center Configuration Manager  
* Diagnostik- eller loggdata från Azure Storage  
 
Andra källor, till exempel virtuella Azure-datorer och Windows eller Linux-datorer i din miljö finns i följande avsnitt:

* [Samla in data från Azure-datorer](../../azure-monitor/learn/quick-collect-azurevm.md)
* [Samla in data från hybrid Linux-dator](../../azure-monitor/learn/quick-collect-linux-computer.md)
* [Samla in data från hybrid Windows-dator](quick-collect-windows-computer.md)

Om du inte har en Azure-prenumeration kan du skapa [ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0.30 eller senare under den här snabbstarten. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-a-workspace"></a>Skapa en arbetsyta
Skapa en arbetsyta med [az group deployment skapa](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create). I följande exempel skapas en arbetsyta med namnet *TestWorkspace* i resursgruppen *Lab* i den *eastus* plats med hjälp av en Resource Manager-mall från din lokala datorn. JSON-mallen har konfigurerats för att bara efterfråga du namnet på arbetsytan och anger ett standardvärde för de andra parametrarna som sannolikt skulle användas som en standardkonfiguration i din miljö. Eller du kan lagra mallen i Azure-lagringskonton för delad åtkomst i din organisation. Ytterligare information om hur du arbetar med mallar finns i [distribuera resurser med Resource Manager-mallar och Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Följande parametrar anger ett standardvärde:

* plats – standardvärdet är USA, östra
* SKU - som standard den nya Per GB prisnivån som introducerades i den prissättningsmodellen från April 2018

>[!WARNING]
>Skapar eller konfigurerar en Log Analytics-arbetsyta i en prenumeration som har valt att den nya prissättningsmodellen från April 2018, är det enda giltiga Log Analytics prisnivån **PerGB2018**. 
>

### <a name="create-and-deploy-template"></a>Skapa och distribuera mall

1. Kopiera och klistra in följande JSON-syntax i filen:

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String",
            "metadata": {
              "description": "Specifies the name of the workspace."
            }
        },
        "location": {
            "type": "String",
            "allowedValues": [
              "eastus",
              "westus"
            ],
            "defaultValue": "eastus",
            "metadata": {
              "description": "Specifies the location in which to create the workspace."
            }
        },
        "sku": {
            "type": "String",
            "allowedValues": [
              "Standalone",
              "PerNode",
              "PerGB2018"
            ],
            "defaultValue": "PerGB2018",
            "metadata": {
            "description": "Specifies the service tier of the workspace: Standalone, PerNode, Per-GB"
        }
          }
    },
    "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2017-03-15-preview",
            "location": "[parameters('location')]",
            "properties": {
                "sku": {
                    "Name": "[parameters('sku')]"
                },
                "features": {
                    "searchVersion": 1
                }
            }
          }
       ]
    }
    ```

2. Redigera mallen så att den uppfyller dina krav.  Granska [Microsoft.OperationalInsights/workspaces mall](https://docs.microsoft.com/azure/templates/microsoft.operationalinsights/workspaces) referens till att lära dig vilka egenskaper och värden som stöds. 
3. Spara filen som **deploylaworkspacetemplate.json** till en lokal mapp.   
4. Nu är det dags att distribuera den här mallen. Använd följande kommandon från mappen som innehåller mallen:

    ```azurecli
    azure group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deploylaworkspacetemplate.json
    ```

Det kan ta några minuter att slutföra distributionen. När den är klar kan du se ett meddelande som liknar följande som innehåller resultatet:

![Exempelresultat när distributionen är klar](media/quick-create-workspace-cli/template-output-01.png)

## <a name="next-steps"></a>Nästa steg
Nu när du har en arbetsyta som är tillgängliga kan du konfigurera insamling av övervakning av telemetri, köra loggsökningar för att analysera dessa data och lägga till en hanteringslösning för att ge ytterligare data och analytisk insikt.  

* Om du vill aktivera insamling av data från Azure-resurser med Azure Diagnostics eller Azure storage, se [samla in Azure-tjänsteloggar och mått för användning i Log Analytics](../../azure-monitor/platform/collect-azure-metrics-logs.md).  
* Lägg till [System Center Operations Manager som en datakälla](../../azure-monitor/platform/om-agents.md) att samla in data från agenter som rapporterar Operations Manager-hanteringsgrupp och lagra den i Log Analytics-arbetsytan.  
* Ansluta [Configuration Manager](../../azure-monitor/platform/collect-sccm.md) att importera datorer som är medlemmar i samlingar i hierarkin.  
* Granska den [hanteringslösningar](../../azure-monitor/insights/solutions.md) tillgängliga och hur du lägger till eller ta bort en lösning från din arbetsyta.
