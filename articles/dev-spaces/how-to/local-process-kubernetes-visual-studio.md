---
title: Använda lokal process med Kubernetes med Visual Studio (förhandsversion)
services: azure-dev-spaces
ms.date: 06/02/2020
ms.topic: conceptual
description: Lär dig hur du använder lokal process med Kubernetes med Visual Studio för att ansluta din utvecklings dator till ett Kubernetes-kluster med Azure dev Spaces
keywords: Lokal process med Kubernetes, Azure dev Spaces, dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes service, containers
ms.openlocfilehash: 8e89baeac8d9042a87685ed1268ed694db8d9455
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84316805"
---
# <a name="use-local-process-with-kubernetes-with-visual-studio-preview"></a>Använda lokal process med Kubernetes med Visual Studio (förhandsversion)

Lokal process med Kubernetes gör att du kan köra och felsöka kod på din utvecklings dator, medan du fortfarande är ansluten till ditt Kubernetes-kluster med resten av dina program eller tjänster. Om du till exempel har en stor mikrotjänsters arkitektur med många beroende tjänster och databaser, kan det vara svårt att replikera dessa beroenden på din utvecklings dator. Dessutom kan du skapa och distribuera kod till ditt Kubernetes-kluster för varje kod ändring under en inre loop-utveckling kan vara långsam, tids krävande och svårt att använda med en fel sökare.

Lokal process med Kubernetes Undvik att behöva skapa och distribuera din kod till klustret genom att i stället skapa en anslutning direkt mellan utvecklings datorn och klustret. Genom att ansluta din utvecklings dator till klustret medan du felsöker kan du snabbt testa och utveckla din tjänst i kontexten för det fullständiga programmet utan att skapa någon Docker-eller Kubernetes-konfiguration.

Lokal process med Kubernetes omdirigerar trafik mellan ditt anslutna Kubernetes-kluster och din utvecklings dator. Den här trafiken tillåter kod på din utvecklings dator och tjänster som körs i ditt Kubernetes-kluster för att kommunicera som om de befinner sig i samma Kubernetes-kluster. Lokal process med Kubernetes ger också ett sätt att replikera miljövariabler och monterade volymer som är tillgängliga för poddar i ditt Kubernetes-kluster på din utvecklings dator. Genom att ge åtkomst till miljövariabler och monterade volymer på din utvecklings dator kan du snabbt arbeta med din kod utan att behöva replikera dessa beroenden manuellt.

I den här guiden får du lära dig hur du använder lokal process med Kubernetes för att dirigera om trafik mellan ditt Kubernetes-kluster och kod som körs på din utvecklings dator. Den här guiden innehåller också ett skript för att distribuera ett stort exempel program med flera mikrotjänster i ett Kubernetes-kluster.

> [!IMPORTANT]
> Den här funktionen finns för närvarande som en förhandsversion. Förhandsversioner är tillgängliga för dig under förutsättning att du godkänner de [kompletterande användningsvillkoren][preview-terms]. Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).

## <a name="before-you-begin"></a>Innan du börjar

I den här guiden används [cykel delnings exempel programmet i Azure dev Spaces][bike-sharing-github] för att demonstrera anslutning av din utvecklings dator till ett Kubernetes-kluster. Om du redan har ett eget program som körs på ett Kubernetes-kluster kan du fortfarande följa stegen nedan och använda namnen på dina egna tjänster.

### <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free).
* [Azure CLI installerat][azure-cli].
* [Visual Studio 2019][visual-studio] version 16,7 Preview 2 eller senare som körs på Windows 10 med arbets belastningarna *ASP.net och webb utveckling* och *Azure Development* .
* [Azure dev Spaces CLI installerat][azds-cli].

### <a name="enable-the-local-process-with-kubernetes-preview-feature-in-visual-studio"></a>Aktivera den lokala processen med Kubernetes Preview-funktionen i Visual Studio

