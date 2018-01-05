---
title: "Arbeta med Apptjänst Mobilappar hanterade klientbiblioteket (Windows | Microsoft Docs"
description: "Lär dig hur du använder en .NET-klient för Azure Apptjänst Mobilappar med Windows och Xamarin-appar."
services: app-service\mobile
documentationcenter: 
author: conceptdev
manager: crdun
editor: 
ms.assetid: 0280785c-e027-4e0d-aaf2-6f155e5a6197
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 01/04/2017
ms.author: crdun
ms.openlocfilehash: a92fc21881375989f4ebd192c2c42e419e7aee59
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2018
---
# <a name="how-to-use-the-managed-client-for-azure-mobile-apps"></a>Så här använder du den hanterade klienten för Azure Mobile Apps
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

## <a name="overview"></a>Översikt
Den här guiden visar hur du utför vanliga scenarier med hjälp av hanterade klientbiblioteket för Azure App Service mobila appar för Windows och Xamarin-appar. Om du är nybörjare på Mobile Apps bör du först slutföra den [Azure Mobile Apps quickstart] [ 1] kursen. I den här guiden fokuserar på klientsidan hanterade SDK. Mer information om SDK: er för serversidan för Mobilappar, finns i dokumentationen för den [SDK för .NET Server] [ 2] eller [SDK för Node.js Server] [ 3].

## <a name="reference-documentation"></a>Referensdokumentation
I referensdokumentationen för klient-SDK finns här: [Azure Mobile Apps .NET klienten referens][4].
Du kan också hitta flera klienten exempel i den [Azure-exempel GitHub-lagringsplatsen][5].

## <a name="supported-platforms"></a>Plattformar som stöds
.NET-plattformen har stöd för följande plattformar:

* Xamarin Android-versioner för API-19 till 24 (KitKat via Nougat)
* Xamarin iOS-versioner för iOS version 8.0 och senare
* Universella Windows-plattformen
* Windows Phone 8.1
* Windows Phone 8.0 förutom Silverlight-program

”Server flöde” autentisering använder en webbvy för presenterades Användargränssnittet.  Om enheten inte är kunna presentera en WebView UI behövs andra metoder för autentisering.  Detta SDK lämpar sig därför inte för titta på typen eller begränsade enheter på samma sätt.

## <a name="setup"></a>Installationen och förutsättningar
Vi förutsätter att du redan har skapat och publicerat serverdelsprojektet Mobilapp som innehåller minst en tabell.  I den kod som används i det här avsnittet tabellen med namnet `TodoItem` och har följande kolumner: `Id`, `Text`, och `Complete`. Den här tabellen är i samma tabell som skapas när du har slutfört den [Azure Mobile Apps quickstart][1].

Motsvarande skrivna klientsidan typ i C# är följande:

```
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

Information om hur du skapar tabeller i Mobile Apps-serverdel finns i [SDK för .NET Server avsnittet] [ 7] eller [SDK för Node.js Server avsnittet][8]. Om du har skapat din mobilappsserverdel i Azure portal med Snabbstart, kan du också använda den **enkelt tabeller** i den [Azure-portalen].

### <a name="how-to-install-the-managed-client-sdk-package"></a>Så här: Installera hanterad klient-SDK-paketet
Använd någon av följande metoder för att installera hanterad klient-SDK-paketet för Mobile Apps från [NuGet][9]:

* **Visual Studio** Högerklicka på ditt projekt, klickar du på **hantera NuGet-paket**, söka efter den `Microsoft.Azure.Mobile.Client` paketet och klicka sedan på **installera**.
* **Xamarin Studio** Högerklicka på ditt projekt, klickar du på **Lägg till** > **Lägg till NuGet-paket**, söka efter den `Microsoft.Azure.Mobile.Client `paketet och klickar sedan på **Lägg till Paketet**.

Kom ihåg att lägga till följande i filen huvudaktiviteten **med** instruktionen:

```
using Microsoft.WindowsAzure.MobileServices;
```

### <a name="symbolsource"></a>Så här: arbeta med symboler för felsökning i Visual Studio
Symboler för Microsoft.Azure.Mobile namnområdet är tillgängliga på [SymbolSource][10].  Referera till den [SymbolSource instruktioner] [ 11] att integrera SymbolSource med Visual Studio.

## <a name="create-client"></a>Skapa Mobile Apps-klient
Följande kod skapar den [MobileServiceClient] [ 12] objekt som används för åtkomst till din mobilappsserverdel.

```
var client = new MobileServiceClient("MOBILE_APP_URL");
```

I föregående kod ersätter `MOBILE_APP_URL` med URL: en för serverdelen för Mobilappen som hittas i bladet för din mobilappsserverdel i den [Azure-portalen]. MobileServiceClient-objektet ska vara en singleton.

## <a name="work-with-tables"></a>Arbeta med tabeller
Följande avsnitt beskriver hur du söka efter och hämta poster och ändra data i tabellen.  I följande avsnitt beskrivs:

* [Skapa en tabellreferens](#instantiating)
* [Fråga efter data](#querying)
* [Filtret returnerade data](#filtering)
* [Sortera returnerade data](#sorting)
* [Returnera data på sidor](#paging)
* [Markera specifika kolumner](#selecting)
* [Leta upp en post-ID: t](#lookingup)
* [Hantera ej typbestämd frågor](#untypedqueries)
* [Infoga data](#inserting)
* [Uppdatera data](#updating)
* [Ta bort data](#deleting)
* [Konfliktlösning och Optimistisk samtidighet](#optimisticconcurrency)
* [Bindning till en Windows-gränssnittet](#binding)
* [Ändrar storlek på sidan](#pagesize)

### <a name="instantiating"></a>Så här: skapa en tabellreferens
Den kod som har åtkomst till eller ändrar data i en backend-tabell anropar funktioner på den `MobileServiceTable` objekt. Hämta en referens till tabellen genom att anropa den [GetTable] metoden på följande sätt:

```
IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
```

Det returnerade objektet använder skrivna serialisering modellen. En modell för ej typbestämd serialisering stöds också. I följande exempel [skapar en referens till en ej typbestämd tabell]:

```
// Get an untyped table reference
IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");
```

Du måste ange den underliggande OData-frågesträngen i ej typbestämd frågor.

### <a name="querying"></a>Så här: fråga efter data från din Mobilapp
Det här avsnittet beskrivs hur du utfärda frågor till serverdelen för Mobilappen som innehåller följande funktioner:

* [Filtret returnerade data](#filtering)
* [Sortera returnerade data](#sorting)
* [Returnera data på sidor](#paging)
* [Markera specifika kolumner](#selecting)
* [Leta upp data-ID: t](#lookingup)

> [!NOTE]
> En server-driven sidstorlek tillämpas för att förhindra att alla rader som returneras.  Sidindelning håller standard begäranden för stora datamängder från negativt påverka tjänsten.  Om du vill returnera fler än 50 rader, Använd den `Skip` och `Take` metod, enligt beskrivningen i [returnerar data på sidor](#paging).

### <a name="filtering"></a>Så här: filtret returnerade data
Följande kod visar hur du kan filtrera data genom att inkludera en `Where` -satsen i en fråga. Returnerar alla objekt från `todoTable` vars `Complete` -egenskapen är lika med `false`. Den [där] funktion gäller en rad filtrering predikat i frågan mot tabellen.

```
// This query filters out completed TodoItems and items without a timestamp.
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .ToListAsync();
```

Du kan visa URI för begäran som skickades till serverdelen med hjälp av meddelandet inspektion programvara, till exempel webbläsare utvecklingsverktyg eller [Fiddler]. Om du tittar på URI-begäran att märka att frågesträngen ändras:

```
GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1
```

Denna OData-begäran översätts till en SQL-fråga av Server-SDK:

```
SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0
```

Funktionen som skickas till den `Where` metoden kan ha ett godtyckligt antal villkor.

```
// This query filters out completed TodoItems where Text isn't null
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false && todoItem.Text != null)
    .ToListAsync();
