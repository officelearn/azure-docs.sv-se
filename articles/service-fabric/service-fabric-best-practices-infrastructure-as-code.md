---
title: Azure Service Fabric-infrastruktur enligt bästa kod
description: Metodtips och designöverväganden för hantering av Azure Service Fabric som infrastruktur som kod.
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 1c044d5fd973d3c577088a887f2fac413d2ab79d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551836"
---
# <a name="infrastructure-as-code"></a>Infrastruktur som kod

Skapa Azure Service Fabric-kluster i ett produktionsscenario med Resource Manager-mallar. Resource Manager-mallar ger större kontroll över resursegenskaper och ser till att du har en konsekvent resursmodell.

Exempel på Resource Manager-mallar är tillgängliga för Windows och Linux i [Azure-exemplen på GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates). Dessa mallar kan användas som utgångspunkt för klustermallen. Ladda `azuredeploy.json` `azuredeploy.parameters.json` ner och redigera dem för att uppfylla dina egna krav.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Om du `azuredeploy.json` `azuredeploy.parameters.json` vill distribuera de mallar som du har hämtat ovan använder du följande Azure CLI-kommandon:

```azurecli
ResourceGroupName="sfclustergroup"
Location="westus"

az group create --name $ResourceGroupName --location $Location 
az group deployment create --name $ResourceGroupName  --template-file azuredeploy.json --parameters @azuredeploy.parameters.json
```

Skapa en resurs med Powershell

```powershell
$ResourceGroupName="sfclustergroup"
$Location="westus"
$Template="azuredeploy.json"
$Parameters="azuredeploy.parameters.json"

New-AzResourceGroup -Name $ResourceGroupName -Location $Location
New-AzResourceGroupDeployment -Name $ResourceGroupName -TemplateFile $Template -TemplateParameterFile $Parameters
```

## <a name="azure-service-fabric-resources"></a>Azure Service Fabric-resurser

Du kan distribuera program och tjänster till ditt Service Fabric-kluster via Azure Resource Manager. Mer information [finns i Hantera program och tjänster som Azure Resource Manager-resurser.](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-arm-resource) Följande är tjänstinfrastrukturspecifika resurser för serviceinfrastruktur som ska inkluderas i mallresurserna i Resource Manager.

```json
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```

Om du vill distribuera ditt program med Azure Resource Manager måste du först [skapa ett sfpkg](https://docs.microsoft.com/azure/service-fabric/service-fabric-package-apps#create-an-sfpkg) Service Fabric Application-paket. Följande pythonskript är ett exempel på hur du skapar en sfpkg:

```python
# Create SFPKG that needs to be uploaded to Azure Storage Blob Container
microservices_sfpkg = zipfile.ZipFile(
    self.microservices_app_package_name, 'w', zipfile.ZIP_DEFLATED)
package_length = len(self.microservices_app_package_path)

for root, dirs, files in os.walk(self.microservices_app_package_path):
    root_folder = root[package_length:]
    for file in files:
        microservices_sfpkg.write(os.path.join(
            root, file), os.path.join(root_folder, file))

microservices_sfpkg.close()
```

## <a name="azure-virtual-machine-operating-system-automatic-upgrade-configuration"></a>Konfiguration av automatisk uppgradering av operativsystemet azure virtual machine 
Uppgradering av virtuella datorer är en användarinitierad åtgärd och vi rekommenderar att du använder automatisk uppgradering av [det automatiska operativsystemet](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) för Azure Service Fabric-kluster. Patch Orchestration Application är en alternativ lösning som är avsedd för när den finns utanför Azure, även om POA kan användas i Azure, med overhead värd för POA i Azure är en vanlig orsak att föredra automatisk uppgradering av virtuella datorer via POA. Följande är mallen Beräkna resursuppsättning för virtuell datoruppsättning för att aktivera automatisk os-uppgradering:

```json
"upgradePolicy": {
   "mode": "Automatic",
   "automaticOSUpgradePolicy": {
        "enableAutomaticOSUpgrade": true,
        "disableAutomaticRollback": false
    }
},
```
När du använder Automatiska OS-uppgraderingar med Service Fabric, den nya OS-avbildningen rullas ut en uppdateringsdomän i taget för att upprätthålla hög tillgänglighet för de tjänster som körs i Service Fabric. Om du vill använda automatiska OS-uppgraderingar i Service Fabric måste klustret vara konfigurerat för att använda silverhållbarhetsnivån eller högre.

Kontrollera att följande registernyckel är inställd på false för att förhindra att windows-värddatorerna initierar icke samordnade uppdateringar: HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU.

Följande är mallegenskaperna För beräkningsskalningsuppsättningsresurshanteraren för att ange registernyckeln För WindowsUpdate till false:
```json
"osProfile": {
        "computerNamePrefix": "{vmss-name}",
        "adminUsername": "{your-username}",
        "secrets": [],
        "windowsConfiguration": {
          "provisionVMAgent": true,
          "enableAutomaticUpdates": false
        }
      },
```

## <a name="azure-service-fabric-cluster-upgrade-configuration"></a>Uppgraderingskonfiguration för Azure Service Fabric-kluster
Följande är mallen Service Fabric cluster Resource Manager för automatisk uppgradering:
```json
"upgradeMode": "Automatic",
```
Om du vill uppgradera klustret manuellt laddar du ned distributionen av hytten/deb till en virtuell klusterdator och anropar sedan följande PowerShell:
```powershell
Copy-ServiceFabricClusterPackage -Code -CodePackagePath <"local_VM_path_to_msi"> -CodePackagePathInImageStore ServiceFabric.msi -ImageStoreConnectionString "fabric:ImageStore"
Register-ServiceFabricClusterPackage -Code -CodePackagePath "ServiceFabric.msi"
Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <"msi_code_version">
```

## <a name="next-steps"></a>Nästa steg

* Skapa ett kluster på virtuella datorer eller datorer som kör Windows Server: [Skapa service fabric-kluster för Windows Server](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* Skapa ett kluster på virtuella datorer eller datorer som kör Linux: [Skapa ett Linux-kluster](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* Lär dig mer om [Service Fabric-supportalternativen](service-fabric-support.md)