Om du vill aktivera lokal process med Kubernetes i Visual Studio klickar du på *verktyg*  >  *alternativ*  >  *miljö*för  >  *hands versions funktioner*. Välj *Aktivera lokal fel sökning för Kubernetes-tjänster*.

För .NET-konsol program måste du också installera paketet *Microsoft. VisualStudio. Azure. Kubernetes. Tools. mål* NuGet.

## <a name="create-a-kubernetes-cluster"></a>Skapa ett Kubernetes-kluster

Skapa ett AKS-kluster i en [region som stöds][supported-regions]. Kommandona nedan skapar en resurs grupp med namnet *MyResourceGroup* och ett AKS-kluster som kallas *MyAKS*.

```azurecli-interactive
az group create \
    --name MyResourceGroup \
    --location eastus

az aks create \
    --resource-group MyResourceGroup \
    --name MyAKS \
    --location eastus \
    --node-count 3 \
    --generate-ssh-keys
```

## <a name="install-the-sample-application"></a>Installera exempel programmet

Installera exempel programmet på klustret med hjälp av det tillhandahållna skriptet. Du kan köra det här skriptet på din utvecklings dator eller med hjälp av [Azure Cloud Shell][azure-cloud-shell].

```azurecli-interactive
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/
chmod +x ./local-process-quickstart.sh
./local-process-quickstart.sh -g MyResourceGroup -n MyAKS
```

Navigera till exempel programmet som kör klustret genom att öppna dess offentliga URL, som visas i utdata från installations skriptet.

```console
$ ./local-process-quickstart.sh -g MyResourceGroup -n MyAKS
Defaulting Dev spaces repository root to current directory : ~/dev-spaces
Setting the Kube context
...
To try out the app, open the url:
dev.bikesharingweb.EXTERNAL_IP.nip.io
```

I exemplet ovan är den offentliga URL: en `dev.bikesharingweb.EXTERNAL_IP.nip.io` .

## <a name="connect-to-your-cluster-and-debug-a-service"></a>Anslut till ditt kluster och Felsök en tjänst

Hämta och konfigurera Kubernetes CLI på din utvecklings dator för att ansluta till ditt Kubernetes-kluster med [AZ AKS get-credentials][az-aks-get-credentials].

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
```

Öppna *dev-Spaces/samples/BikeSharingApp/ReservationEngine/app. CSPROJ* från [Azure dev Spaces cykel dela exempel program][bike-sharing-github] i Visual Studio.

I projektet väljer du *lokal process med Kubernetes* i list rutan Start inställningar enligt nedan.

![Välj lokal process med Kubernetes](../media/local-process-kubernetes-visual-studio/choose-local-process.png)

Klicka på Start-knappen bredvid *lokal process med Kubernetes*. I dialog rutan *lokal process med Kubernetes* :

* Välj din prenumeration.
* Välj *MyAKS* för klustret.
* Välj *dev* för ditt namn område.
* Välj *reservationengine* för tjänsten som ska omdirigeras.
* Välj *app* för start profilen.
* Välj `http://dev.bikesharingweb.EXTERNAL_IP.nip.io` för URL: en för att starta webbläsaren.

![Välj lokal process med Kubernetes-kluster](../media/local-process-kubernetes-visual-studio/choose-local-process-cluster.png)

> [!IMPORTANT]
> Du kan bara omdirigera tjänster som har en enda pod.

Klicka på *Spara och starta fel sökning*.

All trafik i Kubernetes-klustret omdirigeras för *reservationengine* -tjänsten till den version av programmet som körs i utvecklings datorn. Lokal process med Kubernetes dirigerar också all utgående trafik från programmet tillbaka till ditt Kubernetes-kluster.

> [!NOTE]
> Du uppmanas att göra det möjligt för *KubernetesDNSManager* att köra utökade och modifiera värd filen.

Din utvecklings dator är ansluten när statusfältet visar att du är ansluten till *reservationengine* -tjänsten.

