---
title: " Ansluta din utvecklings dator till ett AKS-kluster"
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 11/04/2019
ms.topic: conceptual
description: Lär dig hur du ansluter din utvecklings dator till ett AKS-kluster med Azure dev Spaces
keywords: Azure dev Spaces, dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes service, containers
ms.openlocfilehash: 15e46fba19d92e08a9c87a63f6f01ec6b0836910
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888914"
---
# <a name="connect-your-development-machine-to-an-aks-cluster-preview"></a>Ansluta din utvecklings dator till ett AKS-kluster (för hands version)

Med Azure dev Spaces kan du köra och felsöka kod med eller utan en behållare på din utvecklings dator, medan du fortfarande är ansluten till ditt Kubernetes-kluster med resten av dina program eller tjänster. Genom att ansluta din utvecklings dator till klustret kan du snabbt utveckla ditt program och utföra testning från slut punkt till slut punkt utan att behöva skapa någon Docker-eller Kubernetes-konfiguration. Du kan också ansluta till ditt AKS-kluster utan att påverka andra arbets belastningar eller användare som kan använda samma kluster.

Azure dev Spaces omdirigerar trafik mellan ditt anslutna AKS-kluster och din utvecklings dator. Den här fördirigeringen av trafiken tillåter kod på din utvecklings dator och tjänster som körs i ditt AKS-kluster att kommunicera som om de befinner sig i samma AKS-kluster. Eftersom din kod körs på din utvecklings dator har du också flexibilitet i utvecklingsverktyg som du använder för att köra och felsöka koden. Azure dev Spaces är också ett sätt att replikera miljövariabler och monterade filer som är tillgängliga för poddar i ditt AKS-kluster i din utvecklings dator.

I den här guiden får du lära dig hur du:

* Konfigurera Azure dev Spaces i ett hanterat Kubernetes-kluster i Azure.
* Distribuera ett stort program med flera mikrotjänster till ett dev-utrymme.
* Använd Azure dev Spaces för att dirigera om trafik mellan ditt AKS-kluster och kod som körs på din utvecklings dator.

> [!IMPORTANT]
> Den här funktionen är för närvarande en förhandsversion. Förhandsversioner görs tillgängliga för dig under förutsättning att du godkänner [kompletterande användningsvillkor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).

## <a name="before-you-begin"></a>Innan du börjar

I den här guiden används [cykel delnings exempel programmet i Azure dev Spaces](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) för att demonstrera anslutning av din utvecklings dator till ett AKS-kluster. Följ anvisningarna i README- [exemplet för cykel delning i Azure dev Spaces](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/README.md) för att köra exempel programmet. Om du har ett eget program i ett AKS-kluster kan du fortfarande följa stegen nedan och använda namnen på dina egna tjänster och poddar.

### <a name="limitations"></a>Begränsningar

* UDP stöds inte för tillfället.

### <a name="prerequisites"></a>Nödvändiga komponenter

* En Azure-prenumeration. Om du inte har en Azure-prenumeration, kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free).
* [Azure CLI installerat][azure-cli].
* [Visual Studio Code][vs-code] med tillägget [Azure dev Spaces][azds-vs-code] installerat och körs på MacOS eller Windows 10.
* [Exempel programmet för cykel delning i Azure dev Spaces](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) eller ditt eget program som körs på ett AKS-kluster.

## <a name="connect-your-development-machine"></a>Anslut din utvecklings dator

Öppna *dev-Spaces/samples/BikeSharingApp/Bikes* i Visual Studio Code och Använd tillägget Azure dev Spaces för att ansluta din utvecklings dator till ditt AKS-kluster.

Om du vill använda tillägget Azure dev Spaces, öppnar du kommando-paletten i Visual Studio Code genom att klicka på *Visa* och sedan på *kommando-palett*. Börja skriva `Azure Dev Spaces: Redirect` och klicka på antingen `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`, `Azure Dev Spaces: Redirect an existing Kubernetes pod to my machine [Preview]`eller `Azure Dev Spaces: Redirect a new Kubernetes pod to my machine [Preview]`.

