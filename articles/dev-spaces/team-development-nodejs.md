---
title: Teamutveckling med Node.js och Visual Studio Code
services: azure-dev-spaces
ms.date: 07/09/2018
ms.topic: tutorial
description: Den här självstudien visar hur du använder Azure Dev Spaces och Visual Studio Code för att göra grupputveckling på ett Node.js-program i Azure Kubernetes Service
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, behållare, Helm, servicenät, routning av tjänstnät, kubectl, k8s '
ms.openlocfilehash: abcf4934af056d508ac136f80758597294d40b1a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "78251935"
---
# <a name="team-development-using-nodejs-and-visual-studio-code-with-azure-dev-spaces"></a>Teamutveckling med Node.js och Visual Studio Code med Azure Dev Spaces

I den här självstudien lär du dig hur ett team med utvecklare kan samarbeta samtidigt i samma Kubernetes-kluster med hjälp av Dev Spaces.

## <a name="learn-about-team-development"></a>Lär dig mer om utveckling i team
Hittills har du kört programmets kod som om du var den enda utvecklaren som arbetade med appen. I det här avsnittet lär du dig hur Azure Dev Spaces förenklar utvecklingen i ett team:
* Göra det möjligt för ett utvecklarteam att arbeta i samma miljö, genom att arbeta i en delad utvecklingsmiljö eller i särskilda utvecklingsmiljöer efter behov.
* Varje utvecklare kan arbeta med sin egen kod utan att oroa sig för att skada andras kod.
* Testa koden från slutpunkt till slutpunkt, innan koden checkas in, utan att skapa simulerade objekt eller beroendesimuleringar.

### <a name="challenges-with-developing-microservices"></a>Utmaningar vid utveckling av mikrotjänster
Exempelprogrammet är inte särskilt komplext för tillfället. Men i en riktig utvecklingsprocess börjar utmaningarna snart att hopa sig allt eftersom du lägger till fler tjänster och utvecklingsteamet växer. I detta fall kan det vara orealistiskt att köra allt lokalt.

* Utvecklingsdatorn har kanske inte tillräckligt med resurser för att köra varje tjänst som du behöver på samma gång.
* Vissa tjänster kan behöva vara offentligt nåbara. Till exempel kan en tjänst behöva ha en slutpunkt som svarar på en webhook.
* Om du vill köra en delmängd av tjänster behöver du känna till den fullständiga beroendehierarkin mellan alla dina tjänster. Det kan vara svårt att fastställa det här, särskilt när det antal tjänster som du använder ökar.
* Vissa utvecklare försöker kringgå problemet genom att simulera eller testa många av tjänstberoendena. Den här metoden kan vara till hjälp, men att hantera dessa simuleringar kan snabbt börja påverka utvecklingskostnaden. Dessutom gör den metoden att din utvecklingsmiljö ser väldigt annorlunda ut jämfört med produktionen, vilket gör att svårupptäckta buggar kan uppstå.
* Av detta följer att all slags integreringstestning blir komplicerad. Realistiskt sett kan integrationstestning endast ske när koden checkats in, vilket kan medföra problem senare i utvecklingscykeln.

    ![](media/common/microservices-challenges.png)

### <a name="work-in-a-shared-dev-space"></a>Arbeta i en delad utvecklarmiljö
Med Azure Dev Spaces kan du konfigurera en *delad* utvecklarmiljö i Azure. Varje utvecklare kan fokusera på sin del av programmet, och kan arbeta med *icke incheckad kod* i en utvecklarmiljö som redan innehåller alla andra tjänster och molnresurser som krävs i det aktuella scenariot. Beroenden är alltid uppdaterade och utvecklarna arbetar på ett sätt som återspeglar produktionsmiljön.

### <a name="work-in-your-own-space"></a>Arbeta i ditt eget utrymme
När du utvecklar kod för en tjänst är koden sällan perfekt förrän du väljer att checka in den. Du arbetar ju fortfarande med den, testar den och experimenterar med lösningar. Azure Dev Spaces införlivar begreppet **utrymme**, som innebär att du kan arbeta isolerat, utan att oroa dig för att skada andra teammedlemmars kod.

