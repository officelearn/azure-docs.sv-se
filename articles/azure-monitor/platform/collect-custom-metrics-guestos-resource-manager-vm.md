---
title: Samla in Windows VM-mått i Azure Monitor med mall
description: Skicka gäst-OS-mått till Azure Monitor-måttarkivet med hjälp av en Resource Manager-mall för en virtuell Windows-dator
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: e747ca89912c36538bfb9d02986629fe57c5adcb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77657375"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-using-a-resource-manager-template-for-a-windows-virtual-machine"></a>Skicka svar om gästoperativsystem till Azure Monitor-måttarkivet med hjälp av en Resource Manager-mall för en virtuell Windows-dator

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Genom att använda tillägget Azure Monitor [Diagnostics](diagnostics-extension-overview.md)kan du samla in mått och loggar från gästoperativsystemet (Guest OS) som körs som en del av ett virtuellt dator-, molntjänst- eller Service Fabric-kluster. Tillägget kan skicka telemetri till [många olika platser.](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json)

I den här artikeln beskrivs processen för att skicka prestandamått för gästoperativsystem för en virtuell Windows-dator till Azure Monitor-datalagret. Från och med Diagnostik version 1.11 kan du skriva mått direkt till Azure Monitor-måttarkivet, där standardplattformsmått redan har samlats in.

Genom att lagra dem på den här platsen kan du komma åt samma åtgärder för plattformsmått. Åtgärderna omfattar aviseringar i nära realtid, diagram, routning och åtkomst från ett REST API med mera. Tidigare skrev diagnostiktillägget till Azure Storage, men inte till Azure Monitor-datalagringen.

Om du inte har tidigare i Resource Manager-mallar kan du läsa om [malldistributioner](../../azure-resource-manager/management/overview.md) och deras struktur och syntax.

## <a name="prerequisites"></a>Krav

- Din prenumeration måste vara registrerad hos [Microsoft.Insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services).

- Du måste ha antingen [Azure PowerShell](/powershell/azure) eller [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) installerat.

