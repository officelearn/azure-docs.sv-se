---
title: Självstudie – Distribuera ett Service Fabric Mesh-program | Microsoft Docs
description: Lär dig hur du använder Visual Studio för att publicera ett Azure Service Fabric Mesh-program bestående av en ASP.NET Core-webbplats som kommunicerar med en serversidewebbtjänst.
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
ms.openlocfilehash: 467484824ec3a3ceffb6dfa692953406ed6acc1b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46963329"
---
# <a name="tutorial-deploy-a-service-fabric-mesh-application"></a>Självstudie: Distribuera ett Service Fabric Mesh-program

Den här självstudien är del tre i en serie. Här får du se hur du publicerar ett Azure Service Fabric Mesh-webbprogram direkt från Visual Studio.

I den här självstudiekursen får du lära du dig att:
> [!div class="checklist"]
> * Publicera programmet till Azure med Visual Studio.
> * Kontrollera programmets distributionsstatus
> * Se alla program som för närvarande distribueras till din prenumeration

I den här självstudieserien får du lära du dig att:
> [!div class="checklist"]
> * [Skapa en Service Fabric Mesh-app i Visual Studio](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [Felsöka en Service Fabric Mesh-app som körs i ditt lokala utvecklingskluster](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * Distribuera en Service Fabric Mesh-app
> * [Uppgradera en Service Fabric Mesh-app](service-fabric-mesh-tutorial-upgrade.md)
> * [Rensa Service Fabric Mesh-resurser](service-fabric-mesh-tutorial-cleanup-resources.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du börjar den här självstudien:

* Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

* Se till att du har [skapat utvecklingsmiljön](service-fabric-mesh-howto-setup-developer-environment-sdk.md) vilket omfattar att installera Service Fabric-runtime, SDK, Docker, och Visual Studio 2017.

## <a name="download-the-to-do-sample-application"></a>Hämta att göra-exempelprogrammet

Om du inte skapade att göra-exempelprogrammet i [del två av självstudieserien](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md) kan du ladda ned det. Kör följande kommando i ett kommandofönster för att klona databasen för exempelappen till den lokala datorn.

```
git clone https://github.com/azure-samples/service-fabric-mesh
```

Programmet ligger under katalogen `src\todolistapp`.

## <a name="publish-to-azure"></a>Publicera till Azure

Om du vill publicera Service Fabric Mesh-projektet till Azure högerklickar du på **todolistapp** i Visual Studio och väljer **Publish...** (publicera).

Därefter visas dialogrutan **Publish Service Fabric Application** (Publicera Service Fabric-program).

![Visual Studio, dialogruta för Service Fabric Mesh-projekt](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-dialog.png)

Välj Azure-konto och Azure-prenumeration. Välj en **plats**. Den här artikeln använder **USA, östra**.

Under **Resursgrupp** väljer du **\<Skapa ny resursgrupp...>**. En dialogruta visas där du skapar en ny resursgrupp. Den här artikeln använder platsen **USA, östra** och namnger gruppen **sfmeshTutorial1RG** (Välj ett unikt gruppnamn om organisationen har flera personer med samma prenumeration).  Tryck på **Create** (Skapa) för att skapa resursgruppen och återgå till publiceringsdialogrutan.

![Visual Studio, dialogruta för ny Service Fabric Mesh-resursgrupp](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-new-resource-group-dialog.png)

Återgå till dialogrutan **Publicera Service Fabric-program**. Under **Azure Container Registry** väljer du **\<Skapa nytt containerregister...>**. I dialogrutan **Create Container Registry** (Skapa containerregister) använder du ett unikt namn för **Container registry name** (Containerregisternamn). Ange en **plats** (den här självstudien använder **USA, östra**). Välj den **resursgrupp** som du skapade i föregående steg i listrutan, exempelvis **sfmeshTutorial1RG**. Ställ in **SKU** på **Basic** och tryck på **Skapa** för att skapa det privata Azure-containerregistret och återvänd till publiceringsdialogrutan.

![Visual Studio, dialogruta för nytt Service Fabric Mesh-containerregister](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-new-container-registry-dialog.png)

Om du får ett felmeddelande om att resursprovidern inte har registrerats för prenumerationen kan du registrera den. Se först om resursprovidern är tillgänglig för din prenumeration:

```Powershell
Get-AzureRmResourceProvider -ListAvailable
```

Om containerregisterprovidern (`Microsoft.ContainerRegistry`) är tillgänglig registrerar du den från Powershell:

```Powershell
Connect-AzureRmAccount
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ContainerRegistry
```

I publiceringsdialogrutan trycker du på knappen **Publish** (Publicera) för att distribuera Service Fabric-programmet till Azure.

När du publicerar till Azure första gången skickas dockeravbildningen till ACR (Azure Container Registry), vilket tar tid beroende på storleken på avbildningen. Efterföljande publiceringar av samma projekt går snabbare. Du kan övervaka förloppet för distributionen genom att välja fönstret **Service Fabric Tools** (Service Fabric-verktyg) i fönstret **Output** (Utdata) i Visual Studio. När distributionen är klar visas utdata för **Service Fabric Tools** (Service Fabric-verktyg) IP-adress och port för programmet i form av en webbadress.

```json
Packaging Application...
Building Images...
Web1 -> C:\Code\ServiceFabricMeshApp\ToDoService\bin\Any CPU\Release\netcoreapp2.0\ToDoService.dll
Uploading the images to Azure Container Registy...
Deploying application to remote endpoint...
The application was deployed successfully and it can be accessed at http://10.000.38.000:20000.
```

Öppna en webbläsare och navigera till webbadressen för att se webbplatsen köras i Azure.

## <a name="set-up-service-fabric-mesh-cli"></a>Konfigurera Service Fabric Mesh CLI 
Du kan använda Azure Cloud Shell eller en lokal installation av Azure CLI för återstående steg. Installera Azure Service Fabric Mesh CLI-tilläggsmodulen genom att följa de här [instruktionerna](service-fabric-mesh-howto-setup-cli.md).

## <a name="check-application-deployment-status"></a>Kontrollera programmets distributionsstatus

Nu har programmet distribuerats. Du kan kontrollera statusen med kommandot `app show`. 

Programnamnet för exempelprogrammet är `ServiceMeshApp`. Samla in information om programmet med följande kommando:

```azurecli-interactive
az mesh app show --resource-group $rg --name ServiceMeshApp
```

## <a name="see-all-applications-currently-deployed-to-your-subscription"></a>Se alla program som för närvarande distribueras till din prenumeration

Med kommandot "app list" kan du hämta en lista över program som du har distribuerat till prenumerationen.

```azurecli-interactive
az mesh app list --output table
```

## <a name="next-steps"></a>Nästa steg

I den här självstudiedelen lärde du dig att:
> [!div class="checklist"]
> * Publicera programmet i Azure.
> * Kontrollera programmets distributionsstatus
> * Se alla program som för närvarande distribueras till din prenumeration

Gå vidare till nästa kurs:
> [!div class="nextstepaction"]
> [Uppgradera en Service Fabric Mesh-app](service-fabric-mesh-tutorial-upgrade.md)

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
