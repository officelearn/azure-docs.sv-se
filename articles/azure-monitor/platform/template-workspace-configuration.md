---
title: Använd Azure Resource Manager-mallar för att skapa och konfigurera en Log Analytics-arbetsyta | Microsoft Docs
description: Du kan använda Azure Resource Manager-mallar för att skapa och konfigurera Log Analytics-arbetsytor.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: d21ca1b0-847d-4716-bb30-2a8c02a606aa
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: magoedte
ms.openlocfilehash: 0578b50952c12d4587f7a4751bc831d3134c64e7
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2019
ms.locfileid: "58540779"
---
# <a name="manage-log-analytics-workspace-using-azure-resource-manager-templates"></a>Hantera Log Analytics-arbetsyta med hjälp av Azure Resource Manager-mallar

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Du kan använda [Azure Resource Manager-mallar](../../azure-resource-manager/resource-group-authoring-templates.md) du skapar och konfigurerar Log Analytics-arbetsytor i Azure Monitor. Exempel på de uppgifter du kan utföra med mallar:

* Skapa en arbetsyta inklusive inställningen prisnivå 
* Lägga till en lösning
* Skapa sparade sökningar
* Skapa en datorgrupp
* Aktivera insamling av IIS-loggar från datorer med Windows-agenten installerad
* Samla in prestandaräknare från Linux- och Windows-datorer
* Samla in händelser från syslog på Linux-datorer 
* Samla in händelser från Windows-händelseloggar
* Lägg till log analytics-agenten till en Azure-dator
* Konfigurera log analytics för att indexera data som samlas in med Azure-diagnostik

Den här artikeln innehåller mallexempel på som beskriva några av den konfiguration som du kan utföra med mallar.

## <a name="api-versions"></a>API-versioner
I följande tabell visas den API-versionen för resurser som används i det här exemplet.

| Resurs | Resurstyp | API-version |
|:---|:---|:---|
| Arbetsyta   | arbetsytor    | 2017-03-15-preview |
| Search      | savedSearches | 2015-03-20 |
| Datakälla | datakällor   | 2015-11-01-preview |
| Lösning    | lösningar     | 2015-11-01-preview |

## <a name="create-a-log-analytics-workspace"></a>Skapa en Log Analytics-arbetsyta
I följande exempel skapas en arbetsyta med hjälp av en mall från den lokala datorn. JSON-mallen har konfigurerats för att bara efterfråga du namnet på arbetsytan och anger ett standardvärde för de andra parametrarna som sannolikt skulle användas som en standardkonfiguration i din miljö.  

Följande parametrar anger ett standardvärde:

* plats – standardvärdet är USA, östra
* SKU - som standard den nya Per GB prisnivån som introducerades i April 2018 prismodellen

