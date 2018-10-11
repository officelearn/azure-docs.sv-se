---
title: Självstudie – Skapa, felsök och distribuera en webbapp för flera tjänster till Service Fabric Mesh | Microsoft Docs
description: I den här självstudien skapar du ett Azure Service Fabric Mesh-program för flera tjänster som består av en ASP.NET Core-webbplats som kommunicerar med en serversidewebbtjänst, felsöker lokalt och publicerar till Azure.
services: service-fabric-mesh
documentationcenter: .net
author: TylerMSFT
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/18/2018
ms.author: twhitney
ms.custom: mvc, devcenter
ms.openlocfilehash: 09112aafdbabf0cda2b3ae13af73a9223533a6e1
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46979202"
---
# <a name="tutorial-create-debug-deploy-and-upgrade-a-multi-service-service-fabric-mesh-app"></a>Självstudie: skapa, felsök, distribuera och uppgradera en webbapp för flera tjänster till Service Fabric Mesh

Den här självstudien ingår i en serie. Du får lära dig att använda Visual Studio för att skapa en Azure Service Fabric Mesh-app som har en ASP.NET-webbklientdels- och en ASP.NET Core webb-API-serverdelstjänst. Därefter felsöker du appen i det lokala utvecklingsklustret. Du publicerar appen i Azure och gör därefter konfigurations- och kodändringar och uppgraderar appen. Slutligen rensar du upp oanvända Azure-resurser så att du inte debiteras för vad du inte använder.

När du är klar, kommer du att ha gått igenom de flesta faser i en apps livscykelhantering och har skapat en app som visar ett tjänst-till-tjänst-anrop i en Service Fabric Mesh-app.

