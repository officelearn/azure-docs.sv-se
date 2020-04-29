---
title: Samla in Windows VM-mått i Azure Monitor med mall
description: Skicka gäst operativ systemets mått till Azure Monitor Metric Store med hjälp av en Resource Manager-mall för en virtuell Windows-dator
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: e747ca89912c36538bfb9d02986629fe57c5adcb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "77657375"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-using-a-resource-manager-template-for-a-windows-virtual-machine"></a>Skicka gäst operativ systemets mått till Azure Monitor Metric Store med hjälp av en Resource Manager-mall för en virtuell Windows-dator

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Med hjälp av [tillägget Azure Monitor diagnostik](diagnostics-extension-overview.md)kan du samla in mått och loggar från gäst operativ systemet (gäst operativ system) som körs som en del av en virtuell dator, moln tjänst eller Service Fabric kluster. Tillägget kan skicka telemetri till [flera olika platser.](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json)

Den här artikeln beskriver processen för att skicka gäst operativ systemets prestanda mått för en virtuell Windows-dator till Azure Monitor data lager. Från och med diagnostik version 1,11 kan du skriva mått direkt till lagrings platsen Azure Monitor mått, där standard plattforms mått redan har samlats in.

Genom att lagra dem på den här platsen kan du komma åt samma åtgärder för plattforms mått. Åtgärder är nästan real tids avisering, diagram, Routning och åtkomst från en REST API med mera. Tidigare skrev diagnostikprogrammet-tillägget till Azure Storage, men inte till Azure Monitor data lager.

Om du är nybörjare på Resource Manager-mallar kan du läsa mer om [mall distributioner](../../azure-resource-manager/management/overview.md) och deras struktur och syntax.

## <a name="prerequisites"></a>Krav

- Din prenumeration måste vara registrerad med [Microsoft. Insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services).

- Du måste antingen ha [Azure PowerShell](/powershell/azure) eller [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) installerat.

