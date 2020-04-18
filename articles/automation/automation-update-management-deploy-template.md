---
title: Använda Azure Resource Manager-mallar för att uppdatera uppdateringshantering ombord | Microsoft-dokument
description: Du kan använda en Azure Resource Manager-mall för att gå in på Azure Automation Update Management-lösningen.
ms.service: automation
ms.subservice: update-management
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 03/30/2020
ms.openlocfilehash: 81f9d242d93ffe513c0c3733ceb9d38ca9cadc1c
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617457"
---
# <a name="onboard-update-management-solution-using-azure-resource-manager-template"></a>Uppdaterad hanteringslösning med Azure Resource Manager-mall

Du kan använda [Azure Resource Manager-mallar](../azure-resource-manager/templates/template-syntax.md) för att aktivera Azure Automation Update Management-lösningen i din resursgrupp. Den här artikeln innehåller en exempelmall som automatiserar följande:

* Skapa en Azure Monitor Log Analytics-arbetsyta.
* Skapa ett Azure Automation-konto.
* Länka Automation-kontot till log analytics-arbetsytan, om det inte redan är länkat.
* Introduktion till Azure Automation Update Management-lösningen.

Mallen automatiserar inte introduktionen av en eller flera virtuella Azure- eller icke-Azure-datorer.