![Utvecklings dator ansluten](../media/local-process-kubernetes-visual-studio/development-computer-connected.png)

> [!NOTE]
> När subesquent startas visas inte dialog rutan *lokal process med Kubernetes* . Du uppdaterar inställningarna i *fel söknings* fönstret i projekt egenskaperna.

När din utvecklings dator är ansluten börjar trafiken omdirigeras till utvecklings datorn för tjänsten som du ersätter.

## <a name="set-a-break-point"></a>Ange en Bryt punkt

Öppna [BikesHelper.cs][bikeshelper-cs-breakpoint] och klicka någonstans på rad 26 för att placera markören där. Ange en Bryt punkt genom att trycka på *F9* eller klicka på *Felsök* och sedan på *Växla Bryt punkt*.

Navigera till exempel programmet genom att öppna den offentliga URL: en. Välj *Aurelia Briggs (kund)* som användare och välj sedan en cykel att hyra. Klicka på *Hyr cykel*. Gå tillbaka till Visual Studio och Observera att rad 26 är markerat. Den Bryt punkt som du har angett har pausat tjänsten på rad 26. Tryck på *F5* eller klicka på *Felsök* och *Fortsätt*om du vill återuppta tjänsten. Gå tillbaka till webbläsaren och kontrol lera att sidan visar att du har hyr cykeln.

Ta bort Bryt punkten genom att placera markören på rad 26 i `BikesHelper.cs` och trycka på *F9*.

> [!NOTE]
> Som standard kopplar inte fel söknings aktiviteten till din utvecklings dator från ditt Kubernetes-kluster. Du kan ändra det här beteendet genom att ändra *från koppling efter fel sökning* till *falskt* i avsnittet *Kubernetes fel söknings verktyg* i fel söknings alternativen. När du har uppdaterat den här inställningen är din utvecklings dator fortfarande ansluten när du stoppar och startar fel sökning. Om du vill koppla från din utvecklings dator från klustret klickar du på knappen *koppla* från i verktygsfältet.
>
> Om Visual Studio plötsligt avslutar anslutningen till klustret eller avbryter, kanske tjänsten som du omdirigerar inte återställs till det ursprungliga läget innan du anslöt till den lokala processen med Kubernetes. Information om hur du löser det här problemet finns i [fel söknings guiden][troubleshooting].

## <a name="using-logging-and-diagnostics"></a>Använda loggning och diagnostik

Du hittar diagnostikloggar i `Azure Dev Spaces` katalogen i [utvecklings datorns *Temp* -katalog][azds-tmp-dir].

## <a name="remove-the-sample-application-from-your-cluster"></a>Ta bort exempel programmet från klustret

Använd det tillhandahållna skriptet för att ta bort exempel programmet från klustret.

```azurecli-interactive
./local-process-quickstart.sh -c -g MyResourceGroup -n MyAKS
```

## <a name="next-steps"></a>Nästa steg

Lär dig hur du använder Azure dev Spaces och GitHub-åtgärder för att testa ändringar från en pull-begäran direkt i AKS innan pull-begäran slås samman i huvud grenen för din databas.

> [!div class="nextstepaction"]
> [GitHub åtgärder & Azure Kubernetes service][gh-actions]

[azds-cli]: install-dev-spaces.md#install-the-client-side-tools
[azds-tmp-dir]: ../troubleshooting.md#before-you-begin
[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[azure-cloud-shell]: ../../cloud-shell/overview.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-vs-code]: https://marketplace.visualstudio.com/items?itemName=ms-kubernetes-tools.vscode-aks-tools
[bike-sharing-github]: https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp
[gh-actions]: github-actions.md
[preview-terms]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[bikeshelper-cs-breakpoint]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/ReservationEngine/BikesHelper.cs#L26
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[troubleshooting]: ../troubleshooting.md#fail-to-restore-original-configuration-of-deployment-on-cluster
[visual-studio]: https://www.visualstudio.com/vs/