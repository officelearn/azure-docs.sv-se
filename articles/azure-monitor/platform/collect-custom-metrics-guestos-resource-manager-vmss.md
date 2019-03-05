---
title: Skicka gäst-OS mått till arkivet som Azure Monitor-mått med en Azure Resource Manager-mall för en Windows VM-skalningsuppsättning
description: Skicka gäst-OS mått till arkivet som Azure Monitor-mått med hjälp av Resource Manager-mall för en Windows VM-skalningsuppsättning
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: howto
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: afe1214299b7f1ecd741f992af75abedcfe77b84
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2019
ms.locfileid: "57308711"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-by-using-an-azure-resource-manager-template-for-a-windows-virtual-machine-scale-set"></a>Skicka gäst-OS mått till arkivet som Azure Monitor-mått med en Azure Resource Manager-mall för en Windows VM-skalningsuppsättning

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Med hjälp av Azure Monitor [Windows Azure Diagnostics SÄKERHETSSPECIFIKA tillägget](diagnostics-extension-overview.md), du kan samla in mått och loggar från gästoperativsystemet (gästoperativsystem) som körs som en del av en virtuell dator, molntjänst eller Azure Service Fabric-kluster. Tillägget kan skicka telemetri till många olika platser som anges i tidigare länkade artikeln.  

Den här artikeln beskriver processen för att skicka guest OS-prestandamått för en Windows skalningsuppsättning för virtuella datorer till Azure Monitor-datalager. Från och med Windows Azure Diagnostics version 1.11 kan skriva du mått direkt till Azure Monitor-mått som lagrar, där standardplattform mått är redan har samlats in. Genom att lagra dem på den här platsen, kan du komma åt samma åtgärder som är tillgängliga för plattformen mått. Åtgärderna är bland annat nära avisering i realtid, diagram, routning, komma åt från REST API och mycket mer. Tidigare skrev Windows Azure Diagnostics-tillägget Azure Storage, men inte Azure Monitor-datalagret.  

Om du är nybörjare på Resource Manager-mallar kan du läsa om [malldistributioner](../../azure-resource-manager/resource-group-overview.md) och deras struktur och syntax.  

## <a name="prerequisites"></a>Förutsättningar

- Prenumerationen måste vara registrerad med [Microsoft.Insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services). 

- Du måste ha [Azure PowerShell](/powershell/azure) installerad, eller så kan du använda [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview). 


## <a name="set-up-azure-monitor-as-a-data-sink"></a>Konfigurera Azure Monitor som mellanlagringsplats 
Azure Diagnostics-tillägget använder en funktion som kallas **datamottagare** till vägens mått och loggar på olika platser. Följande steg visar hur du använder en Resource Manager-mall och PowerShell för att distribuera en virtuell dator med hjälp av Azure Monitor-mellanlagringsplatsen för nya data. 

## <a name="author-a-resource-manager-template"></a>Skapa en Resource Manager-mall 
För det här exemplet kan du använda ett offentligt tillgängligt [exempelmallen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-autoscale):  

- **Azuredeploy.JSON** är förkonfigurerade Resource Manager-mall för distribution av en VM-skalningsuppsättning.

- **Azuredeploy.parameters.JSON** är en fil med parametrar som lagrar information, till exempel användarnamn och lösenord för vilka du vill ange för den virtuella datorn. Under distributionen av använder Resource Manager-mallen parametrarna som anges i den här filen. 

Ladda ned och spara filerna lokalt. 

###  <a name="modify-azuredeployparametersjson"></a>Modify azuredeploy.parameters.json
Öppna den **azuredeploy.parameters.json** fil:  
 
- Ange en **vmSKU** du vill distribuera. We recommend Standard_D2_v3. 
- Ange en **windowsOSVersion** du vill använda för din skalningsuppsättning för virtuell dator. Vi rekommenderar att 2016-Datacenter. 
- Namn på VM-skalningsuppsättningen resurs som ska distribueras med hjälp av en **vmssName** egenskapen. Ett exempel är **VMSS-WAD-TEST**.    
- Ange hur många virtuella datorer du vill köra på VM-skalningsuppsättning med hjälp av den **instanceCount** egenskapen.
- Ange värden för **adminUsername** och **adminPassword** för VM-skalningsuppsättningen. Dessa parametrar används för fjärråtkomst till de virtuella datorerna i skalningsuppsättningen. För att förhindra att den virtuella datorn som uppsnappas, **inte** använda dem i den här mallen. Robotar skanna internet för användarnamn och lösenord i offentliga GitHub-lagringsplatser. Det är troligt att testa virtuella datorer med standardinställningarna. 


###  <a name="modify-azuredeployjson"></a>Ändra azuredeploy.json
Öppna den **azuredeploy.json** fil. 

