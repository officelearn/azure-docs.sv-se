---
title: Skapa en molnbaserad Kubernetes-utvecklingsmiljö | Microsoft Docs
titleSuffix: Azure Dev Spaces
author: ghogen
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
ms.author: ghogen
ms.date: 06/06/2018
ms.topic: quickstart
description: Snabb Kubernetes-utveckling med behållare och mikrotjänster i Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
manager: douge
ms.openlocfilehash: 16ec493708f85e9b3819943e131b9f9c3649f27e
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/06/2018
ms.locfileid: "34824646"
---
# <a name="quickstart-create-a-kubernetes-dev-space-with-azure-dev-spaces-net-core-and-visual-studio"></a>Snabbstart: Skapa en Kubernetes-utvecklingsmiljö med Azure Dev Spaces (.NET Core och Visual Studio)

I den här guiden får du lära dig hur du:

- Ställer in Azure Dev Spaces med ett hanterat Kubernetes-kluster i Azure.
- Iterativt utvecklar kod i behållare med Visual Studio.
- Felsöka kod som körs i klustret.

> [!Note]
> **Om du fastnar** läser du [felsökningsavsnittet](troubleshooting.md) eller skriver en kommentar på den här sidan. Du kan också försöka med den mer ingående [kursen](get-started-netcore-visualstudio.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

- Ett Kubernetes-kluster som kör Kubernetes 1.9.6 i området EastUS, WestEurope eller CanadaEast med HTTP-programroutning.

  ![Se till att aktivera HTTP-programroutning.](media/common/Kubernetes-Create-Cluster-3.PNG)

- Visual Studio 2017 med arbetsbelastningen webbutveckling installerad. Om du inte har installerat den kan du hämta den [här](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

## <a name="set-up-azure-dev-spaces"></a>Konfigurera Azure Dev Spaces

Installera [Visual Studio-tillägget för Azure Dev Spaces](https://aka.ms/get-azds-visualstudio).

## <a name="connect-to-a-cluster"></a>Anslut till ett kluster

Därefter måste du skapa och konfigurera ett projekt för Azure Dev Spaces.

### <a name="create-an-aspnet-web-app"></a>Skapa en ASP.NET-webbapp

Skapa ett nytt projekt i Visual Studio 2017. För närvarande måste projektet vara ett **ASP.NET Core-webbprogram**. Ge projektet namnet ”**webfrontend**”.

Välj mallen **Webbprogram (MVC, Model-View-Controller)** och välj **.NET Core** och **ASP.NET Core 2.0** som mål.

### <a name="create-a-dev-space-in-azure"></a>Skapa en utvecklingsmiljö i Azure

För projektet som du nyss skapat väljer du **Azure Dev Spaces** i listrutan för startinställningar, som du ser nedan.

![](media/get-started-netcore-visualstudio/LaunchSettings.png)

I nästa dialogruta som visas kontrollerar du att du har loggat in med rätt konto och välj sedan ett befintligt kluster.

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog.png)

Lämna inställningen `default` i listrutan **Utrymme** tills vidare. Markera kryssrutan **Offentligt tillgänglig**, så att webbprogrammet är tillgängligt via en offentlig slutpunkt.

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog2.png)

Välj eller skapa klustret genom att klicka på **OK**.

Om du väljer ett kluster som inte har konfigurerats för användning med Azure Dev Spaces tillfrågas du om du vill konfigurera det.

![](media/get-started-netcore-visualstudio/Add-Azure-Dev-Spaces-Resource.png)

Välj **OK**. 

### <a name="look-at-the-files-added-to-project"></a>Titta på filerna som lagts till i projektet
Medan utvecklingsmiljön skapas kan du titta på filerna som lades till i projektet när du valde att använda en Azure-utvecklingsmiljö.

- En mapp med namnet `charts` har lagts till och ett [Helm-diagram](https://docs.helm.sh) har skapats för ditt program i den här mappen. Dessa filer används för att distribuera ditt program till utvecklingsmiljön.
- `Dockerfile` innehåller information som behövs för att paketera ditt program i Docker-standardformatet.
- `azds.yaml` innehåller konfigurationsinformation som krävs av utvecklingsmiljön, t.ex. huruvida programmet ska vara tillgängligt via en offentlig slutpunkt.

![](media/get-started-netcore-visualstudio/ProjectFiles.png)

## <a name="debug-a-container-in-kubernetes"></a>Felsöka en behållare i Kubernetes
När utvecklingsmiljön har skapats kan du felsöka programmet. Lägg till en brytpunkt i koden, till exempel på rad 20 i filen `HomeController.cs` där variabeln `Message` anges. Starta felsökningen genom att trycka på **F5**. 

Visual Studio kommunicerar med utvecklingsmiljön för att skapa och distribuera programmet och öppnar sedan en webbläsare där webbprogrammet körs. Det kan verka som om behållaren körs lokalt, men i själva verket körs den i utvecklingsmiljön i Azure. localhost-adressen beror på att Azure Dev Spaces skapar en tillfällig SSH-tunnel för behållaren som körs i Azure.

Klicka på länken **Om** längst upp på sidan för att utlösa brytpunkten. Du har fullständig åtkomst till felsökningsinformation precis som när koden körs lokalt, t.ex. anropsstack, lokala variabler, undantagsinformation och så vidare.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Arbeta med flera behållare och utveckling i team](get-started-netcore-visualstudio.md#call-another-container)