> [!NOTE]
>Skapar eller konfigurerar en Log Analytics-arbetsyta i en prenumeration som har valt att den nya prissättningsmodellen från April 2018, är det enda giltiga Log Analytics prisnivån **PerGB2018**.  
>Om du kanske har några prenumerationer i den [pre-April 2018 prismodellen](https://docs.microsoft.com/azure/azure-monitor/platform/usage-estimated-costs#new-pricing-model), kan du ange den **fristående** prisnivå, och detta lyckas för båda prenumerationen i prismodellen pre-April 2018 och prenumerationer i den nya prissättningen. För arbetsytor i prenumerationer som har antagit den nya proicing modellen prisnivån anges till **PerGB2018**. 

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
2. Redigera mallen så att den uppfyller dina krav.  Granska [Microsoft.OperationalInsights/workspaces mall](https://docs.microsoft.com/azure/templates/microsoft.operationalinsights/workspaces) referens till att lära dig vilka egenskaper och värden som stöds. 
3. Spara filen som **deploylaworkspacetemplate.json** till en lokal mapp.
4. Nu är det dags att distribuera den här mallen. Du kan använda PowerShell eller från kommandoraden för att skapa arbetsytan.

   * För PowerShell använder du följande kommandon från mappen som innehåller mallen:
   
        ```powershell
        New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deploylaworkspacetemplate.json
        ```

   * För kommandoraden, använder du följande kommandon från mappen som innehåller mallen:

        ```cmd
        azure config mode arm
        azure group deployment create <my-resource-group> <my-deployment-name> --TemplateFile deploylaworkspacetemplate.json
        ```

Det kan ta några minuter att slutföra distributionen. När den är klar kan du se ett meddelande som liknar följande som innehåller resultatet:<br><br> ![Exempelresultat när distributionen är klar](./media/template-workspace-configuration/template-output-01.png)

## <a name="configure-a-log-analytics-workspace"></a>Konfigurera en Log Analytics-arbetsyta
I följande exempel i mallen visas hur du:

1. Lägga till lösningar i arbetsytan
2. Skapa sparade sökningar
3. Skapa en datorgrupp
4. Aktivera insamling av IIS-loggar från datorer med Windows-agenten installerad
5. Samla in prestandaräknare för logisk Disk från Linux-datorer (% noder i procent; Ledigt utrymme i MB; Använt utrymme; i % Disköverföringar/sek; Diskläsningar/sek; Diskskrivningar/sek)
6. Samla in syslog-händelser från Linux-datorer
7. Samla in händelser för fel och varningar från programmets händelselogg från Windows-datorer
8. Samla in prestandaräknaren för minne tillgängligt, MB från Windows-datorer
9. Samla in IIS-loggar och Windows-händelseloggar som skrivits av Azure-diagnostik till ett lagringskonto

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "type": "string",
      "metadata": {
        "description": "workspaceName"
      }
    },
    "serviceTier": {
      "type": "string",
      "allowedValues": [
        "Free",
        "Standalone",
        "PerNode",
        "PerGB2018"
      ],
      "metadata": {
        "description": "Service Tier: Free, Standalone, PerNode, or PerGB2018"
    }
      },
    "dataRetention": {
      "type": "int",
      "defaultValue": 30,
      "minValue": 7,
      "maxValue": 730,
      "metadata": {
        "description": "Number of days of retention. Free plans can only have 7 days, Standalone and Log Analytics plans include 30 days for free"
      }
    },
    "location": {
      "type": "string",
      "allowedValues": [
        "East US",
        "West Europe",
        "Southeast Asia",
        "Australia Southeast"
      ]
    },
    "applicationDiagnosticsStorageAccountName": {
        "type": "string",
        "metadata": {
          "description": "Name of the storage account with Azure diagnostics output"
        }
    },
    "applicationDiagnosticsStorageAccountResourceGroup": {
        "type": "string",
        "metadata": {
          "description": "The resource group name containing the storage account with Azure diagnostics output"
        }
    }
  },
  "variables": {
    "Updates": {
      "Name": "[Concat('Updates', '(', parameters('workspaceName'), ')')]",
      "GalleryName": "Updates"
    },
    "AntiMalware": {
      "Name": "[concat('AntiMalware', '(', parameters('workspaceName'), ')')]",
      "GalleryName": "AntiMalware"
    },
    "SQLAssessment": {
      "Name": "[Concat('SQLAssessment', '(', parameters('workspaceName'), ')')]",
      "GalleryName": "SQLAssessment"
    },
    "diagnosticsStorageAccount": "[resourceId(parameters('applicationDiagnosticsStorageAccountResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName'))]"
  },
  "resources": [
    {
      "apiVersion": "2015-11-01-preview",
      "type": "Microsoft.OperationalInsights/workspaces",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "properties": {
        "sku": {
          "Name": "[parameters('serviceTier')]"
        },
    "retentionInDays": "[parameters('dataRetention')]"
      },
      "resources": [
        {
          "apiVersion": "2015-03-20",
          "name": "VMSS Queries2",
          "type": "savedSearches",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "Category": "VMSS",
            "ETag": "*",
            "DisplayName": "VMSS Instance Count",
            "Query": "Event | where Source == \"ServiceFabricNodeBootstrapAgent\" | summarize AggregatedValue = count() by Computer",
            "Version": 1
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleWindowsEvent1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "WindowsEvent",
          "properties": {
            "eventLogName": "Application",
            "eventTypes": [
              {
                "eventType": "Error"
              },
              {
                "eventType": "Warning"
              }
            ]
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleWindowsPerfCounter1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "WindowsPerformanceCounter",
          "properties": {
            "objectName": "Memory",
            "instanceName": "*",
            "intervalSeconds": 10,
            "counterName": "Available MBytes"
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleIISLog1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "IISLogs",
          "properties": {
            "state": "OnPremiseEnabled"
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleSyslog1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "LinuxSyslog",
          "properties": {
            "syslogName": "kern",
            "syslogSeverities": [
              {
                "severity": "emerg"
              },
              {
                "severity": "alert"
              },
              {
                "severity": "crit"
              },
              {
                "severity": "err"
              },
              {
                "severity": "warning"
              }
            ]
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleSyslogCollection1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "LinuxSyslogCollection",
          "properties": {
            "state": "Enabled"
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleLinuxPerf1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "LinuxPerformanceObject",
          "properties": {
            "performanceCounters": [
              {
                "counterName": "% Used Inodes"
              },
              {
                "counterName": "Free Megabytes"
              },
              {
                "counterName": "% Used Space"
              },
              {
                "counterName": "Disk Transfers/sec"
              },
              {
                "counterName": "Disk Reads/sec"
              },
              {
                "counterName": "Disk Writes/sec"
              }
            ],
            "objectName": "Logical Disk",
            "instanceName": "*",
            "intervalSeconds": 10
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleLinuxPerfCollection1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "LinuxPerformanceCollection",
          "properties": {
            "state": "Enabled"
          }
        },
        {
          "apiVersion": "2015-03-20",
          "name": "[concat(parameters('applicationDiagnosticsStorageAccountName'),parameters('workspaceName'))]",
          "type": "storageinsightconfigs",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "containers": [ 
              "wad-iis-logfiles" 
            ],
            "tables": [
              "WADWindowsEventLogsTable"
            ],
            "storageAccount": {
              "id": "[variables('diagnosticsStorageAccount')]",
              "key": "[listKeys(variables('diagnosticsStorageAccount'),'2015-06-15').key1]"
            }
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "location": "[parameters('location')]",
          "name": "[variables('Updates').Name]",
          "type": "Microsoft.OperationsManagement/solutions",
          "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationsManagement/solutions/', variables('Updates').Name)]",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          },
          "plan": {
            "name": "[variables('Updates').Name]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('Updates').GalleryName)]",
            "promotionCode": ""
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "location": "[parameters('location')]",
          "name": "[variables('AntiMalware').Name]",
          "type": "Microsoft.OperationsManagement/solutions",
          "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationsManagement/solutions/', variables('AntiMalware').Name)]",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          },
          "plan": {
            "name": "[variables('AntiMalware').Name]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('AntiMalware').GalleryName)]",
            "promotionCode": ""
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "location": "[parameters('location')]",
          "name": "[variables('SQLAssessment').Name]",
          "type": "Microsoft.OperationsManagement/solutions",
          "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationsManagement/solutions/', variables('SQLAssessment').Name)]",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          },
          "plan": {
            "name": "[variables('SQLAssessment').Name]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('SQLAssessment').GalleryName)]",
            "promotionCode": ""
          }
        }
      ]
    }
  ],
  "outputs": {
    "workspaceName": {
      "type": "string",
      "value": "[parameters('workspaceName')]"
    },
    "provisioningState": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').provisioningState]"
    },
    "source": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').source]"
    },
    "customerId": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').customerId]"
    },
    "pricingTier": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').sku.name]"
    },
    "retentionInDays": {
      "type": "int",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').retentionInDays]"
    },
    "portalUrl": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').portalUrl]"
    }
  }
}