```

Det här exemplet kan översättas till en SQL-fråga av Server-SDK:

```
SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0
          AND ISNULL(text, 0) = 0
```

Den här frågan kan också delas upp i flera villkor:

```
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .Where(todoItem => todoItem.Text != null)
    .ToListAsync();
```

De två metoderna är likvärdiga och utbytbara.  Alternativet tidigare&mdash;sammanfoga flera predikat i en fråga&mdash;blir mer kompakt och rekommenderade.

Den `Where` -satsen stöder åtgärder som kan översättas till OData-delmängden. Åtgärder är:

* Relationsoperatorer (==,! =, <, < =, >, > =),
* Aritmetiska operatorer (+, -, /, *, %),
* Number precision (Math.Floor, Math.Ceiling)
* Strängfunktioner (längd, delsträngen, Ersätt, IndexOf, StartsWith, EndsWith)
* Egenskaper för datum (år, månad, dag, timme, minut, sekund)
* Komma åt egenskaper för ett objekt, och
* Uttryck kombinera någon av dessa åtgärder.

När du överväger Server SDK stöder, kan du den [OData v3 dokumentationen].

### <a name="sorting"></a>Så här: sortera returnerade data
Följande kod visar hur du kan sortera data genom att inkludera en [OrderBy] eller [OrderByDescending] funktion i frågan. Returnerar objekten från `todoTable` Sortera stigande efter den `Text` fältet.

```
// Sort items in ascending order by Text field
MobileServiceTableQuery<TodoItem> query = todoTable
                .OrderBy(todoItem => todoItem.Text)
List<TodoItem> items = await query.ToListAsync();

// Sort items in descending order by Text field
MobileServiceTableQuery<TodoItem> query = todoTable
                .OrderByDescending(todoItem => todoItem.Text)
List<TodoItem> items = await query.ToListAsync();
```

### <a name="paging"></a>Så här: returnerar data på sidor
Som standard returnerar endast de första 50 raderna i serverdelen. Du kan öka antalet rader som returneras genom att anropa den [ta] metod. Använd `Take` tillsammans med den [hoppa över] metod för att begära en specifik ”page” på den totala datamängden som returneras av frågan. Följande fråga när den exekveras, returnerar de översta tre objekten i tabellen.

```
// Define a filtered query that returns the top 3 items.
MobileServiceTableQuery<TodoItem> query = todoTable.Take(3);
List<TodoItem> items = await query.ToListAsync();
```

Följande reviderade fråga hoppar över de tre första resultaten och returnerar följande tre resultaten. Den här frågan ger andra ”sidan” data, där sidstorleken är tre objekt.

```
// Define a filtered query that skips the top 3 items and returns the next 3 items.
MobileServiceTableQuery<TodoItem> query = todoTable.Skip(3).Take(3);
List<TodoItem> items = await query.ToListAsync();
```

Den [IncludeTotalCount] metoden begär det totala antalet för *alla* poster som har returnerats, ignoreras eventuella sidindelning/gräns-sats har angetts:

```
query = query.IncludeTotalCount();
```

I verkligheten app, kan du använda frågor liknar föregående exempel med en personsökare kontroll eller jämförbar UI för att navigera mellan sidor.

> [!NOTE]
> Om du vill åsidosätta 50-rad-gränsen i en mobilappsserverdel, måste du också använda den [EnableQueryAttribute] för allmänheten GET-metod och ange beteendet sidindelning. När det används metoden, anger följande den maximala returnerade rader till 1000:
>
> `[EnableQuery(MaxTop=1000)]`


### <a name="selecting"></a>Så här: Markera specifika kolumner
Du kan ange vilken uppsättning egenskaper som ska inkluderas i resultaten genom att lägga till en [Välj] sats i frågan. Följande kod visar till exempel hur du väljer ett enda fält och markera och formatera flera fält:

```
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

