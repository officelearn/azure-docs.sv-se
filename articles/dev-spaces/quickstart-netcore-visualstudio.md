---
title: Utveckla med .NET Core på AKS med Azure Dev blanksteg och Visual Studio
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
ms.author: zarhoads
ms.date: 03/22/2019
ms.topic: quickstart
description: Snabb Kubernetes-utveckling med containrar och mikrotjänster i Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, behållare, Helm, tjänsten nät, tjänsten nät routning, kubectl, k8s
manager: jeconnoc
ms.custom: vs-azure
ms.workload: azure-vs
ms.openlocfilehash: 110962c03f0236ebb26c9ed586981b51f36c635f
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399228"
---
# <a name="quickstart-develop-with-net-core-on-kubernetes-with-azure-dev-spaces-visual-studio"></a>Snabbstart: Utveckla med .NET Core på Kubernetes med Azure Dev blanksteg (Visual Studio)

I den här guiden får du lära dig hur du:

- Ställa in Azure Dev Spaces med ett hanterat Kubernetes-kluster i Azure.
- Iterativt utvecklar kod i containrar med Visual Studio.
- Felsöka kod som körs i klustret med hjälp av Visual Studio.

## <a name="prerequisites"></a>Nödvändiga komponenter

- En Azure-prenumeration. Om du inte har någon, kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free).
- Visual Studio-2019 på Windows med arbetsbelastningen Azure Development installerad. Du kan också använda Visual Studio 2017 på Windows med arbetsbelastningen webbutveckling och [Visual Studio Tools for Kubernetes](https://aka.ms/get-vsk8stools) installerad. Om du inte har Visual Studio installerat kan du hämta det [här](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Skapa ett Azure Kubernetes Service-kluster

Du måste skapa ett AKS-kluster i en [region som stöds][supported-regions]. Skapa ett kluster:

1. Logga in på [Azure-portalen](https://portal.azure.com)
1. Välj *+ skapa en resurs > Kubernetes Service*. 
1. Ange den _prenumeration_, _resursgrupp_, _Kubernetes-klusternamn_, _Region_, _Kubernetes-version_, och _DNS-namnprefix_.

    ![Skapa AKS i Azure portal](media/get-started-netcore-visualstudio/create-aks-portal.png)

1. Klicka på *Granska + skapa*.
1. Klicka på *Skapa*.

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Aktivera Azure Dev blanksteg på AKS-kluster

Navigera till ditt AKS-kluster i Azure-portalen och klicka på *Dev blanksteg*. Ändra *aktivera Dev blanksteg* till *Ja* och klicka på *spara*.

![Aktivera Dev blanksteg i Azure portal](media/get-started-netcore-visualstudio/enable-dev-spaces-portal.png)

## <a name="create-a-new-aspnet-web-app"></a>Skapa en ny ASP.NET-webbapp

1. Öppna Visual Studio.
1. Skapa ett nytt projekt.
1. Välj *ASP.NET Core-Webbapp* och namnge projektet *webfrontend*.
1. Klicka på *OK*.
1. Välj *webbprogram (Model-View-Controller)* för mallen.
1. Välj *.NET Core* och *ASP.NET Core 2.0* högst upp.
1. Klicka på *OK*.

## <a name="connect-your-project-to-your-dev-space"></a>Ansluta ditt projekt till ditt dev-adressutrymme

I projektet, väljer **Azure Dev blanksteg** i listrutan Start inställningar som visas nedan.

![](media/get-started-netcore-visualstudio/LaunchSettings.png)

I Azure Dev blanksteg, väljer din *prenumeration* och *Azure Kubernetes-kluster*. Lämna *utrymme* inställd *standard* och aktivera den *offentligt tillgänglig* kryssrutan. Klicka på *OK*.

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog.png)

Den här processen distribuerar din tjänst till den *standard* dev utrymme med en offentligt tillgänglig URL. Om du väljer ett kluster som inte har konfigurerats för användning med Azure Dev Spaces tillfrågas du om du vill konfigurera det. Klicka på *OK*.

![](media/get-started-netcore-visualstudio/Add-Azure-Dev-Spaces-Resource.png)

En offentlig URL för tjänsten som körs i den *standard* dev yta ska visas i den *utdata* fönster:

```cmd
Starting warmup for project 'webfrontend'.
Waiting for namespace to be provisioned.
Using dev space 'default' with target 'MyAKS'
...
Successfully built 1234567890ab
Successfully tagged webfrontend:devspaces-11122233344455566
Built container image in 39s
Waiting for container...
36s

Service 'webfrontend' port 'http' is available at http://webfrontend.1234567890abcdef1234.eus.azds.io/
Service 'webfrontend' port 80 (http) is available at http://localhost:62266
Completed warmup for project 'webfrontend' in 125 seconds.
```

I exemplet ovan är en offentlig URL http://webfrontend.1234567890abcdef1234.eus.azds.io/. Gå till din tjänsts offentliga URL och interagera med tjänsten som körs i ditt dev-adressutrymme.

Den här processen kan ha inaktiverat offentlig åtkomst till din tjänst. Om du vill aktivera offentlig åtkomst, kan du uppdatera den [ingress-värde i den *values.yaml*][ingress-update].

## <a name="update-code"></a>Uppdatera kod

Om Visual Studio fortfarande är ansluten till ditt dev adressutrymme klickar du på stopp-knappen. Ändra rad 20 i `Controllers/HomeController.cs` till:
    
```csharp
ViewData["Message"] = "Your application description page in Azure.";
```

Spara dina ändringar och starta din tjänst med hjälp av **Azure Dev blanksteg** listrutan Start Inställningar. Öppna den offentliga URL: en för din tjänst i en webbläsare och klicka på *om*. Observera att din uppdaterade meddelande visas.

Kompilerar koden i befintliga behållaren att tillhandahålla en snabbare redigera/debug-loop i stället för att bygga om och omdistribuera en ny behållaravbildning varje gång kod ändringar görs, Azure Dev blanksteg inkrementellt.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Ställa in och använda brytpunkter för felsökning

Om Visual Studio fortfarande är ansluten till ditt dev adressutrymme klickar du på stopp-knappen. Öppna `Controllers/HomeController.cs` och klicka någonstans på rad 20 att placera markören där. Ange en brytpunkt når *F9* eller klicka på *felsöka* sedan */Radera brytpunkt*. Om du vill starta tjänsten i felsökningsläge i dev-utrymme når *F5* eller klicka på *felsöka* sedan *Starta felsökning*.

Öppna din tjänst i en webbläsare och Observera visas inget meddelande. Gå tillbaka till Visual Studio och notera rad 20 markeras. Brytpunkt som du angett har pausats tjänsten på rad 20. Om du vill återuppta tjänsten når *F5* eller klicka på *felsöka* sedan *Fortsätt*. Gå tillbaka till webbläsaren och Observera meddelandet visas nu.

När du kör din tjänst i Kubernetes ett internt, har du fullständig åtkomst till felsökningsinformation som anropsstacken, lokala variabler och undantagsinformation.

Ta bort brytpunkten genom att placera markören på rad 20 i `Controllers/HomeController.cs` och träffa *F9*.

## <a name="clean-up-your-azure-resources"></a>Rensa dina Azure-resurser

Navigera till din resursgrupp i Azure-portalen och klicka på *ta bort resursgrupp*. Du kan också använda den [az aks ta bort](/cli/azure/aks#az-aks-delete) kommando:

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Arbeta med flera containrar och utveckling i team](multi-service-netcore-visualstudio.md)

[ingress-update]: how-dev-spaces-works.md#how-running-your-code-is-configured
[supported-regions]: about.md#supported-regions-and-configurations
