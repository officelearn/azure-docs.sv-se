---
title: 'Skapa ett Kubernetes dev-utrymme: Visual Studio & .NET Core'
services: azure-dev-spaces
ms.custom: vs-azure
ms.workload: azure-vs
ms.date: 07/09/2018
ms.topic: tutorial
description: Den här självstudien visar hur du använder Azure dev Spaces och Visual Studio för att felsöka och snabbt iterera ett .NET Core-program i Azure Kubernetes service
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes service, Containers, Helm, service nät, service nät-routning, kubectl, K8s
ms.openlocfilehash: ba90cbc8bc0267f1fba8c9495886bdc8ce2ac5e3
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2020
ms.locfileid: "83995912"
---
# <a name="create-a-kubernetes-dev-space-visual-studio-and-net-core-with-azure-dev-spaces"></a>Skapa ett Kubernetes dev-utrymme: Visual Studio och .NET Core med Azure dev Spaces

I den här guiden får du lära dig hur du:

- Ställa in Azure Dev Spaces med ett hanterat Kubernetes-kluster i Azure.
- Iterativt utvecklar kod i containrar med Visual Studio.
- Oberoende utvecklar två separata tjänster och använder Kubernetes DNS-tjänstidentifiering för att anropa en annan tjänst.
- Effektivt utvecklar och testar din kod i en teammiljö.

> [!Note]
> **Om du får fastna när som** helst kan du läsa avsnittet [fel sökning](troubleshooting.md) .

## <a name="install-the-azure-cli"></a>Installera Azure CLI
Azure Dev Spaces kräver minimal konfiguration av den lokala datorn. Merparten av utvecklarmiljöns konfiguration lagras i molnet och kan delas med andra användare. Börja genom att ladda ned och köra [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

### <a name="sign-in-to-azure-cli"></a>Logga in på Azure CLI
Logga in i Azure. Skriv in följande kommando i ett terminalfönster:

```azurecli
az login
```

> [!Note]
> Om du inte har någon Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free).

#### <a name="if-you-have-multiple-azure-subscriptions"></a>Om du har flera Azure-prenumerationer ...
Du kan visa dina prenumerationer genom att köra: 

```azurecli
az account list --output table
```

Leta upp prenumerationen som har *värdet sant* för *IsDefault*.
Om det här inte är den prenumeration som du vill använda kan du ändra standardprenumerationen:

```azurecli
az account set --subscription <subscription ID>
```

## <a name="create-a-kubernetes-cluster-enabled-for-azure-dev-spaces"></a>Skapa ett Kubernetes-kluster som är aktiverat för Azure Dev Spaces

I kommando tolken skapar du resurs gruppen i en [region som stöder Azure dev Spaces][supported-regions].

```azurecli
az group create --name MyResourceGroup --location <region>
```

Skapa ett Kubernetes-kluster med följande kommando:

```azurecli
az aks create -g MyResourceGroup -n MyAKS --location <region> --generate-ssh-keys
```

Det tar några minuter att skapa klustret.

### <a name="configure-your-aks-cluster-to-use-azure-dev-spaces"></a>Konfigurera ditt AKS-kluster för att använda Azure Dev Spaces

Ange följande Azure CLI-kommando med hjälp av den resursgrupp som innehåller ditt AKS-kluster och AKS-klusternamn. Kommandot konfigurerar ditt kluster med stöd för Azure Dev Spaces.

   ```azurecli
   az aks use-dev-spaces -g MyResourceGroup -n MyAKS
   ```
   
> [!IMPORTANT]
> Azure Dev Spaces-konfigurationsprocessen tar bort namnområdet `azds` i klustret, om det finns.

## <a name="get-the-visual-studio-tools"></a>Hämta Visual Studio-verktygen
Installera den senaste versionen av [Visual Studio 2019](https://www.visualstudio.com/vs/) i Windows med arbets belastningen Azure Development.

## <a name="create-a-web-app-running-in-a-container"></a>Skapa en webbapp som körs i en container

I det här avsnittet ska du skapa en ASP.NET Core-webbapp och få den igång i en behållare i Kubernetes.

### <a name="create-an-aspnet-web-app"></a>Skapa en ASP.NET-webbapp

Skapa ett nytt projekt i Visual Studio. För närvarande måste projektet vara ett **ASP.NET Core-webbprogram**. Namnge projektet "**webfrontend**".

![](media/get-started-netcore-visualstudio/NewProjectDialog1.png)

Välj mallen **Webbprogram (MVC, Model-View-Controller)** och välj **.NET Core** och **ASP.NET Core 2.0** som mål i de två listrutorna överst i dialogrutan. Klicka på **OK** för att skapa projektet.

![](media/get-started-netcore-visualstudio/NewProjectDialog2.png)

### <a name="enable-dev-spaces-for-an-aks-cluster"></a>Aktivera Dev Spaces för ett AKS-kluster

För projektet som du nyss skapat väljer du **Azure Dev Spaces** i listrutan för startinställningar, som du ser nedan.

![](media/get-started-netcore-visualstudio/LaunchSettings.png)

I nästa dialogruta som visas kontrollerar du att du har loggat in med rätt konto och väljer sedan ett befintligt Kubernetes-kluster eller skapar ett nytt.

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog.PNG)

