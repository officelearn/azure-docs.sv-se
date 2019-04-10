---
title: Grupputveckling Azure Dev blanksteg med .NET Core och Visual Studio
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.custom: vs-azure
ms.workload: azure-vs
author: DrEsteban
ms.author: stevenry
ms.date: 12/09/2018
ms.topic: tutorial
description: Snabb Kubernetes-utveckling med containrar och mikrotjänster i Azure
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, behållare, Helm, tjänsten nät, tjänsten nät routning, kubectl, k8s '
ms.openlocfilehash: 58807aa5a540de6eaa9a337caa0c34fee9408296
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2019
ms.locfileid: "59361446"
---
# <a name="team-development-with-azure-dev-spaces"></a>Teamutveckling med Azure Dev Spaces

I den här självstudien lär du dig hur ett team med utvecklare kan samarbeta samtidigt i samma Kubernetes-kluster med hjälp av Dev Spaces.

## <a name="learn-about-team-development"></a>Lär dig mer om utveckling i team

Hittills har du kört programmets kod som om du var den enda utvecklaren som arbetade med appen. I det här avsnittet lär du dig hur Azure Dev Spaces förenklar utvecklingen i ett team:
* Göra det möjligt för ett utvecklarteam att arbeta i samma miljö, genom att arbeta i en delad utvecklingsmiljö eller i särskilda utvecklingsmiljöer efter behov.
* Varje utvecklare kan arbeta med sin egen kod utan att oroa sig för att skada andras kod.
* Testa koden från slutpunkt till slutpunkt, innan koden checkas in, utan att skapa simulerade objekt eller beroendesimuleringar.

### <a name="challenges-with-developing-microservices"></a>Utmaningar vid utveckling av mikrotjänster
Exempelprogrammet är inte komplext för tillfället. Men i en riktig utvecklingsprocess börjar utmaningarna snart att hopa sig allt eftersom du lägger till fler tjänster och utvecklingsteamet växer.

* Utvecklingsdatorn har kanske inte tillräckligt med resurser för att köra varje tjänst som du behöver på samma gång.
* Vissa tjänster kan behöva vara offentligt nåbara. Till exempel kan en tjänst behöva ha en slutpunkt som svarar på en webhook.
* Om du vill köra en delmängd av tjänster behöver du känna till den fullständiga beroendehierarkin mellan alla dina tjänster. Det kan vara svårt att fastställa den här hierarkin, särskilt när det antal tjänster som du använder ökar.
* Vissa utvecklare försöker kringgå problemet genom att simulera eller testa många av tjänstberoendena. Den här metoden kan vara till hjälp, men att hantera dessa simuleringar kan snabbt börja påverka utvecklingskostnaden. Dessutom gör den metoden att din utvecklingsmiljö ser annorlunda ut jämfört med produktionen, vilket kan ge upphov till svårupptäckta buggar.
* Av detta följer att all slags integreringstestning blir komplicerad. Realistiskt sett kan integrationstestning endast ske när koden checkats in, vilket kan medföra problem senare i utvecklingscykeln.

    ![](media/common/microservices-challenges.png)

### <a name="work-in-a-shared-dev-space"></a>Arbeta i en delad utvecklarmiljö
Med Azure Dev Spaces kan du konfigurera en *delad* utvecklarmiljö i Azure. Varje utvecklare kan fokusera på sin del av programmet, och kan arbeta med *icke incheckad kod* i en utvecklarmiljö som redan innehåller alla andra tjänster och molnresurser som krävs i det aktuella scenariot. Beroenden är alltid uppdaterade och utvecklarna arbetar på ett sätt som återspeglar produktionsmiljön.

### <a name="work-in-your-own-space"></a>Arbeta i ditt eget utrymme
När du utvecklar kod för en tjänst är koden sällan perfekt förrän du väljer att checka in den. Du arbetar ju fortfarande med den, testar den och experimenterar med lösningar. Azure Dev Spaces införlivar begreppet **utrymme**, som innebär att du kan arbeta isolerat, utan att oroa dig för att skada andra teammedlemmars kod.

## <a name="use-dev-spaces-for-team-development"></a>Använda Dev Spaces för teamutveckling
Nu demonstrerar vi de här idéerna via ett konkret exempel med hjälp av vårt exempelprogram *webfrontend* -> *mywebapi*. Vi tänker oss ett scenario där utvecklaren Scott behöver göra en ändring i tjänsten *mywebapi*, och *enbart* den tjänsten. *webfrontend* behöver inte ändras i Scotts uppdatering.

