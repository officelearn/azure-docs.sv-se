---
title: Skapa ett Automation-konto med hjälp av en Azure Resource Manager mall | Microsoft Docs
description: Den här artikeln beskriver hur du använder en Azure Resource Manager mall för att skapa ett Azure Automation-konto.
ms.service: automation
ms.subservice: update-management
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 06/09/2020
ms.openlocfilehash: 7b9d3ea30f502f8f95bb12c6a3b270f8eddde0cf
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2020
ms.locfileid: "86186630"
---
# <a name="create-an-automation-account-using-an-azure-resource-manager-template"></a>Skapa ett Automation-konto med hjälp av en Azure Resource Manager mall

Du kan använda [Azure Resource Manager mallar](../azure-resource-manager/templates/template-syntax.md) för att skapa ett Azure Automation konto i din resurs grupp. Den här artikeln innehåller en exempel-mall som:

* Automatiserar skapandet av en Azure Monitor Log Analytics-arbetsyta.
* Automatiserar skapandet av ett Azure Automation-konto.
* Länkar Automation-kontot till Log Analytics-arbetsytan.

Mallen automatiserar inte aktiveringen av virtuella datorer i Azure eller icke-Azure. 

>[!NOTE]
>Det går inte att skapa Automation-kör som-kontot när du använder en Azure Resource Manager-mall. Information om hur du skapar ett Kör som-konto manuellt från portalen eller med PowerShell finns i [Hantera kör som-konton](manage-runas-account.md).

När du har slutfört de här stegen måste du [Konfigurera diagnostikinställningar](automation-manage-send-joblogs-log-analytics.md) för ditt Automation-konto så att Runbook-jobbets status och jobb strömmar skickas till den länkade Log Analytics-arbetsytan. 

## <a name="api-versions"></a>API-versioner

I följande tabell visas API-versionen för de resurser som används i det här exemplet.

| Resurs | Resurstyp | API-version |
|:---|:---|:---|
| Arbetsyta | arbetsytor | 2020-03-01 – för hands version |
| Automation-konto | automatisering | 2018-06-30 | 

## <a name="before-you-use-the-template"></a>Innan du använder mallen

Om du väljer att installera och använda PowerShell lokalt kräver den här artikeln Azure PowerShell AZ-modulen. Kör `Get-Module -ListAvailable Az` för att hitta versionen. Om du behöver uppgradera kan du läsa [Installera Azure PowerShell-modulen](/powershell/azure/install-az-ps). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure. Med PowerShell använder distributionen [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment).

Om du väljer att installera och använda Azure CLI lokalt, kräver den här artikeln att du kör version 2.1.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest). Med Azure CLI använder den här distributionen [AZ Group Deployment Create](/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create). 

JSON-mallen har kon figurer ATS för att uppmana dig att:

* Namnet på arbets ytan.
* Regionen som arbets ytan ska skapas i.
* Aktivera resurs-eller arbets ytans behörigheter.
* Namnet på Automation-kontot.
* Regionen som du skapar Automation-kontot i.

Följande parametrar i mallen anges med ett standardvärde för Log Analytics arbets ytan:

* *SKU: n* är som standard den pris nivå per GB som lanserades i pris sättnings modellen april 2018.
* *dataRetention* är som standard 30 dagar.

>[!WARNING]
>Om du vill skapa eller konfigurera en Log Analytics arbets yta i en prenumeration som har valt att använda pris sättnings modellen från april 2018 är den enda giltiga Log Analytics pris nivån *PerGB2018*.
>

JSON-mallen anger ett standardvärde för de andra parametrarna som sannolikt används som standard konfiguration i din miljö. Du kan lagra mallen i ett Azure Storage-konto för delad åtkomst i din organisation. Mer information om hur du arbetar med mallar finns i [distribuera resurser med Resource Manager-mallar och Azure CLI](../azure-resource-manager/templates/deploy-cli.md).

Om du är nybörjare på Azure Automation och Azure Monitor är det viktigt att du förstår följande konfigurations information. De kan hjälpa dig att undvika fel när du försöker skapa, konfigurera och använda en Log Analytics arbets yta som är länkad till det nya Automation-kontot.