- Vm-resursen måste finnas i en [region som stöder anpassade mått](metrics-custom-overview.md#supported-regions). 


## <a name="set-up-azure-monitor-as-a-data-sink"></a>Konfigurera Azure Monitor som en datamottagare
Azure Diagnostics-tillägget använder en funktion som kallas "datamottagare" för att dirigera mått och loggar till olika platser. Följande steg visar hur du använder en Resource Manager-mall och PowerShell för att distribuera en virtuell dator med hjälp av den nya datamottagaren "Azure Monitor".

## <a name="author-resource-manager-template"></a>Mall för Författarresurshanteraren
I det här exemplet kan du använda en allmänt tillgänglig exempelmall. Startmallarna finns https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windowspå .

- **Azuredeploy.json** är en förkonfigurerad Resource Manager-mall för distribution av en virtuell dator.

- **Azuredeploy.parameters.json** är en parameterfil som lagrar information som vilket användarnamn och lösenord du vill ange för den virtuella datorn. Under distributionen använder Resource Manager-mallen de parametrar som anges i den här filen.

Ladda ned och spara båda filerna lokalt.

### <a name="modify-azuredeployparametersjson"></a>Ändra azuredeploy.parameters.json
Öppna filen *azuredeploy.parameters.json*

1. Ange värden för **adminUsername** och **adminPassword** för den virtuella datorn. Dessa parametrar används för fjärråtkomst till den virtuella datorn. Använd INTE värdena i den här mallen för att undvika att den virtuella datorn kapas. Robotar skannar internet efter användarnamn och lösenord i offentliga GitHub-databaser. De kommer sannolikt att testa virtuella datorer med dessa standardvärden.

1. Skapa ett unikt dnsname för den virtuella datorn.

### <a name="modify-azuredeployjson"></a>Ändra azuredeploy.json

Öppna *filen azuredeploy.json*

Lägg till ett lagringskonto-ID i **variabelavsnittet** i mallen efter posten för **storageAccountName.**

```json
// Find these lines.
"variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'sawinvm')]",

// Add this line directly below.
    "accountid": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
```

Lägg till det här MSI-tillägget (Managed Service **resources** Identity) i mallen högst upp i resursavsnittet. Tillägget säkerställer att Azure Monitor accepterar de mått som släpps ut.

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

Lägg **identity** till identitetskonfigurationen i vm-resursen för att säkerställa att Azure tilldelar en systemidentitet till MSI-tillägget. Det här steget säkerställer att den virtuella datorn kan avge gästmått om sig själv till Azure Monitor.

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

Lägg till följande konfiguration för att aktivera diagnostiktillägget på en virtuell Windows-dator. För en enkel Resource Manager-baserad virtuell dator kan vi lägga till tilläggskonfigurationen i resursmatrisen för den virtuella datorn. Raden "sänker"&mdash; "AzMonSink" och motsvarande "SinksConfig"&mdash;senare i avsnittet gör det möjligt för tillägget att släppa ut mått direkt till Azure Monitor. Lägg gärna till eller ta bort prestandaräknare efter behov.


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


Spara och stäng båda filerna.


## <a name="deploy-the-resource-manager-template"></a>Distribuera resurshanteraren-mallen

> [!NOTE]
> Du måste köra Azure Diagnostics-tilläggsversionen 1.5 eller senare OCH ha **autoUpgradeMinorVersion:** egenskapen inställd på "true" i resource manager-mallen. Azure läser sedan in rätt tillägg när den startar den virtuella datorn. Om du inte har de här inställningarna i mallen ändrar du dem och distribuerar om mallen.


Om du vill distribuera Resource Manager-mallen utnyttjar vi Azure PowerShell.

1. Starta PowerShell.
1. Logga in på `Login-AzAccount`Azure med .
1. Få din lista över `Get-AzSubscription`prenumerationer med hjälp av .
1. Ange den prenumeration som du använder för att skapa/uppdatera den virtuella datorn i:

   ```powershell
   Select-AzSubscription -SubscriptionName "<Name of the subscription>"
   ```
1. Om du vill skapa en ny resursgrupp för den virtuella datorn som distribueras kör du följande kommando:

   ```powershell
    New-AzResourceGroup -Name "<Name of Resource Group>" -Location "<Azure Region>"
   ```
   > [!NOTE]
   > Kom ihåg att [använda en Azure-region som är aktiverad för anpassade mått](metrics-custom-overview.md).

1. Kör följande kommandon för att distribuera den virtuella datorn med hjälp av Resource Manager-mallen.
   > [!NOTE]
   > Om du vill uppdatera en befintlig virtuell dator lägger du bara till *-Inkrementellt läge i* slutet av följande kommando.

   ```powershell
   New-AzResourceGroupDeployment -Name "<NameThisDeployment>" -ResourceGroupName "<Name of the Resource Group>" -TemplateFile "<File path of your Resource Manager template>" -TemplateParameterFile "<File path of your parameters file>"
   ```

1. När distributionen lyckas bör den virtuella datorn finnas i Azure-portalen och avge mått till Azure Monitor.

   > [!NOTE]
   > Du kan stöta på fel runt den valda vmSkuSize. Om detta inträffar går du tillbaka till filen azuredeploy.json och uppdaterar standardvärdet för parametern vmSkuSize. I det här fallet rekommenderar vi att du försöker "Standard_DS1_v2").

## <a name="chart-your-metrics"></a>Kartlägga dina mätvärden

1. Logga in på Azure Portal.

2. Välj **Övervaka**på den vänstra menyn .

3. Välj **Mått**på sidan Övervakare .

   ![Sidan Mått](media/collect-custom-metrics-guestos-resource-manager-vm/metrics.png)

4. Ändra aggregeringsperioden till **Sista 30 minuterna**.

5. Välj den virtuella dator som du skapade på den nedrullningsbara resursmenyn. Om du inte har ändrat namnet i mallen bör det vara *SimpleWinVM2*.

6. Välj **azure.vm.windows.guest** på den nedrullningsbara menyn namnområden

7. I den nedrullningsbara menyn Mått väljer du **Minnesrestaserade\%byte i Använd**.


## <a name="next-steps"></a>Nästa steg
- Läs mer om [anpassade mått](metrics-custom-overview.md).

