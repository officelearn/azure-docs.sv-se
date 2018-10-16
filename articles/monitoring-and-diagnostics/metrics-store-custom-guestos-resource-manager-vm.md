---
title: Skicka OS gästmått av Azure Monitor-måtten lagra med en Resource Manager-mall för en Windows-dator
description: Skicka OS gästmått av Azure Monitor-måtten lagra med en Resource Manager-mall för en Windows-dator
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: howto
ms.date: 09/24/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: f3076054eb6e18eb5143a34ba558c1f9e43ea4a5
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2018
ms.locfileid: "49345194"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-using-a-resource-manager-template-for-a-windows-virtual-machine"></a>Skicka OS gästmått av Azure Monitor-måtten lagra med en Resource Manager-mall för en Windows-dator

Azure Monitor [Windows Azure-diagnostiktillägget](azure-diagnostics.md) (WAD) kan du samla in mått och loggar från gästoperativsystemet (gästoperativsystem) körs som en del av en virtuell dator, molntjänst eller Service Fabric-kluster.  Tillägget kan skicka telemetri till många olika platser som anges i tidigare länkade artikeln.  

Den här artikeln beskriver processen för att skicka gäst-OS prestandamått för en Windows-datorn till Azure Monitor-datalager. Från och med WAD version 1.11, kan du skriva mått direkt till Azure Monitor metrics store där standardplattform mått är redan har samlats in. Lagra dem i den här platsen kan du komma åt samma åtgärder som är tillgängliga för plattformen mått.  Åtgärder omfattar nästan i realtid avisering, diagram, routning, komma åt från REST API och mycket mer.  Tidigare skrev WAD tillägget till Azure Storage, men inte Azure Monitor-datalagret.   

Om du är nybörjare på Resource Manager-mallar kan du läsa om [malldistributioner](../azure-resource-manager/resource-group-overview.md), och deras struktur och syntax.  

## <a name="pre-requisites"></a>Förutsättningar

- Prenumerationen måste vara registrerad med [Microsoft.Insights](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) 