![Kommandon](../media/how-to-connect/connect-commands.png)

### <a name="select-a-redirection-option"></a>Välj ett alternativ för omdirigering

Om du kör `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`uppmanas du att välja en befintlig Kubernetes-tjänst:

![Välj tjänst](../media/how-to-connect/connect-choose-service.png)

Det här alternativet dirigerar om all trafik i AKS-klustret för den här tjänsten till den version av programmet som körs i utvecklings datorn. Om den här tjänsten har flera poddar som körs i AKS-klustret dirigeras all trafik för den här tjänsten endast till din utvecklings dator. Azure dev Spaces dirigerar också all utgående trafik från programmet tillbaka till ditt AKS-kluster.

Om du kör `Azure Dev Spaces: Redirect an existing Kubernetes pod to my machine [Preview]`uppmanas du att välja en speciell pod:

![Välj Pod](../media/how-to-connect/connect-choose-pod.png)

Det här alternativet ansluter till en angiven pod. Det här alternativet är användbart för att interagera med poddar som inte skickar eller tar emot trafik och replikering av avslutade poddar. Om Pod skickar och tar emot trafik, fungerar det här alternativet på ett liknande sätt som `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]` och kommer att omdirigera all trafik i AKS-klustret för alla poddar som är relaterade till tjänsten för den valda pod.

Om du kör `Azure Dev Spaces: Redirect a new Kubernetes pod to my machine [Preview]`uppmanas du inte att välja en befintlig POD eller tjänst. Det här alternativet dirigerar om all utgående trafik från programmet som körs på utvecklings datorn till AKS-klustret.

I det här exemplet väljer du `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]` och väljer tjänsten *cyklar* .

### <a name="select-a-connection-mode"></a>Välj ett anslutnings läge

När du har valt alternativet omdirigering uppmanas du att välja antingen alternativet *Ersätt* eller *klona* anslutnings läge.

![Ersätt eller klona](../media/how-to-connect/connect-replace-clone.png)

Alternativet *Ersätt* ersätter nuvarande POD eller tjänst i AKS-klustret och dirigerar om all trafik för tjänsten till utvecklings datorn. Det här alternativet kan vara störande för andra tjänster i ditt AKS-kluster som interagerar med tjänsten som du omdirigerar kanske inte fungerar förrän du startar programmet på utvecklings datorn. Med alternativet *klona* kan du välja ett befintligt underordnat dev Space eller skapa ett nytt underordnat dev utrymme för omdirigering av trafik för en POD eller tjänst till din utvecklings dator. Med det här alternativet kan du arbeta i isolering och inte störa andra tjänster eftersom endast trafik till det underordnade dev-utrymmet kommer att omdirigeras till din utvecklings dator. *Klonings* alternativet kräver att ditt AKS-kluster har aktiverat Azure dev Spaces.

I det här exemplet väljer du *Ersätt*.

> [!NOTE]
> Om din befintliga tjänsts Pod har flera behållare, uppmanas du också att välja programmets behållare.

### <a name="select-a-port-for-your-application"></a>Välj en port för ditt program

När du har valt anslutnings läge uppmanas du att ange den TCP-port som det lokala programmet finns i. Om programmet öppnar flera portar avgränsar du dem med ett kommatecken till exempel *80, 81*. Om programmet inte accepterar några nätverks begär Anden anger du *0*. I det här exemplet anger du *3000*.

![Anslut Välj port](../media/how-to-connect/connect-choose-port.png)

### <a name="confirm-you-are-connected"></a>Bekräfta att du är ansluten

När du har valt appens TCP-port upprättar Azure dev-utrymmen en anslutning till AKS-klustret. Azure dev Spaces infogar en agent i AKS-klustret för att omdirigera trafik mellan AKS-klustret och din utvecklings dator. Det kan ta några minuter att upprätta anslutningen. Azure dev Spaces kommer också att begära administratörs åtkomst för att ändra *värd* filen på din utvecklings dator.

