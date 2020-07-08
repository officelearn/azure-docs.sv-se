---
title: Använda lokal process med Kubernetes med Visual Studio Code (förhandsversion)
services: azure-dev-spaces
ms.date: 06/02/2020
ms.topic: conceptual
description: Lär dig hur du använder lokal process med Kubernetes för att ansluta din utvecklings dator till ett Kubernetes-kluster med Azure dev Spaces
keywords: Lokal process med Kubernetes, Azure dev Spaces, dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes service, containers
ms.openlocfilehash: 23a94528ffa4e9e412f472349ea26d1a14003616
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84316742"
---
# <a name="use-local-process-with-kubernetes-with-visual-studio-code-preview"></a>Använda lokal process med Kubernetes med Visual Studio Code (förhandsversion)

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
* [Visual Studio Code][vs-code] körs på MacOS eller Windows 10.
* [Azure dev Spaces][azds-vs-code] -tillägget 2.0.220200601 eller senare installerat i Visual Studio Code.
* [Azure dev Spaces CLI installerat][azds-cli].

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

> [!IMPORTANT]
> Du måste ha *ägar* -eller *deltagar* åtkomst till klustret för att kunna köra skriptet.

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

Öppna *dev-Spaces/samples/BikeSharingApp/Bikes* från [Azure dev Spaces cykel dela exempel program][bike-sharing-github] i Visual Studio Code. Öppna Azure Kubernetes service-tillägget och välj *dev* -namnutrymmet i *MyAKS* -klustret.

![Välj namnrymd](../media/local-process-kubernetes-vs-code/select-namespace.png)

Använd `npm install` kommandot för att installera beroenden för programmet.

```console
npm install
```

Välj ikonen *Felsök* till vänster och välj *lokal process med Kubernetes (för hands version)* överst.

![Välj lokal process med Kubernetes](../media/local-process-kubernetes-vs-code/choose-local-process.png)

Klicka på Start-knappen bredvid *lokal process med Kubernetes (för hands version)*. Första gången du kör den här start konfigurationen uppmanas du att konfigurera den tjänst som du vill ersätta, porten som ska vidarebefordras från utvecklings datorn och start uppgiften som ska användas.

Välj tjänsten *Bikes* .

![Välj tjänst](../media/local-process-kubernetes-vs-code/choose-service.png)

All trafik i Kubernetes-klustret omdirigeras för tjänsten *Bikes* till den version av programmet som körs i utvecklings datorn. Lokal process med Kubernetes dirigerar också all utgående trafik från programmet tillbaka till ditt Kubernetes-kluster.

> [!IMPORTANT]
> Du kan bara omdirigera tjänster som har en enda pod.

När du har valt tjänsten uppmanas du att ange TCP-port för ditt lokala program. I det här exemplet anger du *3000*.

![Anslut Välj port](../media/local-process-kubernetes-vs-code/choose-port.png)

Välj *starta via NPM* som start uppgift.

![Anslut Välj Starta uppgift](../media/local-process-kubernetes-vs-code/choose-launch.png)

> [!NOTE]
> Du uppmanas att göra det möjligt för *KubernetesDNSManager* att köra utökade och modifiera värd filen.

Utvecklings datorn är ansluten när statusfältet aktiverar orange och dev Spaces-tillägget visar att du är ansluten.

![Utvecklings dator ansluten](../media/local-process-kubernetes-vs-code/development-computer-connected.png)

> [!NOTE]
> När subesquent startas uppmanas du inte att ange tjänst namnet, porten eller start uppgiften. Dessa värden sparas i *. VSCode/tasks.jspå*.

När din utvecklings dator är ansluten börjar trafiken omdirigeras till utvecklings datorn för tjänsten som du ersätter.

## <a name="set-a-break-point"></a>Ange en Bryt punkt

Öppna [server.js][server-js-breakpoint] och klicka någonstans på rad 233 för att placera markören där. Ange en Bryt punkt genom att trycka på *F9* eller klicka på *Kör* och sedan på *Växla Bryt punkt*.

Navigera till exempel programmet genom att öppna den offentliga URL: en. Välj *Aurelia Briggs (kund)* som användare och välj sedan en cykel att hyra. Observera att bilden för cykeln inte läses in. Gå tillbaka till Visual Studio Code och Observera att rad 233 är markerad. Den Bryt punkten som du har angett har pausat tjänsten på rad 233. Tryck på *F5* eller *Kör* och *Fortsätt*om du vill återuppta tjänsten. Gå tillbaka till webbläsaren och kontrol lera att du ser en platshållarbild för cykeln.

Ta bort Bryt punkten genom att placera markören på rad 233 i `server.js` och trycka på *F9*.

### <a name="update-your-application"></a>Uppdatera programmet

Redigera `server.js` för att ta bort raderna 234 och 235:

```javascript
    // Hard code image url *FIX ME*
    theBike.imageUrl = "/static/logo.svg";
```

Avsnittet bör nu se ut så här:

```javascript
    var theBike = result;
    theBike.id = theBike._id;
    delete theBike._id;
```

Spara ändringarna och klicka på *Kör* *starta om fel sökning*. När du har anslutit igen uppdaterar du webbläsaren och kontrollerar att du inte längre ser en platshållarbild för cykeln.

Klicka på *Kör* och *stoppa fel* sökningen för att stoppa fel söknings programmet.

> [!NOTE]
> Som standard kopplar inte fel söknings aktiviteten till din utvecklings dator från ditt Kubernetes-kluster. Du kan ändra det här beteendet genom att söka efter *lokal process med Kubernetes: koppla från efter fel sökning* i Visual Studio Code-inställningarna och ta bort kryss rutan bredvid *Koppla från automatiskt när fel sökning slutar.* När du har uppdaterat den här inställningen är din utvecklings dator fortfarande ansluten när du stoppar och startar fel sökning. Om du vill koppla från din utvecklings dator från klustret klickar du på tillägget Azure dev Spaces i statusfältet och väljer sedan *Koppla från aktuell session*.
>
> Om Visual Studio Code avslutar anslutningen till klustret eller avslutas, kan tjänsten som du omdirigerar inte återställas till det ursprungliga läget innan du anslöt till den lokala processen med Kubernetes. Information om hur du löser det här problemet finns i [fel söknings guiden][troubleshooting].

## <a name="using-logging-and-diagnostics"></a>Använda loggning och diagnostik

Loggning av utdata skrivs till fönstret *dev Spaces* när din utvecklings dator är ansluten till ditt Kubernetes-kluster.

![Utdata](../media/local-process-kubernetes-vs-code/output.png)

Klicka på status fältet i Azure dev Spaces och välj *Visa information om anslutnings diagnos*. Det här kommandot skriver ut aktuella miljövariabler och DNS-hela i loggnings resultatet.

![Utdata med diagnostik](../media/local-process-kubernetes-vs-code/output-diagnostics.png)

Du kan också hitta diagnostikloggar i `Azure Dev Spaces` katalogen i [utvecklings datorns *Temp* -katalog][azds-tmp-dir].

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
[server-js-breakpoint]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L233
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[troubleshooting]: ../troubleshooting.md#fail-to-restore-original-configuration-of-deployment-on-cluster
[vs-code]: https://code.visualstudio.com/download