```
### <a name="deploying-the-sample-template"></a>Distribuera exempelmallen
För att distribuera exempelmallen:

1. Spara bifogade exemplet i en fil, till exempel `azuredeploy.json` 
2. Redigera mallen om du vill att den konfiguration du vill ha
3. Använda PowerShell eller från kommandoraden för att distribuera mallen

#### <a name="powershell"></a>PowerShell
```powershell
New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile azuredeploy.json
```

#### <a name="command-line"></a>Kommandorad
```cmd
azure config mode arm
azure group deployment create <my-resource-group> <my-deployment-name> --TemplateFile azuredeploy.json
```

## <a name="example-resource-manager-templates"></a>Exempel Resource Manager-mallar
Azure Snabbstart mallgalleriet innehåller ett antal mallar för Log Analytics, inklusive:

* [Distribuera en virtuell dator som kör Windows med Log Analytics VM-tillägg](https://azure.microsoft.com/documentation/templates/201-oms-extension-windows-vm/)
* [Distribuera en virtuell dator som kör Linux med Log Analytics VM-tillägg](https://azure.microsoft.com/documentation/templates/201-oms-extension-ubuntu-vm/)
* [Övervaka Azure Site Recovery med en befintlig Log Analytics-arbetsyta](https://azure.microsoft.com/documentation/templates/asr-oms-monitoring/)
* [Övervaka Azure Web Apps med en befintlig Log Analytics-arbetsyta](https://azure.microsoft.com/documentation/templates/101-webappazure-oms-monitoring/)
* [Lägg till ett befintligt lagringskonto i Log Analytics](https://azure.microsoft.com/resources/templates/oms-existing-storage-account/)

## <a name="next-steps"></a>Nästa steg
* [Distribuera Windows-agenten till Azure virtuella datorer med Resource Manager-mall](../../virtual-machines/extensions/oms-windows.md).
* [Distribuera Linux-agenten till Azure virtuella datorer med Resource Manager-mall](../../virtual-machines/extensions/oms-linux.md).

