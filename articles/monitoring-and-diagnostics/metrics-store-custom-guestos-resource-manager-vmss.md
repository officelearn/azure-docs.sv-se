---
title: Skicka OS gästmått av Azure Monitor-måtten lagra med en Resource Manager-mall för en Windows VM-skalningsuppsättning
description: Skicka OS gästmått av Azure Monitor-måtten lagra med en Resource Manager-mall för en Windows VM-skalningsuppsättning
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: howto
ms.date: 09/24/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: b9808233e08e545c31e171afe104173dccc6abed
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/28/2018
ms.locfileid: "47434933"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-using-a-resource-manager-template-for-a-windows-virtual-machine-scale-set"></a>Skicka OS gästmått av Azure Monitor-måtten lagra med en Resource Manager-mall för en Windows VM-skalningsuppsättning

Azure Monitor [Windows Azure-diagnostiktillägget](azure-diagnostics.md) (WAD) kan du samla in mått och loggar från gästoperativsystemet (gästoperativsystem) körs som en del av en virtuell dator, molntjänst eller Service Fabric-kluster.  Tillägget kan skicka telemetri till många olika platser som anges i tidigare länkade artikeln.  

Den här artikeln beskriver processen för att skicka guest OS-prestandamått för en Windows skalningsuppsättning för virtuella datorer till Azure Monitor-datalager. Från och med WAD version 1.11, kan du skriva mått direkt till Azure Monitor metrics store där standardplattform mått är redan har samlats in. Lagra dem i den här platsen kan du komma åt samma åtgärder som är tillgängliga för plattformen mått.  Åtgärder omfattar nästan i realtid avisering, diagram, routning, komma åt från REST API och mycket mer.  Tidigare skrev WAD tillägget till Azure Storage, men inte Azure Monitor-datalagret.  

Om du är nybörjare på Resource Manager-mallar kan du läsa om [malldistributioner](../azure-resource-manager/resource-group-overview.md), och deras struktur och syntax.  

## <a name="pre-requisites"></a>Förutsättningar

- Prenumerationen måste vara registrerad med [Microsoft.Insights](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) 

