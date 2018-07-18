---
title: Distribuera appen från ett privat register till Azure Service Fabric nät | Microsoft Docs
description: Lär dig hur du distribuerar en app som använder ett privat behållarregister till Service Fabric nät med hjälp av Azure CLI.
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
ms.date: 07/16/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: af92d3c6ea881d00ec687a5560bf4db35aa431c5
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/17/2018
ms.locfileid: "39089494"
---
# <a name="deploy-a-service-fabric-mesh-app-from-a-private-container-image-registry"></a>Distribuera en Service Fabric-nät-app från ett privat behållarregister för avbildning

Den här artikeln visar hur du distribuerar en Azure Service Fabric nät app som använder ett privat behållarregister för avbildningen.

## <a name="prerequisites"></a>Förutsättningar

### <a name="set-up-service-fabric-mesh-cli"></a>Konfigurera Service Fabric nät CLI 
Du kan använda Azure Cloud Shell eller en lokal installation av Azure CLI för att slutföra den här uppgiften. Installera Azure Service Fabric nät CLI-tillägg-modulen genom att följa dessa [instruktioner](service-fabric-mesh-howto-setup-cli.md).

### <a name="install-docker"></a>Installera Docker

Installera Docker för att stödja de container Service Fabric-appar som används av Service Fabric-nät.

### <a name="windows-10"></a>Windows 10

Ladda ned och installera den senaste versionen av [Docker Community Edition för Windows][download-docker]. 

Under installationen, Välj **Använd Windows-behållare i stället för Linux-behållare** när du tillfrågas. Du måste sedan logga ut och logga in igen. När du har loggat in igen om du inte tidigare har aktiverat Hyper-V, kan du uppmanas att aktivera Hyper-V. Aktivera Hyper-V och starta sedan om datorn.

När datorn har startats om, Docker uppmanas du att aktivera den **behållare** funktion, aktivera den och starta om datorn.

### <a name="windows-server-2016"></a>Windows Server 2016

Använd följande PowerShell-kommandon för att installera Docker. Mer information finns i [Docker Enterprise Edition för Windows Server][download-docker-server].

```powershell
Install-Module DockerMsftProvider -Force
Install-Package Docker -ProviderName DockerMsftProvider -Force
Install-WindowsFeature Containers
```

Starta om datorn.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure och ange aktiv prenumeration:

```azurecli-interactive
az login
az account set --subscription "<subscriptionName>"
```

## <a name="create-a-container-registry-and-push-an-image-to-it"></a>Skapa ett behållarregister och push-överför avbildningen till den

Skapa ett Azure Container Registry genom att följa instruktionerna i [skapa ett privat Docker-register i Azure med Azure CLI](../container-registry/container-registry-get-started-azure-cli.md). Utför steg upp till den [logga in på ACR](../container-registry/container-registry-get-started-azure-cli.md#log-in-to-acr) steg. 

### <a name="push-image-to-acr"></a>Push-överföra avbildning till ACR

Innan du kan push-överföra en avbildning till Azure Container Registry måste du ha en avbildning. Om du inte har några lokala containeravbildningar ännu kan du köra följande kommando för att hämta en befintlig avbildning från Docker Hub.

```bash
docker pull seabreeze/azure-mesh-helloworld:1.1-alpine
```

Innan du kan push-överföra en avbildning till ditt register måste du tagga den med det fullständiga namnet på din ACR-inloggningsserver. Kör följande kommando för att hämta det fullständiga namnet på inloggningsservern för ACR-instansen.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Tagga avbildningen med hjälp av kommandot [docker tag][docker-tag]. Ersätt `<acrLoginServer>` med namnet på inloggningsservern för ACR-instansen.

```bash
docker tag seabreeze/azure-mesh-helloworld:1.1-alpine <acrLoginServer>/azure-mesh-helloworld:1.1-alpine
```
### <a name="list-container-images"></a>Visa lista över containeravbildningar

I följande exempel visas lagringsplatserna i ett register:

```azurecli
az acr repository list --name <acrName> --output table
```

Resultat:

```bash
Result
----------------
azure-mesh-helloworld
```

I följande exempel visas taggarna för den **azure-nät-helloworld** lagringsplats.

```azurecli
az acr repository show-tags --name <acrName> --repository azure-mesh-helloworld --output table
```

Resultat:

```bash
Result
--------
1.1-alpine
```
Föregående resultatet bekräftar förekomsten av `azure-mesh-helloworld:1.1-alpine` i privata behållarregister.

## <a name="retrieve-credentials-for-the-registry"></a>Hämta autentiseringsuppgifter för registret

För att distribuera en behållarinstans från registret som har skapats måste autentiseringsuppgifter anges under distributionen. Aktivera administratörsanvändaren i registret med följande kommando:

```azurecli-interactive
az acr update --name <acrName> --admin-enabled true
```

Hämta den registret servernamn, användarnamn och lösenord med hjälp av följande kommandon:

```azurecli-interactive
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
az acr credential show --name <acrName> --query username
az acr credential show --name <acrName> --query "passwords[0].value"
```

De värden som anges av föregående kommandon refereras till som `<acrLoginServer>`, `<acrUserName>`, och `<acrPassword>` i följande kommando.


## <a name="deploy-the-template"></a>Distribuera mallen

Skapa program och relaterade resurser med följande kommando och ange autentiseringsuppgifter från föregående steg.

Den `registry-password` parametern i mallen är en `securestring`. Den visas inte i distributionens status och `az mesh service show` kommandon. Se till att den har angetts korrekt i följande kommando.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/helloworld/mesh_rp.private_registry.linux.json --parameters "{\"location\": {\"value\": \"eastus\"}, \"registry-server\": {\"value\": \"<acrLoginServer>\"}, \"registry-username\": {\"value\": \"<acrUserName>\"}, \"registry-password\": {\"value\": \"<acrPassword>\"}}" 
```

Om några minuter bör kommandot returnerar med:

`helloWorldPrivateRegistryApp has been deployed successfully on helloWorldPrivateRegistryNetwork with public ip address <IP Address>` 

## <a name="open-the-application"></a>Öppna programmet
När programmet har distribuerats hämta den offentliga IP-adressen för tjänsteslutpunkt och öppna den i en webbläsare. Den visar en webbsida med Service Fabric-nät-logotypen.

Distributionskommandot returnerar den offentliga IP-adressen för tjänsteslutpunkt. Du kan också kan du också fråga nätverksresurs för att hitta den offentliga IP-adressen till tjänstens slutpunkt. 
 
Resursnamnet nätverk för det här programmet är `helloWorldPrivateRegistryNetwork`, hämta information om den med hjälp av följande kommando. 

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name helloWorldPrivateRegistryNetwork
```

## <a name="delete-the-resources"></a>Ta bort resurser

Om du vill spara de begränsade resurser som tilldelats för förhandsversionen kan du ta bort resurserna ofta. Ta bort resurser relaterade till det här exemplet genom att ta bort resursgruppen som de har distribuerats.

```azurecli-interactive
az group delete --resource-group myResourceGroup 
```

## <a name="next-steps"></a>Nästa steg
- Visa programmet Hello World-exemplet på [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/helloworld).
- Läs mer om Service Fabric-Resursmodell i [nät Resursmodell i Service Fabric](service-fabric-mesh-service-fabric-resources.md).
- Om du vill veta mer om Service Fabric-nät kan läsa den [Service Fabric-nät översikt](service-fabric-mesh-overview.md).

[download-docker-server]: https://docs.docker.com/install/windows/docker-ee/
[download-docker]: https://store.docker.com/editions/community/docker-ce-desktop-windows
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/