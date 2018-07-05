---
title: Automatiskt aktivera diagnostikinställningar med Resource Manager-mall
description: Lär dig hur du använder Resource Manager-mall för att skapa diagnostiska inställningar som gör att du kan strömma dina diagnostikloggar till Event Hubs eller lagra dem i ett lagringskonto.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 3/26/2018
ms.author: johnkem
ms.component: ''
ms.openlocfilehash: a69cefc3c9363c0e8378a90c44d6a466780402b1
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37434495"
---
# <a name="automatically-enable-diagnostic-settings-at-resource-creation-using-a-resource-manager-template"></a>Aktivera diagnostikinställningar automatiskt när en resurs skapas med en Resource Manager-mall
I den här artikeln visar vi hur du kan använda en [Azure Resource Manager-mall](../azure-resource-manager/resource-group-authoring-templates.md) att konfigurera diagnostikinställningar på en resurs när den skapas. På så sätt kan du automatiskt starta direktuppspelning av dina diagnostikloggar och mått till Event Hubs, arkivera dem i ett Lagringskonto eller skicka dem till Log Analytics när en resurs skapas.

Metoden för att aktivera diagnostikloggar med en Resource Manager-mall beror på resurstypen.

* **Icke-Compute** resurser (till exempel Nätverkssäkerhetsgrupperna, Logic Apps, Automation) använder [diagnostikinställningar som beskrivs i den här artikeln](monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings).
* **Compute** (WAD/LAD-baserad) resurser i [WAD/LAD konfigurationsfilen som beskrivs i den här artikeln](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md).

I den här artikeln beskriver vi hur du konfigurerar diagnostik med någon av metoderna.

De grundläggande stegen är följande:

1. Skapa en mall som en JSON-fil som beskriver hur du skapar resursen och aktivera diagnostik.
2. [Distribuera mallen med hjälp av valfri distributionsmetod](../azure-resource-manager/resource-group-template-deploy.md).

Nedan kan vi ge ett exempel på mall för JSON-filen måste du generera för icke-beräknings- och beräkningsresurser.

## <a name="non-compute-resource-template"></a>Icke-beräkningsresurs mall
För icke-beräkningsresurser behöver du göra två saker:

1. Lägg till parametrar till bloben parametrar för lagringskontonamn, regel-ID för event hub-auktorisering och/eller Log Analytics arbetsyte-ID (aktivera arkivering av diagnostikloggar i ett lagringskonto, strömning av loggar till Event Hubs och/eller skickar loggar till Log Analytics).
   
    ```json
    "settingName": {
      "type": "string",
      "metadata": {
        "description": "Name for the diagnostic setting resource. Eg. 'archiveToStorage' or 'forSecurityTeam'."
      }
    },
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Storage Account in which Diagnostic Logs should be saved."
      }
    },
    "eventHubAuthorizationRuleId": {
      "type": "string",
      "metadata": {
        "description": "Resource ID of the event hub authorization rule for the Event Hubs namespace in which the event hub should be created or streamed to."
      }
    },
    "eventHubName": {
      "type": "string",
      "metadata": {
        "description": "Optional. Name of the event hub within the namespace to which logs are streamed. Without this, an event hub is created for each log category."
      }
    },
    "workspaceId":{
      "type": "string",
      "metadata": {
        "description": "Azure Resource ID of the Log Analytics workspace for the Log Analytics workspace to which logs will be sent."
      }
    }
    ```
2. Lägg till en resurs av typen i matrisen resurser av den resurs som du vill aktivera diagnostikloggar `[resource namespace]/providers/diagnosticSettings`.
   
    ```json
    "resources": [
      {
        "type": "providers/diagnosticSettings",
        "name": "[concat('Microsoft.Insights/', parameters('settingName'))]",
        "dependsOn": [
          "[/*resource Id for which Diagnostic Logs will be enabled>*/]"
        ],
        "apiVersion": "2017-05-01-preview",
        "properties": {
          "name": "[parameters('settingName')]",
          "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
          "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
          "eventHubName": "[parameters('eventHubName')]",
          "workspaceId": "[parameters('workspaceId')]",
          "logs": [ 
            {
              "category": "/* log category name */",
              "enabled": true,
              "retentionPolicy": {
                "days": 0,
                "enabled": false
              }
            }
          ],
          "metrics": [
            {
              "category": "AllMetrics",
              "enabled": true,
              "retentionPolicy": {
                "enabled": false,
                "days": 0
              }
            }
          ]
        }
      }
    ]
    ```