> [!IMPORTANT]
> När Azure dev Spaces upprättar en anslutning till ditt AKS-kluster, fungerar inte de andra tjänsterna i ditt AKS-kluster förrän du startar tjänsten på utvecklings datorn om du väljer *Ersätt* anslutnings läge. Du kan välja *klonings* anslutnings läge i stället för att skapa ett underordnat dev utrymme för omdirigeringen och undvika eventuella avbrott i det överordnade utrymmet. Om din tjänst har ett beroende som inte är tillgängligt på utvecklings datorn kan du behöva ändra ditt program eller ange [ytterligare konfiguration](#additional-configuration)

Azure dev Spaces öppnar ett terminalfönster med titeln *AZDS Connect-Bikes* när den upprättar en anslutning till ditt AKS-kluster. Det här terminalfönstret innehåller alla miljövariabler och DNS-poster som kon figurer ATS från ditt AKS-kluster. All kod som du kör i det här terminalfönstret eller med Visual Studio Code debugger är ansluten till AKS-klustret.

![Slutliga](../media/how-to-connect/connect-terminal.png)

Dessutom skapar Azure dev Spaces ett fönster med rubriken *dev Spaces Connect* med alla utdata.

![Resultat](../media/how-to-connect/connect-output.png)

Azure dev Spaces har också ett Statusfälts objekt som visar anslutnings status.

![Status](../media/how-to-connect/connect-status.png)

Kontrol lera att statusfältet visar *dev Spaces: anslutna till dev/Bikes på den lokala porten 3000*.

### <a name="configure-your-application-on-your-development-machine"></a>Konfigurera ditt program på utvecklings datorn

Öppna terminalfönstret *AZDS Connect-Bikes* och kör `npm install`:

```console
$ npm install

> fsevents@1.2.9 install ./dev-spaces/samples/BikeSharingApp/Bikes/node_modules/fsevents
> node install
...
```


Klicka på *Felsök* och sedan på *Öppna konfigurationer*. Om du uppmanas att välja en miljö väljer du *Node. js*. Detta skapar en `.vscode/launch.json`-fil. Ersätt innehållet i filen med följande:

```json
{
    "configurations": [
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

Ditt program startas och Azure dev Spaces dirigerar om trafiken mellan ditt AKS-kluster och din utvecklings dator. Meddelanden som liknar nedanstående visas i *fel söknings konsolen*:

```console
/usr/local/bin/npm run-script debug 
...
Debugger attached.
Collection: bikes
MongoDB connection string: mongodb://databases-mongo
Connected to MongoDB
Listening on port 3000
```

Gå till *bikesharingweb* -tjänsten genom att klicka på statusfältet i Azure dev Spaces och välja den offentliga URL: en för ditt program. Du kan också hitta den offentliga URL: en från `azds list-uris` kommandot som du körde tidigare. Om du inte använder Azure dev Spaces i klustret använder du IP-adressen eller URL: en för programmet för det namn område som du använder. I exemplet ovan är den offentliga URL: en för *bikesharingweb* -tjänsten `http://dev.bikesharingweb.fedcab0987.eus.azds.io/`. Välj *Aurelia Briggs (kund)* som användare och välj sedan en cykel att hyra.

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

Klicka på *Felsök* och *stoppa* fel sökningen för att stoppa fel söknings programmet. Klicka på statusfältet för Azure dev Spaces om du vill koppla bort från AKS-klustret.

## <a name="additional-configuration"></a>Ytterligare konfiguration

Azure dev Spaces kan hantera routnings trafik och replikera miljövariabler utan ytterligare konfiguration. Om du behöver hämta filer som är monterade till behållaren i ditt AKS-kluster, till exempel en ConfigMap-fil, kan du skapa en `azds-local.env` för att ladda ned filerna till din utvecklings dator.

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