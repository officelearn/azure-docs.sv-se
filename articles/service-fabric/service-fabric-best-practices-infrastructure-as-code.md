---
title: Azure Service Fabric-infrastrukturen som kod bästa praxis | Microsoft Docs
description: Metodtips för att hantera Service Fabric infrastruktur som kod.
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
ms.openlocfilehash: 2a4c3adb39ebf6e58770348eccc8c78e8ff92167
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/25/2019
ms.locfileid: "56804475"
---
# <a name="infrastructure-as-code"></a>Infrastruktur som kod

Skapa Azure Service Fabric-kluster med hjälp av Resource Manager-mallar i ett produktionsscenario för. Resource Manager-mallar ge större kontroll av resursegenskaper och se till att du har en konsekvent resursmodell.

Exemplet Resource Manager-mallar är tillgängliga för Windows och Linux i den [Azure-exempel på GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates). Dessa mallar kan användas som utgångspunkt för mallen för klustret. Ladda ned `azuredeploy.json` och `azuredeploy.parameters.json` och redigera dem för att uppfylla dina specifika behov.

Att distribuera den `azuredeploy.json` och `azuredeploy.parameters.json` mallar som du laddade ned ovan, använder du följande Azure CLI-kommandon:

```azurecli
ResourceGroupName="sfclustergroup"
Location="westus"

az group create --name $ResourceGroupName --location $Location 
az group deployment create --name $ResourceGroupName  --template-file azuredeploy.json --parameters @azuredeploy.parameters.json
```

Skapa en resurs med hjälp av Powershell

```powershell
$ResourceGroupName="sfclustergroup"
$Location="westus"
$Template="azuredeploy.json"
$Parameters="azuredeploy.parameters.json"

New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location
New-AzureRmResourceGroupDeployment -Name $ResourceGroupName -TemplateFile $Template -TemplateParameterFile $Parameters
```

## <a name="azure-service-fabric-resources"></a>Azure Service Fabric resources

Du kan distribuera program och tjänster till ditt Service Fabric-kluster via Azure Resource Manager. Se [hantera program och tjänster som Azure Resource Manager-resurser](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-arm-resource) mer information. Här följer bästa praxis Service Fabric application specifika resurser ska ingå i din Resource Manager-Mallresurser.

```json
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```

För att distribuera ditt program med Azure Resource Manager måste du först måste [skapa en sfpkg](https://docs.microsoft.com/azure/service-fabric/service-fabric-package-apps#create-an-sfpkg) paketet för Service Fabric-program. Följande skript i python är ett exempel på hur du skapar en sfpkg:

```python
# Create SFPKG that needs to be uploaded to Azure Storage Blob Container
microservices_sfpkg = zipfile.ZipFile(self.microservices_app_package_name, 'w', zipfile.ZIP_DEFLATED)
package_length = len(self.microservices_app_package_path)

for root, dirs, files in os.walk(self.microservices_app_package_path):
    root_folder = root[package_length:]
    for file in files:
        microservices_sfpkg.write(os.path.join(root, file), os.path.join(root_folder, file))

microservices_sfpkg.close()
```

## <a name="next-steps"></a>Nästa steg

* Skapa ett kluster på virtuella datorer eller datorer som kör Windows Server: [Skapa för Service Fabric-kluster för Windows Server](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* Skapa ett kluster på virtuella datorer eller datorer som kör Linux: [Skapa en Linux-kluster](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* Lär dig mer om [Service Fabric-supportalternativen](service-fabric-support.md)