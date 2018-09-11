---
title: Distribuera en Service Fabric-nät appen från ett privat behållarregister avbildning till Azure Service Fabric-nät | Microsoft Docs
description: Lär dig hur du distribuerar en app från ett privat behållarregister avbildning till Service Fabric nät med hjälp av Azure CLI.
services: service-fabric-mesh
documentationcenter: .net
author: rwike77
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/20/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: a2791c86512790f36477e562cb82718174df8dc3
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2018
ms.locfileid: "44296606"
---
# <a name="deploy-a-service-fabric-mesh-app-from-a-private-container-image-registry"></a>Distribuera en Service Fabric-nät-app från ett privat behållarregister för avbildning

Den här artikeln visar hur du distribuerar en Azure Service Fabric nät app som använder ett privat behållarregister för avbildningen.

## <a name="prerequisites"></a>Förutsättningar

### <a name="set-up-service-fabric-mesh-cli"></a>Konfigurera Service Fabric Mesh CLI

Använda en lokal installation av Azure CLI för att slutföra den här åtgärden. Installera Azure Service Fabric Mesh CLI-tilläggsmodulen genom att följa de här [instruktionerna](service-fabric-mesh-howto-setup-cli.md).

## <a name="install-docker"></a>Installera Docker

#### <a name="windows-10"></a>Windows 10

Ladda ned och installera den senaste versionen av [Docker Community Edition för Windows][download-docker] för att stödja de containerbaserade Service Fabric-appar som används av Service Fabric Mesh.

Under installationen väljer du att **använda Windows-container i stället för Linux-container** när frågan dyker upp. 

Om Hyper-V inte är aktiverat på datorn kommer installationsprogrammet för Docker att erbjuda att aktivera det. Klicka på **OK** för att göra det om du tillfrågas.

#### <a name="windows-server-2016"></a>Windows Server 2016

Om du inte har Hyper-V-rollen aktiverad öppnar du PowerShell som administratör och kör följande kommando för att aktivera Hyper-V, och startar sedan om datorn. Mer information finns i dokumentationen om [Docker Enterprise Edition för Windows Server][download-docker-server].

```powershell
Install-WindowsFeature -Name Hyper-V -IncludeManagementTools
```

Starta om datorn.

Öppna PowerShell som administratör och kör följande kommando för att installera Docker:

```powershell
Install-Module DockerMsftProvider -Force
Install-Package Docker -ProviderName DockerMsftProvider -Force
Install-WindowsFeature Containers
```

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure och ange aktiv prenumeration.

```azurecli-interactive
az login
az account set --subscription "<subscriptionName>"
```

## <a name="create-a-container-registry-and-push-an-image-to-it"></a>Skapa ett behållarregister och push-överför avbildningen till den

Använd följande steg för att skapa ett Azure Container Registry.

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. Använd följande kommando för att skapa en resursgrupp med namnet *myResourceGroup* i den *eastus* plats.

```azurecli
az group create --name myResourceGroup --location eastus
```

### <a name="create-a-container-registry"></a>Skapa ett containerregister

Skapa ett Azure container registry (ACR) instans med hjälp av den `az acr create` kommando. Registernamnet måste vara unikt i Azure och innehålla 5 – 50 alfanumeriska tecken. I följande exempel visas namnet *myContainerRegistry007* används. Om du får ett felmeddelande som registernamnet används, väljer du ett annat namn. Använd som namnger överallt `<acrName>` visas i dessa anvisningar.

```azurecli
az acr create --resource-group myResourceGroup --name myContainerRegistry007 --sku Basic
```

När registret har skapats ser utdata som liknar följande:

```json
{
  "adminUserEnabled": false,
  "creationDate": "2017-09-08T22:32:13.175925+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry007",
  "location": "eastus",
  "loginServer": "myContainerRegistry007.azurecr.io",
  "name": "myContainerRegistry007",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

### <a name="push-the-image-to-azure-container-registry"></a>Överför avbildningen till Azure Container Registry

Du måste ha en behållaravbildning för att skicka en avbildning till Azure container registry (ACR). Om du ännu inte har några lokala behållaravbildningar, kör du följande kommando för att hämta den en avbildning från Docker Hub (du kan behöva byta Docker för att arbeta med Linux-avbildningar genom att högerklicka på docker-ikonen och välja **växla till Linux-behållare**).

```bash
docker pull seabreeze/azure-mesh-helloworld:1.1-alpine
```

Innan du kan push-överföra en avbildning till ditt register måste du tagga den med det fullständiga namnet på din ACR-inloggningsserver.

Kör följande kommando för att hämta det fullständiga namnet på inloggningsservern för ACR-instansen.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Det fullständiga namnet på inloggningsservern som returneras kommer kallas `<acrLoginServer>` i resten av den här artikeln.

Tagga nu din docker-avbildning med den [dockertagg] [ docker-tag] kommando. I kommandot nedan Ersätt `<acrLoginServer>` med namnet på inloggningsservern som rapporterats av kommandot ovan. I följande exempel taggar seabreeze/azure-nät-helloworld:1.1-alpine bild. Om du använder en annan avbildning, ersätter du avbildningens namn i följande kommando.

```bash
docker tag seabreeze/azure-mesh-helloworld:1.1-alpine <acrLoginServer>/seabreeze/azure-mesh-helloworld:1.1-alpine
```

Exempel: `docker tag seabreeze/azure-mesh-helloworld:1.1-alpine myContainerRegistry007.azurecr.io/seabreeze/azure-mesh-helloworld:1.1-alpine`

Logga in på Azure Container Registry.

```bash
az acr login -n <acrName>
```

Exempel: `az acr login -n myContainerRegistry007`

Överför avbildningen till azure-behållarregister med följande kommando:

```bash
docker push <acrLoginServer>/seabreeze/azure-mesh-helloworld:1.1-alpine
```

Exempel: `docker push myContainerRegistry007.azurecr.io/seabreeze/azure-mesh-helloworld:1.1-alpine`

### <a name="list-container-images"></a>Visa lista över containeravbildningar

I följande exempel visas lagringsplatserna i ett register. I exemplen förutsätter att du använder azure-nät-helloworld:1.1-alpine bild. Om du använder en annan avbildning, ersätter du dess namn där den azure-nät-helloworld-avbildningen används.

```azurecli
az acr repository list --name <acrName> --output table
```
Exempel: `az acr repository list --name myContainerRegistry007 --output table`

Resultat:

```bash
Result
-------------------------------
seabreeze/azure-mesh-helloworld
```

I följande exempel visas taggarna för den **azure-nät-helloworld** lagringsplats.

```azurecli
az acr repository show-tags --name <acrName> --repository seabreeze/azure-mesh-helloworld --output table
```

Exempel: `az acr repository show-tags --name myContainerRegistry007 --repository seabreeze/azure-mesh-helloworld --output table`

Resultat:

```bash
Result
--------
1.1-alpine
```

Föregående resultatet bekräftar förekomsten av `azure-mesh-helloworld:1.1-alpine` i privata behållarregister.

## <a name="retrieve-credentials-for-the-registry"></a>Hämta autentiseringsuppgifter för registret

> [!IMPORTANT]
> Aktiverar administratörsanvändare på ett Azure container registry rekommenderas inte för produktionsscenarier. Det är klart här om du vill behålla den här demonstrationen kort. I produktionsscenarier använder en [tjänstens huvudnamn](https://docs.microsoft.com/azure/container-registry/container-registry-auth-service-principal) för både användar- och autentisering i produktionsscenarier.

För att distribuera en behållarinstans från registret som har skapats måste du ange autentiseringsuppgifter under distributionen. Aktivera administratörsanvändaren i registret med följande kommando:

```azurecli-interactive
az acr update --name <acrName> --admin-enabled true
```

Exempel: `az acr update --name myContainerRegistry007 --admin-enabled true`

Hämta den registret servernamn, användarnamn och lösenord med hjälp av följande kommandon:

```azurecli-interactive
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
az acr credential show --name <acrName> --query username
az acr credential show --name <acrName> --query "passwords[0].value"
```

De värden som anges av föregående kommandon refereras till som `<acrLoginServer>`, `<acrUserName>`, och `<acrPassword>` nedan.

## <a name="deploy-the-template"></a>Distribuera mallen

Skapa program och relaterade resurser med följande kommando och ange autentiseringsuppgifter från föregående steg.

Den `registry-password` parametern i mallen är en säker sträng. Den visas inte i distributionens status och `az mesh service show` kommandon.

Om du använder en Bash-konsol kör du följande:

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/helloworld/mesh_rp.private_registry.linux.json --parameters "{\"location\": {\"value\": \"eastus\"}, \"registry-server\": {\"value\": \"<acrLoginServer>\"}, \"registry-username\": {\"value\": \"<acrUserName>\"}, \"registry-password\": {\"value\": \"<acrPassword>\"}}"
```

Om du använder en PowerShell-konsol kör du följande:

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/helloworld/mesh_rp.private_registry.linux.json --parameters "{'location': {'value': 'eastus'}, 'registry-server': {'value': '<acrLoginServer>'}, 'registry-username': {'value': '<acrUserName>'}, 'registry-password': {'value': '<acrPassword>'}}"
```

Om några minuter bör du se:

`helloWorldPrivateRegistryApp has been deployed successfully on helloWorldPrivateRegistryNetwork with public ip address <IP Address>`

## <a name="open-the-application"></a>Öppna programmet

När programmet har distribuerats hämta den offentliga IP-adressen för tjänsteslutpunkt och öppna den i en webbläsare. Den visar en webbsida med Service Fabric-nät-logotypen.

Distributionskommandot returnerar den offentliga IP-adressen för tjänsteslutpunkt. Du kan också kan du också fråga nätverksresurs för att hitta den offentliga IP-adressen till tjänstens slutpunkt. 
 
Resursnamnet nätverk för det här programmet är `helloWorldPrivateRegistryNetwork`, hämta information om den med hjälp av följande kommando. 

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name helloWorldPrivateRegistryNetwork
```

## <a name="delete-the-resources"></a>Ta bort resurser

Ofta ta bort de resurser som du inte längre använder i Azure. Ta bort de resurser som rör det här exemplet genom att ta bort resursgruppen som de har distribuerats (som tar bort allt som är associerade med resursgruppen) med följande kommando:

```azurecli-interactive
az group delete --resource-group myResourceGroup
```

Om du har växlat Docker för att arbeta med Linux-avbildningar för den här övningen och gå tillbaka till att arbeta med Windows-avbildningar, högerklickar du docker-ikonen och välj **växla till Windows-behållare**

## <a name="next-steps"></a>Nästa steg

- Visa programmet Hello World-exemplet på [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/helloworld).
- Läs mer om Service Fabric-Resursmodell i [nät Resursmodell i Service Fabric](service-fabric-mesh-service-fabric-resources.md).
- Om du vill veta mer om Service Fabric-nät kan läsa den [Service Fabric-nät översikt](service-fabric-mesh-overview.md).

[download-docker-server]: https://docs.docker.com/install/windows/docker-ee/
[download-docker]: https://store.docker.com/editions/community/docker-ce-desktop-windows
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/