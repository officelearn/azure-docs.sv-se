---
title: Självstudie – ta bort en app som körs i Azure Service Fabric Mesh | Microsoft Docs
description: I den här självstudien lär du dig hur du tar bort ett program som körs i Service Fabric Mesh och tar bort resurserna.
services: service-fabric-mesh
documentationcenter: .net
author: rwike77
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/15/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: f366413ae5f758601dfebc2a29ff848feb756083
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46960438"
---
# <a name="tutorial-remove-an-application-and-resources"></a>Självstudie: Ta bort ett program och resurser

Den här självstudien är del fyra i en serie. Du får lära dig hur du tar bort ett program som körs som [tidigare har distribuerat till Service Fabric Mesh](service-fabric-mesh-tutorial-template-deploy-app.md). 

I del fyra i serien lär du dig att:

> [!div class="checklist"]
> * Ta bort ett program som körs i Service Fabric Mesh
> * Ta bort programresurserna

I den här självstudieserien får du lära du dig att:
> [!div class="checklist"]
> * [Distribuera ett program till Service Fabric Mesh med en mall](service-fabric-mesh-tutorial-template-deploy-app.md)
> * [Skala ett program som körs i Service Fabric Mesh](service-fabric-mesh-tutorial-template-scale-services.md)
> * [Uppgradera ett program som körs i Service Fabric Mesh](service-fabric-mesh-tutorial-template-upgrade-app.md)
> * Ta bort ett program

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du börjar den här självstudien:

* Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

* Öppna [Azure Cloud Shell](service-fabric-mesh-howto-setup-cli.md), eller [installera Azure CLI och Service Fabric Mesh CLI lokalt](service-fabric-mesh-howto-setup-cli.md#install-the-service-fabric-mesh-cli-locally).

## <a name="delete-the-resource-group-and-all-the-resources"></a>Tar bort en resursgrupp och alla dess resurser

När de inte längre behövs kan du ta bort alla skapade resurser. Tidigare [skapade du en ny resursgrupp](service-fabric-mesh-tutorial-template-deploy-app.md#create-a-container-registry) som värd för Azure Container Registry-instansen och Service Fabric Mesh-programresurserna.  Du kan ta bort den här resursgruppen, vilket även tar bort alla associerade resurser.

```azurecli
az group delete --resource-group myResourceGroup
```

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="individually-delete-the-resources"></a>Ta bort resurserna individuellt
Du kan också ta bort ACR-instansen, Service Fabric Mesh-programmet och nätverksresurserna individuellt.

Ta bort ACR-instansen:

```azurecli
az acr delete --resource-group myResourceGroup --name myContainerRegistry
```

Ta bort Service Fabric Mesh-programmet:

```azurecli
az mesh app delete --resource-group myResourceGroup --name todolistapp
```

Ta bort nätverket:
```azurecli
az mesh network delete --resource-group myResourceGroup --name todolistappNetwork
```

## <a name="next-steps"></a>Nästa steg

I den här självstudiedelen lärde du dig att:

> [!div class="checklist"]
> * Ta bort ett program som körs i Service Fabric Mesh
> * Ta bort programresurserna