_Utan_ användning av Dev Spaces skulle Scott vara begränsad till vissa sätt att utveckla och testa sina uppdatering. Inget av dessa lämpar sig särskilt väl:
* Köra ALLA komponenter lokalt, vilket kräver en kraftfullare utvecklingsdator med Docker samt potentiellt MiniKube installerat.
* Köra ALLA komponenter i en isolerad namnrymd på Kubernetes-klustret. Eftersom *webfrontend* inte ändras utgör användningen av en isolerad namnrymd ett slöseri med resurser för klustret.
* Köra ENDAST *mywebapi* och göra manuell REST-anrop för att testa. Den här typen av testning testar inte flödet fullständigt från slutpunkt till slutpunkt.
* Lägga till utvecklingsfokuserad kod i *webfrontend* som gör det möjligt för utvecklaren att skicka begäranden till en annan instans av *mywebapi*. Om den här koden läggs till blir tjänsten *webfrontend* mer komplicerad.

### <a name="set-up-your-baseline"></a>Konfigurera din baslinje
Först behöver vi distribuera en baslinje för våra tjänster. Den här distributionen representerar den ”senaste kända välfungerande” så att du enkelt kan jämföra beteendet för din lokala kod jämfört med den incheckade versionen. Sedan skapar vi ett underordnat utrymme baserat på den här baslinjen så att vi kan testa ändringarna i *mywebapi* inom ramen för det större programmet.

