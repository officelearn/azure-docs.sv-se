---
title: Ansluta utvecklingsdatorn till ett AKS-kluster (förhandsversion)
services: azure-dev-spaces
ms.date: 11/04/2019
ms.topic: conceptual
description: Lär dig hur du ansluter utvecklingsdatorn till ett AKS-kluster med Azure Dev Spaces
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, behållare
ms.openlocfilehash: 772f221a8047a71902f36fa98ded6c24b5e02d27
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235009"
---
# <a name="connect-your-development-computer-to-an-aks-cluster-preview"></a>Ansluta utvecklingsdatorn till ett AKS-kluster (förhandsversion)

Med Azure Dev Spaces kan du köra och felsöka kod med eller utan en behållare på utvecklingsdatorn, samtidigt som du fortfarande är ansluten till Kubernetes-klustret med resten av programmet eller tjänsterna. Genom att ansluta utvecklingsdatorn till klustret kan du snabbt utveckla ditt program och utföra heltäckande tester utan att behöva skapa någon Docker- eller Kubernetes-konfiguration. Du kan också ansluta till AKS-klustret utan att påverka andra arbetsbelastningar eller användare som kanske använder samma kluster.

Azure Dev Spaces omdirigerar trafik mellan ditt anslutna AKS-kluster och din utvecklingsdator. Med den här omdirigeringen av trafiken kan kod på utvecklingsdatorn och tjänster som körs i AKS-klustret kommunicera som om de finns i samma AKS-kluster. Eftersom koden körs på utvecklingsdatorn har du också flexibilitet i de utvecklingsverktyg du använder för att köra och felsöka koden. Azure Dev Spaces är också ett sätt att replikera miljövariabler och monterade filer som är tillgängliga för poddar i AKS-klustret i utvecklingsdatorn.

I den här guiden får du lära dig hur du:

* Konfigurera Azure Dev Spaces på ett hanterat Kubernetes-kluster i Azure.
* Distribuera ett stort program med flera mikrotjänster till ett utvecklingsutrymme.
* Använd Azure Dev Spaces för att omdirigera trafik mellan AKS-klustret och koden som körs på utvecklingsdatorn.

> [!IMPORTANT]
> Den här funktionen är för närvarande en förhandsversion. Förhandsversioner är tillgängliga för dig under förutsättning att du godkänner de [kompletterande användningsvillkoren](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).

## <a name="before-you-begin"></a>Innan du börjar

Den här guiden använder [exempelprogrammet Azure Dev Spaces Bike Sharing](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) för att demonstrera anslutning av utvecklingsdatorn till ett AKS-kluster. Följ instruktionerna i [exempelprogrammet ReadME](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/README.md) för Azure Dev Spaces Bike Sharing för att köra exempelprogrammet. Alternativt, om du har ett eget program på ett AKS-kluster kan du fortfarande följa stegen nedan och använda namnen på dina egna tjänster och poddar.

### <a name="limitations"></a>Begränsningar

* UDP stöds inte just nu.

### <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free).
* [Azure CLI installerat][azure-cli].
* [Visual Studio-kod][vs-code] med [Azure Dev Spaces-tillägget][azds-vs-code] installerat och körs på MacOS eller Windows 10.
* Exempelprogrammet [för Azure Dev Spaces Bike Sharing](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) eller ditt eget program som körs på ett AKS-kluster.

## <a name="connect-your-development-computer"></a>Anslut din utvecklingsdator

Öppna *dev-spaces/samples/BikeSharingApp/Bikes* i Visual Studio-kod och använd Azure Dev Spaces-tillägget för att ansluta utvecklingsdatorn till AKS-klustret.

Om du vill använda tillägget Azure Dev Spaces öppnar du kommandopaletten i Visual Studio-kod genom att klicka på *Visa* och sedan *kommandopalett*. Börja skriva `Azure Dev Spaces: Redirect` och klicka `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]` `Azure Dev Spaces: Redirect an existing Kubernetes pod to my machine [Preview]`på `Azure Dev Spaces: Redirect a new Kubernetes pod to my machine [Preview]`antingen , eller .

![Kommandon](../media/how-to-connect/connect-commands.png)