Lämna standardinställningen `default` i listrutan **Utrymme** tills vidare. Du lär dig mer om det här alternativet längre fram. Markera kryssrutan **Offentligt tillgänglig**, så att webbprogrammet är tillgängligt via en offentlig slutpunkt. Den här inställningen är inte obligatorisk, men är användbar för att demonstrera några begrepp längre fram i den här genomgången. Men oroa dig inte, du kan felsöka din webbplats med hjälp av Visual Studio oavsett om du väljer inställningen eller inte.

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog2.png)

Välj eller skapa klustret genom att klicka på **OK**.

Om du väljer ett kluster som inte har konfigurerats för användning med Azure Dev Spaces tillfrågas du om du vill konfigurera det.

![](media/get-started-netcore-visualstudio/Add-Azure-Dev-Spaces-Resource.png)

Välj **OK**.

> [!IMPORTANT]
> Azure Dev Spaces-konfigurationsprocessen tar bort namnområdet `azds` i klustret, om det finns.

 En bakgrundsaktivitet startas och de nödvändiga inställningarna konfigureras. Åtgärden tar flera minuter att slutföra. Du kan kontrollera att åtgärden fortfarande körs genom att hovra med muspekaren över ikonen för **bakgrundsaktiviteter** i det nedre vänstra hörnet i statusfältet, som du ser i följande bild.

![](media/get-started-netcore-visualstudio/BackgroundTasks.PNG)

> [!Note]
> Du kan inte felsöka programmet förrän utvecklingsmiljön har skapats.

### <a name="look-at-the-files-added-to-project"></a>Titta på filerna som lagts till i projektet
Medan utvecklingsmiljön skapas kan du titta på filerna som lades till i projektet när du valde att använda en utvecklingsmiljö.

Först ser du att en mapp med namnet `charts` har lagts till och att ett [Helm-diagram](https://docs.helm.sh) har skapats för ditt program i den här mappen. Dessa filer används för att distribuera ditt program till utvecklingsmiljön.

Du ser att en fil med namnet `Dockerfile` har lagts till. Den här filen innehåller information som behövs för att paketera ditt program i Docker-standardformatet.

Slutligen ser du en fil med namnet `azds.yaml`, som innehåller den utvecklingstidskonfiguration som krävs av utvecklingsmiljön.

![](media/get-started-netcore-visualstudio/ProjectFiles.png)

## <a name="debug-a-container-in-kubernetes"></a>Felsöka en container i Kubernetes
När utvecklingsmiljön har skapats kan du felsöka programmet. Lägg till en brytpunkt i koden, till exempel på rad 20 i filen `HomeController.cs` där variabeln `Message` anges. Starta felsökningen genom att trycka på **F5**. 

Visual Studio kommunicerar med utvecklingsmiljön för att skapa och distribuera programmet och öppnar sedan en webbläsare där webbprogrammet körs. Det kan verka som om containern körs lokalt, men i själva verket körs den i utvecklingsmiljön i Azure. localhost-adressen beror på att Azure Dev Spaces skapar en tillfällig SSH-tunnel för containern som körs i AKS.

Klicka på länken **Om** längst upp på sidan för att utlösa brytpunkten. Du har fullständig åtkomst till felsökningsinformation precis som när koden körs lokalt, t.ex. anropsstack, lokala variabler, undantagsinformation och så vidare.

## <a name="iteratively-develop-code"></a>Iterativ kodutveckling

Azure Dev Spaces handlar om mer än att bara få kod att köra i Kubernetes – det handlar om att du snabbt och löpande kan se effekten av dina kodändringar i en Kubernetes-miljö i molnet.

### <a name="update-a-content-file"></a>Uppdatera en innehållsfil


1. Leta upp filen `./Views/Home/Index.cshtml` och gör en ändring i HTML-koden. Ändra till exempel [rad 73 som läser `<h2>Application uses</h2>` ](https://github.com/Azure/dev-spaces/blob/master/samples/dotnetcore/getting-started/webfrontend/Views/Home/Index.cshtml#L73) till något som: 
  
    ```html
    <h2>Hello k8s in Azure!</h2>`
    ```

2. Spara filen.
3. Gå till webbläsaren och uppdatera sidan. Den uppdaterade HTML-koden bör visas på webbsidan.

Vad hände? Redigering av innehållsfiler som HTML och CSS kräver inte omkompilering i en .NET Core-webbapp. En aktiv F5-session synkroniserar automatiskt ändrade innehållsfiler i containern som körs i AKS, så att du genast kan se dina innehållsändringar.

### <a name="update-a-code-file"></a>Uppdatera en kodfil
Uppdateringar av kodfiler kräver lite mer arbete eftersom .NET Core-appar måste återskapas och skapa uppdaterade binärfiler för programmet.

1. Stoppa felsökaren i Visual Studio.
1. Öppna kodfilen `Controllers/HomeController.cs` och ändra meddelandet som ska visas på sidan Om: `ViewData["Message"] = "Your application description page.";`
1. Spara filen.
1. Starta felsökningen igen genom att trycka på **F5**. 

I stället för att återskapa och distribuera om en ny containeravbildning varje gång koden ändras, vilket ofta tar lång tid, kompilerar Azure Dev Spaces om koden inkrementellt i den befintliga containern för snabbare redigerings- och felsökningsförlopp.

Uppdatera webbappen i webbläsaren och gå till sidan Om. Nu bör ditt anpassade meddelande visas i användargränssnittet.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om utveckling för flera tjänster](multi-service-netcore-visualstudio.md)


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service