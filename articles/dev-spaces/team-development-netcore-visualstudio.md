---
title: Grupputveckling för Azure Dev Spaces med .NET Core och Visual Studio | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.custom: vs-azure
ms.workload: azure-vs
ms.subservice: azds-kubernetes
author: zr-msft
ms.author: zarhoads
ms.date: 07/09/2018
ms.topic: tutorial
description: Snabb Kubernetes-utveckling med containrar och mikrotjänster i Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
ms.openlocfilehash: ecacb7d3d4576b18eee3faf88c2a598d6acf94a0
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55465375"
---
# <a name="team-development-with-azure-dev-spaces"></a>Teamutveckling med Azure Dev Spaces

I den här självstudien lär du hur kan få flera Dev Spaces att fungera samtidigt i olika utvecklingsmiljöerna,för att hålla separat arbete i separata Dev Spaces i samma kluster.

## <a name="call-another-container"></a>Anropa en annan container
I det här avsnittet ska du skapa en andra tjänst, `mywebapi`, som ska anropas av `webfrontend`. Varje tjänst körs i en separat container. Du ska sedan felsöka båda containrarna.

![](media/common/multi-container.png)

### <a name="download-sample-code-for-mywebapi"></a>Ladda ned exempelkoden för *mywebapi*
För enkelhetens skull laddar vi ned exempelkoden från en GitHub-databas. Gå till https://github.com/Azure/dev-spaces och välj **Klona eller Ladda ned** för att ladda ned GitHub-databasen. Koden för det här avsnittet finns i `samples/dotnetcore/getting-started/mywebapi`.

### <a name="run-mywebapi"></a>Kör *mywebapi*
1. Öppna projektet `mywebapi` i ett *separat fönster i Visual Studio*.
1. Välj **Azure Dev Spaces** i listrutan med startinställningar som du gjorde tidigare för projektet `webfrontend`. I stället för att skapa ett nytt AKS-kluster väljer du den här gången samma som du redan skapat. Precis som förut lämnar du standardinställningen `default` för Utrymme och klickar på **OK**. I fönstret Output (Utdata) märker du kanske att Visual Studio börjar ”värma upp” den här nya tjänsten i utvecklingsmiljön för att påskynda förloppet när du börjar felsöka.
1. Tryck på F5 och vänta tills tjänsten har skapats och distribuerats. Processen är klar när statusfältet i Visual Studio blir orange
1. Anteckna slutpunktens webbadress som visas i fönstret **Azure Dev Spaces för AKS** i fönstret **Utdata**. Den ser ut ungefär så här: http://localhost:\<portnumber\>. Det kan verka som om containern körs lokalt, men i själva verket körs den i utvecklingsmiljön i Azure.
2. När `mywebapi` är klar öppnar du webbläsaren på localhost-adressen och lägger till `/api/values` i URL:en för att anropa standard-GET-API:et för `ValuesController`. 
3. Om alla steg lyckades bör du se ett svar från `mywebapi`-tjänsten som ser ut så här.

    ![](media/get-started-netcore-visualstudio/WebAPIResponse.png)

### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>Skicka en begäran från *webfrontend* till *mywebapi*
Nu ska vi skriva kod i `webfrontend` som skickar en begäran till `mywebapi`. Växla till Visual Studio-fönstret som innehåller `webfrontend`-projektet. *Ersätt* koden för About-metoden i filen `HomeController.cs` med följande kod:

   ```csharp
   public async Task<IActionResult> About()
   {
      ViewData["Message"] = "Hello from webfrontend";

      using (var client = new System.Net.Http.HttpClient())
            {
                // Call *mywebapi*, and display its response in the page
                var request = new System.Net.Http.HttpRequestMessage();
                request.RequestUri = new Uri("http://mywebapi/api/values/1");
                if (this.Request.Headers.ContainsKey("azds-route-as"))
                {
                    // Propagate the dev space routing header
                    request.Headers.Add("azds-route-as", this.Request.Headers["azds-route-as"] as IEnumerable<string>);
                }
                var response = await client.SendAsync(request);
                ViewData["Message"] += " and " + await response.Content.ReadAsStringAsync();
            }

      return View();
   }
   ```