Alla funktioner som beskrivs hittills är tillsatsen, så vi kan hålla länkning dem. Varje länkad anrop påverkar flera av frågan. Ytterligare ett exempel:

```
MobileServiceTableQuery<TodoItem> query = todoTable
                .Where(todoItem => todoItem.Complete == false)
                .Select(todoItem => todoItem.Text)
                .Skip(3).
                .Take(3);
List<string> items = await query.ToListAsync();
```

### <a name="lookingup"></a>Så här: Leta upp data-ID: t
Den [LookupAsync] funktionen kan användas för att söka efter objekt från databasen med en viss-ID.

```
// This query filters out the item with the ID of 37BBF396-11F0-4B39-85C8-B319C729AF6D
TodoItem item = await todoTable.LookupAsync("37BBF396-11F0-4B39-85C8-B319C729AF6D");
```

### <a name="untypedqueries"></a>Så här: köra någon frågor
När du kör en fråga med ett argument tabellobjekt, måste du uttryckligen ange frågesträngen OData genom att anropa [ReadAsync], som i följande exempel:

```
// Lookup untyped data using OData
JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");
```

Åter JSON-värden som du kan använda som en egenskapsuppsättning. Mer information om JToken och Newtonsoft Json.NET finns i [Json.NET] plats.

### <a name="inserting"></a>Så här: Infoga data i en mobilappsserverdel
Alla klienttyper av måste innehålla en medlem med namnet **Id**, vilket är som standard en sträng. Detta **Id** krävs för att utföra CRUD-åtgärder och för offlinesynkronisering. Följande kod visar hur du använder den [InsertAsync] metod för att infoga nya rader i en tabell. Parametern innehåller data som ska läggas till som ett .NET-objekt.

```
await todoTable.InsertAsync(todoItem);
```

Om ett unikt värde för anpassad ID inte ingår i den `todoItem` under en insert-, ett GUID som genereras av servern.
Du kan hämta den genererade Id genom att inspektera objektet när anropet returnerar.

Om du vill infoga någon data, kan du dra nytta av Json.NET:

```
JObject jo = new JObject();
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.InsertAsync(jo);
```

Här är ett exempel som använder en e-postadress som en unik sträng-id:

```
JObject jo = new JObject();
jo.Add("id", "myemail@emaildomain.com");
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.InsertAsync(jo);
```

### <a name="working-with-id-values"></a>Arbeta med ID-värden
Mobile Apps stöder unika anpassade strängvärden för tabellens **id** kolumn. Ett strängvärde som gör att program kan använda anpassade värden, till exempel e-postadresser eller användarnamn för-ID.  Sträng-ID: N ge följande fördelar:

* ID: N skapas utan att göra onödig kommunikation till databasen.
* Poster är enklare att koppla från olika tabeller eller databaser.
* ID-värden kan integrera bättre med en programlogik.

När ett strängvärde ID inte har angetts på en infogad post genererar serverdelen för Mobilappen ett unikt värde för-ID. Du kan använda den [Guid.NewGuid] metod för att generera en egen ID-värden på klienten eller i serverdelen.

```
JObject jo = new JObject();
jo.Add("id", Guid.NewGuid().ToString("N"));
```

### <a name="modifying"></a>Så här: ändra data i en mobilappsserverdel
Följande kod visar hur du använder den [UpdateAsync] metod för att uppdatera en befintlig post med samma ID med ny information. Parametern innehåller data som ska uppdateras som ett .NET-objekt.

```
await todoTable.UpdateAsync(todoItem);
```

Om du vill uppdatera ej typbestämd data, kan du dra nytta av [Json.NET] på följande sätt:

```
JObject jo = new JObject();
jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.UpdateAsync(jo);
```

En `id` fält måste anges när du gör en uppdatering. Serverdelen använder den `id` fältet för att identifiera vilken rad att uppdatera. Den `id` fältet kan hämtas från resultatet av den `InsertAsync` anropa. En `ArgumentException` uppstår om du försöker uppdatera ett objekt utan att ange den `id` värde.

### <a name="deleting"></a>Så här: ta bort data i en mobilappsserverdel
Följande kod visar hur du använder den [DeleteAsync] metod för att ta bort en befintlig instans. Instansen identifieras av den `id` fältet set på den `todoItem`.

```
await todoTable.DeleteAsync(todoItem);
```

Om du vill ta bort ej typbestämd data, kan du dra nytta av Json.NET på följande sätt:

```
JObject jo = new JObject();
jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
await table.DeleteAsync(jo);
```

När du gör en begäran om borttagning måste ett ID anges. Andra egenskaper skickas inte till tjänsten eller ignoreras i tjänsten. Resultatet av en `DeleteAsync` anropet är vanligtvis `null`. Det ID: T för att skicka in kan hämtas från resultatet av den `InsertAsync` anropa. En `MobileServiceInvalidOperationException` när du försöker ta bort ett objekt utan att ange den `id` fältet.

### <a name="optimisticconcurrency"></a>Så här: använda Optimistisk samtidighet för konfliktlösning
Två eller flera klienter kan skriva ändringar till samma objekt samtidigt. Utan konfliktidentifiering, skulle den senaste skrivningen skriva över eventuella tidigare uppdateringar. **Optimistisk samtidighetskontroll** förutsätter att varje transaktion kan genomföra och därför använder inte någon resurslåsning.  Innan du genomför en transaktion verifierar optimistisk samtidighetskontroll att ingen annan transaktion har ändrat data. Om data har ändrats, återställs committing transaktionen.