### <a name="select-a-redirection-option"></a>Välj ett omdirigeringsalternativ

Om du `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`kör uppmanas du att välja en befintlig Kubernetes-tjänst:

![Välj tjänst](../media/how-to-connect/connect-choose-service.png)

Det här alternativet omdirigerar all trafik i AKS-klustret för den här tjänsten till den version av programmet som körs på utvecklingsdatorn. Om den här tjänsten har flera poddar som körs i AKS-klustret dirigeras all trafik för den här tjänsten endast till utvecklingsdatorn. Azure Dev Spaces dirigerar också all utgående trafik från programmet tillbaka till AKS-klustret.

Om du `Azure Dev Spaces: Redirect an existing Kubernetes pod to my machine [Preview]`kör uppmanas du att välja en viss pod:

![Välj pod](../media/how-to-connect/connect-choose-pod.png)

Det här alternativet ansluter till en viss pod. Det här alternativet är användbart för att interagera med poddar som inte skickar eller tar emot trafik och replikerar avslutade poddar. Om podden skickar och tar emot trafik fungerar det `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]` här alternativet på ett liknande sätt och omdirigerar all trafik i AKS-klustret för alla poddar som är relaterade till tjänsten för den valda podden.

Om du `Azure Dev Spaces: Redirect a new Kubernetes pod to my machine [Preview]`kör uppmanas du inte att välja en befintlig pod eller tjänst. Det här alternativet omdirigerar all utgående trafik från programmet som körs på utvecklingsdatorn till AKS-klustret.

I det här `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]` exemplet väljer du och väljer *cykelservice.*

### <a name="select-a-connection-mode"></a>Välj ett anslutningsläge

När du har valt omdirigeringsalternativet uppmanas du att välja *anslutningsläget Ersätt* eller *Klona.*

![Ersätt eller klona](../media/how-to-connect/connect-replace-clone.png)

Alternativet *Ersätt* ersätter aktuell pod eller tjänst i AKS-klustret och omdirigerar all trafik för den tjänsten till utvecklingsdatorn. Det här alternativet kan vara störande för andra tjänster i AKS-klustret som interagerar med tjänsten du omdirigerar kanske inte fungerar förrän du startar programmet på utvecklingsdatorn. Med alternativet *Klona* kan du välja ett befintligt underordnat utvecklingsutrymme eller skapa ett nytt underordnat utvecklingsutrymme för att omdirigera trafik för en pod eller tjänst till utvecklingsdatorn. Med det här alternativet kan du arbeta isolerat och inte störa andra tjänster eftersom endast trafik till det underordnade utvecklingsutrymmet omdirigeras till utvecklingsdatorn. *Kloningsalternativet* kräver att AKS-klustret har Azure Dev Spaces aktiverat.

I det här exemplet väljer du *Ersätt*.

> [!NOTE]
> Om din befintliga tjänsts pod har flera behållare uppmanas du också att välja programmets behållare.

### <a name="select-a-port-for-your-application"></a>Välj en port för ditt program

När du har valt anslutningsläge uppmanas du att ange TCP-porten till ditt lokala program. Om programmet öppnar flera portar, separera dem med ett kommatecken till exempel *80,81*. Om programmet inte accepterar några nätverksbegäranden anger du *0*. I det här exemplet anger du *3000*.

![Anslut välj port](../media/how-to-connect/connect-choose-port.png)

### <a name="confirm-you-are-connected"></a>Bekräfta att du är ansluten

När du har valt programmets TCP-port upprättar Azure Dev Spaces en anslutning till AKS-klustret. Azure Dev Spaces injicerar en agent i AKS-klustret för att omdirigera trafik mellan AKS-klustret och utvecklingsdatorn. Det kan ta några minuter att upprätta den här anslutningen. Azure Dev Spaces kommer också att begära administratörsåtkomst för att ändra *hosts-filen* på utvecklingsdatorn.