I föregående kodexempel vidarebefordras rubriken `azds-route-as` från den inkommande till den utgående begäran. Senare ser du hur detta bidrar till en mer produktiv utvecklingsupplevelse i teamscenarier.

### <a name="debug-across-multiple-services"></a>Felsöka över flera tjänster
1. I detta läge bör `mywebapi` fortfarande köras med felsökaren. Om inte trycker du på F5 i `mywebapi`-projektet.
1. Ange en brytpunkt i `Get(int id)`-metoden i `Controllers/ValuesController.cs`-filen som hanterar `api/values/{id}`-GET-begäranden.
1. I `webfrontend`-projektet där du klistrade in koden ovan lägger du till en brytpunkt precis innan en GET-begäran skickas till `mywebapi/api/values`.
1. Tryck på F5 i `webfrontend`-projektet. Visual Studio öppnar återigen en webbläsare på lämplig localhost-port och webbappen visas.
1. Klicka på länken **Om** längst upp på sidan för att utlösa brytpunkten i `webfrontend`-projektet. 
1. Fortsätt genom att trycka på F10. Detta utlöser brytpunkten i `mywebapi`-projektet.
1. Fortsätt genom att trycka på F5 så kommer du tillbaka till koden i `webfrontend`-projektet.
1. Om du trycker på F5 en gång till slutförs begäran och en sida returneras i webbläsaren. I webbappen visar sidan ”Om” ett sammanslaget meddelande från de båda tjänsterna: ”Hello from webfrontend and Hello from mywebapi” (Hej från webfrontend och hej från mywebapi).

Bra gjort! Nu har du ett program med flera containrar där varje container kan utvecklas och distribueras separat.

## <a name="learn-about-team-development"></a>Lär dig mer om utveckling i team

Hittills har du kört programmets kod som om du var den enda utvecklaren som arbetade med appen. I det här avsnittet lär du dig hur Azure Dev Spaces förenklar utvecklingen i ett team:
* Göra det möjligt för ett utvecklarteam att arbeta i samma miljö, genom att arbeta i en delad utvecklarmiljö eller i särskilda utvecklarmiljöer efter behov..
* Varje utvecklare kan arbeta med sin egen kod utan att oroa sig för att skada andras kod.
* Testa koden från slutpunkt till slutpunkt, innan koden checkas in, utan att skapa simulerade objekt eller beroendesimuleringar.

### <a name="challenges-with-developing-microservices"></a>Utmaningar vid utveckling av mikrotjänster
Exempelprogrammet är inte särskilt komplext för tillfället. Men i en riktig utvecklingsprocess börjar utmaningarna snart att hopa sig allt eftersom du lägger till fler tjänster och utvecklingsteamet växer.

Föreställ dig att du arbetar med en tjänst som interagerar med en rad andra tjänster.

- I detta fall kan det vara orealistiskt att köra allt lokalt. Utvecklingsdatorn kanske inte har tillräckligt med resurser för att köra hela programmet. Eller så kanske programmet har slutpunkter som måste vara offentligt åtkomliga (till exempel om ditt program svarar på en webhook från ett SaaS-program).
- Du kan försöka att bara köra de tjänster som du är beroende av, men det innebär att du måste känna till hela beroendekedjan (till exempel beroenden av beroenden). Det kan också vara svårt att veta hur du ska skapa och köra beroendena om du inte arbetat med dem.
- Vissa utvecklare försöker kringgå problemet genom att simulera eller testa många av tjänstberoendena. Detta kan hjälpa ibland, men hanteringen av dessa simuleringar kan snabbt förvandlas till ett tidskrävande utvecklingsarbete i sig. Dessutom leder det till att utvecklingsmiljön skiljer sig från produktionsmiljön, och små buggar kan smyga sig in.
- Av detta följer att all slags testning från slutpunkt till slutpunkt blir komplicerad. Realistiskt sett kan integrationstestning endast ske när koden checkats in, vilket kan medföra problem senare i utvecklingscykeln.

    ![](media/common/microservices-challenges.png)