1. Klona den [Dev blanksteg exempelprogrammet](https://github.com/Azure/dev-spaces): `git clone https://github.com/Azure/dev-spaces && cd dev-spaces`
1. Kolla in fjärrgren *azds_updates*: `git checkout -b azds_updates origin/azds_updates`
1. Stäng alla F5-/felsökningssessioner för båda tjänsterna, men lämna projekten öppna i Visual Studio-fönstren.
1. Växla till Visual Studio-fönstret med projektet _mywebapi_.
1. Högerklicka på projektet i **Solution Explorer** och välj **Egenskaper**.
1. Visa Azure Dev Spaces-inställningarna genom att välja fliken **Felsök** till vänster.
1. Välj **Ändra** för att skapa det utrymme som ska användas när du trycker på F5 eller Ctrl+F5 för tjänsten.
1. I listrutan Utrymme väljer du **\<Create New Space…\>** (Skapa nytt utrymme…).
1. Kontrollera att det överordnade utrymmet anges till **\<none\>** (inget), och ange utrymmesnamnet **dev**. Klicka på OK.
1. Tryck på Ctrl+F5 för att köra _mywebapi_ utan felsökaren inkopplad.
1. Växla till Visual Studio-fönstret med projektet _webfrontend_ och tryck på Ctrl+F5 för att köra även det.

> [!Note]
> Ibland måste du uppdatera webbläsaren när webbsidan visas efter att du har kört Ctrl+F5.

> [!TIP]
> Stegen ovan konfigurerar en baslinje manuellt, men vi rekommenderar att team använder CI/CD för att automatiskt hålla baslinjen uppdaterad med incheckad kod.
>
> Läs vår [guide om hur du konfigurerar CI/CD med Azure DevOps](how-to/setup-cicd.md) om du vill skapa ett arbetsflöde som liknar följande diagram.
>
> ![CI/CD-exempeldiagram](media/common/ci-cd-complex.png)

Om någon öppnar den offentliga URL:en och navigerar till webbprogrammet anropas den kodsökväg som du har skrivit, som körs via båda tjänsterna med _dev_-standardutrymmet. Anta nu att du vill fortsätta utveckla *mywebapi* – hur kan du göra det utan att det stör andra utvecklare som använder utvecklingsmiljön? Det gör du genom att konfigurera ett eget utrymme.

### <a name="create-a-new-dev-space"></a>Skapa en ny utvecklingsmiljö
I Visual Studio kan du kan skapa ytterligare utrymmen som kan användas när du kör F5 eller Ctrl+F5 mot din tjänst. Du kan ge ett utrymme vilket namn du vill med valfri innebörd (t.ex. _sprint4_ eller _demo_).

Så här skapar du ett nytt utrymme:
1. Växla till Visual Studio-fönstret med projektet *mywebapi*.
2. Högerklicka på projektet i **Solution Explorer** och välj **Egenskaper**.
3. Visa Azure Dev Spaces-inställningarna genom att välja fliken **Felsök** till vänster.
4. Härifrån kan du ändra eller skapa klustret och/eller utrymmet som ska användas när du kör F5 eller Ctrl+F5. *Kontrollera att det Azure Dev Spaces-utrymme som du skapade tidigare är valt*.
5. I listrutan Utrymme väljer du **\<Create New Space…\>** (Skapa nytt utrymme…).

    ![](media/get-started-netcore-visualstudio/Settings.png)

6. I dialogrutan **Lägg till utrymme** anger du det överordnade utrymmet till **dev** och anger ett namn för ditt nya utrymme. Du kan använda ditt namn (till exempel ”scott”) för det nya utrymmet så att dina kolleger ser vilket utrymme du arbetar i. Klicka på **OK**.

    ![](media/get-started-netcore-visualstudio/AddSpace.png)

7. Nu bör du se att ditt AKS-kluster och det nya utrymmet har valts på egenskapssidan för projektet.

    ![](media/get-started-netcore-visualstudio/Settings2.png)

### <a name="update-code-for-mywebapi"></a>Uppdatera koden för *mywebapi*

1. I projektet *mywebapi* gör du en kodändring i metoden `string Get(int id)` i filen `Controllers/ValuesController.cs` enligt följande:
 
    ```csharp
    [HttpGet("{id}")]
    public string Get(int id)
    {
        return "mywebapi now says something new";
    }
    ```

2. Ange en brytpunkt i det här uppdaterade kodblocket (du kanske redan har en sedan tidigare).
3. Tryck på F5 för att starta tjänsten _mywebapi_, vilket startar tjänsten i klustret med det valda utrymmet. I det här fallet är det valda utrymmet _scott_.

Här är ett diagram som hjälper dig att förstå hur de olika utrymmena fungerar. Den lila vägen visar en begäran via utrymmet _dev_, vilket är standardsökvägen som används om inget utrymme har lagts till i URL:en. Den rosa vägen visar en begäran via utrymmet _dev/scott_.

![](media/common/Space-Routing.png)

Med den här inbyggda funktionen i Azure Dev Spaces kan du testa kod från slutpunkt till slutpunkt i en delad miljö utan att varje utvecklare måste återskapa hela tjänststacken i deras respektive utrymme. Den här typen av routning kräver att spridningshuvuden vidarebefordras i programkoden, vilket illustrerades i föregående steg i den här guiden.

### <a name="test-code-running-in-the-devscott-space"></a>Testa den kod som körs i utrymmet _dev/scott_
Du testar den nya versionen av *mywebapi* tillsammans med *webfrontend* genom att öppna webbläsaren på URL:en för offentlig åtkomst för *webfrontend* (till exempel http://dev.webfrontend.123456abcdef.eus.azds.io)) och gå till sidan Om. Du bör se det ursprungliga meddelandet ”Hello from webfrontend and Hello from mywebapi”.

Lägg till delen ”scott.s.” i URL:en, så att den ser ut ungefär så här http://scott.s.dev.webfrontend.123456abcdef.eus.azds.io, och uppdatera webbläsaren. Den brytpunkt som du angett i projektet *mywebapi* bör nås. Fortsätt genom att trycka på F5. Nu bör du se det nya meddelandet ”Hello from webfrontend and mywebapi now says something new”. Det beror på att sökvägen till den uppdaterade koden i *mywebapi* körs i utrymmet _dev/scott_.

När du har ett _dev_-utrymme som alltid innehåller dina senaste ändringar, och under förutsättning att ditt program är utformat för att dra nytta av DevSpace utrymmesbaserade routning enligt beskrivningen i den här delen av självstudien, blir det förhoppningsvis enkelt att se hur Dev Spaces avsevärt kan underlätta testning av nya funktioner inom ramen för det större programmet. I stället för att behöva distribuera _alla_ tjänster till ditt privata utrymme kan du skapa ett privat utrymme som härleds från _dev_ och endast ”införa” de tjänster som du faktiskt arbetar med. Dev Spaces-routningsinfrastrukturen hanterar resten genom att utnyttja så många tjänster utanför ditt privata adressutrymme som går att hitta, samtidigt som det återgår till den senaste versionen som körs i utrymmet _dev_. Ännu bättre är att _flera_ utvecklare aktivt kan utveckla olika tjänster samtidigt i sina egna utrymmen utan att störa varandra.

### <a name="well-done"></a>Bra gjort!
Du har slutfört guiden för att komma igång! Du har lärt dig att:

> [!div class="checklist"]
> * Ställa in Azure Dev Spaces med ett hanterat Kubernetes-kluster i Azure.
> * Iterativt utveckla kod i containrar.
> * Oberoende utvecklar två separata tjänster och använder Kubernetes DNS-tjänstidentifiering för att anropa en annan tjänst.
> * Effektivt utvecklar och testar din kod i en teammiljö.
> * Upprätta en baslinje med funktioner med hjälp av Dev Spaces för att enkelt testa isolerade ändringar inom ramen för ett större mikrotjänstprogram

Nu när du har utforskat Azure Dev Spaces kan du [dela din utvecklarmiljö med en gruppmedlem](how-to/share-dev-spaces.md) och visa personen hur lätt det är att samarbeta.

## <a name="clean-up"></a>Rensa
Om du vill ta bort en Azure Dev Spaces-instans i ett kluster fullständigt, inklusive alla utvecklarmiljöer och tjänster som körs i den, använder du kommandot `az aks remove-dev-spaces`. Kom ihåg att den här åtgärden inte kan ångras. Du kan lägga till stöd för Azure Dev Spaces på klustret igen, men det blir som om du börjar om på nytt. Dina gamla tjänster och utrymmen kommer inte att återställas.

Följande exempel visar en lista över Azure Dev Spaces-kontrollanter i din aktiva prenumeration och sedan tar det bort den Azure Dev Spaces-kontrollant som är associerad med AKS-klustret ”myaks” i resursgruppen ”myaks-rg”.

```cmd
    azds controller list
    az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```
