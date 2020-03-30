---
title: Så här använder du SDK för Android
description: Så här använder du Azure Mobile Apps SDK för Android
ms.assetid: 5352d1e4-7685-4a11-aaf4-10bd2fa9f9fc
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 52e91d900ce0f22862904695ba8adf463219c469
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249391"
---
# <a name="how-to-use-the-azure-mobile-apps-sdk-for-android"></a>Så här använder du Azure Mobile Apps SDK för Android

Den här guiden visar hur du använder Android-klienten SDK för mobilappar för att implementera vanliga scenarier, till exempel:

* Fråga efter data (infoga, uppdatera och ta bort).
* Autentisering.
* Hantera fel.
* Anpassa klienten.

Den här guiden fokuserar på android SDK på klientsidan.  Mer information om SDK:er på serversidan för mobilappar finns i [Arbeta med .NET-serverdel SDK][10] eller [Så här använder du nod.js-serverdelen SDK][11].

## <a name="reference-documentation"></a>Referensdokumentation

Du hittar [Javadocs API-referens][12] för Android-klientbiblioteket på GitHub.

## <a name="supported-platforms"></a>Plattformar som stöds

Azure Mobile Apps SDK för Android stöder API-nivåerna 19 till 24 (KitKat through Nougat) för telefon- och tablettformfaktorer.  Autentisering, i synnerhet, använder en gemensam webbramverk metod för att samla in autentiseringsuppgifter.  Autentisering med serverflöde fungerar inte med små formfaktorenheter, till exempel klockor.

## <a name="setup-and-prerequisites"></a>Inställningar och förutsättningar

Slutför [snabbstartshandledningen för mobilappar.](app-service-mobile-android-get-started.md)  Den här uppgiften säkerställer att alla förutsättningar för att utveckla Azure Mobile Apps har uppfyllts.  Snabbstart hjälper dig också att konfigurera ditt konto och skapa din första Mobile App-serverdel.

Om du bestämmer dig för att inte slutföra snabbstartshandledningen slutför du följande uppgifter:

* [skapa en backend][13] för mobilapp som du kan använda med din Android-app.
* I Android Studio [uppdaterar du Gradle-byggfilerna](#gradle-build).
* [Aktivera internetbehörighet](#enable-internet).

### <a name="update-the-gradle-build-file"></a><a name="gradle-build"></a>Uppdatera filen Gradle build

Ändra båda **build.gradle-filerna:**

1. Lägg till den här koden i filen **Build.gradle** på *projektnivå:*

    ```gradle
    buildscript {
        repositories {
            jcenter()
            google()
        }
    }

    allprojects {
        repositories {
            jcenter()
            google()
        }
    }
    ```

2. Lägg till den här koden i *modulen app* nivå **build.gradle** fil inuti *beroenden* taggen:

    ```gradle
    implementation 'com.microsoft.azure:azure-mobile-android:3.4.0@aar'
    ```

    För närvarande den senaste versionen är 3.4.0. De versioner som stöds visas [på bintray][14].

### <a name="enable-internet-permission"></a><a name="enable-internet"></a>Aktivera internetbehörighet

För att komma åt Azure måste din app ha INTERNET-behörigheten aktiverad. Om den inte redan är aktiverad lägger du till följande kodrad i filen **AndroidManifest.xml:**

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

## <a name="create-a-client-connection"></a>Skapa en klientanslutning

Azure Mobile Apps innehåller fyra funktioner i ditt mobilapplikation:

* Dataåtkomst och offlinesynkronisering med en Azure Mobile Apps-tjänst.
* Anropa anpassade API:er som skrivits med Azure Mobile Apps Server SDK.
* Autentisering med autentisering och auktorisering av Azure-apptjänster.
* Push Meddelande registrering med meddelandehubbar.

Var och en av dessa `MobileServiceClient` funktioner kräver först att du skapar ett objekt.  Endast `MobileServiceClient` ett objekt ska skapas i din mobila klient (det vill säga det ska vara ett Singleton-mönster).  Så här `MobileServiceClient` skapar du ett objekt:

```java
MobileServiceClient mClient = new MobileServiceClient(
    "<MobileAppUrl>",       // Replace with the Site URL
    this);                  // Your application Context
```

Det `<MobileAppUrl>` är antingen en sträng eller ett URL-objekt som pekar på din mobila backend.  Om du använder Azure App Service för att vara värd för `https://` din mobila backend, se till att du använder den säkra versionen av webbadressen.

Klienten kräver också åtkomst till aktiviteten `this` eller kontexten - parametern i exemplet.  MobileServiceClient-konstruktionen `onCreate()` ska ske inom metoden för `AndroidManifest.xml` aktiviteten som refereras i filen.

Som bästa praxis bör du abstrakt serverkommunikation till sin egen (singleton-pattern) klass.  I det här fallet bör du skicka aktiviteten inom konstruktorn för att konfigurera tjänsten på rätt sätt.  Ett exempel:

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

Du kan `AzureServiceAdapter.Initialize(this);` nu `onCreate()` anropa metoden för din huvudsakliga aktivitet.  Alla andra metoder som behöver `AzureServiceAdapter.getInstance();` åtkomst till klienten använder för att få en referens till servicekortet.

## <a name="data-operations"></a>Dataåtgärder

Kärnan i Azure Mobile Apps SDK är att ge åtkomst till data som lagras i SQL Azure i mobile app-serverd.  Du kan komma åt dessa data med hjälp av starkt skrivna klasser (önskade) eller otypade frågor (rekommenderas inte).  Huvuddelen av det här avsnittet handlar om att använda starkt skrivna klasser.

### <a name="define-client-data-classes"></a>Definiera klientdataklasser

Om du vill komma åt data från SQL Azure-tabeller definierar du klientdataklasser som motsvarar tabellerna i serveringsuppsättningen för mobilappen. Exempel i det här avsnittet förutsätter en tabell med namnet **MyDataTable**, som har följande kolumner:

* id
* text
* slutföra

Motsvarande maskinskrivna klientobjekt finns i en fil som heter **MyDataTable.java**:

```java
public class ToDoItem {
    private String id;
    private String text;
    private Boolean complete;
}
```

Lägg till getter- och setter-metoder för varje fält som du lägger till.  Om din SQL Azure-tabell innehåller fler kolumner lägger du till motsvarande fält i den här klassen.  Om DTO(dataöverföringsobjektet) till exempel hade kolumnen heltal prioritet kan du lägga till det här fältet tillsammans med dess getter- och setter-metoder:

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

Mer information om hur du skapar ytterligare tabeller i backend för mobilappar finns i Så här definierar du [en tabellstyrenhet][15] (.NET-backend) eller [Definiera tabeller med hjälp av ett dynamiskt schema][16] (Node.js backend).

En backend-tabell för Azure Mobile Apps definierar fem specialfält, varav fyra är tillgängliga för klienter:

* `String id`: Det globalt unika ID:et för posten.  På bästa sätt gör du id:et till strängrepresentation av ett [UUID-objekt.][17]
* `DateTimeOffset updatedAt`: Datum/tid för den senaste uppdateringen.  Fältet updatedAt anges av servern och ska aldrig anges av klientkoden.
* `DateTimeOffset createdAt`: Datum/tid då objektet skapades.  Fältet createdAt anges av servern och ska aldrig anges av klientkoden.
* `byte[] version`: Normalt representeras som en sträng, är versionen också inställd av servern.
* `boolean deleted`: Anger att posten har tagits bort men inte rensats ännu.  Använd inte `deleted` som egendom i din klass.

Fältet `id` är obligatoriskt.  Fältet `updatedAt` och `version` fältet används för offlinesynkronisering (för inkrementell synkronisering respektive konfliktlösning).  Fältet `createdAt` är ett referensfält och används inte av klienten.  Namnen är "across-the-wire" namn på egenskaperna och är inte justerbara.  Du kan dock skapa en mappning mellan objektet och namnen "across-the-wire" med hjälp av [gson-biblioteket.][3]  Ett exempel:

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

Om du vill komma åt en tabell skapar du först ett [MobileServiceTable-objekt][8] genom att anropa **metoden getTable** på [MobileServiceClient][9].  Denna metod har två överbelastningar:

```java
public class MobileServiceClient {
    public <E> MobileServiceTable<E> getTable(Class<E> clazz);
    public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
}
```

I följande kod är **mClient** en referens till ditt MobileServiceClient-objekt.  Den första överbelastningen används där klassnamnet och tabellnamnet är desamma och är det som används i snabbstarten:

```java
MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);
```

Den andra överbelastningen används när tabellnamnet skiljer sig från klassnamnet: den första parametern är tabellnamnet.

```java
MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);
```

## <a name="query-a-backend-table"></a><a name="query"></a>Fråga en backend-tabell

Hämta först en tabellreferens.  Kör sedan en fråga på tabellreferensen.  En fråga är valfri kombination av:

* En `.where()` [filtersats](#filtering).
* En `.orderBy()` [beställningsklausul](#sorting).
* En `.select()` [fältvalsklausul](#selection).
* A `.skip()` `.top()` och för [sökta resultat](#paging).

Klausulerna måste presenteras i föregående ordning.

### <a name="filtering-results"></a><a name="filter"></a>Filtrera resultat

Den allmänna formen av en fråga är:

```java
List<MyDataTable> results = mDataTable
    // More filters here
    .execute()          // Returns a ListenableFuture<E>
    .get()              // Converts the async into a sync result
```

I föregående exempel returneras alla resultat (upp till den maximala sidstorlek som angetts av servern).  Metoden `.execute()` kör frågan på backend.  Frågan konverteras till en [OData v3-fråga][19] före överföring till mobile apps-backend.  Vid inleverans konverterar mobile apps-serverdelen frågan till ett SQL-uttryck innan den körs på SQL Azure-instansen.  Eftersom nätverksaktiviteten tar `.execute()` lite tid [`ListenableFuture<E>`][18]returnerar metoden en .

### <a name="filter-returned-data"></a><a name="filtering"></a>Filtrera returnerade data

Följande frågekörning returnerar alla objekt från **tabellen ToDoItem** där **fullständig är** lika **med false**.

```java
List<ToDoItem> result = mToDoTable
    .where()
    .field("complete").eq(false)
    .execute()
    .get();
```

**mToDoTable** är referensen till den mobila tjänsttabell som vi skapade tidigare.

Definiera ett filter med hjälp av **den där** metoden anropar tabellreferensen. **Var** metoden följs av en **fältmetod** följt av en metod som anger det logiska predikatet. Möjliga predikatmetoder inkluderar **eq** (lika), **ne** (inte lika), **gt** (större än), **ge** (större än eller lika med), **lt** (mindre än), **le** (mindre än eller lika med). Med de här metoderna kan du jämföra tal- och strängfält med specifika värden.

Du kan filtrera på datum. Med följande metoder kan du jämföra hela datumfältet eller delar av datumet: **år,** **månad,** **dag,** **timme,** **minut**och **sekund**. I följande exempel läggs ett filter till för artiklar vars *förfallodatum* är lika med 2013.

```java
List<ToDoItem> results = MToDoTable
    .where()
    .year("due").eq(2013)
    .execute()
    .get();
```

Följande metoder stöder komplexa filter i strängfält: **startsWith**, **endsWith**, **concat**, **subString**, **indexOf**, **replace**, **toLower**, **toUpper**, **trim**och **length**. Följande exempel filtrerar efter tabellrader där *textkolumnen* börjar med "PRI0".

```java
List<ToDoItem> results = mToDoTable
    .where()
    .startsWith("text", "PRI0")
    .execute()
    .get();
```

Följande operatörsmetoder stöds på talfält: **lägg till**, **sub**, **mul**, **div**, **mod**, **golv,** **tak**och **runda**. Följande exempel filtrerar efter tabellrader där **varaktigheten** är ett jämnt tal.

```java
List<ToDoItem> results = mToDoTable
    .where()
    .field("duration").mod(2).eq(0)
    .execute()
    .get();
```

Du kan kombinera predikat med dessa logiska metoder: **och**, **eller** och **inte**. I följande exempel kombineras två av de föregående exemplen.

```java
List<ToDoItem> results = mToDoTable
    .where()
    .year("due").eq(2013).and().startsWith("text", "PRI0")
    .execute()
    .get();
```

Logiska operatorer gruppera och kapsla:

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

Mer detaljerad diskussion och exempel på filtrering finns i [Utforska rikedomen i Android-klientfrågemodellen][20].

### <a name="sort-returned-data"></a><a name="sorting"></a>Sortera returnerade data

Följande kod returnerar alla objekt från en tabell **med ToDoItems** sorterade stigande efter *textfältet.* *mToDoTable* är referensen till den backend-tabell som du skapade tidigare:

```java
List<ToDoItem> results = mToDoTable
    .orderBy("text", QueryOrder.Ascending)
    .execute()
    .get();
```

Den första parametern i **metoden orderBy** är en sträng som är lika med namnet på det fält som ska sorteras. Den andra parametern använder **QueryOrder-uppräkningen** för att ange om stigande eller fallande ska sorteras.  Om du filtrerar med hjälp av ***varmetoden*** måste ***varmetoden*** anropas före ***orderBy-metoden.***

### <a name="select-specific-columns"></a><a name="selection"></a>Markera specifika kolumner

Följande kod visar hur du returnerar alla objekt från en tabell **i ToDoItems**, men visar bara **de fullständiga** fälten och **textfälten.** **mToDoTable** är referensen till backend-tabellen som vi skapade tidigare.

```java
List<ToDoItemNarrow> result = mToDoTable
    .select("complete", "text")
    .execute()
    .get();
```

Parametrarna till markeringsfunktionen är strängnamnen för tabellens kolumner som du vill returnera.  Välj **metod** måste följa metoder som **var** och **beställaBy**. Det kan följas av personsökning metoder som **hoppa och** **topp**.

### <a name="return-data-in-pages"></a><a name="paging"></a>Returnera data på sidor

Data returneras **ALLTID** på sidor.  Det maximala antalet poster som returneras anges av servern.  Om klienten begär fler poster returnerar servern det maximala antalet poster.  Som standard är den maximala sidstorleken på servern 50 poster.

Det första exemplet visar hur du väljer de fem översta objekten från en tabell. Frågan returnerar objekten från en tabell **i ToDoItems**. **mToDoTable** är referensen till den backend-tabell som du skapade tidigare:

```java
List<ToDoItem> result = mToDoTable
    .top(5)
    .execute()
    .get();
```

Här är en fråga som hoppar över de fem första objekten och sedan returnerar de kommande fem:

```java
List<ToDoItem> result = mToDoTable
    .skip(5).top(5)
    .execute()
    .get();
```

Om du vill hämta alla poster i en tabell implementerar du kod för att iterera över alla sidor:

```java
List<MyDataModel> results = new ArrayList<>();
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

En begäran om alla poster med den här metoden skapar minst två begäranden till backend för mobilappar.

> [!TIP]
> Att välja rätt sidstorlek är en balans mellan minnesanvändning medan begäran sker, bandbreddsanvändning och fördröjning i att ta emot data helt.  Standardinställningen (50 poster) är lämplig för alla enheter.  Om du uteslutande arbetar på större minnesenheter ökar du upp till 500.  Vi har funnit att öka sidstorleken utöver 500 poster resulterar i oacceptabla förseningar och stora minnesproblem.

### <a name="how-to-concatenate-query-methods"></a><a name="chaining"></a>Så här: Sammanfoga frågemetoder

De metoder som används i fråga backend-tabeller kan sammanfogas. Med kedjande frågemetoder kan du välja specifika kolumner med filtrerade rader som sorteras och sorteras. Du kan skapa komplexa logiska filter.  Varje frågemetod returnerar ett frågeobjekt. Om du vill avsluta serien med metoder **execute** och faktiskt köra frågan anropar du körningsmetoden. Ett exempel:

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

De kedjade frågemetoderna måste ordnas på följande sätt:

1. Filtrering **(var)** metoder.
2. Sortering **(orderBy)** metoder.
3. Markering **(välj)** metoder.
4. metoder för att söka **(hoppa över** och **överst).**

## <a name="bind-data-to-the-user-interface"></a><a name="binding"></a>Binda data till användargränssnittet

Databindning omfattar tre komponenter:

* Datakällan
* Skärmlayouten
* Adaptern som binder ihop de två.

I vår exempelkod returnerar vi data från Mobile Apps SQL Azure-tabellen **ToDoItem** till en matris. Den här aktiviteten är ett vanligt mönster för dataprogram.  Databasfrågor returnerar ofta en samling rader som klienten får i en lista eller matris. I det här exemplet är matrisen datakällan.  Koden anger en skärmlayout som definierar vyn över de data som visas på enheten.  De två är bundna tillsammans med ett kort, som i den här koden är ett tillägg till klassen **ArrayAdapter&lt;&gt; ToDoItem.**

#### <a name="define-the-layout"></a><a name="layout"></a>Definiera layouten

Layouten definieras av flera kodavsnitt med XML-kod. Med tanke på en befintlig layout representerar följande kod den **ListView** vi vill fylla med våra serverdata.

```xml
    <ListView
        android:id="@+id/listViewToDo"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        tools:listitem="@layout/row_list_to_do" >
    </ListView>
```

I föregående kod anger *listitem-attributet* layouten för en enskild rad i listan. Den här koden anger en kryssruta och tillhörande text och instansieras en gång för varje objekt i listan. Den här layouten visar inte **id-fältet** och en mer komplex layout anger ytterligare fält i visningen. Den här koden finns i filen **row_list_to_do.xml.**

```xml
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

#### <a name="define-the-adapter"></a><a name="adapter"></a>Definiera kortet
Eftersom datakällan för vår uppfattning är en matris med **ToDoItem**underklassar vi vår adapter från en **ArrayAdapter&lt;&gt; ToDoItem-klass.** Den här underklassen skapar en vy för varje **ToDoItem** med hjälp av row_list_to_do-layouten. **row_list_to_do**  I vår kod definierar vi följande klass som är en förlängning av **klassen&lt;ArrayAdapter E:&gt; **

```java
public class ToDoItemAdapter extends ArrayAdapter<ToDoItem> {
}
```

Åsidosätt korten **getView-metoden.** Ett exempel:

```java
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

Vi skapar en instans av den här klassen i vår aktivitet enligt följande:

```java
    ToDoItemAdapter mAdapter;
    mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
```

Den andra parametern till ToDoItemAdapter-konstruktorn är en referens till layouten. Vi kan nu instansiera **ListView** och tilldela kortet till **ListView**.

```java
    ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
    listViewToDo.setAdapter(mAdapter);
```

#### <a name="use-the-adapter-to-bind-to-the-ui"></a><a name="use-adapter"></a>Använda kortet som ska bindas till användargränssnittet

Du är nu redo att använda databindning. Följande kod visar hur du hämtar artiklar i tabellen och fyller det lokala kortet med de returnerade artiklarna.

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

Anropa kortet när du ändrar **tabellen ToDoItem.** Eftersom ändringar görs på post för post hanterar du en enda rad i stället för en samling. När du infogar ett objekt anropar du **tilläggsmetoden** på kortet. när du tar bort anropar du **borttagningsmetoden.**

Du hittar ett komplett exempel i [Android Quickstart Project][21].

## <a name="insert-data-into-the-backend"></a><a name="inserting"></a>Infoga data i backend

Instansiera en instans av *klassen ToDoItem* och ange dess egenskaper.

```java
ToDoItem item = new ToDoItem();
item.text = "Test Program";
item.complete = false;
```

Använd sedan **insert()** för att infoga ett objekt:

```java
ToDoItem entity = mToDoTable
    .insert(item)       // Returns a ListenableFuture<ToDoItem>
    .get();
```

Den returnerade entiteten matchar de data som infogats i backend-tabellen, `updatedAt`inklusive `version` ID och alla andra värden (till exempel `createdAt`, och fält) som angetts på backend.

Tabeller med mobilappar kräver en primärnyckelkolumn med namnet **id**. Den här kolumnen måste vara en sträng. Standardvärdet för ID-kolumnen är ett GUID.  Du kan ange andra unika värden, till exempel e-postadresser eller användarnamn. När ett sträng-ID-värde inte anges för en infogad post genererar backend ett nytt GUID.

Sträng-ID-värden ger följande fördelar:

* ID kan genereras utan att göra en rundresa till databasen.
* Poster är enklare att sammanfoga från olika tabeller eller databaser.
* ID-värden integreras bättre med ett programs logik.

Sträng-ID-värden **krävs** för stöd för offlinesynkronisering.  Du kan inte ändra ett ID när det har lagrats i backend-databasen.

## <a name="update-data-in-a-mobile-app"></a><a name="updating"></a>Uppdatera data i en mobilapp

Om du vill uppdatera data i en tabell skickar du det nya objektet till metoden **update().**

```java
mToDoTable
    .update(item)   // Returns a ListenableFuture<ToDoItem>
    .get();
```

I det här exemplet är *objektet* en referens till en rad i *tabellen ToDoItem,* som har gjort vissa ändringar i den.  Raden med samma **id** uppdateras.

## <a name="delete-data-in-a-mobile-app"></a><a name="deleting"></a>Ta bort data i en mobilapp

Följande kod visar hur du tar bort data från en tabell genom att ange dataobjektet.

```java
mToDoTable
    .delete(item);
```

Du kan också ta bort ett objekt genom att ange **id-fältet** för raden som ska tas bort.

```java
String myRowId = "2FA404AB-E458-44CD-BC1B-3BC847EF0902";
mToDoTable
    .delete(myRowId);
```

## <a name="look-up-a-specific-item-by-id"></a><a name="lookup"></a>Slå upp ett visst objekt efter ID

Slå upp ett objekt med ett specifikt **id-fält** med metoden **lookUp():**

```java
ToDoItem result = mToDoTable
    .lookUp("0380BAFB-BCFF-443C-B7D5-30199F730335")
    .get();
```

## <a name="how-to-work-with-untyped-data"></a><a name="untyped"></a>Så här: Arbeta med otypade data

Den otypade programmeringsmodellen ger dig exakt kontroll över JSON-serialisering.  Det finns några vanliga scenarier där du kanske vill använda en otypad programmeringsmodell. Om backend-tabellen till exempel innehåller många kolumner och du bara behöver referera till en delmängd av kolumnerna.  Den maskinskrivna modellen kräver att du definierar alla kolumner som definierats i backend för mobilappar i dataklassen.  De flesta API-anrop för åtkomst till data liknar de maskinskrivna programmeringsanropen. Den största skillnaden är att du i den oskrivna modellen anropar metoder på **MobileServiceJsonTable-objektet** i stället för **MobileServiceTable-objektet.**

### <a name="create-an-instance-of-an-untyped-table"></a><a name="json_instance"></a>Skapa en instans av en otypad tabell

I likhet med den maskinskrivna modellen börjar du med att hämta en tabellreferens, men i det här fallet är det ett **MobileServicesJsonTable-objekt.** Hämta referensen genom att anropa **getTable-metoden** på en instans av klienten:

```java
private MobileServiceJsonTable mJsonToDoTable;
//...
mJsonToDoTable = mClient.getTable("ToDoItem");
```

När du har skapat en instans av **MobileServiceJsonTable**har den praktiskt taget samma API som är tillgängligt som med den maskinskrivna programmeringsmodellen. I vissa fall tar metoderna en otypad parameter i stället för en typad parameter.

### <a name="insert-into-an-untyped-table"></a><a name="json_insert"></a>Infoga i en otypad tabell
Följande kod visar hur du gör en infogning. Det första steget är att skapa en [JsonObject][1], som är en del av [gson][3] biblioteket.

```java
JsonObject jsonItem = new JsonObject();
jsonItem.addProperty("text", "Wake up");
jsonItem.addProperty("complete", false);
```

Använd sedan **insert()** för att infoga det otypade objektet i tabellen.

```java
JsonObject insertedItem = mJsonToDoTable
    .insert(jsonItem)
    .get();
```

Om du behöver hämta ID:t för det infogade objektet använder du metoden **getAsJsonPrimitive().**

```java
String id = insertedItem.getAsJsonPrimitive("id").getAsString();
```
### <a name="delete-from-an-untyped-table"></a><a name="json_delete"></a>Ta bort från en otypad tabell
Följande kod visar hur du tar bort en instans, i det här fallet samma instans av en **JsonObject** som skapades i föregående *infoga exempel.* Koden är samma som med det maskinskrivna skiftet, men metoden har en annan signatur eftersom den refererar till en **JsonObject**.

```java
mToDoTable
    .delete(insertedItem);
```

Du kan också ta bort en instans direkt med hjälp av dess ID:

```java
mToDoTable.delete(ID);
```

### <a name="return-all-rows-from-an-untyped-table"></a><a name="json_get"></a>Returnera alla rader från en otypad tabell
Följande kod visar hur du hämtar en hel tabell. Eftersom du använder en JSON-tabell kan du selektivt bara hämta några av tabellens kolumner.

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

Samma uppsättning filtrerings-, filtrerings- och växlingsmetoder som är tillgängliga för den maskinskrivna modellen är tillgängliga för den otypade modellen.

## <a name="implement-offline-sync"></a><a name="offline-sync"></a>Implementera offlinesynkronisering

Azure Mobile Apps Client SDK implementerar också offlinesynkronisering av data med hjälp av en SQLite-databas för att lagra en kopia av serverdata lokalt.  Åtgärder som utförs på en offline-tabell kräver inte mobil anslutning för att fungera.  Offlinesynkroniseringshjälpmedel i återhämtning och prestanda på bekostnad av mer komplex logik för konfliktlösning.  Azure Mobile Apps Client SDK implementerar följande funktioner:

* Inkrementell synkronisering: Endast uppdaterade och nya poster hämtas, vilket sparar bandbredd och minnesförbrukning.
* Optimistisk samtidighet: Operationer antas lyckas.  Konfliktlösning skjuts upp tills uppdateringar utförs på servern.
* Konfliktlösning: SDK identifierar när en ändring i konflikt har gjorts på servern och tillhandahåller krokar för att varna användaren.
* Mjuk borttagning: Borttagna poster markeras bort, vilket gör att andra enheter kan uppdatera offlinecachen.

### <a name="initialize-offline-sync"></a>Initiera offlinesynkronisering

Varje offlinetabell måste definieras i offlinecachen före användning.  Normalt görs tabelldefinitionen omedelbart efter det att klienten har skapats:

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

### <a name="obtain-a-reference-to-the-offline-cache-table"></a>Skaffa en referens till offlinecachetabellen

För en online-tabell `.getTable()`använder du .  För en offlinetabell `.getSyncTable()`använder du:

```java
MobileServiceSyncTable<ToDoItem> mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);
```

Alla metoder som är tillgängliga för onlinetabeller (inklusive filtrering, sortering, växling, infoga data, uppdatera data och ta bort data) fungerar lika bra på online- och offlinetabeller.

### <a name="synchronize-the-local-offline-cache"></a>Synkronisera den lokala offlinecachen

Synkroniseringen är inom kontrollen av din app.  Här är ett exempel synkroniseringsmetod:

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

Om ett frågenamn anges `.pull(query, queryname)` till metoden används inkrementell synkronisering för att returnera endast poster som har skapats eller ändrats sedan den senaste lyckades dra.

### <a name="handle-conflicts-during-offline-synchronization"></a>Hantera konflikter under offlinesynkronisering

Om en konflikt `.push()` inträffar `MobileServiceConflictException` under en operation, kastas a.   Det serverutfärdade objektet bäddas in i undantaget `.getItem()` och kan hämtas med undantag.  Justera push genom att anropa följande objekt i MobileServiceSyncContext-objektet:

*  `.cancelAndDiscardItem()`
*  `.cancelAndUpdateItem()`
*  `.updateOperationAndItem()`

När alla konflikter har markerats `.push()` som du vill ringer du igen för att lösa alla konflikter.

## <a name="call-a-custom-api"></a><a name="custom-api"></a>Anropa ett anpassat API

Med ett anpassat API kan du definiera anpassade slutpunkter som visar serverfunktioner som inte mappas till en infogning, uppdatering, borttagning eller läsåtgärd. Genom att använda ett anpassat API kan du ha större kontroll över meddelanden, inklusive att läsa och ange HTTP-meddelanderubriker och definiera ett annat meddelandetextformat än JSON.

Från en Android-klient anropar du **metoden invokeApi** för att anropa den anpassade API-slutpunkten. I följande exempel visas hur du anropar en API-slutpunkt med namnet **completeAll**, som returnerar en samlingsklass med namnet **MarkAllResult**.

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

**Metoden invokeApi** anropas på klienten, som skickar en POST-begäran till det nya anpassade API:et. Resultatet som returneras av det anpassade API:et visas i en meddelandedialogruta, liksom eventuella fel. Andra versioner av **invokeApi** kan du skicka ett objekt i begäran brödtexten, ange HTTP-metoden och skicka frågeparametrar med begäran. Otypade versioner av **invokeApi** tillhandahålls också.

## <a name="add-authentication-to-your-app"></a><a name="authentication"></a>Lägg till autentisering i appen

Självstudier beskriver redan i detalj hur du lägger till dessa funktioner.

App Service stöder [autentisering av appanvändare](app-service-mobile-android-get-started-users.md) med hjälp av olika externa identitetsleverantörer: Facebook, Google, Microsoft-konto, Twitter och Azure Active Directory. Du kan ange behörigheter för tabeller för att begränsa åtkomsten för specifika åtgärder till endast autentiserade användare. Du kan också använda identiteten på autentiserade användare för att implementera auktoriseringsregler i din backend.

Två autentiseringsflöden stöds: ett **serverflöde** och ett **klientflöde.** Serverflödet ger den enklaste autentiseringsupplevelsen, eftersom det är beroende av identitetsleverantörernas webbgränssnitt.  Inga ytterligare SDK-filer krävs för att implementera autentisering av serverflöde. Serverflödesautentisering ger inte en djup integrering i den mobila enheten och rekommenderas endast för proof of concept-scenarier.

Klientflödet möjliggör djupare integrering med enhetsspecifika funktioner som enkel inloggning eftersom det är beroende av SDK:er som tillhandahålls av identitetsprovidern.  Du kan till exempel integrera Facebook SDK i din mobilapplikation.  Den mobila klienten växlar till Facebook-appen och bekräftar din inloggning innan du byter tillbaka till din mobilapp.

Fyra steg krävs för att aktivera autentisering i appen:

* Registrera appen för autentisering hos en identitetsleverantör.
* Konfigurera din apptjänsts serverd.
* Begränsa tabellbehörigheter till autentiserade användare endast i apptjänstens servergrupp.
* Lägg till autentiseringskod i appen.

Du kan ange behörigheter för tabeller för att begränsa åtkomsten för specifika åtgärder till endast autentiserade användare. Du kan också använda SID för en autentiserade användare för att ändra begäranden.  Mer information finns i [Kom igång med autentisering] och dokumentationen för Server SDK HOWTO.

### <a name="authentication-server-flow"></a><a name="caching"></a>Autentisering: Serverflöde

Följande kod startar en inloggningsprocess för serverflödet med hjälp av Google-leverantören.  Ytterligare konfiguration krävs på grund av säkerhetskraven för Google-leverantören:

```java
MobileServiceUser user = mClient.login(MobileServiceAuthenticationProvider.Google, "{url_scheme_of_your_app}", GOOGLE_LOGIN_REQUEST_CODE);
```

Lägg dessutom till följande metod i huvudklassen Aktivitet:

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

Definitionen `GOOGLE_LOGIN_REQUEST_CODE` i din huvudsakliga aktivitet `login()` används för `onActivityResult()` metoden och inom metoden.  Du kan välja ett unikt nummer, så länge `login()` samma nummer `onActivityResult()` används inom metoden och metoden.  Om du abstraherar klientkoden till ett servicekort (som tidigare) bör du anropa lämpliga metoder på servicekortet.

Du måste också konfigurera projektet för customtabs.  Ange först en omdirigerings-URL.  Lägg till följande kodavsnitt i: `AndroidManifest.xml`

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

Lägg till **redirectUriScheme** i `build.gradle` filen för ditt program:

```gradle
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

Lägg slutligen `com.android.support:customtabs:28.0.0` till i beroendelistan `build.gradle` i filen:

```gradle
dependencies {
    implementation fileTree(dir: 'libs', include: ['*.jar'])
    implementation 'com.google.code.gson:gson:2.3'
    implementation 'com.google.guava:guava:18.0'
    implementation 'com.android.support:customtabs:28.0.0'
    implementation 'com.squareup.okhttp:okhttp:2.5.0'
    implementation 'com.microsoft.azure:azure-mobile-android:3.4.0@aar'
    implementation 'com.microsoft.azure:azure-notifications-handler:1.0.1@jar'
}
```

Hämta ID:et för den inloggade användaren från en **MobileServiceUser** med metoden **getUserId.** Ett exempel på hur du använder Futures för att anropa asynkrona inloggnings-API:er finns [i Komma igång med autentisering].

> [!WARNING]
> Url-schemat som nämns är skiftlägeskänsligt.  Se till att `{url_scheme_of_you_app}` alla förekomster av matchfall.

### <a name="cache-authentication-tokens"></a><a name="caching"></a>Cacheautentiseringstoken

Cachelagring av autentiseringstoken kräver att du lagrar användar-ID och autentiseringstoken lokalt på enheten. Nästa gång appen startar kontrollerar du cacheminnet, och om dessa värden finns kan du hoppa över inloggningsproceduren och rehydrera klienten med dessa data. Men dessa uppgifter är känsliga, och det bör lagras krypterad för säkerhet om telefonen blir stulen.  Du kan se ett komplett exempel på hur du cachelagrar autentiseringstoken i [avsnittet Cacheautentiseringstoken][7].

När du försöker använda en token som har upphört att gälla får du ett *401 obehörigt* svar. Du kan hantera autentiseringsfel med hjälp av filter.  Filtrerar begäranden om skärningspunkten till apptjänstens backend. Filterkoden testar svaret för en 401, utlöser inloggningsprocessen och återupptar sedan begäran som genererade 401.

### <a name="use-refresh-tokens"></a><a name="refresh"></a>Använda uppdateringstoken

Token som returneras av Autentisering och auktorisering av Azure-apptjänsten har en definierad livstid på en timme.  Efter den här perioden måste du ge användaren ett omautentisering.  Om du använder en långlivad token som du har tagit emot via klientflödesautentisering kan du återauktorisera med Azure App Service Authentication and Authorization med samma token.  En annan Azure App Service-token genereras med en ny livstid.

Du kan också registrera leverantören för att använda Uppdatera token.  En uppdateringstoken är inte alltid tillgänglig.  Ytterligare konfiguration krävs:

* För **Azure Active Directory**konfigurerar du en klienthemlighet för Azure Active Directory-appen.  Ange klienthemligheten i Azure App-tjänsten när du konfigurerar Azure Active Directory-autentisering.  När `.login()`du `response_type=code id_token` ringer , passera som en parameter:

    ```java
    HashMap<String, String> parameters = new HashMap<String, String>();
    parameters.put("response_type", "code id_token");
    MobileServiceUser user = mClient.login
        MobileServiceAuthenticationProvider.AzureActiveDirectory,
        "{url_scheme_of_your_app}",
        AAD_LOGIN_REQUEST_CODE,
        parameters);
    ```

* För **Google**skickar `access_type=offline` du som parameter:

    ```java
    HashMap<String, String> parameters = new HashMap<String, String>();
    parameters.put("access_type", "offline");
    MobileServiceUser user = mClient.login
        MobileServiceAuthenticationProvider.Google,
        "{url_scheme_of_your_app}",
        GOOGLE_LOGIN_REQUEST_CODE,
        parameters);
    ```

* För **Microsoft-konto** `wl.offline_access` väljer du scope.

Om du vill `.refreshUser()`uppdatera en token anropar du:

```java
MobileServiceUser user = mClient
    .refreshUser()  // async - returns a ListenableFuture<MobileServiceUser>
    .get();
```

På bästa sätt kan du skapa ett filter som identifierar ett 401-svar från servern och försöker uppdatera användartoken.

## <a name="log-in-with-client-flow-authentication"></a>Logga in med autentisering med klientflöde

Den allmänna processen för inloggning med klientflödesautentisering är följande:

* Konfigurera Autentisering och auktorisering av Azure App Service så som du skulle kunna autentisering med serverflöde.
* Integrera SDK för autentiseringsprovidern för autentisering för att skapa en åtkomsttoken.
* Anropa `.login()` metoden enligt följande`result` ( `AuthenticationResult`bör vara en ):

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

Se hela kodexemplet i nästa avsnitt.

Ersätt `onSuccess()` metoden med vilken kod du vill använda vid en lyckad inloggning.  Strängen `{provider}` är en giltig leverantör: **aad** (Azure Active Directory), **facebook**, **google**, **microsoftaccount**eller **twitter**.  Om du har implementerat anpassad autentisering kan du också använda taggen anpassad autentiseringsprovider.

### <a name="authenticate-users-with-the-active-directory-authentication-library-adal"></a><a name="adal"></a>Autentisera användare med Active Directory Authentication Library (ADAL)

Du kan använda Active Directory Authentication Library (ADAL) för att logga in användare i ditt program med Azure Active Directory. Använda en klient flöde inloggning är `loginAsync()` ofta att föredra att använda de metoder som det ger en mer inbyggd UX känsla och möjliggör ytterligare anpassning.

1. Konfigurera din backend för mobilappen för AAD-inloggning genom att följa [självstudien Så här konfigurerar du App Service för Active Directory-inloggningsdoktor.][22] Se till att slutföra det valfria steget för att registrera ett inbyggt klientprogram.
2. Installera ADAL genom att ändra filen build.gradle så att den innehåller följande definitioner:

    ```gradle
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
        implementation fileTree(dir: 'libs', include: ['*.jar'])
        implementation('com.microsoft.aad:adal:1.16.1') {
            exclude group: 'com.android.support'
        } // Recent version is 1.16.1
        implementation 'com.android.support:support-v4:28.0.0'
    }
    ```

3. Lägg till följande kod i ditt program och gör följande ersättare:

    * Ersätt **INSERT-AUTHORITY-HERE** med namnet på klienten där du har etablerat ditt program. Formatet ska https://login.microsoftonline.com/contoso.onmicrosoft.comvara .
    * Ersätt **INSERT-RESOURCE-ID-HERE** med klient-ID för din mobilapps backend. Du kan hämta klient-ID:t från fliken **Avancerat** under **Azure Active Directory-inställningar** i portalen.
    * Ersätt **INSERT-CLIENT-ID-HERE** med klient-ID som du kopierade från det inbyggda klientprogrammet.
    * Ersätt **INSERT-REDIRECT-URI-HERE** med webbplatsens *slutpunkt /.auth/login/done* med hjälp av HTTPS-schemat. Det här värdet *https://contoso.azurewebsites.net/.auth/login/done*bör liknas vid .

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

## <a name="adjust-the-client-server-communication"></a><a name="filters"></a>Justera kommunikationen mellan klient och server

Klientanslutningen är normalt en grundläggande HTTP-anslutning med det underliggande HTTP-biblioteket som medföljer Android SDK.  Det finns flera anledningar till varför du vill ändra på det:

* Du vill använda ett alternativt HTTP-bibliotek för att justera tidsutgångar.
* Du vill tillhandahålla en förloppsindikator.
* Du vill lägga till en anpassad rubrik för att stödja API-hanteringsfunktioner.
* Du vill avlyssna ett misslyckat svar så att du kan implementera omautentisering.
* Du vill logga backend-begäranden till en analystjänst.

### <a name="using-an-alternate-http-library"></a>Använda ett alternativt HTTP-bibliotek

Anropa `.setAndroidHttpClientFactory()` metoden direkt efter att du har skapat klientreferensen.  Om du till exempel vill ange tidsgränsen för anslutningen till 60 sekunder (i stället för standard10 sekunder):

```java
mClient = new MobileServiceClient("https://myappname.azurewebsites.net");
mClient.setAndroidHttpClientFactory(new OkHttpClientFactory() {
    @Override
    public OkHttpClient createOkHttpClient() {
        OkHttpClient client = new OkHttpClient();
        client.setReadTimeout(60, TimeUnit.SECONDS);
        client.setWriteTimeout(60, TimeUnit.SECONDS);
        return client;
    }
});
```

### <a name="implement-a-progress-filter"></a>Implementera ett förloppsfilter

Du kan implementera en skärningspunkt `ServiceFilter`för varje begäran genom att implementera en .  Följande uppdaterar till exempel en förskapad förloppsindikator:

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
                    public void run() {
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

Du kan koppla filtret till klienten på följande sätt:

```java
mClient = new MobileServiceClient(applicationUrl).withFilter(new ProgressFilter());
```

### <a name="customize-request-headers"></a>Anpassa målanpassningshuvuden

Använd följande `ServiceFilter` och fäst filtret på `ProgressFilter`samma sätt som:

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

### <a name="configure-automatic-serialization"></a><a name="conversions"></a>Konfigurera automatisk serialisering

Du kan ange en konverteringsstrategi som gäller för varje kolumn med hjälp av [Gson][3] API. Android-klientbiblioteket använder [gson][3] bakom kulisserna för att serialisera Java-objekt till JSON-data innan data skickas till Azure App Service.  Följande kod använder metoden **setFieldNamingStrategy()** för att ange strategin. Det här exemplet tar bort det ursprungliga tecknet (ett "m" och sedan gemener nästa tecken för varje fältnamn. Till exempel skulle det vända "mId" till "id".  Implementera en konverteringsstrategi för `SerializedName()` att minska behovet av anteckningar på de flesta fält.

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
        .setFieldNamingStrategy(namingStrategy)
);
```

Den här koden måste köras innan en mobil klientreferens skapas med **MobileServiceClient**.

<!-- URLs. -->
[Get started with Azure Mobile Apps]: app-service-mobile-android-get-started.md
[ASCII control codes C0 and C1]: https://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[Mobile Services SDK for Android]: https://go.microsoft.com/fwlink/p/?LinkID=717033
[Azure portal]: https://portal.azure.com
[Komma igång med autentisering]: app-service-mobile-android-get-started-users.md
[1]: https://static.javadoc.io/com.google.code.gson/gson/2.8.5/com/google/gson/JsonObject.html
[2]: https://hashtagfail.com/post/44606137082/mobile-services-android-serialization-gson
[3]: https://www.javadoc.io/doc/com.google.code.gson/gson/2.8.5
[4]: https://go.microsoft.com/fwlink/p/?LinkId=296840
[5]: app-service-mobile-android-get-started-push.md
[6]: ../notification-hubs/notification-hubs-push-notification-overview.md#integration-with-app-service-mobile-apps
[7]: app-service-mobile-android-get-started-users.md#cache-tokens
[8]: https://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/table/MobileServiceTable.html
[9]: https://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html
[10]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[11]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[12]: https://azure.github.io/azure-mobile-apps-android-client/
[13]: app-service-mobile-android-get-started.md#create-a-new-azure-mobile-app-backend
[14]: https://go.microsoft.com/fwlink/p/?LinkID=717034
[15]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#define-table-controller
[16]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[17]: https://developer.android.com/reference/java/util/UUID.html
[18]: https://github.com/google/guava/wiki/ListenableFutureExplained
[19]: https://www.odata.org/documentation/odata-version-3-0/
[20]: https://hashtagfail.com/post/46493261719/mobile-services-android-querying
[21]: https://github.com/Azure-Samples/azure-mobile-apps-android-quickstart
[22]: ../app-service/configure-authentication-provider-aad.md
[Future]: https://developer.android.com/reference/java/util/concurrent/Future.html
[AsyncTask]: https://developer.android.com/reference/android/os/AsyncTask.html
