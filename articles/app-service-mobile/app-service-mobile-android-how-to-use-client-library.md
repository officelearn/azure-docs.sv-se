---
title: Hur du använder Azure Mobile Apps-SDK för Android | Microsoft Docs
description: Hur du använder Azure Mobile Apps-SDK för Android
services: app-service\mobile
documentationcenter: android
author: conceptdev
manager: crdun
ms.assetid: 5352d1e4-7685-4a11-aaf4-10bd2fa9f9fc
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 11/16/2017
ms.author: crdun
ms.openlocfilehash: d89aa308ab8f6684cebbec49bbefdcb54d77c886
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
---
# <a name="how-to-use-the-azure-mobile-apps-sdk-for-android"></a>Hur du använder Azure Mobile Apps-SDK för Android

Den här guiden visar hur du använder Android klient-SDK för Mobile Apps för att implementera vanliga scenarier som:

* Frågar efter data (Infoga, uppdatera och ta bort).
* Autentisering.
* Hantera fel.
* Anpassning av klienten.

Den här guiden fokuserar på klientsidan Android SDK.  Läs mer om serversidan-SDK: er för Mobile Apps i [arbeta med .NET-serverdel SDK] [ 10] eller [hur du använder SDK för Node.js-serverdel][11].

## <a name="reference-documentation"></a>Referensdokumentationen

Du hittar den [Javadocs API-referens] [ 12] för Android klientbiblioteket på GitHub.

## <a name="supported-platforms"></a>Plattformar som stöds

Azure Mobile Apps-SDK för Android stöder API nivåer 19 till 24 (KitKat via Nougat) för Telefoner och surfplattor formfaktorer.  Autentisering, i synnerhet använder en gemensam web framework strategi för att samla in autentiseringsuppgifter.  Server-flöde autentisering fungerar inte med liten faktor enheter, till exempel ur.

## <a name="setup-and-prerequisites"></a>Installationen och förutsättningar

Slutför den [Mobile Apps quickstart](app-service-mobile-android-get-started.md) kursen.  Den här åtgärden säkerställer att alla förutsättningar för att utveckla Azure Mobile Apps har uppfyllts.  Snabbstart hjälper dig att konfigurera ditt konto och skapa din första mobilappsserverdel.

Om du inte att slutföra Snabbstartsguide kan du utföra följande åtgärder:

