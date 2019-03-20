---
title: ta med fil
description: ta med fil
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
author: DrEsteban
ms.author: stevenry
ms.date: 12/17/2018
ms.topic: include
manager: yuvalm
ms.openlocfilehash: 5d66dcaccc6ca2e40fbd516f535ec56c1baf6b17
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/01/2019
ms.locfileid: "57195618"
---
### <a name="run-the-service"></a>Köra tjänsten

1. Tryck på F5 (eller ange `azds up` i terminalfönstret) om du vill köra tjänsten. Tjänsten körs automatiskt i det valda utrymmet _dev/scott_. 
1. Du kan bekräfta att tjänsten körs i sitt eget utrymme genom att köra `azds list-up` igen. Du ser nu att en instans av *mywebapi* nu körs i utrymmet _dev/scott_ (versionen som körs i _dev_ körs fortfarande men visas inte).

    ```
    Name                      DevSpace  Type     Updated  Status
    mywebapi                  scott     Service  3m ago   Running
    mywebapi-bb4f4ddd8-sbfcs  scott     Pod      3m ago   Running
    webfrontend               dev       Service  26m ago  Running
    ```

1. Kör `azds list-uris` och notera åtkomstpunktsadressen för *webfrontend*.

    ```
    Uri                                                                        Status
    -------------------------------------------------------------------------  ---------
    http://localhost:53831 => mywebapi.scott:80                                Tunneled
    http://scott.s.dev.webfrontend.6364744826e042319629.ce.azds.io/  Available
    ```

1. Använd en URL med prefixet *scott.s* för att navigera till ditt program. Observera att den uppdaterade webbadressen fortfarande kan tolkas. Den här webbadressen är unik för utrymmet _dev/scott_. Den särskilda webbadressen innebär att förfrågningar som skickas till ”scott-webbadressen” först skickas till tjänster i _dev/scott_-utrymmet. Om det misslyckas återgår de till tjänster i _dev_-utrymmet.

<!--
TODO: replace 2 & 3 with below once bug#753164 and PR#158827 get pushed to production.

You can confirm that your service is running in its own space by running `azds list-up` again. First, you'll notice an instance of *mywebapi* is now running in the _dev/scott_ space (the version running in _dev_ is still running but it is not listed). If you run `azds list-uris`, you will notice that the access point URL for *webfrontend* is prefixed with the text "scott.s.". This URL is unique to the _dev/scott_ space. The special URL signifies that requests sent to the "Scott URL" will try to first route to services in the _dev/scott_ space, but if that fails, they will fall back to services in the _dev_ space.

```
Name                      DevSpace  Type     Updated  Status
mywebapi                  scott     Service  3m ago   Running
mywebapi-bb4f4ddd8-sbfcs  scott     Pod      3m ago   Running
webfrontend               dev       Service  26m ago  Running
```

```
Uri                                                                        Status
-------------------------------------------------------------------------  ---------
http://localhost:53831 => mywebapi.scott:80                                Tunneled
http://scott.s.dev.webfrontend.6364744826e042319629.ce.azds.io/  Available
```
-->

![](../articles/dev-spaces/media/common/space-routing.png)

Med den här inbyggda Azure Dev Spaces-funktionen kan du testa kod i ett delat utrymme utan att varje utvecklare måste återskapa hela tjänstestacken i deras respektive utrymme. Den här typen av routning kräver att appkoden vidarebefordrar spridningshuvuden, vilket visas i föregående steg i den här guiden.

### <a name="test-code-in-a-space"></a>Testa koden i ett utrymme
Du testar den nya versionen av *mywebapi* tillsammans med *webfrontend* genom att öppna webbläsaren på URL:en för offentlig åtkomst för *webfrontend* och gå till sidan Om. Nu bör ditt nya meddelande visas.

Ta nu bort delen ”scott.s.” från webbadressen och uppdatera webbläsaren. Nu bör du se det gamla beteendet (med *mywebapi*-versionen som körs i _dev_).

När du har ett _dev_-utrymme som alltid innehåller dina senaste ändringar, och under förutsättning att ditt program är utformat för att dra nytta av DevSpace utrymmesbaserade routning enligt beskrivningen i den här delen av självstudien, kan du förhoppningsvis se hur mycket enklare det blir med Dev Spaces att testa nya funktioner inom ramen för det större programmet. I stället för att behöva distribuera _alla_ tjänster till ditt privata utrymme kan du skapa ett privat utrymme som härleds från _dev_ och endast ”införa” de tjänster du faktiskt arbetar med. Dev Spaces-routningsinfrastrukturen hanterar resten genom att utnyttja så många tjänster utanför ditt privata adressutrymme som går att hitta, samtidigt som det återgår till den senaste versionen som körs i utrymmet _dev_. Ännu bättre är att _flera_ utvecklare aktivt kan utveckla olika tjänster samtidigt i sina egna utrymmen utan att störa varandra.

### <a name="well-done"></a>Bra gjort!
Du har slutfört guiden för att komma igång! Du har lärt dig att:

> [!div class="checklist"]
> * Ställa in Azure Dev Spaces med ett hanterat Kubernetes-kluster i Azure.
> * Iterativt utveckla kod i containrar.
> * Oberoende utvecklar två separata tjänster och använder Kubernetes DNS-tjänstidentifiering för att anropa en annan tjänst.
> * Effektivt utvecklar och testar din kod i en teammiljö.
> * Upprätta en baslinje med funktioner med hjälp av Dev Spaces för att enkelt testa isolerade ändringar inom ramen för ett större mikrotjänstprogram

Nu när du har utforskat Azure Dev blanksteg, [dela ditt dev-adressutrymme med en gruppmedlem](../articles/dev-spaces/how-to/share-dev-spaces.md) och börja samarbeta.

## <a name="clean-up"></a>Rensa
Om du vill ta bort en Azure Dev Spaces-instans i ett kluster fullständigt, inklusive alla utvecklarmiljöer och tjänster som körs i den, använder du kommandot `az aks remove-dev-spaces`. Kom ihåg att den här åtgärden inte kan ångras. Du kan lägga till stöd för Azure Dev Spaces på klustret igen, men det blir som om du börjar om på nytt. Dina gamla tjänster och utrymmen kommer inte att återställas.

Följande exempel visar en lista över Azure Dev Spaces-kontrollanter i din aktiva prenumeration och sedan tar det bort den Azure Dev Spaces-kontrollant som är associerad med AKS-klustret ”myaks” i resursgruppen ”myaks-rg”.

```cmd
    azds controller list
    az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```