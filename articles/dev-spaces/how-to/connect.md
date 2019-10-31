---
title: Azure dev-Spaces Connect
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 10/24/2019
ms.topic: conceptual
description: Lär dig hur du använder Azure dev Spaces Connect
keywords: Azure dev Spaces, dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes service, Containers, Connect
ms.openlocfilehash: 2e1984b838e961239e0533179c34eccb118abd8e
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73065878"
---
# <a name="azure-dev-spaces-connect-preview"></a>Azure dev Spaces Connect (för hands version)

Med Azure dev Spaces Connect kan du köra och felsöka kod med eller utan en behållare på din utvecklings dator, medan du fortfarande är ansluten till ditt Kubernetes-kluster med resten av dina program eller tjänster. Genom att ansluta din utvecklings dator till klustret kan du snabbt utveckla ditt program och utföra testning från slut punkt till slut punkt utan att behöva skapa någon Docker-eller Kubernetes-konfiguration. Du kan också ansluta till ditt AKS-kluster utan att påverka andra arbets belastningar eller användare som kan använda samma kluster.

Azure dev Spaces Connect omdirigerar trafik mellan ditt anslutna AKS-kluster och din utvecklings dator. Den här fördirigeringen av trafiken tillåter kod på din utvecklings dator och tjänster som körs i ditt AKS-kluster att kommunicera som om de befinner sig i samma AKS-kluster. Eftersom din kod körs på din utvecklings dator har du också flexibilitet i utvecklingsverktyg som du använder för att köra och felsöka koden. Azure dev Spaces Connect ger också ett sätt att replikera miljövariabler och monterade filer som är tillgängliga för poddar i ditt AKS-kluster i din utvecklings dator.

I den här guiden får du lära dig hur du:

- Konfigurera Azure dev Spaces i ett hanterat Kubernetes-kluster i Azure.
- Distribuera ett stort program med flera mikrotjänster till ett dev-utrymme.
- Använd Azure dev Spaces Connect för att omdirigera trafik mellan ditt AKS-kluster och kod som körs på din utvecklings dator.

> [!IMPORTANT]
> Den här funktionen är för närvarande en förhandsversion. Förhandsversioner görs tillgängliga för dig under förutsättning att du godkänner [kompletterande användningsvillkor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).

## <a name="before-you-begin"></a>Innan du börjar

