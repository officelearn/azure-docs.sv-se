---
title: 'Felsöka och iterera på Kubernetes: Visual Studio & .NET Core'
services: azure-dev-spaces
ms.date: 11/13/2019
ms.topic: quickstart
description: Den här snabbstarten visar hur du använder Azure Dev Spaces och Visual Studio för att felsöka och snabbt iterera ett .NET Core-program på Azure Kubernetes Service
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, behållare, Helm, servicenät, routning av tjänstnät, kubectl, k8s
manager: gwallace
ms.custom: vs-azure
ms.workload: azure-vs
ms.openlocfilehash: 5d0d1541600f4c10b021d5d7f7f435f1aa5ae589
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "80239726"
---
# <a name="quickstart-debug-and-iterate-on-kubernetes-visual-studio--net-core---azure-dev-spaces"></a>Snabbstart: Felsökning och iterera på Kubernetes: Visual Studio & .NET Core - Azure Dev Spaces

I den här guiden får du lära dig hur du:

- Ställa in Azure Dev Spaces med ett hanterat Kubernetes-kluster i Azure.
- Iterativt utvecklar kod i containrar med Visual Studio.
- Felsöka kod som körs i klustret med Visual Studio.

Med Azure Dev Spaces kan du också felsöka och iterera med:
- [Java- och Visual Studio-kod](quickstart-java.md)
- [Nod.js och Visual Studio-kod](quickstart-nodejs.md)
- [.NET Core- och Visual Studio-kod](quickstart-netcore.md)

## <a name="prerequisites"></a>Krav