### <a name="work-in-a-shared-dev-space"></a>Arbeta i en delad utvecklarmiljö
Med Azure Dev Spaces kan du konfigurera en *delad* utvecklarmiljö i Azure. Varje utvecklare kan fokusera på sin del av programmet, och kan arbeta med *icke incheckad kod* i en utvecklarmiljö som redan innehåller alla andra tjänster och molnresurser som krävs i det aktuella scenariot. Beroenden är alltid uppdaterade och utvecklarna arbetar på ett sätt som återspeglar produktionsmiljön.

### <a name="work-in-your-own-space"></a>Arbeta i ditt eget utrymme
När du utvecklar kod för en tjänst är koden sällan perfekt förrän du väljer att checka in den. Du arbetar ju fortfarande med den, testar den och experimenterar med lösningar. Azure Dev Spaces införlivar begreppet **utrymme**, som innebär att du kan arbeta isolerat, utan att oroa dig för att skada andra teammedlemmars kod.

Gör följande för att se till att både `webfrontend`- och `mywebapi`-tjänsten körs **i `default`utvecklingsmiljön**.
1. Stäng alla F5-/felsökningssessioner för båda tjänsterna, men lämna projekten öppna i Visual Studio-fönstren.
2. Växla till Visual Studio-fönstret med `mywebapi`-projektet och tryck på Ctrl+F5 för att köra tjänsten utan felsökaren.
3. Växla till Visual Studio-fönstret med `webfrontend`-projektet och tryck på Ctrl+F5 för att köra även den tjänsten.

> [!Note]
> Ibland måste du uppdatera webbläsaren när webbsidan visas efter att du har kört Ctrl+F5.

Om någon öppnar den offentliga URL:en och navigerar till webbprogrammet anropas kodsökvägen som du har skrivit, som körs via båda tjänsterna med `default`-standardutrymmet. Anta nu att du vill fortsätta utveckla `mywebapi` – hur kan du göra det utan att det påverkar andra utvecklare som använder utvecklingsmiljön? Det gör du genom att konfigurera ett eget utrymme.

### <a name="create-a-new-dev-space"></a>Skapa en ny utvecklingsmiljö
I Visual Studio kan du kan skapa ytterligare utrymmen som kan användas när du kör F5 eller Ctrl+F5 mot din tjänst. Du kan ge ett utrymme vilket namn du vill med valfri innebörd (t.ex. `sprint4` eller `demo`).

Så här skapar du ett nytt utrymme:
1. Växla till Visual Studio-fönstret med `mywebapi`-projektet.
2. Högerklicka på projektet i **Solution Explorer** och välj **Egenskaper**.
3. Visa Azure Dev Spaces-inställningarna genom att välja fliken **Felsök** till vänster.
4. Härifrån kan du ändra eller skapa klustret och/eller utrymmet som ska användas när du kör F5 eller Ctrl+F5. *Kontrollera att det Azure Dev Spaces-utrymme som du skapade tidigare är valt*.
5. Välj **<Skapa nytt utrymme…>** i listrutan Utrymme.

    ![](media/get-started-netcore-visualstudio/Settings.png)

6. I dialogrutan **Lägg till utrymme** ställer du in det överordnade utrymmet på **standard** och anger ett namn för ditt nya utrymme. Du kan använda ditt namn (till exempel ”scott”) för det nya utrymmet så att dina kolleger ser vilket utrymme du arbetar i. Klicka på **OK**.

    ![](media/get-started-netcore-visualstudio/AddSpace.png)

7. Nu bör du se att ditt AKS-kluster och det nya utrymmet har valts på egenskapssidan för projektet.

    ![](media/get-started-netcore-visualstudio/Settings2.png)