I den här guiden används [Azure dev Spaces cykel delnings exempel program](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) för att demonstrera Azure dev-platser. Om du har ett eget program på AKS-kluster som du vill använda i stället, kan du börja [här](#use-azure-dev-spaces-connect).

### <a name="limitations"></a>Begränsningar

* UDP stöds inte med Azure dev Spaces Connect just nu.

### <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free).
* [Azure CLI installerat][azure-cli].
* [Helm 2,13 eller senare installerad][helm-installed].
* [Visual Studio Code][vs-code] med tillägget [Azure dev Spaces][azds-vs-code] installerat och körs på MacOS eller Windows 10.

## <a name="create-an-azure-kubernetes-service-cluster"></a>Skapa ett Azure Kubernetes service-kluster

Du måste skapa ett AKS-kluster i en [region som stöds][supported-regions]. Kommandona nedan skapar en resurs grupp med namnet *MyResourceGroup* och ett AKS-kluster som kallas *MyAKS*.

```cmd
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --disable-rbac --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Aktivera Azure dev Spaces i ditt AKS-kluster

Använd kommandot `use-dev-spaces` för att aktivera dev Spaces i ditt AKS-kluster och följa anvisningarna. Kommandot nedan aktiverar dev Spaces i *MyAKS* -klustret i gruppen *MyResourceGroup* och skapar ett dev-utrymme som heter *dev*.

> [!NOTE]
> Kommandot `use-dev-spaces` installerar även Azure dev Spaces CLI om det inte redan är installerat. Det går inte att installera Azure dev Spaces CLI i Azure Cloud Shell.

```cmd
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space dev --yes
```

## <a name="get-sample-application-code"></a>Hämta exempel program kod

I den här artikeln använder du [exempel programmet Azure dev Spaces Bike sharing](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) för att demonstrera användningen av Azure dev Spaces.

Klona programmet från GitHub och gå till programmets katalog:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/BikeSharingApp/
```

## <a name="retrieve-the-hostsuffix-for-dev"></a>Hämta HostSuffix för *utveckling*

Använd kommandot `azds show-context` för att Visa HostSuffix för *dev*.

```cmd
$ azds show-context

Name                ResourceGroup     DevSpace  HostSuffix
------------------  ----------------  --------  -----------------------
MyAKS               MyResourceGroup   dev       fedcab0987.eus.azds.io
```

## <a name="update-the-helm-chart-with-your-hostsuffix"></a>Uppdatera Helm-diagrammet med din HostSuffix

Öppna [Charts/Values. yaml](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml) och Ersätt alla instanser av `<REPLACE_ME_WITH_HOST_SUFFIX>` med HostSuffix-värdet som du hämtade tidigare. Spara ändringarna och Stäng filen.

## <a name="run-the-sample-application-in-kubernetes"></a>Kör exempel programmet i Kubernetes

Kommandona för att köra exempel programmet på Kubernetes är en del av en befintlig process och har inget beroende av Azure dev Spaces-verktyg. I det här fallet är Helm det verktyg som används för att köra det här exempel programmet, men andra verktyg kan användas för att köra hela programmet i ett namn område i ett kluster. Helm-kommandona är riktade mot dev-utrymmet som heter *dev* som du skapade tidigare, men detta dev-utrymme är också ett Kubernetes-namnområde. Detta innebär att dev-rymder kan riktas mot andra verktyg på samma sätt som andra namn områden.

Du kan använda Azure dev Spaces för utveckling när ett program körs i ett kluster oavsett vilka verktyg som används för att distribuera det.

### <a name="use-helm-to-install-the-sample-application"></a>Använda Helm för att installera exempel programmet

Använd kommandona `helm init` och `helm install` för att konfigurera och installera exempel programmet i klustret.

```cmd
cd charts/
helm init --wait
helm install -n bikesharing . --dep-up --namespace dev --atomic 
```

> [!Note]
> **Om du använder ett RBAC-aktiverat kluster måste du**konfigurera [ett tjänst konto för till en](https://helm.sh/docs/using_helm/#role-based-access-control). Annars fungerar inte `helm` kommandon.

Det kan ta flera minuter att slutföra kommandot `helm install`. Kommandots utdata visar status för alla tjänster som distribuerats till klustret när de är slutförda:

```cmd
$ cd charts/
$ helm init --wait
...
Happy Helming!

$ helm install -n bikesharing . --dep-up --namespace dev --atomic

Hang tight while we grab the latest from your chart repositories...
...
NAME               READY  UP-TO-DATE  AVAILABLE  AGE
bikes              1/1    1           1          4m32s
bikesharingweb     1/1    1           1          4m32s
billing            1/1    1           1          4m32s
gateway            1/1    1           1          4m32s
reservation        1/1    1           1          4m32s
reservationengine  1/1    1           1          4m32s
users              1/1    1           1          4m32s
```

### <a name="navigate-to-your-sample-application"></a>Navigera till exempel programmet

När exempel programmet har installerats på klustret och eftersom du har aktiverat dev Spaces i klustret, använder du kommandot `azds list-uris` för att visa URL: erna för exempel programmet i den aktuella *utvecklings* gruppen.

```cmd
$ azds list-uris
Uri                                                 Status
--------------------------------------------------  ---------
http://dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://dev.gateway.fedcab0987.eus.azds.io/         Available
```

Navigera till *bikesharingweb* -tjänsten genom att öppna den offentliga URL: en från kommandot `azds list-uris`. I exemplet ovan är den offentliga URL: en för *bikesharingweb* -tjänsten `http://dev.bikesharingweb.fedcab0987.eus.azds.io/`. Välj *Aurelia Briggs (kund)* som användare och välj sedan en cykel att hyra. Kontrol lera att du ser en platshållarbild för cykeln.

## <a name="use-azure-dev-spaces-connect"></a>Använd Azure dev Spaces Connect

Öppna *dev-Spaces/samples/BikeSharingApp/Bikes* i Visual Studio Code och Använd tillägget Azure dev Spaces Connect för att ansluta din utvecklings dator till ditt AKS-kluster.

Om du vill använda Azure dev Spaces Connect-tillägget öppnar du kommando-paletten i Visual Studio Code genom att klicka på *Visa* och sedan på *kommando-palett*. Börja skriva `Azure Dev Spaces: Redirect` och klicka på antingen `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`, `Azure Dev Spaces: Redirect an existing Kubernetes pod to my machine [Preview]`eller `Azure Dev Spaces: Redirect a new Kubernetes pod to my machine [Preview]`.

![Anslutnings kommandon](../media/how-to-connect/connect-commands.png)

### <a name="select-a-redirection-option"></a>Välj ett alternativ för omdirigering

Om du kör `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`uppmanas du att välja en befintlig Kubernetes-tjänst:

![Anslut Välj tjänst](../media/how-to-connect/connect-choose-service.png)

Det här alternativet dirigerar om all trafik i AKS-klustret för den här tjänsten till den version av programmet som körs i utvecklings datorn. Om den här tjänsten har flera poddar som körs i AKS-klustret dirigeras all trafik för den här tjänsten endast till din utvecklings dator. Azure dev Spaces Connect dirigerar också all utgående trafik från programmet tillbaka till ditt AKS-kluster.

Om du kör `Azure Dev Spaces: Redirect an existing Kubernetes pod to my machine [Preview]`uppmanas du att välja en speciell pod:

![Anslut Välj Pod](../media/how-to-connect/connect-choose-pod.png)

Det här alternativet ansluter till en angiven pod. Det här alternativet är användbart för att interagera med poddar som inte skickar eller tar emot trafik och replikering av avslutade poddar. Om Pod skickar och tar emot trafik, fungerar det här alternativet på ett liknande sätt som `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]` och kommer att omdirigera all trafik i AKS-klustret för alla poddar som är relaterade till tjänsten för den valda pod.

Om du kör `Azure Dev Spaces: Redirect a new Kubernetes pod to my machine [Preview]`uppmanas du inte att välja en befintlig POD eller tjänst. Det här alternativet dirigerar om all utgående trafik från programmet som körs på utvecklings datorn till AKS-klustret.

I det här exemplet väljer du `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]` och väljer tjänsten *cyklar* .

### <a name="select-a-connection-mode"></a>Välj ett anslutnings läge

När du har valt alternativet omdirigering uppmanas du att välja antingen alternativet *Ersätt* eller *klona* anslutnings läge.

![Anslut Ersätt eller klona](../media/how-to-connect/connect-replace-clone.png)

Alternativet *Ersätt* ersätter nuvarande POD eller tjänst i AKS-klustret och dirigerar om all trafik för tjänsten till utvecklings datorn. Det här alternativet kan vara störande för andra tjänster i ditt AKS-kluster som interagerar med tjänsten som du omdirigerar kanske inte fungerar förrän du startar programmet på utvecklings datorn. Med alternativet *klona* kan du välja ett befintligt underordnat dev Space eller skapa ett nytt underordnat dev utrymme för omdirigering av trafik för en POD eller tjänst till din utvecklings dator. Med det här alternativet kan du arbeta i isolering och inte störa andra tjänster eftersom endast trafik till det underordnade dev-utrymmet kommer att omdirigeras till din utvecklings dator. *Klonings* alternativet kräver att ditt AKS-kluster har aktiverat Azure dev Spaces.

I det här exemplet väljer du *Ersätt*.

> [!NOTE]
> Om din befintliga tjänsts Pod har flera behållare, uppmanas du också att välja programmets behållare.

### <a name="select-a-port-for-your-application"></a>Välj en port för ditt program

När du har valt anslutnings läge uppmanas du att ange den TCP-port som det lokala programmet finns i. Om programmet öppnar flera portar avgränsar du dem med ett kommatecken till exempel *80, 81*. Om programmet inte accepterar några nätverks begär Anden anger du *0*. I det här exemplet anger du *8080*.

![Anslut Välj port](../media/how-to-connect/connect-choose-port.png)

### <a name="confirm-you-are-connected"></a>Bekräfta att du är ansluten

När du har valt appens TCP-port upprättar Azure dev Spaces en anslutning till AKS-klustret. Azure dev Spaces-anslutning infogar en agent i AKS-klustret för att dirigera om trafik mellan AKS-klustret och din utvecklings dator. Det kan ta några minuter att upprätta anslutningen. Azure dev Spaces Connect kommer också att be om administratörs åtkomst för att ändra *värd* filen i utvecklings datorn.

> [!IMPORTANT]
> När Azure dev Spaces Connect upprättar en anslutning till ditt AKS-kluster kanske de andra tjänsterna i ditt AKS-kluster inte fungerar korrekt förrän du startar tjänsten på utvecklings datorn. Om din tjänst har ett beroende som inte är tillgängligt på utvecklings datorn kan du behöva ändra ditt program eller ange [ytterligare konfiguration](#additional-configuration)

Azure dev Spaces Connect öppnar ett terminalfönster med titeln *AZDS Connect-Bikes* när den upprättar en anslutning till ditt AKS-kluster. Det här terminalfönstret innehåller alla miljövariabler och DNS-poster som kon figurer ATS från ditt AKS-kluster. All kod som du kör i det här terminalfönstret eller med Visual Studio Code debugger är ansluten till AKS-klustret.

![Anslut Terminal](../media/how-to-connect/connect-terminal.png)

Dessutom skapar Azure dev Spaces Connect ett fönster med rubriken *dev Spaces Connect* med alla utdata.

![Anslut utdata](../media/how-to-connect/connect-output.png)

Azure dev Spaces Connect har också ett Statusfälts objekt som visar anslutnings status.

![Anslutnings status](../media/how-to-connect/connect-status.png)

Kontrol lera att statusfältet visar *dev Spaces: anslutna till dev/Bikes på den lokala porten 8080*.

### <a name="configure-your-application-on-your-development-machine"></a>Konfigurera ditt program på utvecklings datorn

Öppna terminalfönstret *AZDS Connect-Bikes* och kör `npm install`:

```console
$ npm install

> fsevents@1.2.9 install ./dev-spaces/samples/BikeSharingApp/Bikes/node_modules/fsevents
> node install
...
```

Öppna [Server. js](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L352) och på rad 352 uppdaterings *port* till *8080*:

```javascript
var port = 8080;
var server = null;
```

Klicka på *Felsök* och sedan på *Öppna konfigurationer*. Detta skapar en `.vscode/launch.json`-fil. Ersätt innehållet i filen med följande:

```json
{
    "configurations": [
        ...
        {
            "type": "node",
            "request": "launch",
            "name": "Launch via NPM",
            "runtimeExecutable": "npm",
            "runtimeArgs": [
                "run-script",
                "debug"
            ],
            "port": 9229
        }
    ]
}
```

Öppna [Package. JSON](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/package.json) och Lägg till ett skript för fel sökning:

```json
  "devDependencies": {
    "nodemon": "^1.18.10"
  },
  "scripts": {
    "debug": "node --nolazy --inspect-brk=9229 server.js"
  }
```

### <a name="start-your-application-on-your-development-machine"></a>Starta ditt program på utvecklings datorn

Klicka på ikonen *Felsök* till vänster och klicka på Start-knappen bredvid *starta via NPM* överst.

![Starta via NPM](../media/how-to-connect/launch-npm.png)

Ditt program startar och Azure dev Spaces Connect dirigerar om trafiken mellan ditt AKS-kluster och din utvecklings dator. Meddelanden som liknar nedanstående visas i *fel söknings konsolen*:

```console
/usr/local/bin/npm run-script debug 
...
Debugger attached.
Collection: bikes
MongoDB connection string: mongodb://databases-mongo
Connected to MongoDB
Listening on port 8080
```

Navigera till *bikesharingweb* -tjänsten genom att klicka på status fältet för Azure dev Spaces Connect och välja den offentliga URL: en för ditt program. Du kan också hitta den offentliga URL: en från `azds list-uris` kommandot som du körde tidigare. Om du inte använder Azure dev Spaces i klustret använder du IP-adressen eller URL: en för programmet för det namn område som du använder. I exemplet ovan är den offentliga URL: en för *bikesharingweb* -tjänsten `http://dev.bikesharingweb.fedcab0987.eus.azds.io/`. Välj *Aurelia Briggs (kund)* som användare och välj sedan en cykel att hyra.

### <a name="set-a-break-point"></a>Ange en Bryt punkt

Öppna [Server. js](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L233) och klicka någonstans på rad 233 för att placera markören där. Ange en Bryt punkt genom att trycka på *F9* eller klicka på *Felsök* och sedan på *Växla Bryt punkt*.

Navigera till *bikesharingweb* -tjänsten genom att öppna den offentliga URL: en. Välj *Aurelia Briggs (kund)* som användare och välj sedan en cykel att hyra. Observera att bilden för cykeln inte läses in. Gå tillbaka till Visual Studio Code och Observera att rad 233 är markerad. Den Bryt punkten som du har angett har pausat tjänsten på rad 233. Tryck på *F5* eller klicka på *Felsök* och *Fortsätt*om du vill återuppta tjänsten. Gå tillbaka till webbläsaren och kontrol lera att du ser en platshållarbild för cykeln.

Ta bort Bryt punkten genom att placera markören på rad 233 i `server.js` och trycka på *F9*.

### <a name="update-your-application"></a>Uppdatera programmet

Redigera `server.js` för att ta bort raderna 232 och 233:

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

Spara ändringarna och klicka sedan på *Felsök* och *starta sedan om fel sökning*. Uppdatera webbläsaren och kontrol lera att du inte längre ser en platshållarbild för cykeln.

Klicka på *Felsök* och *stoppa* fel sökningen för att stoppa fel söknings programmet. Klicka på status fältet för Azure dev Spaces Connect för att koppla bort från AKS-klustret.

## <a name="additional-configuration"></a>Ytterligare konfiguration

Azure dev Spaces Connect kan hantera replikeringstrafik och replikera miljövariabler utan ytterligare konfiguration. Om du behöver hämta filer som är monterade till behållaren i ditt AKS-kluster, till exempel en ConfigMap-fil, kan du skapa en `azds-local.env` för att ladda ned filerna till din utvecklings dator.

Här är ett exempel `azds-local.env`:

```
# This downloads the "whitelist" volume from the container,
# saves it to a temporary directory on your development machine,
# and sets the full path to an environment variable called WHITELIST_PATH.

WHITELIST_PATH=${volumes.whitelist}/whitelist

# This downloads a file from the container's 'default-token-<any>' mount directory 
# to /var/run/secrets/kubernetes.io/serviceaccount on your development machine.

KUBERNETES_IN_CLUSTER_CONFIG_OVERWRITE=${volumes.default-token-*|/var/run/secrets/kubernetes.io/serviceaccount}


# This makes the myapp1 service available to your development machine
# regardless of the AKS cluster you are connected to and
# sets the local IP to an environment variable called MYAPP1_SERVICE_HOST.

MYAPP1_SERVICE_HOST=${services.myapp1}

# If the myapp1 service is made available in this way, 
# you can also access it using "myapp1" and "myapp1.svc.cluster.local"
# in addition to the IP in the MYAPP1_SERVICE_HOST environment variable.
```

## <a name="next-steps"></a>Nästa steg

Lär dig hur du använder Azure dev Spaces och GitHub-åtgärder för att testa ändringar från en pull-begäran direkt i AKS innan pull-begäran slås samman i huvud grenen för din databas.

> [!div class="nextstepaction"]
> [GitHub åtgärder & Azure Kubernetes service][gh-actions]

[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[bike-sharing-github]: https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp
[gh-actions]: github-actions.md
[helm-installed]: https://helm.sh/docs/using_helm/#installing-helm
[supported-regions]: ../about.md#supported-regions-and-configurations
[team-quickstart]: ../quickstart-team-development.md
[vs-code]: https://code.visualstudio.com/download