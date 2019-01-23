---
title: Skicka gäst-OS mått av Azure Monitor-måtten lagra med en Resource Manager-mall för en Windows-dator
description: Skicka OS gästmått av Azure Monitor-måtten lagra med en Resource Manager-mall för en Windows-dator
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: howto
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 49c37c5dd2910cda234c479dc624f6c6ae92f6c1
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54467935"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-using-a-resource-manager-template-for-a-windows-virtual-machine"></a>Skicka gäst-OS mått av Azure Monitor-måtten lagra med en Resource Manager-mall för en Windows-dator

Med hjälp av Azure Monitor [diagnostiktillägget](diagnostics-extension-overview.md), du kan samla in mått och loggar från gästoperativsystemet (gäst-OS) som körs som en del av en virtuell dator, en molntjänst eller ett Service Fabric-kluster. Tillägget kan skicka telemetri till [många olika platser.](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json)

Den här artikeln beskriver processen för att skicka gäst-OS prestandamått för en virtuell Windows-dator till Azure Monitor-datalager. Från och med diagnostik version 1.11, kan du skriva mått direkt till i Azure Monitor mått store, där standardplattform mått är redan har samlats in.

Lagra dem i den här platsen kan du komma åt samma åtgärder för mått för plattformen. Åtgärder omfattar nästan i realtid avisering, diagram, Routning och åtkomst till från ett REST-API och mycket mer. Tidigare skrev Diagnostics-tillägg till Azure Storage, men inte till Azure Monitor-datalager.

Om du är nybörjare på Resource Manager-mallar kan du läsa om [malldistributioner](../../azure-resource-manager/resource-group-overview.md) och deras struktur och syntax.

## <a name="prerequisites"></a>Förutsättningar