* [Skapa en mobilappsserverdel] [ 13] ska användas med din Android-app.
* I Android Studio [uppdatering av Gradle skapa filer](#gradle-build).
* [Aktivera internet behörigheten](#enable-internet).

### <a name="gradle-build"></a>Uppdatera Gradle build-filen

Ändra både **build.gradle** filer:

1. Lägg till denna kod till den *projekt* nivå **build.gradle** filen i den *buildscript* tagg:

    ```text
    buildscript {
        repositories {
            jcenter()
        }
    }
    ```

2. Lägg till denna kod till den *modulen app* nivå **build.gradle** filen i den *beroenden* tagg:

    ```text
    compile 'com.microsoft.azure:azure-mobile-android:3.4.0@aar'
    ```

    Den senaste versionen är för närvarande 3.4.0. Versionerna som stöds anges [på bintray][14].

### <a name="enable-internet"></a>Aktivera internet behörighet

För att komma åt Azure måste appen ha behörigheten INTERNET aktiverad. Om den inte redan är aktiverat lägger du till följande rad med kod till din **AndroidManifest.xml** fil:

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

## <a name="create-a-client-connection"></a>Skapa en klientanslutning

Azure Mobile Apps innehåller fyra funktioner i mobila programmet:

* Dataåtkomst och offlinesynkronisering med en Azure Mobile Apps-tjänst.
* Anropa anpassade API: er skrivs med Azure Mobile Apps Server SDK.
* Autentisering med Azure App Service-autentisering och auktorisering.
* Registrera push-meddelande med Notification Hubs.

Dessa funktioner först måste du skapa en `MobileServiceClient` objekt.  Endast en `MobileServiceClient` objekt ska skapas i din mobila klienten (det vill säga att det ska vara en Singleton-mönster).  Så här skapar du en `MobileServiceClient` objekt:

```java
MobileServiceClient mClient = new MobileServiceClient(
    "<MobileAppUrl>",       // Replace with the Site URL
    this);                  // Your application Context
```

Den `<MobileAppUrl>` är en sträng eller ett objekt i URL som pekar på din mobila serverdel.  Om du använder Azure App Service som värd för din mobila serverdel sedan kontrollera att du använder den säkra `https://` version av URL: en.

Klienten kräver också tillgång till aktivitet eller kontext - den `this` parameter i exemplet.  MobileServiceClient konstruktionen ska ske inom den `onCreate()` metod för aktiviteten som refereras till i den `AndroidManifest.xml` filen.

Som bästa praxis bör du abstrakt serverkommunikation i sin egen (singleton-mönster)-klassen.  I det här fallet bör du överföra aktiviteten i konstruktorn för att konfigurera tjänsten på lämpligt sätt.  Exempel:

```java
package com.example.appname.services;

import android.content.Context;
import com.microsoft.windowsazure.mobileservices.*;

public class AzureServiceAdapter {
    private String mMobileBackendUrl = "https://myappname.azurewebsites.net";
    private Context mContext;
    private MobileServiceClient mClient;
    private static AzureServiceAdapter mInstance = null;

    private AzureServiceAdapter(Context context) {
        mContext = context;
        mClient = new MobileServiceClient(mMobileBackendUrl, mContext);
    }

    public static void Initialize(Context context) {
        if (mInstance == null) {
            mInstance = new AzureServiceAdapter(context);
        } else {
            throw new IllegalStateException("AzureServiceAdapter is already initialized");
        }
    }

    public static AzureServiceAdapter getInstance() {
        if (mInstance == null) {
            throw new IllegalStateException("AzureServiceAdapter is not initialized");
        }
        return mInstance;
    }

    public MobileServiceClient getClient() {
        return mClient;
    }

    // Place any public methods that operate on mClient here.
}
```

Nu kan du anropa `AzureServiceAdapter.Initialize(this);` i den `onCreate()` metod för den huvudsakliga aktiviteten.  Andra metoder som behöver åtkomst till klienten använder `AzureServiceAdapter.getInstance();` att hämta en referens till tjänsten kortet.

## <a name="data-operations"></a>Dataåtgärder

Kärnan i Azure Mobile Apps-SDK är att ge åtkomst till data som lagras i SQL Azure på serverdelen för Mobilappen.  Du kan komma åt dessa data med strikt typkontroll klasser (rekommenderas) eller utan angiven frågor (rekommenderas inte).  Den största delen av det här avsnittet behandlar med strikt typkontroll klasser.

### <a name="define-client-data-classes"></a>Definiera dataklasser som klienten

Definiera klienten dataklasser som är kopplade till tabeller i serverdelen för Mobilappen för att komma åt data från SQL Azure-tabeller. Exemplen i det här avsnittet förutsätter att en tabell med namnet **MyDataTable**, som innehåller följande kolumner:

* id
* Text
* Slutför

Motsvarande skrivna klientsidan objektet finns i en fil med namnet **MyDataTable.java**:

```java
public class ToDoItem {
    private String id;
    private String text;
    private Boolean complete;
}
```

Lägg till get och set-metoder för varje fält som du lägger till.  Om din SQL Azure-tabellen innehåller fler kolumner, och Lägg till motsvarande fält på den här klassen.  Till exempel om DTO (data transfer objekt) hade en heltalskolumn prioritet och sedan kan du lägga till det här fältet och dess get- och set-metoder:

```java
private Integer priority;

/**
* Returns the item priority
*/
public Integer getPriority() {
    return mPriority;
}

/**
* Sets the item priority
*
* @param priority
*            priority to set
*/
public final void setPriority(Integer priority) {
    mPriority = priority;
}
```

Information om hur du skapar ytterligare tabeller i Mobile Apps-serverdel finns [så här: definiera en tabell styrenhet] [ 15] (.NET-serverdel) eller [definiera tabeller med en dynamisk Schema] [ 16] (Node.js-serverdel).

En Azure Mobile Apps serverdel tabell definierar fem särskilda fält fyra som är tillgängliga för klienter:

* `String id`: Globalt unikt ID för posten.  Som bästa praxis, se id strängrepresentation av en [UUID] [ 17] objekt.
* `DateTimeOffset updatedAt`: Det datum/tid för senaste uppdateringen.  Fältet updatedAt anges av servern och aldrig ställas in av din klientkod.
* `DateTimeOffset createdAt`: Det datum/tid som objektet skapades.  Fältet createdAt anges av servern och aldrig ställas in av din klientkod.
* `byte[] version`: Normalt representeras som en sträng, anges versionen också av servern.
* `boolean deleted`: Anger att posten har tagits bort men inte bort ännu.  Använd inte `deleted` som en egenskap i klassen.

Den `id` fältet är obligatoriskt.  Den `updatedAt` fält och `version` används för offlinesynkronisering (för matchning av inkrementell synkronisering och konflikt respektive).  Den `createdAt` fältet är en referensfält som inte används av klienten.  Namnen är ”över överföring” namnen på egenskaperna och är inte ställas in.  Du kan dock skapa en mappning mellan objektet och ”över överföring”-namn med hjälp av den [gson] [ 3] bibliotek.  Exempel:

```java
package com.example.zumoappname;

import com.microsoft.windowsazure.mobileservices.table.DateTimeOffset;

public class ToDoItem
{
    @com.google.gson.annotations.SerializedName("id")
    private String mId;
    public String getId() { return mId; }
    public final void setId(String id) { mId = id; }

    @com.google.gson.annotations.SerializedName("complete")
    private boolean mComplete;
    public boolean isComplete() { return mComplete; }
    public void setComplete(boolean complete) { mComplete = complete; }

    @com.google.gson.annotations.SerializedName("text")
    private String mText;
    public String getText() { return mText; }
    public final void setText(String text) { mText = text; }

    @com.google.gson.annotations.SerializedName("createdAt")
    private DateTimeOffset mCreatedAt;
    public DateTimeOffset getCreatedAt() { return mCreatedAt; }
    protected void setCreatedAt(DateTimeOffset createdAt) { mCreatedAt = createdAt; }

    @com.google.gson.annotations.SerializedName("updatedAt")
    private DateTimeOffset mUpdatedAt;
    public DateTimeOffset getUpdatedAt() { return mUpdatedAt; }
    protected void setUpdatedAt(DateTimeOffset updatedAt) { mUpdatedAt = updatedAt; }

    @com.google.gson.annotations.SerializedName("version")
    private String mVersion;
    public String getVersion() { return mVersion; }
    public final void setVersion(String version) { mVersion = version; }

    public ToDoItem() { }

    public ToDoItem(String id, String text) {
        this.setId(id);
        this.setText(text);
    }

    @Override
    public boolean equals(Object o) {
        return o instanceof ToDoItem && ((ToDoItem) o).mId == mId;
    }

    @Override
    public String toString() {
        return getText();
    }
}
```

### <a name="create-a-table-reference"></a>Skapa en tabellreferens

Om du vill få åtkomst till en tabell måste först skapa en [MobileServiceTable] [ 8] objekt genom att anropa den **getTable** -metoden i den [MobileServiceClient] [9].  Den här metoden har två överlagringar:

```java
public class MobileServiceClient {
    public <E> MobileServiceTable<E> getTable(Class<E> clazz);
    public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
}
```

I följande kod **mClient** är en referens till MobileServiceClient-objektet.  Första överlagring används där klassnamnet och tabellens namn är desamma, och är den som används i Snabbstart:

```java
MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);
```

Andra överlagring används när tabellens namn skiljer sig från namnet på klassen: den första parametern är namnet på tabellen.

```java
MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);
```

## <a name="query"></a>Fråga en Backend-tabell

Skaffa först en tabellreferens.  Kör en fråga på tabellreferensen.  En fråga är en kombination av:

* En `.where()` [filtersats](#filtering).
* En `.orderBy()` [ordning satsen](#sorting).
* En `.select()` [fältet markeringen satsen](#selection).
* En `.skip()` och `.top()` för [växlingsbart systemminne resultat](#paging).

Satserna måste vara angiven i ordningen som föregående.

### <a name="filter"></a> Filtrerar resultaten

Den allmänna formen av en fråga är:

```java
List<MyDataTable> results = mDataTable
    // More filters here
    .execute()          // Returns a ListenableFuture<E>
    .get()              // Converts the async into a sync result
```

Föregående exempel returnerar alla resultat (upp till den maximala sidstorleken som angetts av servern).  Den `.execute()` metoden Kör frågan på serverdelen.  Frågan har konverterats till ett [OData v3] [ 19] fråga innan informationen överförs till Mobile Apps-serverdel.  Erhåller konverterar Mobile Apps-serverdel frågan till en SQL-instruktion innan den körs på SQL Azure-instansen.  Eftersom nätverksaktivitet tar en stund, den `.execute()` metoden returnerar en [ `ListenableFuture<E>` ] [ 18].

### <a name="filtering"></a>Filtret returnerade data

Följande frågan returnerar alla objekt från den **ToDoItem** tabellen var **fullständig** är lika med **FALSKT**.

```java
List<ToDoItem> result = mToDoTable
    .where()
    .field("complete").eq(false)
    .execute()
    .get();
```

**mToDoTable** är referens till tabellen Mobiltjänst som vi skapade tidigare.

Definiera ett filter som använder den **där** metodanrop på tabellreferensen. Den **där** metoden följs av en **fältet** metoden följt av en metod som anger logiska predikatet. Möjliga metoder kan predikat **eq** (motsvarar) **ne** (inte lika med), **gt** (större än), **ge** (större än eller lika med), **lt** (minst), **le** (mindre än eller lika med). Dessa metoder kan du jämföra antalet och strängen fält till specifika värden.

Du kan filtrera efter datum. Följande metoder kan du jämföra datumfält hela eller delar av datum: **år**, **månad**, **dag**, **timme**,  **minut**, och **andra**. I följande exempel lägger till ett filter för objekt vars *förfallodatum* är lika med 2013.

```java
List<ToDoItem> results = MToDoTable
    .where()
    .year("due").eq(2013)
    .execute()
    .get();
```

Följande metoder stöder komplexa filter på strängfält: **startsWith**, **endsWith**, **concat**, **delsträngen**, **indexOf**, **ersätta**, **toLower**, **toUpper**, **trim**, och **längd** . Följande exempel filter för tabellen rader där den *text* kolumnen som börjar med ”PRI0”.

```java
List<ToDoItem> results = mToDoTable
    .where()
    .startsWith("text", "PRI0")
    .execute()
    .get();
```

Följande metoder för operatorn stöds på fälten: **lägga till**, **sub**, **mul**, **div**, **mod**, **våning**, **tak**, och **avrunda**. Följande exempel filter för tabellen rader där den **varaktighet** är ett jämnt tal.

```java
List<ToDoItem> results = mToDoTable
    .where()
    .field("duration").mod(2).eq(0)
    .execute()
    .get();
```

Du kan kombinera predikat med metoderna logiska: **och**, **eller** och **inte**. I följande exempel kombinerar två av föregående exempel.

```java
List<ToDoItem> results = mToDoTable
    .where()
    .year("due").eq(2013).and().startsWith("text", "PRI0")
    .execute()
    .get();
```

Gruppera och kapsla logiska operatorer:

```java
List<ToDoItem> results = mToDoTable
    .where()
    .year("due").eq(2013)
    .and(
        startsWith("text", "PRI0")
        .or()
        .field("duration").gt(10)
    )
    .execute().get();
```

Mer detaljerad information och exempel på filtrering finns [utforska informationen i Android klienten frågemodell][20].

### <a name="sorting"></a>Sortera returnerade data

Följande kod returnerar alla objekt från en tabell med **ToDoItems** Sortera stigande efter den *text* fältet. *mToDoTable* är referens till tabellen backend som du skapade tidigare:

```java
List<ToDoItem> results = mToDoTable
    .orderBy("text", QueryOrder.Ascending)
    .execute()
    .get();
```

Den första parametern för den **orderBy** metoden är en sträng som är lika med namnet på fältet som du vill sortera. Den andra parametern använder den **QueryOrder** uppräkningen för att ange om du vill sortera stigande eller fallande.  Om du filtrerar med hjälp av den ***där*** -metoden i ***där*** metod måste anropas innan den ***orderBy*** metod.

### <a name="selection"></a>Markera specifika kolumner

Följande kod visar hur du återställer alla objekt från en tabell med **ToDoItems**, men visar endast den **fullständig** och **text** fält. **mToDoTable** är referens till tabellen backend som vi skapade tidigare.

```java
List<ToDoItemNarrow> result = mToDoTable
    .select("complete", "text")
    .execute()
    .get();
```

Parametrarna för funktionen väljer är sträng namnen på kolumnerna i tabellen som du vill återställa.  Den **Välj** metod måste följa metoder som **där** och **orderBy**. Det kan följas av sidindelning metoder som **hoppa över** och **upp**.

### <a name="paging"></a>Returnera data på sidor

Data är **alltid** returneras i sidor.  Det maximala antalet poster som returneras anges av servern.  Om klienten begär fler poster, returnerar servern det maximala antalet poster.  Som standard är den maximala sidstorleken på servern 50 poster.

Det första exemplet visar hur du väljer de översta fem posterna från en tabell. Frågan returnerar objekten från en tabell med **ToDoItems**. **mToDoTable** är referens till tabellen backend som du skapade tidigare:

```java
List<ToDoItem> result = mToDoTable
    .top(5)
    .execute()
    .get();
```

Här är en fråga som hoppar över de fem första objekt och returnerar sedan nästa fem:

```java
List<ToDoItem> result = mToDoTable
    .skip(5).top(5)
    .execute()
    .get();
```

Om du vill hämta alla poster i en tabell kan du implementera kod för att iterera över alla sidor:

```java
List<MyDataModel> results = new List<MyDataModel>();
int nResults;
do {
    int currentCount = results.size();
    List<MyDataModel> pagedResults = mDataTable
        .skip(currentCount).top(500)
        .execute().get();
    nResults = pagedResults.size();
    if (nResults > 0) {
        results.addAll(pagedResults);
    }
} while (nResults > 0);
```

En begäran om alla poster med den här metoden skapar minst två begäranden till Mobile Apps-serverdel.

> [!TIP]
> Att välja rätt sidstorleken är en avvägning mellan minnesanvändning när begäran pågår, bandbreddsanvändning och fördröjning ta emot data helt.  Standardvärdet (50 poster) är lämplig för alla enheter.  Om du använder uteslutande på större minnesenheter, öka upp till 500.  Vi har hittat som ökar storleken på utöver 500 poster resulterar i oacceptabel fördröjningar och stora minnesproblem.

### <a name="chaining"></a>Så här: sammanfoga frågan metoder

De metoder som används i frågor till backend-tabeller kan sammanfogas. Länkning frågan metoder kan du markera specifika kolumner filtrerade rader som sorteras och växlingsbart systemminne. Du kan skapa komplexa logiska filter.  Varje fråga-metoden returnerar ett frågeobjekt. Avsluta serien av metoder och faktiskt kör frågan genom att anropa den **köra** metod. Exempel:

```java
List<ToDoItem> results = mToDoTable
        .where()
        .year("due").eq(2013)
        .and(
            startsWith("text", "PRI0").or().field("duration").gt(10)
        )
        .orderBy(duration, QueryOrder.Ascending)
        .select("id", "complete", "text", "duration")
        .skip(200).top(100)
        .execute()
        .get();
```

Metoderna länkad fråga måste sorteras på följande sätt:

1. Filtrering (**där**) metoder.
2. Sortering (**orderBy**) metoder.
3. Markeringen (**Välj**) metoder.
4. växling (**hoppa över** och **upp**) metoder.

## <a name="binding"></a>Binda data till användargränssnittet

Databindning omfattar tre komponenter:

* Datakällan
* Skärmlayout
* Kort som kopplar samman två tillsammans.

I vårt exempelkod vi returnera data från tabellen Mobile Apps SQL Azure **ToDoItem** i en matris. Den här aktiviteten är ett vanligt mönster för program.  Databasfrågor returnera ofta en mängd rader som klienten hämtar i en lista eller en matris. I det här exemplet är matrisen datakällan.  Koden anger skärmlayout som definierar vyn för de data som visas på enheten.  Två är bundna tillsammans med en kort, som i den här koden är en utökning av den **ArrayAdapter&lt;ToDoItem&gt;**  klass.

#### <a name="layout"></a>Definiera layouten

Layouten definieras av flera kodavsnitt för XML-koden. En befintlig layout får följande kod visar den **ListView** vi vill fylla i med vår serverdata.

```xml
    <ListView
        android:id="@+id/listViewToDo"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        tools:listitem="@layout/row_list_to_do" >
    </ListView>
```

I föregående kod i *listitem* attribut anger id på layout för en enskild rad i listan. Den här koden anger en kryssruta och tillhörande text och hämtar instansieras en gång för varje objekt i listan. Den här layouten visas inte i **id** fältet och en mer komplicerad layout anger ytterligare fält i vyn. Den här koden finns i den **row_list_to_do.xml** fil.

```java
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="horizontal">
    <CheckBox
        android:id="@+id/checkToDoItem"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/checkbox_text" />
</LinearLayout>
```

#### <a name="adapter"></a>Definiera kortet
Eftersom datakällan för våra vyn är en matris med **ToDoItem**, vi underklass våra kort från en **ArrayAdapter&lt;ToDoItem&gt;**  klass. Den här underklass producerar en vy för varje **ToDoItem** med hjälp av den **row_list_to_do** layout.  I vår kod vi definiera följande klass som är en utökning av den **ArrayAdapter&lt;E&gt;**  klass:

```java
public class ToDoItemAdapter extends ArrayAdapter<ToDoItem> {
}
```

Åsidosätt korten **getView** metod. Exempel:

```
    @Override
    public View getView(int position, View convertView, ViewGroup parent) {
        View row = convertView;

        final ToDoItem currentItem = getItem(position);

        if (row == null) {
            LayoutInflater inflater = ((Activity) mContext).getLayoutInflater();
            row = inflater.inflate(R.layout.row_list_to_do, parent, false);
        }
        row.setTag(currentItem);

        final CheckBox checkBox = (CheckBox) row.findViewById(R.id.checkToDoItem);
        checkBox.setText(currentItem.getText());
        checkBox.setChecked(false);
        checkBox.setEnabled(true);

        checkBox.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View arg0) {
                if (checkBox.isChecked()) {
                    checkBox.setEnabled(false);
                    if (mContext instanceof ToDoActivity) {
                        ToDoActivity activity = (ToDoActivity) mContext;
                        activity.checkItem(currentItem);
                    }
                }
            }
        });
        return row;
    }
```

Vi skapa en instans av den här klassen i vår aktiviteten enligt följande:

```java
    ToDoItemAdapter mAdapter;
    mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
```

Den andra parametern till konstruktorn ToDoItemAdapter är en referens till layouten. Vi kan nu skapa en instans av den **ListView** och tilldela kortet så att den **ListView**.

```java
    ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
    listViewToDo.setAdapter(mAdapter);
```

#### <a name="use-adapter"></a>Använda kort att binda till Användargränssnittet

Du är nu redo att använda databindning. Följande kod visar hur du hämtar objekt i tabellen och fylls det lokala kortet med returnerade artiklar.

```java
    public void showAll(View view) {
        AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final List<ToDoItem> results = mToDoTable.execute().get();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (ToDoItem item : results) {
                                mAdapter.add(item);
                            }
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        };
        runAsyncTask(task);
    }
```

Anropa kortet när du ändrar den **ToDoItem** tabell. Eftersom ändringar görs på en post med basis, kan du hantera en enskild rad i stället för en samling. När du infogar ett objekt kan anropa den **lägga till** metod på kortet; när du tar bort, anropa den **ta bort** metod.

Du hittar ett komplett exempel i den [Android Snabbstartsprojekt][21].

## <a name="inserting"></a>Infoga data i serverdelen

Skapa en instans av en instans av den *ToDoItem* klassen och ange dess egenskaper.

```java
ToDoItem item = new ToDoItem();
item.text = "Test Program";
item.complete = false;
```

Använd sedan **insert()** att infoga ett objekt:

```java
ToDoItem entity = mToDoTable
    .insert(item)       // Returns a ListenableFuture<ToDoItem>
    .get();
```

Returnerade enheten matchar de data som infogats i backend-tabellen med ID och andra värden (som den `createdAt`, `updatedAt`, och `version` fält) inställd på serverdelen.

Mobile Apps tabeller kräver en primärnyckelkolumn med namnet **id**. Den här kolumnen måste vara en sträng. Standardvärdet för kolumnen ID är ett GUID.  Du kan ange andra unika värden, till exempel e-postadresser eller användarnamn. När ett strängvärde ID inte finns för en infogad post är genererar serverdelen en ny GUID.

ID strängvärden ger följande fördelar:

* ID: N kan skapas utan att göra onödig kommunikation till databasen.
* Poster är enklare att koppla från olika tabeller eller databaser.
* ID-värden integrera bättre med en programlogik.

Sträng-ID-värden är **REQUIRED** för offlinesynkronisering support.  Du kan inte ändra ett Id när den är lagrad i databasen.

## <a name="updating"></a>Uppdatera data i en mobil app

Om du vill uppdatera data i en tabell kan skicka det nya objektet ska den **update()** metod.

```java
mToDoTable
    .update(item)   // Returns a ListenableFuture<ToDoItem>
    .get();
```

I det här exemplet *objektet* är en referens till en rad i den *ToDoItem* tabellen som har haft vissa ändringar.  Raden med samma **id** uppdateras.

## <a name="deleting"></a>Ta bort data i en mobil app

Följande kod visar hur du tar bort data från en tabell genom att ange dataobjektet.

```java
mToDoTable
    .delete(item);
```

Du kan också ta bort ett objekt genom att ange den **id** för att ta bort raden.

```java
String myRowId = "2FA404AB-E458-44CD-BC1B-3BC847EF0902";
mToDoTable
    .delete(myRowId);
```

## <a name="lookup"></a>Leta upp ett specifikt objekt-ID: t

Leta upp ett objekt med en specifik **id** med den **LETAUPP()** metod:

```java
ToDoItem result = mToDoTable
    .lookUp("0380BAFB-BCFF-443C-B7D5-30199F730335")
    .get();
```

## <a name="untyped"></a>Så här: arbeta med någon data

Ej typbestämd programmeringsmiljö ger exakt kontroll över JSON-serialisering.  Det finns några vanliga scenarier där kan du använda en ej typbestämd programmeringsmodell. Till exempel om backend-tabellen innehåller många kolumner och du behöver bara att referera till en delmängd av kolumnerna.  Den angivna modellen måste du definiera alla kolumner som definierats i Mobile Apps-serverdel i dataklass.  De flesta av API-anrop för att komma åt data liknar skrivna API-anrop. Den största skillnaden är att i ej typbestämd modellen du anropa metoder på det **MobileServiceJsonTable** objekt, i stället för den **MobileServiceTable** objekt.

### <a name="json_instance"></a>Skapa en instans av en ej typbestämd tabell

Liknar skrivna modellen, börja med att hämta en tabellreferens, men i det här fallet är det en **MobileServicesJsonTable** objekt. Hämta referensen genom att anropa den **getTable** metod i en instans av klienten:

```java
private MobileServiceJsonTable mJsonToDoTable;
//...
mJsonToDoTable = mClient.getTable("ToDoItem");
```

När du har skapat en instans av den **MobileServiceJsonTable**, har nästan samma API: N som är tillgängliga med skrivna programmeringsmiljö. I vissa fall kan ta metoderna som en utan angiven parameter i stället för en typifierad parameter.

### <a name="json_insert"></a>Infoga i en ej typbestämd tabell
Följande kod visar hur du gör en infogning. Det första steget är att skapa en [JsonObject][1], vilket är en del av den [gson] [ 3] bibliotek.

```java
JsonObject jsonItem = new JsonObject();
jsonItem.addProperty("text", "Wake up");
jsonItem.addProperty("complete", false);
```

Använd sedan **insert()** att infoga någon objektet i tabellen.

```java
JsonObject insertedItem = mJsonToDoTable
    .insert(jsonItem)
    .get();
```

Om du behöver hämta infogat objekt-ID, använder du den **getAsJsonPrimitive()** metod.

```java
String id = insertedItem.getAsJsonPrimitive("id").getAsString();
```
### <a name="json_delete"></a>Ta bort från en ej typbestämd tabell
Följande kod visar hur du tar bort en instans, i det här fallet samma instans av en **JsonObject** som skapades i föregående *infoga* exempel. Koden är densamma som i fallet skrivna, men metoden har en annan signatur eftersom den refererar till en **JsonObject**.

```java
mToDoTable
    .delete(insertedItem);
```

Du kan också ta bort en instans direkt med ID:

```java
mToDoTable.delete(ID);
```

### <a name="json_get"></a>Returnera alla rader från en ej typbestämd tabell
Följande kod visar hur du hämtar en hel tabell. Eftersom du använder en JSON-tabellen kan hämta du selektivt endast en del av kolumnerna i tabellen.

```java
public void showAllUntyped(View view) {
    new AsyncTask<Void, Void, Void>() {
        @Override
        protected Void doInBackground(Void... params) {
            try {
                final JsonElement result = mJsonToDoTable.execute().get();
                final JsonArray results = result.getAsJsonArray();
                runOnUiThread(new Runnable() {

                    @Override
                    public void run() {
                        mAdapter.clear();
                        for (JsonElement item : results) {
                            String ID = item.getAsJsonObject().getAsJsonPrimitive("id").getAsString();
                            String mText = item.getAsJsonObject().getAsJsonPrimitive("text").getAsString();
                            Boolean mComplete = item.getAsJsonObject().getAsJsonPrimitive("complete").getAsBoolean();
                            ToDoItem mToDoItem = new ToDoItem();
                            mToDoItem.setId(ID);
                            mToDoItem.setText(mText);
                            mToDoItem.setComplete(mComplete);
                            mAdapter.add(mToDoItem);
                        }
                    }
                });
            } catch (Exception exception) {
                createAndShowDialog(exception, "Error");
            }
            return null;
        }
    }.execute();
}
```

Samma uppsättning filtrering, filtrering och växling metoder som är tillgängliga för den angivna modellen är tillgängliga för modellen som argument.

## <a name="offline-sync"></a>Implementera offlinesynkronisering

Azure Mobile Apps klient-SDK implementerar också offlinesynkronisering av data med hjälp av en SQLite-databas för att spara en kopia av data på servern lokalt.  Åtgärder som utförs på en offline-tabell kräver inte mobil anslutning ska fungera.  Offlinesynkronisering aids återhämtning och prestanda på bekostnad av mer komplex logik för konfliktlösning.  Azure Mobile Apps klient-SDK innehåller följande funktioner:

* Inkrementell synkronisering: Endast uppdaterade och nya poster har hämtats, spara bandbredd och minne.
* Optimistisk samtidighet: Operations antas lyckas.  Konfliktlösning skjuts upp tills uppdateringar utförs på servern.
* Konfliktlösning: SDK identifierar när en motstridig ändring har gjorts på servern och ger hook för att varna användaren.
* Mjuk borttagning: Borttagna poster är markerade tagits bort, så att andra enheter att uppdatera deras offline cache.

### <a name="initialize-offline-sync"></a>Initiera synkronisering Offline

Varje tabell som är offline måste definieras i offline-cachen innan de används.  Normalt görs tabelldefinitionen omedelbart efter att klienten:

```java
AsyncTask<Void, Void, Void> initializeStore(MobileServiceClient mClient)
    throws MobileServiceLocalStoreException, ExecutionException, InterruptedException
{
    AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>() {
        @Override
        protected void doInBackground(Void... params) {
            try {
                MobileServiceSyncContext syncContext = mClient.getSyncContext();
                if (syncContext.isInitialized()) {
                    return null;
                }
                SQLiteLocalStore localStore = new SQLiteLocalStore(mClient.getContext(), "offlineStore", null, 1);

                // Create a table definition.  As a best practice, store this with the model definition and return it via
                // a static method
                Map<String, ColumnDataType> toDoItemDefinition = new HashMap<String, ColumnDataType>();
                toDoItemDefinition.put("id", ColumnDataType.String);
                toDoItemDefinition.put("complete", ColumnDataType.Boolean);
                toDoItemDefinition.put("text", ColumnDataType.String);
                toDoItemDefinition.put("version", ColumnDataType.String);
                toDoItemDefinition.put("updatedAt", ColumnDataType.DateTimeOffset);

                // Now define the table in the local store
                localStore.defineTable("ToDoItem", toDoItemDefinition);

                // Specify a sync handler for conflict resolution
                SimpleSyncHandler handler = new SimpleSyncHandler();

                // Initialize the local store
                syncContext.initialize(localStore, handler).get();
            } catch (final Exception e) {
                createAndShowDialogFromTask(e, "Error");
            }
            return null;
        }
    };
    return runAsyncTask(task);
}
```

### <a name="obtain-a-reference-to-the-offline-cache-table"></a>Hämta en referens till tabellen Cache

Ett online tabell kan du använda `.getTable()`.  För en offline-tabell, använder du `.getSyncTable()`:

```java
MobileServiceSyncTable<ToDoItem> mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);
```

De metoder som är tillgängliga för online-tabeller (inklusive filtrering, sortering, växling, infoga data, uppdatera data och ta bort data) fungerar lika bra på tabellerna online och offline.

### <a name="synchronize-the-local-offline-cache"></a>Synkronisera den lokala cachen för Offline

Synkronisering är inom din app kontroll.  Här är ett exempel synkroniseringsmetoden:

```java
private AsyncTask<Void, Void, Void> sync(MobileServiceClient mClient) {
    AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
        @Override
        protected Void doInBackground(Void... params) {
            try {
                MobileServiceSyncContext syncContext = mClient.getSyncContext();
                syncContext.push().get();
                mToDoTable.pull(null, "todoitem").get();
            } catch (final Exception e) {
                createAndShowDialogFromTask(e, "Error");
            }
            return null;
        }
    };
    return runAsyncTask(task);
}
```

Om ett namn på frågan har angetts för den `.pull(query, queryname)` metoden och inkrementell synkronisering används för att returnera endast poster som har skapats eller ändrats sedan senast slutförts pull.

### <a name="handle-conflicts-during-offline-synchronization"></a>Hantera konflikter under offlinesynkronisering

Om en konflikt uppstår under en `.push()` åtgärd, en `MobileServiceConflictException` genereras.   Utfärdat av server-objektet är inbäddat i undantaget och kan hämtas av `.getItem()` undantaget.  Justera push-meddelandet genom att anropa följande objekt i MobileServiceSyncContext-objektet:

*  `.cancelAndDiscardItem()`
*  `.cancelAndUpdateItem()`
*  `.updateOperationAndItem()`

När alla konflikter markeras som du vill, anropa `.push()` igen för att lösa alla konflikter.

## <a name="custom-api"></a>Anropa anpassade API

En anpassad API kan du definiera anpassade slutpunkter som exponerar serverfunktioner som inte mappas till en infoga, uppdatera, ta bort eller Läsåtgärd. Genom att använda en anpassad API kan ha du mer kontroll över meddelanden, inklusive läsning och ange HTTP-meddelandehuvuden och definiera ett body-meddelandeformat än JSON.

I en Android-klient du anropar den **invokeApi** metod för att anropa anpassade API-slutpunkten. I följande exempel visas hur du anropar en API-slutpunkt med namnet **completeAll**, som returnerar en samlingsklass som heter **MarkAllResult**.

```java
public void completeItem(View view) {
    ListenableFuture<MarkAllResult> result = mClient.invokeApi("completeAll", MarkAllResult.class);
    Futures.addCallback(result, new FutureCallback<MarkAllResult>() {
        @Override
        public void onFailure(Throwable exc) {
            createAndShowDialog((Exception) exc, "Error");
        }

        @Override
        public void onSuccess(MarkAllResult result) {
            createAndShowDialog(result.getCount() + " item(s) marked as complete.", "Completed Items");
            refreshItemsFromTable();
        }
    });
}
```

Den **invokeApi** metoden anropas på klienten, som skickar en POST-begäran till den nya anpassade API. Resultatet som returneras av anpassade API visas i en dialogruta för meddelande som fel. Andra versioner av **invokeApi** kan du om du vill skicka ett objekt i begärandetexten anger HTTP-metoden och skicka frågeparametrar med begäran. Utan angiven versioner av **invokeApi** tillhandahålls också.

## <a name="authentication"></a>Lägg till autentisering i appen

Självstudiekurser beskrivs redan i detalj hur du lägger till dessa funktioner.

Har stöd för Apptjänst [autentisera användarna](app-service-mobile-android-get-started-users.md) med olika externa identitetsleverantörer: Facebook, Google, Account, Twitter och Azure Active Directory. Du kan ange behörigheter på tabeller för att begränsa åtkomsten för specifika åtgärder endast autentiserade användare. Du kan också använda identiteten för autentiserade användare för att implementera auktoriseringsregler i din serverdel.

Två autentisering flöden stöds: en **server** flöde och en **klienten** flöde. Server-flöde ger den enklaste autentiseringsupplevelse som använder webbgränssnitt för identitets-providers.  Inga ytterligare SDK krävs för att implementera flödet för serverautentisering. Flöde för serverautentisering ger inte djupgående integrering i den mobila enheten och rekommenderas endast för bevis på koncept scenarier.

Flödet tillåter djupare integrering med specifika funktioner, till exempel enkel inloggning som använder SDK: er som tillhandahållits av identitetsleverantören.  Exempelvis kan du integrera Facebook SDK i din mobila program.  Den mobila klienten växlingar i Facebook-app och bekräftar din inloggning innan du växlar tillbaka till din mobila app.

Fyra steg krävs för att aktivera autentisering i appen:

* Registrera din app för autentisering med en identitetsleverantör.
* Konfigurera din App Service-serverdel.
* Begränsa tabellbehörigheter till autentiserade användare endast på App Service-serverdelen.
* Lägg till Autentiseringskod i appen.

Du kan ange behörigheter på tabeller för att begränsa åtkomsten för specifika åtgärder endast autentiserade användare. Du kan också använda SID för en autentiserad användare för att ändra begäranden.  Mer information hittar [komma igång med autentisering] och servern ta för SDK-dokumentationen.

### <a name="caching"></a>: Server Autentiseringsflödet

Följande kod startar en server flödet inloggningen med hjälp av Google-providern.  Ytterligare konfiguration krävs på grund av säkerhetskraven för Google-provider:

```java
MobileServiceUser user = mClient.login(MobileServiceAuthenticationProvider.Google, "{url_scheme_of_your_app}", GOOGLE_LOGIN_REQUEST_CODE);
```

Lägg till följande metod i klassen huvudsakliga aktivitet dessutom:

```java
// You can choose any unique number here to differentiate auth providers from each other. Note this is the same code at login() and onActivityResult().
public static final int GOOGLE_LOGIN_REQUEST_CODE = 1;

@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    // When request completes
    if (resultCode == RESULT_OK) {
        // Check the request code matches the one we send in the login request
        if (requestCode == GOOGLE_LOGIN_REQUEST_CODE) {
            MobileServiceActivityResult result = mClient.onActivityResult(data);
            if (result.isLoggedIn()) {
                // login succeeded
                createAndShowDialog(String.format("You are now logged in - %1$2s", mClient.getCurrentUser().getUserId()), "Success");
                createTable();
            } else {
                // login failed, check the error message
                String errorMessage = result.getErrorMessage();
                createAndShowDialog(errorMessage, "Error");
            }
        }
    }
}
```

Den `GOOGLE_LOGIN_REQUEST_CODE` definierats i din huvudsakliga aktivitet används för den `login()` metod och inom den `onActivityResult()` metoden.  Du kan välja alla unikt nummer som samma nummer som används i den `login()` metod och `onActivityResult()` metod.  Om du abstrakt klientkoden till ett service-kort (som visas tidigare) bör du anropa lämpliga-metoder för service-kort.

Du måste också konfigurera projektet för customtabs.  Ange en omdirigerings-URL.  Lägg till följande kodavsnitt till `AndroidManifest.xml`:

```xml
<activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="{url_scheme_of_your_app}" android:host="easyauth.callback"/>
    </intent-filter>
</activity>
```

Lägg till den **redirectUriScheme** till den `build.gradle` filen för tillämpningsprogrammet:

```text
android {
    buildTypes {
        release {
            // … …
            manifestPlaceholders = ['redirectUriScheme': '{url_scheme_of_your_app}://easyauth.callback']
        }
        debug {
            // … …
            manifestPlaceholders = ['redirectUriScheme': '{url_scheme_of_your_app}://easyauth.callback']
        }
    }
}
```

Slutligen lägger du till `com.android.support:customtabs:23.0.1` i beroendelistan i den `build.gradle` filen:

```text
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile 'com.google.code.gson:gson:2.3'
    compile 'com.google.guava:guava:18.0'
    compile 'com.android.support:customtabs:23.0.1'
    compile 'com.squareup.okhttp:okhttp:2.5.0'
    compile 'com.microsoft.azure:azure-mobile-android:3.4.0@aar'
    compile 'com.microsoft.azure:azure-notifications-handler:1.0.1@jar'
}
```

Hämta ID för den inloggade användaren från en **MobileServiceUser** med hjälp av den **getUserId** metod. Ett exempel på hur du använder Futures för att anropa asynkron inloggningen API: er finns [komma igång med autentisering].

> [!WARNING]
> URL-schemat som nämns är skiftlägeskänslig.  Se till att alla förekomster av `{url_scheme_of_you_app}` gemener/versaler.

### <a name="caching"></a>Cache-autentiseringstoken

Cachelagring autentiseringstoken måste du lagra användar-ID och autentiseringstoken lokalt på enheten. Nästa gång appen startas du kontrollera cachen, och om dessa värden finns kan du hoppa över loggen i proceduren och rehydrate klienten med dessa data. Men dessa data är känsligt och lagras krypterad för säkerhet om telefonen blir stulen.  Du kan se en komplett exempel på hur till cache autentiseringstoken i [cachelagra autentisering tokenavsnittet][7].

När du försöker använda en utgången token visas en *401-Ej behörig* svar. Du kan hantera med hjälp av filter-autentiseringsfel.  Filter hantera begäranden till App Service-serverdelen. Filter koden testar svar för en 401, utlöser inloggningsprocessen och återupptar sedan den begäran som genereras av 401.

### <a name="refresh"></a>Använd Uppdateringstoken

Den token som returnerades av Azure App Service-autentisering och auktorisering har en definierad livslängden för en timme.  Du måste autentiseras användaren efter denna period.  Om du använder en långlivade token som du har fått via flödet autentisering kan du autentiseras med Azure App Service-autentisering och auktorisering med samma token.  En annan Azure App Service-token genereras med nya livslängden.

Du kan också registrera providern om du vill använda uppdatera token.  En uppdatera Token är inte alltid tillgängligt.  Det krävs ytterligare konfiguration:

* För **Azure Active Directory**, konfigurera en klienthemlighet för Azure Active Directory-appen.  Ange klienthemligheten i Azure App Service när du konfigurerar Azure Active Directory-autentisering.  När du anropar `.login()`, skicka `response_type=code id_token` som en parameter:

    ```java
    HashMap<String, String> parameters = new HashMap<String, String>();
    parameters.put("response_type", "code id_token");
    MobileServiceUser user = mClient.login
        MobileServiceAuthenticationProvider.AzureActiveDirectory,
        "{url_scheme_of_your_app}",
        AAD_LOGIN_REQUEST_CODE,
        parameters);
    ```

* För **Google**, skickar den `access_type=offline` som en parameter:

    ```java
    HashMap<String, String> parameters = new HashMap<String, String>();
    parameters.put("access_type", "offline");
    MobileServiceUser user = mClient.login
        MobileServiceAuthenticationProvider.Google,
        "{url_scheme_of_your_app}",
        GOOGLE_LOGIN_REQUEST_CODE,
        parameters);
    ```

* För **Account**, Välj den `wl.offline_access` omfång.

Om du vill uppdatera en token ring `.refreshUser()`:

```java
MobileServiceUser user = mClient
    .refreshUser()  // async - returns a ListenableFuture<MobileServiceUser>
    .get();
```

Ett bra tips är att skapa ett filter som identifierar ett 401 svar från servern och försök att uppdatera användar-token.

## <a name="log-in-with-client-flow-authentication"></a>Logga in med klient-flöde för autentisering

Den allmänna processen för att logga in med klient-flöde för autentisering är följande:

* Konfigurera Azure App Service-autentisering och auktorisering som server-flöde för autentisering.
* Integrera autentiseringsprovider SDK för autentisering för att skapa en åtkomst-token.
* Anropa den `.login()` metoden på följande sätt:

    ```java
    JSONObject payload = new JSONObject();
    payload.put("access_token", result.getAccessToken());
    ListenableFuture<MobileServiceUser> mLogin = mClient.login("{provider}", payload.toString());
    Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
        @Override
        public void onFailure(Throwable exc) {
            exc.printStackTrace();
        }
        @Override
        public void onSuccess(MobileServiceUser user) {
            Log.d(TAG, "Login Complete");
        }
    });
    ```

Ersätt den `onSuccess()` metod med oavsett kod som du vill använda på en genomförd inloggning.  Den `{provider}` strängen är en giltig provider: **aad** (Azure Active Directory), **facebook**, **google**, **microsoftaccount**, eller **twitter**.  Om du har implementerat anpassad autentisering, kan du också använda taggen för anpassad autentisering-providern.

### <a name="adal"></a>Autentisera användarna med Active Directory Authentication Library (ADAL)

Du kan använda Active Directory Authentication Library (ADAL) för att logga in användare på ditt program med Azure Active Directory. Med hjälp av en klient flödet för inloggning är ofta bättre än att använda den `loginAsync()` metoder som ger en mer ursprungligt UX känslan och gör det möjligt för ytterligare anpassning.

1. Konfigurera mobilappsserverdelen för AAD-inloggning genom att följa den [konfigurera App Service för Active Directory-inloggningen] [ 22] kursen. Se till att slutföra det valfria steget med att registrera en native client-program.
2. Installera ADAL genom att ändra filen build.gradle för att inkludera följande definitioner:

```
repositories {
    mavenCentral()
    flatDir {
        dirs 'libs'
    }
    maven {
        url "YourLocalMavenRepoPath\\.m2\\repository"
    }
}
packagingOptions {
    exclude 'META-INF/MSFTSIG.RSA'
    exclude 'META-INF/MSFTSIG.SF'
}
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile('com.microsoft.aad:adal:1.1.1') {
        exclude group: 'com.android.support'
    } // Recent version is 1.1.1
    compile 'com.android.support:support-v4:23.0.0'
}
```

1. Lägg till följande kod i ditt program, gör följande ersättningar:

* Ersätt **INSERT-UTFÄRDARE-här** med namnet på klienten som du har etablerat ditt program. Formatet ska vara https://login.microsoftonline.com/contoso.onmicrosoft.com.
* Ersätt **INSERT-resurs-ID-här** med klient-ID för din mobilappsserverdel. Du kan hämta klient-ID från den **Avancerat** fliken **inställningarna för Azure Active Directory** i portalen.
* Ersätt **INSERT-klient-ID-här** med klient-ID som du kopierade från native client-program.
* Ersätt **INSERT-OMDIRIGERINGS-URI-här** med webbplatsens */.auth/login/done* slutpunkten, med hjälp av HTTPS-schema. Det här värdet ska vara liknar *https://contoso.azurewebsites.net/.auth/login/done*.

```java
private AuthenticationContext mContext;

private void authenticate() {
    String authority = "INSERT-AUTHORITY-HERE";
    String resourceId = "INSERT-RESOURCE-ID-HERE";
    String clientId = "INSERT-CLIENT-ID-HERE";
    String redirectUri = "INSERT-REDIRECT-URI-HERE";
    try {
        mContext = new AuthenticationContext(this, authority, true);
        mContext.acquireToken(this, resourceId, clientId, redirectUri, PromptBehavior.Auto, "", callback);
    } catch (Exception exc) {
        exc.printStackTrace();
    }
}

private AuthenticationCallback<AuthenticationResult> callback = new AuthenticationCallback<AuthenticationResult>() {
    @Override
    public void onError(Exception exc) {
        if (exc instanceof AuthenticationException) {
            Log.d(TAG, "Cancelled");
        } else {
            Log.d(TAG, "Authentication error:" + exc.getMessage());
        }
    }

    @Override
    public void onSuccess(AuthenticationResult result) {
        if (result == null || result.getAccessToken() == null
                || result.getAccessToken().isEmpty()) {
            Log.d(TAG, "Token is empty");
        } else {
            try {
                JSONObject payload = new JSONObject();
                payload.put("access_token", result.getAccessToken());
                ListenableFuture<MobileServiceUser> mLogin = mClient.login("aad", payload.toString());
                Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
                    @Override
                    public void onFailure(Throwable exc) {
                        exc.printStackTrace();
                    }
                    @Override
                    public void onSuccess(MobileServiceUser user) {
                        Log.d(TAG, "Login Complete");
                    }
                });
            }
            catch (Exception exc){
                Log.d(TAG, "Authentication error:" + exc.getMessage());
            }
        }
    }
};

@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    super.onActivityResult(requestCode, resultCode, data);
    if (mContext != null) {
        mContext.onActivityResult(requestCode, resultCode, data);
    }
}
```

## <a name="filters"></a>Justera klient-/ serverkommunikation

Klientanslutningen är vanligtvis en grundläggande HTTP-anslutning med hjälp av den underliggande http-bibliotek som medföljer Android SDK.  Det finns flera skäl till varför du vill ändra som:

* Du vill använda ett alternativt HTTP-bibliotek för att justera timeout.
* Du vill ange en förloppsindikator.
* Du vill lägga till en anpassad rubrik för att stödja API management-funktioner.
* Du vill fånga misslyckade svar så att du kan implementera omautentisering.
* Du vill logga backend-begäranden till en analytics-tjänsten.

### <a name="using-an-alternate-http-library"></a>Med hjälp av en annan HTTP-bibliotek

Anropa den `.setAndroidHttpClientFactory()` metoden omedelbart när du har skapat din klient-referens.  Till exempel ange anslutningstidsgränsen för till 60 sekunder (i stället för standard 10 sekunder):

```java
mClient = new MobileServiceClient("https://myappname.azurewebsites.net");
mClient.setAndroidHttpClientFactory(new OkHttpClientFactory() {
    @Override
    public OkHttpClient createOkHttpClient() {
        OkHttpClient client = new OkHttpClinet();
        client.setReadTimeout(60, TimeUnit.SECONDS);
        client.setWriteTimeout(60, TimeUnit.SECONDS);
        return client;
    }
});
```

### <a name="implement-a-progress-filter"></a>Implementera ett Filter för pågår

Du kan implementera en skärningspunkt för varje begäran genom att implementera en `ServiceFilter`.  Följande uppdateringar en förskapad förloppsindikator:

```java
private class ProgressFilter implements ServiceFilter {
    @Override
    public ListenableFuture<ServiceFilterResponse> handleRequest(ServiceFilterRequest request, NextServiceFilterCallback next) {
        final SettableFuture<ServiceFilterResponse> resultFuture = SettableFuture.create();
        runOnUiThread(new Runnable() {
            @Override
            public void run() {
                if (mProgressBar != null) mProgressBar.setVisibility(ProgressBar.VISIBLE);
            }
        });

        ListenableFuture<ServiceFilterResponse> future = next.onNext(request);
        Futures.addCallback(future, new FutureCallback<ServiceFilterResponse>() {
            @Override
            public void onFailure(Throwable e) {
                resultFuture.setException(e);
            }
            @Override
            public void onSuccess(ServiceFilterResponse response) {
                runOnUiThread(new Runnable() {
                    @Override
                    pubic void run() {
                        if (mProgressBar != null)
                            mProgressBar.setVisibility(ProgressBar.GONE);
                    }
                });
                resultFuture.set(response);
            }
        });
        return resultFuture;
    }
}
```

Du kan koppla det här filtret till klienten på följande sätt:

```java
mClient = new MobileServiceClient(applicationUrl).withFilter(new ProgressFilter());
```

### <a name="customize-request-headers"></a>Anpassa huvuden för begäran

Använd följande `ServiceFilter` och bifoga filtret på samma sätt som den `ProgressFilter`:

```java
private class CustomHeaderFilter implements ServiceFilter {
    @Override
    public ListenableFuture<ServiceFilterResponse> handleRequest(ServiceFilterRequest request, NextServiceFilterCallback next) {
        runOnUiThread(new Runnable() {
            @Override
            public void run() {
                request.addHeader("X-APIM-Router", "mobileBackend");
            }
        });
        SettableFuture<ServiceFilterResponse> result = SettableFuture.create();
        try {
            ServiceFilterResponse response = next.onNext(request).get();
            result.set(response);
        } catch (Exception exc) {
            result.setException(exc);
        }
    }
}
```

### <a name="conversions"></a>Konfigurera automatisk serialisering

Du kan ange en konvertering strategi som gäller för alla kolumner med hjälp av den [gson] [ 3] API. Android klientbiblioteket använder [gson] [ 3] i bakgrunden att serialisera Java-objekt till JSON-data innan informationen skickas till Azure App Service.  I följande kod används den **setFieldNamingStrategy()** metod för att ange strategin. Det här exemplet tar bort det första tecknet (en ”m”), och sedan nästa tecken för varje fältnamn. Till exempel skulle den göra ”mId” till ”id”.  Implementera en konvertering strategi för att minska behovet av `SerializedName()` anteckningar i de flesta fält.

```java
FieldNamingStrategy namingStrategy = new FieldNamingStrategy() {
    public String translateName(File field) {
        String name = field.getName();
        return Character.toLowerCase(name.charAt(1)) + name.substring(2);
    }
}

client.setGsonBuilder(
    MobileServiceClient
        .createMobileServiceGsonBuilder()
        .setFieldNamingStrategy(namingStategy)
);
```

Den här koden måste köras innan du skapar en mobil klient referens med hjälp av den **MobileServiceClient**.

<!-- URLs. -->
[Get started with Azure Mobile Apps]: app-service-mobile-android-get-started.md
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[Mobile Services SDK for Android]: http://go.microsoft.com/fwlink/p/?LinkID=717033
[Azure portal]: https://portal.azure.com
[komma igång med autentisering]: app-service-mobile-android-get-started-users.md
[1]: http://google-gson.googlecode.com/svn/trunk/gson/docs/javadocs/com/google/gson/JsonObject.html
[2]: http://hashtagfail.com/post/44606137082/mobile-services-android-serialization-gson
[3]: http://go.microsoft.com/fwlink/p/?LinkId=290801
[4]: http://go.microsoft.com/fwlink/p/?LinkId=296840
[5]: app-service-mobile-android-get-started-push.md
[6]: ../notification-hubs/notification-hubs-push-notification-overview.md#integration-with-app-service-mobile-apps
[7]: app-service-mobile-android-get-started-users.md#cache-tokens
[8]: http://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/table/MobileServiceTable.html
[9]: http://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html
[10]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[11]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[12]: http://azure.github.io/azure-mobile-apps-android-client/
[13]: app-service-mobile-android-get-started.md#create-a-new-azure-mobile-app-backend
[14]: http://go.microsoft.com/fwlink/p/?LinkID=717034
[15]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#define-table-controller
[16]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[17]: https://developer.android.com/reference/java/util/UUID.html
[18]: https://github.com/google/guava/wiki/ListenableFutureExplained
[19]: http://www.odata.org/documentation/odata-version-3-0/
[20]: http://hashtagfail.com/post/46493261719/mobile-services-android-querying
[21]: https://github.com/Azure-Samples/azure-mobile-apps-android-quickstart
[22]: ../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md
[Future]: http://developer.android.com/reference/java/util/concurrent/Future.html
[AsyncTask]: http://developer.android.com/reference/android/os/AsyncTask.html