## <a name="use-dev-spaces-for-team-development"></a>Använda Dev Spaces för teamutveckling
Låt oss visa dessa idéer med ett konkret exempel med hjälp av vår *webfrontend* -> *mywebapi* prov ansökan. Vi tänker oss ett scenario där utvecklaren Scott behöver göra en ändring i tjänsten *mywebapi*, och *enbart* den tjänsten. *webfrontend* behöver inte ändras i Scotts uppdatering.

_Utan_ användning av Dev Spaces skulle Scott vara begränsad till vissa sätt att utveckla och testa sina uppdatering. Inget av dessa lämpar sig särskilt väl:
* Köra ALLA komponenter lokalt. Det här kräver en kraftfullare utvecklingsdator med Docker samt potentiellt MiniKube installerat.
* Köra ALLA komponenter i en isolerad namnrymd på Kubernetes-klustret. Eftersom *webfrontend* inte ändras utgör det här ett slöseri med klusterresurser.
* Köra ENDAST *mywebapi* och göra manuell REST-anrop för att testa. Det här testar inte flödet fullständigt från slutpunkt till slutpunkt.
* Lägga till utvecklingsfokuserad kod i *webfrontend* som gör det möjligt för utvecklaren att skicka begäranden till en annan instans av *mywebapi*. Det här gör tjänsten *webfrontend* mer komplicerad.

### <a name="set-up-your-baseline"></a>Konfigurera din baslinje
Först behöver vi distribuera en baslinje för våra tjänster. Den här distributionen representerar den ”senaste kända välfungerande” så att du enkelt kan jämföra beteendet för din lokala kod jämfört med den incheckade versionen. Sedan skapar vi ett underordnat utrymme baserat på den här baslinjen så att vi kan testa ändringarna i *mywebapi* inom ramen för det större programmet.