- Prenumerationen måste vara registrerad med [Microsoft.Insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services#portal).

- Du måste ha antingen [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) eller [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) installerad.


## <a name="set-up-azure-monitor-as-a-data-sink"></a>Konfigurera Azure Monitor som mellanlagringsplats
Azure Diagnostics-tillägget använder en funktion som kallas ”data egenskaperna” till vägens mått och loggar på olika platser. Följande steg visar hur du använder en Resource Manager-mall och PowerShell för att distribuera en virtuell dator med hjälp av den nya ”Azure Monitor” mellanlagringsplatsen.

## <a name="author-resource-manager-template"></a>Skapa Resource Manager-mall
Du kan använda en offentligt tillgänglig exempelmall för det här exemplet. Från mallarna är på https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows.

- **Azuredeploy.JSON** är förkonfigurerade Resource Manager-mall för distribution av en virtuell dator.

- **Azuredeploy.parameters.JSON** är en fil med parametrar som lagrar information, till exempel vilka användarnamn och lösenord du vill ange för den virtuella datorn. Under distributionen av använder Resource Manager-mallen de parametrar som anges i den här filen.

Ladda ned och spara filerna lokalt.

### <a name="modify-azuredeployparametersjson"></a>Modify azuredeploy.parameters.json
Öppna den *azuredeploy.parameters.json* fil

1. Ange värden för **adminUsername** och **adminPassword** för den virtuella datorn. Dessa parametrar används för fjärråtkomst till den virtuella datorn. Om du vill undvika att den virtuella datorn som uppsnappas, använda inte värdena i den här mallen. Robotar skanna internet för användarnamn och lösenord i offentliga GitHub-lagringsplatser. Det är troligt att testa virtuella datorer med standardinställningarna.

1. Skapa ett unikt DNS-namn för den virtuella datorn.

### <a name="modify-azuredeployjson"></a>Ändra azuredeploy.json

Öppna den *azuredeploy.json* fil

Lägg till ett storage-konto-ID till den **variabler** avsnitt i mallen när posten för **storageAccountName.**

```json
// Find these lines.
"variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'sawinvm')]",

// Add this line directly below.
    "accountid": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
```

Lägg till det här hanterad tjänstidentitet (MSI)-tillägget i mallen överst i den **resurser** avsnittet. Tillägget säkerställer att Azure Monitor accepterar de mått som genereras.

```json
//Find this code.
"resources": [
// Add this code directly below.
    {
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "WADExtensionSetup",
        "apiVersion": "2015-05-01-preview",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]" ],
        "properties": {
            "publisher": "Microsoft.ManagedIdentity",
            "type": "ManagedIdentityExtensionForWindows",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "port": 50342
            }
        }
    },
```

Lägg till den **identitet** konfiguration för att den Virtuella datorresursen så att Azure tilldelar en systemidentitet till MSI-tillägget. Det här steget säkerställer att den virtuella datorn kan generera gästmått om sig själv till Azure Monitor.

```json
// Find this section
                "subnet": {
            "id": "[variables('subnetRef')]"
            }
        }
        }
    ]
    }
},
{
    "apiVersion": "2017-03-30",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    // add these 3 lines below
    "identity": {
    "type": "SystemAssigned"
    },
    //end of added lines
    "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
    "hardwareProfile": {
    ...
```

Lägg till följande konfiguration för att aktivera diagnostiktillägget på en Windows-dator. För en enkel Resource Manager-baserad virtuell dator, kan vi lägga till tilläggskonfigurationen resurser matrisen för den virtuella datorn. Raden ”mottagare”&mdash; ”AzMonSink” och motsvarande ”SinksConfig” senare i avsnittet&mdash;aktivera tillägget att skapa mått direkt till Azure Monitor. Passa på att lägga till eller ta bort prestandaräknare efter behov.


```json
        "networkProfile": {
            "networkInterfaces": [
            {
                "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
            }
            ]
        },
"diagnosticsProfile": {
    "bootDiagnostics": {
    "enabled": true,
    "storageUri": "[reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob]"
    }
}
},
//Start of section to add
"resources": [
{
            "type": "extensions",
            "name": "Microsoft.Insights.VMDiagnosticsSettings",
            "apiVersion": "2015-05-01-preview",
            "location": "[resourceGroup().location]",
            "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
            ],
            "properties": {
            "publisher": "Microsoft.Azure.Diagnostics",
            "type": "IaaSDiagnostics",
            "typeHandlerVersion": "1.12",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "WadCfg": {
                "DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "DiagnosticInfrastructureLogs": {
                    "scheduledTransferLogLevelFilter": "Error"
        },
                    "Directories": {
                    "scheduledTransferPeriod": "PT1M",
    "IISLogs": {
                        "containerName": "wad-iis-logfiles"
                    },
                    "FailedRequestLogs": {
                        "containerName": "wad-failedrequestlogs"
                    }
                    },
                    "PerformanceCounters": {
                    "scheduledTransferPeriod": "PT1M",
                    "sinks": "AzMonSink",
                    "PerformanceCounterConfiguration": [
                        {
                        "counterSpecifier": "\\Memory\\Available Bytes",
                        "sampleRate": "PT15S"
                        },
                        {
                        "counterSpecifier": "\\Memory\\% Committed Bytes In Use",
                        "sampleRate": "PT15S"
                        },
                        {
                        "counterSpecifier": "\\Memory\\Committed Bytes",
                        "sampleRate": "PT15S"
                        }
                    ]
                    },
                    "WindowsEventLog": {
                    "scheduledTransferPeriod": "PT1M",
                    "DataSource": [
                        {
                        "name": "Application!*"
                        }
                    ]
                    },
                    "Logs": {
                    "scheduledTransferPeriod": "PT1M",
                    "scheduledTransferLogLevelFilter": "Error"
                    }
                },
                "SinksConfig": {
                    "Sink": [
                    {
                        "name" : "AzMonSink",
                        "AzureMonitor" : {}
                    }
                    ]
                }
                },
                "StorageAccount": "[variables('storageAccountName')]"
            },
            "protectedSettings": {
                "storageAccountName": "[variables('storageAccountName')]",
                "storageAccountKey": "[listKeys(variables('accountid'),'2015-06-15').key1]",
                "storageAccountEndPoint": "https://core.windows.net/"
            }
            }
        }
        ]
//End of section to add
```


Spara och Stäng båda filerna.


## <a name="deploy-the-resource-manager-template"></a>Distribuera Resource Manager-mall

> [!NOTE]
> Du måste köra Azure Diagnostics-tilläggets version 1.5 eller senare och har den **autoUpgradeMinorVersion**: egenskapen angiven till ”SANT” i Resource Manager-mallen. Azure läser sedan in rätt tillägg när den startas den virtuella datorn. Om du inte har de här inställningarna i mallen kan ändra dem och distribuerar om mallen.


För att distribuera Resource Manager-mall, kan vi använda Azure PowerShell.

1. Starta PowerShell.
1. Logga in på Azure med hjälp av `Login-AzureRmAccount`.
1. Hämta listan över prenumerationer genom att använda `Get-AzureRmSubscription`.
1. Ange den prenumeration som du använder för att skapa/uppdatera den virtuella datorn i:

   ```PowerShell
   Select-AzureRmSubscription -SubscriptionName "<Name of the subscription>"
   ```
1. Skapa en ny resursgrupp för den virtuella datorn som distribueras genom att köra följande kommando:

   ```PowerShell
    New-AzureRmResourceGroup -Name "<Name of Resource Group>" -Location "<Azure Region>"
   ```
   > [!NOTE]
   > Kom ihåg att [använder en Azure-region som är aktiverad för anpassade mått](metrics-custom-overview.md).

1. Kör följande kommandon för att distribuera den virtuella datorn med hjälp av Resource Manager-mall.
   > [!NOTE]
   > Om du vill uppdatera en befintlig virtuell dator kan bara lägga till *-läge inkrementella* i slutet av följande kommando.

   ```PowerShell
   New-AzureRmResourceGroupDeployment -Name "<NameThisDeployment>" -ResourceGroupName "<Name of the Resource Group>" -TemplateFile "<File path of your Resource Manager template>" -TemplateParameterFile "<File path of your parameters file>"
   ```

1. Efter distributionen lyckas ska den virtuella datorn i Azure-portalen avger mått till Azure Monitor.

   > [!NOTE]
   > Du kanske stöter på fel runt den valda vmSkuSize. Om det händer kan du gå tillbaka till filen azuredeploy.json och uppdatera standardvärdet för parametern vmSkuSize. I det här fallet rekommenderar vi försöker ”Standard_DS1_v2”).

## <a name="chart-your-metrics"></a>Skapa diagram över dina mått

1. Logga in på Azure Portal.

2. På menyn till vänster väljer **övervakaren**.

3. På sidan övervakaren väljer **mått**.

   ![Mått-sidan](media/collect-custom-metrics-guestos-resource-manager-vm/metrics.png)

4. Ändra sammanställningsperioden till **senaste 30 minuterna**.

5. Välj den virtuella datorn som du skapade i listrutan resurs. Om du inte ändrar namnet i mallen, bör den vara *SimpleWinVM2*.

6. Välj i listrutan namnområden **azure.vm.windows.guest**

7. Mått nedrullningsbara menyn, Välj **minne\%allokerade byte som används**.


## <a name="next-steps"></a>Nästa steg
- Läs mer om [anpassade mått](metrics-custom-overview.md).

