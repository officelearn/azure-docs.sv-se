---
title: Arbeta med hanterade klientbiblioteket för App Service Mobile Apps | Microsoft Docs
description: Lär dig hur du använder .NET-klientbiblioteket för Azure App Service Mobile Apps med Windows- och Xamarin-appar.
services: app-service\mobile
documentationcenter: ''
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 0280785c-e027-4e0d-aaf2-6f155e5a6197
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 09/24/2018
ms.author: crdun
ms.openlocfilehash: 4709d3afce890941689396200347b3212d85159d
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2018
ms.locfileid: "51280868"
---
# <a name="how-to-use-the-managed-client-for-azure-mobile-apps"></a>Så här använder du den hanterade klienten för Azure Mobile Apps
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

## <a name="overview"></a>Översikt
Den här guiden visar hur du utför vanliga scenarier med hanterade-klientbiblioteket för Azure App Service Mobile Apps för Windows- och Xamarin-appar. Om du är nybörjare på Mobile Apps, bör du först slutföra den [Azure Mobile Apps-Snabbstart] [ 1] självstudien. I den här guiden kommer fokusera vi på den hanterade klient-SDK. Läs mer om SDK: er för serversidan för Mobile Apps finns i dokumentationen för den [.NET Server SDK] [ 2] eller [Node.js Server SDK] [ 3].

## <a name="reference-documentation"></a>Referensdokumentation
Referensdokumentation för klient-SDK finns här: [Azure Mobile Apps .NET-referens för klienthantering][4].
Du kan också hitta exempel för flera klienter i den [Azure-Samples GitHub-lagringsplatsen][5].

## <a name="supported-platforms"></a>Plattformar som stöds
.NET-plattformen har stöd för följande plattformar:

* Xamarin Android-versioner för API 19 till 24 (KitKat via Nougat)
* Xamarin iOS-versioner för iOS version 8.0 och senare
* Universell Windows-plattform
* Windows Phone 8.1
* Windows Phone 8.0 förutom Silverlight-program

”Server-flöde” autentiseringen använder en webbvy för uppvisade Användargränssnittet.  Om enheten inte är kunna presentera en WebView UI behövs andra metoder för autentisering.  Denna SDK är därför inte lämpligt för Watch-type eller begränsade enheter på samma sätt.

## <a name="setup"></a>Installation och krav
Vi förutsätter att du redan har skapat och publicerat serverdelsprojektet Mobilapp som innehåller minst en tabell.  I den kod som används i det här avsnittet i tabell heter `TodoItem` och har följande kolumner: `Id`, `Text`, och `Complete`. Den här tabellen är i samma tabell som skapas när du har slutfört den [Azure Mobile Apps-Snabbstart][1].

Typen för motsvarande skrivna på klientsidan i C# är följande klass:

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

Den [JsonPropertyAttribute] [ 6] används för att definiera den *PropertyName* mappning mellan fälten klienten och tabellen.

Läs hur du skapar tabeller i Mobile Apps-serverdel, i den [.NET Server SDK innehåller] [ 7] eller [Node.js Server SDK innehåller][8]. Om du har skapat din mobilappsserverdel i Azure portal med snabbstarten kan du också använda den **enkla tabeller** i den [Azure Portal].

### <a name="how-to-install-the-managed-client-sdk-package"></a>Så här: Installera hanterad klient-SDK-paketet
Använd någon av följande metoder för att installera hanterad klient-SDK-paketet för Mobile Apps från [NuGet][9]:

* **Visual Studio** högerklickar du på projektet, klicka på **hantera NuGet-paket**, Sök efter den `Microsoft.Azure.Mobile.Client` paketet och klicka sedan på **installera**.
* **Xamarin Studio** högerklickar du på projektet, klicka på **Lägg till** > **Lägg till NuGet-paket**, Sök efter den `Microsoft.Azure.Mobile.Client `paketera och klicka sedan på **Lägg till paket** .

Kom ihåg att lägga till följande i filen huvudaktiviteten **med** instruktionen:

```csharp
using Microsoft.WindowsAzure.MobileServices;
```

> [!NOTE]
> Observera att alla supportpaket som det refereras till i ditt Android-projekt måste ha samma version. SDK: N har `Xamarin.Android.Support.CustomTabs` beroende för Android-plattformen, så om projektet använder nyare support paket du behöver installera det här paketet med version som krävs direkt för att undvika konflikter.

### <a name="symbolsource"></a>Så här: arbeta med felsökningssymboler i Visual Studio
Symboler för Microsoft.Azure.Mobile namnområdet är tillgängliga på [SymbolSource][10].  Referera till den [SymbolSource instruktioner] [ 11] att integrera SymbolSource med Visual Studio.

## <a name="create-client"></a>Skapa Mobile Apps-klienten
Följande kod skapar den [MobileServiceClient] [ 12] objekt som används för att få åtkomst till din mobilappsserverdel.

```csharp
var client = new MobileServiceClient("MOBILE_APP_URL");
```

I den föregående koden, Ersätt `MOBILE_APP_URL` med Webbadressen för serverdelen för Mobilappen som finns i bladet för din mobilappsserverdel i den [Azure Portal]. MobileServiceClient-objektet ska vara en singleton.

## <a name="work-with-tables"></a>Arbeta med tabeller
Följande avsnitt beskriver hur du söka efter och hämta poster och ändra data i tabellen.  I följande avsnitt beskrivs:

* [Skapa en tabellreferens](#instantiating)
* [Fråga efter data](#querying)
* [Filtrera returnerade data](#filtering)
* [Sortera returnerade data](#sorting)
* [Returnera data på sidor](#paging)
* [Markera specifika kolumner](#selecting)
* [Leta upp en post efter Id](#lookingup)
* [Hantera ej typbestämd frågor](#untypedqueries)
* [Infoga data](#inserting)
* [Uppdatera data](#updating)
* [Ta bort data](#deleting)
* [Konfliktlösning och Optimistisk samtidighet](#optimisticconcurrency)
* [Bindning till en Windows-användargränssnittet](#binding)
* [Ändra sidstorleken](#pagesize)

### <a name="instantiating"></a>Så här: skapa en tabellreferens
Den kod som har åtkomst till eller ändrar data i en backend-tabell anropar funktioner på den `MobileServiceTable` objekt. Hämta en referens till tabellen genom att anropa den [GetTable] metoden på följande sätt:

```csharp
IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
```

Det returnerade objektet använder modellen skrivna serialisering. En modell för ej typbestämd serialisering stöds också. I följande exempel [skapar en referens till en ej typbestämd tabell]:

```csharp
// Get an untyped table reference
IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");
```

Du måste ange den underliggande OData-frågesträngen i ej typbestämd frågor.

### <a name="querying"></a>Anvisning: fråga efter data från din Mobilapp
Det här avsnittet beskrivs hur du ställa frågor till serverdelen för Mobilappen som innehåller följande funktioner:

* [Filtrera returnerade data](#filtering)
* [Sortera returnerade data](#sorting)
* [Returnera data på sidor](#paging)
* [Markera specifika kolumner](#selecting)
* [Leta upp data efter ID](#lookingup)

> [!NOTE]
> En server-drivna sidstorlek tillämpas för att förhindra att alla rader som returneras.  Växling behåller standard begäranden för stora mängder data från negativt påverka tjänsten.  Du kan returnera fler än 50 rader med de `Skip` och `Take` metod, enligt beskrivningen i [returnerar data på sidor](#paging).

### <a name="filtering"></a>Så här: Filter returnerade data
Följande kod visar hur du kan filtrera data genom att inkludera en `Where` -satsen i en fråga. Den returnerar alla objekt från `todoTable` vars `Complete` egenskapen är lika med `false`. Den [där] funktion gäller en rad som filtrering predikat i frågan mot tabellen.

```csharp
// This query filters out completed TodoItems and items without a timestamp.
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .ToListAsync();
```

Du kan visa URI för begäran som skickats till serverdelen med hjälp av meddelande inspektion programvara, till exempel utvecklarverktyg i webbläsaren eller [Fiddler]. Om du tittar på begärande-URI, Lägg märke till att frågesträngen ändras:

```csharp
GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1
```

Den här OData-begäran översätts till en SQL-fråga genom att Server-SDK:

```csharp
SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0
```

Funktionen som skickas till den `Where` metoden kan ha ett valfritt antal villkor.

```csharp
// This query filters out completed TodoItems where Text isn't null
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false && todoItem.Text != null)
    .ToListAsync();
```

Det här exemplet skulle översättas till en SQL-fråga genom att Server-SDK:

```csharp
SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0
          AND ISNULL(text, 0) = 0
```

Den här frågan kan också delas upp på flera satser:

```csharp
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .Where(todoItem => todoItem.Text != null)
    .ToListAsync();
```

De två metoderna är likvärdiga och utbytbara.  Alternativet tidigare&mdash;sammanfoga flera predikat i en fråga&mdash;blir mer kompakt och rekommenderade.

Den `Where` satsen har stöd för åtgärder som att översättas till OData-delmängd. Åtgärder är bland annat:

* Relationsoperatorer (==,! =, <, < =, >, > =),
* Aritmetiska operatorer (+, -, /, *, %),
* Number precision (Math.Floor, Math.Ceiling)
* Strängfunktioner (längd, delsträngen, Ersätt, IndexOf, StartsWith, EndsWith)
* Datum-egenskaper (år, månad, dag, timme, minut, sekund)
* Komma åt egenskaper för ett objekt, och
* Uttryck som kombinerar någon av dessa åtgärder.

När du överväger Server SDK stöder, kan du överväga att den [OData v3-dokumentation].

### <a name="sorting"></a>Så här: sortera returnerade data
Följande kod visar hur du kan sortera data genom att inkludera en [OrderBy] eller [OrderByDescending] funktion i frågan. Returnerar den objekt från `todoTable` sorterade stigande efter den `Text` fält.

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

### <a name="paging"></a>Anvisning: returnera data i sidor
Som standard returnerar bara de första 50 raderna i serverdelen. Du kan öka antalet returnerade rader genom att anropa den [ta] metod. Använd `Take` tillsammans med den [hoppa över] metod för att begära en specifik ”page” av den totala datamängden som returneras av frågan. Följande fråga när den exekveras, returnerar de tre översta objekten i tabellen.

```csharp
// Define a filtered query that returns the top 3 items.
MobileServiceTableQuery<TodoItem> query = todoTable.Take(3);
List<TodoItem> items = await query.ToListAsync();
```

Följande reviderade fråga hoppar över de tre första resultaten och returnerar följande tre resultaten. Den här frågan producerar andra ”page” av data, där sidstorleken är tre objekt.

```csharp
// Define a filtered query that skips the top 3 items and returns the next 3 items.
MobileServiceTableQuery<TodoItem> query = todoTable.Skip(3).Take(3);
List<TodoItem> items = await query.ToListAsync();
```

Den [IncludeTotalCount] metoden begär det totala antalet för *alla* poster som har returnerats, och ignorerar eventuella sidindelning/limit-sats har angetts:

```csharp
query = query.IncludeTotalCount();
```

I en app i verkligheten, kan du använda frågor som liknar föregående exempel med en personsökare kontroll eller jämförbar Användargränssnittet för att navigera mellan sidor.

> [!NOTE]
> Om du vill åsidosätta gränsen på 50-rader i en mobilappsserverdel, måste du också använda den [EnableQueryAttribute] offentligt GET-metod och ange sidindelning beteende. Vid tillämpningen till metoden anger följande den maximala returnerade rader till 1000:
>
> `[EnableQuery(MaxTop=1000)]`


### <a name="selecting"></a>Så här: Markera specifika kolumner
Du kan ange vilken uppsättning egenskaper som ska inkluderas i resultaten genom att lägga till en [Välj] sats i frågan. Till exempel visar följande kod hur du väljer ett enda fält och hur du väljer och formatera flera fält:

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

Alla funktioner som beskrivits hittills är additiva, så vi kan hålla länkning dem. Varje länkad anrop påverkar flera av frågan. Ytterligare ett exempel:

```csharp
MobileServiceTableQuery<TodoItem> query = todoTable
                .Where(todoItem => todoItem.Complete == false)
                .Select(todoItem => todoItem.Text)
                .Skip(3).
                .Take(3);
List<string> items = await query.ToListAsync();
```

### <a name="lookingup"></a>Så här: Leta upp data efter ID
Den [LookupAsync] funktionen kan användas för att söka efter objekt från databasen med en viss-ID.

```csharp
// This query filters out the item with the ID of 37BBF396-11F0-4B39-85C8-B319C729AF6D
TodoItem item = await todoTable.LookupAsync("37BBF396-11F0-4B39-85C8-B319C729AF6D");
```

### <a name="untypedqueries"></a>Så här: köra ej typbestämd frågor
När du kör en fråga med ett ej typbestämd tabellobjekt, måste du uttryckligen ange OData-frågesträngen genom att anropa [ReadAsync], som i följande exempel:

```csharp
// Lookup untyped data using OData
JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");
```

Du kommer tillbaka JSON-värden som du kan använda som en egenskapsuppsättning. Mer information om JToken och Newtonsoft Json.NET finns i den [Json.NET] plats.

### <a name="inserting"></a>Anvisning: Infoga data i en Mobilapp-serverdel
Alla klienttyper av måste innehålla en medlem med namnet **Id**, vilket är som standard en sträng. Detta **Id** krävs för att utföra CRUD-åtgärder och för offline-synkronisering. Följande kod visar hur du använder den [InsertAsync] metod för att infoga nya rader i en tabell. Parametern innehåller data som ska infogas som ett .NET-objekt.

```csharp
await todoTable.InsertAsync(todoItem);
```

Om ett unikt eget ID-värde inte ingår i den `todoItem` under en insert-, ett GUID genereras av servern.
Du kan hämta den genererade ID: T genom att kontrollera objektet efter att anropet returnerar.

Om du vill infoga värden utan angiven typ data, kan du dra nytta av Json.NET:

```csharp
JObject jo = new JObject();
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.InsertAsync(jo);
```

Här är ett exempel som använder en e-postadress som en unik sträng-id:

```csharp
JObject jo = new JObject();
jo.Add("id", "myemail@emaildomain.com");
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.InsertAsync(jo);
```

### <a name="working-with-id-values"></a>Arbeta med ID-värden
Mobile Apps stöder unika anpassade strängvärden för tabellens **id** kolumn. Ett strängvärde gör programmen kan använda anpassade värden, till exempel e-postadresser eller användarnamn för-ID.  Sträng-ID: N ger dig följande fördelar:

* ID: N skapas utan att göra en tur och RETUR till databasen.
* Poster är lättare att koppla från olika tabeller eller databaser.
* ID-värden kan integrera bättre med logik på ett program.

När ett strängvärde för ID: T inte har angetts på en infogad post genererar serverdelen för Mobilappen ett unikt värde för-ID. Du kan använda den [Guid.NewGuid] metod för att skapa dina egna värden för ID, antingen på klienten eller i serverdelen.

```csharp
JObject jo = new JObject();
jo.Add("id", Guid.NewGuid().ToString("N"));
```

### <a name="modifying"></a>Så här: ändra data i en Mobilapp-serverdel
Följande kod visar hur du använder den [UpdateAsync] metod för att uppdatera en befintlig post med samma ID med den nya informationen. Parametern innehåller data som ska uppdateras som ett .NET-objekt.

```csharp
await todoTable.UpdateAsync(todoItem);
```

Om du vill uppdatera ej typbestämd data, kan du dra nytta av [Json.NET] på följande sätt:

```csharp
JObject jo = new JObject();
jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.UpdateAsync(jo);
```

En `id` fält måste anges när du gör en uppdatering. Serverdelen använder den `id` fält för att identifiera vilka raden som ska uppdateras. Den `id` fältet kan hämtas från resultatet av den `InsertAsync` anropa. En `ArgumentException` uppstår om du försöker uppdatera ett objekt utan att ange den `id` värde.

### <a name="deleting"></a>Så här: ta bort data i en Mobilapp-serverdel
Följande kod visar hur du använder den [DeleteAsync] metod för att ta bort en befintlig instans. Instansen har identifierats av den `id` fältet set på den `todoItem`.

```csharp
await todoTable.DeleteAsync(todoItem);
```

Om du vill ta bort ej typbestämd data, kan du dra nytta av Json.NET på följande sätt:

```csharp
JObject jo = new JObject();
jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
await table.DeleteAsync(jo);
```

När du gör en delete-begäran, måste ett ID anges. Andra egenskaper skickas inte till tjänsten eller ignoreras på tjänsten. Resultatet av en `DeleteAsync` anropet är vanligtvis `null`. ID för att skicka in kan hämtas från resultatet av den `InsertAsync` anropa. En `MobileServiceInvalidOperationException` genereras när du försöker ta bort ett objekt utan att ange den `id` fält.

### <a name="optimisticconcurrency"></a>Så här: Använd Optimistisk samtidighet för konfliktlösning
Två eller fler klienter kan skriva ändringar till samma objekt samtidigt. Utan konfliktidentifiering, skulle den senaste skrivningen skriva över eventuella tidigare uppdateringar. **Optimistisk samtidighetskontroll** förutsätter att varje transaktion åtaganden och därför använder inte någon resurslåsning.  Innan du genomför en transaktion verifierar optimistisk samtidighetskontroll att ingen annan transaktion har ändrat data. Om data har ändrats, återställs committing transaktionen.

Mobile Apps stöder optimistisk samtidighetskontroll genom att spåra ändringar av varje objekt med hjälp av den `version` system egenskapskolumn som har definierats för varje tabell i din mobilappsserverdel. Varje gång som en post uppdateras Mobile Apps anger den `version` egenskapen för den posten till ett nytt värde. Under varje begäran om uppdatering, den `version` egenskapen för den post som ingår i begäran jämförs med samma egenskap för posten på servern. Om versionen skickas med förfrågan matchar inte serverdelen kommer klientbiblioteket genererar en `MobileServicePreconditionFailedException<T>` undantag. Typen som ingår i undantaget är posten från serverdelen som innehåller servrar-versionen av posten. Programmet kan sedan använda den här informationen för att bestämma om du vill köra begäran om uppdatering igen med rätt `version` värdet från serverdelen att genomföra ändringar.

Definiera en kolumn i tabell-klass för de `version` Systemegenskapen att aktivera Optimistisk samtidighet. Exempel:

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

Program med hjälp av ej typbestämd tabeller aktivera Optimistisk samtidighet genom att ange den `Version` flagga den `SystemProperties` i tabellen på följande sätt.

```csharp
//Enable optimistic concurrency by retrieving version
todoTable.SystemProperties |= MobileServiceSystemProperties.Version;
```

Förutom att aktivera Optimistisk samtidighet, måste du även fånga den `MobileServicePreconditionFailedException<T>` undantag i koden när du anropar [UpdateAsync].  Lös konflikten genom att använda rätt `version` till den uppdaterade posten och anropa [UpdateAsync] med lösta posten. Följande kod visar hur du löser en Skriv-konflikt upptäckte en gång:

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

Mer information finns i den [Datasynkronisering offline i Azure Mobile Apps] avsnittet.

### <a name="binding"></a>Så här: Bind Mobile Apps-data till en Windows-användargränssnittet
Det här avsnittet visar hur du vill visa data som returneras objekt med hjälp av UI-element i en Windows-app.  Följande exempelkod Binder till källan för listan med en fråga för ofullständiga objekt. Den [MobileServiceCollection] skapar en Mobile Apps-anpassade bindning-samling.

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

En del kontroller i hanterade runtime stöder ett gränssnitt som kallas [ISupportIncrementalLoading]. Det här gränssnittet kan kontroller för att begära extra data när användaren rullar. Det finns inbyggt stöd för det här gränssnittet för universella Windows-appar via [MobileServiceIncrementalLoadingCollection], som hanterar automatiskt anrop från kontrollerna. Använd `MobileServiceIncrementalLoadingCollection` i Windows-appar på följande sätt:

```csharp
MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
items = todoTable.Where(todoItem => todoItem.Complete == false).ToIncrementalLoadingCollection();

ListBox lb = new ListBox();
lb.ItemsSource = items;
```

Om du vill använda den nya samlingen på Windows Phone 8 och ”Silverlight” appar, använda den `ToCollection` tilläggsmetoder på `IMobileServiceTableQuery<T>` och `IMobileServiceTable<T>`. Läs in data genom att anropa `LoadMoreItemsAsync()`.

```csharp
MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection();
await items.LoadMoreItemsAsync();
```

När du använder den samling som skapats genom att anropa `ToCollectionAsync` eller `ToCollection`, du får en samling som kan vara kopplat till UI-kontroller.  Den här samlingen är sidindelning-medveten.  Eftersom samlingen är läser in data från nätverket, misslyckas läser in ibland. Om du vill hantera sådana fel kan åsidosätta den `OnException` metoden på `MobileServiceIncrementalLoadingCollection` att hantera undantag som härrör från anrop till `LoadMoreItemsAsync`.

Överväg om din tabell har många fält men du bara vill visa en del av dem i din kontroll. Du kan använda riktlinjerna i det föregående avsnittet ”[markera specifika kolumner](#selecting)” att markera specifika kolumner ska visas i Användargränssnittet.

### <a name="pagesize"></a>Ändra sidstorleken
Azure Mobile Apps returnerar högst 50 objekt per begäran som standard.  Du kan ändra växlingsfilens storlek genom att öka den maximala sidstorleken på både klienten och servern.  För att öka den begärda sidstorleken, ange `PullOptions` när du använder `PullAsync()`:

```csharp
PullOptions pullOptions = new PullOptions
    {
        MaxPageSize = 100
    };
```

Om vi antar att du har gjort det `PageSize` lika med eller större än 100 i server, en begäran returnerar upp till 100 objekt.

## <a name="#offlinesync"></a>Arbeta med Offline tabeller
Offline tabeller använder en lokal SQLite att lagra data för användning offline.  Alla tabell åtgärder som utförs mot lokalt SQLite lagra i stället för arkivet för fjärrservern.  Om du vill skapa en offline-tabell, först förbereda projektet:

1. Högerklicka på lösningen i Visual Studio > **hantera NuGet-paket för lösningen...** , Sök sedan efter och installera den **Microsoft.Azure.Mobile.Client.SQLiteStore** NuGet-paketet för alla projekt i lösningen.
2. (Valfritt) Installera ett av följande SQLite runtime-paket för Windows-enheter måste:

   * **Windows 8.1 Runtime:** installera [SQLite för Windows 8.1][3].
   * **Windows Phone 8.1:** installera [SQLite för Windows Phone 8.1][4].
   * **Universal Windows Platform** installera [SQLite för Universal Windows][5].
3. (Valfritt). För Windows-enheter, klickar du på **referenser** > **Lägg till referens...** , expandera den **Windows** mapp > **tillägg**, aktivera sedan lämplig **SQLite för Windows** SDK tillsammans med **Visual C++ 2013 Runtime för Windows** SDK.
    SQLite SDK-namnen kan variera med varje Windows-plattform.

Innan en tabellreferens kan skapas, måste du förbereda det lokala arkivet:

```csharp
var store = new MobileServiceSQLiteStore(Constants.OfflineDbPath);
store.DefineTable<TodoItem>();

//Initializes the SyncContext using the default IMobileServiceSyncHandler.
await this.client.SyncContext.InitializeAsync(store);
```

Store-initieringen görs normalt omedelbart efter att klienten har skapats.  Den **OfflineDbPath** ska vara ett filnamn som är lämplig för användning på alla plattformar som du stöder.  Om sökvägen är en fullständigt kvalificerad sökväg (d.v.s. den börjar med ett snedstreck), och sedan används den.  Om sökvägen inte är fullständigt kvalificerad, placeras filen i en plattformsspecifik plats.

* Standardsökvägen är ”personliga mappen” för iOS och Android-enheter.
* Standardsökvägen är programspecifika ”AppData” mappen för Windows-enheter.

En tabellreferens kan hämtas med hjälp av den `GetSyncTable<>` metoden:

```csharp
var table = client.GetSyncTable<TodoItem>();
```

Du behöver inte autentisera om du vill använda en offline-tabell.  Du behöver bara att autentisera när du kommunicerar med serverdelstjänsten.

### <a name="syncoffline"></a>Synkronisera en Offline-tabell
Offline tabeller är inte synkroniserade med serverdelen som standard.  Synkronisering är uppdelat i två delar.  Du kan skicka ändringar separat från att hämta nya objekt.  Här är en typisk synkroniseringsmetod:

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

Om det första argumentet för `PullAsync` är null, och sedan inte används för inkrementell synkronisering.  Varje synkroniseringsåtgärden hämtar alla poster.

SDK: N som utför en implicit `PushAsync()` innan du hämtar poster.

Konflikt hantering sker på en `PullAsync()` metod.  Du kan hantera konflikter på samma sätt som online tabeller.  Konflikten skapas när `PullAsync()` anropas i stället för under insert, update eller delete. Om flera sådana konflikter uppstår slås de ihop till en enda MobileServicePushFailedException.  Hantera varje fel separat.

## <a name="#customapi"></a>Arbeta med ett anpassat API
Ett anpassat API kan du definiera anpassade slutpunkter som exponerar serverfunktioner som inte mappas till en infoga, uppdatera, ta bort eller Läsåtgärd. Genom att använda ett anpassat API kan har du mer kontroll över meddelanden, inklusive läsning och ange HTTP-huvuden för meddelandet och definiera ett meddelandeformat brödtext än JSON.

Du kan anropa en anpassad API genom att anropa en av de [InvokeApiAsync] metoder på klienten. Till exempel följande kodrad skickar en POST-begäran till den **completeAll** API på serverdelen:

```
var result = await client.InvokeApiAsync<MarkAllResult>("completeAll", System.Net.Http.HttpMethod.Post, null);
```

Det här formuläret är ett skrivna metodanrop och kräver att den **MarkAllResult** returnerar typen har definierats. Både skrivet och ej typbestämd metoder stöds.

Metoden InvokeApiAsync() läggs/api / ”-API: et som du vill anropa såvida inte API: et som börjar med en '/'.
Exempel:

* `InvokeApiAsync("completeAll",...)` anropar /api/completeAll på serverdelen
* `InvokeApiAsync("/.auth/me",...)` anropar /.auth/me på serverdelen

Du kan använda InvokeApiAsync för att anropa alla WebAPI, inklusive de WebAPIs som inte har definierats med Azure Mobile Apps.  När du använder InvokeApiAsync() skickas lämpliga rubriker, inklusive autentiseringshuvuden, med begäran.

## <a name="authentication"></a>Autentisera användare
Mobile Apps stöder autentisering och auktorisering av appanvändare som använder olika externa indentitetsprovidrar: Facebook, Google, Account, Twitter och Azure Active Directory. Du kan ange behörigheter för tabeller för att begränsa åtkomst för specifika åtgärder endast autentiserade användare. Du kan också använda identiteten för autentiserade användare för att implementera auktoriseringsregler i server-skript. Mer information finns i självstudierna [Lägg till autentisering i appen].

Två autentiseringsflöden stöds: *hanteras med klientprogram* och *server-hanterad* flöde. Server-hanterad flödet innehåller enklaste autentiseringsupplevelse som den är beroende av leverantörens Webbgränssnitt för autentisering. Flödet hanteras med klientprogram möjliggör djupare integrering med specifika funktioner som den är beroende av provider-specifik enhetsspecifika SDK: er.

> [!NOTE]
> Vi rekommenderar att du använder ett flöde som hanteras med klientprogram i dina produktionsappar.

Om du vill konfigurera autentisering måste du registrera din app med en eller flera Identitetsproviders.  Identitetsprovidern genererar ett klient-ID och en klienthemlighet för din app.  Dessa värden anges sedan i serverdelen för att aktivera autentisering/auktorisering i Azure App Service.  Mer information följer detaljerade instruktioner i självstudiekursen [Lägg till autentisering i appen].

I följande avsnitt beskrivs i det här avsnittet:

* [Hanteras med klientprogram autentisering](#clientflow)
* [Server-hanterad autentisering](#serverflow)
* [Cachelagring autentiseringstoken](#caching)

### <a name="clientflow"></a>Hanteras med klientprogram autentisering
Din app kan oberoende kontakta identitetsprovidern och sedan tillhandahålla den returnerade token under inloggningen med serverdelen. Det här klientflödet gör det möjligt för dig att tillhandahålla en enkel inloggning för användare eller för att hämta ytterligare användardata från identitetsprovidern. Flow klientautentisering är att föredra att använda ett server-flöde som identitetsprovider SDK tillhandahåller en mer interna UX-design och möjliggör ytterligare anpassning.

Exempel finns endast för följande klientflödet autentiseringsmönster:

* [Active Directory Authentication Library](#adal)
* [Facebook eller Google](#client-facebook)

#### <a name="adal"></a>Autentisera användare med Active Directory Authentication Library
Du kan använda Active Directory Authentication Library (ADAL) för att initiera användarautentisering från klienten med hjälp av Azure Active Directory-autentisering.

1. Konfigurera mobilappsserverdelen för inloggning AAD genom att följa den [så här konfigurerar du App Service för Active Directory-inloggning] självstudien. Se till att slutföra det valfria steget med att registrera ett internt klientprogram.
2. Öppna projektet i Visual Studio eller Xamarin Studio och Lägg till en referens till den `Microsoft.IdentityModel.CLients.ActiveDirectory` NuGet-paketet. När du söker kan innehålla förhandsversioner.
3. Lägg till följande kod i ditt program, beroende på plattform som du använder. I varje, gör du följande ersättningar:

   * Ersätt **INSERT-UTFÄRDARE-HERE** med namnet på den klient som du har etablerat för ditt program. Formatet ska vara https://login.microsoftonline.com/contoso.onmicrosoft.com. Det här värdet kan kopieras från fliken domän i Azure Active Directory i den [Azure Portal].
   * Ersätt **INSERT-resurs-ID-HERE** med klient-ID för din mobilappsserverdel. Du kan hämta klient-ID från den **Avancerat** fliken **Azure Active Directory-inställningar** i portalen.
   * Ersätt **INSERT-klient-ID-HERE** med klient-ID som du kopierade från native client-program.
   * Ersätt **INSERT-OMDIRIGERINGS-URI-HERE** med webbplatsens */.auth/login/done* slutpunkten, med hjälp av HTTPS-schema. Det här värdet ska vara liknar *https://contoso.azurewebsites.net/.auth/login/done*.

     Den kod som behövs för varje plattform som följer:

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

#### <a name="client-facebook"></a>Enkel inloggning med hjälp av en token från Facebook eller Google
Du kan använda klientflödet enligt det här kodfragmentet för Facebook eller Google.

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
När du har registrerat din identitetsprovider anropar den [LoginAsync] metoden på [MobileServiceClient] med den [MobileServiceAuthenticationProvider] värdet på din provider. Till exempel initierar följande kod en server flow logga in med hjälp av Facebook.

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

Om du använder en identitetsprovider än Facebook, ändra värdet för [MobileServiceAuthenticationProvider] till värdet för din provider.

I ett server-flöde hanterar flödet för OAuth-autentisering i Azure App Service genom att visa inloggningssidan för den valda providern.  När den identity providern returnerar, Azure App Service genererar en App Service-autentiseringstoken. Den [LoginAsync] metoden returnerar en [MobileServiceUser], som erbjuder både den [Användar-ID] på den autentiserade användaren och [MobileServiceAuthenticationToken], som en JSON-webbtoken (JWT). Den här token kan cachelagras och återanvändas tills den upphör att gälla. Mer information finns i [cachelagring autentiseringstoken](#caching).

### <a name="caching"></a>Cachelagring autentiseringstoken
I vissa fall kan anrop till metoden inloggningen undvikas efter den första autentiseringen genom att lagra autentiseringstoken från providern.  Microsoft Store- och UWP-appar kan använda [PasswordVault] cachelagra aktuella autentiseringstoken efter en lyckad inloggning, enligt följande:

```csharp
await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook);

PasswordVault vault = new PasswordVault();
vault.Add(new PasswordCredential("Facebook", client.currentUser.UserId,
    client.currentUser.MobileServiceAuthenticationToken));
```

Användar-ID-värdet lagras som användarnamn för autentiseringsuppgift och token som är lagrade som lösenord. I efterföljande nystartade företag, kan du kontrollera den **PasswordVault** för cachelagrade autentiseringsuppgifter. I följande exempel används cachelagrade autentiseringsuppgifter när de hittas och annars försöker autentisera igen med serverdel:

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

När du loggar ut en användare kan du måste också ta bort lagrade autentiseringsuppgifter, enligt följande:

```csharp
client.Logout();
vault.Remove(vault.Retrieve("Facebook", client.currentUser.UserId));
```

Xamarin-appar används i [Xamarin.Auth] API: er på ett säkert sätt lagra autentiseringsuppgifter i en **konto** objekt. Ett exempel på hur du använder dessa API: er finns i den [AuthStore.cs] kodfilen i den [ContosoMoments foto delning exempel](https://github.com/azure-appservice-samples/ContosoMoments).

När du använder hanteras med klientprogram autentisering kan du också cachelagra åtkomsttoken som erhållits från leverantören, till exempel Facebook eller Twitter. Denna token kan anges för att begära en ny autentiseringstoken från serverdelen, enligt följande:

```csharp
var token = new JObject();
// Replace <your_access_token_value> with actual value of your access token
token.Add("access_token", "<your_access_token_value>");

// Authenticate using the access token.
await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
```

## <a name="pushnotifications"></a>Push-meddelanden
Följande avsnitt ger Push-meddelanden:

* [Registrera dig för Push-meddelanden](#register-for-push)
* [Hämta ett paket för Microsoft Store-SID](#package-sid)
* [Registrera med plattformsoberoende mallar](#register-xplat)

### <a name="register-for-push"></a>Så här: registrera dig för Push-meddelanden
Mobile Apps-klienten kan du registrera dig för push-meddelanden med Azure Notification Hubs. Vid registreringen kan du hämta en referens som hämtas från plattformsspecifika Push Notification Service (PNS). Du kan sedan ange det här värdet tillsammans med alla taggar när du skapar registreringen. Följande kod registrerar din Windows-app för push-meddelanden med Windows Notification Service (WNS):

```csharp
private async void InitNotificationsAsync()
{
    // Request a push notification channel.
    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    // Register for notifications using the new channel.
    await MobileService.GetPush().RegisterNativeAsync(channel.Uri, null);
}
```

Om du skickar i WNS, så måste du [hämtar ett paket för Microsoft Store SID](#package-sid).  Mer information om Windows-appar, inklusive hur du registrerar dig för mallen registreringar finns [Lägg till push-meddelanden i appen].

Begär taggar från klienten stöds inte.  Taggen begäranden ignoreras tyst från registrering.
Om du vill registrera din enhet med taggar kan du skapa ett anpassat API som använder API: et för Notification Hubs för att utföra registreringen på din räkning.  [Anropa anpassade API: et](#customapi) i stället för den `RegisterNativeAsync()` metoden.

### <a name="package-sid"></a>Så här: skaffa en Microsoft Store-paket-SID
En paket-SID krävs för att aktivera push-meddelanden i Microsoft Store-appar.  Registrera ditt program med Microsoft Store för att ta emot en paket-SID.

Du kan hämta det här värdet:

1. Högerklicka på Microsoft Store-app-projekt i Visual Studio Solution Explorer, klicka på **Store** > **associera App med Store...** .
2. I guiden klickar du på **nästa**, logga in med ditt Microsoft-konto, ange ett namn för din app i **reservera ett nytt appnamn**, klicka sedan på **reservera**.
3. När appregistreringen har skapats, Välj namnet på, klicka på **nästa**, och klicka sedan på **associera**.
4. Logga in på den [Windows Dev Center] med ditt Microsoft-Account. Under **Mina appar**, klickar du på appregistreringen som du skapade.
5. Klicka på **apphantering** > **App identitet**, och rulla ned till hitta din **paket-SID**.

Många användningsområden för paket-SID behandlar det som en URI, i vilket fall måste du använda *ms-app: / /* som schema. Anteckna versionen av ditt paket-SID som skapats på rätt sätt genom att sammanfoga det här värdet som ett prefix.

Xamarin-appar som kräver ytterligare kod för att kunna registrera en app som körs på iOS eller Android-plattformar. Mer information finns i avsnittet för din plattform:

* [Xamarin.Android](app-service-mobile-xamarin-android-get-started-push.md#add-push)
* [Xamarin.iOS](app-service-mobile-xamarin-ios-get-started-push.md#add-push-notifications-to-your-app)

### <a name="register-xplat"></a>Så här: registrera push-mallar för att skicka plattformsoberoende meddelanden
Registrera mallar genom att använda den `RegisterAsync()` metoden med mallarna på följande sätt:

```csharp
JObject templates = myTemplates();
MobileService.GetPush().RegisterAsync(channel.Uri, templates);
```

Dina mallar ska vara `JObject` datatyper och kan innehålla flera mallar i följande JSON-format:

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

Metoden **RegisterAsync()** sekundära paneler kan också användas:

```csharp
MobileService.GetPush().RegisterAsync(string channelUri, JObject templates, JObject secondaryTiles);
```

Alla taggar tas bort direkt under registreringen för säkerhet. Om du vill lägga till taggar i installationer eller mallar i installationer finns i [arbeta med SDK för .NET-serverdelen för Azure Mobile Apps].

Om du vill skicka meddelanden via dessa registrerade mallar som avser den [Notification Hubs API: er].

## <a name="misc"></a>Övriga ämnen
### <a name="errors"></a>Så här: hantera fel
När ett fel uppstår i serverdelen, klienten SDK aktiverar en `MobileServiceInvalidOperationException`.  I följande exempel visas hur du hanterar ett undantag som returneras av serverdelen:

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

Ett annat exempel för att behandla felvillkor kan hittas i den [Exempel för Mobile Apps-filer]. Den [LoggingHandler] exempel innehåller en loggning ombud hanterare för att logga begäranden som görs till serverdelen.

### <a name="headers"></a>Så här: anpassa begärandehuvuden
Du kan behöva anpassa kommunikation med serverdelen för Mobilappen för att stödja ditt scenario specifik app. Du kanske vill lägga till en anpassad rubrik i varje utgående begäran eller t.o.m. Ändra statuskoder för svar. Du kan använda en anpassad [DelegatingHandler], som i följande exempel:

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
[6]: http://www.newtonsoft.com/json/help/html/Properties_T_Newtonsoft_Json_JsonPropertyAttribute.htm
[7]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#define-table-controller
[8]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[9]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/
[10]: http://www.symbolsource.org/
[11]: http://www.symbolsource.org/Public/Wiki/Using
[12]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx

[Lägg till autentisering i appen]: app-service-mobile-windows-store-dotnet-get-started-users.md
[Datasynkronisering offline i Azure Mobile Apps]: app-service-mobile-offline-data-sync.md
[Lägg till push-meddelanden i appen]: app-service-mobile-windows-store-dotnet-get-started-push.md
[Register your app to use a Microsoft account login]: ../app-service/app-service-mobile-how-to-configure-microsoft-authentication.md
[Så här konfigurerar du App Service för Active Directory-inloggning]: ../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md

<!-- Microsoft URLs. -->
[MobileServiceCollection]: https://msdn.microsoft.com/library/azure/dn250636(v=azure.10).aspx
[MobileServiceIncrementalLoadingCollection]: https://msdn.microsoft.com/library/azure/dn268408(v=azure.10).aspx
[MobileServiceAuthenticationProvider]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider(v=azure.10).aspx
[MobileServiceUser]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser(v=azure.10).aspx
[MobileServiceAuthenticationToken]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken(v=azure.10).aspx
[GetTable]: https://msdn.microsoft.com/library/azure/jj554275(v=azure.10).aspx
[skapar en referens till en ej typbestämd tabell]: https://msdn.microsoft.com/library/azure/jj554278(v=azure.10).aspx
[DeleteAsync]: https://msdn.microsoft.com/library/azure/dn296407(v=azure.10).aspx
[IncludeTotalCount]: https://msdn.microsoft.com/library/azure/dn250560(v=azure.10).aspx
[InsertAsync]: https://msdn.microsoft.com/library/azure/dn296400(v=azure.10).aspx
[InvokeApiAsync]: https://msdn.microsoft.com/library/azure/dn268343(v=azure.10).aspx
[LoginAsync]: https://msdn.microsoft.com/library/azure/dn296411(v=azure.10).aspx
[LookupAsync]: https://msdn.microsoft.com/library/azure/jj871654(v=azure.10).aspx
[OrderBy]: https://msdn.microsoft.com/library/azure/dn250572(v=azure.10).aspx
[OrderByDescending]: https://msdn.microsoft.com/library/azure/dn250568(v=azure.10).aspx
[ReadAsync]: https://msdn.microsoft.com/library/azure/mt691741(v=azure.10).aspx
[ta]: https://msdn.microsoft.com/library/azure/dn250574(v=azure.10).aspx
[Välj]: https://msdn.microsoft.com/library/azure/dn250569(v=azure.10).aspx
[Hoppa över]: https://msdn.microsoft.com/library/azure/dn250573(v=azure.10).aspx
[UpdateAsync]: https://msdn.microsoft.com/library/azure/dn250536.(v=azure.10)aspx
[Användar-ID]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid(v=azure.10).aspx
[där]: https://msdn.microsoft.com/library/azure/dn250579(v=azure.10).aspx
[Azure Portal]: https://portal.azure.com/
[EnableQueryAttribute]: https://msdn.microsoft.com/library/system.web.http.odata.enablequeryattribute.aspx
[Guid.NewGuid]: https://msdn.microsoft.com/library/system.guid.newguid(v=vs.110).aspx
[ISupportIncrementalLoading]: http://msdn.microsoft.com/library/windows/apps/Hh701916.aspx
[Windows Dev Center]: https://dev.windows.com/overview
[DelegatingHandler]: https://msdn.microsoft.com/library/system.net.http.delegatinghandler(v=vs.110).aspx
[PasswordVault]: http://msdn.microsoft.com/library/windows/apps/windows.security.credentials.passwordvault.aspx
[ProtectedData]: http://msdn.microsoft.com/library/system.security.cryptography.protecteddata%28VS.95%29.aspx
[Notification Hubs API: er]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[Exempel för Mobile Apps-filer]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files
[LoggingHandler]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files/blob/master/src/client/MobileAppsFilesSample/Helpers/LoggingHandler.cs#L63

<!-- External URLs -->
[OData v3-dokumentation]: http://www.odata.org/documentation/odata-version-3-0/
[Fiddler]: http://www.telerik.com/fiddler
[Json.NET]: http://www.newtonsoft.com/json
[Xamarin.Auth]: https://components.xamarin.com/view/xamarin.auth/
[AuthStore.cs]: https://github.com/azure-appservice-samples/ContosoMoments
[ContosoMoments photo sharing sample]: https://github.com/azure-appservice-samples/ContosoMoments
