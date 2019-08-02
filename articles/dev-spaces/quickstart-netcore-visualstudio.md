---
title: Felsöka och iterera med Visual Studio och .NET Core på AKS med Azure dev Spaces
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 03/22/2019
ms.topic: quickstart
description: Snabb Kubernetes-utveckling med containrar och mikrotjänster i Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes service, Containers, Helm, service nät, service nät-routning, kubectl, K8s
manager: gwallace
ms.custom: vs-azure
ms.workload: azure-vs
ms.openlocfilehash: ece47c86fba6bc975a4146f596fa001014352a4f
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68725849"
---
# <a name="quickstart-debug-and-iterate-with-visual-studio-and-net-core-on-kubernetes-with-azure-dev-spaces"></a>Snabbstart: Felsöka och iterera med Visual Studio och .NET Core på Kubernetes med Azure dev Spaces

I den här guiden får du lära dig hur du:

- Ställa in Azure Dev Spaces med ett hanterat Kubernetes-kluster i Azure.
- Iterativt utvecklar kod i containrar med Visual Studio.
- Felsöka kod som körs i klustret med Visual Studio.

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration. Om du inte har någon, kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free).
- Visual Studio 2019 på Windows med arbets belastningen Azure Development installerad. Du kan också använda Visual Studio 2017 i Windows med arbets belastningen webb utveckling och [Visual Studio Tools för Kubernetes](https://aka.ms/get-vsk8stools) installerat. Om du inte har Visual Studio installerat kan du ladda ned det [här](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Skapa ett Azure Kubernetes service-kluster

Du måste skapa ett AKS-kluster i en [region som stöds][supported-regions]. Så här skapar du ett kluster:

1. Logga in på [Azure-portalen](https://portal.azure.com)
1. Välj *+ skapa en resurs > Kubernetes-tjänst*. 
1. Ange _prenumeration_, _resurs grupp_, _Kubernetes kluster namn_, _region_, _Kubernetes-version_och _DNS-namn-prefix_.

    ![Skapa AKS i Azure Portal](media/get-started-netcore-visualstudio/create-aks-portal.png)

1. Klicka på *Granska + skapa*.
1. Klicka på *Skapa*.

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Aktivera Azure dev Spaces i ditt AKS-kluster

Navigera till ditt AKS-kluster i Azure Portal och klicka på *dev Spaces*. Ändra *Aktivera dev Spaces* till *Ja* och klicka på *Spara*.

![Aktivera dev Spaces i Azure Portal](media/get-started-netcore-visualstudio/enable-dev-spaces-portal.png)

## <a name="create-a-new-aspnet-web-app"></a>Skapa en ny ASP.NET-webbapp

1. Öppna Visual Studio.
1. Skapa ett nytt projekt.
1. Välj *ASP.net Core webb program* och ge projektet *webfrontend*.
1. Klicka på *OK*.
1. När du uppmanas väljer du *webb program (modell-Visa-kontrollant)* för mallen.
1. Välj *.net Core* och *ASP.net Core 2,0* överst.
1. Klicka på *OK*.

## <a name="connect-your-project-to-your-dev-space"></a>Anslut ditt projekt till ditt utvecklings utrymme

I projektet väljer du **Azure dev Spaces** från List rutan Start inställningar enligt nedan.

![](media/get-started-netcore-visualstudio/LaunchSettings.png)

I dialog rutan Azure dev Spaces väljer du din *prenumeration* och *Azure Kubernetes-kluster*. Lämna *utrymme* inställt på *standard* och aktivera kryss rutan *offentligt tillgänglig* . Klicka på *OK*.

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog.png)

Den här processen distribuerar tjänsten till *standard* dev-utrymmet med en offentligt tillgänglig URL. Om du väljer ett kluster som inte har konfigurerats för användning med Azure Dev Spaces tillfrågas du om du vill konfigurera det. Klicka på *OK*.

![](media/get-started-netcore-visualstudio/Add-Azure-Dev-Spaces-Resource.png)

Den offentliga URL: en för tjänsten som körs i *standard* dev-utrymmet visas i fönstret *utdata* :

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

I exemplet ovan är http://webfrontend.1234567890abcdef1234.eus.azds.io/ den offentliga URL: en. Navigera till tjänstens offentliga URL och interagera med tjänsten som körs i ditt utvecklings utrymme.

Den här processen kan ha inaktiverat offentlig åtkomst till din tjänst. Om du vill aktivera offentlig åtkomst kan du uppdatera [ingress-värdet i *Values. yaml*][ingress-update].

## <a name="update-code"></a>Uppdatera kod

Om Visual Studio fortfarande är anslutet till ditt dev-utrymme klickar du på knappen Stoppa. Ändra rad 20 i `Controllers/HomeController.cs` till:
    
```csharp
ViewData["Message"] = "Your application description page in Azure.";
```

Spara ändringarna och starta tjänsten med hjälp av **Azure dev Spaces** från List rutan Start Inställningar. Öppna tjänstens offentliga URL i en webbläsare och klicka på *om*. Observera att det uppdaterade meddelandet visas.

I stället för att bygga om och omdistribuera en ny behållar avbildning varje tids kods redigering görs, kompilerar Azure dev Spaces stegvis om koden i den befintliga behållaren för att ge en snabbare redigerings-/fel söknings slinga.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Inställning och användning av Bryt punkter för fel sökning

Om Visual Studio fortfarande är anslutet till ditt dev-utrymme klickar du på knappen Stoppa. Öppna `Controllers/HomeController.cs` och klicka någonstans på rad 20 för att placera markören där. Ange en Bryt punkt genom att trycka på *F9* eller klicka på *Felsök* och sedan på *Växla Bryt punkt*. Starta tjänsten i fel söknings läge i ditt dev-utrymme genom att trycka på *F5* eller klicka på *Felsök* och *Starta fel sökning*.

Öppna din tjänst i en webbläsare och Observera att inget meddelande visas. Gå tillbaka till Visual Studio och Observera att rad 20 är markerad. Den Bryt punkten som du har angett har pausat tjänsten på rad 20. Tryck på *F5* eller klicka på *Felsök* och *Fortsätt*om du vill återuppta tjänsten. Gå tillbaka till webbläsaren och Observera att meddelandet visas nu.

När du kör tjänsten i Kubernetes med en fel sökare ansluten har du fullständig åtkomst till felsöknings information som anrops stack, lokala variabler och undantags information.

Ta bort Bryt punkten genom att placera markören på rad 20 `Controllers/HomeController.cs` i och trycka på *F9*.

## <a name="clean-up-your-azure-resources"></a>Rensa dina Azure-resurser

Navigera till din resurs grupp i Azure Portal och klicka på *ta bort resurs grupp*. Du kan också använda kommandot [AZ AKS Delete](/cli/azure/aks#az-aks-delete) :

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Arbeta med flera containrar och utveckling i team](multi-service-netcore-visualstudio.md)

[ingress-update]: how-dev-spaces-works.md#how-running-your-code-is-configured
[supported-regions]: about.md#supported-regions-and-configurations