> [!IMPORTANT]
> När Azure Dev Spaces upprättar en anslutning till AKS-klustret kanske de andra tjänsterna i AKS-klustret inte fungerar korrekt förrän du startar tjänsten i utvecklingsdatorn om du väljer *anslutningsläget Ersätt.* Du kan välja *anslutningsläget Klona* i stället för att skapa ett underordnat utvecklingsutrymme för omdirigeringen och undvika störningar i det överordnade utrymmet. Om tjänsten har ett beroende som inte är tillgängligt på utvecklingsdatorn kan du behöva ändra programmet eller tillhandahålla [ytterligare konfiguration](#additional-configuration)

Azure Dev Spaces öppnar ett terminalfönster med namnet *AZDS Connect - Bikes* när det har upprättat en anslutning till AKS-klustret. Det här terminalfönstret har alla miljövariabler och DNS-poster konfigurerade från AKS-klustret. Alla koder som du kör i det här terminalfönstret eller med hjälp av felsökningsprogrammet för Visual Studio-kod är anslutna till AKS-klustret.

![Terminal](../media/how-to-connect/connect-terminal.png)

Dessutom skapar Azure Dev Spaces ett fönster med namnet *Dev Spaces Connect* med alla dess utdata.

![Resultat](../media/how-to-connect/connect-output.png)

Azure Dev Spaces har också ett statusfältsobjekt som visar anslutningsstatus.

![Status](../media/how-to-connect/connect-status.png)

Kontrollera statusfältet visar *Dev Spaces: Ansluten till dev/bikes på lokal port 3000*.

### <a name="configure-your-application-on-your-development-computer"></a>Konfigurera ditt program på utvecklingsdatorn

Öppna *TERMINALFÖNSTRET AZDS Connect - Bikes* och kör: `npm install`

```console
$ npm install

> fsevents@1.2.9 install ./dev-spaces/samples/BikeSharingApp/Bikes/node_modules/fsevents
> node install
...
```

Klicka på *Felsök och* öppna *konfigurationer.* Om du uppmanas att välja en miljö väljer du *Nod.js*. Detta skapar `.vscode/launch.json` en fil. Ersätt innehållet i filen med följande:

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

Öppna [package.json](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/package.json) och lägg till ett felsökningsskript:

```json
  "devDependencies": {
    "nodemon": "^1.18.10"
  },
  "scripts": {
    "debug": "node --nolazy --inspect-brk=9229 server.js"
  }
```

### <a name="start-your-application-on-your-development-computer"></a>Starta programmet på din utvecklingsdator

Klicka på *Debug* ikonen till vänster och klicka på startknappen *bredvid Starta via NPM* högst upp.

![Starta via NPM](../media/how-to-connect/launch-npm.png)

Ditt program startar och Azure Dev Spaces omdirigerar trafik mellan AKS-klustret och utvecklingsdatorn. Du kommer att se meddelanden som liknar nedan i *Felsökningskonsolen:*

```console
/usr/local/bin/npm run-script debug 
...
Debugger attached.
Collection: bikes
MongoDB connection string: mongodb://databases-mongo
Connected to MongoDB
Listening on port 3000
```

Navigera till *bikesharingweb-tjänsten* genom att klicka på statusfältet Azure Dev Spaces och välja den offentliga URL:en för ditt program. Du kan också hitta den `azds list-uris` offentliga webbadressen från kommandot du körde tidigare. Om du inte använder Azure Dev Spaces i klustret använder du IP-adressen eller URL:en för programmet för det namnområde som du använder. I exemplet ovan är `http://dev.bikesharingweb.fedcab0987.eus.azds.io/`den offentliga URL:en för *bikesharingweb-tjänsten* . Välj *Aurelia Briggs (kund)* som användare och välj sedan en cykel att hyra.

### <a name="set-a-break-point"></a>Ange en brytpunkt

Öppna [server.js](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L233) och klicka någonstans på linje 233 för att sätta markören där. Ange en brytpunkt genom att trycka på *F9* eller klicka på *Felsök* sedan *Växla brytpunkt*.

Navigera till *bikesharingweb-tjänsten* genom att öppna den offentliga webbadressen. Välj *Aurelia Briggs (kund)* som användare och välj sedan en cykel att hyra. Lägg märke till att bilden för cykeln inte laddas. Återgå till Visual Studio-kod och observera linje 233 är markerad. Brytpunkten som du anger har pausat tjänsten på linje 233. Om du vill återuppta tjänsten trycker du på *F5* eller klickar på *Felsök* sedan *Fortsätt*. Gå tillbaka till din webbläsare och kontrollera att du ser en platshållarbild för cykeln.

Ta bort brytpunkten genom att sätta markören `server.js` på linje 233 i och slå *F9*.

### <a name="update-your-application"></a>Uppdatera ditt program

Redigera `server.js` om du vill ta bort raderna 232 och 233:

```javascript
    // Hard code image url *FIX ME*
    theBike.imageUrl = "/static/logo.svg";
```

Avsnittet ska nu se ut så här:

```javascript
    var theBike = result;
    theBike.id = theBike._id;
    delete theBike._id;
```

Spara ändringarna och klicka sedan på *Felsök* sedan *Starta om felsökning*. Uppdatera webbläsaren och kontrollera att du inte längre ser en platshållarbild för cykeln.

Klicka på *Felsöka* och stoppa *felsökning* för att stoppa felsökningen. Klicka på statusfältet Azure Dev Spaces om du vill koppla från AKS-klustret.

## <a name="additional-configuration"></a>Ytterligare konfiguration

Azure Dev Spaces kan hantera routningstrafik och replikera miljövariabler utan ytterligare konfiguration. Om du behöver hämta filer som är monterade på behållaren i AKS-klustret, till `azds-local.env` exempel en ConfigMap-fil, kan du skapa en för att hämta filerna till utvecklingsdatorn.

Här är `azds-local.env`ett exempel:

```
# This downloads the "whitelist" volume from the container,
# saves it to a temporary directory on your development computer,
# and sets the full path to an environment variable called WHITELIST_PATH.

WHITELIST_PATH=${volumes.whitelist}/whitelist

# This downloads a file from the container's 'default-token-<any>' mount directory 
# to /var/run/secrets/kubernetes.io/serviceaccount on your development computer.

KUBERNETES_IN_CLUSTER_CONFIG_OVERWRITE=${volumes.default-token-*|/var/run/secrets/kubernetes.io/serviceaccount}


# This makes the myapp1 service available to your development computer
# regardless of the AKS cluster you are connected to and
# sets the local IP to an environment variable called MYAPP1_SERVICE_HOST.

# If the myapp1 service is made available in this way, 
# you can also access it using "myapp1" and "myapp1.svc.cluster.local"
# in addition to the IP in the MYAPP1_SERVICE_HOST environment variable.

MYAPP1_SERVICE_HOST=${services.myapp1}

# This makes the service myapp2 in namespace mynamespace available to your 
# development computer regardless of the AKS cluster you are connected to and
# sets the local IP to an environment variable called MYAPP2_SERVICE_HOST.

MYAPP2_SERVICE_HOST=${services.mynamespace.myapp2}
```

## <a name="using-logging-and-diagnostics"></a>Använda loggning och diagnostik

Loggningsutdata skrivs till fönstret *Dev Spaces Connect* när du har anslutit utvecklingsdatorn till AKS-klustret.

![Resultat](../media/how-to-connect/connect-output.png)

Klicka på statusfältet för Azure Dev Spaces och välj *Visa diagnostikinformation*. Det här kommandot skriver ut de aktuella miljövariablerna och DNS-helheterna i loggningsutdata.

![Utdata med diagnostik](../media/how-to-connect/connect-output-diagnostics.png)

Dessutom kan du hitta diagnostikloggarna `Azure Dev Spaces` i katalogen i [utvecklingsdatorns *TEMP-katalog* ][azds-tmp-dir].

## <a name="next-steps"></a>Nästa steg

Lär dig hur du använder Azure Dev Spaces och GitHub-åtgärder för att testa ändringar från en pull-begäran direkt i AKS innan pull-begäran slås samman till databasens huvudgren.

> [!div class="nextstepaction"]
> [GitHub-åtgärder & Azure Kubernetes-tjänsten][gh-actions]

[azds-tmp-dir]: ../troubleshooting.md#before-you-begin
[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[bike-sharing-github]: https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp
[gh-actions]: github-actions.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[team-quickstart]: ../quickstart-team-development.md
[vs-code]: https://code.visualstudio.com/download