Egenskaper för blob för Diagnostikinställningen följer [formatet som beskrivs i den här artikeln](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings/createorupdate). Att lägga till den `metrics` egenskapen gör att du kan också skicka Resursmått till dessa samma utdata, förutsatt att [resursen har stöd för Azure Monitor metrics](monitoring-supported-metrics.md).

Här är ett fullständigt exempel som skapar en Logikapp och aktiverar direktuppspelning till Event Hubs och lagringsutrymme i ett lagringskonto.

```json

{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "logicAppName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Logic App that will be created."
      }
    },
    "testUri": {
      "type": "string",
      "defaultValue": "http://azure.microsoft.com/status/feed/"
    },
    "settingName": {
      "type": "string",
      "metadata": {
        "description": "Name of the setting. Name for the diagnostic setting resource. Eg. 'archiveToStorage' or 'forSecurityTeam'."
      }
    },
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Storage Account in which Diagnostic Logs should be saved."
      }
    },
    "eventHubAuthorizationRuleId": {
      "type": "string",
      "metadata": {
        "description": "Resource ID of the event hub authorization rule for the Event Hubs namespace in which the event hub should be created or streamed to."
      }
    },
    "eventHubName": {
      "type": "string",
      "metadata": {
        "description": "Optional. Name of the event hub within the namespace to which logs are streamed. Without this, an event hub is created for each log category."
      }
    },
    "workspaceId": {
      "type": "string",
      "metadata": {
        "description": "Log Analytics workspace ID for the Log Analytics workspace to which logs will be sent."
      }
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Logic/workflows",
      "name": "[parameters('logicAppName')]",
      "apiVersion": "2016-06-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "definition": {
          "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "testURI": {
              "type": "string",
              "defaultValue": "[parameters('testUri')]"
            }
          },
          "triggers": {
            "recurrence": {
              "type": "recurrence",
              "recurrence": {
                "frequency": "Hour",
                "interval": 1
              }
            }
          },
          "actions": {
            "http": {
              "type": "Http",
              "inputs": {
                "method": "GET",
                "uri": "@parameters('testUri')"
              },
              "runAfter": {}
            }
          },
          "outputs": {}
        },
        "parameters": {}
      },
      "resources": [
        {
          "type": "providers/diagnosticSettings",
          "name": "[concat('Microsoft.Insights/', parameters('settingName'))]",
          "dependsOn": [
            "[resourceId('Microsoft.Logic/workflows', parameters('logicAppName'))]"
          ],
          "apiVersion": "2017-05-01-preview",
          "properties": {
            "name": "[parameters('settingName')]",
            "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
            "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
            "eventHubName": "[parameters('eventHubName')]",
            "workspaceId": "[parameters('workspaceId')]",
            "logs": [
              {
                "category": "WorkflowRuntime",
                "enabled": true,
                "retentionPolicy": {
                  "days": 0,
                  "enabled": false
                }
              }
            ],
            "metrics": [
              {
                "timeGrain": "PT1M",
                "enabled": true,
                "retentionPolicy": {
                  "enabled": false,
                  "days": 0
                }
              }
            ]
          }
        }
      ],
      "dependsOn": []
    }
  ]
}

```

## <a name="compute-resource-template"></a>Compute-resursmall
Om du vill aktivera diagnostik på en beräkningsresurs, till exempel en virtuell dator eller Service Fabric-kluster måste du:

1. Lägg till Azure Diagnostics-tillägget i VM-resursdefinitionen.
2. Ange en storage-konto och/eller event hub som en parameter.
3. Lägg till innehållet i filen WADCfg XML i egenskapen XMLCfg undantagstecken alla XML-tecknen korrekt.

> [!WARNING]
> Det sista steget kan vara svårt att få rätt. [Se den här artikeln](../virtual-machines/extensions/diagnostics-template.md#diagnostics-configuration-variables) ett exempel som delar upp konfigurationsschema diagnostik i variabler som är undantagna och formaterats korrekt.
> 
> 

Hela processen, inklusive exempel beskrivs [i det här dokumentet](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="next-steps"></a>Nästa steg
* [Läs mer om Azure-diagnostikloggar](monitoring-overview-of-diagnostic-logs.md)
* [Stream Azure diagnostikloggar till Event Hubs](monitoring-stream-diagnostic-logs-to-event-hubs.md)

