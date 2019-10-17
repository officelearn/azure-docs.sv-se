---
title: Arbeta med det App Service Mobile Apps hanterade klient biblioteket | Microsoft Docs
description: Lär dig hur du använder .NET-klient biblioteket för Azure App Service Mobile Apps med Windows-och Xamarin-appar.
services: app-service\mobile
documentationcenter: ''
author: elamalani
manager: crdun
editor: ''
ms.assetid: 0280785c-e027-4e0d-aaf2-6f155e5a6197
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 1a56c6eed8b6a78587ea272c44f01d16e8a2b88d
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388707"
---
# <a name="how-to-use-the-managed-client-for-azure-mobile-apps"></a>Så här använder du den hanterade klienten för Azure Mobile Apps
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

> [!NOTE]
> Visual Studio App Center stöder utveckling av slutpunkt till slutpunkt-tjänster och integrerade tjänster som är centrala för utveckling av mobilappar. Utvecklare kan använda tjänsterna för att **bygga**, **testa** och **distribuera** för att skapa en pipeline för kontinuerlig integrering och leverans. När appen har distribuerats kan utvecklarna övervaka status och användning av appen med hjälp av tjänsterna **Analys** och **Diagnostik**, och kommunicera med användarna via **Push**-tjänsten. Utvecklare kan också dra nytta av **Auth** för att autentisera sina användare och tjänsten **Data** för att spara och synkronisera appdata i molnet.
>
> Om du vill integrera moln tjänster i ditt mobil program kan du registrera dig med [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) idag.

## <a name="overview"></a>Översikt
Den här guiden visar hur du utför vanliga scenarier med hjälp av det hanterade klient biblioteket för Azure App Service Mobile Apps för Windows-och Xamarin-appar. Om du är nybörjare på Mobile Apps bör du först följa självstudien för [Azure Mobile Apps snabb start][1] . I den här hand boken fokuserar vi på klient sidans hanterade SDK. Mer information om SDK: er för Server sidan för Mobile Apps finns i dokumentationen för [.NET Server SDK][2] eller [Node. js Server SDK][3].

## <a name="reference-documentation"></a>Referensdokumentation
Referens dokumentationen för klient-SDK: n finns här: [Azure Mobile Apps .net-klient referens][4].
Du kan också hitta flera klient exempel i [Azure-samples GitHub-lagringsplatsen][5].

## <a name="supported-platforms"></a>Plattformar som stöds
.NET-plattformen stöder följande plattformar:

* Xamarin Android-versioner för API 19 – 24 (KitKat via nougat)
* Xamarin iOS-versioner för iOS version 8,0 och senare
* Universell Windows-plattform
* Windows Phone 8,1
* Windows Phone 8,0 utom Silverlight-program

Autentiseringen "Server-Flow" använder en webbvy för det visade användar gränssnittet.  Om enheten inte kan visa användar gränssnittet för WebView krävs andra metoder för autentisering.  Detta SDK är därför inte lämpligt för bevakade eller liknande enheter.

## <a name="setup"></a>Konfiguration och krav
Vi förutsätter att du redan har skapat och publicerat ditt mobil programs Server dels projekt, som innehåller minst en tabell.  I koden som används i det här avsnittet heter tabellen `TodoItem` och innehåller följande kolumner: `Id`, `Text` och `Complete`. Den här tabellen är samma tabell som skapas när du slutför [snabb starten för Azure Mobile Apps][1].

Motsvarande typ av klient sidan i C# är följande klass:

```csharp
public class TodoItem
{
    public string Id { get; set; }

    [JsonProperty(PropertyName = "text")]
    public string Text { get; set; }

    [JsonProperty(PropertyName = "complete")]
    public bool Complete { get; set; }
}
```

[JsonPropertyAttribute][6] används för att definiera *PropertyName* -mappning mellan fältet klient och fältet tabell.

Information om hur du skapar tabeller i din Mobile Apps Server del finns i [avsnittet .NET Server SDK][7] eller i [avsnittet Node. js-Server-SDK][8]. Om du har skapat en server del för mobilappen i Azure Portal med snabb starten kan du också använda inställningen **enkla tabeller** i [Azure-portalen].

### <a name="how-to-install-the-managed-client-sdk-package"></a>Gör så här: installera det hanterade klient-SDK-paketet
Använd någon av följande metoder för att installera SDK-paketet för den hanterade klienten för Mobile Apps från [NuGet][9]:

* **Visual Studio** Högerklicka på projektet, klicka på **Hantera NuGet-paket**, Sök efter paketet `Microsoft.Azure.Mobile.Client` och klicka sedan på **Installera**.
* **Xamarin Studio** Högerklicka på projektet, klicka på **Lägg till** > **Lägg till NuGet-paket**, Sök efter `Microsoft.Azure.Mobile.Client`-paketet och klicka sedan på **Lägg till paket**.

Kom ihåg att lägga till följande **using** -instruktion i huvud aktivitets filen:

```csharp
using Microsoft.WindowsAzure.MobileServices;
```

> [!NOTE]
> Observera att alla supportpaket som det refereras till i ditt Android-projekt måste ha samma version. SDK: n har `Xamarin.Android.Support.CustomTabs` beroende för Android-plattform, så om ditt projekt använder nyare support paket måste du installera det här paketet med den version som krävs direkt för att undvika konflikter.

### <a name="symbolsource"></a>Gör så här: arbeta med fel söknings symboler i Visual Studio
Symbolerna för namn området Microsoft. Azure. Mobile finns på [SymbolSource][10].  Se SymbolSource- [instruktionerna][11] för att integrera SymbolSource med Visual Studio.

## <a name="create-client"></a>Skapa Mobile Apps-klienten
Följande kod skapar [MobileServiceClient][12] -objektet som används för att komma åt Server delen för mobilappar.

```csharp
var client = new MobileServiceClient("MOBILE_APP_URL");
```

I föregående kod ersätter du `MOBILE_APP_URL` med URL: en för Server delen för mobilappar, som finns på bladet för Server delen för mobilappar i [Azure-portalen]. MobileServiceClient-objektet ska vara en singleton.

## <a name="work-with-tables"></a>Arbeta med tabeller
I följande avsnitt beskrivs hur du söker efter och hämtar poster och ändrar data i tabellen.  Följande avsnitt beskrivs:

* [Skapa en tabell referens](#instantiating)
* [Fråga efter data](#querying)
* [Filtrera returnerade data](#filtering)
* [Sortera returnerade data](#sorting)
* [Returnera data på sidor](#paging)
* [Välj vissa kolumner](#selecting)
* [Leta upp en post efter ID](#lookingup)
* [Hantera frågor utan typ](#untypedqueries)
* [Infoga data](#inserting)
* Uppdaterar data
* [Ta bort data](#deleting)
* [Konflikt lösning och optimistisk samtidighet](#optimisticconcurrency)
* [Bindning till ett Windows-användargränssnitt](#binding)
* [Ändra sid storlek](#pagesize)

### <a name="instantiating"></a>Gör så här: skapa en tabell referens
All kod som används för att komma åt eller ändra data i en server dels tabell anropar funktioner i `MobileServiceTable`-objektet. Hämta en referens till tabellen genom att anropa metoden [GetTable] enligt följande:

```csharp
IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
```

Det returnerade objektet använder den skrivna serialiserade modellen. En modell med en typ som inte är typ stöds också. I följande exempel [skapas en referens till en tabell som inte är av typen]:

```csharp
// Get an untyped table reference
IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");
```

I frågor som inte har angets måste du ange den underliggande OData-frågesträngen.

### <a name="querying"></a>Så här gör du: fråga efter data från mobilappen
I det här avsnittet beskrivs hur du skickar frågor till Server delen för mobilappar, som innehåller följande funktioner:

* [Filtrera returnerade data](#filtering)
* [Sortera returnerade data](#sorting)
* [Returnera data på sidor](#paging)
* [Välj vissa kolumner](#selecting)
* [Leta upp data efter ID](#lookingup)

> [!NOTE]
> En server driven sid storlek tillämpas för att förhindra att alla rader returneras.  Sid indelning behåller standard begär Anden för stora data mängder från att påverka tjänsten negativt.  Om du vill returnera fler än 50 rader använder du metoden `Skip` och `Take`, enligt beskrivningen i [returnera data på sidor](#paging).

### <a name="filtering"></a>Gör så här: filtrera returnerade data
Följande kod visar hur du filtrerar data genom att inkludera en `Where`-sats i en fråga. Den returnerar alla objekt från `todoTable` vars `Complete`-egenskap är lika med `false`. Funktionen [Vilken] använder ett rad filtrerings-predikat för frågan mot tabellen.

```csharp
// This query filters out completed TodoItems and items without a timestamp.
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .ToListAsync();
```

Du kan visa URI: n för begäran som skickats till Server delen med hjälp av meddelande gransknings program vara, t. ex. webbläsarbaserade utvecklingsverktyg eller [Fiddler]. Lägg märke till att frågesträngen ändras om du tittar på URI: n för begäran:

```csharp
GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1
```

Denna OData-begäran översätts till en SQL-fråga av Server-SDK: n:

```csharp
SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0
```

Funktionen som skickas till metoden `Where` kan ha ett godtyckligt antal villkor.

```csharp
// This query filters out completed TodoItems where Text isn't null
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false && todoItem.Text != null)
    .ToListAsync();
```

Det här exemplet skulle översättas till en SQL-fråga av Server-SDK: n:

```csharp
SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0
          AND ISNULL(text, 0) = 0
```

Den här frågan kan också delas upp i flera satser:

```csharp
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .Where(todoItem => todoItem.Text != null)
    .ToListAsync();
```

De två metoderna är likvärdiga och kan användas utbytbart.  Det tidigare alternativet @ no__t-0of sammanfogar flera predikat i en fråga @ no__t-1is Compact och Recommended.

@No__t-0-satsen stöder åtgärder som översätts till OData-delmängden. Åtgärderna omfattar:

* Relations operatorer (= =,! =, <, < =, >, > =),
* Aritmetiska operatorer (+,-,/, *,%),
* Tal precision (matematik. golv, matematik. tak)
* Sträng funktioner (längd, del sträng, ersätta, IndexOf, StartsWith, EndsWith),
* Datum egenskaper (år, månad, dag, timme, minut, sekund),
* Åtkomst egenskaper för ett objekt och
* Uttryck som kombinerar någon av dessa åtgärder.

När du överväger att Server-SDK: n stöder kan du överväga [OData v3-dokumentation].

### <a name="sorting"></a>Så här: sortera returnerade data
Följande kod visar hur du sorterar data genom att inkludera en [OrderBy] -eller [OrderByDescending] -funktion i frågan. Den returnerar objekt från `todoTable` sorterade stigande efter fältet `Text`.

```csharp
// Sort items in ascending order by Text field
MobileServiceTableQuery<TodoItem> query = todoTable
                .OrderBy(todoItem => todoItem.Text)
List<TodoItem> items = await query.ToListAsync();

// Sort items in descending order by Text field
MobileServiceTableQuery<TodoItem> query = todoTable
                .OrderByDescending(todoItem => todoItem.Text)
List<TodoItem> items = await query.ToListAsync();
```

### <a name="paging"></a>Så här gör du: returnera data på sidor
Server delen returnerar som standard bara de första 50 raderna. Du kan öka antalet returnerade rader genom att anropa metoden [Gå] . Använd `Take` tillsammans med [Ignorera] -metoden för att begära en speciell "sida" av den totala data mängden som returneras av frågan. Följande fråga, när den körs, returnerar de tre översta objekten i tabellen.

```csharp
// Define a filtered query that returns the top 3 items.
MobileServiceTableQuery<TodoItem> query = todoTable.Take(3);
List<TodoItem> items = await query.ToListAsync();
```

Följande ändrade fråga hoppar över de första tre resultaten och returnerar följande tre resultat. Den här frågan genererar den andra "sidan" av data där sid storleken är tre objekt.

```csharp
// Define a filtered query that skips the top 3 items and returns the next 3 items.
MobileServiceTableQuery<TodoItem> query = todoTable.Skip(3).Take(3);
List<TodoItem> items = await query.ToListAsync();
```

Metoden [IncludeTotalCount] begär det totala antalet för *alla* poster som skulle ha returnerats, vilket ignorerar växling/gräns-satsen som har angetts:

```csharp
query = query.IncludeTotalCount();
```

I en verklig världs-app kan du använda frågor som liknar föregående exempel med en pager-kontroll eller ett jämförbart gränssnitt för att navigera mellan sidor.

> [!NOTE]
> Om du vill åsidosätta gränsen på 50-rader i en server del för mobilappar, måste du även använda [EnableQueryAttribute] till den offentliga Get-metoden och ange växlings beteendet. När det tillämpas på-metoden ställer följande in maximalt antal returnerade rader till 1000:
>
> `[EnableQuery(MaxTop=1000)]`


### <a name="selecting"></a>Gör så här: Välj vissa kolumner
Du kan ange vilken uppsättning egenskaper som ska ingå i resultatet genom att lägga till en [Välj] -sats i frågan. Följande kod visar till exempel hur du väljer bara ett fält och hur du väljer och formaterar flera fält:

```csharp
// Select one field -- just the Text
MobileServiceTableQuery<TodoItem> query = todoTable
                .Select(todoItem => todoItem.Text);
List<string> items = await query.ToListAsync();

// Select multiple fields -- both Complete and Text info
MobileServiceTableQuery<TodoItem> query = todoTable
                .Select(todoItem => string.Format("{0} -- {1}",
                    todoItem.Text.PadRight(30), todoItem.Complete ?
                    "Now complete!" : "Incomplete!"));
List<string> items = await query.ToListAsync();
```

Alla funktioner som beskrivs hittills är additiva så att vi kan fortsätta att länka dem. Varje länkat anrop påverkar mer av frågan. Ett annat exempel:

```csharp
MobileServiceTableQuery<TodoItem> query = todoTable
                .Where(todoItem => todoItem.Complete == false)
                .Select(todoItem => todoItem.Text)
                .Skip(3).
                .Take(3);
List<string> items = await query.ToListAsync();
```

### <a name="lookingup"></a>Gör så här: Leta upp data efter ID
Funktionen [LookupAsync] kan användas för att söka efter objekt från databasen med ett visst ID.

```csharp
// This query filters out the item with the ID of 37BBF396-11F0-4B39-85C8-B319C729AF6D
TodoItem item = await todoTable.LookupAsync("37BBF396-11F0-4B39-85C8-B319C729AF6D");
```

### <a name="untypedqueries"></a>Gör så här: köra frågor som inte har påskrivits
När du kör en fråga med ett objekt som inte är av typen% måste du uttryckligen ange OData-frågesträngen genom att anropa [ReadAsync], som i följande exempel:

```csharp
// Lookup untyped data using OData
JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");
```

Du får tillbaka JSON-värden som du kan använda som egenskaps uppsättning. Mer information om JToken och Newtonsoft Json.NET finns på [JSON.net] -webbplatsen.

### <a name="inserting"></a>Så här: infoga data i en server del för mobila appar
Alla klient typer måste innehålla en medlem med namnet **ID**, som är som standard en sträng. Detta **ID** krävs för att utföra CRUD-åtgärder och för synkronisering offline. Följande kod visar hur du använder [InsertAsync] -metoden för att infoga nya rader i en tabell. Parametern innehåller de data som ska infogas som ett .NET-objekt.

```csharp
await todoTable.InsertAsync(todoItem);
```

Om ett unikt anpassat ID-värde inte ingår i `todoItem` under en infogning, genereras ett GUID av servern.
Du kan hämta det genererade ID: t genom att inspektera objektet när anropet har returnerats.

För att infoga data som inte har matats in kan du dra nytta av Json.NET:

```csharp
JObject jo = new JObject();
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.InsertAsync(jo);
```

Här är ett exempel som använder en e-postadress som ett unikt sträng-ID:

```csharp
JObject jo = new JObject();
jo.Add("id", "myemail@emaildomain.com");
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.InsertAsync(jo);
```

### <a name="working-with-id-values"></a>Arbeta med ID-värden
Mobile Apps stöder unika anpassade sträng värden för tabellens **ID-** kolumn. Med ett sträng värde kan program använda anpassade värden, till exempel e-postadresser eller användar namn för ID: t.  Sträng-ID: n ger dig följande fördelar:

* ID: n genereras utan att göra en tur och retur i databasen.
* Poster är enklare att sammanfoga från olika tabeller och databaser.
* ID-värden kan integreras bättre med ett programs logik.

När ett sträng-ID-värde inte är inställt på en infogad post genererar mobilappen för mobilappen ett unikt värde för ID: t. Du kan använda metoden [GUID. NewGuid] för att generera egna ID-värden, antingen på klienten eller i Server delen.

```csharp
JObject jo = new JObject();
jo.Add("id", Guid.NewGuid().ToString("N"));
```

### <a name="modifying"></a>Gör så här: ändra data i en server del för mobila appar
Följande kod visar hur du använder [UpdateAsync] -metoden för att uppdatera en befintlig post med samma ID med ny information. Parametern innehåller de data som ska uppdateras som ett .NET-objekt.

```csharp
await todoTable.UpdateAsync(todoItem);
```

Om du vill uppdatera data som inte har skrivits kan du dra nytta av [JSON.net] på följande sätt:

```csharp
JObject jo = new JObject();
jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.UpdateAsync(jo);
```

Ett `id`-fält måste anges när en uppdatering görs. Server delen använder fältet `id` för att identifiera vilken rad som ska uppdateras. Fältet `id` kan hämtas från resultatet av anropet `InsertAsync`. En `ArgumentException` aktive ras om du försöker uppdatera ett objekt utan att ange värdet för @no__t 1.

### <a name="deleting"></a>Så här gör du: ta bort data i en server del för mobila appar
Följande kod visar hur du använder [DeleteAsync] -metoden för att ta bort en befintlig instans. Instansen identifieras av `id`-fältet som anges i `todoItem`.

```csharp
await todoTable.DeleteAsync(todoItem);
```

För att ta bort data som inte har påskrivits kan du dra nytta av Json.NET på följande sätt:

```csharp
JObject jo = new JObject();
jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
await table.DeleteAsync(jo);
```

När du gör en borttagnings förfrågan måste du ange ett ID. Andra egenskaper skickas inte till tjänsten eller ignoreras vid tjänsten. Resultatet av ett `DeleteAsync`-anrop är vanligt vis `null`. Det ID som ska skickas kan hämtas från resultatet av anropet `InsertAsync`. En `MobileServiceInvalidOperationException` genereras när du försöker ta bort ett objekt utan att ange fältet `id`.

### <a name="optimisticconcurrency"></a>Gör så här: Använd optimistisk samtidighet för konflikt lösning
Två eller flera klienter kan skriva ändringar till samma objekt på samma tidpunkt. Utan konflikt identifiering skulle den senaste skrivningen skriva över alla tidigare uppdateringar. **Optimistisk concurrency-kontroll** förutsätter att varje transaktion kan genomföra och därför inte använder någon resurs låsning.  Innan du genomför en transaktion verifierar optimistisk concurrency-kontrollen att ingen annan transaktion har ändrat data. Om data har ändrats återställs commit-transaktionen.

Mobile Apps stöder optimistisk concurrency-kontroll genom att spåra ändringar av varje objekt med hjälp av den `version` system egenskaps kolumn som definieras för varje tabell i din server del för mobila appar. Varje gång en post uppdateras anger Mobile Apps egenskapen `version` för posten till ett nytt värde. Under varje uppdaterings förfrågan jämförs egenskapen `version` för posten som ingår i begäran med samma egenskap för posten på servern. Om versionen som skickades med begäran inte matchar Server delen, genererar klient biblioteket ett `MobileServicePreconditionFailedException<T>`-undantag. Den typ som ingår i undantaget är posten från Server delen som innehåller servrarnas version av posten. Programmet kan sedan använda den här informationen för att avgöra om du vill köra uppdateringsbegäran igen med rätt `version`-värde från Server delen för att genomföra ändringar.

Definiera en kolumn i tabell klassen för system egenskapen `version` för att aktivera optimistisk samtidighet. Exempel:

```csharp
public class TodoItem
{
    public string Id { get; set; }

    [JsonProperty(PropertyName = "text")]
    public string Text { get; set; }

    [JsonProperty(PropertyName = "complete")]
    public bool Complete { get; set; }

    // *** Enable Optimistic Concurrency *** //
    [JsonProperty(PropertyName = "version")]
    public string Version { set; get; }
}
```

Program som använder tabeller utan typ möjliggör optimistisk samtidighet genom att ange flaggan `Version` i `SystemProperties` i tabellen enligt följande.

```csharp
//Enable optimistic concurrency by retrieving version
todoTable.SystemProperties |= MobileServiceSystemProperties.Version;
```

Förutom att aktivera optimistisk samtidighet måste du också fånga det `MobileServicePreconditionFailedException<T>`-undantaget i koden när du anropar [UpdateAsync].  Lös konflikten genom att använda rätt `version` till den uppdaterade posten och anropa [UpdateAsync] med den lösta posten. Följande kod visar hur du löser en Skriv konflikt när den har identifierats:

```csharp
private async void UpdateToDoItem(TodoItem item)
{
    MobileServicePreconditionFailedException<TodoItem> exception = null;

    try
    {
        //update at the remote table
        await todoTable.UpdateAsync(item);
    }
    catch (MobileServicePreconditionFailedException<TodoItem> writeException)
    {
        exception = writeException;
    }

    if (exception != null)
    {
        // Conflict detected, the item has changed since the last query
        // Resolve the conflict between the local and server item
        await ResolveConflict(item, exception.Item);
    }
}


private async Task ResolveConflict(TodoItem localItem, TodoItem serverItem)
{
    //Ask user to choose the resolution between versions
    MessageDialog msgDialog = new MessageDialog(
        String.Format("Server Text: \"{0}\" \nLocal Text: \"{1}\"\n",
        serverItem.Text, localItem.Text),
        "CONFLICT DETECTED - Select a resolution:");

    UICommand localBtn = new UICommand("Commit Local Text");
    UICommand ServerBtn = new UICommand("Leave Server Text");
    msgDialog.Commands.Add(localBtn);
    msgDialog.Commands.Add(ServerBtn);

    localBtn.Invoked = async (IUICommand command) =>
    {
        // To resolve the conflict, update the version of the item being committed. Otherwise, you will keep
        // catching a MobileServicePreConditionFailedException.
        localItem.Version = serverItem.Version;

        // Updating recursively here just in case another change happened while the user was making a decision
        UpdateToDoItem(localItem);
    };

    ServerBtn.Invoked = async (IUICommand command) =>
    {
        RefreshTodoItems();
    };

    await msgDialog.ShowAsync();
}
```

Mer information finns i avsnittet [Datasynkronisering offline i Azure Mobile Apps] .

### <a name="binding"></a>Gör så här: bind Mobile Apps data till ett Windows-användargränssnitt
I det här avsnittet visas hur du visar returnerade data objekt med hjälp av GRÄNSSNITTs element i en Windows-app.  Följande exempel kod binder till källan för listan med en fråga för oavslutade objekt. [MobileServiceCollection] skapar en Mobile Apps medveten bindnings samling.

```csharp
// This query filters out completed TodoItems.
MobileServiceCollection<TodoItem, TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .ToCollectionAsync();

// itemsControl is an IEnumerable that could be bound to a UI list control
IEnumerable itemsControl  = items;

// Bind this to a ListBox
ListBox lb = new ListBox();
lb.ItemsSource = items;
```

Vissa kontroller i den hanterade körnings miljön stöder ett gränssnitt som kallas [ISupportIncrementalLoading]. Det här gränssnittet gör det möjligt för kontroller att begära extra data när användaren rullar. Det finns inbyggt stöd för det här gränssnittet för universella Windows-appar via [MobileServiceIncrementalLoadingCollection], som automatiskt hanterar anropen från kontrollerna. Använd `MobileServiceIncrementalLoadingCollection` i Windows-appar på följande sätt:

```csharp
MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
items = todoTable.Where(todoItem => todoItem.Complete == false).ToIncrementalLoadingCollection();

ListBox lb = new ListBox();
lb.ItemsSource = items;
```

Om du vill använda den nya samlingen på Windows Phone 8-och "Silverlight"-appar använder du `ToCollection`-tilläggs metoder på `IMobileServiceTableQuery<T>` och `IMobileServiceTable<T>`. Anropa `LoadMoreItemsAsync()` om du vill läsa in data.

```csharp
MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection();
await items.LoadMoreItemsAsync();
```

När du använder samlingen som skapats genom att anropa `ToCollectionAsync` eller `ToCollection`, får du en samling som kan bindas till UI-kontroller.  Den här samlingen är växlings medveten.  Eftersom samlingen läser in data från nätverket går det ibland inte att läsa in. Om du vill hantera sådana fel kan du åsidosätta metoden `OnException` på `MobileServiceIncrementalLoadingCollection` för att hantera undantag som uppstår vid anrop till `LoadMoreItemsAsync`.

Överväg om din tabell har många fält men du bara vill visa några av dem i kontrollen. Du kan använda vägledningen i föregående avsnitt "[Välj vissa kolumner](#selecting)" om du vill välja vissa kolumner som ska visas i användar gränssnittet.

### <a name="pagesize"></a>Ändra sid storlek
Azure Mobile Apps returnerar högst 50 objekt per begäran som standard.  Du kan ändra växlings storleken genom att öka den maximala sid storleken på både klienten och servern.  Om du vill öka den begärda sid storleken anger du `PullOptions` när du använder `PullAsync()`:

```csharp
PullOptions pullOptions = new PullOptions
    {
        MaxPageSize = 100
    };
```

Förutsatt att du har gjort `PageSize` lika med eller större än 100 på servern, returnerar en begäran upp till 100 objekt.

## <a name="#offlinesync"></a>Arbeta med offline-tabeller
Offline-tabeller använder en lokal SQLite-lagring för att lagra data som ska användas när de är offline.  Alla tabell åtgärder görs mot den lokala SQLite-butiken i stället för fjärrserverns lagrings plats.  Om du vill skapa en offline-tabell förbereder du först ditt projekt:

1. I Visual Studio högerklickar du på lösningen > **Hantera NuGet-paket för lösningen...** och söker efter och installerar paketet **Microsoft. Azure. Mobile. client. SQLiteStore** NuGet för alla projekt i lösningen.
2. Valfritt Om du vill ha stöd för Windows-enheter installerar du ett av följande SQLite runtime-paket:

   * **Windows 8,1-körning:** Installera [sqlite för Windows 8,1][3].
   * **Windows Phone 8,1:** Installera [sqlite för Windows Phone 8,1][4].
   * **Universell Windows-plattform** Installera [sqlite för universella Windows][5].
3. (Valfritt). För Windows-enheter klickar du på **referenser** > **Lägg till referens...** , expandera **Windows** -mappen > **tillägg**och aktivera lämplig **sqlite för Windows** SDK tillsammans med **Visual C++ 2013-körningen för Windows** SDK.
    SQLite SDK-namnen skiljer sig något med varje Windows-plattform.

Innan en tabell referens kan skapas måste det lokala lagret förberedas:

```csharp
var store = new MobileServiceSQLiteStore(Constants.OfflineDbPath);
store.DefineTable<TodoItem>();

//Initializes the SyncContext using the default IMobileServiceSyncHandler.
await this.client.SyncContext.InitializeAsync(store);
```

Lagrings initiering görs normalt omedelbart efter att klienten har skapats.  **OfflineDbPath** bör vara ett fil namn som lämpar sig för användning på alla plattformar som du stöder.  Om sökvägen är en fullständigt kvalificerad sökväg (dvs. den börjar med ett snedstreck) används den sökvägen.  Om sökvägen inte är fullständigt kvalificerad placeras filen på en plattformsspecifik plats.

* För iOS-och Android-enheter är standard Sök vägen mappen "personliga filer".
* För Windows-enheter är standard Sök vägen den programspecifika "AppData"-mappen.

En tabell referens kan hämtas med metoden `GetSyncTable<>`:

```csharp
var table = client.GetSyncTable<TodoItem>();
```

Du behöver inte autentisera för att använda en offline-tabell.  Du behöver bara autentisera när du kommunicerar med backend-tjänsten.

### <a name="syncoffline"></a>Synkronisera en offline-tabell
Offline-tabeller synkroniseras inte med Server delen som standard.  Synkroniseringen delas upp i två delar.  Du kan skicka ändringar separat från att hämta nya objekt.  Här är en typisk synkroniseringsmetod:

```csharp
public async Task SyncAsync()
{
    ReadOnlyCollection<MobileServiceTableOperationError> syncErrors = null;

    try
    {
        await this.client.SyncContext.PushAsync();

        await this.todoTable.PullAsync(
            //The first parameter is a query name that is used internally by the client SDK to implement incremental sync.
            //Use a different query name for each unique query in your program
            "allTodoItems",
            this.todoTable.CreateQuery());
    }
    catch (MobileServicePushFailedException exc)
    {
        if (exc.PushResult != null)
        {
            syncErrors = exc.PushResult.Errors;
        }
    }

    // Simple error/conflict handling. A real application would handle the various errors like network conditions,
    // server conflicts and others via the IMobileServiceSyncHandler.
    if (syncErrors != null)
    {
        foreach (var error in syncErrors)
        {
            if (error.OperationKind == MobileServiceTableOperationKind.Update && error.Result != null)
            {
                //Update failed, reverting to server's copy.
                await error.CancelAndUpdateItemAsync(error.Result);
            }
            else
            {
                // Discard local change.
                await error.CancelAndDiscardItemAsync();
            }

            Debug.WriteLine(@"Error executing sync operation. Item: {0} ({1}). Operation discarded.", error.TableName, error.Item["id"]);
        }
    }
}
```

Om det första argumentet till `PullAsync` är null används inte stegvis synkronisering.  Varje Sync-åtgärd hämtar alla poster.

SDK utför en implicit `PushAsync()` innan du hämtar poster.

Konflikt hantering sker på en `PullAsync()`-metod.  Du kan hantera konflikter på samma sätt som online-tabeller.  Konflikten skapas när `PullAsync()` anropas i stället för under INSERT, Update eller Delete. Om flera konflikter sker paketeras de i en enda MobileServicePushFailedException.  Hantera varje enskilt haveri separat.

## <a name="#customapi"></a>Arbeta med ett anpassat API
Med ett anpassat API kan du definiera anpassade slut punkter som visar Server funktioner som inte mappas till en INSERT-, Update-, DELETE-eller Read-åtgärd. Genom att använda ett anpassat API kan du ha mer kontroll över meddelanden, inklusive läsa och ställa in HTTP-meddelandehuvuden och definiera ett annat text format än JSON.

Du anropar ett anpassat API genom att anropa en av [InvokeApiAsync] -metoderna på klienten. Följande kodrad skickar till exempel en POST-begäran till **completeAll** -API: et på Server delen:

```javascript
var result = await client.InvokeApiAsync<MarkAllResult>("completeAll", System.Net.Http.HttpMethod.Post, null);
```

Det här formuläret är ett inskrivet metod anrop och kräver att RETUR typen **MarkAllResult** har definierats. Både skrivna och avskrivna metoder stöds.

Metoden InvokeApiAsync () prepends "/API/" till det API som du vill anropa om inte API: et börjar med "/".
Exempel:

* `InvokeApiAsync("completeAll",...)` anropar/api/completeAll på Server delen
* `InvokeApiAsync("/.auth/me",...)` anropar/.auth/me på Server delen

Du kan använda InvokeApiAsync för att anropa eventuella WebAPI, inklusive de WebAPI: er som inte har definierats med Azure Mobile Apps.  När du använder InvokeApiAsync () skickas lämpliga huvuden, inklusive autentiseringsscheman, med begäran.

## <a name="authentication"></a>Autentisera användare
Mobile Apps stöder autentisering och auktorisering av App-användare med olika externa identitets leverantörer: Facebook, Google, Microsoft-konto, Twitter och Azure Active Directory. Du kan ange behörigheter för tabeller för att begränsa åtkomsten för vissa åtgärder till endast autentiserade användare. Du kan också använda identiteten för autentiserade användare för att implementera auktoriseringsregler i Server skript. Mer information finns i självstudierna [Lägg till autentisering i appen].

Två autentiserings flöden stöds: *klient-hanterat* och *Server-hanterat* flöde. Det Server-hanterade flödet ger den enklaste autentiseringen, eftersom det förlitar sig på providerns webbautentiserings-gränssnitt. Det klient-hanterade flödet möjliggör djupare integrering med enhetsspecifika funktioner eftersom det förlitar sig på providerspecifika, enhetsspecifika SDK: er.

> [!NOTE]
> Vi rekommenderar att du använder ett klient hanterat flöde i dina produktions program.

Om du vill konfigurera autentisering måste du registrera din app med en eller flera identitets leverantörer.  Identitets leverantören genererar ett klient-ID och en klient hemlighet för din app.  Dessa värden ställs sedan in i Server delen för att aktivera Azure App Service autentisering/auktorisering.  Mer information finns i de detaljerade anvisningarna i självstudien [Lägg till autentisering i appen].

Följande avsnitt beskrivs i det här avsnittet:

* [Klient-hanterad autentisering](#clientflow)
* [Server-hanterad autentisering](#serverflow)
* [Cachelagring av autentiseringstoken](#caching)

### <a name="clientflow"></a>Klient-hanterad autentisering
Din app kan kontakta identitets leverantören oberoende och sedan ange den returnerade token under inloggningen med din server del. Med det här klient flödet kan du tillhandahålla enkel inloggning för användare eller hämta ytterligare användar data från identitets leverantören. Autentisering med klient flöde föredras för att använda ett Server flöde eftersom Identity Provider SDK ger en mer enhetlig känsla och möjliggör ytterligare anpassning.

Exempel finns för följande mönster för autentisering av klient flöde:

* [Active Directory-autentiseringsbibliotek](#adal)
* [Facebook eller Google](#client-facebook)

#### <a name="adal"></a>Autentisera användare med Active Directory-autentiseringsbibliotek
Du kan använda Active Directory-autentiseringsbibliotek (ADAL) för att starta användarautentisering från klienten med Azure Active Directory autentisering.

1. Konfigurera din server del för mobilappen för AAD-inloggning genom att följa själv studie kursen [Så här konfigurerar du App Service för Active Directory inloggning] . Se till att slutföra det valfria steget när du registrerar ett internt klient program.
2. Öppna projektet i Visual Studio eller Xamarin Studio och Lägg till en referens till `Microsoft.IdentityModel.Clients.ActiveDirectory` NuGet-paketet. Inkludera för hands versioner vid sökning.
3. Lägg till följande kod i programmet, enligt den plattform som du använder. I vart och ett ska du göra följande ersättningar:

   * Ersätt **insert-Authority – här** visas namnet på den klient där du etablerade ditt program. Formatet ska vara https://login.microsoftonline.com/contoso.onmicrosoft.com. Det här värdet kan kopieras från fliken domän i Azure Active Directory i [Azure-portalen].
   * Ersätt **insert-Resource-ID – här** med klient-ID: t för Server delen för mobilappen. Du kan hämta klient-ID: t från fliken **Avancerat** under **Azure Active Directory inställningar** i portalen.
   * Ersätt **insert-Client-ID – här** med det klient-ID som du kopierade från det interna klient programmet.
   * Ersätt **insert-Redirect-URI – här** med platsens */.auth/login/Done* -slutpunkt, med hjälp av https-schemat. Värdet bör likna *https://contoso.azurewebsites.net/.auth/login/done* .

     Den kod som krävs för varje plattform följer:

     **Windows:**

     ```csharp
     private MobileServiceUser user;
     private async Task AuthenticateAsync()
     {

        string authority = "INSERT-AUTHORITY-HERE";
        string resourceId = "INSERT-RESOURCE-ID-HERE";
        string clientId = "INSERT-CLIENT-ID-HERE";
        string redirectUri = "INSERT-REDIRECT-URI-HERE";
        while (user == null)
        {
            string message;
            try
            {
                AuthenticationContext ac = new AuthenticationContext(authority);
                AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                    new Uri(redirectUri), new PlatformParameters(PromptBehavior.Auto, false) );
                JObject payload = new JObject();
                payload["access_token"] = ar.AccessToken;
                user = await App.MobileService.LoginAsync(
                    MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
                message = string.Format("You are now logged in - {0}", user.UserId);
            }
            catch (InvalidOperationException)
            {
                message = "You must log in. Login Required";
            }
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
     }
     ```

     **Xamarin.iOS**

     ```csharp
     private MobileServiceUser user;
     private async Task AuthenticateAsync(UIViewController view)
     {

        string authority = "INSERT-AUTHORITY-HERE";
        string resourceId = "INSERT-RESOURCE-ID-HERE";
        string clientId = "INSERT-CLIENT-ID-HERE";
        string redirectUri = "INSERT-REDIRECT-URI-HERE";
        try
        {
            AuthenticationContext ac = new AuthenticationContext(authority);
            AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                new Uri(redirectUri), new PlatformParameters(view));
            JObject payload = new JObject();
            payload["access_token"] = ar.AccessToken;
            user = await client.LoginAsync(
                MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
        }
        catch (Exception ex)
        {
            Console.Error.WriteLine(@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
        }
     }
     ```

     **Xamarin.Android**

     ```csharp
     private MobileServiceUser user;
     private async Task AuthenticateAsync()
     {

        string authority = "INSERT-AUTHORITY-HERE";
        string resourceId = "INSERT-RESOURCE-ID-HERE";
        string clientId = "INSERT-CLIENT-ID-HERE";
        string redirectUri = "INSERT-REDIRECT-URI-HERE";
        try
        {
            AuthenticationContext ac = new AuthenticationContext(authority);
            AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                new Uri(redirectUri), new PlatformParameters(this));
            JObject payload = new JObject();
            payload["access_token"] = ar.AccessToken;
            user = await client.LoginAsync(
                MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
        }
        catch (Exception ex)
        {
            AlertDialog.Builder builder = new AlertDialog.Builder(this);
            builder.SetMessage(ex.Message);
            builder.SetTitle("You must log in. Login Required");
            builder.Create().Show();
        }
     }
     protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
     {

        base.OnActivityResult(requestCode, resultCode, data);
        AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
     }
     ```

#### <a name="client-facebook"></a>Enkel inloggning med en token från Facebook eller Google
Du kan använda klient flödet som det visas i det här kodfragmentet för Facebook eller Google.

```csharp
var token = new JObject();
// Replace access_token_value with actual value of your access token obtained
// using the Facebook or Google SDK.
token.Add("access_token", "access_token_value");

private MobileServiceUser user;
private async Task AuthenticateAsync()
{
    while (user == null)
    {
        string message;
        try
        {
            // Change MobileServiceAuthenticationProvider.Facebook
            // to MobileServiceAuthenticationProvider.Google if using Google auth.
            user = await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
            message = string.Format("You are now logged in - {0}", user.UserId);
        }
        catch (InvalidOperationException)
        {
            message = "You must log in. Login Required";
        }

        var dialog = new MessageDialog(message);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
}
```

### <a name="serverflow"></a>Server-hanterad autentisering
När du har registrerat din identitetsprovider anropar du [LoginAsync] -metoden på [MobileServiceClient] med [MobileServiceAuthenticationProvider] -värdet för providern. Följande kod initierar till exempel en server flödes inloggning genom att använda Facebook.

```csharp
private MobileServiceUser user;
private async System.Threading.Tasks.Task Authenticate()
{
    while (user == null)
    {
        string message;
        try
        {
            user = await client
                .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
            message =
                string.Format("You are now logged in - {0}", user.UserId);
        }
        catch (InvalidOperationException)
        {
            message = "You must log in. Login Required";
        }

        var dialog = new MessageDialog(message);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
}
```

Om du använder en annan identitets leverantör än Facebook ändrar du värdet för [MobileServiceAuthenticationProvider] till värdet för din Provider.

Azure App Service hanterar flödet OAuth-autentisering i ett Server flöde genom att Visa inloggnings sidan för den valda providern.  När identitets leverantören returnerar Azure App Service genererar en token för App Service autentisering. Metoden [LoginAsync] returnerar en [MobileServiceUser], som tillhandahåller både [UserID] för den autentiserade användaren och [MobileServiceAuthenticationToken], som en JSON Web token (JWT). Den här token kan cachelagras och återanvändas tills den upphör att gälla. Mer information finns i [caching Authentication token](#caching).

### <a name="caching"></a>Cachelagring av autentiseringstoken
I vissa fall kan anropet till inloggnings metoden undvikas efter den första lyckade autentiseringen genom att lagra autentiseringstoken från providern.  Microsoft Store-och UWP-appar kan använda [PasswordVault] för att cachelagra aktuell autentiseringstoken efter en lyckad inloggning, enligt följande:

```csharp
await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook);

PasswordVault vault = new PasswordVault();
vault.Add(new PasswordCredential("Facebook", client.currentUser.UserId,
    client.currentUser.MobileServiceAuthenticationToken));
```

UserId-värdet lagras som användar namn för autentiseringsuppgiften och token är lagrat som lösen ord. I efterföljande start fönster kan du kontrol lera **PasswordVault** för cachelagrade autentiseringsuppgifter. I följande exempel används cachelagrade autentiseringsuppgifter när de hittas och försöker på annat sätt autentisera igen med Server delen:

```csharp
// Try to retrieve stored credentials.
var creds = vault.FindAllByResource("Facebook").FirstOrDefault();
if (creds != null)
{
    // Create the current user from the stored credentials.
    client.currentUser = new MobileServiceUser(creds.UserName);
    client.currentUser.MobileServiceAuthenticationToken =
        vault.Retrieve("Facebook", creds.UserName).Password;
}
else
{
    // Regular login flow and cache the token as shown above.
}
```

När du loggar ut en användare måste du också ta bort den lagrade autentiseringsuppgiften enligt följande:

```csharp
client.Logout();
vault.Remove(vault.Retrieve("Facebook", client.currentUser.UserId));
```

Xamarin-appar använder API: erna [Xamarin. auth] för att lagra autentiseringsuppgifter på ett säkert sätt i ett **konto** objekt. Ett exempel på hur du använder dessa API: er finns i [AuthStore.cs] -kod filen i [exempel på ContosoMoments-bilddelning](https://github.com/azure-appservice-samples/ContosoMoments).

När du använder klient-hanterad autentisering kan du också cachelagra den åtkomsttoken som hämtats från leverantören, till exempel Facebook eller Twitter. Denna token kan anges för att begära en ny autentiseringstoken från Server delen, enligt följande:

```csharp
var token = new JObject();
// Replace <your_access_token_value> with actual value of your access token
token.Add("access_token", "<your_access_token_value>");

// Authenticate using the access token.
await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
```

## <a name="pushnotifications"></a>Push-meddelanden
Följande avsnitt beskriver push-meddelanden:

* [Registrera dig för push-meddelanden](#register-for-push)
* [Hämta ett Microsoft Store-paket-SID](#package-sid)
* [Registrera med mallar för fler plattformar](#register-xplat)

### <a name="register-for-push"></a>Gör så här: registrera dig för push-meddelanden
Med Mobile Apps-klienten kan du registrera dig för push-meddelanden med Azure Notification Hubs. När du registrerar dig får du en referens som du får från den plattformsspecifika PNS (Push Notification Service). Du anger detta värde tillsammans med alla Taggar när du skapar registreringen. Följande kod registrerar Windows-appen för push-meddelanden med Windows Notification Service (WNS):

```csharp
private async void InitNotificationsAsync()
{
    // Request a push notification channel.
    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    // Register for notifications using the new channel.
    await MobileService.GetPush().RegisterNativeAsync(channel.Uri, null);
}
```

Om du skickar till WNS måste du [Skaffa ett Microsoft Store-paket-sid](#package-sid).  Mer information om Windows-appar, inklusive hur du registrerar för mall registreringar finns i [Lägg till push-meddelanden i appen].

Det finns inte stöd för att begära taggar från klienten.  Tagg begär Anden tas bort tyst från registreringen.
Om du vill registrera din enhet med Taggar skapar du en anpassad API som använder Notification Hubs API för att utföra registreringen för din räkning.  Anropa det anpassade API: t i stället för metoden `RegisterNativeAsync()`.

### <a name="package-sid"></a>Gör så här: Hämta ett Microsoft Store-paket-SID
Ett paket-SID krävs för att aktivera push-meddelanden i Microsoft Store appar.  Registrera ditt program med Microsoft Store för att ta emot ett paket-SID.

Så här hämtar du det här värdet:

1. Högerklicka på projektet Microsoft Store app i Visual Studio Solution Explorer, klicka på **Store** > **associera appen med butiken..** ..
2. I guiden klickar du på **Nästa**, loggar in med ditt Microsoft-konto, skriver ett namn för din app i **reservera ett nytt**namn på appen och klickar sedan på **reservera**.
3. När appens registrering har skapats väljer du appens namn, klickar på **Nästa**och klickar sedan på **associera**.
4. Logga in på [Windows Dev Center] med ditt Microsoft-konto. Under **Mina appar**klickar du på den app-registrering som du skapade.
5. Klicka på **app management** > **app Identity**och bläddra sedan ned för att hitta **paket-sid**.

Många användnings områden i paket-SID: et behandlar det som en URI, i så fall måste du använda *MS-app://* som schema. Anteckna vilken version av paket-SID som är utformad genom att sammanfoga det här värdet som ett prefix.

Xamarin-appar kräver ytterligare kod för att kunna registrera en app som körs på iOS-eller Android-plattformarna. Mer information finns i avsnittet för din plattform:

* [Xamarin.Android](app-service-mobile-xamarin-android-get-started-push.md#add-push)
* [Xamarin.iOS](app-service-mobile-xamarin-ios-get-started-push.md#add-push-notifications-to-your-app)

### <a name="register-xplat"></a>Gör så här: registrera push-mallar för att skicka meddelanden mellan plattformar
Registrera mallar genom att använda metoden `RegisterAsync()` med mallarna enligt följande:

```csharp
JObject templates = myTemplates();
MobileService.GetPush().RegisterAsync(channel.Uri, templates);
```

Mallarna bör vara `JObject` typer och kan innehålla flera mallar i följande JSON-format:

```csharp
public JObject myTemplates()
{
    // single template for Windows Notification Service toast
    var template = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(message)</text></binding></visual></toast>";

    var templates = new JObject
    {
        ["generic-message"] = new JObject
        {
            ["body"] = template,
            ["headers"] = new JObject
            {
                ["X-WNS-Type"] = "wns/toast"
            },
            ["tags"] = new JArray()
        },
        ["more-templates"] = new JObject {...}
    };
    return templates;
}
```

Metoden **RegisterAsync ()** accepterar också sekundära paneler:

```csharp
MobileService.GetPush().RegisterAsync(string channelUri, JObject templates, JObject secondaryTiles);
```

Alla Taggar rensas bort under registreringen för säkerhet. Om du vill lägga till taggar till installationer eller mallar i installationer, se [arbeta med .NET-Server del Server SDK för Azure Mobile Apps].

Information om hur du skickar meddelanden med hjälp av de här registrerade mallarna finns i [Notification Hubs-API: er].

## <a name="misc"></a>Diverse ämnen
### <a name="errors"></a>Så här gör du: hantera fel
När ett fel uppstår i Server delen genererar klient-SDK en `MobileServiceInvalidOperationException`.  I följande exempel visas hur du hanterar ett undantag som returneras av Server delen:

```csharp
private async void InsertTodoItem(TodoItem todoItem)
{
    // This code inserts a new TodoItem into the database. When the operation completes
    // and App Service has assigned an Id, the item is added to the CollectionView
    try
    {
        await todoTable.InsertAsync(todoItem);
        items.Add(todoItem);
    }
    catch (MobileServiceInvalidOperationException e)
    {
        // Handle error
    }
}
```

Ett annat exempel på hur du hanterar fel villkor finns i exemplet på [Exempel på Mobile Apps-filer]. [LoggingHandler] -exemplet innehåller en hanterare för loggning av ombud för att logga de begär Anden som görs till Server delen.

### <a name="headers"></a>Gör så här: anpassa begärandehuvuden
För att stödja ditt speciella program scenario kan du behöva anpassa kommunikationen med Server delen för mobilappar. Du kanske exempelvis vill lägga till en anpassad rubrik till varje utgående begäran eller till och med ändra svars status koder. Du kan använda en anpassad [DelegatingHandler], som i följande exempel:

```csharp
public async Task CallClientWithHandler()
{
    MobileServiceClient client = new MobileServiceClient("AppUrl", new MyHandler());
    IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
    var newItem = new TodoItem { Text = "Hello world", Complete = false };
    await todoTable.InsertAsync(newItem);
}

public class MyHandler : DelegatingHandler
{
    protected override async Task<HttpResponseMessage>
        SendAsync(HttpRequestMessage request, CancellationToken cancellationToken)
    {
        // Change the request-side here based on the HttpRequestMessage
        request.Headers.Add("x-my-header", "my value");

        // Do the request
        var response = await base.SendAsync(request, cancellationToken);

        // Change the response-side here based on the HttpResponseMessage

        // Return the modified response
        return response;
    }
}
```


<!-- Anchors. -->


<!-- Images. -->

<!-- URLs. -->
[1]: app-service-mobile-windows-store-dotnet-get-started.md
[2]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[3]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[4]: https://msdn.microsoft.com/library/azure/mt419521(v=azure.10).aspx
[5]: https://github.com/Azure-Samples
[6]: https://www.newtonsoft.com/json/help/html/Properties_T_Newtonsoft_Json_JsonPropertyAttribute.htm
[7]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#define-table-controller
[8]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[9]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/
[10]: https://github.com/SymbolSource/SymbolSource
[11]: http://www.symbolsource.org/Public/Wiki/Using
[12]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx

[Lägg till autentisering i appen]: app-service-mobile-windows-store-dotnet-get-started-users.md
[Datasynkronisering offline i Azure Mobile Apps]: app-service-mobile-offline-data-sync.md
[Lägg till push-meddelanden i appen]: app-service-mobile-windows-store-dotnet-get-started-push.md
[Register your app to use a Microsoft account login]: ../app-service/configure-authentication-provider-microsoft.md
[Så här konfigurerar du App Service för Active Directory inloggning]: ../app-service/configure-authentication-provider-aad.md

<!-- Microsoft URLs. -->
[MobileServiceCollection]: https://msdn.microsoft.com/library/azure/dn250636(v=azure.10).aspx
[MobileServiceIncrementalLoadingCollection]: https://msdn.microsoft.com/library/azure/dn268408(v=azure.10).aspx
[MobileServiceAuthenticationProvider]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider(v=azure.10).aspx
[MobileServiceUser]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser(v=azure.10).aspx
[MobileServiceAuthenticationToken]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken(v=azure.10).aspx
[GetTable]: https://msdn.microsoft.com/library/azure/jj554275(v=azure.10).aspx
[skapas en referens till en tabell som inte är av typen]: https://msdn.microsoft.com/library/azure/jj554278(v=azure.10).aspx
[DeleteAsync]: https://msdn.microsoft.com/library/azure/dn296407(v=azure.10).aspx
[IncludeTotalCount]: https://msdn.microsoft.com/library/azure/dn250560(v=azure.10).aspx
[InsertAsync]: https://msdn.microsoft.com/library/azure/dn296400(v=azure.10).aspx
[InvokeApiAsync]: https://msdn.microsoft.com/library/azure/dn268343(v=azure.10).aspx
[LoginAsync]: https://msdn.microsoft.com/library/azure/dn296411(v=azure.10).aspx
[LookupAsync]: https://msdn.microsoft.com/library/azure/jj871654(v=azure.10).aspx
[OrderBy]: https://msdn.microsoft.com/library/azure/dn250572(v=azure.10).aspx
[OrderByDescending]: https://msdn.microsoft.com/library/azure/dn250568(v=azure.10).aspx
[ReadAsync]: https://msdn.microsoft.com/library/azure/mt691741(v=azure.10).aspx
[Gå]: https://msdn.microsoft.com/library/azure/dn250574(v=azure.10).aspx
[Välj]: https://msdn.microsoft.com/library/azure/dn250569(v=azure.10).aspx
[Ignorera]: https://msdn.microsoft.com/library/azure/dn250573(v=azure.10).aspx
[UpdateAsync]: https://msdn.microsoft.com/library/azure/dn250536.(v=azure.10)aspx
[UserID]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid(v=azure.10).aspx
[Vilken]: https://msdn.microsoft.com/library/azure/dn250579(v=azure.10).aspx
[Azure-portalen]: https://portal.azure.com/
[EnableQueryAttribute]: https://msdn.microsoft.com/library/system.web.http.odata.enablequeryattribute.aspx
[GUID. NewGuid]: https://msdn.microsoft.com/library/system.guid.newguid(v=vs.110).aspx
[ISupportIncrementalLoading]: https://msdn.microsoft.com/library/windows/apps/Hh701916.aspx
[Windows Dev Center]: https://dev.windows.com/overview
[DelegatingHandler]: https://msdn.microsoft.com/library/system.net.http.delegatinghandler(v=vs.110).aspx
[PasswordVault]: https://msdn.microsoft.com/library/windows/apps/windows.security.credentials.passwordvault.aspx
[ProtectedData]: https://msdn.microsoft.com/library/system.security.cryptography.protecteddata%28VS.95%29.aspx
[Notification Hubs-API: er]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[Exempel på Mobile Apps-filer]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files
[LoggingHandler]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files/blob/master/src/client/MobileAppsFilesSample/Helpers/LoggingHandler.cs#L63

<!-- External URLs -->
[OData v3-dokumentation]: https://www.odata.org/documentation/odata-version-3-0/
[Fiddler]: https://www.telerik.com/fiddler
[Json.NET]: https://www.newtonsoft.com/json
[Xamarin. auth]: https://components.xamarin.com/view/xamarin.auth/
[AuthStore.cs]: https://github.com/azure-appservice-samples/ContosoMoments
[ContosoMoments photo sharing sample]: https://github.com/azure-appservice-samples/ContosoMoments