- Din VM-resurs måste finnas i en [region som stöder anpassade mått](metrics-custom-overview.md#supported-regions). 


## <a name="set-up-azure-monitor-as-a-data-sink"></a>Konfigurera Azure Monitor som en data mottagare
Azure-diagnostik-tillägget använder en funktion som kallas "data mottagare" för att dirigera mått och loggar till olika platser. Följande steg visar hur du använder en Resource Manager-mall och PowerShell för att distribuera en virtuell dator med hjälp av den nya "Azure Monitor"-data mottagaren.

## <a name="author-resource-manager-template"></a>Skapa Resource Manager-mall
I det här exemplet kan du använda en offentligt tillgänglig exempel mall. Startmallarna finns på https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows.

- **Azuredeploy. JSON** är en förkonfigurerad Resource Manager-mall för distribution av en virtuell dator.

- **Azuredeploy. Parameters. JSON** är en parameter fil som lagrar information, till exempel vilket användar namn och lösen ord som du vill ange för din virtuella dator. Under distributionen använder Resource Manager-mallen de parametrar som anges i den här filen.

Ladda ned och spara båda filerna lokalt.

### <a name="modify-azuredeployparametersjson"></a>Ändra azuredeploy. Parameters. JSON
Öppna filen *azuredeploy. Parameters. JSON*

1. Ange värden för **adminUsername** och **adminPassword** för den virtuella datorn. Dessa parametrar används för fjärråtkomst till den virtuella datorn. Använd inte värdena i den här mallen för att undvika att den virtuella datorn har kapats. Robotar Genomsök Internet efter användar namn och lösen ord i offentliga GitHub-databaser. De kommer förmodligen att testa virtuella datorer med dessa standardvärden.

1. Skapa en unik dnsname för den virtuella datorn.

### <a name="modify-azuredeployjson"></a>Ändra azuredeploy. JSON

Öppna filen *azuredeploy. JSON*

Lägg till ett lagrings konto-ID i avsnittet **variabler** i mallen efter posten för **storageAccountName.**

```json
// Find these lines.
"variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'sawinvm')]",

// Add this line directly below.
    "accountid": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
```

Lägg till det här Hanterad tjänstidentitet (MSI)-tillägget i mallen överst i avsnittet **resurser** . Tillägget säkerställer att Azure Monitor accepterar de mått som genereras.

```json
//Find this code.
"resources": [
// Add this code directly below.
    {
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "[concat(variables('vmName'), '/', 'WADExtensionSetup')]",
        "apiVersion": "2017-12-01",
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

Lägg till **identitets** konfigurationen i VM-resursen för att säkerställa att Azure tilldelar en system identitet till MSI-tillägget. Det här steget säkerställer att den virtuella datorn kan generera gäst mått om sig själv för att Azure Monitor.

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

Lägg till följande konfiguration för att aktivera diagnostikprogrammet-tillägget på en virtuell Windows-dator. För en enkel Resource Manager-baserad virtuell dator kan vi lägga till tilläggs konfigurationen till resurs-matrisen för den virtuella datorn. Raden "Sinks"&mdash; "AzMonSink" och motsvarande "SinksConfig" senare i avsnittet&mdash;aktiverar tillägget för att generera mått direkt till Azure Monitor. Du kan lägga till eller ta bort prestanda räknare efter behov.


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
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(variables('vmName'), '/', 'Microsoft.Insights.VMDiagnosticsSettings')]",
            "apiVersion": "2017-12-01",
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


## <a name="deploy-the-resource-manager-template"></a>Distribuera Resource Manager-mallen

> [!NOTE]
> Du måste köra Azure-diagnostik-tillägget version 1,5 eller högre och ha egenskapen **aktiverat autoupgrademinorversion**: inställd på True i Resource Manager-mallen. Azure läser sedan in rätt tillägg när den virtuella datorn startas. Om du inte har de här inställningarna i mallen ändrar du dem och distribuerar om mallen.


För att distribuera Resource Manager-mallen utnyttjar vi Azure PowerShell.

1. Starta PowerShell.
1. Logga in på Azure med `Login-AzAccount`.
1. Hämta din lista över prenumerationer med `Get-AzSubscription`hjälp av.
1. Ange den prenumeration som du använder för att skapa/uppdatera den virtuella datorn i:

   ```powershell
   Select-AzSubscription -SubscriptionName "<Name of the subscription>"
   ```
1. Kör följande kommando för att skapa en ny resurs grupp för den virtuella dator som distribueras:

   ```powershell
    New-AzResourceGroup -Name "<Name of Resource Group>" -Location "<Azure Region>"
   ```
   > [!NOTE]
   > Kom ihåg att [använda en Azure-region som är aktive rad för anpassade mått](metrics-custom-overview.md).

1. Kör följande kommandon för att distribuera den virtuella datorn med Resource Manager-mallen.
   > [!NOTE]
   > Om du vill uppdatera en befintlig virtuell dator, lägger du bara till *läges ökning* i slutet av följande kommando.

   ```powershell
   New-AzResourceGroupDeployment -Name "<NameThisDeployment>" -ResourceGroupName "<Name of the Resource Group>" -TemplateFile "<File path of your Resource Manager template>" -TemplateParameterFile "<File path of your parameters file>"
   ```

1. När distributionen har slutförts bör den virtuella datorn finnas i Azure Portal som avger mått till Azure Monitor.

   > [!NOTE]
   > Du kan stöta på fel kring de valda vmSkuSize. Om detta händer går du tillbaka till filen azuredeploy. JSON och uppdaterar standardvärdet för parametern vmSkuSize. I det här fallet rekommenderar vi att du testar "Standard_DS1_v2").

## <a name="chart-your-metrics"></a>Diagrammets mått

1. Logga in på Azure Portal.

2. På den vänstra menyn väljer du **övervaka**.

3. Välj **mått**på sidan övervaka.

   ![Sidan mått](media/collect-custom-metrics-guestos-resource-manager-vm/metrics.png)

4. Ändra agg regerings perioden till de **senaste 30 minuterna**.

5. I list menyn resurs väljer du den virtuella dator som du skapade. Om du inte ändrade namnet i mallen bör det vara *SimpleWinVM2*.

6. I den nedrullningsbara menyn namn områden väljer du **Azure. VM. Windows. gäst**

7. I den nedrullningsbara menyn mått väljer du **minnes\%allokerade byte som används**.


## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [anpassade mått](metrics-custom-overview.md).