- Du måste ha antingen [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) installerad, eller så kan du använda [Azure CloudShell](https://docs.microsoft.com/azure/cloud-shell/overview.md) 

 
## <a name="set-up-azure-monitor-as-a-data-sink"></a>Konfigurera Azure Monitor som mellanlagringsplats 
Azure Diagnostics-tillägget använder en funktion som kallas ”data egenskaperna” till vägens mått och loggar på olika platser.  Följande steg visar hur du använder Resource Manager-mall och PowerShell för att distribuera en virtuell dator med den nya ”Azure Monitor” mellanlagringsplatsen. 

## <a name="author-resource-manager-template"></a>Skapa Resource Manager-mall 
Du kan använda en offentligt tillgänglig exempelmall för det här exemplet. Från mallarna är på https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows 

- **Azuredeploy.JSON** är en förkonfigurerad Resource Manager-mall för distribution av en virtuell dator. 

- **Azuredeploy.parameters.JSON** är en fil med parametrar som lagrar information, till exempel användarnamn och lösenord för vilka du vill ange för den virtuella datorn. Under distributionen av använder Resource Manager-mallen parametrarna som anges i den här filen. 

Ladda ned och spara filerna lokalt. 

###  <a name="modify-azuredeployparametersjson"></a>Ändra azuredeploy.parameters.json
Öppna den *azuredeploy.parameters.json* fil 

1. Ange värden för *adminUsername* och *adminPassword* för den virtuella datorn. Dessa parametrar används för fjärråtkomst till den virtuella datorn. Använd inte de i den här mallen för att förhindra att den virtuella datorn som uppsnappas. Robotar skanna internet för användarnamn och lösenord i offentliga Github-lagringsplatser. Det är troligt att testa virtuella datorer med standardinställningarna.  

1. Skapa ett unikt DNS-namn för den virtuella datorn.  

### <a name="modify-azuredeployjson"></a>Ändra azuredeploy.json

Öppna den *azuredeploy.json* fil 

Lägg till ett storage-konto-ID till den **variabler** avsnitt i mallen när posten för **storageAccountName**.  

```json
// Find these lines 
"variables": { 
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'sawinvm')]", 

// Add this line directly below.  
    "accountid": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]", 
```

Lägg till den här hanterade identiteter för tillägget för Azure-resurser i mallen överst i avsnittet ”resurser”.  Tillägget säkerställer att Azure Monitor accepterar de mått som genereras.  

```json
//Find this code 
"resources": [
// Add this code directly below
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

Lägga till ”identitet”-konfiguration för den Virtuella datorresursen så Azure tilldelar MSI-tillägget en systemidentitet. Det här steget säkerställer att den virtuella datorn kan generera gästmått om sig själv till Azure Monitor 

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

Lägg till följande konfiguration för att aktivera diagnostiktillägget på en Windows-dator.  För en Resource Manager-baserad virtuell dator och vi kan lägga till tilläggskonfigurationen resurser matrisen för den virtuella datorn. Raden ”mottagare”: ”AzMonSink” och den motsvarande ”SinksConfig” senare i avsnittet aktivera tillägget för att skapa mått direkt till Azure Monitor. Passa på att lägga till/ta bort räknare för serverprestanda.  


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
            "typeHandlerVersion": "1.4", 
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


Spara och Stäng båda filerna 
 

## <a name="deploy-the-resource-manager-template"></a>Distribuera Resource Manager-mall 

> [!NOTE]
> Du måste köra Azure Diagnostics-tilläggets version 1.5 eller senare och har ”autoUpgradeMinorVersion”: egenskapen angiven till ”SANT” i Resource Manager-mallen.  Azure läser sedan in rätt tillägg när den startas den virtuella datorn. Om du inte har de här inställningarna i mallen, ändra dem och distribuerar om mallen. 


Vi ska använda Azure PowerShell för att distribuera Resource Manager-mallen.  

1. Starta PowerShell 
1. Logga in på Azure med hjälp av `Login-AzureRmAccount`
1. Hämta listan över prenumerationer med hjälp av `Get-AzureRmSubscription`
1. Ange den prenumeration som du kommer att skapa/uppdatera den virtuella datorn i 

   ```PowerShell
   Select-AzureRmSubscription -SubscriptionName "<Name of the subscription>" 
   ```
1. Skapa en ny resursgrupp för den virtuella datorn distribueras, kör den nedanstående kommando 

   ```PowerShell
    New-AzureRmResourceGroup -Name "<Name of Resource Group>" -Location "<Azure Region>" 
   ```
   > [!NOTE] 
   > Kom ihåg att [använder en Azure-region som är aktiverad för anpassade mått](metrics-custom-overview.md). 
 
1. Kör följande kommandon för att distribuera den virtuella datorn med den  
   > [!NOTE] 
   > Om du vill uppdatera en befintlig virtuell dator kan bara lägga till *-läge inkrementella* i slutet av följande kommando. 
 
   ```PowerShell
   New-AzureRmResourceGroupDeployment -Name "<NameThisDeployment>" -ResourceGroupName "<Name of the Resource Group>" -TemplateFile "<File path of your Resource Manager template>" -TemplateParameterFile "<File path of your parameters file>" 
   ```
  
1. När distributionen lyckas du ska kunna hitta den virtuella datorn i Azure-portalen och den bör de mått för att Azure Monitor. 

   > [!NOTE] 
   > Du kanske stöter på fel runt den valda vmSkuSize. Om det händer kan du gå tillbaka till filen azuredeploy.json och uppdatera standardvärdet för parametern vmSkuSize. I det här fallet rekommenderar vi försöker ”Standard_DS1_v2”). 

## <a name="chart-your-metrics"></a>Skapa diagram över dina mått 

1. Logga in på Azure Portal 

1. I den vänstra menyn klickar du på **Övervakare** 

1. På sidan övervakaren **mått**. 

   ![Mått-sidan](./media/metrics-store-custom-rest-api/metrics.png) 

1. Ändra sammanställningsperioden till **senaste 30 minuterna**.  

1. I resource listrutan väljer du den virtuella datorn som nyss skapade. Om du inte ändrar namnet i mallen, bör den vara *SimpleWinVM2*.  

1. Välj i listrutan för namnområden **azure.vm.windows.guest** 

1. I mått som nedrullningsbar listruta, Välj **minne\%allokerade byte som används**.  
 

## <a name="next-steps"></a>Nästa steg
- Läs mer om [anpassade mått](metrics-custom-overview.md).