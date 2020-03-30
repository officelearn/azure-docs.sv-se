---
title: Samla in Windows-skalningsuppsättningsmått i Azure Monitor med mall
description: Skicka gäst-OS-mått till Azure Monitor-måttarkivet med hjälp av en Resource Manager-mall för en windows-skalningsuppsättning för virtuella datorer
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 24f83e4f6285d045e67bdaef431ebcff2345ef84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663904"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-by-using-an-azure-resource-manager-template-for-a-windows-virtual-machine-scale-set"></a>Skicka gästoperativsystemmått till Azure Monitor-måttarkivet med hjälp av en Azure Resource Manager-mall för en windows-skalningsuppsättning för virtuella datorer

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Genom att använda AZURE Monitor [Windows Azure Diagnostics (WAD) till-tillägget](diagnostics-extension-overview.md)kan du samla in mått och loggar från gästoperativsystemet (gästoperativsystem) som körs som en del av ett virtuellt dator-, molntjänst- eller Azure Service Fabric-kluster. Tillägget kan skicka telemetri till många olika platser som anges i den tidigare länkade artikeln.  

I den här artikeln beskrivs processen för att skicka prestandamått för gästoperativsystem för en Windows-skalningsuppsättning för virtuella datorer till Azure Monitor-datalagret. Från och med Windows Azure Diagnostics version 1.11 kan du skriva mått direkt till Azure Monitor-måttarkivet, där standardplattformsmått redan har samlats in. Genom att lagra dem på den här platsen kan du komma åt samma åtgärder som är tillgängliga för plattformsmått. Åtgärderna omfattar aviseringar i nära realtid, diagram, routning, åtkomst från REST API med mera. Tidigare skrev Windows Azure Diagnostics-tillägget till Azure Storage men inte Azure Monitor-datalagret.  

Om du inte har tidigare i Resource Manager-mallar kan du läsa om [malldistributioner](../../azure-resource-manager/management/overview.md) och deras struktur och syntax.  

## <a name="prerequisites"></a>Krav

- Din prenumeration måste vara registrerad hos [Microsoft.Insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services). 

- Du måste ha [Azure PowerShell](/powershell/azure) installerat eller använda [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview). 