Om du redan har ett Log Analytics-arbetsyta och Automation-konto distribuerat i en region som stöds i din prenumeration är de inte länkade. Arbetsytan har inte redan lösningen update management distribuerad. Om du använder den här mallen skapas länken och lösningen för uppdateringshantering distribueras. 

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installationsinstruktioner för Az-modul på hybridkörningsarbetaren finns [i Installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med [så här uppdaterar du Azure PowerShell-moduler i Azure Automation](automation-update-azure-modules.md).

## <a name="api-versions"></a>API-versioner

I följande tabell visas API-versioner för de resurser som används i den här mallen.

| Resurs | Resurstyp | API-version |
|:---|:---|:---|
| Arbetsyta | arbetsytor | 2017-03-15-förhandsvisning |
| Automation-konto | automation | 2015-10-31 | 
| Lösning | lösningar | 2015-11-01-förhandsvisning |

## <a name="before-using-the-template"></a>Innan du använder mallen

Om du väljer att installera och använda PowerShell lokalt kräver den här artikeln Azure PowerShell Az-modulen. Kör `Get-Module -ListAvailable Az` för att hitta versionen. Om du behöver uppgradera kan du läsa [Installera Azure PowerShell-modulen](/powershell/azure/install-az-ps). Om du kör PowerShell lokalt måste du också köra [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.7.0) för att skapa en anslutning med Azure. Med Azure PowerShell använder distributionen [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment).

Om du väljer att installera och använda CLI lokalt kräver den här artikeln att du kör Azure CLI version 2.1.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Med Azure CLI använder den här distributionen [az-gruppdistribution skapa](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create). 

JSON-mallen är konfigurerad för att fråga dig om:

* Namnet på arbetsytan
* Den region där arbetsytan ska skapas
* Namnet på Automation-kontot
* Den region där kontot ska skapas

JSON-mallen anger ett standardvärde för de andra parametrar som sannolikt kommer att användas för en standardkonfiguration i din miljö. Du kan lagra mallen i ett Azure-lagringskonto för delad åtkomst i din organisation. Mer information om hur du arbetar med mallar finns i [Distribuera resurser med Resource Manager-mallar och Azure CLI](../azure-resource-manager/templates/deploy-cli.md).

Följande parametrar i mallen anges med ett standardvärde för log analytics-arbetsytan:

* sku - standardvärden till den nya prisnivån per GB som släpptes i april 2018-prismodellen
* lagring av data - standardvärden till trettio dagar
* kapacitetsreservation - standardvärdet till 100 GB

>[!WARNING]
>Om du skapar eller konfigurerar en Log Analytics-arbetsyta i en prenumeration som har valt prismodellen april 2018 är den enda giltiga log analytics-prisnivån **PerGB2018**.
>

>[!NOTE]
>Innan du använder den här mallen bör du granska [ytterligare information](../azure-monitor/platform/template-workspace-configuration.md#create-a-log-analytics-workspace) för att till fullo förstå konfigurationsalternativ för arbetsytor, till exempel åtkomstkontrollläge, prisnivå, kvarhållning och kapacitetsreservationsnivå. Om du inte har använt Azure Monitor-loggar och inte redan har distribuerat en arbetsyta bör du granska [arbetsytedesignvägledningen](../azure-monitor/platform/design-logs-deployment.md) för att lära dig mer om åtkomstkontroll och förstå de designimplementeringsstrategier som vi rekommenderar för din organisation.

## <a name="deploy-template"></a>Distribuera mallen

1. Kopiera och klistra in följande JSON-syntax i filen:

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "description": "Workspace name"
            }
        },
        "sku": {
            "type": "string",
            "allowedValues": [
                "pergb2018",
                "Free",
                "Standalone",
                "PerNode",
                "Standard",
                "Premium"
            ],
            "defaultValue": "pergb2018",
            "metadata": {
                "description": "Pricing tier: perGB2018 or legacy tiers (Free, Standalone, PerNode, Standard or Premium) which are not available to all customers."
            }
        },
        "dataRetention": {
            "type": "int",
            "defaultValue": 30,
            "minValue": 7,
            "maxValue": 730,
            "metadata": {
                "description": "Number of days of retention. Workspaces in the legacy Free pricing tier can only have 7 days."
            }
        },
        "immediatePurgeDataOn30Days": {
            "type": "bool",
            "defaultValue": "[bool('false')]",
            "metadata": {
                "description": "If set to true when changing retention to 30 days, older data will be immediately deleted. Use this with extreme caution. This only applies when retention is being set to 30 days."
            }
        },
        "location": {
            "type": "string",
            "allowedValues": [
                "australiacentral",
                "australiaeast",
                "australiasoutheast",
                "brazilsouth",
                "canadacentral",
                "centralindia",
                "centralus",
                "eastasia",
                "eastus",
                "eastus2",
                "francecentral",
                "japaneast",
                "koreacentral",
                "northcentralus",
                "northeurope",
                "southafricanorth",
                "southcentralus",
                "southeastasia",
                "uksouth",
                "ukwest",
                "westcentralus",
                "westeurope",
                "westus",
                "westus2"
            ],
            "metadata": {
                "description": "Specifies the location in which to create the workspace."
            }
        },
        "automationAccountName": {
            "type": "string",
            "metadata": {
                "description": "Automation account name"
            }
        },
        "automationAccountLocation": {
            "type": "string",
            "metadata": {
                "description": "Specify the location in which to create the Automation account."
            }
        }
    },
    "variables": {
        "Updates": {
            "name": "[concat('Updates', '(', parameters('workspaceName'), ')')]",
            "galleryName": "Updates"
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
                    "Name": "[parameters('sku')]",
                    "name": "CapacityReservation",
                    "capacityReservationLevel": 100
                },
                "retentionInDays": "[parameters('dataRetention')]",
                "features": {
                    "searchVersion": 1,
                    "legacy": 0,
                    "enableLogAccessUsingOnlyResourcePermissions": true
                }
            },
            "resources": [
                {
                    "apiVersion": "2015-11-01-preview",
                    "location": "[resourceGroup().location]",
                    "name": "[variables('Updates').name]",
                    "type": "Microsoft.OperationsManagement/solutions",
                    "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationsManagement/solutions/', variables('Updates').name)]",
                    "dependsOn": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                    ],
                    "properties": {
                        "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                    },
                    "plan": {
                        "name": "[variables('Updates').name]",
                        "publisher": "Microsoft",
                        "promotionCode": "",
                        "product": "[concat('OMSGallery/', variables('Updates').galleryName)]"
                    }
                }
            ]
        },
        {
            "type": "Microsoft.Automation/automationAccounts",
            "apiVersion": "2015-01-01-preview",
            "name": "[parameters('automationAccountName')]",
            "location": "[parameters('automationAccountLocation')]",
            "dependsOn": [],
            "tags": {},
            "properties": {
                "sku": {
                    "name": "Basic"
                }
            },
        },
        {
            "apiVersion": "2015-11-01-preview",
            "type": "Microsoft.OperationalInsights/workspaces/linkedServices",
            "name": "[concat(parameters('workspaceName'), '/' , 'Automation')]",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]",
                "[concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
            ],
            "properties": {
                "resourceId": "[resourceId('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
            }
        }
      ]
    }
    ```

2. Redigera mallen för att uppfylla dina krav. Överväg att skapa en [Resource Manager-parameterfil](../azure-resource-manager/templates/parameter-files.md) i stället för att skicka parametrar som infogade värden.

3. Spara filen i en lokal mapp som **deployUMSolutiontemplate.json**.

4. Nu är det dags att distribuera den här mallen. Du kan använda antingen PowerShell eller Azure CLI. När du uppmanas att ange ett jobbyte- och Automation-kontonamn anger du ett namn som är globalt unikt för alla Azure-prenumerationer.

    **PowerShell**

    ```powershell
    New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deployUMSolutiontemplate.json
    ```

    **Azure CLI**

    ```cli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deployUMSolutiontemplate.json
    ```

    Det kan ta några minuter att slutföra distributionen. När det är klart visas ett meddelande som liknar följande som innehåller resultatet:

    ![Exempel på resultat när distributionen är klar](media/automation-update-management-deploy-template/template-output.png)

## <a name="next-steps"></a>Nästa steg

Nu när du har distribuerat lösningen Update Management kan du aktivera virtuella datorer för hantering, granska uppdateringsutvärderingar och distribuera uppdateringar för att få dem att följa reglerna.

- Från ditt [Azure Automation-konto](automation-onboard-solutions-from-automation-account.md) för en eller flera Azure-datorer och manuellt för datorer som inte är Azure.

- För en enda Azure-virtuell dator från sidan för den virtuella datorn i Azure-portalen. Det här scenariot är tillgängligt för [virtuella Linux-](../virtual-machines/linux/tutorial-config-management.md#enable-update-management) och [Windows-datorer.](../virtual-machines/windows/tutorial-config-management.md#enable-update-management)

- För [flera virtuella Azure-datorer](manage-update-multi.md) genom att välja dem från sidan **Virtuella datorer** i Azure-portalen. 