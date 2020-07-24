---
title: 'Köra flera beroende tjänster: .NET Core & Visual Studio'
services: azure-dev-spaces
ms.custom: vs-azure
ms.workload: azure-vs
ms.date: 07/09/2018
ms.topic: tutorial
description: Den här självstudien visar hur du använder Azure dev Spaces och Visual Studio för att felsöka ett .NET Core-program med flera tjänster i Azure Kubernetes service
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes service, Containers, Helm, service nät, service nät-routning, kubectl, K8s
ms.openlocfilehash: 073019a75f78263e9d300a82469b36268d032679
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87072948"
---
# <a name="running-multiple-dependent-services-net-core-and-visual-studio-with-azure-dev-spaces"></a>Köra flera beroende tjänster: .NET Core och Visual Studio med Azure dev Spaces

I den här självstudien lär du dig att utveckla program för flera tjänster med hjälp av Azure Dev Spaces samt några av de fördelar som finns i Dev Spaces.

## <a name="call-another-container"></a>Anropa en annan container
I det här avsnittet ska du skapa en andra tjänst, `mywebapi`, som ska anropas av `webfrontend`. Varje tjänst körs i en separat container. Du ska sedan felsöka båda containrarna.

![Diagrammet visar webfrontend-tjänstens anrop (som indikeras av en pil) mywebapi-tjänsten.](media/common/multi-container.png)

### <a name="download-sample-code-for-mywebapi"></a>Ladda ned exempelkoden för *mywebapi*
För enkelhetens skull laddar vi ned exempelkoden från en GitHub-databas. Gå till https://github.com/Azure/dev-spaces och välj **Klona eller Ladda ned** för att ladda ned GitHub-databasen. Koden för det här avsnittet finns i `samples/dotnetcore/getting-started/mywebapi`.

### <a name="run-mywebapi"></a>Kör *mywebapi*
1. Öppna projektet `mywebapi` i ett *separat fönster i Visual Studio*.
1. Välj **Azure Dev Spaces** i listrutan med startinställningar som du gjorde tidigare för projektet `webfrontend`. I stället för att skapa ett nytt AKS-kluster väljer du den här gången samma som du redan skapat. Precis som förut lämnar du standardinställningen `default` för Utrymme och klickar på **OK**. I fönstret utdata kanske du märker att Visual Studio börjar "värma upp" den här nya tjänsten i ditt utvecklings utrymme för att snabba upp saker när du startar fel sökning.
1. Tryck på F5 och vänta tills tjänsten har skapats och distribuerats. Processen är klar när statusfältet i Visual Studio blir orange
1. Anteckna slutpunktens webbadress som visas i fönstret **Azure Dev Spaces för AKS** i fönstret **Utdata**. Den ser ut ungefär så här: `http://localhost:<portnumber>`. Det kan verka som om containern körs lokalt, men i själva verket körs den i utvecklingsmiljön i Azure.
2. När `mywebapi` är klar öppnar du webbläsaren på localhost-adressen och lägger till `/api/values` i URL:en för att anropa standard-GET-API:et för `ValuesController`. 
3. Om alla steg lyckades bör du se ett svar från `mywebapi`-tjänsten som ser ut så här.

    ![Webb sidan visar en JSON-matris med två strängar: "värde1" och "värde2".](media/get-started-netcore-visualstudio/WebAPIResponse.png)

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

I föregående kodexempel vidarebefordras rubriken `azds-route-as` från den inkommande till den utgående begäran. Senare ser du hur detta bidrar till en mer produktiv utvecklingsupplevelse i [teamscenarier](team-development-netcore-visualstudio.md).

### <a name="debug-across-multiple-services"></a>Felsöka över flera tjänster
1. I detta läge bör `mywebapi` fortfarande köras med felsökaren. Om inte trycker du på F5 i `mywebapi`-projektet.
1. Ange en brytpunkt i `Get(int id)`-metoden i `Controllers/ValuesController.cs`-filen som hanterar `api/values/{id}`-GET-begäranden.
1. I `webfrontend`-projektet där du klistrade in koden ovan lägger du till en brytpunkt precis innan en GET-begäran skickas till `mywebapi/api/values`.
1. Tryck på F5 i `webfrontend`-projektet. Visual Studio öppnar återigen en webbläsare på lämplig localhost-port och webbappen visas.
1. Klicka på länken **Om** längst upp på sidan för att utlösa brytpunkten i `webfrontend`-projektet. 
1. Fortsätt genom att trycka på F10. Detta utlöser brytpunkten i `mywebapi`-projektet.
1. Fortsätt genom att trycka på F5 så kommer du tillbaka till koden i `webfrontend`-projektet.
1. Om du trycker på F5 en gång till slutförs begäran och en sida returneras i webbläsaren. I webbappen visar sidan Om ett sammanslaget meddelande från de båda tjänsterna: ”Hello from webfrontend and Hello from mywebapi”.

### <a name="well-done"></a>Bra gjort!
Nu har du ett program med flera containrar där varje container kan utvecklas och distribueras separat.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om teamutveckling i Dev Spaces](team-development-netcore-visualstudio.md)