### <a name="update-code-for-mywebapi"></a>Uppdatera koden för *mywebapi*

1. Ändra koden i `string Get(int id)`-metoden i filen `Controllers/ValuesController.cs` i `mywebapi`-projektet, som du ser här:
 
    ```csharp
    [HttpGet("{id}")]
    public string Get(int id)
    {
        return "mywebapi now says something new";
    }
    ```

2. Ange en brytpunkt i det här uppdaterade kodblocket (du kanske redan har en sedan tidigare).
3. Starta `mywebapi`-tjänsten genom att trycka på F5. När du gör det startar tjänsten i klustret med det valda utrymmet, i det här fallet `scott`.

Här är ett diagram som hjälper dig att förstå hur de olika utrymmena fungerar. Den lila sökvägen visar en begäran via `default`-utrymmet, vilket är standardsökvägen som används om inget utrymme har lagts till i URL:en. Den rosa sökvägen visar en begäran via `default/scott`-utrymmet.

![](media/common/Space-Routing.png)

Med den här inbyggda funktionen i Azure Dev Spaces kan du testa kod från slutpunkt till slutpunkt i en delad miljö utan att varje utvecklare måste återskapa hela tjänststacken i deras respektive utrymme. Den här typen av routning kräver att spridningshuvuden vidarebefordras i programkoden, vilket illustrerades i föregående steg i den här guiden.

### <a name="test-code-running-in-the-defaultscott-space"></a>Testa koden som körs i utrymmet `default/scott`
Du testar den nya versionen av `mywebapi` tillsammans med `webfrontend` genom att öppna webbläsaren på URL:en för offentlig åtkomst för `webfrontend` (till exempel http://webfrontend.123456abcdef.eastus.aksapp.io) och gå till sidan Om. Du bör se det ursprungliga meddelandet ”Hello from webfrontend and Hello from mywebapi”.

Lägg till delen ”scott.s.” i URL:en, så att den ser ut ungefär så här http://scott.s.webfrontend.123456abcdef.eastus.aksapp.io, och uppdatera webbläsaren. Brytpunkten som du angett i `mywebapi`-projektet bör nås. Fortsätt genom att trycka på F5. Nu bör du se det nya meddelandet ”Hello from webfrontend and mywebapi now says something new”. Det beror på att sökvägen till den uppdaterade koden i `mywebapi` körs i `default/scott`-utrymmet.

### <a name="well-done"></a>Bra gjort!
Du har slutfört guiden för att komma igång! Du har lärt dig att:

> [!div class="checklist"]
> * Ställa in Azure Dev Spaces med ett hanterat Kubernetes-kluster i Azure.
> * Iterativt utveckla kod i containrar.
> * Oberoende utvecklar två separata tjänster och använder Kubernetes DNS-tjänstidentifiering för att anropa en annan tjänst.
> * Effektivt utvecklar och testar din kod i en teammiljö.

Nu när du har utforskat Azure Dev Spaces kan du [dela din utvecklarmiljö med en gruppmedlem](how-to/share-dev-spaces.md) och visa personen hur lätt det är att samarbeta.

## <a name="clean-up"></a>Rensa
Om du vill ta bort en Azure Dev Spaces-instans i ett kluster fullständigt, inklusive alla utvecklarmiljöer och tjänster som körs i den, använder du kommandot `az aks remove-dev-spaces`. Kom ihåg att den här åtgärden inte kan ångras. Du kan lägga till stöd för Azure Dev Spaces på klustret igen, men det blir som om du börjar om på nytt. Dina gamla tjänster och utrymmen kommer inte att återställas.

Följande exempel visar en lista över Azure Dev Spaces-kontrollanter i din aktiva prenumeration och sedan tar det bort den Azure Dev Spaces-kontrollant som är associerad med AKS-klustret ”myaks” i resursgruppen ”myaks-rg”.

```cmd
    azds controller list
    az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```