- Vm-resursen måste finnas i en [region som stöder anpassade mått](metrics-custom-overview.md#supported-regions).

## <a name="set-up-azure-monitor-as-a-data-sink"></a>Konfigurera Azure Monitor som en datamottagare 
Azure Diagnostics-tillägget använder en funktion som kallas **datamottagare** för att dirigera mått och loggar till olika platser. Följande steg visar hur du använder en Resource Manager-mall och PowerShell för att distribuera en virtuell dator med hjälp av den nya Azure Monitor-datamottagaren. 

## <a name="author-a-resource-manager-template"></a>Skapa en Resource Manager-mall 
I det här exemplet kan du använda en allmänt tillgänglig [exempelmall:](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-autoscale)  

- **Azuredeploy.json** är en förkonfigurerad Resource Manager-mall för distribution av en skalningsuppsättning för virtuella datorer.

- **Azuredeploy.parameters.json** är en parameterfil som lagrar information som vilket användarnamn och lösenord du vill ange för den virtuella datorn. Under distributionen använder Resource Manager-mallen de parametrar som anges i den här filen. 

Ladda ned och spara båda filerna lokalt. 

###  <a name="modify-azuredeployparametersjson"></a>Ändra azuredeploy.parameters.json
Öppna filen **azuredeploy.parameters.json:**  
 
- Ange en **vmSKU** som du vill distribuera. Vi rekommenderar Standard_D2_v3. 
- Ange en **windowsOSVersion** som du vill använda för din skalningsuppsättning för virtuella datorer. Vi rekommenderar 2016-Datacenter. 
- Namnge den virtuella datorskaleuppsättningsresurs som ska distribueras med hjälp av egenskapen **vmssName.** Ett exempel är **VMSS-WAD-TEST**.    
- Ange antalet virtuella datorer som du vill köra på den virtuella datorns skala som angetts med hjälp av egenskapen **instanceCount.**
- Ange värden för **adminUsername** och **adminPassword** för skalningsuppsättningen för den virtuella datorn. Dessa parametrar används för fjärråtkomst till de virtuella datorerna i skalningsuppsättningen. Använd **inte** de i den här mallen för att undvika att din virtuella dator kapas. Robotar skannar internet efter användarnamn och lösenord i offentliga GitHub-databaser. De kommer sannolikt att testa virtuella datorer med dessa standardvärden. 


###  <a name="modify-azuredeployjson"></a>Ändra azuredeploy.json
Öppna **filen azuredeploy.json.** 

Lägg till en variabel som innehåller lagringskontoinformationen i Resource Manager-mallen. Alla loggar eller prestandaräknare som anges i konfigurationsfilen för diagnostik skrivs till både Azure Monitor-måttarkivet och det lagringskonto som du anger här: 

```json
"variables": { 
//add this line       
"storageAccountName": "[concat('storage', uniqueString(resourceGroup().id))]", 
```
 
Leta reda på definitionen för skalningsuppsättning för den virtuella datorn i resursavsnittet och lägg till **identitetsavsnittet** i konfigurationen. Det här tillägget säkerställer att Azure tilldelar den en systemidentitet. Det här steget säkerställer också att de virtuella datorerna i skalningsuppsättningen kan släppa ut gästmått om sig själva till Azure Monitor:  

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

Leta reda på avsnittet **virtualMachineProfile** i resursen för skalningsuppsättning för virtuell dator. Lägg till en ny profil som heter **tilläggProfilen** för att hantera tillägg.  


Lägg till ett nytt tillägg i mallen i avsnittet **VMSS-WAD-tillägg** i **tilläggetProfile.**  Det här avsnittet är hanterade identiteter för Azure-resurstillägg som säkerställer att de mått som skickas ut accepteras av Azure Monitor. **Namnfältet** kan innehålla valfritt namn. 

Följande kod från MSI-tillägget lägger också till diagnostiktillägget och konfigurationen som en tilläggsresurs till skaluppsättningsresursen för den virtuella datorn. Lägg gärna till eller ta bort prestandaräknare efter behov: 

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


Lägg till en **dependsOn** för lagringskontot för att säkerställa att det skapas i rätt ordning: 

```json
"dependsOn": [ 
"[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]", 
"[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]" 
//add this line below
"[concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]" 
```

Skapa ett lagringskonto om ett inte redan har skapats i mallen: 

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

Spara och stäng båda filerna. 

## <a name="deploy-the-resource-manager-template"></a>Distribuera resurshanteraren-mallen 

> [!NOTE]  
> Du måste köra Azure Diagnostics-tilläggsversionen 1.5 eller senare **och** ha egenskapen **autoUpgradeMinorVersion:** inställt på **true** i resource manager-mallen. Azure läser sedan in rätt tillägg när den startar den virtuella datorn. Om du inte har de här inställningarna i mallen ändrar du dem och distribuerar om mallen. 


Om du vill distribuera Resource Manager-mallen använder du Azure PowerShell:  

1. Starta PowerShell. 
1. Logga in på `Login-AzAccount`Azure med .
1. Få din lista över `Get-AzSubscription`prenumerationer med hjälp av .
1. Ange den prenumeration du ska skapa eller uppdatera den virtuella datorn: 

   ```powershell
   Select-AzSubscription -SubscriptionName "<Name of the subscription>" 
   ```
1. Skapa en ny resursgrupp för den virtuella datorn som distribueras. Kör följande kommando: 

   ```powershell
    New-AzResourceGroup -Name "VMSSWADtestGrp" -Location "<Azure Region>" 
   ```

   > [!NOTE]  
   > Kom ihåg att använda en Azure-region som är aktiverad för anpassade mått. Kom ihåg att använda en [Azure-region som är aktiverad för anpassade mått](https://github.com/MicrosoftDocs/azure-docs-pr/pull/metrics-custom-overview.md#supported-regions).
 
1. Kör följande kommandon för att distribuera den virtuella datorn:  

   > [!NOTE]  
   > Om du vill uppdatera en befintlig skalningsuppsättning lägger du till **-Stegvis läge i** slutet av kommandot. 
 
   ```powershell
   New-AzResourceGroupDeployment -Name "VMSSWADTest" -ResourceGroupName "VMSSWADtestGrp" -TemplateFile "<File path of your azuredeploy.JSON file>" -TemplateParameterFile "<File path of your azuredeploy.parameters.JSON file>"  
   ```

1. När distributionen har slutförts bör du hitta den virtuella datorns skala som anges i Azure-portalen. Den ska avge mått till Azure Monitor. 

   > [!NOTE]  
   > Du kan stöta på fel runt den valda **vmSkuSize**. I så fall går du tillbaka till filen **azuredeploy.json** och uppdaterar standardvärdet för parametern **vmSkuSize.** Vi rekommenderar att du försöker **Standard_DS1_v2**. 


## <a name="chart-your-metrics"></a>Kartlägga dina mätvärden 

1. Logga in på Azure Portal. 

1. Välj **Övervaka**till vänstermenyn . 

1. Välj **Mått**på sidan **Övervakare** . 

   ![Övervaka - mått sida](media/collect-custom-metrics-guestos-resource-manager-vmss/metrics.png) 

1. Ändra aggregeringsperioden till **Sista 30 minuterna**.  

1. Välj den skalningsuppsättning för virtuella datorn som du skapade på den nedrullningsbara resursmenyn.  

1. Välj **azure.vm.windows.guest**på den nedrullningsbara menyn namnområden . 

1. I den nedrullningsbara menyn Mått väljer du **Minnesrestaserade\%byte i Använd**.  

Du kan sedan också välja att använda dimensionerna på det här måttet för att kartlägga det för en viss virtuell dator eller för att rita varje virtuell dator i skalningsuppsättningen. 



## <a name="next-steps"></a>Nästa steg
- Läs mer om [anpassade mått](metrics-custom-overview.md).


