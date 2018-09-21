---
title: Snabbstart – Distribuera Hello World till Azure Service Fabric Mesh | Microsoft Docs
description: Den här snabbstarten visar hur du distribuerar ett Service Fabric Mesh-program till Azure Service Fabric Mesh.
services: service-fabric-mesh
keywords: Lägg inte till eller redigera nyckelord utan att först kontakta den SEO-ansvarige.
author: rwike77
ms.author: ryanwi
ms.date: 08/24/2018
ms.topic: quickstart
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: d50ebeef686de7e467e2a71b6bb33f207414bcc8
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/13/2018
ms.locfileid: "45541474"
---
# <a name="quickstart-deploy-hello-world-to-service-fabric-mesh"></a>Snabbstart: Distribuera Hello World till Service Fabric Mesh

[Service Fabric Mesh](service-fabric-mesh-overview.md) gör det enkelt att skapa och hantera mikrotjänstprogram i Azure utan att behöva etablera virtuella datorer. I den här snabbstarten skapar du ett Hello World-program i Azure och gör det tillgängligt på Internet. Den här åtgärden utförs med ett enda kommando. Inom ett par minuter visas den här vyn i webbläsaren:

![Hello World-app i webbläsaren][sfm-app-browser]

[!INCLUDE [preview note](./includes/include-preview-note.md)]

Om du inte redan har ett Azure-konto [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="set-up-service-fabric-mesh-cli"></a>Konfigurera Service Fabric Mesh CLI 
Du kan använda Azure Cloud Shell eller en lokal installation av Azure CLI för att genomföra den här snabbstarten. Installera Azure Service Fabric Mesh CLI-tilläggsmodulen genom att följa de här [instruktionerna](service-fabric-mesh-howto-setup-cli.md).

## <a name="sign-in-to-azure"></a>Logga in på Azure
Logga in på Azure och konfigurera din prenumeration.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-resource-group"></a>Skapa resursgrupp
Skapa en resursgrupp som programmet ska distribueras till. Du kan använda en befintlig resursgrupp och hoppa över det här steget. 

```azurecli-interactive
az group create --name myResourceGroup --location eastus 
```

## <a name="deploy-the-application"></a>Distribuera programmet
Skapa ditt program i en resursgrupp med kommandot `az mesh deployment create`.  Kör följande:

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/helloworld/mesh_rp.linux.json --parameters "{'location': {'value': 'eastus'}}" 
```

Föregående kommando distribuerar ett Linux-program med hjälp av [mesh_rp.linux.json-mallen](https://sfmeshsamples.blob.core.windows.net/templates/helloworld/mesh_rp.linux.json). Om du vill distribuera ett Windows-program använder du [mesh_rp.windows.json-mallen](https://sfmeshsamples.blob.core.windows.net/templates/helloworld/mesh_rp.windows.json). Windows-containeravbildningar är större än Linux-containeravbildningar och kan ta längre tid att distribuera.

Efter några minuter returnerar kommandot:

`helloWorldApp has been deployed successfully on helloWorldNetwork with public ip address <IP Address>` 

## <a name="open-the-application"></a>Öppna programmet
När programmet har distribuerats kopierar du den offentliga IP-adressen för tjänstslutpunkten från CLI-utdata. Öppna IP-adressen i en webbläsare. En webbplats med logotypen för Azure Service Fabric Mesh visas.

## <a name="check-the-application-details"></a>Kontrollera programinformationen
Du kan kontrollera programmets status med kommandot `az mesh app show`. Det här kommandot ger användbar information som du kan följa upp.

Programnamnet för den här snabbstarten är `helloWorldApp`. Om du vill hämta information om programmet kör du följande kommando:

```azurecli-interactive
az mesh app show --resource-group myResourceGroup --name helloWorldApp
```

## <a name="see-the-application-logs"></a>Se programloggarna

Granska loggarna för det distribuerade programmet med hjälp av kommandot `az mesh code-package-log get`:

```azurecli-interactive
az mesh code-package-log get --resource-group myResourceGroup --application-name helloWorldApp --service-name helloWorldService --replica-name 0 --code-package-name helloWorldCode
```

## <a name="clean-up-resources"></a>Rensa resurser

När du är redo att ta bort programmet kör du kommandot [az group delete][az-group-delete] för att ta bort resursgruppen och de program- och nätverksresurser som den innehåller.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

Om du vill lära dig mer om att skapa och distribuera Service Fabric Mesh-program fortsätter du till självstudien.
> [!div class="nextstepaction"]
> [Skapa och distribuera ett webbprogram för flera tjänster](service-fabric-mesh-tutorial-create-dotnetcore.md)

<!-- Images -->
[sfm-app-browser]: ./media/service-fabric-mesh-quickstart-deploy-container/HelloWorld.png

<!-- Links / Internal -->
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
