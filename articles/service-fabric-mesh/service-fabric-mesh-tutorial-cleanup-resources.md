---
title: Självstudie – Rensa Azure Service Fabric Mesh-resurser | Microsoft Docs
description: Lär dig hur du tar bort Azure Service Fabric Mesh-resurser så att du inte debiteras för resurser som du inte längre använder.
services: service-fabric-mesh
documentationcenter: .net
author: TylerMSFT
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/18/2018
ms.author: twhitney
ms.custom: mvc, devcenter
ms.openlocfilehash: fb7a444c54a57e7f2c38d941eb99f2fea7eebcef
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46993365"
---
# <a name="tutorial-remove-azure-resources"></a>Självstudie: Ta bort Azure-resurser

Den här självstudien är del fem i en serie och beskriver hur du tar bort appen och dess resurser så att du inte debiteras för dem.

I den här självstudiekursen får du lära du dig att:
> [!div class="checklist"]
> * Rensa de resurser som används av appen så att du inte debiteras för dem.

I den här självstudieserien får du lära du dig att:
> [!div class="checklist"]
> * [Skapa en Service Fabric Mesh-app i Visual Studio](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [Felsöka en Service Fabric Mesh-app som körs i ditt lokala utvecklingskluster](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * [Distribuera en Service Fabric Mesh-app](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * [Uppgradera en Service Fabric Mesh-app](service-fabric-mesh-tutorial-upgrade.md)
> * Rensa Service Fabric Mesh-resurser

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du börjar den här självstudien:

* Om du inte har distribuerat att göra-appen följer du anvisningarna i [Publicera ett Service Fabric Mesh-webbprogram](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md).

## <a name="clean-up-resources"></a>Rensa resurser

Det här är slutet av självstudiekursen. När du är klar med de resurser som du har skapat tar du bort dem så att du inte debiteras för resurser som du inte längre använder. Detta är särskilt viktigt eftersom Mesh är en serverlös tjänst som debiteras per sekund. Mer information om priserna för Mesh finns i https://aka.ms/sfmeshpricing.

En av fördelarna med Azure är att när du skapar resurser som är kopplade till en viss resursgrupp tas alla associerade resurser bort när du tar bort resursgruppen. Det betyder att du inte behöver ta bort dem en i taget.

Eftersom du har skapat en ny resursgrupp för att skapa att göra-appen kan du ta bort den här resursgruppen, vilket gör att alla associerade resurser tas bort.

```azurecli
az group delete --resource-group sfmeshTutorial1RG
```

```powershell
Remove-AzureRmResourceGroup -Name sfmeshTutorial1RG
```

Du kan också ta bort resursgruppen **sfmeshTutorial1RG** [från portalen](../azure-resource-manager/resource-group-portal.md#delete-resource-group-or-resources). 

## <a name="next-steps"></a>Nästa steg

Nu när du har slutfört publiceringen av Service Fabric Mesh-programmet till Azure kan du prova följande:

* Om du vill se ett annat exempel på tjänst-till-tjänst-kommunikation kan du utforska [exempelröstningsprogrammet](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp)
* Mer information om Service Fabric-resursmodellen finns i avsnittet om [Service Fabric Mesh-resursmodellen](service-fabric-mesh-service-fabric-resources.md).
* Mer information om Service Fabric Mesh finns i [översikten över Service Fabric Mesh](service-fabric-mesh-overview.md).
* Läs mer om [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)