- En Azure-prenumeration. Om du inte har någon, kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free).
- Visual Studio 2019 på Windows med Azure Development-arbetsbelastningen installerad. Du kan också använda Visual Studio 2017 i Windows med arbetsbelastningen Webbutveckling och [Visual Studio Tools för Kubernetes](https://aka.ms/get-vsk8stools) installerade. Om du inte har Installerat Visual Studio laddar du ned den [här](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Skapa ett Azure Kubernetes-tjänstkluster

Du måste skapa ett AKS-kluster i en [region som stöds][supported-regions]. Så här skapar du ett kluster:

1. Logga in på [Azure-portalen](https://portal.azure.com)
1. Välj *+ Skapa en resurs > Kubernetes-tjänsten*. 
1. Ange prefixet _Prenumeration,_ _Resursgrupp_, _Kubernetes,_ _Region,_ _Kubernetes_och _DNS-namnprefix_.

    ![Skapa AKS i Azure-portalen](media/get-started-netcore-visualstudio/create-aks-portal.png)

1. Klicka på *Granska + skapa*.
1. Klicka på *Skapa*.

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Aktivera Azure Dev Spaces i AKS-klustret

Navigera till AKS-klustret i Azure-portalen och klicka på *Dev Spaces*. Ändra *Använd utvecklingsutrymmen* till *Ja* och klicka på *Spara*.

![Aktivera dev spaces i Azure-portalen](media/get-started-netcore-visualstudio/enable-dev-spaces-portal.png)

## <a name="create-a-new-aspnet-web-app"></a>Skapa en ny ASP.NET webbapp

1. Öppna Visual Studio.
1. Skapa ett nytt projekt.
1. Välj *ASP.NET Core Web Application* och klicka på *Nästa*.
1. Namnge *projektets webbfönster* och klicka på *Skapa*.
1. När du uppmanas till det väljer du *Webbprogram (Modellvykontrollant)* för mallen.
1. Välj *.NET Core* och *ASP.NET Core 2.1* högst upp.
1. Klicka på *Skapa*.

## <a name="connect-your-project-to-your-dev-space"></a>Anslut projektet till ditt utvecklingsutrymme

I projektet väljer du **Azure Dev Spaces** i listrutan startinställningar som visas nedan.

![](media/get-started-netcore-visualstudio/LaunchSettings.png)

I dialogrutan Azure Dev Spaces väljer du ditt *Prenumerations-* och *Azure Kubernetes-kluster*. Lämna *blanksteg* inställt på *standard* och aktivera kryssrutan *Allmänt tillgänglig.* Klicka på *OK*.

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog.png)

Den här processen distribuerar *default* tjänsten till standarddelegeringsutrymmet med en url som är allmänt tillgänglig. Om du väljer ett kluster som inte har konfigurerats för användning med Azure Dev Spaces tillfrågas du om du vill konfigurera det. Klicka på *OK*.

![](media/get-started-netcore-visualstudio/Add-Azure-Dev-Spaces-Resource.png)

Den offentliga URL:en för *default* tjänsten som körs i standardutgivningsutrymmet visas i *utdatafönstret:*

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

Service 'webfrontend' port 'http' is available at http://default.webfrontend.1234567890abcdef1234.eus.azds.io/
Service 'webfrontend' port 80 (http) is available at http://localhost:62266
Completed warmup for project 'webfrontend' in 125 seconds.
```

I exemplet ovan är http://default.webfrontend.1234567890abcdef1234.eus.azds.io/den offentliga webbadressen . 

Välj **Felsökning** och sedan **Starta felsökning**. Efter några sekunder startar tjänsten och Visual Studio öppnar en webbläsare med tjänstens offentliga webbadress. Om en webbläsare inte öppnas automatiskt navigerar du till tjänstens offentliga WEBBADRESS i en webbläsare och interagerar med tjänsten som körs i ditt utvecklingsutrymme.

Den här processen kan ha inaktiverat allmänhetens åtkomst till din tjänst. Om du vill aktivera offentlig åtkomst kan du uppdatera [ingressvärdet i *values.yaml*][ingress-update].

## <a name="update-code"></a>Uppdatera kod

Om Visual Studio fortfarande är anslutet till ditt utvecklingsutrymme klickar du på stoppknappen. Ändra linje 20 i `Controllers/HomeController.cs` till:
    
```csharp
ViewData["Message"] = "Your application description page in Azure.";
```

Spara ändringarna och välj **Felsök sedan** **Starta felsökning**. Efter några sekunder startar tjänsten och Visual Studio öppnar en webbläsare med tjänstens offentliga webbadress. Om en webbläsare inte öppnas automatiskt navigerar du i tjänstens offentliga webbadress i en webbläsare och klickar på *Om*. Observera att det uppdaterade meddelandet visas.

I stället för att återskapa och distribuera om en ny behållaravbildning varje gång kodredigeringar görs, kompilerar Azure Dev Spaces stegvis om koden i den befintliga behållaren för att tillhandahålla en snabbare redigerings-/felsökningsloop.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Ställa in och använda brytpunkter för felsökning

Om Visual Studio fortfarande är anslutet till ditt utvecklingsutrymme klickar du på stoppknappen. Öppna `Controllers/HomeController.cs` och klicka någonstans på linje 20 för att sätta markören där. Om du vill ange en brytpunkt hit *F9* eller klicka på *Felsök* sedan *Växla brytpunkt*. Om du vill starta tjänsten i felsökningsläge i utvecklingsutrymmet trycker du på *F5* eller klickar på *Felsök* sedan *Starta felsökning*.

Öppna din tjänst i en webbläsare och märker att inget meddelande visas. Gå tillbaka till Visual Studio och observera linje 20 är markerad. Brytpunkten som du anger har pausat tjänsten på rad 20. Om du vill återuppta tjänsten trycker du på *F5* eller klickar på *Felsök* sedan *Fortsätt*. Gå tillbaka till din webbläsare och lägg märke till att meddelandet nu visas.

När du kör tjänsten i Kubernetes med en felsökare bifogad, har du full tillgång till felsökningsinformation som anropsstacken, lokala variabler och undantagsinformation.

Ta bort brytpunkten genom att sätta markören på linje 20 i `Controllers/HomeController.cs` och trycka på *F9*.

## <a name="clean-up-your-azure-resources"></a>Rensa dina Azure-resurser

Navigera till resursgruppen i Azure-portalen och klicka på *Ta bort resursgrupp*. Du kan också använda kommandot [az aks delete:](/cli/azure/aks#az-aks-delete)

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Arbeta med flera containrar och utveckling i team](multi-service-netcore-visualstudio.md)

[ingress-update]: how-dev-spaces-works-up.md#how-running-your-code-is-configured
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
