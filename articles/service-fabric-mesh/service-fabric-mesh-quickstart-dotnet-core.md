---
title: Snabbstart – skapa och distribuera en webbapp till Azure Service Fabric nät | Microsoft Docs
description: Den här snabbstarten visar hur du skapar en ASP.NET Core-webbplats och publicera den till Azure Service Fabric-nät.
services: service-fabric-mesh
documentationcenter: .net
author: tylermsft
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/17/2018
ms.author: twhitney
ms.custom: mvc, devcenter
ms.openlocfilehash: d14420a363cfea23c86f63533a4ea89c5f2fd06f
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39412924"
---
# <a name="quickstart-create-and-deploy-a-web-app-to-azure-service-fabric-mesh"></a>Snabbstart: Skapa och distribuera en webbapp till Azure Service Fabric nät

Azure Service Fabric Mesh är en fullständigt hanterad tjänst som gör att utvecklare kan distribuera mikrotjänstprogram utan att hantera virtuella datorer, lagring eller nätverk.

I den här snabbstarten skapar du ett nytt Service Fabric-nät program som består av en ASP.NET Core-webbapp, köra den på det lokala utvecklingsklustret och sedan publicera den kan köras på Azure.

Du behöver en Azure-prenumeration. Om du inte har någon kan du enkelt kan skapa en kostnadsfri Azure-prenumeration [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar. Du måste också [din konfigurationsmiljö för utvecklare](service-fabric-mesh-howto-setup-developer-environment-sdk.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="create-a-service-fabric-mesh-project"></a>Skapa ett Service Fabric Mesh-projekt

Öppna Visual Studio och välj **filen** > **New** > **projekt...**

I dialogrutan **New Project** (Nytt projekt) i rutan **Search** (Sök) längst upp skriver du `mesh`. Välj mallen **Service Fabric Mesh Application**. (Om du inte ser mallen kontrollerar du att du har installerat Mesh-SDK och förhandsversionen av VS-verktygen enligt anvisningarna om att [skapa en utvecklingsmiljö](service-fabric-mesh-howto-setup-developer-environment-sdk.md). 

I den **namn** skriver **ServiceFabricMesh1** och i den **plats** rutan, ange sökvägen till mappen för där filerna för projektet kommer att lagras.

Se till att alternativet för att **skapa katalog för lösning** är markerat och klicka på **OK** för att skapa Service Fabric Mesh-projektet.

![Visual Studio, dialogruta för nytt Service Fabric Mesh-projekt](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-new-project.png)

### <a name="create-a-service"></a>Skapa en tjänst

När du klickar på **OK**, **ny Service Fabric-tjänst** dialogruta. Välj den **ASP.NET Core** projektet typ, se till att **Container OS** är inställd på **Windows** och klicka på **OK** att skapa ASP.NET Core-projektet . 

![Visual Studio, dialogruta för nytt Service Fabric Mesh-projekt](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-new-service-fabric-service.png)

Den **ny ASP.NET Core-Webbapp** dialogruta. Välj **webbprogram** och klicka sedan på **OK**.

![Visual Studio, nytt ASP.NET-webbprogram](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-new-aspnetcore-app.png)

Visual Studio skapar programprojektet nät för Service Fabric och ASP.NET Core-projektet.

## <a name="build-and-publish-to-your-local-cluster"></a>Skapa och publicera till ditt lokala kluster

En Docker-avbildning skapats och publicerats till ditt lokala kluster så fort projektet läses in automatiskt. Den här processen kan ta lite tid. Du kan övervaka förloppet för Service Fabric-verktygen i den **utdata** fönster om du vill genom att välja den **Service Fabric-verktyg** objektet i den **utdata** fönstret listrutan. Du kan fortsätta att arbeta medan docker-avbildningen distribueras.

När projektet har skapats, klickar du på **F5** felsöka din tjänst lokalt. När den lokala distributionen är klar och Visual Studio med ditt projekt, öppnas ett webbläsarfönster med en exempel-webbsida.

När du är klar Bläddra i den distribuerade tjänsten stoppa felsökningen projektet genom att trycka på **SKIFT + F5** i Visual Studio.

## <a name="publish-to-azure"></a>Publicera till Azure

Om du vill publicera Service Fabric-nät projektet till Azure, högerklicka på den **Service Fabric-nät projekt** i Visual studio och välj **publicera...**

![Visual studio högerklickar du på Service Fabric-nät projekt](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-right-click-publish.png)

Du ser en **publicera Service Fabric-program** dialogrutan.

![Visual Studio, dialogruta för Service Fabric Mesh-projekt](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-publish-dialog.png)

Välj Azure-konto och Azure-prenumeration. Välj en **plats**. Den här artikeln använder **USA, östra**.

Under **Resursgrupp** väljer du **\<Skapa ny resursgrupp...>**. Den **skapa resursgrupp** dialogruta. Ange den **Resursgruppsnamn** och **plats**.  Den här snabbstarten används de **USA, östra** plats och namn gruppen **sfmeshTutorial1RG** (om din organisation har flera personer med samma prenumeration, Välj ett unikt Resursgruppsnamn).  Klicka på **skapa** skapa resursgruppen och gå tillbaka till dialogrutan Publicera.

![Visual Studio, dialogruta för ny Service Fabric Mesh-resursgrupp](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-publish-new-resource-group-dialog.png)

Återgå till dialogrutan **Publicera Service Fabric-program**. Under **Azure Container Registry** väljer du **\<Skapa nytt containerregister...>**. I dialogrutan **Create Container Registry** (Skapa containerregister) använder du ett unikt namn för **Container registry name** (Containerregisternamn). Ange en **plats** (den här snabbstarten används **USA, östra**). Välj den **resursgrupp** som du skapade i föregående steg i listrutan, exempelvis **sfmeshTutorial1RG**. Ange den **SKU** till **grundläggande** och klicka sedan på **skapa** att gå tillbaka till dialogrutan Publicera.

![Visual Studio, dialogruta för ny Service Fabric Mesh-resursgrupp](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-publish-new-container-registry-dialog.png)

I dialogrutan Publicera klickar du på den **publicera** knappen för att distribuera programmet till Azure Service Fabric-nät.

När du publicerar till Azure första gången skickas dockeravbildningen till ACR (Azure Container Registry), vilket tar tid beroende på storleken på avbildningen. Efterföljande publiceringar av samma projekt går snabbare. Du kan övervaka förloppet för distributionen genom att välja **Service Fabric-verktyg** i Visual Studio **utdata** fönstret listrutan. När distributionen är klar visas utdata för **Service Fabric Tools** (Service Fabric-verktyg) IP-adress och port för programmet i form av en webbadress.

```json
Packaging Application...
Building Images...
Web1 -> C:\Code\ServiceFabricMesh1\Web1\bin\Any CPU\Release\netcoreapp2.0\Web1.dll
Uploading the images to Azure Container Registry...
Deploying application to remote endpoint...
The application was deployed successfully and it can be accessed at http://...
```

Öppna en webbläsare och gå till URL: en till webbplatsen som körs i Azure:

![Service Fabric nät Web-program som körs](media/service-fabric-mesh-tutorial-deploy-dotnetcore/deployed-web-project.png)

## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs kan du ta bort alla resurser som du skapade för den här snabbstarten. Eftersom du har skapat en ny resursgrupp för att vara värd för både ACR och Service Fabric blandar resurser kan du ta bort den här resursgruppen, vilket är ett enkelt sätt att ta bort alla resurser som är associerade med den.

```azurecli
az group delete --resource-group sfmeshTutorial1RG
```

```powershell
Connect-AzureRmAccount
Remove-AzureRmResourceGroup -Name sfmeshTutorial1RG
```

Du kan också ta bort resursgruppen [från Azure portal](https://portal.azure.com).

## <a name="next-steps"></a>Nästa steg

Om du vill lära dig mer om att skapa och distribuera Service Fabric Mesh-program fortsätter du till självstudien.
> [!div class="nextstepaction"]
> [Skapa, felsöka och distribuera en webbapp för flera tjänster till Service Fabric Mesh](service-fabric-mesh-tutorial-create-dotnetcore.md)