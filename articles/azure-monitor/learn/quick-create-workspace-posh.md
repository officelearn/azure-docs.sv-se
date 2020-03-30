---
title: Skapa en log analytics-arbetsyta med Azure PowerShell| Microsoft-dokument
description: Lär dig hur du skapar en Log Analytics-arbetsyta för att aktivera hanteringslösningar och datainsamling från dina moln- och lokala miljöer med Azure PowerShell.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2019
ms.openlocfilehash: a2765aaf36aa5f7e541e0ee7fb3178246d2cca5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659908"
---
# <a name="create-a-log-analytics-workspace-with-azure-powershell"></a>Skapa en log analytics-arbetsyta med Azure PowerShell

Azure PowerShell-modulen används för att skapa och hantera Azure-resurser från PowerShell-kommandoraden eller i skript. Den här snabbstarten visar hur du använder Azure PowerShell-modulen för att distribuera en Log Analytics-arbetsyta i Azure Monitor. En Log Analytics-arbetsyta är en unik miljö för Azure Monitor-loggdata. Varje arbetsyta har en egen datadatabas och konfiguration, och datakällor och lösningar är konfigurerade för att lagra sina data på en viss arbetsyta. Du behöver en Log Analytics-arbetsyta om du tänker samla in data från följande källor:

* Azure-resurser i din prenumeration  
* Lokala datorer som övervakas av System Center Operations Manager  
* Enhetssamlingar från Configuration Manager  
* Diagnostik- eller loggdata från Azure Storage  
 
Andra källor, till exempel virtuella Azure-datorer och virtuella Windows- eller Linux-datorer i din miljö, finns i följande avsnitt:

* [Samla in data från virtuella Azure-datorer](../learn/quick-collect-azurevm.md)
* [Samla in data från hybrid-Linux-dator](../learn/quick-collect-linux-computer.md)
* [Samla in data från hybrid windows-dator](quick-collect-windows-computer.md)

Om du inte har en Azure-prenumeration skapar du [ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här självstudien Azure PowerShell Az-modulen. Kör `Get-Module -ListAvailable Az` för att hitta versionen. Om du behöver uppgradera kan du läsa [Installera Azure PowerShell-modulen](/powershell/azure/install-az-ps). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.

## <a name="create-a-workspace"></a>Skapa en arbetsyta
Skapa en arbetsyta med [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment). I följande exempel skapas en arbetsyta på *eastus-platsen* med hjälp av en Resource Manager-mall från den lokala datorn. JSON-mallen är konfigurerad för att bara fråga dig om namnet på arbetsytan och anger ett standardvärde för de andra parametrar som troligen skulle användas som standardkonfiguration i din miljö. 

Information om regioner som stöds finns [i regioner Log Analytics är tillgängligt i](https://azure.microsoft.com/regions/services/) och söker efter Azure Monitor från fältet Sök efter en **produkt.** 

Följande parametrar anger ett standardvärde:

* plats - standard till Östra USA
* sku - standardvärden till den nya prisnivån per GB som släpptes i april 2018-prismodellen

>[!WARNING]
>Om du skapar eller konfigurerar en Log Analytics-arbetsyta i en prenumeration som har valt den nya prismodellen april 2018 är den enda giltiga log analytics-prisnivån **PerGB2018**. 
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
            "apiVersion": "2015-11-01-preview",
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

2. Redigera mallen för att uppfylla dina krav. Läs [mallreferensen för Microsoft.OperationalInsights/workspaces](https://docs.microsoft.com/azure/templates/microsoft.operationalinsights/workspaces) för att ta reda på vilka egenskaper och värden som stöds. 
3. Spara den här filen som **deploylaworkspacetemplate.json** i en lokal mapp.   
4. Nu är det dags att distribuera den här mallen. Använd följande kommandon från mappen som innehåller mallen. När du uppmanas att ange ett arbetsområdesnamn anger du ett namn som är globalt unikt för alla Azure-prenumerationer.

    ```powershell
        New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deploylaworkspacetemplate.json
    ```

Det kan ta några minuter att slutföra distributionen. När det är klart visas ett meddelande som liknar följande som innehåller resultatet:

![Exempel på resultat när distributionen är klar](media/quick-create-workspace-posh/template-output-01.png)

## <a name="next-steps"></a>Nästa steg
Nu när du har en arbetsyta tillgänglig kan du konfigurera insamling av övervakningstelemetri, köra loggsökningar för att analysera dessa data och lägga till en hanteringslösning för att tillhandahålla ytterligare data och analytiska insikter.  

* Information om hur du aktiverar datainsamling från Azure-resurser med Azure Diagnostics eller Azure-lagring finns [i Samla in Azure-tjänstloggar och mått för användning i Azure Monitor](../platform/collect-azure-metrics-logs.md).  
* Lägg till [System Center Operations Manager som en datakälla](../platform/om-agents.md) för att samla in data från agenter som rapporterar din Hanteringsgrupp i Operations Manager och lagra den på logganalysarbetsytan.  
* Anslut [Configuration Manager](../platform/collect-sccm.md) för att importera datorer som är medlemmar i samlingar i hierarkin.  
* Granska de [övervakningslösningar](../insights/solutions.md) som finns tillgängliga och hur du lägger till eller tar bort en lösning från arbetsytan.
