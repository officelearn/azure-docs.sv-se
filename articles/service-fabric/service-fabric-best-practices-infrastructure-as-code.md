---
title: Azure Service Fabric-infrastruktur som metod tips för kod | Microsoft Docs
description: Metod tips för att hantera Service Fabric som infrastruktur som kod.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 83439a913989875c5e26ee51901f10558e5debd5
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/12/2019
ms.locfileid: "68955614"
---
# <a name="infrastructure-as-code"></a>Infrastruktur som kod

I ett produktions scenario skapar du Azure Service Fabric-kluster med hjälp av Resource Manager-mallar. Resource Manager-mallar ger bättre kontroll över resurs egenskaper och säkerställer att du har en konsekvent resurs modell.

Exempel på Resource Manager-mallar är tillgängliga för Windows och Linux i [Azure-exemplen på GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates). Dessa mallar kan användas som utgångs punkt för kluster mal len. Hämta `azuredeploy.json` och`azuredeploy.parameters.json` redigera dem så att de uppfyller dina egna krav.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Om du vill `azuredeploy.json` distribuera `azuredeploy.parameters.json` och-mallarna som du laddade ned ovan använder du följande Azure CLI-kommandon:

```azurecli
ResourceGroupName="sfclustergroup"
Location="westus"

az group create --name $ResourceGroupName --location $Location 
az group deployment create --name $ResourceGroupName  --template-file azuredeploy.json --parameters @azuredeploy.parameters.json
```

Skapa en resurs med hjälp av PowerShell

```powershell
$ResourceGroupName="sfclustergroup"
$Location="westus"
$Template="azuredeploy.json"
$Parameters="azuredeploy.parameters.json"

New-AzResourceGroup -Name $ResourceGroupName -Location $Location
New-AzResourceGroupDeployment -Name $ResourceGroupName -TemplateFile $Template -TemplateParameterFile $Parameters
```

## <a name="azure-service-fabric-resources"></a>Azure Service Fabric-resurser

Du kan distribuera program och tjänster på ditt Service Fabric-kluster via Azure Resource Manager. Mer information finns i [hantera program och tjänster som Azure Resource Managers resurser](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-arm-resource) . Följande är bästa praxis Service Fabric programspecifika resurser som du kan ta med i resurserna i Resource Manager-mallen.

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

För att distribuera ditt program med hjälp av Azure Resource Manager måste du först [skapa ett sfpkg](https://docs.microsoft.com/azure/service-fabric/service-fabric-package-apps#create-an-sfpkg) Service Fabric-programpaket. Följande Python-skript är ett exempel på hur du skapar en sfpkg:

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

## <a name="azure-virtual-machine-operating-system-automatic-upgrade-configuration"></a>Konfiguration av automatisk uppgradering av virtuella Azure-datorns operativ system 
Uppgraderingen av dina virtuella datorer är en initierad användare och vi rekommenderar att du använder [skalnings uppsättning för virtuella datorer](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) för Azure Service Fabric kluster hantering av värd korrigering för Azure-kluster. Uppdaterings Orchestration-programmet är en alternativ lösning som är avsedd för när den ligger utanför Azure, även om POA kan användas i Azure, med kostnad för POA i Azure, vilket är ett vanligt skäl att föredra den virtuella datorns operativ system för automatisk uppgradering över POA. Här följer de egenskaper för Resource Manager-mallen för beräkning av virtuella datorer som aktiverar automatisk uppgradering av operativ system:

```json
"upgradePolicy": {
   "mode": "Automatic",
   "automaticOSUpgradePolicy": {
        "enableAutomaticOSUpgrade": true,
        "disableAutomaticRollback": false
    }
},
```
När du använder automatiska OS-uppgraderingar med Service Fabric distribueras den nya operativ system avbildningen ut en uppdaterings domän i taget för att upprätthålla hög tillgänglighet för de tjänster som körs i Service Fabric. Om du vill använda automatiska operativ Systems uppgraderingar i Service Fabric klustret måste konfigureras för att använda silver hållbarhets nivån eller högre.

Se till att följande register nyckel har angetts till false för att förhindra att Windows-värddatorer initierar inkoordinerade uppdateringar: HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU.

Här följer egenskaperna för den virtuella datorns skalnings uppsättning i Resource Manager för att ange att register nyckeln för WindowsUpdate ska vara falskt:
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

## <a name="azure-service-fabric-cluster-upgrade-configuration"></a>Konfiguration av Azure Service Fabric Cluster-uppgradering
Följande är mallen för Service Fabric Cluster Resource Manager-mallen för att aktivera automatisk uppgradering:
```json
"upgradeMode": "Automatic",
```
Om du vill uppgradera klustret manuellt kan du ladda ned CAB/deb-distributionen till en virtuell kluster dator och sedan anropa följande PowerShell:
```powershell
Copy-ServiceFabricClusterPackage -Code -CodePackagePath <"local_VM_path_to_msi"> -CodePackagePathInImageStore ServiceFabric.msi -ImageStoreConnectionString "fabric:ImageStore"
Register-ServiceFabricClusterPackage -Code -CodePackagePath "ServiceFabric.msi"
Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <"msi_code_version">
```

## <a name="next-steps"></a>Nästa steg

* Skapa ett kluster på virtuella datorer eller datorer som kör Windows Server: [Service Fabric skapa kluster för Windows Server](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* Skapa ett kluster på virtuella datorer eller datorer som kör Linux: [Skapa ett Linux-kluster](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* Lär dig mer om [Service Fabric-supportalternativen](service-fabric-support.md)