* Granska [Ytterligare information](../azure-monitor/platform/template-workspace-configuration.md#create-a-log-analytics-workspace) för att helt förstå konfigurations alternativ för arbets ytor, till exempel åtkomst kontrol läge, pris nivå, kvarhållning och kapacitets reservations nivå.

* Granska [mappningar för arbets ytor](how-to/region-mappings.md) för att ange de regioner som stöds infogade eller i en parameter fil. Endast vissa regioner stöds för att länka en Log Analytics-arbetsyta och ett Automation-konto i din prenumeration.

* Om du inte har använt Azure Monitor loggar och inte har distribuerat en arbets yta redan, bör du gå igenom [rikt linjerna för design av arbets ytor](../azure-monitor/platform/design-logs-deployment.md). Det hjälper dig att lära dig mer om åtkomst kontroll och förstå de design implementerings strategier som vi rekommenderar för din organisation.

## <a name="deploy-the-template"></a>Distribuera mallen

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
                "description": "Pricing tier: perGB2018 or legacy tiers (Free, Standalone, PerNode, Standard or Premium), which are not available to all customers."
            }
        },
        "dataRetention": {
            "type": "int",
            "defaultValue": 30,
            "minValue": 7,
            "maxValue": 730,
            "metadata": {
                "description": "Number of days to retain data."
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
        },
        "sampleGraphicalRunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorial"
            },
            "sampleGraphicalRunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
            },
            "sampleGraphicalRunbookContentUri": {
                "type": "String",
                "defaultValue": "https://eus2oaasibizamarketprod1.blob.core.windows.net/marketplace-runbooks/AzureAutomationTutorial.graphrunbook"
            },
            "samplePowerShellRunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorialScript"
            },
            "samplePowerShellRunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
            },
            "samplePowerShellRunbookContentUri": {
                "type": "String",
                "defaultValue": "https://eus2oaasibizamarketprod1.blob.core.windows.net/marketplace-runbooks/AzureAutomationTutorial.ps1"
            },
            "samplePython2RunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorialPython2"
            },
            "samplePython2RunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
            },
            "samplePython2RunbookContentUri": {
                "type": "String",
                "defaultValue": "https://eus2oaasibizamarketprod1.blob.core.windows.net/marketplace-runbooks/AzureAutomationTutorialPython2.py"
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
            "type": "Microsoft.Automation/automationAccounts",
            "apiVersion": "2018-06-30",
            "name": "[parameters('automationAccountName')]",
            "location": "[parameters('automationAccountLocation')]",
            "dependsOn": [
                 "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]",
            ],
            "tags": {},
            "properties": {
                "sku": {
                    "name": "Basic"
                }
            },
            "resources": [
                    {
                        "type": "runbooks",
                        "apiVersion": "2018-06-30",
                        "name": "[parameters('sampleGraphicalRunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
                        ],
                        "tags": {},
                        "properties": {
                            "runbookType": "GraphPowerShell",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('sampleGraphicalRunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[parameters('sampleGraphicalRunbookContentUri')]",
                                "version": "1.0.0.0"
                            }
                        }
                    },
                    {
                        "type": "runbooks",
                        "apiVersion": "2018-06-30",
                        "name": "[parameters('samplePowerShellRunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
                        ],
                        "tags": {},
                        "properties": {
                            "runbookType": "PowerShell",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('samplePowerShellRunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[parameters('samplePowerShellRunbookContentUri')]",
                                "version": "1.0.0.0"
                            }
                        }
                    },
                    {
                        "type": "runbooks",
                        "apiVersion": "2018-06-30",
                        "name": "[parameters('samplePython2RunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
                        ],
                        "tags": {},
                        "properties": {
                            "runbookType": "Python2",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('samplePython2RunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[parameters('samplePython2RunbookContentUri')]",
                                "version": "1.0.0.0"
                            }
                        }
                    }
                ]
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
     ]
    }
    ```

2. Redigera mallen så att den uppfyller dina krav. Överväg att skapa en [parameter fil i Resource Manager](../azure-resource-manager/templates/parameter-files.md) i stället för att skicka parametrar som infogade värden.

3. Spara den här filen som deployAzAutomationAccttemplate.jspå en lokal mapp.

4. Nu är det dags att distribuera den här mallen. Du kan använda antingen PowerShell eller Azure CLI. När du uppmanas att ange ett namn på en arbets yta och ett Automation-konto anger du ett namn som är globalt unikt för alla dina Azure-prenumerationer.

    **PowerShell**

    ```powershell
    New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deployAzAutomationAccttemplate.json
    ```

    **Azure CLI**

    ```cli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deployAzAutomationAccttemplate.json
    ```

    Det kan ta några minuter att slutföra distributionen. När du gör det visas ett meddelande som liknar resultatet.

    ![Exempel på resultat när distributionen är klar](media/automation-create-account-template/template-output.png)

## <a name="next-steps"></a>Nästa steg

Om du vill vidarebefordra status och jobb strömmar för Runbook-jobb till den länkade Log Analytics arbets ytan granskar du [vidarebefordra Azure Automation jobb data till Azure Monitor loggar](automation-manage-send-joblogs-log-analytics.md). Detta konfigurerar diagnostikinställningar för Automation-kontot med hjälp av Azure PowerShell kommandon för att slutföra integrationen av att skicka loggar till arbets ytan för analys. 
