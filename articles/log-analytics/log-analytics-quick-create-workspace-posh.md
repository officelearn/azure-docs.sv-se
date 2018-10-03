---
title: Skapa en Log Analytics-arbetsyta med hjälp av Azure PowerShell | Microsoft Docs
description: Lär dig hur du skapar en Log Analytics-arbetsyta om du vill aktivera hantering av lösningar och datainsamling från ditt moln och lokala miljöer med Azure PowerShell.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptal
ms.date: 09/18/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: 4834e21b5e9f1cbdd931b59e2b693e45ddfd8876
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2018
ms.locfileid: "48043319"
---
# <a name="create-a-log-analytics-workspace-with-azure-powershell"></a>Skapa en Log Analytics-arbetsyta med Azure PowerShell

Azure PowerShell-modulen används för att skapa och hantera Azure-resurser från PowerShell-kommandoraden eller i skript. Den här snabbstarten visar hur du använder Azure PowerShell-modulen för att distribuera en Log Analytics-arbetsyta i Azure, vilket är en unik miljö med en egen databas, datakällor och lösningar.  Stegen som beskrivs i den här artikeln krävs om du avser att samla in data från följande källor:

* Azure-resurser i din prenumeration  
* Lokala datorer som övervakas av System Center Operations Manager  
* Enhetssamlingar från System Center Configuration Manager  
* Diagnostik- eller loggdata från Azure Storage  
 
Andra källor, till exempel virtuella Azure-datorer och Windows eller Linux-datorer i din miljö finns i följande avsnitt:

* [Samla in data från Azure-datorer](log-analytics-quick-collect-azurevm.md)
* [Samla in data från hybrid Linux-dator](log-analytics-quick-collect-linux-computer.md)
* [Samla in data från hybrid Windows-dator](log-analytics-quick-collect-windows-computer.md)

Om du inte har en Azure-prenumeration kan du skapa [ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda PowerShell lokalt krävs Azure PowerShell-modulen version 5.7.0 eller senare för att du ska kunna genomföra den här självstudiekursen. Kör `Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzureRmAccount` för att skapa en anslutning till Azure.

## <a name="create-a-workspace"></a>Skapa en arbetsyta
Skapa en arbetsyta med [New-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment). I följande exempel skapas en arbetsyta med namnet *TestWorkspace* i resursgruppen *Lab* i den *eastus* plats med hjälp av en Resource Manager-mall från din lokala datorn. JSON-mallen har konfigurerats för att bara efterfråga du namnet på arbetsytan och anger ett standardvärde för de andra parametrarna som sannolikt skulle användas som en standardkonfiguration i din miljö. 

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
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
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

    ```powershell
        New-AzureRmResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deploylaworkspacetemplate.json
    ```

Det kan ta några minuter att slutföra distributionen. När den är klar kan du se ett meddelande som liknar följande som innehåller resultatet:

![Exempelresultat när distributionen är klar](./media/log-analytics-template-workspace-configuration/template-output-01.png)

## <a name="next-steps"></a>Nästa steg
Nu när du har en arbetsyta som är tillgängliga kan du konfigurera insamling av övervakning av telemetri, köra loggsökningar för att analysera dessa data och lägga till en hanteringslösning för att ge ytterligare data och analytisk insikt.  

* Om du vill aktivera insamling av data från Azure-resurser med Azure Diagnostics eller Azure storage, se [samla in Azure-tjänsteloggar och mått för användning i Log Analytics](log-analytics-azure-storage.md).  
* Lägg till [System Center Operations Manager som en datakälla](log-analytics-om-agents.md) att samla in data från agenter som rapporterar Operations Manager-hanteringsgrupp och lagra den i Log Analytics-arbetsytan.  
* Ansluta [Configuration Manager](log-analytics-sccm.md) att importera datorer som är medlemmar i samlingar i hierarkin.  
* Granska den [hanteringslösningar](log-analytics-add-solutions.md) tillgängliga och hur du lägger till eller ta bort en lösning från din arbetsyta.