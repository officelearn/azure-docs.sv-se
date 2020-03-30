---
title: Arbeta med det hanterade klientbiblioteket
description: Lär dig hur du använder .NET-klientbiblioteket för Azure App Service Mobile Apps med Windows- och Xamarin-appar.
ms.assetid: 0280785c-e027-4e0d-aaf2-6f155e5a6197
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 1c9fba3c13cc6e5476377d59130a95a2edaa324d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249378"
---
# <a name="how-to-use-the-managed-client-for-azure-mobile-apps"></a>Så här använder du den hanterade klienten för Azure Mobile Apps
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

## <a name="overview"></a>Översikt
Den här guiden visar hur du utför vanliga scenarier med hjälp av det hanterade klientbiblioteket för Azure App Service Mobile Apps för Windows och Xamarin-appar. Om du inte har gjort det tidigare i mobilappar bör du först slutföra snabbstartshandledningen för [Azure Mobile Apps.][1] I den här guiden fokuserar vi på kundsidan hanterade SDK. Mer information om SDK:er på serversidan för mobilappar finns i dokumentationen för [.NET Server SDK][2] eller [Node.js Server SDK][3].

## <a name="reference-documentation"></a>Referensdokumentation
Referensdokumentationen för klienten SDK finns här: [Azure Mobile Apps .NET-klientreferens][4].
Du kan också hitta flera klientexempel i [Azure-Samples GitHub-databasen][5].

## <a name="supported-platforms"></a>Plattformar som stöds
.NET-plattformen stöder följande plattformar:

* Xamarin Android utgåvor för API 19 till 24 (KitKat genom Nougat)
* Xamarin iOS-versioner för iOS-versioner 8.0 och senare
* Universell Windows-plattform
* Windows Phone 8.1
* Windows Phone 8.0 med undantag för Silverlight-program

Autentiseringen "serverflöde" använder en WebView för det presenterade användargränssnittet.  Om enheten inte kan presentera ett WebView-användargränssnitt behövs andra autentiseringsmetoder.  Denna SDK är därför inte lämplig för Watch-type eller liknande begränsade enheter.

## <a name="setup-and-prerequisites"></a><a name="setup"></a>Inställningar och förutsättningar
Vi förutsätter att du redan har skapat och publicerat ditt backend-projekt för mobilapp, som innehåller minst en tabell.  I koden som används i det `TodoItem` här avsnittet namnges `Id`tabellen `Text`och `Complete`följande kolumner: , och . Den här tabellen är samma tabell som skapas när du slutför [snabbstarten för Azure Mobile Apps][1].

Motsvarande typ av klientsida i C# är följande klass:

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

[JsonPropertyAttribute][6] används för att definiera *mappningen PropertyName* mellan klientfältet och tabellfältet.

Mer information om hur du skapar tabeller i serverdelen Mobile Apps finns i [avsnittet .NET Server SDK][7] eller [avsnittet Node.js Server SDK][8]. Om du har skapat din mobile app-backend i Azure-portalen med hjälp av Snabbstart kan du också använda inställningen **Enkla tabeller** i [Azure-portalen].

### <a name="how-to-install-the-managed-client-sdk-package"></a>Så här installerar du det hanterade klient-SDK-paketet
Använd någon av följande metoder för att installera det hanterade klient-SDK-paketet för mobilappar från [NuGet:][9]

* **Visuell studio** Högerklicka på projektet, klicka på **Hantera NuGet-paket,** sök efter `Microsoft.Azure.Mobile.Client` paketet och klicka sedan på **Installera**.
* **Xamarin Studio** Högerklicka på projektet, klicka på **Lägg till** > Lägg `Microsoft.Azure.Mobile.Client` till **NuGet-paket,** sök efter paketet och klicka sedan på Lägg till **paket**.

Kom ihåg att lägga till följande **med hjälp av** uttryck i huvudaktivitetsfilen:

```csharp
using Microsoft.WindowsAzure.MobileServices;
```

> [!NOTE]
> Observera att alla supportpaket som det refereras till i ditt Android-projekt måste ha samma version. SDK har `Xamarin.Android.Support.CustomTabs` beroende för Android-plattformen, så om ditt projekt använder nyare supportpaket måste du installera det här paketet med nödvändig version direkt för att undvika konflikter.

### <a name="how-to-work-with-debug-symbols-in-visual-studio"></a><a name="symbolsource"></a>Så här: Arbeta med felsökningssymboler i Visual Studio
Symbolerna för namnområdet Microsoft.Azure.Mobile är tillgängliga på [SymbolSource][10].  Se [symbolkällans instruktioner][11] för att integrera SymbolSource med Visual Studio.

## <a name="create-the-mobile-apps-client"></a><a name="create-client"></a>Skapa mobile apps-klienten
Följande kod skapar [MobileServiceClient-objektet][12] som används för att komma åt din Mobile App-backend.

```csharp
var client = new MobileServiceClient("MOBILE_APP_URL");
```

Ersätt `MOBILE_APP_URL` med url:en för den mobila app-backend som finns i bladet för din Mobile App-backend i [Azure-portalen]. MobileServiceClient-objektet ska vara en singleton.

## <a name="work-with-tables"></a>Arbeta med tabeller
I följande avsnitt beskrivs hur du söker efter och hämtar poster och ändrar data i tabellen.  Följande ämnen behandlas:

* [Skapa en tabellreferens](#instantiating)
* [Söka i data](#querying)
* [Filtrera returnerade data](#filtering)
* [Sortera returnerade data](#sorting)
* [Returnera data på sidor](#paging)
* [Markera specifika kolumner](#selecting)
* [Slå upp en post med ID](#lookingup)
* [Hantera otypade frågor](#untypedqueries)
* [Infoga data](#inserting)
* Uppdatera data
* [Ta bort data](#deleting)
* [Konfliktlösning och optimistisk samtidighet](#optimisticconcurrency)
* [Bindning till ett Windows-användargränssnitt](#binding)
* [Ändra sidstorlek](#pagesize)

### <a name="how-to-create-a-table-reference"></a><a name="instantiating"></a>Så här skapar du en tabellreferens
All kod som kommer åt eller ändrar data i en `MobileServiceTable` backend-tabell anropar funktioner på objektet. Hämta en referens till tabellen genom att anropa [GetTable-metoden] enligt följande:

```csharp
IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
```

Det returnerade objektet använder den maskinskrivna serialiseringsmodellen. En otypad serialiseringsmodell stöds också. I följande exempel [skapas en referens till en otypad tabell:]

```csharp
// Get an untyped table reference
IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");
```

I otypade frågor måste du ange den underliggande OData-frågesträngen.

### <a name="how-to-query-data-from-your-mobile-app"></a><a name="querying"></a>Så här: Fråga data från mobilappen
I det här avsnittet beskrivs hur du utfärdar frågor till backend för mobilappen, som innehåller följande funktioner:

* [Filtrera returnerade data](#filtering)
* [Sortera returnerade data](#sorting)
* [Returnera data på sidor](#paging)
* [Markera specifika kolumner](#selecting)
* [Slå upp data efter ID](#lookingup)

> [!NOTE]
> En serverdriven sidstorlek tillämpas för att förhindra att alla rader returneras.  Växlingen förhindrar att standardbegäranden för stora datauppsättningar påverkar tjänsten negativt.  Om du vill returnera mer än `Skip` 50 rader använder du metoden och `Take` som beskrivs i [Returdata på sidor](#paging).

### <a name="how-to-filter-returned-data"></a><a name="filtering"></a>Så här filtrerar du returnerade data
Följande kod visar hur du filtrerar `Where` data genom att inkludera en sats i en fråga. Den returnerar `todoTable` alla `Complete` artiklar från `false`vars egenskap är lika med . Funktionen [Var] används ett radfiltreringspredikat för frågan mot tabellen.

```csharp
// This query filters out completed TodoItems and items without a timestamp.
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .ToListAsync();
```

Du kan visa URI för begäran som skickas till serverda med hjälp av meddelandeinspektionsprogram, till exempel webbläsarutvecklare verktyg eller [Fiddler]. Om du tittar på uri-begäran lägger du märke till att frågesträngen har ändrats:

```csharp
GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1
```

Den här OData-begäran översätts till en SQL-fråga av Server SDK:

```csharp
SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0
```

Funktionen som skickas till `Where` metoden kan ha ett godtyckligt antal villkor.

```csharp
// This query filters out completed TodoItems where Text isn't null
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false && todoItem.Text != null)
    .ToListAsync();
```

Det här exemplet skulle översättas till en SQL-fråga av Server SDK:

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

De två metoderna är likvärdiga och kan användas omväxlande.  Det tidigare&mdash;alternativet att sammanfoga flera predikat i&mdash;en fråga är mer kompakt och rekommenderas.

Satsen `Where` stöder åtgärder som ska översättas till delmängden OData. Verksamheten omfattar:

* Relationsoperatorer (==, !=, <, <=, >, >=),
* Aritmetiska operatorer (+, -, /, *, %),
* Talprecision (matematik.golv, matematik.tak),
* Strängfunktioner (längd, delsträng, ersätt, IndexOf, StartsWith, EndsWith),
* Datumegenskaper (år, månad, dag, timme, minut, sekund),
* Åtkomstegenskaper för ett objekt och
* Uttryck som kombinerar någon av dessa åtgärder.

När du överväger vad Server SDK stöder kan du ta hänsyn till [OData v3-dokumentationen].

### <a name="how-to-sort-returned-data"></a><a name="sorting"></a>Så här sorterar du returnerade data
Följande kod visar hur du sorterar data genom att inkludera en [OrderBy-] eller [OrderByDescending-funktion] i frågan. Objekt returneras `todoTable` från sorterad `Text` stigande efter fältet.

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

### <a name="how-to-return-data-in-pages"></a><a name="paging"></a>Så här returnerar du data på sidor
Som standard returnerar backend endast de första 50 raderna. Du kan öka antalet returnerade rader genom att anropa [metoden Take.] Använd `Take` tillsammans med [skip-metoden] för att begära en viss "sida" av den totala datauppsättningen som returneras av frågan. Följande fråga returnerar de tre översta objekten i tabellen när den körs.

```csharp
// Define a filtered query that returns the top 3 items.
MobileServiceTableQuery<TodoItem> query = todoTable.Take(3);
List<TodoItem> items = await query.ToListAsync();
```

Följande reviderade fråga hoppar över de tre första resultaten och returnerar de följande tre resultaten. Den här frågan ger den andra "sidan" av data, där sidstorleken är tre objekt.

```csharp
// Define a filtered query that skips the top 3 items and returns the next 3 items.
MobileServiceTableQuery<TodoItem> query = todoTable.Skip(3).Take(3);
List<TodoItem> items = await query.ToListAsync();
```

Metoden [IncludeTotalCount] begär det totala antalet för *alla* poster som skulle ha returnerats, ignorera alla växlings-/limit-satser som angetts:

```csharp
query = query.IncludeTotalCount();
```

I en verklig app kan du använda frågor som liknar föregående exempel med en personsökarekontroll eller jämförbart användargränssnitt för att navigera mellan sidor.

> [!NOTE]
> Om du vill åsidosätta 50-radsgränsen i en serverdel i mobilappen måste du också använda [Metoden EnableQueryAttribute] på den offentliga GET-metoden och ange växlingsbeteendet. När den används på metoden anger följande maximala returnerade rader till 1000:
>
> `[EnableQuery(MaxTop=1000)]`


### <a name="how-to-select-specific-columns"></a><a name="selecting"></a>Så här väljer du specifika kolumner
Du kan ange vilka egenskaper som ska inkluderas i resultaten genom att lägga till en [Select-sats] i frågan. Följande kod visar till exempel hur du bara väljer ett fält och även hur du markerar och formaterar flera fält:

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

Alla funktioner som beskrivits hittills är additiva, så vi kan fortsätta kedja dem. Varje kedjat anrop påverkar mer av frågan. Ytterligare ett exempel:

```csharp
MobileServiceTableQuery<TodoItem> query = todoTable
                .Where(todoItem => todoItem.Complete == false)
                .Select(todoItem => todoItem.Text)
                .Skip(3).
                .Take(3);
List<string> items = await query.ToListAsync();
```

### <a name="how-to-look-up-data-by-id"></a><a name="lookingup"></a>Så här slår du upp data efter ID
[Funktionen LookupAsync] kan användas för att slå upp objekt från databasen med ett visst ID.

```csharp
// This query filters out the item with the ID of 37BBF396-11F0-4B39-85C8-B319C729AF6D
TodoItem item = await todoTable.LookupAsync("37BBF396-11F0-4B39-85C8-B319C729AF6D");
```

### <a name="how-to-execute-untyped-queries"></a><a name="untypedqueries"></a>Så här kör du otypade frågor
När du kör en fråga med ett otypat tabellobjekt måste du uttryckligen ange OData-frågesträngen genom att anropa [ReadAsync], som i följande exempel:

```csharp
// Lookup untyped data using OData
JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");
```

Du får tillbaka JSON värden som du kan använda som en egenskap väska. För mer information om JToken och Newtonsoft Json.NET, se [Json.NET] webbplats.

### <a name="how-to-insert-data-into-a-mobile-app-backend"></a><a name="inserting"></a>Så här infogar du data i en backend för mobilappen
Alla klienttyper måste innehålla en medlem med namnet **ID**, som som standard är en sträng. Detta **ID** krävs för att utföra CRUD-åtgärder och för offlinesynkronisering. Följande kod visar hur du använder [metoden InsertAsync] för att infoga nya rader i en tabell. Parametern innehåller de data som ska infogas som ett .NET-objekt.

```csharp
await todoTable.InsertAsync(todoItem);
```

Om ett unikt anpassat ID-värde `todoItem` inte ingår i under en infogning genereras ett GUID av servern.
Du kan hämta det genererade ID:t genom att inspektera objektet när samtalet har returnerats.

Om du vill infoga otypade data kan du dra nytta av Json.NET:

```csharp
JObject jo = new JObject();
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.InsertAsync(jo);
```

Här är ett exempel med en e-postadress som ett unikt sträng-ID:

```csharp
JObject jo = new JObject();
jo.Add("id", "myemail@emaildomain.com");
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.InsertAsync(jo);
```

### <a name="working-with-id-values"></a>Arbeta med ID-värden
Mobilappar stöder unika anpassade strängvärden för tabellens **id-kolumn.** Med ett strängvärde kan program använda anpassade värden som e-postadresser eller användarnamn för ID:t.  Sträng-ID:er ger dig följande fördelar:

* ID genereras utan att göra en rundresa till databasen.
* Poster är enklare att sammanfoga från olika tabeller eller databaser.
* ID-värden kan integreras bättre med ett programs logik.

När ett sträng-ID-värde inte anges på en infogad post genererar mobile app-backend ett unikt värde för ID:n. Du kan använda metoden [Guid.NewGuid] för att generera dina egna ID-värden, antingen på klienten eller i backend.

```csharp
JObject jo = new JObject();
jo.Add("id", Guid.NewGuid().ToString("N"));
```

### <a name="how-to-modify-data-in-a-mobile-app-backend"></a><a name="modifying"></a>Så här ändrar du data i en backend för mobilapp
Följande kod visar hur du använder [metoden UpdateAsync] för att uppdatera en befintlig post med samma ID med ny information. Parametern innehåller de data som ska uppdateras som ett .NET-objekt.

```csharp
await todoTable.UpdateAsync(todoItem);
```

Om du vill uppdatera otypade data kan du dra nytta av [Json.NET] enligt följande:

```csharp
JObject jo = new JObject();
jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.UpdateAsync(jo);
```

Ett `id` fält måste anges när en uppdatering. I backend `id` används fältet för att identifiera vilken rad som ska uppdateras. Fältet `id` kan erhållas från resultatet `InsertAsync` av samtalet. En `ArgumentException` höjs om du försöker uppdatera `id` ett objekt utan att ange värdet.

### <a name="how-to-delete-data-in-a-mobile-app-backend"></a><a name="deleting"></a>Så här tar du bort data i en backend för mobilapp
Följande kod illustrerar hur du använder [metoden DeleteAsync] för att ta bort en befintlig instans. Instansen identifieras av `id` fältet som `todoItem`anges i .

```csharp
await todoTable.DeleteAsync(todoItem);
```

Om du vill ta bort otypade data kan du dra nytta av Json.NET enligt följande:

```csharp
JObject jo = new JObject();
jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
await table.DeleteAsync(jo);
```

När du gör en borttagningsbegäran måste ett ID anges. Andra egenskaper skickas inte till tjänsten eller ignoreras vid tjänsten. Resultatet av `DeleteAsync` ett samtal `null`är vanligtvis . ID:t som ska skickas in kan `InsertAsync` erhållas från resultatet av samtalet. A `MobileServiceInvalidOperationException` genereras när du försöker ta bort `id` ett objekt utan att ange fältet.

### <a name="how-to-use-optimistic-concurrency-for-conflict-resolution"></a><a name="optimisticconcurrency"></a>Så här: Använd optimistisk samtidighet för konfliktlösning
Två eller flera klienter kan skriva ändringar i samma objekt samtidigt. Utan konfliktidentifiering skulle den senaste skrivningen skriva över alla tidigare uppdateringar. **Optimistisk samtidighetskontroll** förutsätter att varje transaktion kan genomföra och därför inte använder någon resurslåsning.  Innan du genomför en transaktion verifierar optimistisk samtidighetskontroll att ingen annan transaktion har ändrat data. Om data har ändrats återställs den genomförda transaktionen.

Mobilappar stöder optimistisk samtidighetskontroll genom att spåra `version` ändringar av varje objekt med hjälp av systemegenskapskolumnen som har definierats för varje tabell i din mobile app-backend. Varje gång en post uppdateras `version` anger Mobilappar egenskapen för posten till ett nytt värde. Under varje uppdateringsbegäran jämförs egenskapen `version` för posten som ingår i begäran med samma egenskap för posten på servern. Om den version som skickades med begäran inte matchar serveringen, väcker klientbiblioteket ett `MobileServicePreconditionFailedException<T>` undantag. Den typ som ingår i undantaget är posten från serveringen som innehåller serverhanteringsversionen av posten. Programmet kan sedan använda den här informationen för att avgöra `version` om uppdateringsbegäran ska köras igen med rätt värde från backend för att genomföra ändringar.

Definiera en kolumn i tabellklassen för systemegenskapen `version` för att aktivera optimistisk samtidighet. Ett exempel:

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

Program som använder oskrivna tabeller möjliggör optimistisk `Version` samtidighet `SystemProperties` genom att ställa in flaggan på tabellens i tabellen enligt följande.

```csharp
//Enable optimistic concurrency by retrieving version
todoTable.SystemProperties |= MobileServiceSystemProperties.Version;
```

Förutom att aktivera optimistisk samtidighet måste du också `MobileServicePreconditionFailedException<T>` fånga upp undantaget i koden när du [anropar UpdateAsync].  Lös konflikten genom att `version` tillämpa rätt på den uppdaterade posten och anropa [UpdateAsync] med den lösta posten. Följande kod visar hur du löser en skrivkonflikt när den har upptäckts:

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

Mer information finns i avsnittet [Offlinedatasynkronisering i Azure Mobile Apps.]

### <a name="how-to-bind-mobile-apps-data-to-a-windows-user-interface"></a><a name="binding"></a>Så här binder du mobile apps-data till ett Windows-användargränssnitt
I det här avsnittet visas hur du visar returnerade dataobjekt med gränssnittselement i en Windows-app.  Följande exempelkod binder till listans källa med en fråga om ofullständiga objekt. [MobileServiceCollection] skapar en bindningssamling för mobilappar.

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

Vissa kontroller i den hanterade körningen stöder ett gränssnitt som kallas [ISupportIncrementalLoading]. Det här gränssnittet gör det möjligt att begära extra data när användaren rullar. Det finns inbyggt stöd för det här gränssnittet för universella Windows-appar via [MobileServiceIncrementalLoadingCollection], som automatiskt hanterar anrop från kontrollerna. Använd `MobileServiceIncrementalLoadingCollection` i Windows-appar på följande sätt:

```csharp
MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
items = todoTable.Where(todoItem => todoItem.Complete == false).ToIncrementalLoadingCollection();

ListBox lb = new ListBox();
lb.ItemsSource = items;
```

Om du vill använda den nya samlingen i Windows Phone `ToCollection` 8- och "Silverlight"-appar använder du tilläggsmetoderna på `IMobileServiceTableQuery<T>` och `IMobileServiceTable<T>`. Om du vill `LoadMoreItemsAsync()`läsa in data ringer du .

```csharp
MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection();
await items.LoadMoreItemsAsync();
```

När du använder samlingen `ToCollectionAsync` som `ToCollection`skapas genom att anropa eller får du en samling som kan bindas till gränssnittskontroller.  Den här samlingen är växlingsmedveten.  Eftersom samlingen läser in data från nätverket misslyckas inläsningen ibland. Om du vill hantera `OnException` sådana `MobileServiceIncrementalLoadingCollection` fel åsidosätter du `LoadMoreItemsAsync`metoden för att hantera undantag som följer av anrop till .

Fundera över om tabellen har många fält men du bara vill visa några av dem i kontrollen. Du kan använda vägledningen i föregående avsnitt "[Välj specifika kolumner](#selecting)" för att välja specifika kolumner som ska visas i användargränssnittet.

### <a name="change-the-page-size"></a><a name="pagesize"></a>Ändra sidstorlek
Azure Mobile Apps returnerar högst 50 objekt per begäran som standard.  Du kan ändra växlingsstorleken genom att öka den maximala sidstorleken på både klienten och servern.  Om du vill öka `PullOptions` den `PullAsync()`begärda sidstorleken anger du när du använder:

```csharp
PullOptions pullOptions = new PullOptions
    {
        MaxPageSize = 100
    };
```

Förutsatt att du `PageSize` har gjort lika med eller större än 100 inom servern returnerar en begäran upp till 100 objekt.

## <a name="work-with-offline-tables"></a><a name="#offlinesync"></a>Arbeta med offlinetabeller
Offlinetabeller använder ett lokalt SQLite-arkiv för att lagra data för användning när de är offline.  Alla tabellåtgärder utförs mot det lokala SQLite-arkivet i stället för fjärrserverarkivet.  Om du vill skapa en offlinetabell förbereder du först projektet:

1. Högerklicka på lösningen > **Hantera NuGet-paket för lösning i**Visual Studio och sök sedan efter och installera **Microsoft.Azure.Mobile.Client.SQLiteStore** NuGet-paketet för alla projekt i lösningen.
2. (Valfritt) Installera något av följande SQLite-runtime-paket för att stödja Windows-enheter:

   * **Windows 8.1 Runtime:** Installera [SQLite för Windows 8.1][3].
   * **Windows Phone 8.1:** Installera [SQLite för Windows Phone 8.1][4].
   * **Universell Windows-plattform** Installera [SQLite för Universal Windows][5].
3. (Valfritt). För Windows-enheter klickar du på **Referenser** > **Lägg till referens...**, expanderar **Windows-mappen** > **tillägg**och aktiverar sedan lämplig **SQLite för Windows** SDK tillsammans med Visual **C++ 2013 Runtime for Windows** SDK.
    SQLite SDK-namnen varierar något med varje Windows-plattform.

Innan en tabellreferens kan skapas måste det lokala arkivet förberedas:

```csharp
var store = new MobileServiceSQLiteStore(Constants.OfflineDbPath);
store.DefineTable<TodoItem>();

//Initializes the SyncContext using the default IMobileServiceSyncHandler.
await this.client.SyncContext.InitializeAsync(store);
```

Initiering av store görs normalt omedelbart efter att klienten har skapats.  **OfflineDbPath** ska vara ett filnamn som är lämpligt för användning på alla plattformar som du stöder.  Om sökvägen är en helt kvalificerad sökväg (det vill ha en startstart med ett snedstreck) används den sökvägen.  Om sökvägen inte är helt kvalificerad placeras filen på en plattformsspecifik plats.

* För iOS- och Android-enheter är standardsökvägen mappen "Personliga filer".
* För Windows-enheter är standardsökvägen den programspecifika mappen "AppData".

En tabellreferens kan erhållas med hjälp av `GetSyncTable<>` metoden:

```csharp
var table = client.GetSyncTable<TodoItem>();
```

Du behöver inte autentisera för att använda en offlinetabell.  Du behöver bara autentisera när du kommunicerar med backend-tjänsten.

### <a name="syncing-an-offline-table"></a><a name="syncoffline"></a>Synkronisera en offlinetabell
Offlinetabeller synkroniseras inte med serverdelen som standard.  Synkroniseringen är uppdelad i två delar.  Du kan skicka ändringar separat från hämtning av nya objekt.  Här är en typisk synkroniseringsmetod:

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

Om det första `PullAsync` argumentet som ska vara null används inte inkrementell synkronisering.  Varje synkroniseringsåtgärd hämtar alla poster.

SDK utför en `PushAsync()` implicit innan du drar poster.

Konflikthantering sker `PullAsync()` på en metod.  Du kan hantera konflikter på samma sätt som onlinetabeller.  Konflikten skapas när `PullAsync()` anropas i stället för under infogningen, uppdateringen eller borttagningen. Om flera konflikter inträffar paketeras de till en enda MobileServicePushFailedException.  Hantera varje fel separat.

## <a name="work-with-a-custom-api"></a><a name="#customapi"></a>Arbeta med ett anpassat API
Med ett anpassat API kan du definiera anpassade slutpunkter som visar serverfunktioner som inte mappas till en infogning, uppdatering, borttagning eller läsåtgärd. Genom att använda ett anpassat API kan du ha större kontroll över meddelanden, inklusive att läsa och ange HTTP-meddelanderubriker och definiera ett annat meddelandetextformat än JSON.

Du anropar ett anpassat API genom att anropa en av [Metoderna InvokeApiAsync] på klienten. Följande kodrad skickar till exempel en POST-begäran till **completeAll** API på serverda:

```javascript
var result = await client.InvokeApiAsync<MarkAllResult>("completeAll", System.Net.Http.HttpMethod.Post, null);
```

Det här formuläret är ett typat metodanrop och kräver att **MarkAllResult-returtypen** har definierats. Både maskinskrivna och otypade metoder stöds.

Metoden InvokeApiAsync() prepends '/api/' till API:et som du vill anropa om inte API:et börjar med ett '/'.
Ett exempel:

* `InvokeApiAsync("completeAll",...)`anropar /api/completeAll på serverda
* `InvokeApiAsync("/.auth/me",...)`samtal /.auth/me på backend

Du kan använda InvokeApiAsync för att anropa alla WebAPI, inklusive de WebAPIs som inte har definierats med Azure Mobile Apps.  När du använder InvokeApiAsync() skickas lämpliga rubriker, inklusive autentiseringshuvuden, med begäran.

## <a name="authenticate-users"></a><a name="authentication"></a>Autentisera användare
Mobilappar stöder autentisering och auktorisera appanvändare med hjälp av olika externa identitetsleverantörer: Facebook, Google, Microsoft-konto, Twitter och Azure Active Directory. Du kan ange behörigheter för tabeller för att begränsa åtkomsten för specifika åtgärder till endast autentiserade användare. Du kan också använda identiteten för autentiserade användare för att implementera auktoriseringsregler i serverskript. Mer information finns i självstudierna [Lägg till autentisering till din app].

Två autentiseringsflöden stöds: *klienthanterade* och *serverhanterade* flöde. Det serverhanterade flödet ger den enklaste autentiseringsupplevelsen, eftersom det är beroende av leverantörens webbautentiseringsgränssnitt. Det klienthanterade flödet möjliggör djupare integrering med enhetsspecifika funktioner eftersom det är beroende av leverantörsspecifika enhetsspecifika SDK:er.

> [!NOTE]
> Vi rekommenderar att du använder ett klienthanteradt flöde i dina produktionsappar.

Om du vill konfigurera autentisering måste du registrera appen hos en eller flera identitetsleverantörer.  Identitetsleverantören genererar ett klient-ID och en klienthemlighet för din app.  Dessa värden anges sedan i serverda för att aktivera Azure App Service-autentisering/auktorisering.  Om du vill ha mer information följer du de detaljerade instruktionerna i självstudien [Lägg till autentisering i appen].

Följande avsnitt beskrivs i det här avsnittet:

* [Klienthanterad autentisering](#clientflow)
* [Serverhanterad autentisering](#serverflow)
* [Cachelagring av autentiseringstoken](#caching)

### <a name="client-managed-authentication"></a><a name="clientflow"></a>Klienthanterad autentisering
Din app kan självständigt kontakta identitetsleverantören och sedan tillhandahålla den returnerade token under inloggning med din backend. Med det här klientflödet kan du tillhandahålla en enda inloggningsupplevelse för användare eller hämta ytterligare användardata från identitetsprovidern. Klientflödesautentisering är att föredra framför att använda ett serverflöde eftersom identitetsprovidern SDK ger en mer inbyggd UX-känsla och möjliggör ytterligare anpassning.

Exempel finns för följande autentiseringsmönster för klientflöde:

* [Active Directory-autentiseringsbibliotek](#adal)
* [Facebook eller Google](#client-facebook)

#### <a name="authenticate-users-with-the-active-directory-authentication-library"></a><a name="adal"></a>Autentisera användare med Active Directory-autentiseringsbiblioteket
Du kan använda Active Directory Authentication Library (ADAL) för att initiera användarautentisering från klienten med Azure Active Directory-autentisering.

1. Konfigurera din backend för din mobilapp för AAD-inloggning genom att följa så [här konfigurerar du App Service för Active Directory-inloggningsdoktor.] Se till att slutföra det valfria steget för att registrera ett inbyggt klientprogram.
2. Öppna projektet i Visual Studio eller Xamarin Studio `Microsoft.IdentityModel.Clients.ActiveDirectory` och lägg till en referens till NuGet-paketet. När du söker, inkludera förhandsversioner.
3. Lägg till följande kod i ditt program, enligt den plattform du använder. I varje, gör följande ersättare:

   * Ersätt **INSERT-AUTHORITY-HERE** med namnet på klienten där du har etablerat ditt program. Formatet ska https://login.microsoftonline.com/contoso.onmicrosoft.comvara . Det här värdet kan kopieras från fliken Domän i Din Azure Active Directory i [Azure-portalen].
   * Ersätt **INSERT-RESOURCE-ID-HERE** med klient-ID för din mobilapps backend. Du kan hämta klient-ID:t från fliken **Avancerat** under **Azure Active Directory-inställningar** i portalen.
   * Ersätt **INSERT-CLIENT-ID-HERE** med klient-ID som du kopierade från det inbyggda klientprogrammet.
   * Ersätt **INSERT-REDIRECT-URI-HERE** med webbplatsens *slutpunkt /.auth/login/done* med hjälp av HTTPS-schemat. Det här värdet *https://contoso.azurewebsites.net/.auth/login/done*bör liknas vid .

     Koden som behövs för varje plattform följer:

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

#### <a name="single-sign-on-using-a-token-from-facebook-or-google"></a><a name="client-facebook"></a>Enkel inloggning med hjälp av en token från Facebook eller Google
Du kan använda klientflödet enligt det här kodavsnittet för Facebook eller Google.

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

### <a name="server-managed-authentication"></a><a name="serverflow"></a>Serverhanterad autentisering
När du har registrerat din identitetsleverantör ringer du [metoden LoginAsync] på [MobileServiceClient] med [mobileserviceAuthenticationProvider-värdet] för din leverantör. Följande kod initierar till exempel en inloggning i serverflödet med hjälp av Facebook.

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

Om du använder en annan identitetsleverantör än Facebook ändrar du värdet för [MobileServiceAuthenticationProvider] till värdet för din leverantör.

I ett serverflöde hanterar Azure App Service OAuth-autentiseringsflödet genom att visa inloggningssidan för den valda providern.  När identitetsleverantören returnerar genererar Azure App Service en apptjänstautentiseringstoken. Metoden [LoginAsync] returnerar en [MobileServiceUser], som ger både [UserId] för den autentiserade användaren och [MobileServiceAuthenticationToken], som en JSON-webbtoken (JWT). Den här token kan cachelagras och återanvändas tills den upphör att gälla. Mer information finns i [Cachelagring av autentiseringstoken](#caching).

### <a name="caching-the-authentication-token"></a><a name="caching"></a>Cachelagring av autentiseringstoken
I vissa fall kan anropet till inloggningsmetoden undvikas efter den första lyckade autentiseringen genom att lagra autentiseringstoken från providern.  Microsoft Store- och UWP-appar kan använda [PasswordVault] för att cachelagra den aktuella autentiseringstoken efter en lyckad inloggning enligt följande:

```csharp
await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook);

PasswordVault vault = new PasswordVault();
vault.Add(new PasswordCredential("Facebook", client.currentUser.UserId,
    client.currentUser.MobileServiceAuthenticationToken));
```

UserId-värdet lagras som användarnamn för autentiseringsuppgifterna och token lagras som lösenord. Vid efterföljande startningar kan du kontrollera **PasswordVault** för cachelagrade autentiseringsuppgifter. I följande exempel används cachelagrade autentiseringsuppgifter när de hittas och försöker på annat sätt autentisera igen med backend:

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

När du loggar ut en användare måste du också ta bort den lagrade autentiseringsuppgifterna enligt följande:

```csharp
client.Logout();
vault.Remove(vault.Retrieve("Facebook", client.currentUser.UserId));
```

Xamarin-appar använder [Xamarin.Auth-API:erna] för att lagra autentiseringsuppgifter på ett säkert sätt i ett **kontoobjekt.** Ett exempel på hur du använder dessa API:er finns i [AuthStore.cs] kodfilen i exemplet med fotodelning i [ContosoMoments](https://github.com/azure-appservice-samples/ContosoMoments).

När du använder klienthanterad autentisering kan du också cachelagra åtkomsttoken som erhållits från din leverantör, till exempel Facebook eller Twitter. Den här token kan levereras för att begära en ny autentiseringstoken från serveringsfel, enligt följande:

```csharp
var token = new JObject();
// Replace <your_access_token_value> with actual value of your access token
token.Add("access_token", "<your_access_token_value>");

// Authenticate using the access token.
await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
```

## <a name="push-notifications"></a><a name="pushnotifications"></a>Push-meddelanden
Följande avsnitt omfattar push-meddelanden:

* [Registrera dig för push-meddelanden](#register-for-push)
* [Skaffa ett SID-paket för Microsoft Store](#package-sid)
* [Registrera dig med plattformsoberoende mallar](#register-xplat)

### <a name="how-to-register-for-push-notifications"></a><a name="register-for-push"></a>Så här registrerar du dig för push-meddelanden
Med Mobile Apps-klienten kan du registrera dig för push-meddelanden med Azure Notification Hubs. När du registrerar dig får du ett handtag som du får från den plattformsspecifika Push Notification Service (PNS). Du anger sedan det här värdet tillsammans med eventuella taggar när du skapar registreringen. Följande kod registrerar din Windows-app för push-meddelanden med Windows Notification Service (WNS):

```csharp
private async void InitNotificationsAsync()
{
    // Request a push notification channel.
    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    // Register for notifications using the new channel.
    await MobileService.GetPush().RegisterNativeAsync(channel.Uri, null);
}
```

Om du skickar till WNS måste du [skaffa ett SID-paket för Microsoft Store.](#package-sid)  Mer information om Windows-appar, inklusive hur du registrerar dig för mallregistreringar, finns i [Lägga till push-meddelanden i appen].

Det går inte att begära taggar från klienten.  Taggbegäranden tas bort tyst från registreringen.
Om du vill registrera enheten med taggar skapar du ett anpassat API som använder API:et för meddelandehubbar för att utföra registreringen för din räkning.  Anropa det anpassade API:et `RegisterNativeAsync()` i stället för metoden.

### <a name="how-to-obtain-a-microsoft-store-package-sid"></a><a name="package-sid"></a>Så här skaffar du ett SID-paket för Microsoft Store
Ett paket SID behövs för att aktivera push-meddelanden i Microsoft Store-appar.  Om du vill ta emot ett paket-SID registrerar du programmet hos Microsoft Store.

Så här hämtar du det här värdet:

1. Högerklicka på Microsoft Store-appprojektet i Visual Studio Solution Explorer, klicka på **Store** > **Associera-app med Store...**.
2. Klicka på **Nästa**i guiden, logga in med ditt Microsoft-konto, skriv ett namn på appen i **Reservera ett nytt appnamn**och klicka sedan på **Reservera**.
3. När appregistreringen har skapats markerar du appnamnet, klickar på **Nästa**och klickar sedan på **Associera**.
4. Logga in på [Windows Dev Center] med ditt Microsoft-konto. Klicka på den appregistrering du skapade under **Mina appar.**
5. Klicka på **App management** > **App-identitet**och bläddra sedan nedåt för att hitta ditt **package SID**.

Många användningsområden för paketet SID behandla det som en URI, i vilket fall du behöver använda *ms-app://* som schema. Anteckna den version av ditt paket SID som bildas genom att sammanfoga det här värdet som ett prefix.

Xamarin-appar kräver ytterligare kod för att kunna registrera en app som körs på iOS- eller Android-plattformarna. Mer information finns i avsnittet för din plattform:

* [Xamarin.Android](app-service-mobile-xamarin-android-get-started-push.md#add-push)
* [Xamarin.iOS](app-service-mobile-xamarin-ios-get-started-push.md#add-push-notifications-to-your-app)

### <a name="how-to-register-push-templates-to-send-cross-platform-notifications"></a><a name="register-xplat"></a>Så här registrerar du push-mallar för att skicka meddelanden över flera plattformar
Om du vill registrera `RegisterAsync()` mallar använder du metoden med mallarna enligt följande:

```csharp
JObject templates = myTemplates();
MobileService.GetPush().RegisterAsync(channel.Uri, templates);
```

Mallarna ska `JObject` vara typer och innehålla flera mallar i följande JSON-format:

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

Metoden **RegisterAsync()** accepterar även sekundära paneler:

```csharp
MobileService.GetPush().RegisterAsync(string channelUri, JObject templates, JObject secondaryTiles);
```

Alla taggar tas bort under registreringen för säkerhet. Information om hur du lägger till taggar i installationer eller mallar i installationer finns i [Arbeta med .NET-server för server för server för servernamn för Azure Mobile Apps].

Om du vill skicka meddelanden som använder dessa registrerade mallar läser du [API:erna för Notification Hubs].

## <a name="miscellaneous-topics"></a><a name="misc"></a>Diverse ämnen
### <a name="how-to-handle-errors"></a><a name="errors"></a>Så här: Hantera fel
När ett fel inträffar i backend, klienten `MobileServiceInvalidOperationException`SDK höjer en .  I följande exempel visas hur du hanterar ett undantag som returneras av backend:

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

Ett annat exempel på hur du hanterar felvillkor finns i [exemplet med filer i mobilappar]. [Exemplet LoggingHandler] innehåller en loggningsombudshanterare för att logga de begäranden som görs till backend.

### <a name="how-to-customize-request-headers"></a><a name="headers"></a>Så här anpassar du begäranden
För att stödja ditt specifika appscenario kan du behöva anpassa kommunikationen med mobile app-backend. Du kanske till exempel vill lägga till ett anpassat huvud i varje utgående begäran eller till och med ändra svarsstatuskoder. Du kan använda en anpassad [DelegatingHandler], som i följande exempel:

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
[Konfigurera App Service för Active Directory-inloggning]: ../app-service/configure-authentication-provider-aad.md

<!-- Microsoft URLs. -->
[MobileServiceKollektion]: https://msdn.microsoft.com/library/azure/dn250636(v=azure.10).aspx
[MobileServiceIncrementalLoadingCollection]: https://msdn.microsoft.com/library/azure/dn268408(v=azure.10).aspx
[MobileServiceAuthenticationProvider]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider(v=azure.10).aspx
[MobileServiceUser]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser(v=azure.10).aspx
[MobileServiceAuthenticationToken]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken(v=azure.10).aspx
[GetTable (GetTable)]: https://msdn.microsoft.com/library/azure/jj554275(v=azure.10).aspx
[skapar en referens till en otypad tabell]: https://msdn.microsoft.com/library/azure/jj554278(v=azure.10).aspx
[DeleteAsync]: https://msdn.microsoft.com/library/azure/dn296407(v=azure.10).aspx
[InkluderaTotalCount]: https://msdn.microsoft.com/library/azure/dn250560(v=azure.10).aspx
[InsertAsync]: https://msdn.microsoft.com/library/azure/dn296400(v=azure.10).aspx
[Anropa ApiAsync]: https://msdn.microsoft.com/library/azure/dn268343(v=azure.10).aspx
[LoginAsync]: https://msdn.microsoft.com/library/azure/dn296411(v=azure.10).aspx
[LookupAsync]: https://msdn.microsoft.com/library/azure/jj871654(v=azure.10).aspx
[Orderby]: https://msdn.microsoft.com/library/azure/dn250572(v=azure.10).aspx
[OrderByDescending]: https://msdn.microsoft.com/library/azure/dn250568(v=azure.10).aspx
[LäsAsynkra]: https://msdn.microsoft.com/library/azure/mt691741(v=azure.10).aspx
[Ta]: https://msdn.microsoft.com/library/azure/dn250574(v=azure.10).aspx
[Välj]: https://msdn.microsoft.com/library/azure/dn250569(v=azure.10).aspx
[Hoppa över]: https://msdn.microsoft.com/library/azure/dn250573(v=azure.10).aspx
[UpdateAsync (UppdateringAsync)]: https://msdn.microsoft.com/library/azure/dn250536.(v=azure.10)aspx
[Userid]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid(v=azure.10).aspx
[Där]: https://msdn.microsoft.com/library/azure/dn250579(v=azure.10).aspx
[Azure-portal]: https://portal.azure.com/
[AktiveraQueryAttribute]: https://msdn.microsoft.com/library/system.web.http.odata.enablequeryattribute.aspx
[Guid.NewGuid (guid.newguid)]: https://msdn.microsoft.com/library/system.guid.newguid(v=vs.110).aspx
[ISupportIncrementalLoading]: https://msdn.microsoft.com/library/windows/apps/Hh701916.aspx
[Windows Dev Center]: https://dev.windows.com/overview
[DelegeraHandler]: https://msdn.microsoft.com/library/system.net.http.delegatinghandler(v=vs.110).aspx
[LösenordVault]: https://msdn.microsoft.com/library/windows/apps/windows.security.credentials.passwordvault.aspx
[ProtectedData]: https://msdn.microsoft.com/library/system.security.cryptography.protecteddata%28VS.95%29.aspx
[API:er för meddelandehubbar]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[Exempel på filer i mobilappar]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files
[LoggningHandler]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files/blob/master/src/client/MobileAppsFilesSample/Helpers/LoggingHandler.cs#L63

<!-- External URLs -->
[OData v3 Dokumentation]: https://www.odata.org/documentation/odata-version-3-0/
[Fiddler]: https://www.telerik.com/fiddler
[Json.NET]: https://www.newtonsoft.com/json
[Xamarin.Auth (på andra sätt)]: https://components.xamarin.com/view/xamarin.auth/
[AuthStore.cs]: https://github.com/azure-appservice-samples/ContosoMoments
[ContosoMoments photo sharing sample]: https://github.com/azure-appservice-samples/ContosoMoments
