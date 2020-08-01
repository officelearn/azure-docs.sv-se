---
title: Aktivera Uppdateringshantering med Azure Resource Manager-mall | Microsoft Docs
description: Den här artikeln beskriver hur du använder en Azure Resource Manager-mall för att aktivera Uppdateringshantering.
ms.service: automation
ms.subservice: update-management
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 06/10/2020
ms.openlocfilehash: 37f4b7208d7c8fafcd3aa1d25106a2cd5e4949c6
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2020
ms.locfileid: "87450605"
---
# <a name="enable-update-management-using-azure-resource-manager-template"></a>Aktivera Uppdateringshantering via en Azure Resource Manager-mall

Du kan använda en [Azure Resource Manager-mall](../../azure-resource-manager/templates/template-syntax.md) för att aktivera funktionen Azure Automation uppdateringshantering i resurs gruppen. Den här artikeln innehåller en exempel mall som automatiserar följande:

* Skapa en Azure Monitor Log Analytics-arbetsyta.
* Skapa ett Azure Automation-konto.
* Länkar Automation-kontot till Log Analytics-arbetsytan, om det inte redan är länkat.
* Aktiverar Uppdateringshantering.

Mallen aktiverar inte Uppdateringshantering på en eller flera virtuella Azure-eller icke-Azure-datorer.

Om du redan har en Log Analytics arbets yta och ett Automation-konto som har distribuerats i en region som stöds i din prenumeration är de inte länkade. Med den här mallen skapas länken och distribuerar Uppdateringshantering.

## <a name="api-versions"></a>API-versioner

I följande tabell visas API-versionerna för de resurser som används i den här mallen.

| Resurs | Resurstyp | API-version |
|:---|:---|:---|
| Arbetsyta | arbetsytor | 2020-03-01 – för hands version |
| Automation-konto | automatisering | 2018-06-30 |
| Lösning | lösningar | 2015-11-01 – för hands version |

## <a name="before-using-the-template"></a>Innan du använder mallen

Om du väljer att installera och använda PowerShell lokalt kräver den här artikeln Azure PowerShell AZ-modulen. Kör `Get-Module -ListAvailable Az` för att hitta versionen. Om du behöver uppgradera kan du läsa [Installera Azure PowerShell-modulen](/powershell/azure/install-az-ps). Om du kör PowerShell lokalt måste du också köra [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.7.0) för att skapa en anslutning till Azure. Med Azure PowerShell använder distributionen [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment).

Om du väljer att installera och använda CLI lokalt kräver den här artikeln att du kör Azure CLI-version 2.1.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest). Med Azure CLI använder den här distributionen [AZ Group Deployment Create](/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create). 

JSON-mallen har kon figurer ATS för att uppmana dig att:

* Namnet på arbets ytan.
* Den region där du vill skapa arbets ytan.
* Aktivera resurs-eller arbets ytans behörigheter.
* Namnet på Automation-kontot.
* Den region där kontot ska skapas.

JSON-mallen anger ett standardvärde för de andra parametrarna som sannolikt kommer att användas för en standard konfiguration i din miljö. Du kan lagra mallen i ett Azure Storage-konto för delad åtkomst i din organisation. Mer information om hur du arbetar med mallar finns i [distribuera resurser med Resource Manager-mallar och Azure CLI](../../azure-resource-manager/templates/deploy-cli.md).

Följande parametrar i mallen anges med ett standardvärde för Log Analytics arbets ytan:

* SKU – standardvärdet för den nya pris nivån per GB som lanseras i pris sättnings modellen från april 2018
* data kvarhållning – standardvärdet är trettio dagar

>[!WARNING]
>Om du skapar eller konfigurerar en Log Analytics arbets yta i en prenumeration som har valt att ha en ny pris modell på april 2018 är den enda giltiga Log Analytics pris nivån **PerGB2018**.
>

JSON-mallen anger ett standardvärde för de andra parametrarna som sannolikt används som standard konfiguration i din miljö. Du kan lagra mallen i ett Azure Storage-konto för delad åtkomst i din organisation. Mer information om hur du arbetar med mallar finns i [distribuera resurser med Resource Manager-mallar och Azure CLI](../../azure-resource-manager/templates/deploy-cli.md).

Det är viktigt att förstå följande konfigurations information om du är nybörjare på Azure Automation och Azure Monitor, för att undvika fel vid försök att skapa, konfigurera och använda en Log Analytics arbets yta som är länkad till det nya Automation-kontot.

* Granska [Ytterligare information](../../azure-monitor/platform/template-workspace-configuration.md#create-a-log-analytics-workspace) för att helt förstå konfigurations alternativ för arbets ytor, till exempel åtkomst kontrol läge, pris nivå, kvarhållning och kapacitets reservations nivå.

* Eftersom bara vissa regioner stöds för att länka en Log Analytics-arbetsyta och ett Automation-konto i din prenumeration, kan du granska [mappningar för arbets ytor](../how-to/region-mappings.md) för att ange de regioner som stöds infogade eller i en parameter fil.

* Om du inte har använt Azure Monitor loggar och inte har distribuerat en arbets yta redan, bör du gå igenom design vägledningen för [arbets ytan](../../azure-monitor/platform/design-logs-deployment.md) för att lära dig mer om åtkomst kontroll och förstå de design implementerings strategier som vi rekommenderar för din organisation.

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
        "location": {
            "type": "string",
            "metadata": {
                "description": "Specifies the location in which to create the workspace."
            }
        },
        "resourcePermissions": {
              "type": "bool",
              "metadata": {
                "description": "true to use resource or workspace permissions. false to require workspace permissions."
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
            "apiVersion": "2020-03-01-preview",
            "location": "[parameters('location')]",
            "properties": {
                "sku": {
                    "name": "[parameters('sku')]",
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
                    "location": "[parameters('location')]",
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
            "apiVersion": "2018-06-30",
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
            "apiVersion": "2020-03-01-preview",
            "type": "Microsoft.OperationalInsights/workspaces/linkedServices",
            "name": "[concat(parameters('workspaceName'), '/' , 'Automation')]",
            "location": "[parameters('location')]",
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

2. Redigera mallen så att den uppfyller dina krav. Överväg att skapa en [Resource Manager-parameter fil](../../azure-resource-manager/templates/parameter-files.md) i stället för att skicka parametrar som infogade värden.

3. Spara filen i en lokal mapp som **deployUMSolutiontemplate.jspå**.

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

    ![Exempel på resultat när distributionen är klar](media/update-mgmt-enable-template/template-output.png)

## <a name="next-steps"></a>Nästa steg

* Om du vill använda Uppdateringshantering för virtuella datorer läser du [Hantera uppdateringar och korrigeringar för dina virtuella datorer](update-mgmt-manage-updates-for-vm.md).

* Om du inte längre vill använda Uppdateringshantering och vill ta bort det, se anvisningar i [ta bort uppdateringshantering-funktionen](update-mgmt-remove-feature.md).

* Om du vill ta bort virtuella datorer från Uppdateringshantering, se [ta bort virtuella datorer från uppdateringshantering](update-mgmt-remove-vms.md).
