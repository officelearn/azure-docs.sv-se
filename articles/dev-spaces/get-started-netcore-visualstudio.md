---
title: Skapa en Kubernetes-utvecklarmiljö i molnet med .NET Core och Visual Studio | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: tutorial
description: Snabb Kubernetes-utveckling med behållare och mikrotjänster i Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
manager: douge
ms.openlocfilehash: 93c1c9cb27e5eb2d56583dccaffe92e9d50ecc2d
ms.sourcegitcommit: 0408c7d1b6dd7ffd376a2241936167cc95cfe10f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/26/2018
ms.locfileid: "36959282"
---
# <a name="get-started-on-azure-dev-spaces-with-net-core-and-visual-studio"></a>Komma igång med Azure Dev Spaces med .NET Core och Visual Studio

I den här guiden får du lära dig hur du:

- Ställa in Azure Dev Spaces med ett hanterat Kubernetes-kluster i Azure.
- Iterativt utvecklar kod i behållare med Visual Studio.
- Oberoende utvecklar två separata tjänster och använder Kubernetes DNS-tjänstidentifiering för att anropa en annan tjänst.
- Effektivt utvecklar och testar din kod i en teammiljö.

[!INCLUDE[](includes/see-troubleshooting.md)]

[!INCLUDE[](includes/portal-aks-cluster.md)]

