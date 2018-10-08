---
title: Snabbstart – Skapa och distribuera en webbapp till Azure Service Fabric Mesh | Microsoft Docs
description: Den här snabbstarten beskriver hur du skapar en ASP.NET Core-webbplats och publicerar den till Azure Service Fabric Mesh.
services: service-fabric-mesh
documentationcenter: .net
author: tylermsft
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/17/2018
ms.author: twhitney
ms.custom: mvc, devcenter
ms.openlocfilehash: 7e324b80968017e0160f41b88fa1824669050ac9
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2018
ms.locfileid: "47407407"
---
# <a name="quickstart-create-and-deploy-a-web-app-to-azure-service-fabric-mesh"></a>Snabbstart: Skapa och distribuera en webbapp till Azure Service Fabric Mesh

Azure Service Fabric Mesh är en fullständigt hanterad tjänst som gör att utvecklare kan distribuera mikrotjänstprogram utan att hantera virtuella datorer, lagring eller nätverk.

I den här snabbstarten ska du skapa ett nytt Service Fabric Mesh-program som består av en ASP.NET Core-webbapp, köra den i det lokala utvecklingsklustret och sedan publicera och köra den i Azure.

Du behöver en Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du enkelt [skapa en kostnadsfri Azure-prenumeration](https://azure.microsoft.com/free/) innan du börjar. Du måste också [konfigurera utvecklingsmiljön](service-fabric-mesh-howto-setup-developer-environment-sdk.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="create-a-service-fabric-mesh-project"></a>Skapa ett Service Fabric Mesh-projekt

Öppna Visual Studio och välj **Arkiv** > **Nytt** > **Projekt**.

I dialogrutan **New Project** (Nytt projekt) i rutan **Search** (Sök) längst upp skriver du `mesh`. Välj mallen **Service Fabric Mesh Application**. (Om du inte ser mallen kontrollerar du att du har installerat Mesh-SDK och förhandsversionen av VS-verktygen enligt anvisningarna om att [skapa en utvecklingsmiljö](service-fabric-mesh-howto-setup-developer-environment-sdk.md). 

I rutan **Namn** skriver du **ServiceFabricMesh1** och i rutan **Plats** anger du mappsökvägen där filerna för projektet ska lagras.

Se till att alternativet för att **skapa katalog för lösning** är markerat och klicka på **OK** för att skapa Service Fabric Mesh-projektet.

![Visual Studio, dialogruta för nytt Service Fabric Mesh-projekt](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-new-project.png)

### <a name="create-a-service"></a>Skapa en tjänst

När du klickar på **OK** öppnas dialogrutan **Ny Service Fabric-tjänst**. Välj projekttypen **ASP.NET Core**, kontrollera att **Container OS** (Operativsystem för container) är inställt på **Windows** och klicka på **OK** för att skapa ASP.NET Core-projektet. 

![Visual Studio, dialogruta för nytt Service Fabric Mesh-projekt](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-new-service-fabric-service.png)

Dialogrutan **Nytt ASP.NET Core-webbprogram** öppnas. Välj **Webbprogram** och klicka sedan på **OK**.

![Visual Studio, nytt ASP.NET-webbprogram](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-new-aspnetcore-app.png)

Visual Studio skapar Service Fabric Mesh-programprojektet och ASP.NET Core-projektet.

## <a name="build-and-publish-to-your-local-cluster"></a>Skapa och publicera till ditt lokala kluster

En Docker-avbildning skapas och publiceras automatiskt till det lokala klustret så fort projektet läses in. Den här processen kan ta lite tid. Om du vill kan du övervaka Service Fabric-verktygens förlopp i **utdatafönstret** genom att välja objektet **Service Fabric-verktyg** i listrutan för fönstret **Utdata**. Du kan fortsätta att arbeta medan Docker-avbildningen distribueras.

När projektet har skapats klickar du på **F5** för att felsöka tjänsten lokalt. När den lokala distributionen är klar och Visual Studio kör ditt projekt öppnas ett webbläsarfönster med en exempelwebbsida.

När du är klar med granskningen av den nya distribuerade tjänsten stoppar du felsökningen av projektet genom att trycka på **Skift + F5** i Visual Studio.

## <a name="publish-to-azure"></a>Publicera till Azure

För att publicera Service Fabric Mesh-projektet till Azure högerklickar du på **Service Fabric Mesh-projektet** i Visual studio och väljer **Publicera**.

![Högerklicka på Service Fabric Mesh-projektet i Visual Studio](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-right-click-publish.png)

Dialogrutan **Publicera Service Fabric-program** visas.

![Visual Studio, dialogruta för Service Fabric Mesh-projekt](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-publish-dialog.png)

Välj Azure-konto och Azure-prenumeration. Välj en **plats**. Den här artikeln använder **USA, östra**.

Under **Resursgrupp** väljer du **\<Skapa ny resursgrupp...>**. Dialogrutan **Skapa resursgrupp** visas. Ange **Resursgruppsnamn** och **Plats**.  Den här snabbstarten använder platsen **USA, östra** och namnger gruppen **sfmeshTutorial1RG** (Välj ett unikt resursgruppnamn om organisationen har flera personer med samma prenumeration).  Klicka på **Skapa** för att skapa resursgruppen och återgå till publiceringsdialogrutan.

![Visual Studio, dialogruta för ny Service Fabric Mesh-resursgrupp](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-publish-new-resource-group-dialog.png)

Återgå till dialogrutan **Publicera Service Fabric-program**. Under **Azure Container Registry** väljer du **\<Skapa nytt containerregister...>**. I dialogrutan **Create Container Registry** (Skapa containerregister) använder du ett unikt namn för **Container registry name** (Containerregisternamn). Ange en **plats** (i den här snabbstarten används **USA, östra**). Välj den **resursgrupp** som du skapade i föregående steg i listrutan, exempelvis **sfmeshTutorial1RG**. Välj **Basic** för **SKU** och klicka sedan på **Skapa** för att återgå till publiceringsdialogrutan.

![Visual Studio, dialogruta för ny Service Fabric Mesh-resursgrupp](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-publish-new-container-registry-dialog.png)

I publiceringsdialogrutan klickar du på knappen **Publicera** för att distribuera Service Fabric Mesh-programmet till Azure.

När du publicerar till Azure första gången skickas dockeravbildningen till ACR (Azure Container Registry), vilket tar tid beroende på storleken på avbildningen. Efterföljande publiceringar av samma projekt går snabbare. Du kan övervaka distributionsförloppet genom att välja fönstret **Service Fabric-verktyg** i fönstret **Utdata** i Visual Studio. När distributionen är klar visas utdata för **Service Fabric Tools** (Service Fabric-verktyg) IP-adress och port för programmet i form av en webbadress.

```json
Packaging Application...
Building Images...
Web1 -> C:\Code\ServiceFabricMesh1\Web1\bin\Any CPU\Release\netcoreapp2.0\Web1.dll
Uploading the images to Azure Container Registry...
Deploying application to remote endpoint...
The application was deployed successfully and it can be accessed at http://...
```

Öppna en webbläsare och navigera till webbadressen för att se webbplatsen när den körs i Azure:

![Köra ett Service Fabric Mesh-webbprogram](media/service-fabric-mesh-tutorial-deploy-dotnetcore/deployed-web-project.png)

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs kan du ta bort alla resurser som du skapade för den här snabbstarten. Eftersom du har skapat en ny resursgrupp som fungerar som värd för både ACR och Service Fabric Mesh går det bra att ta bort den här resursgruppen, vilket är ett enkelt sätt att ta bort alla resurser som är associerade med den.

```azurecli
az group delete --resource-group sfmeshTutorial1RG
```

```powershell
Connect-AzureRmAccount
Remove-AzureRmResourceGroup -Name sfmeshTutorial1RG
```

Du kan också ta bort resursgruppen [från Azure-portalen](https://portal.azure.com).

## <a name="next-steps"></a>Nästa steg

Om du vill lära dig mer om att skapa och distribuera Service Fabric Mesh-program fortsätter du till självstudien.
> [!div class="nextstepaction"]
> [Skapa, felsöka och distribuera en webbapp för flera tjänster till Service Fabric Mesh](service-fabric-mesh-tutorial-create-dotnetcore.md)