Lägg till en variabel som innehåller informationen om lagring i Resource Manager-mallen. Alla loggar eller prestandaräknare som angetts i konfigurationsfilen diagnostik har skrivits till både arkivet som Azure Monitor-mått och storage-konto som du anger här: 

```json
"variables": { 
//add this line       
"storageAccountName": "[concat('storage', uniqueString(resourceGroup().id))]", 
```
 
Hitta VM-skalningsuppsättningen principuppsättningsdefinition i resursavsnittet och lägga till den **identitet** avsnittet i konfigurationen. Detta säkerställer att Azure tilldelar den till en systemidentitet. Det här steget säkerställer också att de virtuella datorerna i skalningsuppsättningen kan generera gästmått om sig själva till Azure Monitor:  

```json
    { 
      "type": "Microsoft.Compute/virtualMachineScaleSets", 
      "name": "[variables('namingInfix')]", 
      "location": "[resourceGroup().location]", 
      "apiVersion": "2017-03-30", 
      //add these lines below
      "identity": { 
           "type": "systemAssigned" 
       }, 
       //end of lines to add
```

I virtual machine scale Sets resurs, hitta den **virtualMachineProfile** avsnittet. Lägg till en ny profil med namnet **extensionsProfile** att hantera tillägg.  


I den **extensionProfile**, lägga till ett nytt tillägg i mallen, enligt den **VMSS-WAD-extension** avsnittet.  Det här avsnittet är hanterade identiteter för tillägget för Azure-resurser som säkerställer att de mått som genereras accepteras av Azure Monitor. Den **namn** fältet kan innehålla valfritt namn. 

Följande kod från MSI-tillägget lägger till diagnostiktillägget och konfigurationen som en resurs för tillägget scale set-resursen för virtuella datorer. Passa på att lägga till eller ta bort prestandaräknare efter behov: 

```json
          "extensionProfile": { 
            "extensions": [ 
            // BEGINNING of added code  
            // Managed identities for Azure resources   
                { 
                 "name": "VMSS-WAD-extension", 
                 "properties": { 
                       "publisher": "Microsoft.ManagedIdentity", 
                       "type": "ManagedIdentityExtensionForWindows", 
                       "typeHandlerVersion": "1.0", 
                       "autoUpgradeMinorVersion": true, 
                       "settings": { 
                             "port": 50342 
                           }, 
                       "protectedSettings": {} 
                     } 
                                
            }, 
            // add diagnostic extension. (Remove this comment after pasting.)
            { 
              "name": "[concat('VMDiagnosticsVmExt','_vmNodeType0Name')]", 
              "properties": { 
                   "type": "IaaSDiagnostics", 
                   "autoUpgradeMinorVersion": true, 
                   "protectedSettings": { 
                        "storageAccountName": "[variables('storageAccountName')]", 
                        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')),'2015-05-01-preview').key1]", 
                        "storageAccountEndPoint": "https://core.windows.net/" 
                   }, 
                   "publisher": "Microsoft.Azure.Diagnostics", 
                   "settings": { 
                        "WadCfg": { 
                              "DiagnosticMonitorConfiguration": { 
                                   "overallQuotaInMB": "50000", 
                                   "PerformanceCounters": { 
                                       "scheduledTransferPeriod": "PT1M", 
                                       "sinks": "AzMonSink", 
                                       "PerformanceCounterConfiguration": [ 
                                          { 
              "counterSpecifier": "\\Memory\\% Committed Bytes In Use", 
                                              "sampleRate": "PT15S" 
           }, 
           { 
              "counterSpecifier": "\\Memory\\Available Bytes", 
              "sampleRate": "PT15S" 
           }, 
           { 
              "counterSpecifier": "\\Memory\\Committed Bytes", 
              "sampleRate": "PT15S" 
           } 
                                       ] 
                                 }, 
                                 "EtwProviders": { 
                                       "EtwEventSourceProviderConfiguration": [ 
                                           { 
                                              "provider": "Microsoft-ServiceFabric-Actors", 
                                              "scheduledTransferKeywordFilter": "1", 
                                              "scheduledTransferPeriod": "PT5M", 
                                              "DefaultEvents": { 
                                              "eventDestination": "ServiceFabricReliableActorEventTable" 
                                           } 
                                           }, 
                                           { 
                                              "provider": "Microsoft-ServiceFabric-Services", 
                                              "scheduledTransferPeriod": "PT5M", 
                                              "DefaultEvents": { 
                                                   "eventDestination": "ServiceFabricReliableServiceEventTable" 
                                              } 
                                           } 
                                     ], 
                                     "EtwManifestProviderConfiguration": [ 
                                           { 
                                              "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8", 
                                              "scheduledTransferLogLevelFilter": "Information", 
                                              "scheduledTransferKeywordFilter": "4611686018427387904", 
                                              "scheduledTransferPeriod": "PT5M", 
                                              "DefaultEvents": { 
                                                   "eventDestination": "ServiceFabricSystemEventTable" 
                                              } 
                                          } 
                                     ] 
                               } 
                               }, 
                               "SinksConfig": { 
                                     "Sink": [ 
                                          { 
                                              "name": "AzMonSink", 
                                              "AzureMonitor": {} 
                                          } 
                                      ] 
                               } 
                         }, 
                         "StorageAccount": "[variables('storageAccountName')]" 
                         }, 
                        "typeHandlerVersion": "1.11" 
                  } 
           } 
            ] 
          }
          }
      }
    },
    //end of added code plus a few brackets. Be sure that the number and type of brackets match properly when done. 
    {
      "type": "Microsoft.Insights/autoscaleSettings",
...
```


Lägg till en **dependsOn** för lagringskontot så att den har skapats i rätt ordning: 

```json
"dependsOn": [ 
"[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]", 
"[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]" 
//add this line below
"[concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]" 
```

Skapa ett storage-konto om något inte redan har skapat i mallen: 

```json
"resources": [
// add this code    
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2015-05-01-preview",
    "location": "[resourceGroup().location]",
    "properties": {
    "accountType": "Standard_LRS"
    }
},
// end added code
{ 
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[variables('virtualNetworkName')]",
```

Spara och Stäng båda filerna. 

## <a name="deploy-the-resource-manager-template"></a>Distribuera Resource Manager-mall 

> [!NOTE]  
> Du måste köra Azure Diagnostics-tilläggets version 1.5 eller senare **och** har den **autoUpgradeMinorVersion:** egenskapen **SANT** i Resource Manager mallen. Azure läser sedan in rätt tillägg när den startas den virtuella datorn. Om du inte har de här inställningarna i mallen kan ändra dem och distribuerar om mallen. 


Använd Azure PowerShell för att distribuera Resource Manager-mallen:  

1. Starta PowerShell. 
1. Logga in på Azure med `Login-AzAccount`.
1. Hämta listan över prenumerationer genom att använda `Get-AzSubscription`.
1. Ange den prenumeration du ska skapa eller uppdatera den virtuella datorn: 

   ```PowerShell
   Select-AzSubscription -SubscriptionName "<Name of the subscription>" 
   ```
1. Skapa en ny resursgrupp för den virtuella datorn som ska distribueras. Kör följande kommando: 

   ```PowerShell
    New-AzResourceGroup -Name "VMSSWADtestGrp" -Location "<Azure Region>" 
   ```

   > [!NOTE]  
   > Kom ihåg att använda en Azure-region som är aktiverad för anpassade mått. Kom ihåg att använda en [Azure-region som är aktiverad för anpassade mått](https://github.com/MicrosoftDocs/azure-docs-pr/pull/metrics-custom-overview.md#supported-regions).
 
1. Kör följande kommandon för att distribuera den virtuella datorn:  

   > [!NOTE]  
   > Om du vill uppdatera en befintlig skalningsuppsättning, lägger du till **-läge inkrementella** i slutet av kommandot. 
 
   ```PowerShell
   New-AzResourceGroupDeployment -Name "VMSSWADTest" -ResourceGroupName "VMSSWADtestGrp" -TemplateFile "<File path of your azuredeploy.JSON file>" -TemplateParameterFile "<File path of your azuredeploy.parameters.JSON file>"  
   ```

1. När distributionen har slutförts bör du hitta skalningsuppsättning för virtuell dator i Azure-portalen. Det ska sända mått till Azure Monitor. 

   > [!NOTE]  
   > Du kanske stöter på fel runt den valda **vmSkuSize**. I så fall kan gå tillbaka till din **azuredeploy.json** filen och uppdatera standardvärdet för den **vmSkuSize** parametern. Vi rekommenderar att du försöker **Standard_DS1_v2**. 


## <a name="chart-your-metrics"></a>Skapa diagram över dina mått 

1. Logga in på Azure Portal. 

1. I den vänstra menyn och väljer **övervakaren**. 

1. På den **övervakaren** väljer **mått**. 

   ![Övervaka – mått sidan](media/collect-custom-metrics-guestos-resource-manager-vmss/metrics.png) 

1. Ändra sammanställningsperioden till **senaste 30 minuterna**.  

1. Välj virtuella datorns skalningsuppsättning som du skapade i listrutan resurs.  

1. Välj i listrutan namnområden **azure.vm.windows.guest**. 

1. Välj i listrutan mått **minne\%allokerade byte som används**.  

Du kan sedan också välja att använda dimensioner på det här måttet i diagrammet för en viss virtuell dator eller för att rita varje virtuell dator i skalningsuppsättningen. 



## <a name="next-steps"></a>Nästa steg
- Läs mer om [anpassade mått](metrics-custom-overview.md).