Om du inte vill skapa programmet manuellt kan du [ladda ned källkoden för det färdiga programmet](https://github.com/azure-samples/service-fabric-mesh) och gå vidare för att [felsöka programmet lokalt](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md).

I del ett i den här serien lärde du dig att:

> [!div class="checklist"]
> * Använd Visual Studio för att skapa en Service Fabric Mesh-app som består av en ASP.NET-webbklientdel.
> * Skapa en modell för att representera att göra-objekt.
> * Skapa en serverdelstjänst och hämta data från den.
> * Lägga till en kontrollant och DataContext som del i MVC-mönstret (Model View Controller) för serverdelstjänsten.
> * Skapa en webbsida för att visa att göra-objekt.
> * Skapa miljövariabler som identifierar serverdelstjänsten

I den här självstudieserien får du lära du dig att:
> [!div class="checklist"]
> * Skapa en Service Fabric Mesh-app i Visual Studio
> * [Felsök en Service Fabric Mesh-app som körs i ditt lokala utvecklingskluster](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * [Distribuera en Service Fabric Mesh-app](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * [Uppgradera en Service Fabric Mesh-app](service-fabric-mesh-tutorial-upgrade.md)
> * [Rensa Service Fabric Mesh-resurser](service-fabric-mesh-tutorial-cleanup-resources.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du börjar den här självstudien:

* Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

* Se till att du har [skapat utvecklingsmiljön](service-fabric-mesh-howto-setup-developer-environment-sdk.md) vilket omfattar att installera Service Fabric-runtime, SDK, Docker, och Visual Studio 2017.

## <a name="create-a-service-fabric-mesh-project-in-visual-studio"></a>Skapa ett Service Fabric Mesh-projekt i Visual Studio

Kör Visual Studio och välj **File** > **New** > **Project...** (Arkiv > Nytt > Projekt...).

I dialogrutan **Nytt projekt** dialogrutan, i rutan **Sök** längst upp skriver du `mesh`. Välj mallen **Service Fabric Mesh Application**. (Om du inte ser mallen kontrollerar du att du har installerat Mesh-SDK och förhandsversionen av VS-verktygen enligt anvisningarna om att [skapa en utvecklingsmiljö](service-fabric-mesh-howto-setup-developer-environment-sdk.md).  

I rutan **Namn** skriver du `todolistapp` och i rutan **Plats** ställer du in mappsökvägen där du vill lagra filen för projektet.

Se till att alternativet för att **skapa katalog för lösning** är markerat och klicka på **OK** för att skapa Service Fabric Mesh-projektet.

![Visual Studio, dialogruta för nytt Service Fabric Mesh-projekt](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-new-project.png)

Därefter ser du dialogrutan **Ny Fabric Service-tjänst**.

### <a name="create-the-web-front-end-service"></a>Skapa webbtjänsten för klientdelen

I dialogrutan **Ny Fabric Service-tjänst** väljer du projekttypen **ASP.NET Core** och ser till att **operativsystem för container** är **Windows**.

Ställ in **tjänstnamnet** som **WebFrontEnd**. Tryck på **OK** för att skapa ASP.NET Core-tjänsten.

![Visual Studio, dialogruta för nytt Service Fabric Mesh-projekt](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-new-service-fabric-service.png)

Nu ser du dialogrutan **New ASP.NET Core Web Application** (Nytt ASP.NET-webbprogram). I dialogrutan **New ASP.NET Core Web Application** (Nytt ASP.NET-webbprogram) väljer du **Web Application** (Webbprogram) och klickar på **OK**.

![Visual Studio, nytt ASP.NET-webbprogram](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-new-aspnetcore-app.png)

Nu har du ett Service Fabric Mesh-program. Skapa sedan modellen för att göra-information.

## <a name="create-the-to-do-items-model"></a>Skapa modellen för att göra-objekt

För enkelhetens skull lagras att göra-objekten i en lista i minnet. Skapa ett klassbibliotek för att göra-objekt och en lista för att lagra dem. I Visual Studio, som för närvarande har **todolistapp** inläst väljer du **File** > **Add** > **New Project** (Arkiv > Lägg till > Nytt projekt).

I dialogrutan **New Project** (Nytt projekt) i rutan **Search** (Sök) längst upp skriver du `C# .net core class`. Välj mallen **Class Library (.NET Core)**.

I rutan **Name** (Namn) skriver du `Model`. Klicka på **OK** för att skapa klassbiblioteket.

I Solution Explorer, under **Model** (Modell) högerklickar du på **Class1.cs** och väljer **Rename** (Byt namn). Byt namn på klassen **ToDoItem.cs**. Om du tillfrågas om du vill byta namn på alla referenser klickar du på **Yes** (Ja).

Ersätt innehållet i den nu tomma `class ToDoItem` med:

```csharp
public class ToDoItem
{
    public string Description { get; set; }
    public int Index { get; set; }
    public bool Completed { get; set; }

    public ToDoItem(string description)
    {
        Description = description;
        Index = 0;
    }

    public static ToDoItem Load(string description, int index, bool completed)
    {
        ToDoItem newItem = new ToDoItem(description)
        {
            Index = index,
            Completed = completed
        };

        return newItem;
    }
}
```

Den här klassen representerar enskilda att göra-objekt.

I Visual Studio högerklickar du på klassbiblioteket **Model** (Modell) och väljer **Add** > **Class...** (Lägg till > Klass) för att skapa en lista för att göra-objekten. Dialogrutan **Add New Item** (Lägg till nytt objekt) visas. Ange **Name** (Namn) som `ToDoList.cs` och klicka på **Add** (Lägg till).

I **ToDoList.cs** ersätter du tomma `class ToDoList` med:

```csharp
public class ToDoList
{
    private List<ToDoItem> _items;

    public string Name { get; set; }
    public IEnumerable<ToDoItem> Items { get => _items; }

    public ToDoList(string name)
    {
        Name = name;
        _items = new List<ToDoItem>();
    }

    public ToDoItem Add(string description)
    {
        var item = new ToDoItem(description);
        _items.Add(item);
        item.Index = _items.IndexOf(item);
        return item;
    }
    public void Add(ToDoItem item)
    {
        _items.Add(item);
        item.Index = _items.Count - 1;
    }

    public ToDoItem RemoveAt(int index)
    {
        if (index >= 0 && index < _items.Count)
        {
            var result = _items[index];
            _items.RemoveAt(index);

            // Reorder items
            for (int i = index; i < _items.Count; i++)
            {
                _items[i].Index = i;
            }

            return result;
        }
        else
        {
            throw new IndexOutOfRangeException();
        }
    }
}
```

Skapa därefter Service Fabric-tjänsten som spårar att göra-objekten.

## <a name="create-the-back-end-service"></a>Skapa serversidetjänsten

I fönstret **Solution Explorer** i Visual Studio högerklickar du på **todolistapp** och klickar på **Lägg till** > **Ny Fabric Service-tjänst...**.

Dialogrutan **Ny Fabric Service-tjänst** visas. Välj projekttypen **ASP.NET Core** och se till att **operativsystem för container** är **Windows**.

Ange **Service Name** (Tjänstnamn) som **ToDoService**. Klicka på **OK** för att skapa ASP.NET Core-tjänsten. Dialogrutan **Nytt ASP.NET-webbprogram** visas därefter. I den dialogrutan väljer du **API** och sedan **OK**, och ett projekt för tjänsten läggs till i lösningen.

![Visual Studio, nytt ASP.NET-webbprogram](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-new-webapi.png)

Eftersom serversidetjänsten inte ger något användargränssnitt kan du inaktivera webbläsarstarten när tjänsten startas. I **Solution Explorer** högerklickar du på **ToDoService** och väljer **Properties** (Egenskaper). I egenskapsfönstret som visas väljer du fliken **Debug** (Felsök) till vänster och avmarkerar **Launch browser** (Starta webbläsare). Tryck på **Ctrl+S** för att spara ändringen.

Eftersom den här tjänsten underhåller att göra-informationen lägger du till en referens till modellklassbiblioteket. I Solution Explorer högerklickar du på **ToDoService** och väljer **Add** > **Reference...** (Lägg till > Referens). Dialogrutan **Reference Manager** (referenshanteraren) visas.

I **Reference Manager** väljer du kryssrutan för **Model** (Modell) och klickar på **OK**.

### <a name="add-a-data-context"></a>Lägga till datakontext

Skapa sedan en datakontext som samordnar skickandet av data från datamodellen.

När du vill lägga till datakontextklassen i Solution Explorer högerklickar du på **ToDoService** och sedan **Add** > **Class** (Lägg till > Klass).
I dialogrutan **Add New Item** (Lägg till nytt objekt) som visas ser du till att **Class** (Klass) är valt, ställer in **Name** (Namn) till `DataContext` och klickar på **Add** (Lägg till).

I **DataContext.cs** ersätter du innehållet i nu tomma `class DataContext` med:

```csharp
public static class DataContext
{
    public static Model.ToDoList ToDoList { get; } = new Model.ToDoList("Azure learning List");

    static DataContext()
    {
        // Seed to-do list
        ToDoList.Add(Model.ToDoItem.Load("Learn about microservices", 0, true));
        ToDoList.Add(Model.ToDoItem.Load("Learn about Service Fabric", 1, true));
        ToDoList.Add(Model.ToDoItem.Load("Learn about Service Fabric Mesh", 2, false));
    }
}
```

Den här minimala datakontexten fyller i vissa exempelobjekt och ger åtkomst till dem.

### <a name="add-a-controller"></a>Lägga till en styrenhet

En standardstyrenhet, som hanterar HTPP-begäranden och som genererar HTTP-svaret, tillhandahölls av mallen när **ToDoService**-projektet skapades. I **Solution Explorer**, under **ToDoService** öppnar du mappen **Controllers** (Kontrollanter) och visar filen **ValuesController.cs**. 

Högerklicka på **ValuesController.cs** och sedan på **Rename** (Byt namn). Byt namn på filen till `ToDoController.cs`. Om du tillfrågas om du vill byta namn på alla referenser klickar du på **Yes** (Ja).

Öppna filen **ToDoController.cs** och byt ut innehållet i `class ToDoController` mot:

```csharp
[Route("api/[controller]")]
public class ToDoController : Controller
{
    // GET api/todo
    [HttpGet]
    public IEnumerable<Model.ToDoItem> Get()
    {
        return DataContext.ToDoList.Items;
    }

    // GET api/todo/5
    [HttpGet("{index}")]
    public Model.ToDoItem Get(int index)
    {
        return DataContext.ToDoList.Items.ElementAt(index);
    }

    //// POST api/values
    //[HttpPost]
    //public void Post([FromBody]string value)
    //{
    //}

    //// PUT api/values/5
    //[HttpPut("{id}")]
    //public void Put(int id, [FromBody]string value)
    //{
    //}

    // DELETE api/values/5
    [HttpDelete("{index}")]
    public void Delete(int index)
    {
    }
}
```

Den här självstudien implementerar inte lägga till, ta bort och så vidare, för att behålla fokus på kommunikation med annan tjänst.

## <a name="create-the-web-page-that-displays-to-do-items"></a>Skapa webbsidan som visar att göra-objekten

Med serversidetjänsten implementerad kodar du webbplatsen som visar de att göra-objekt som tillhandahålls. Följande steg äger rum inom projektet **WebFrontEnd**.

Webbplatsen som visar att göra-objekten behöver åtkomst till klass och lista för **ToDoItem**.
I **Solution Explorer** lägger du till en referens till modellprojektet genom att högerklicka på **WebFrontEnd** och välja **Add** > **Reference...** (Lägg till > Referens). Dialogrutan **Reference Manager** (referenshanteraren) visas.

I **Reference Manager** klickar du i kryssrutan **Model** (Modell) och klickar på **OK**.

I **Solution Explorer** öppnar du sidan Index.cshtml genom att navigera till **WebFrontEnd** > **Pages** > **Index.cshtml**. Öppna **Index.cshtml**.

Ersätt innehållet i hela filen med följande HTML-kod som definierar en enkel tabell för att visa att göra-objekt:

```HTML
@page
@model IndexModel
@{
    ViewData["Title"] = "Home page";
}

<div>
    <table class="table-bordered">
        <thead>
            <tr>
                <th>Description</th>
                <th>Done?</th>
            </tr>
        </thead>
        <tbody>
            @foreach (var item in Model.Items)
            {
                <tr>
                    <td>@item.Description</td>
                    <td>@item.Completed</td>
                </tr>
            }
        </tbody>
    </table>
</div>
```

Öppna koden för sidan Index i **Solution Explorer** genom att öppna **Index.cshtml** och sedan öppna **Index.cshtml.cs**.
Överst i **Index.cshtml.cs** lägger du till `using System.Net.Http;`

Ersätt innehållet i nu tomma `public class IndexModel` med:

```csharp
public class IndexModel : PageModel
{
    public Model.ToDoItem[] Items = new Model.ToDoItem[] { };

    public void OnGet()
    {
        HttpClient client = new HttpClient();

        using (HttpResponseMessage response = client.GetAsync(backendUrl).GetAwaiter().GetResult())
        {
            if (response.StatusCode == System.Net.HttpStatusCode.OK)
            {
                Items = Newtonsoft.Json.JsonConvert.DeserializeObject<Model.ToDoItem[]>(response.Content.ReadAsStringAsync().Result);
            }
        }
    }

    private static string backendDNSName = $"{Environment.GetEnvironmentVariable("ToDoServiceName")}";
    private static Uri backendUrl = new Uri($"http://{backendDNSName}:{Environment.GetEnvironmentVariable("ApiHostPort")}/api/todo");
}
```

### <a name="create-environment-variables"></a>Skapa miljövariabler

Webbadressen för serversidetjänsten krävs för att kommunicera med den tjänsten. I den här kursen läser följande kodutdrag (som definieras ovan som del i IndexModel) miljövariabler för att skapa webbadressen:

```csharp
private static string backendDNSName = $"{Environment.GetEnvironmentVariable("ToDoServiceName")}";
private static Uri backendUrl = new Uri($"http://{backendDNSName}:{Environment.GetEnvironmentVariable("ApiHostPort")}/api/todo");
```

Webbadressen består av tjänstens namn och porten. All information finns i filen service.yaml som finns i projektet **ToDoService**.

Navigera i **Solution Explorer** till projektet **ToDoService** och öppna **Service Resources** (Serviceresurser)  > **service.yaml**.

![Bild 1: filen ToDoService service.yaml](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-serviceyaml-port.png)

* Namnet på tjänsten `ToDoService` finns under `services:` after `name:` (efter) Se (1) i bilden ovan.
* Porten `20008` finns under `endpoints:` after `port:` (efter) Se (2) i bilden ovan. Portnumret för ditt projekt kan vara ett annat.

Miljövariabler som representerar namnet på tjänsten och portnumret definieras därefter i projektet WebFrontEnd så att det kan anropa serversidetjänsten.

I **Solution Explorer** navigerar du till **WebFrontEnd** > **Service Resources** > **service.yaml** för att definiera de variabler som specificerar adressen till serversidetjänsten.

Lägg till följande variabler under `environmentVariables` i service.yaml-filen. Avståndet är viktigt, så justera de variabler du lägger till efter de andra variablerna under `environmentVariables:`

> [!IMPORTANT]
> Blanksteg måste användas, inte tabbar, för att dra in variabler i filen service.yaml. Annars kompileras den inte. Visual Studio kan infoga tabbar när du skapar miljövariablerna. Ersätt alla tabbar med blanksteg. Programmet startar även om du ser fel i felsökningsutdata för **build** (version). Det fungerar dock inte förrän du gör om tabbarna till blanksteg. För att säkerställa att inga tabbar finns i filen service.yaml kan du synliggöra blanksteg i Visual Studio-redigeraren med **Edit**  > **Advanced**  > **View White Space** (Redigera > Avancerat > Visa blanksteg).
> Observera att service.yaml-filer bearbetas på engelska.  Om du till exempel behöver använda en decimalavgränsare så används en punkt i stället för ett kommatecken.

**WebFrontEnd**-projektets **service.yaml**-fil ska se ut ungefär så här, även om värdet `ApiHostPort` förmodligen är ett annat:

![Service.yaml i WebFrontEnd-projekt](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-serviceyaml-envvars.png)

Nu är du redo att skapa och distribuera avbildningen för Service Fabric Mesh-programmet, tillsammans med serverwebbtjänsten, till det lokala klustret.

## <a name="next-steps"></a>Nästa steg

I den här självstudiedelen lärde du dig att:

> [!div class="checklist"]
> * Skapa en Service Fabric Mesh-app bestående av en ASP.NET-webbklientdel.
> * Skapa en modell för att representera att göra-objekt.
> * Skapa en serverdelstjänst och hämta data från den.
> * Lägga till en kontrollant och DataContext som del i MVC-mönstret (Model View Controller) för serverdelstjänsten.
> * Skapa en webbsida för att visa att göra-objekt.
> * Skapa miljövariabler som identifierar serverdelstjänsten

Gå vidare till nästa kurs:
> [!div class="nextstepaction"]
> [Felsök ett Service Fabric Mesh-program som körs i ditt lokala utvecklingskluster](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)