Mobile Apps stöder optimistisk samtidighetskontroll genom att spåra ändringar av varje objekt med hjälp av den `version` system egenskapskolumn som har definierats för varje tabell i din mobilappsserverdel. Varje gång en post uppdateras Mobile Apps anger den `version` -egenskapen för posten till ett nytt värde. Under varje begäran om uppdatering av `version` egenskapen på den post som ingår i begäran jämförs med samma egenskap för posten på servern. Om versionen har skickats med förfrågan matchar inte serverdelen och sedan klientbiblioteket genererar en `MobileServicePreconditionFailedException<T>` undantag. Den typ som anges med undantaget är post från serverdel som innehåller servrar version av posten. Programmet kan sedan använda den här informationen för att bestämma om du vill köra begäran om uppdatering igen med rätt `version` värde från backend att genomföra ändringarna.

Definiera en kolumn i tabellen-klass för de `version` Systemegenskapen att aktivera Optimistisk samtidighet. Exempel:

```
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

Program med hjälp av någon tabeller aktivera Optimistisk samtidighet genom att ange den `Version` flaggan på den `SystemProperties` i tabellen på följande sätt.

```
//Enable optimistic concurrency by retrieving version
todoTable.SystemProperties |= MobileServiceSystemProperties.Version;
```

Utöver att möjliggöra Optimistisk samtidighet, måste du också fånga den `MobileServicePreconditionFailedException<T>` undantag i koden när du anropar [UpdateAsync].  Lös konflikten genom att använda rätt `version` uppdaterade posten och anropet [UpdateAsync] med löst posten. Följande kod visar hur du löser en Skriv-konflikt upptäcktes när:

```
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
    //Ask user to choose the resoltion between versions
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

Mer information finns i [Offline datasynkronisering i Azure Mobile Apps] avsnittet.

### <a name="binding"></a>Så här: Bind Mobile Apps data till en Windows-gränssnittet
Det här avsnittet visar hur du visar returnerade dataobjekt med hjälp av UI-element i en Windows-app.  Följande exempelkod Binder till källan för listan med en fråga om ofullständiga objekt. Den [MobileServiceCollection] skapar en Mobile Apps-medvetna bindning-samling.

```
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

Vissa kontroller i hanterade körningsmiljön stöd för ett gränssnitt som kallas [ISupportIncrementalLoading]. Du kan använda kontroller för att begära extra data när användaren rullar i gränssnittet. Det finns inbyggt stöd för det här gränssnittet för universella Windows-appar via [MobileServiceIncrementalLoadingCollection], som automatiskt hanterar anrop från kontroller. Använd `MobileServiceIncrementalLoadingCollection` i Windows-appar på följande sätt:

```
MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
items = todoTable.Where(todoItem => todoItem.Complete == false).ToIncrementalLoadingCollection();

ListBox lb = new ListBox();
lb.ItemsSource = items;
```

Om du vill använda den nya samlingen på Windows Phone 8 och ”Silverlight” den `ToCollection` tilläggsmetoder på `IMobileServiceTableQuery<T>` och `IMobileServiceTable<T>`. Om du vill läsa in data, anropa `LoadMoreItemsAsync()`.

```
MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection();
await items.LoadMoreItemsAsync();
```

När du använder den samling som skapats genom att anropa `ToCollectionAsync` eller `ToCollection`, du får en samling som kan bindas till UI-kontroller.  Den här samlingen är medveten om sidindelning.  Eftersom samlingen är läser in data från nätverket, misslyckas inläsning ibland. Om du vill hantera dessa fel, åsidosätta den `OnException` metod på `MobileServiceIncrementalLoadingCollection` att hantera undantag som uppstår till följd av anrop till `LoadMoreItemsAsync`.

Överväg om tabellen innehåller många fält men du bara vill visa en del av dem i kontrollen. Du kan använda riktlinjerna i föregående avsnitt ”[markera specifika kolumner](#selecting)” att markera specifika kolumner ska visas i Användargränssnittet.

### <a name="pagesize"></a>Ändra sidstorleken
Azure Mobile Apps returnerar maximalt 50 objekt per begäran som standard.  Du kan ändra växlingsfilens storlek genom att öka den maximala sidstorleken på både klienten och servern.  För att öka storleken på begärt ange `PullOptions` när du använder `PullAsync()`:

```
PullOptions pullOptions = new PullOptions
    {
        MaxPageSize = 100
    };
```

Under förutsättning att du har gjort det `PageSize` lika med eller större än 100 inom server, en begäran returnerar upp till 100 poster.

## <a name="#offlinesync"></a>Arbeta med Offline tabeller
Offline tabeller använder en lokal SQLite att lagra data för användning offline.  Alla tabellen operations görs mot lokalt SQLite lagra i stället för arkivet för fjärrservern.  Om du vill skapa en tabell som är offline, förbereder du projektet:

1. Högerklicka på lösningen i Visual Studio > **hantera NuGet-paket för lösningen...** , och Sök efter och installera den **Microsoft.Azure.Mobile.Client.SQLiteStore** NuGet-paket för alla projekt i lösningen.
2. (Valfritt) Installera en av följande SQLite runtime paket för att stödja Windows-enheter:

   * **Windows 8.1 Runtime:** installera [SQLite för Windows 8.1][3].
   * **Windows Phone 8.1:** installera [SQLite för Windows Phone 8.1][4].
   * **Universella Windows-plattformen** installera [SQLite för universella Windows][5].
3. (Valfritt). Windows-enheter klickar du på **referenser** > **Lägg till referens...** , expandera den **Windows** mappen > **tillägg**, aktivera rätt **SQLite för Windows** SDK tillsammans med **Visual C++ 2013 Runtime för Windows** SDK.
    SQLite SDK namnen varierar något med varje Windows-plattformen.

Innan en tabellreferens kan skapas, måste du förbereda det lokala arkivet:

```
var store = new MobileServiceSQLiteStore(Constants.OfflineDbPath);
store.DefineTable<TodoItem>();

