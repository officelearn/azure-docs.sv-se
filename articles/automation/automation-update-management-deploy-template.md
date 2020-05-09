---
title: Använd Azure Resource Manager mallar för att publicera Uppdateringshantering | Microsoft Docs
description: Du kan använda en Azure Resource Manager-mall för att publicera Azure Automation Uppdateringshantering-lösningen.
ms.service: automation
ms.subservice: update-management
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 04/24/2020
ms.openlocfilehash: dd8706c1e95e6b1e4ca4a38d4a336f6186464696
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2020
ms.locfileid: "82872199"
---
# <a name="onboard-update-management-solution-using-azure-resource-manager-template"></a>Publicera Uppdateringshantering-lösning med Azure Resource Manager-mall

Du kan använda [Azure Resource Manager mallar](../azure-resource-manager/templates/template-syntax.md) för att aktivera Azure Automation uppdateringshantering-lösningen i din resurs grupp. Den här artikeln innehåller en exempel mall som automatiserar följande:

* Skapa en Azure Monitor Log Analytics-arbetsyta.
* Skapa ett Azure Automation-konto.
* Länkar Automation-kontot till Log Analytics-arbetsytan, om det inte redan är länkat.
* Onboarding The Azure Automation Uppdateringshantering-lösningen.

Mallen automatiserar inte onboarding av en eller flera virtuella Azure-eller icke-Azure-datorer.

Om du redan har en Log Analytics arbets yta och ett Automation-konto som har distribuerats i en region som stöds i din prenumeration är de inte länkade. Den Uppdateringshantering-lösningen har inte distribuerats på arbets ytan. Genom att använda den här mallen skapas länken och distribuerar Uppdateringshantering-lösningen. 

>[!NOTE]
>**Nxautomation** -användaren som ingår i uppdateringshantering på Linux kör bara signerade Runbooks.

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installations anvisningar för AZ-modulen på Hybrid Runbook Worker finns i [installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med hjälp av [hur du uppdaterar Azure PowerShell moduler i Azure Automation](automation-update-azure-modules.md).

## <a name="api-versions"></a>API-versioner

I följande tabell visas API-versionerna för de resurser som används i den här mallen.

| Resurs | Resurstyp | API-version |
|:---|:---|:---|
| Arbetsyta | arbetsytor | 2017-03-15 – för hands version |
| Automation-konto | automation | 2015-10-31 | 
| Lösning | lösningar | 2015-11-01 – för hands version |

## <a name="before-using-the-template"></a>Innan du använder mallen

Om du väljer att installera och använda PowerShell lokalt kräver den här artikeln Azure PowerShell AZ-modulen. Kör `Get-Module -ListAvailable Az` för att hitta versionen. Om du behöver uppgradera kan du läsa [Installera Azure PowerShell-modulen](/powershell/azure/install-az-ps). Om du kör PowerShell lokalt måste du också köra [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.7.0) för att skapa en anslutning till Azure. Med Azure PowerShell använder distributionen [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment).

Om du väljer att installera och använda CLI lokalt kräver den här artikeln att du kör Azure CLI-version 2.1.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Med Azure CLI använder den här distributionen [AZ Group Deployment Create](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create). 

JSON-mallen har kon figurer ATS för att uppmana dig att:

* Namnet på arbets ytan
* Regionen där arbets ytan ska skapas
* Namnet på Automation-kontot
* Regionen där kontot ska skapas

JSON-mallen anger ett standardvärde för de andra parametrarna som sannolikt kommer att användas för en standard konfiguration i din miljö. Du kan lagra mallen i ett Azure Storage-konto för delad åtkomst i din organisation. Mer information om hur du arbetar med mallar finns i [distribuera resurser med Resource Manager-mallar och Azure CLI](../azure-resource-manager/templates/deploy-cli.md).

Följande parametrar i mallen anges med ett standardvärde för Log Analytics arbets ytan:

* SKU – standardvärdet för den nya pris nivån per GB som lanseras i pris sättnings modellen från april 2018
* data kvarhållning – standardvärdet är trettio dagar
* kapacitets reservation – standardvärdet är 100 GB

>[!WARNING]
>Om du skapar eller konfigurerar en Log Analytics arbets yta i en prenumeration som har valt att ha en ny pris modell på april 2018 är den enda giltiga Log Analytics pris nivån **PerGB2018**.
>

JSON-mallen anger ett standardvärde för de andra parametrarna som sannolikt används som standard konfiguration i din miljö. Du kan lagra mallen i ett Azure Storage-konto för delad åtkomst i din organisation. Mer information om hur du arbetar med mallar finns i [distribuera resurser med Resource Manager-mallar och Azure CLI](../azure-resource-manager/templates/deploy-cli.md).

Det är viktigt att förstå följande konfigurations information om du är nybörjare på Azure Automation och Azure Monitor, för att undvika fel vid försök att skapa, konfigurera och använda en Log Analytics arbets yta som är länkad till det nya Automation-kontot.

* Granska [Ytterligare information](../azure-monitor/platform/template-workspace-configuration.md#create-a-log-analytics-workspace) för att helt förstå konfigurations alternativ för arbets ytor, till exempel åtkomst kontrol läge, pris nivå, kvarhållning och kapacitets reservations nivå.

* Eftersom bara vissa regioner stöds för att länka en Log Analytics-arbetsyta och ett Automation-konto i din prenumeration, kan du granska [mappningar för arbets ytor](how-to/region-mappings.md) för att ange de regioner som stöds infogade eller i en parameter fil.

* Om du inte har använt Azure Monitor loggar och inte har distribuerat en arbets yta redan, bör du gå igenom design vägledningen för [arbets ytan](../azure-monitor/platform/design-logs-deployment.md) för att lära dig mer om åtkomst kontroll och förstå de design implementerings strategier som vi rekommenderar för din organisation.

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

2. Redigera mallen så att den uppfyller dina krav. Överväg att skapa en [Resource Manager-parameter fil](../azure-resource-manager/templates/parameter-files.md) i stället för att skicka parametrar som infogade värden.

3. Spara filen i en lokal mapp som **deployUMSolutiontemplate. JSON**.

4. Nu är det dags att distribuera den här mallen. Du kan använda antingen PowerShell eller Azure CLI. När du uppmanas att ange ett namn på en arbets yta och ett Automation-konto anger du ett namn som är globalt unikt för alla Azure-prenumerationer.

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

Nu när du har distribuerat Uppdateringshantering-lösningen kan du aktivera virtuella datorer för hantering, granska uppdaterings utvärderingar och distribuera uppdateringar för att göra dem kompatibla.

- Från ditt [Azure Automation-konto](automation-onboard-solutions-from-automation-account.md) för en eller flera Azure-datorer och manuellt för datorer som inte är Azure-datorer.

- För en enskild virtuell Azure-dator från sidan virtuell dator i Azure Portal. Det här scenariot är tillgängligt för virtuella [Linux](../virtual-machines/linux/tutorial-config-management.md#enable-update-management) -och [Windows](../virtual-machines/windows/tutorial-config-management.md#enable-update-management) -datorer.

- För [flera virtuella Azure-datorer](manage-update-multi.md) genom att välja dem från sidan **virtuella datorer** i Azure Portal. 