1. Klona [exempelprogrammet Dev Spaces:](https://github.com/Azure/dev-spaces)`git clone https://github.com/Azure/dev-spaces && cd dev-spaces`
1. Kassa fjärrgrenen *azds_updates:*`git checkout -b azds_updates origin/azds_updates`
1. Välj utrymmet _dev_: `azds space select --name dev`. När du uppmanas att välja _ \<ett\>_ överordnat utvecklingsutrymme väljer du ingen .
1. Navigera till katalogen _mywebapi_ och kör: `azds up -d`
1. Navigera till katalogen _webfrontend_ och kör: `azds up -d`
1. Kör `azds list-uris` för att se den offentliga slutpunkten för _webfrontend_

> [!TIP]
> Stegen ovan konfigurerar en baslinje manuellt, men vi rekommenderar att team använder CI/CD för att automatiskt hålla baslinjen uppdaterad med incheckad kod.
>
> Läs vår [guide om hur du konfigurerar CI/CD med Azure DevOps](how-to/setup-cicd.md) om du vill skapa ett arbetsflöde som liknar följande diagram.
>
> ![CI/CD-exempeldiagram](media/common/ci-cd-complex.png)

I det här läget bör din baslinje köras. Kör kommandot `azds list-up --all` så ser du ett resultat som liknar följande:

```
$ azds list-up --all

Name                          DevSpace  Type     Updated  Status
----------------------------  --------  -------  -------  -------
mywebapi                      dev       Service  3m ago   Running
mywebapi-56c8f45d9-zs4mw      dev       Pod      3m ago   Running
webfrontend                   dev       Service  1m ago   Running
webfrontend-6b6ddbb98f-fgvnc  dev       Pod      1m ago   Running
```

DevSpace-kolumnen visar att båda tjänsterna körs i ett utrymme som heter _dev_. Om någon öppnar den offentliga URL:en och navigerar till webbappen anropas den incheckade kodsökväg som körs via båda tjänsterna. Anta nu att du vill fortsätta utveckla _mywebapi_. Hur kan du göra kodändringar och testa dem utan att störa andra utvecklare som använder utvecklarmiljön? Det gör du genom att konfigurera ett eget utrymme.

### <a name="create-a-dev-space"></a>Skapa en utvecklarmiljö
Om du vill köra din egen version av _mywebapi_ i ett annat utrymme än _dev_ kan du skapa ett eget utrymme med hjälp av följande kommando:

```cmd
azds space select --name scott
```

När du uppmanas väljer du _dev_ som **parent dev space** (överordnad utvecklingsmiljö). Det innebär att det nya utrymmet, _dev/scott_, härleds från utrymmet _dev_. Vi kommer snart att se hur det hjälper oss med testning.

I linje med vårt inledande antagande har vi har använt namnet _scott_ för det nya utrymmet så att andra kan identifiera vem som arbetar i den. Men det kan kallas vad som helst och är flexibelt vad gäller innebörden, till exempel _sprint4_ eller _demo_. Oavsett vilket så fungerar _dev_ som baslinje för alla utvecklare som arbetar med en del av det här programmet:

![](media/common/ci-cd-space-setup.png)

Kör kommandot `azds space list` för att se en lista över alla utrymmen i utvecklarmiljön. Den _valda_ kolumnen anger vilket utrymme du för närvarande har valt (sant/falskt). I ditt fall valdes det utrymme som heter _dev/scott_ automatiskt när det skapades. Du kan välja ett annat utrymme när som helst med kommandot `azds space select`.

Nu ska vi se hur det fungerar i praktiken.

### <a name="make-a-code-change"></a>Göra en kodändring
Gå till VS-kodfönstret och `mywebapi` gör en kodredigering till standard GET-hanteraren `/` i `server.js`, till exempel:

```javascript
app.get('/', function (req, res) {
    res.send('mywebapi now says something new');
});
```

### <a name="run-the-service"></a>Köra tjänsten

Om du vill köra tjänsten trycker `azds up` du på F5 (eller skriver i terminalfönstret) för att köra tjänsten. Tjänsten körs automatiskt i det valda utrymmet _dev/scott_. Bekräfta att tjänsten körs i sitt `azds list-up`eget utrymme genom att köra:

```cmd
$ azds list-up

Name                      DevSpace  Type     Updated  Status
mywebapi                  scott     Service  3m ago   Running
webfrontend               dev       Service  26m ago  Running
```

Lägg märke till att en instans av *mywebapi* nu körs i _dev/scott-utrymmet._ Den version som körs i _dev_ körs fortfarande men det visas inte.

Lista url:erna för det `azds list-uris`aktuella utrymmet genom att köra .

```cmd
$ azds list-uris

Uri                                                                        Status
-------------------------------------------------------------------------  ---------
http://localhost:53831 => mywebapi.scott:80                                Tunneled
http://scott.s.dev.webfrontend.6364744826e042319629.ce.azds.io/  Available
```

Observera att webbadressen till den offentliga åtkomstpunkten för *webbfrontend* är föreskriven med *scott.s*. Den här webbadressen är unik för utrymmet _dev/scott_. Det här URL-prefixet talar om för ingress-styrenheten att dirigera begäranden till _dev/scott-versionen_ av en tjänst. När en begäran med den här URL:en hanteras av Dev Spaces försöker ingress controller först dirigera begäran till *webbfrontend-tjänsten* i _dev/scott-utrymmet._ Om det misslyckas dirigeras begäran till *webfrontend-tjänsten* i _dev-utrymmet_ som en reserv. Också märker att det finns en localhost URL för att komma åt tjänsten via localhost med kubernetes *port-forward* funktionalitet. Mer information om webbadresser och routning i Azure Dev Spaces finns i [Hur Azure Dev Spaces fungerar och är konfigurerat](how-dev-spaces-works.md).

![Utrymme Routning](media/common/Space-Routing.png)

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

Nu när du har utforskat Azure Dev Spaces [delar du ditt utvecklingsutrymme med en gruppmedlem](how-to/share-dev-spaces.md) och börjar samarbeta.

## <a name="clean-up"></a>Rensa
Om du vill ta bort en Azure Dev Spaces-instans i ett kluster fullständigt, inklusive alla utvecklarmiljöer och tjänster som körs i den, använder du kommandot `az aks remove-dev-spaces`. Kom ihåg att den här åtgärden inte kan ångras. Du kan lägga till stöd för Azure Dev Spaces på klustret igen, men det blir som om du börjar om på nytt. Dina gamla tjänster och utrymmen kommer inte att återställas.

Följande exempel visar en lista över Azure Dev Spaces-kontrollanter i din aktiva prenumeration och sedan tar det bort den Azure Dev Spaces-kontrollant som är associerad med AKS-klustret ”myaks” i resursgruppen ”myaks-rg”.

```cmd
azds controller list
```

```azurecli
az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```