//Initializes the SyncContext using the default IMobileServiceSyncHandler.
await this.client.SyncContext.InitializeAsync(store);
```

Initiering av certifikatarkiv görs normalt omedelbart efter att klienten har skapats.  Den **OfflineDbPath** bör vara ett filnamn som är lämplig för användning på alla plattformar som du stöder.  Om sökvägen är en fullständigt kvalificerad sökväg (d.v.s. den börjar med ett snedstreck), och sedan används.  Om sökvägen inte är fullständigt kvalificerad, placera filen i en plattformsspecifik plats.

* Standardsökvägen är mappen ”personliga filer” för iOS och Android-enheter.
* Standardsökvägen är mappen programspecifika ”AppData” för Windows-enheter.

En tabellreferens kan erhållas med hjälp av den `GetSyncTable<>` metoden:

```
var table = client.GetSyncTable<TodoItem>();
```

Du behöver inte autentisera om du vill använda en offline-tabell.  Du behöver bara autentisera när du kommunicerar med serverdelstjänsten.

### <a name="syncoffline"></a>Synkroniserar en Offline-tabell
Offline tabeller är inte synkroniserade med serverdelen som standard.  Synkronisering är uppdelat i två delar.  Du kan skicka ändringar separat hämtar nya objekt.  Här är en typisk synkroniseringsmetod:

```
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

Om det första argumentet för `PullAsync` är null, och sedan inte används för inkrementell synkronisering.  Varje synkroniseringsåtgärd hämtar alla poster.

SDK utför en implicit `PushAsync()` innan dra poster.

Konflikt hantering sker på en `PullAsync()` metod.  Du kan hantera konflikter på samma sätt som online-tabeller.  Konflikten skapas när `PullAsync()` anropas i stället för under insert-, update- eller delete. Om flera sådana konflikter uppstår ingår de i en enda MobileServicePushFailedException.  Hantera varje fel separat.

## <a name="#customapi"></a>Arbeta med en anpassad API
En anpassad API kan du definiera anpassade slutpunkter som exponerar serverfunktioner som inte mappas till en infoga, uppdatera, ta bort eller Läsåtgärd. Genom att använda en anpassad API kan ha du mer kontroll över meddelanden, inklusive läsning och ange HTTP-meddelandehuvuden och definiera ett body-meddelandeformat än JSON.

Du kan anropa en anpassad API genom att anropa en av de [InvokeApiAsync] metoder på klienten. Till exempel följande kodrad skickar en POST-begäran till den **completeAll** API på serverdelen:

```
var result = await client.InvokeApiAsync<MarkAllResult>("completeAll", System.Net.Http.HttpMethod.Post, null);
```

Det här formuläret är skrivna metodanrop och kräver att den **MarkAllResult** returnera typen är definierad. Både skrivna och ej typbestämd metoder stöds.

Metoden InvokeApiAsync() läggs/api /'-API: et som du vill anropa om API: et som börjar med en '/'.
Exempel:

* `InvokeApiAsync("completeAll",...)`anropar /api/completeAll på serverdelen
* `InvokeApiAsync("/.auth/me",...)`anropar /.auth/me på serverdelen

Du kan använda InvokeApiAsync för att anropa alla WebAPI, inklusive de WebAPIs som inte har definierats med Azure Mobile Apps.  När du använder InvokeApiAsync() skickas lämplig sidhuvud, inklusive autentiseringshuvuden med begäran.

## <a name="authentication"></a>Autentisera användare
Mobile Apps stöder autentisering och auktorisering av appanvändare som använder olika externa identitetsleverantörer: Facebook, Google, Account, Twitter och Azure Active Directory. Du kan ange behörigheter på tabeller för att begränsa åtkomsten för specifika åtgärder endast autentiserade användare. Du kan också använda identiteten för autentiserade användare för att implementera auktoriseringsregler i server-skript. Mer information finns i självstudierna [Lägg till autentisering till din app].

Två autentisering flöden stöds: *klienten hanteras* och *server-hanterade* flöde. Server-hanterade flödet ger enklaste autentiseringsupplevelse, eftersom det är beroende av leverantörens Webbgränssnitt för autentisering. Klient-hanterade flödet tillåter djupare integrering med specifika funktioner som använder den provider-specifik enhetsspecifika SDK.

> [!NOTE]
> Du bör använda ett klient-hanterade flöde i dina appar i produktion.

Om du vill konfigurera autentisering måste du registrera din app med en eller flera identitetsleverantörer.  Identitetsleverantören som genererar ett klient-ID och en klienthemlighet för din app.  Dessa värden anges sedan i din serverdel för att aktivera autentisering/auktorisering i Azure App Service.  Mer information följer detaljerade instruktioner i självstudiekursen [Lägg till autentisering till din app].

I följande avsnitt beskrivs i det här avsnittet:

* [Klienten hanteras autentisering](#clientflow)
* [Hanterad Server-autentisering](#serverflow)
* [Cachelagring autentiseringstoken](#caching)

### <a name="clientflow"></a>Klienten hanteras autentisering
Din app kan oberoende kontakta identitetsleverantören och ange sedan det returnerade token under inloggningen med din serverdel. Detta klient-flöde kan du tillhandahålla en enkel inloggning för användare eller hämta ytterligare användardata från identitetsleverantören. Flöde för klientautentisering är att föredra med ett server-flöde som identitetsleverantören SDK tillhandahåller en mer ursprungligt UX känslan och gör för ytterligare anpassning.

Exempel har angetts för följande-flödet autentisering mönster:

* [Active Directory Authentication Library](#adal)
* [Facebook eller Google](#client-facebook)
* [Live SDK](#client-livesdk)

#### <a name="adal"></a>Autentisera användarna med Active Directory Authentication Library
Du kan använda Active Directory Authentication Library (ADAL) för att initiera användarautentisering från klienten med hjälp av Azure Active Directory-autentisering.

1. Konfigurera mobilappsserverdelen för inloggning AAD genom att följa den [konfigurera App Service för Active Directory-inloggningen] kursen. Se till att slutföra det valfria steget med att registrera en native client-program.
2. Öppna projektet i Visual Studio eller Xamarin Studio och lägga till en referens till den `Microsoft.IdentityModel.CLients.ActiveDirectory` NuGet-paketet. När du söker kan innehålla förhandsversioner.
3. Lägg till följande kod i ditt program, beroende på plattform som du använder. I varje, gör du följande ersättningar:

   * Ersätt **INSERT-UTFÄRDARE-här** med namnet på klienten som du har etablerat ditt program. Formatet som ska vara https://login.microsoftonline.com/contoso.onmicrosoft.com. Det här värdet kan kopieras från fliken domän i Azure Active Directory i den [Azure-portalen].
   * Ersätt **INSERT-resurs-ID-här** med klient-ID för din mobilappsserverdel. Du kan hämta klient-ID från den **Avancerat** fliken **inställningarna för Azure Active Directory** i portalen.
   * Ersätt **INSERT-klient-ID-här** med klient-ID som du kopierade från native client-program.
   * Ersätt **INSERT-OMDIRIGERINGS-URI-här** med webbplatsens */.auth/login/done* slutpunkten, med hjälp av HTTPS-schema. Det här värdet ska vara liknar *https://contoso.azurewebsites.net/.auth/login/done*.

     Den kod som behövs för varje plattform som följer:

     **Windows:**

    ```
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

    ```
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

    ```
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
Du kan använda flödet som visas i den här fragment för Facebook eller Google.

```
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

#### <a name="client-livesdk"></a>Enkel inloggning med Live SDK Account
För att autentisera användare, måste du registrera din app på Microsoft-konto Developer Center. Konfigurera registreringsinformation på din mobilappsserverdel. Skapa ett Microsoft-Kontoregistrering och ansluta till din mobilappsserverdel genom att slutföra stegen i [registrera din app om du vill använda en Microsoft-kontoinloggning]. Om du har både Windows Store och Windows Phone 8/Silverlight-versionerna av din app kan du registrera Windows Store-versionen först.

Följande kod autentiserar med hjälp av Live SDK och använder den returnerade token för att logga in på din mobilappsserverdel.

```
private LiveConnectSession session;
    //private static string clientId = "<microsoft-account-client-id>";
private async System.Threading.Tasks.Task AuthenticateAsync()
{

    // Get the URL the Mobile App backend.
    var serviceUrl = App.MobileService.ApplicationUri.AbsoluteUri;

    // Create the authentication client for Windows Store using the service URL.
    LiveAuthClient liveIdClient = new LiveAuthClient(serviceUrl);
    //// Create the authentication client for Windows Phone using the client ID of the registration.
    //LiveAuthClient liveIdClient = new LiveAuthClient(clientId);

    while (session == null)
    {
        // Request the authentication token from the Live authentication service.
        // The wl.basic scope should always be requested.  Other scopes can be added
        LiveLoginResult result = await liveIdClient.LoginAsync(new string[] { "wl.basic" });
        if (result.Status == LiveConnectSessionStatus.Connected)
        {
            session = result.Session;

            // Get information about the logged-in user.
            LiveConnectClient client = new LiveConnectClient(session);
            LiveOperationResult meResult = await client.GetAsync("me");

            // Use the Microsoft account auth token to sign in to App Service.
            MobileServiceUser loginResult = await App.MobileService
                .LoginWithMicrosoftAccountAsync(result.Session.AuthenticationToken);

            // Display a personalized sign-in greeting.
            string title = string.Format("Welcome {0}!", meResult.Result["first_name"]);
            var message = string.Format("You are now logged in - {0}", loginResult.UserId);
            var dialog = new MessageDialog(message, title);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
        else
        {
            session = null;
            var dialog = new MessageDialog("You must log in.", "Login Required");
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
    }
}
```

Mer information finns i [Windows Live SDK] dokumentation.

### <a name="serverflow"></a>Hanterad Server-autentisering
När du har registrerat din identitetsleverantör anropa den [LoginAsync] metod på [MobileServiceClient] med den [MobileServiceAuthenticationProvider] värdet för leverantören. Till exempel initierar följande kod en server flödet inloggning med Facebook.

```
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

Om du använder en identitetsleverantör än Facebook, ändrar du värdet för [MobileServiceAuthenticationProvider] med värdet för leverantören.

I ett server-flöde hanterar Azure App Service flödet för OAuth-autentisering genom att visa inloggningssidan för vald leverantör.  När identitet providern returnerar Azure App Service genererar en Apptjänst-token för autentisering. Den [LoginAsync] metoden returnerar en [MobileServiceUser], som innehåller både den [UserId] på den autentiserade användaren och [ MobileServiceAuthenticationToken], som JSON web token (JWT). Den här token kan cachelagras och återanvändas tills den upphör att gälla. Mer information finns i [cachelagring autentiseringstoken](#caching).

### <a name="caching"></a>Cachelagring autentiseringstoken
I vissa fall undvikas anrop till metoden inloggningen efter den första autentiseringen genom att lagra autentiseringstoken från leverantören.  Windows Store och UWP-appar kan använda [PasswordVault] till cachen aktuella autentiseringstoken efter en lyckad inloggning, enligt följande:

```
await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook);

PasswordVault vault = new PasswordVault();
vault.Add(new PasswordCredential("Facebook", client.currentUser.UserId,
    client.currentUser.MobileServiceAuthenticationToken));
```

Användar-ID-värdet lagras som användarnamnet för autentiseringsuppgifter och token som är lagrade som lösenord. I efterföljande nystartade företag, kan du kontrollera den **PasswordVault** för cachelagrade autentiseringsuppgifter. I följande exempel används cachelagrade autentiseringsuppgifter när de hittas och annars försöker autentisera igen med serverdelen:

```
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

När du loggar ut en användare måste du också bort lagrade autentiseringsuppgifter på följande sätt:

```
client.Logout();
vault.Remove(vault.Retrieve("Facebook", client.currentUser.UserId));
```

Xamarin-appar används i [Xamarin.Auth] -API: er på ett säkert sätt lagra autentiseringsuppgifter i en **konto** objekt. Ett exempel på hur du använder dessa API: er finns i [AuthStore.cs] kodfilen i den [ContosoMoments foto delning exempel](https://github.com/azure-appservice-samples/ContosoMoments).

När du använder klienten hanteras autentisering, kan du Cachelagra den åtkomst-token som hämtades från leverantören, till exempel Facebook och Twitter. Denna token kan anges för att begära en ny autentiseringstoken från serverdelen, enligt följande:

```
var token = new JObject();
// Replace <your_access_token_value> with actual value of your access token
token.Add("access_token", "<your_access_token_value>");

// Authenticate using the access token.
await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
```

## <a name="pushnotifications"></a>Push-meddelanden
I följande avsnitt beskrivs Push-meddelanden:

* [Registrera dig för Push-meddelanden](#register-for-push)
* [Skaffa en Windows Store paket-SID](#package-sid)
* [Registrera med plattformsoberoende mallar](#register-xplat)

### <a name="register-for-push"></a>Så här: registrera dig för Push-meddelanden
Mobile Apps-klienten kan du registrera dig för push-meddelanden med Azure Notification Hubs. När du registrerar dig, kan du hämta en referens som du har fått från plattformsspecifika Push Notification Service (PNS). Du kan sedan ange värdet tillsammans med alla taggar när du skapar registreringen. Följande kod registrerar din Windows-app för push-meddelanden med Windows Notification Service (WNS):

```
private async void InitNotificationsAsync()
{
    // Request a push notification channel.
    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    // Register for notifications using the new channel.
    await MobileService.GetPush().RegisterNativeAsync(channel.Uri, null);
}
```

Om du sänder till WNS och sedan måste du [hämtar Windows Store-paketet SID](#package-sid).  Mer information om Windows-appar, inklusive hur du registrerar dig för mallen registreringar finns [Lägg till push-meddelanden i appen].

Begär taggar från klienten stöds inte.  Taggen begäranden släpps tyst från registreringen.
Om du vill registrera din enhet med taggar kan du skapa en anpassad API som använder API: et för Notification Hubs för att utföra registreringen för din räkning.  [Anropa anpassade API](#customapi) i stället för den `RegisterNativeAsync()` metoden.

### <a name="package-sid"></a>Så här: hämta en Windows Store paket-SID
Paket-SID krävs för att aktivera push-meddelanden i Windows Store-appar.  Registrera ditt program med Windows Store för att ta emot paket-SID.

Du kan hämta det här värdet:

1. Högerklicka på Windows Store-app-projekt i Visual Studio Solution Explorer, klickar du på **Store** > **associera App med Store...** .
2. I guiden klickar du på **nästa**, logga in med ditt Microsoft-konto, Skriv ett namn för din app i **reservera ett nytt namn för appen**, klicka på **reservera**.
3. När registreringen app har skapats, Välj namnet på appen, klicka på **nästa**, och klicka sedan på **associera**.
4. Logga in på den [Windows Dev Center] med ditt Microsoft-Account. Under **Mina appar**, klicka på appen registreringen som du skapade.
5. Klicka på **apphantering** > **App identitet**, och rulla sedan hitta din **paket-SID**.

Många användningsområden för paket-SID behandla det som en URI i så fall måste du använda *ms-app: / /* som schema. Anteckna versionen av ditt paket-SID som bildas genom att det här värdet som ett prefix.

Xamarin-appar kräver ytterligare kod för att kunna registrera en app som körs på iOS eller Android-plattformar. Mer information finns i avsnittet för din plattform:

* [Xamarin.Android](app-service-mobile-xamarin-android-get-started-push.md#add-push)
* [Xamarin.iOS](app-service-mobile-xamarin-ios-get-started-push.md#add-push-notifications-to-your-app)

### <a name="register-xplat"></a>Så här: registrera push-mallar för att skicka plattformsoberoende meddelanden
Registrera mallar genom att använda den `RegisterAsync()` metod med mallarna på följande sätt:

```
JObject templates = myTemplates();
MobileService.GetPush().RegisterAsync(channel.Uri, templates);
```

Dina mallar ska vara `JObject` datatyper och kan innehålla flera mallar i följande JSON-format:

```
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

```
MobileService.GetPush().RegisterAsync(string channelUri, JObject templates, JObject secondaryTiles);
```

Alla taggar tas bort direkt under registreringen för säkerhet. Om du vill lägga till taggar i installationer eller mallar inom installationer finns i [arbeta med serverdelen .NET SDK för Azure Mobile Apps].

Om du vill skicka meddelanden genom att använda mallarna registrerade, referera till den [Notification Hubs API: er].

## <a name="misc"></a>Diverse avsnitt
### <a name="errors"></a>Så här: hantera fel
När ett fel uppstår i serverdelen kan klienten SDK genererar en `MobileServiceInvalidOperationException`.  I följande exempel visas hur du hanterar ett undantag som returneras av serverdelen:

```
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

Ett annat exempel för att behandla felvillkor kan hittas i den [Mobile Apps filer exempel]. Den [LoggingHandler] exempel ger en loggning ombud hanterare för att logga begäranden som görs till serverdelen.

### <a name="headers"></a>Så här: anpassa begärandehuvuden
För att stödja specifika app-scenariot, kan du behöva anpassa kommunikationen med serverdelen för Mobilappen. Du kanske vill lägga till en anpassad rubrik i varje utgående begäran eller även ändra svar statuskoder. Du kan använda en anpassad [DelegatingHandler], som i följande exempel:

```
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
[4]: https://msdn.microsoft.com/en-us/library/azure/mt419521(v=azure.10).aspx
[5]: https://github.com/Azure-Samples
[6]: http://www.newtonsoft.com/json/help/html/Properties_T_Newtonsoft_Json_JsonPropertyAttribute.htm
[7]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#define-table-controller
[8]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[9]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/
[10]: http://www.symbolsource.org/
[11]: http://www.symbolsource.org/Public/Wiki/Using
[12]: https://msdn.microsoft.com/en-us/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx

[Lägg till autentisering till din app]: app-service-mobile-windows-store-dotnet-get-started-users.md
[Offline datasynkronisering i Azure Mobile Apps]: app-service-mobile-offline-data-sync.md
[Lägg till push-meddelanden i appen]: app-service-mobile-windows-store-dotnet-get-started-push.md
[registrera din app om du vill använda en Microsoft-kontoinloggning]: ../app-service/app-service-mobile-how-to-configure-microsoft-authentication.md
[konfigurera App Service för Active Directory-inloggningen]: ../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md

<!-- Microsoft URLs. -->
[MobileServiceCollection]: https://msdn.microsoft.com/en-us/library/azure/dn250636(v=azure.10).aspx
[MobileServiceIncrementalLoadingCollection]: https://msdn.microsoft.com/en-us/library/azure/dn268408(v=azure.10).aspx
[MobileServiceAuthenticationProvider]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider(v=azure.10).aspx
[MobileServiceUser]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser(v=azure.10).aspx
[ MobileServiceAuthenticationToken]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken(v=azure.10).aspx
[GetTable]: https://msdn.microsoft.com/en-us/library/azure/jj554275(v=azure.10).aspx
[skapar en referens till en ej typbestämd tabell]: https://msdn.microsoft.com/en-us/library/azure/jj554278(v=azure.10).aspx
[DeleteAsync]: https://msdn.microsoft.com/en-us/library/azure/dn296407(v=azure.10).aspx
[IncludeTotalCount]: https://msdn.microsoft.com/en-us/library/azure/dn250560(v=azure.10).aspx
[InsertAsync]: https://msdn.microsoft.com/en-us/library/azure/dn296400(v=azure.10).aspx
[InvokeApiAsync]: https://msdn.microsoft.com/en-us/library/azure/dn268343(v=azure.10).aspx
[LoginAsync]: https://msdn.microsoft.com/en-us/library/azure/dn296411(v=azure.10).aspx
[LookupAsync]: https://msdn.microsoft.com/en-us/library/azure/jj871654(v=azure.10).aspx
[OrderBy]: https://msdn.microsoft.com/en-us/library/azure/dn250572(v=azure.10).aspx
[OrderByDescending]: https://msdn.microsoft.com/en-us/library/azure/dn250568(v=azure.10).aspx
[ReadAsync]: https://msdn.microsoft.com/en-us/library/azure/mt691741(v=azure.10).aspx
[ta]: https://msdn.microsoft.com/en-us/library/azure/dn250574(v=azure.10).aspx
[Välj]: https://msdn.microsoft.com/en-us/library/azure/dn250569(v=azure.10).aspx
[hoppa över]: https://msdn.microsoft.com/en-us/library/azure/dn250573(v=azure.10).aspx
[UpdateAsync]: https://msdn.microsoft.com/en-us/library/azure/dn250536.(v=azure.10)aspx
[Användar-ID]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid(v=azure.10).aspx
[där]: https://msdn.microsoft.com/en-us/library/azure/dn250579(v=azure.10).aspx
[Azure-portalen]: https://portal.azure.com/
[EnableQueryAttribute]: https://msdn.microsoft.com/library/system.web.http.odata.enablequeryattribute.aspx
[Guid.NewGuid]: https://msdn.microsoft.com/en-us/library/system.guid.newguid(v=vs.110).aspx
[ISupportIncrementalLoading]: http://msdn.microsoft.com/library/windows/apps/Hh701916.aspx
[Windows Dev Center]: https://dev.windows.com/en-us/overview
[DelegatingHandler]: https://msdn.microsoft.com/library/system.net.http.delegatinghandler(v=vs.110).aspx
[Windows Live SDK]: https://msdn.microsoft.com/en-us/library/bb404787.aspx
[PasswordVault]: http://msdn.microsoft.com/library/windows/apps/windows.security.credentials.passwordvault.aspx
[ProtectedData]: http://msdn.microsoft.com/library/system.security.cryptography.protecteddata%28VS.95%29.aspx
[Notification Hubs API: er]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[Mobile Apps filer exempel]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files
[LoggingHandler]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files/blob/master/src/client/MobileAppsFilesSample/Helpers/LoggingHandler.cs#L63

<!-- External URLs -->
[OData v3 dokumentationen]: http://www.odata.org/documentation/odata-version-3-0/
[Fiddler]: http://www.telerik.com/fiddler
[Json.NET]: http://www.newtonsoft.com/json
[Xamarin.Auth]: https://components.xamarin.com/view/xamarin.auth/
[AuthStore.cs]: https://github.com/azure-appservice-samples/ContosoMoments
[ContosoMoments photo sharing sample]: https://github.com/azure-appservice-samples/ContosoMoments