## <a name="get-the-visual-studio-tools"></a>Skaffa Visual Studio-verktygen
1. Installera den senaste versionen av [Visual Studio 2017](https://www.visualstudio.com/vs/)
1. Se till att följande arbetsbelastning är markerad i Visual Studio-installationsprogrammet:
    * ASP.NET och webbutveckling
1. Installera [Visual Studio-tillägget för Azure Dev Spaces](https://aka.ms/get-azds-visualstudio)

## <a name="create-a-web-app-running-in-a-container"></a>Skapa en webbapp som körs i en behållare

I det här avsnittet ska du skapa en ASP.NET Core-webbapp och köra den i en behållare i Kubernetes.

### <a name="create-an-aspnet-web-app"></a>Skapa en ASP.NET-webbapp

Skapa ett nytt projekt i Visual Studio 2017. För närvarande måste projektet vara ett **ASP.NET Core-webbprogram**. Ge projektet namnet ”**webfrontend**”.

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

## <a name="debug-a-container-in-kubernetes"></a>Felsöka en behållare i Kubernetes
När utvecklingsmiljön har skapats kan du felsöka programmet. Lägg till en brytpunkt i koden, till exempel på rad 20 i filen `HomeController.cs` där variabeln `Message` anges. Starta felsökningen genom att trycka på **F5**. 

Visual Studio kommunicerar med utvecklingsmiljön för att skapa och distribuera programmet och öppnar sedan en webbläsare där webbprogrammet körs. Det kan verka som om behållaren körs lokalt, men i själva verket körs den i utvecklingsmiljön i Azure. localhost-adressen beror på att Azure Dev Spaces skapar en tillfällig SSH-tunnel för behållaren som körs i AKS.

Klicka på länken **Om** längst upp på sidan för att utlösa brytpunkten. Du har fullständig åtkomst till felsökningsinformation precis som när koden körs lokalt, t.ex. anropsstack, lokala variabler, undantagsinformation och så vidare.

## <a name="iteratively-develop-code"></a>Iterativ kodutveckling

Azure Dev Spaces handlar om mer än att bara få kod att köra i Kubernetes – det handlar om att du snabbt och löpande kan se effekten av dina kodändringar i en Kubernetes-miljö i molnet.

### <a name="update-a-content-file"></a>Uppdatera en innehållsfil
1. Leta upp filen `./Views/Home/Index.cshtml` och gör en ändring i HTML-koden. Ändra till exempel rad 70, `<h2>Application uses</h2>`, till något som: `<h2>Hello k8s in Azure!</h2>`
1. Spara filen.
1. Gå till webbläsaren och uppdatera sidan. Den uppdaterade HTML-koden bör visas på webbsidan.

Vad hände? Redigering av innehållsfiler som HTML och CSS kräver inte omkompilering i en .NET Core-webbapp. En aktiv F5-session synkroniserar automatiskt ändrade innehållsfiler i behållaren som körs i AKS, så att du genast kan se dina innehållsändringar.

### <a name="update-a-code-file"></a>Uppdatera en kodfil
Uppdateringar av kodfiler kräver lite mer arbete eftersom .NET Core-appar måste återskapas och skapa uppdaterade binärfiler för programmet.

1. Stoppa felsökaren i Visual Studio.
1. Öppna kodfilen `Controllers/HomeController.cs` och ändra meddelandet som ska visas på sidan Om: `ViewData["Message"] = "Your application description page.";`
1. Spara filen.
1. Starta felsökningen igen genom att trycka på **F5**. 

I stället för att återskapa och distribuera om en ny behållaravbildning varje gång koden ändras, vilket ofta tar lång tid, kompilerar Azure Dev Spaces om koden inkrementellt i befintliga behållaren för snabbare redigerings- och felsökningsförlopp.

Uppdatera webbappen i webbläsaren och gå till sidan Om. Nu bör ditt anpassade meddelande visas i användargränssnittet.


## <a name="call-another-container"></a>Anropa en annan behållare
I det här avsnittet ska du skapa en andra tjänst, `mywebapi`, som ska anropas av `webfrontend`. Varje tjänst körs i en separat behållare. Du ska sedan felsöka båda behållarna.

![](media/common/multi-container.png)

### <a name="download-sample-code-for-mywebapi"></a>Ladda ned exempelkoden för *mywebapi*
För enkelhetens skull laddar vi ned exempelkoden från en GitHub-databas. Gå till https://github.com/Azure/dev-spaces och välj **Klona eller Ladda ned** för att ladda ned GitHub-databasen. Koden för det här avsnittet finns i `samples/dotnetcore/getting-started/mywebapi`.

### <a name="run-mywebapi"></a>Kör *mywebapi*
1. Öppna projektet `mywebapi` i ett *separat fönster i Visual Studio*.
1. Välj **Azure Dev Spaces** i listrutan med startinställningar som du gjorde tidigare för projektet `webfrontend`. I stället för att skapa ett nytt AKS-kluster väljer du den här gången samma som du redan skapat. Precis som förut lämnar du standardinställningen `default` för Utrymme och klickar på **OK**. I fönstret Output (Utdata) märker du kanske att Visual Studio börjar ”värma upp” den här nya tjänsten i utvecklingsmiljön för att påskynda förloppet när du börjar felsöka.
1. Tryck på F5 och vänta tills tjänsten har skapats och distribuerats. Processen är klar när statusfältet i Visual Studio blir orange
1. Anteckna slutpunktens webbadress som visas i fönstret **Azure Dev Spaces för AKS** i fönstret **Utdata**. Den ser ut ungefär så här: http://localhost:\<portnumber\>. Det kan verka som om behållaren körs lokalt, men i själva verket körs den i utvecklingsmiljön i Azure.
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
1. Om du trycker på F5 en gång till slutförs begäran och en sida returneras i webbläsaren. I webbappen visar sidan Om ett sammanslaget meddelande från de båda tjänsterna: ”Hello from webfrontend and Hello from mywebapi”.

Bra gjort! Nu har du ett program med flera behållare där varje behållare kan utvecklas och distribueras separat.

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

6. Skriv ett namn för utrymmet i dialogrutan för att **lägga till utrymme** och klicka på **OK**. Du kan använda ditt namn (till exempel ”scott”) för det nya utrymmet så att dina kolleger ser vilket utrymme du arbetar i.

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

[!INCLUDE[](includes/well-done.md)]

[!INCLUDE[](includes/clean-up.md)]