- Du måste ha antingen [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) installerad, eller så kan du använda [Azure CloudShell](https://docs.microsoft.com/azure/cloud-shell/overview.md) 


## <a name="set-up-azure-monitor-as-a-data-sink"></a>Konfigurera Azure Monitor som mellanlagringsplats 
Azure Diagnostics-tillägget använder en funktion som kallas ”data egenskaperna” till vägens mått och loggar på olika platser.  Följande steg visar hur du använder Resource Manager-mall och PowerShell för att distribuera en virtuell dator med den nya ”Azure Monitor” mellanlagringsplatsen. 

## <a name="author-resource-manager-template"></a>Skapa Resource Manager-mall 
Du kan använda en offentligt tillgänglig exempelmall för det här exemplet. Från mallarna är på https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-autoscale  

- **Azuredeploy.JSON** är en förkonfigurerad Resource Manager-mall för distribution av en VM-skalningsuppsättning

- **Azuredeploy.parameters.JSON** är en fil med parametrar som lagrar information, till exempel användarnamn och lösenord för vilka du vill ange för den virtuella datorn. Under distributionen av använder Resource Manager-mallen parametrarna som anges i den här filen. 

Ladda ned och spara filerna lokalt. 

###  <a name="modify-azuredeployparametersjson"></a>Ändra azuredeploy.parameters.json
Öppna den *azuredeploy.parameters.json* fil 

- Ange en **vmSKU** du vill distribuera (Vi rekommenderar Standard_D2_v3) 
- Ange en **windowsOSVersion** du vill använda för din VM-skalningsuppsättning (Vi rekommenderar 2016-Datacenter) 
- Namn på VM-skalningsuppsättningen resurs som ska distribueras med hjälp av en **vmssName** egenskapen. Till exempel *VMSS-WAD-TEST*.    
- Ange hur många virtuella datorer som ska köras på VM-skaluppsättning som anges med den **instanceCount** egenskap
- Ange värden för **adminUsername** och **adminPassword** för VM-skalningsuppsättningen. Dessa parametrar används för fjärråtkomst till de virtuella datorerna i skalningsuppsättningen. Dessa parametrar används för fjärråtkomst till den virtuella datorn. Använd inte de i den här mallen för att förhindra att den virtuella datorn som uppsnappas. Robotar skanna internet för användarnamn och lösenord i offentliga Github-lagringsplatser. Det är troligt att testa virtuella datorer med standardinställningarna. 


###  <a name="modify-azuredeployjson"></a>Ändra azuredeploy.json
Öppna den *azuredeploy.json* fil 

Lägg till en variabel som innehåller informationen om lagring i Resource Manager-mallen. Du måste fortfarande ange ett Lagringskonto som en del av installationen av diagnostics-tillägg. Loggar och/eller prestandaräknare som angetts i konfigurationsfilen diagnostik skrivs till det angivna lagringskontot förutom som skickas till arkivet som Azure Monitor-mått. 

```json
"variables": { 
//add this line       
"storageAccountName": "[concat('storage', uniqueString(resourceGroup().id))]", 
 ```
 
Hittar du definitioner av Virtual Machine Scale Sets i resursavsnittet och Lägg till avsnittet ”identitet” i konfigurationen. Detta säkerställer Azure tilldelar den en systemidentitet. Det här steget säkerställer att de virtuella datorerna i skalningsuppsättningen kan generera gästmått om sig själva till Azure Monitor.  

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


I den **extensionProfile**, lägga till ett nytt tillägg i mallen som den **VMSS-WAD-extension avsnittet**.  Det här avsnittet är hanterad tjänstidentitet (MSI)-tillägg som säkerställer att de mått som genereras accepteras av Azure Monitor. Den **namn** fältet kan innehålla valfritt namn. 

Koden nedan från MSI-tillägget lägger till diagnostiktillägget och konfigurationen som en resurs för tillägget scale set-resursen för virtuella datorer. Passa på att lägga till/ta bort räknare för serverprestanda. 

```json
          "extensionProfile": { 
            "extensions": [ 
            // BEGINNING of added code  
            // Managed service identity   
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


Lägg till en dependsOn för lagringskontot så att den har skapats i rätt ordning. 
```json
"dependsOn": [ 
"[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]", 
"[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]" 
//add this line below
"[concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]" 
 ```

Skapa ett storage-konto om något inte redan har skapats i mallen.  
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
> Du måste köra Azure Diagnostics-tilläggets version 1.5 eller senare och har ”autoUpgradeMinorVersion”: egenskapen *SANT* i Resource Manager-mallen.  Azure läser sedan in rätt tillägg när den startas den virtuella datorn. Om du inte har de här inställningarna i mallen, ändra dem och distribuerar om mallen. 


Vi ska använda Azure PowerShell för att distribuera Resource Manager-mallen.  

1. Starta PowerShell 
1. Logga in på Azure med `Login-AzureRmAccount`
1. Hämta listan över prenumerationer med hjälp av `Get-AzureRmSubscription`
1. Ange den prenumeration som du kommer att skapa/uppdatera den virtuella datorn i 

   ```PowerShell
   Select-AzureRmSubscription -SubscriptionName "<Name of the subscription>" 
   ```
1. Skapa en ny resursgrupp för den virtuella datorn distribueras, kör den nedanstående kommando 

   ```PowerShell
    New-AzureRmResourceGroup -Name "VMSSWADtestGrp" -Location "<Azure Region>" 
   ```

   > [!NOTE]  
   > Kom ihåg att använda en Azure-region som är aktiverad för anpassade mått. Se 
 
1. Kör följande kommandon för att distribuera den virtuella datorn med den  
   > [!NOTE] 
   > Om du vill uppdatera en befintlig skalningsuppsättning Lägg bara till *-läge inkrementella* i slutet av följande kommando. 
 
   ```PowerShell
   New-AzureRmResourceGroupDeployment -Name "VMSSWADTest" -ResourceGroupName "VMSSWADtestGrp" -TemplateFile "<File path of your azuredeploy.JSON file>" -TemplateParameterFile "<File path of your azuredeploy.parameters.JSON file>"  
   ```

1. När distributionen lyckas du ska kunna hitta skalningsuppsättning för virtuell dator i Azure-portalen och den bör de mått för att Azure Monitor. 

   > [!NOTE] 
   > Du kanske stöter på fel runt den valda vmSkuSize. Om det händer kan du gå tillbaka till filen azuredeploy.json och uppdatera standardvärdet för parametern vmSkuSize. I det här fallet rekommenderar vi försöker ”Standard_DS1_v2”). 


## <a name="chart-your-metrics"></a>Skapa diagram över dina mått 

1. Logga in på Azure Portal 

1. I den vänstra menyn, klickar du på **Övervakare** 

1. På sidan övervakaren **mått**. 

   ![Mått-sidan](./media/metrics-store-custom-rest-api/metrics.png) 

1. Ändra sammanställningsperioden till **senaste 30 minuterna**.  

1. I resursen i listrutan, väljer du virtuella datorns skaluppsättning som du nyss skapade.  

1. Välj i listrutan namnområden **azure.vm.windows.guest** 

1. I mått som nedrullningsbar listruta, Välj **minne\%allokerade byte som används**.  

Du kan sedan också välja att använda dimensioner på det här måttet i diagrammet med det här måttet för en viss virtuell dator i skalningsuppsättningen eller för att rita varje virtuell dator i skalningsuppsättningen. 



## <a name="next-steps"></a>Nästa steg
- Läs mer om [anpassade mått](metrics-custom-overview.md).

