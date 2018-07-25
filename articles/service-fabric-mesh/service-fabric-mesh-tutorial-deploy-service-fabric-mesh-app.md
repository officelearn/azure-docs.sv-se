---
title: Självstudie – Distribuera ett Service Fabric Mesh-program i Service Fabric Mesh | Microsoft Docs
description: Lär dig hur du publicerar ett Azure Service Fabric Mesh-program bestående av en ASP.NET Core-webbplats som kommunicerar med en serversidewebbtjänst.
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
ms.date: 07/12/2018
ms.author: twhitney
ms.custom: mvc, devcenter
ms.openlocfilehash: f9dea759f6556bc521dda4efbd27176f1e06452b
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39126583"
---
# <a name="tutorial-deploy-a-service-fabric-mesh-web-application"></a>Självstudie: Distribuera ett Service Fabric Mesh-webbprogram

Den här självstudien är del tre i en serie. Här får du se hur du publicerar ett Azure Service Fabric Mesh-webbprogram direkt från Visual Studio.

I den här självstudiekursen får du lära du dig att:
> [!div class="checklist"]
> * Publicera programmet i Azure.
> * Kontrollera programmets distributionsstatus
> * Se alla program som för närvarande distribueras till din prenumeration
> * Se programloggarna
> * Rensa de resurser som används av programmet.

I den här självstudieserien får du lära du dig att:
> [!div class="checklist"]
> * [Bygga ett Service Fabric Mesh-webbprogram](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [Felsöka programmet lokalt](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * Publicera appen i Azure

Vi visar hur du skapar ett Azure Service Fabric Mesh-program som har en ASP.NET-webbklientdels- och en ASP.NET Core webb-API-serverdelstjänst. Du felsöker sedan programmet i det lokala utvecklingsklustret och publicerar i Azure. När du är klar har du ett enkelt program som visar hur du genomför tjänst-till-tjänst-anrop i Service Fabric Mesh-webbprogrammet.

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

Om du vill publicera Service Fabric Mesh-projektet till Azure högerklickar du på **ServiceFabricMeshApp** i Visual Studio och väljer **Publish...** (publicera).

Därefter visas dialogrutan **Publish Service Fabric Application** (Publicera Service Fabric-program).

![Visual Studio, dialogruta för Service Fabric Mesh-projekt](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-dialog.png)

Välj Azure-konto och Azure-prenumeration. Välj en **plats**. Den här artikeln använder **USA, östra**.

Under **Resursgrupp** väljer du **\<Skapa ny resursgrupp...>**. En dialogruta visas där du skapar en ny resursgrupp. Den här artikeln använder platsen **USA, östra** och namnger gruppen **sfmeshTutorial1RG** (Välj ett unikt gruppnamn om organisationen har flera personer med samma prenumeration).  Tryck på **Create** (Skapa) för att skapa resursgruppen och återgå till publiceringsdialogrutan.

![Visual Studio, dialogruta för ny Service Fabric Mesh-resursgrupp](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-new-resource-group-dialog.png)

Återgå till dialogrutan **Publicera Service Fabric-program**. Under **Azure Container Registry** väljer du **\<Skapa nytt containerregister...>**. I dialogrutan **Create Container Registry** (Skapa containerregister) använder du ett unikt namn för **Container registry name** (Containerregisternamn). Ange en **plats** (den här självstudien använder **USA, östra**). Välj den **resursgrupp** som du skapade i föregående steg i listrutan, exempelvis **sfmeshTutorial1RG**. Ställ in **SKU** till **Basic** och tryck sedan på **Create** (Grundläggande > Skapa) för att återgå till publiceringsdialogrutan.

![Visual Studio, dialogruta för ny Service Fabric Mesh-resursgrupp](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-new-container-registry-dialog.png)

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

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Du kan använda Azure Cloud Shell eller en lokal installation av Azure CLI för återstående steg.

Om du väljer att installera och använda CLI lokalt krävs Azure CLI version 2.0.35 eller senare för att du ska kunna genomföra den här självstudiekursen. Kör `az --version` för att hitta versionen. Om du vill installera eller uppgradera till den senaste versionen av CLI kan du läsa [Installera Azure CLI 2.0][azure-cli-install].

## <a name="install-the-az-mesh-cli"></a>Installera az mesh cli
Vid prompt i kommandoradsgränssnittet

1) Ta bort alla föregående installationer av Azure Service Fabric Mesh CLI-modulen.

```cli
az extension remove --name mesh
```

2)  Installera Azure Service Fabric Mesh CLI-tilläggsmodulen. För förhandsversionen är kommandoradsgränssnittet (CLI) för Azure Service Fabric Mesh skrivet som ett tillägg till Azure-CLI. I den offentliga förhandsversionen ingår det dock i Azure-CLI.

```cli
az extension add --source https://sfmeshcli.blob.core.windows.net/cli/mesh-0.8.1-py2.py3-none-any.whl
```

## <a name="check-application-deployment-status"></a>Kontrollera programmets distributionsstatus

Nu har programmet distribuerats. Du kan kontrollera statusen med kommandot `app show`. 

Programnamnet för exempelprogrammet är `ServiceMeshApp`. Samla in information om programmet med följande kommando:

```azurecli-interactive
az mesh app show --resource-group $rg --name ServiceMeshApp
```

## <a name="see-all-applications-currently-deployed-to-your-subscription"></a>Se alla program som för närvarande distribueras till din prenumeration

Med kommandot "app list" kan du hämta en lista över program som du har distribuerat till prenumerationen.

```cli
az mesh app list --output table
```

## <a name="see-the-application-logs"></a>Se programloggarna

Granska loggarna för det distribuerade programmet:

```azurecli-interactive
az mesh code-package-log get --resource-group $rg --application-name ServiceMeshApp --service-name todoservice --replica-name 0 --code-package-name ServiceMeshApp
```

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs kan du ta bort alla skapade resurser. Eftersom du har skapat en ny resursgrupp som fungerar som värd för både ACR och Service Fabric går det bra att ta bort den här resursgruppen, vilket tar bort alla till den kopplade resurser.

```azurecli
az group delete --resource-group sfmeshTutorial1RG
```

```powershell
Remove-AzureRmResourceGroup -Name sfmeshTutorial1RG
```

Du kan också ta bort resursgruppen [från portalen](../azure-resource-manager/resource-group-portal.md#delete-resource-group-or-resources). 

## <a name="next-steps"></a>Nästa steg

I den här självstudiedelen lärde du dig:
> [!div class="checklist"]
> * Publicera programmet i Azure.
> * Kontrollera programmets distributionsstatus
> * Se alla program som för närvarande distribueras till din prenumeration
> * Se programloggarna
> * Rensa de resurser som används av programmet.

Nu när du har slutfört publiceringen av Service Fabric Mesh-programmet till Azure kan du prova följande:

* Titta närmare på [exempelröstningsprogrammet](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp) och se ännu ett exempel på tjänst-till-tjänst-kommunikation.
* Läs [Service Fabric-resurser](service-fabric-mesh-service-fabric-resources.md)
* Läs mer om [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest