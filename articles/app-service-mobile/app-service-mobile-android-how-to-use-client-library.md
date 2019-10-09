---
title: Så här använder du Azure Mobile Apps SDK för Android | Microsoft Docs
description: Använda Azure Mobile Apps SDK för Android
services: app-service\mobile
documentationcenter: android
author: elamalani
manager: crdun
ms.assetid: 5352d1e4-7685-4a11-aaf4-10bd2fa9f9fc
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: b67e0eaabe63707455eaa6cd4b235ec828dddff3
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2019
ms.locfileid: "72025439"
---
# <a name="how-to-use-the-azure-mobile-apps-sdk-for-android"></a>Använda Azure Mobile Apps SDK för Android

> [!NOTE]
> Visual Studio App Center stöder utveckling av mobila appar från slut punkt till slut punkt och integrerade tjänster. Utvecklare kan använda **bygge**-, **test** -och **distributions** tjänster för att konfigurera kontinuerlig integrering och leverans pipeliner. När appen har distribuerats kan utvecklare övervaka status och användning av appen med hjälp av **analys** -och **diagnos** tjänster och engagera med användare med **push** -tjänsten. Utvecklare kan också utnyttja **auth** för att autentisera sina användare och **data** tjänster för att spara och synkronisera AppData i molnet.
> Om du vill integrera moln tjänster i ditt mobil program kan du registrera dig med App Center [App Center](https://appcenter.ms/signup?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) idag.

Den här guiden visar hur du använder Android client SDK för Mobile Apps för att implementera vanliga scenarier, till exempel:

* Fråga efter data (Infoga, uppdatera och ta bort).
* Anspråksautentisering.
* Hanterings fel.
* Anpassa klienten.

Den här guiden fokuserar på klient sidans Android SDK.  Mer information om SDK: er för Server sidan för Mobile Apps finns i [arbeta med .net][10] -Server dels-SDK eller [så här använder du Node. js Server dels SDK][11].

## <a name="reference-documentation"></a>Referensdokumentation

Du hittar [JAVADOCS API-referensen][12] för Android-klient biblioteket på GitHub.

## <a name="supported-platforms"></a>Plattformar som stöds

Azure Mobile Apps SDK för Android har stöd för API-nivåer 19 till 24 (KitKat till nougat) för telefon-och Tablet form-faktorer.  Autentiseringen använder särskilt en gemensam Web Framework-metod för att samla in autentiseringsuppgifter.  Autentisering med Server flöde fungerar inte med små form faktor enheter, till exempel arm Watcher.

## <a name="setup-and-prerequisites"></a>Konfiguration och krav

Slutför självstudien om [Mobile Apps snabb start](app-service-mobile-android-get-started.md) .  Den här uppgiften säkerställer alla krav för att utveckla Azure-Mobile Apps har uppfyllts.  Snabb starten hjälper dig också att konfigurera ditt konto och skapa din första Server del för mobilappen.

Om du väljer att inte slutföra snabb starts guiden utför du följande aktiviteter:

* [skapa en server][13] del för mobilappar som ska användas med din Android-app.
* [Uppdatera Gradle build-filerna](#gradle-build)i Android Studio.
* [Aktivera Internet behörighet](#enable-internet).

### <a name="gradle-build"></a>Uppdatera Gradle build-filen

Ändra både **build. gradle** -filer:

1. Lägg till den här koden till filen **build. gradle** för *projekt* nivå:

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

2. Lägg till den här koden i *modulen app* Level **build. gradle** i *beroende* taggen:

    ```gradle
    implementation 'com.microsoft.azure:azure-mobile-android:3.4.0@aar'
    ```

    För närvarande är den senaste versionen 3.4.0. De versioner som stöds finns [på bintray][14].

### <a name="enable-internet"></a>Aktivera Internet-behörighet

För att få åtkomst till Azure måste din app ha INTERNET-behörighet aktive rad. Om den inte redan är aktive rad lägger du till följande kodrad i filen **AndroidManifest. XML** :

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

## <a name="create-a-client-connection"></a>Skapa en klient anslutning

Azure Mobile Apps innehåller fyra funktioner i mobil programmet:

* Data åtkomst och offlinesynkronisering med en Azure Mobile Apps-tjänst.
* Anropa anpassade API: er skrivna med Azure Mobile Apps Server SDK.
* Autentisering med Azure App Service autentisering och auktorisering.
* Registrering av push-meddelanden med Notification Hubs.

För var och en av dessa funktioner måste du först skapa ett `MobileServiceClient`-objekt.  Endast ett `MobileServiceClient`-objekt ska skapas i din mobila klient (det vill säga det ska vara ett singleton-mönster).  Så här skapar du ett `MobileServiceClient`-objekt:

```java
MobileServiceClient mClient = new MobileServiceClient(
    "<MobileAppUrl>",       // Replace with the Site URL
    this);                  // Your application Context
```

@No__t-0 är antingen en sträng eller ett URL-objekt som pekar på din mobila Server del.  Om du använder Azure App Service som värd för din mobil Server del kontrollerar du att du använder den säkra `https://`-versionen av URL: en.

Klienten måste också ha åtkomst till aktiviteten eller kontexten – parametern `this` i exemplet.  MobileServiceClient-konstruktionen bör inträffa i metoden `onCreate()` för aktiviteten som refereras i `AndroidManifest.xml`-filen.

Som bästa praxis bör du abstrakt server kommunikation i en egen (singleton-Pattern)-klass.  I det här fallet bör du skicka aktiviteten i konstruktorn för att konfigurera tjänsten på lämpligt sätt.  Exempel:

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

Du kan nu anropa `AzureServiceAdapter.Initialize(this);` i metoden `onCreate()` för din huvud aktivitet.  Andra metoder som behöver åtkomst till klienten använder `AzureServiceAdapter.getInstance();` för att hämta en referens till tjänst kortet.

## <a name="data-operations"></a>Data åtgärder

Kärnan i Azure Mobile Apps SDK är att ge åtkomst till data som lagras i SQL Azure i Server delen för mobilappar.  Du kan komma åt dessa data med hjälp av starkt skrivna klasser (prioriterade) eller ej angivna frågor (rekommenderas inte).  Det här avsnittet handlar om att använda starkt skrivna klasser.

### <a name="define-client-data-classes"></a>Definiera klient data klasser

För att få åtkomst till data från SQL Azure tabeller definierar du de klient data klasser som motsvarar tabellerna i Server delen för mobilappen. Exemplen i det här avsnittet förutsätter en tabell med namnet ' **DataTable**, som innehåller följande kolumner:

* id
* text
* full

Motsvarande typ av objekt på klient sidan finns i en fil med namnet min **DataTable. java**:

```java
public class ToDoItem {
    private String id;
    private String text;
    private Boolean complete;
}
```

Lägg till get-och set-metoder för varje fält som du lägger till.  Om SQL Azures tabellen innehåller fler kolumner lägger du till motsvarande fält i den här klassen.  Om t. ex. DTO (Data Transfer Object) hade en heltals prioritets kolumn kan du lägga till det här fältet tillsammans med dess get-och set-metoder:

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

Information om hur du skapar ytterligare tabeller i Mobile Apps Server del finns i [How till: Definiera en tabell kontroll @ no__t-0 (.NET-Server del) eller [definiera tabeller med ett dynamiskt schema][16] (Node. js-backend).

En Azure Mobile Apps-backend-tabell definierar fem särskilda fält, som är fyra av de som är tillgängliga för klienter:

* `String id`: Det globalt unika ID: t för posten.  Vi rekommenderar att du gör ID: t till sträng representationen av ett [UUID][17] -objekt.
* `DateTimeOffset updatedAt`: Datum/tid för den senaste uppdateringen.  UpdatedAt-fältet anges av servern och ska aldrig anges av klient koden.
* `DateTimeOffset createdAt`: Datum/tid då objektet skapades.  CreatedAt-fältet anges av servern och ska aldrig anges av klient koden.
* `byte[] version`: Som vanligt vis visas som en sträng, anges versionen också av-servern.
* `boolean deleted`: Anger att posten har tagits bort men ännu inte har rensats.  Använd inte `deleted` som en egenskap i klassen.

Fältet `id` är obligatoriskt.  Fälten `updatedAt` och `version` används för offlinesynkronisering (för stegvis synkronisering respektive konflikt lösning).  Fältet `createdAt` är ett referens fält och används inte av klienten.  Namnen är "över-namnet" i egenskaperna och är inte justerbara.  Du kan dock skapa en mappning mellan ditt objekt och namnet "över-kabel" med hjälp av [Gson][3] -biblioteket.  Exempel:

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

### <a name="create-a-table-reference"></a>Skapa en tabell referens

För att få åtkomst till en tabell måste du först skapa ett [MobileServiceTable][8] -objekt genom att anropa metoden **getTable** på [MobileServiceClient][9].  Den här metoden har två överbelastningar:

```java
public class MobileServiceClient {
    public <E> MobileServiceTable<E> getTable(Class<E> clazz);
    public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
}
```

I följande kod är **mClient** en referens till ditt MobileServiceClient-objekt.  Den första överlagringen används där klass namnet och tabell namnet är detsamma, och det som används i snabb starten:

```java
MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);
```

Den andra överlagringen används när tabell namnet skiljer sig från klass namnet: den första parametern är tabellens namn.

```java
MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);
```

## <a name="query"></a>Fråga en server dels tabell

Börja med att hämta en tabell referens.  Kör sedan en fråga på tabell referensen.  En fråga är en valfri kombination av:

* En `.where()` [filter-sats](#filtering).
* En `.orderBy()`- [ordnings sats](#sorting).
* En `.select()` [fält markerings sats](#selection).
* En `.skip()` och `.top()` för [växlade resultat](#paging).

Satserna måste presenteras i föregående ordning.

### <a name="filter"></a>Filtrerings resultat

Den allmänna formen av en fråga är:

```java
List<MyDataTable> results = mDataTable
    // More filters here
    .execute()          // Returns a ListenableFuture<E>
    .get()              // Converts the async into a sync result
```

Föregående exempel returnerar alla resultat (upp till den maximala sid storleken som anges av servern).  Metoden `.execute()` kör frågan på Server delen.  Frågan konverteras till en [OData v3][19] -fråga innan överföring till Mobile Apps Server delen.  Vid mottagande konverterar Mobile Apps Server delen frågan till ett SQL-uttryck innan den körs på SQL Azure-instansen.  Eftersom nätverks aktiviteten tar en stund returnerar metoden `.execute()` en [`ListenableFuture<E>`][18].

### <a name="filtering"></a>Filtrera returnerade data

Följande frågekörningen returnerar alla objekt från **ToDoItem** -tabellen där **Complete** är **false**.

```java
List<ToDoItem> result = mToDoTable
    .where()
    .field("complete").eq(false)
    .execute()
    .get();
```

**mToDoTable** är referensen till den Mobile Service-tabell som vi skapade tidigare.

Definiera ett filter med metod anropet **WHERE** i tabell referensen. **WHERE** -metoden följs av en **fält** metod följt av en metod som anger det logiska predikatet. Möjliga predikat är **EQ** (lika med), **Ne** (inte lika med), **gt** (större än), **ge** (större än eller lika med), **lt** (mindre än), **Le** (mindre än eller lika med). Med dessa metoder kan du jämföra tal-och sträng fält med vissa värden.

Du kan filtrera efter datum. Med följande metoder kan du jämföra hela datum fältet eller delar av datumet: **år**, **månad**, **dag**, **timme**, **minut**och **sekund**. I följande exempel läggs ett filter till för objekt vars *förfallo datum* är lika med 2013.

```java
List<ToDoItem> results = MToDoTable
    .where()
    .year("due").eq(2013)
    .execute()
    .get();
```

Följande metoder stöder komplexa filter i sträng fält: **startsWith**, **endsWith**, **concat**, **substring**, **indexOf**, **replace**, **toLower**, **toUpper**, **trim**och **length** . I följande exempel filtreras tabell rader där *text* kolumnen börjar med "PRI0".

```java
List<ToDoItem> results = mToDoTable
    .where()
    .startsWith("text", "PRI0")
    .execute()
    .get();
```

Följande operator metoder stöds i siffer fält: **Add**, **Sub**, **mul**, **div**, **mod**, **våning**, **tak**och **Round**. I följande exempel filtreras tabell rader där **varaktigheten** är ett jämnt tal.

```java
List<ToDoItem> results = mToDoTable
    .where()
    .field("duration").mod(2).eq(0)
    .execute()
    .get();
```

Du kan kombinera predikat med dessa logiska metoder: **och**, **eller** . I följande exempel kombineras två av föregående exempel.

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

Mer detaljerad information och exempel på filtrering finns i [utforska informationen i Android-klientens fråga modell][20].

### <a name="sorting"></a>Sortera returnerade data

Följande kod returnerar alla objekt från en tabell med **ToDoItems** sorterade stigande efter *textfältet.* *mToDoTable* är referensen till den server dels tabell som du skapade tidigare:

```java
List<ToDoItem> results = mToDoTable
    .orderBy("text", QueryOrder.Ascending)
    .execute()
    .get();
```

Den första parametern för **orderBy** -metoden är en sträng som är lika med namnet på det fält som sorteringen ska sorteras efter. Den andra parametern använder **QueryOrder** -uppräkningen för att ange om stigande eller fallande ska sorteras.  Om du filtrerar med hjälp av metoden ***WHERE*** måste ***WHERE*** -metoden anropas före ***orderBy*** -metoden.

### <a name="selection"></a>Välj vissa kolumner

Följande kod visar hur du returnerar alla objekt från en tabell med **ToDoItems**, men visar bara fälten **fullständig** och **text** . **mToDoTable** är referensen till den server dels tabell som vi skapade tidigare.

```java
List<ToDoItemNarrow> result = mToDoTable
    .select("complete", "text")
    .execute()
    .get();
```

Parametrarna för funktionen Select är sträng namnen för tabellens kolumner som du vill returnera.  Metoden **Select** måste följa metoder som **WHERE** och **orderBy**. Det kan följas av växlings metoder som **Skip** och **Top**.

### <a name="paging"></a>Returnera data på sidor

Data returneras **alltid** i sidor.  Det maximala antalet returnerade poster anges av servern.  Om klienten begär fler poster, returnerar servern det maximala antalet poster.  Som standard är den maximala sid storleken på servern 50 poster.

I det första exemplet visas hur du väljer de översta fem objekten i en tabell. Frågan returnerar objekten från en tabell med **ToDoItems**. **mToDoTable** är referensen till den server dels tabell som du skapade tidigare:

```java
List<ToDoItem> result = mToDoTable
    .top(5)
    .execute()
    .get();
```

Här är en fråga som hoppar över de första fem objekten och returnerar sedan nästa fem:

```java
List<ToDoItem> result = mToDoTable
    .skip(5).top(5)
    .execute()
    .get();
```

Om du vill hämta alla poster i en tabell, implementerar du kod för att iterera över alla sidor:

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

En begäran om alla poster som använder den här metoden skapar minst två begär anden till Mobile Apps Server del.

> [!TIP]
> Att välja rätt sid storlek är en balans mellan minnes användningen medan begäran sker, bandbredds användning och fördröjning när data tas emot fullständigt.  Standard (50 poster) är lämplig för alla enheter.  Om du uteslutande arbetar med större minnes enheter kan du öka upp till 500.  Vi har påträffat att öka sid storleken bortom 500 poster resulterar i oacceptabla fördröjningar och stora minnes problem.

### <a name="chaining"></a>Hur: Sammanfoga fråge metoder

De metoder som används för att fråga Server dels tabeller kan sammanfogas. Genom att länka fråge metoder kan du välja vissa kolumner med filtrerade rader som är sorterade och växlade. Du kan skapa komplexa logiska filter.  Varje fråge metod returnerar ett Query-objekt. Anropa metoden **execute** om du vill avsluta en serie metoder och köra frågan. Exempel:

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

De kedjade fråge metoderna måste ordnas på följande sätt:

1. Filtrerings metoder (**där**).
2. Sorterings metoder (**orderBy**).
3. Urvals metoder (**Select**).
4. växlings-(**Skip** -och **Top**) metoder.

## <a name="binding"></a>Binda data till användar gränssnittet

Data bindningen omfattar tre komponenter:

* Data källan
* Skärmens layout
* Det kort som binder samman de två.

I vår exempel kod returnerar vi data från Mobile Apps SQL Azure tabellen **ToDoItem** till en matris. Den här aktiviteten är ett vanligt mönster för data program.  Databas frågor returnerar ofta en samling rader som klienten får i en lista eller matris. I det här exemplet är matrisen data källan.  Koden anger en skärmlayout som definierar visningen av de data som visas på enheten.  De två är kopplade samman med ett kort, som i den här koden är en utökning av klassen **ArrayAdapter @ no__t-1ToDoItem @ no__t-2** .

#### <a name="layout"></a>Definiera layouten

Layouten definieras av flera kodfragment av XML-kod. Med en befintlig layout representerar följande kod den **ListView** som vi vill fylla med våra Server data.

```xml
    <ListView
        android:id="@+id/listViewToDo"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        tools:listitem="@layout/row_list_to_do" >
    </ListView>
```

I föregående kod anger attributet *ListItem* ID för layouten för en enskild rad i listan. Den här koden anger en kryss ruta och dess tillhör ande text och instansieras en gång för varje objekt i listan. Den här layouten visar inte **ID-** fältet och en mer komplex layout anger ytterligare fält i visningen. Den här koden finns i filen **row_list_to_do. XML** .

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

#### <a name="adapter"></a>Definiera kortet
Eftersom data källan för vår vy är en matris med **ToDoItem**, underklasserar vi vårt kort från en **ArrayAdapter @ no__t-2ToDoItem @ no__t-3-** klass. Den här underklassen genererar en vy för varje **ToDoItem** med **row_list_to_do** -layouten.  I vår kod definierar vi följande klass som är en utökning av klassen **ArrayAdapter @ no__t-1e @ no__t-2** :

```java
public class ToDoItemAdapter extends ArrayAdapter<ToDoItem> {
}
```

Åsidosätt **getView** -metoden för adaptrar. Exempel:

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

Den andra parametern för ToDoItemAdapter-konstruktorn är en referens till layouten. Nu kan vi instansiera **ListView** och tilldela kortet till **ListView**.

```java
    ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
    listViewToDo.setAdapter(mAdapter);
```

#### <a name="use-adapter"></a>Använd nätverkskortet för att binda till användar gränssnittet

Du är nu redo att använda data bindning. Följande kod visar hur du hämtar objekt i tabellen och fyller det lokala kortet med de returnerade objekten.

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

Anropa kortet när som helst när du ändrar **ToDoItem** -tabellen. Eftersom ändringar utförs på en post efter post, hanterar du en enskild rad i stället för en samling. När du infogar ett objekt anropar du metoden **Add** på kortet. anropa **Remove** -metoden när du tar bort den.

Du kan hitta ett fullständigt exempel i [projektet för Android-snabb start][21].

## <a name="inserting"></a>Infoga data i Server delen

Instansiera en instans av klassen *ToDoItem* och ange dess egenskaper.

```java
ToDoItem item = new ToDoItem();
item.text = "Test Program";
item.complete = false;
```

Använd sedan **Infoga ()** för att infoga ett objekt:

```java
ToDoItem entity = mToDoTable
    .insert(item)       // Returns a ListenableFuture<ToDoItem>
    .get();
```

Den returnerade entiteten matchar de data som infogats i Server dels tabellen, inklusive ID och andra värden (till exempel fälten `createdAt`, `updatedAt` och @no__t 2) i Server delen.

Mobile Apps tabeller kräver en primär nyckel kolumn med namnet **ID**. Den här kolumnen måste vara en sträng. Standardvärdet för ID-kolumnen är ett GUID.  Du kan ange andra unika värden, till exempel e-postadresser eller användar namn. När ett sträng-ID-värde inte anges för en infogad post genererar Server delen ett nytt GUID.

Sträng-ID-värden ger följande fördelar:

* ID: n kan genereras utan att göra en tur och retur i databasen.
* Poster är enklare att sammanfoga från olika tabeller och databaser.
* ID-värden integreras bättre med programmets logik.

Sträng-ID-värden **krävs** för stöd för offline-synkronisering.  Du kan inte ändra ett ID när det är lagrat i backend-databasen.

## <a name="updating"></a>Uppdatera data i en mobilapp

Om du vill uppdatera data i en tabell skickar du det nya objektet till **Update ()-** metoden.

```java
mToDoTable
    .update(item)   // Returns a ListenableFuture<ToDoItem>
    .get();
```

I det här exemplet är *objektet* en referens till en rad i tabellen *ToDoItem* , som har gjort några ändringar i den.  Raden med samma **ID** uppdateras.

## <a name="deleting"></a>Ta bort data i en mobilapp

Följande kod visar hur du tar bort data från en tabell genom att ange data objekt.

```java
mToDoTable
    .delete(item);
```

Du kan också ta bort ett objekt genom att ange fältet **ID** för raden som ska tas bort.

```java
String myRowId = "2FA404AB-E458-44CD-BC1B-3BC847EF0902";
mToDoTable
    .delete(myRowId);
```

## <a name="lookup"></a>Leta upp ett speciellt objekt efter ID

Leta upp ett objekt med ett angivet **ID-** fält med **lookUp ()** -metoden:

```java
ToDoItem result = mToDoTable
    .lookUp("0380BAFB-BCFF-443C-B7D5-30199F730335")
    .get();
```

## <a name="untyped"></a>Hur: Arbeta med data utan typ

Den avskrivna programmerings modellen ger dig exakt kontroll över JSON-serialisering.  Det finns några vanliga scenarier där du kanske vill använda en typ som inte är typ av programmerings modell. Om din server dels tabell till exempel innehåller många kolumner och du bara behöver referera till en delmängd av kolumnerna.  Den inskrivna modellen kräver att du definierar alla kolumner som definierats i Mobile Apps Server del i din data klass.  De flesta API-anrop för att komma åt data liknar de skrivna programmerings anropen. Den största skillnaden är att i den modell utan typ som du anropar metoder i **MobileServiceJsonTable** -objektet, i stället för **MobileServiceTable** -objektet.

### <a name="json_instance"></a>Skapa en instans av en tabell som inte är av typen

Precis som den typ av modell som skrivs in börjar du med att hämta en tabell referens, men i det här fallet är det ett **MobileServicesJsonTable** -objekt. Hämta referensen genom att anropa metoden **getTable** på en klient instans:

```java
private MobileServiceJsonTable mJsonToDoTable;
//...
mJsonToDoTable = mClient.getTable("ToDoItem");
```

När du har skapat en instans av **MobileServiceJsonTable**har den praktiskt taget samma API tillgängligt som i den skrivna programmerings modellen. I vissa fall tar metoderna en parameter utan typ i stället för en angiven parameter.

### <a name="json_insert"></a>Infoga i en tabell som inte är av typen
Följande kod visar hur du gör en infogning. Det första steget är att skapa en [JsonObject][1], som är en del av [Gson][3] -biblioteket.

```java
JsonObject jsonItem = new JsonObject();
jsonItem.addProperty("text", "Wake up");
jsonItem.addProperty("complete", false);
```

Använd sedan **Infoga ()** för att infoga det inskrivna objektet i tabellen.

```java
JsonObject insertedItem = mJsonToDoTable
    .insert(jsonItem)
    .get();
```

Om du behöver hämta ID för det infogade objektet använder du metoden **getAsJsonPrimitive ()** .

```java
String id = insertedItem.getAsJsonPrimitive("id").getAsString();
```
### <a name="json_delete"></a>Ta bort från en tabell som inte är av typen
Följande kod visar hur du tar bort en instans, i det här fallet samma instans av en **JsonObject** som skapades i föregående *insert* -exempel. Koden är samma som för det skrivna fallet, men metoden har en annan signatur eftersom den refererar till en **JsonObject**.

```java
mToDoTable
    .delete(insertedItem);
```

Du kan också ta bort en instans direkt genom att använda dess ID:

```java
mToDoTable.delete(ID);
```

### <a name="json_get"></a>Returnera alla rader från en tabell som inte är av typen
Följande kod visar hur du hämtar en hel tabell. Eftersom du använder en JSON-tabell kan du selektivt hämta några av tabellens kolumner.

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

Samma uppsättning filtrerings-, filtrerings-och växlings metoder som är tillgängliga för den inskrivna modellen är tillgängliga för den ej angivna modellen.

## <a name="offline-sync"></a>Implementera offline-synkronisering

Azure Mobile Apps client SDK implementerar också offline-synkronisering av data genom att använda en SQLite-databas för att lagra en kopia av Server data lokalt.  Åtgärder som utförs på en offline-tabell kräver inte att mobila anslutningar fungerar.  Offline Sync hjälper till med återhämtning och prestanda på bekostnad av mer komplex logik för konflikt lösning.  Azure Mobile Apps client SDK implementerar följande funktioner:

* Stegvis synkronisering: Endast uppdaterade och nya poster har laddats ned, spara bandbredd och minnes användning.
* Optimistisk samtidighet: Åtgärder antas utföras.  Konflikt lösning uppskjuts tills uppdateringar utförs på servern.
* Konflikt lösning: SDK identifierar när en motstridig ändring har gjorts på servern och ger hookar för att varna användaren.
* Mjuk borttagning: Borttagna poster har marker ATS som borttagna, så att andra enheter kan uppdatera offline-cacheminnet.

### <a name="initialize-offline-sync"></a>Initiera synkronisering offline

Varje offline-tabell måste definieras i offline-cachen före användning.  Normalt görs tabell definition direkt efter att klienten har skapats:

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

### <a name="obtain-a-reference-to-the-offline-cache-table"></a>Hämta en referens till tabellen offline-cache

För en online-tabell använder du `.getTable()`.  Använd `.getSyncTable()` för en offline-tabell:

```java
MobileServiceSyncTable<ToDoItem> mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);
```

Alla metoder som är tillgängliga för online-tabeller (inklusive filtrering, sortering, växling, infoga data, uppdatera data och ta bort data) fungerar lika bra på online-och offline-tabeller.

### <a name="synchronize-the-local-offline-cache"></a>Synkronisera den lokala offline-cachen

Synkronisering är i kontroll över din app.  Här är ett exempel på en synkroniseringsmetod:

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

Om ett frågenamn har angetts till `.pull(query, queryname)`-metoden används stegvis synkronisering för att returnera endast poster som har skapats eller ändrats sedan den senaste hämtningen.

### <a name="handle-conflicts-during-offline-synchronization"></a>Hantera konflikter under offlinesynkronisering

Om en konflikt uppstår under en `.push()`-åtgärd genereras en `MobileServiceConflictException`.   Det Server-utfärdade objektet är inbäddat i undantaget och kan hämtas av `.getItem()` i undantaget.  Justera push genom att anropa följande objekt på MobileServiceSyncContext-objektet:

*  `.cancelAndDiscardItem()`
*  `.cancelAndUpdateItem()`
*  `.updateOperationAndItem()`

När alla konflikter har marker ATS som du vill kan du anropa `.push()` igen för att lösa alla konflikter.

## <a name="custom-api"></a>Anropa en anpassad API

Med ett anpassat API kan du definiera anpassade slut punkter som visar Server funktioner som inte mappas till en INSERT-, Update-, DELETE-eller Read-åtgärd. Genom att använda ett anpassat API kan du ha mer kontroll över meddelanden, inklusive läsa och ställa in HTTP-meddelandehuvuden och definiera ett annat text format än JSON.

Från en Android-klient anropar du **invokeApi** -metoden för att anropa den anpassade API-slutpunkten. Följande exempel visar hur du anropar en API-slutpunkt med namnet **completeAll**, som returnerar en samlings klass med namnet **MarkAllResult**.

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

Metoden **invokeApi** anropas på klienten, som skickar en post-begäran till det nya anpassade API: et. Resultatet som returneras av det anpassade API: t visas i en meddelande dialog ruta, som alla fel. Med andra versioner av **invokeApi** kan du välja att skicka ett objekt i begär ande texten, ange http-metoden och skicka frågeparametrar med begäran. **InvokeApi** -versioner som inte har angetts tillhandahålls också.

## <a name="authentication"></a>Lägg till autentisering i din app

Självstudierna beskriver redan detaljerad information om hur du lägger till dessa funktioner.

App Service stöder [autentisering av App-användare](app-service-mobile-android-get-started-users.md) med olika externa identitets leverantörer: Facebook, Google, Microsoft-konto, Twitter och Azure Active Directory. Du kan ange behörigheter för tabeller för att begränsa åtkomsten för vissa åtgärder till endast autentiserade användare. Du kan också använda identiteten för autentiserade användare för att implementera auktoriseringsregler i Server delen.

Två autentiserings flöden stöds: ett **Server** flöde och ett **klient** flöde. Server flödet ger den enklaste autentiseringen, eftersom det förlitar sig på webb gränssnittet för identitets leverantörer.  Det krävs inga ytterligare SDK: er för att implementera autentisering av Server flöde. Autentisering av Server flöde ger inte en djupgående integrering i den mobila enheten och rekommenderas bara för koncept bevis scenarier.

Klient flödet möjliggör djupare integrering med enhetsspecifika funktioner, till exempel enkel inloggning eftersom det är beroende av SDK: er som tillhandahålls av identitets leverantören.  Du kan till exempel integrera Facebook SDK i det mobila programmet.  Den mobila klienten byter till Facebook-appen och bekräftar din inloggning innan du byter tillbaka till mobilappen.

Fyra steg krävs för att aktivera autentisering i din app:

* Registrera din app för autentisering med en identitets leverantör.
* Konfigurera din App Service server del.
* Begränsa tabell behörigheter till autentiserade användare endast på den App Service server delen.
* Lägg till en autentiseringsnyckel till din app.

Du kan ange behörigheter för tabeller för att begränsa åtkomsten för vissa åtgärder till endast autentiserade användare. Du kan också använda SID: t för en autentiserad användare för att ändra begär Anden.  Mer information finns i [Kom igång med autentisering] och howto-dokumentationen för Server SDK.

### <a name="caching"></a>Anspråksautentisering Server flöde

Följande kod startar en inloggnings process för Server flöde med Google-providern.  Ytterligare konfiguration krävs på grund av säkerhets kraven för Google-providern:

```java
MobileServiceUser user = mClient.login(MobileServiceAuthenticationProvider.Google, "{url_scheme_of_your_app}", GOOGLE_LOGIN_REQUEST_CODE);
```

Lägg också till följande metod i huvud aktivitets klassen:

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

Den `GOOGLE_LOGIN_REQUEST_CODE` som definieras i din huvud aktivitet används för metoden `login()` och inom `onActivityResult()`-metoden.  Du kan välja ett unikt nummer, så länge samma nummer används inom metoden `login()` och metoden `onActivityResult()`.  Om du sammanfattar klient koden i ett tjänst kort (som visas tidigare) bör du anropa lämpliga metoder på tjänst kortet.

Du måste också konfigurera projektet för customtabs.  Ange först en omdirigerings-URL.  Lägg till följande kodfragment i `AndroidManifest.xml`:

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

Lägg till **redirectUriScheme** i `build.gradle`-filen för programmet:

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

Lägg slutligen till `com.android.support:customtabs:28.0.0` i listan över beroenden i filen `build.gradle`:

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

Hämta ID: t för den inloggade användaren från en **MobileServiceUser** med hjälp av **getUserId** -metoden. Ett exempel på hur du kan använda framtiden för att anropa de asynkrona inloggnings-API: erna finns i [Kom igång med autentisering].

> [!WARNING]
> Det angivna URL-schemat är Skift läges känsligt.  Se till att alla förekomster av `{url_scheme_of_you_app}` matchnings fall.

### <a name="caching"></a>Cachelagra autentiseringstoken

Cachelagring av autentiseringstoken kräver att du lagrar användar-ID och autentiseringstoken lokalt på enheten. Nästa gången appen startas kontrollerar du cachen, och om dessa värden finns kan du hoppa över inloggnings proceduren och sedan synkronisera klienten med dessa data igen. Dessa data är dock känsliga och bör lagras krypterade för säkerhet, om telefonen blir stulen.  Du kan se ett fullständigt exempel på hur du cachelagrar autentiseringstoken i [avsnittet cache][7]-autentiseringstoken.

När du försöker använda en utgånget token får du ett *401 obehörigt* svar. Du kan hantera autentiseringsfel med hjälp av filter.  Filtrerar avlyssnings begär anden till App Service server delen. Filter koden testar svaret för en 401, utlöser inloggnings processen och återupptar sedan den begäran som genererade 401.

### <a name="refresh"></a>Använd uppdateringstoken

Den token som returnerades av Azure App Service autentiseringen och auktoriseringen har en definierad livs längd på en timme.  Efter den här perioden måste du autentisera om användaren.  Om du använder en token med lång livs längd som du har tagit emot via klient flödes autentisering kan du autentisera med Azure App Service autentisering och auktorisering med samma token.  En annan Azure App Service token genereras med en ny livs längd.

Du kan också registrera providern för att använda uppdateringstoken.  Det är inte alltid möjligt att uppdatera token.  Ytterligare konfiguration krävs:

* För **Azure Active Directory**konfigurerar du en klient hemlighet för Azure Active Directory-appen.  Ange klient hemligheten i Azure App Service när du konfigurerar Azure Active Directory autentisering.  När du anropar `.login()`, pass `response_type=code id_token` som parameter:

    ```java
    HashMap<String, String> parameters = new HashMap<String, String>();
    parameters.put("response_type", "code id_token");
    MobileServiceUser user = mClient.login
        MobileServiceAuthenticationProvider.AzureActiveDirectory,
        "{url_scheme_of_your_app}",
        AAD_LOGIN_REQUEST_CODE,
        parameters);
    ```

* För **Google**skickar du `access_type=offline` som en parameter:

    ```java
    HashMap<String, String> parameters = new HashMap<String, String>();
    parameters.put("access_type", "offline");
    MobileServiceUser user = mClient.login
        MobileServiceAuthenticationProvider.Google,
        "{url_scheme_of_your_app}",
        GOOGLE_LOGIN_REQUEST_CODE,
        parameters);
    ```

* För **Microsoft-konto**väljer du omfånget `wl.offline_access`.

Om du vill uppdatera en token, anropa `.refreshUser()`:

```java
MobileServiceUser user = mClient
    .refreshUser()  // async - returns a ListenableFuture<MobileServiceUser>
    .get();
```

Vi rekommenderar att du skapar ett filter som identifierar ett 401-svar från servern och försöker uppdatera användartoken.

## <a name="log-in-with-client-flow-authentication"></a>Logga in med klient flödes autentisering

Den allmänna processen för att logga in med klient flödes autentisering är följande:

* Konfigurera Azure App Service autentisering och auktorisering på samma sätt som du gör med autentisering av Server flöde.
* Integrera Authentication provider SDK för autentisering för att skapa en åtkomsttoken.
* Anropa metoden `.login()` enligt följande (`result` ska vara en `AuthenticationResult`):

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

Se det fullständiga kod exemplet i nästa avsnitt.

Ersätt metoden `onSuccess()` med den kod som du vill använda vid en lyckad inloggning.  Strängen `{provider}` är en giltig provider: **AAD** (Azure Active Directory), **Facebook**, **Google**, **MicrosoftAccount**eller **Twitter**.  Om du har implementerat anpassad autentisering kan du också använda taggen för anpassad autentiseringsprovider.

### <a name="adal"></a>Autentisera användare med Active Directory-autentiseringsbibliotek (ADAL)

Du kan använda Active Directory-autentiseringsbibliotek (ADAL) för att logga användare i ditt program med Azure Active Directory. Att använda en klient flödes inloggning är ofta att föredra att använda `loginAsync()`-metoder eftersom det ger en mer enhetlig känsla och möjliggör ytterligare anpassning.

1. Konfigurera din server del för mobilappen för AAD-inloggning genom att följa själv studie kursen [konfigurera App Service för Active Directory inloggning][22] . Se till att slutföra det valfria steget när du registrerar ett internt klient program.
2. Installera ADAL genom att ändra din build. gradle-fil så att den innehåller följande definitioner:

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

3. Lägg till följande kod i programmet, vilket gör följande ersättningar:

    * Ersätt **insert-Authority – här** visas namnet på den klient där du etablerade ditt program. Formatet ska vara https://login.microsoftonline.com/contoso.onmicrosoft.com.
    * Ersätt **insert-Resource-ID – här** med klient-ID: t för Server delen för mobilappen. Du kan hämta klient-ID: t från fliken **Avancerat** under **Azure Active Directory inställningar** i portalen.
    * Ersätt **insert-Client-ID – här** med det klient-ID som du kopierade från det interna klient programmet.
    * Ersätt **insert-Redirect-URI – här** med platsens */.auth/login/Done* -slutpunkt, med hjälp av https-schemat. Värdet bör likna *https://contoso.azurewebsites.net/.auth/login/done* .

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

## <a name="filters"></a>Justera kommunikationen mellan klienten och servern

Klient anslutningen är vanligt vis en grundläggande HTTP-anslutning med det underliggande HTTP-biblioteket som medföljer Android SDK.  Det finns flera orsaker till varför du vill ändra:

* Du vill använda ett alternativt HTTP-bibliotek för att justera timeout.
* Du vill ange en förlopps indikator.
* Du vill lägga till en anpassad rubrik som stöder API Management-funktioner.
* Du vill avlyssna ett misslyckat svar så att du kan implementera reautentisering.
* Du vill logga backend-begäranden till en Analytics-tjänst.

### <a name="using-an-alternate-http-library"></a>Använda ett alternativt HTTP-bibliotek

Anropa metoden `.setAndroidHttpClientFactory()` direkt efter att du har skapat klient referensen.  Om du till exempel vill ange tids gräns för anslutning till 60 sekunder (i stället för standard 10 sekunder):

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

### <a name="implement-a-progress-filter"></a>Implementera ett förlopps filter

Du kan implementera en avlyssning av varje begäran genom att implementera en `ServiceFilter`.  Följande uppdaterar till exempel en förgenererad förlopps indikator:

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

Du kan koppla det här filtret till klienten på följande sätt:

```java
mClient = new MobileServiceClient(applicationUrl).withFilter(new ProgressFilter());
```

### <a name="customize-request-headers"></a>Anpassa begärandehuvuden

Använd följande `ServiceFilter` och koppla filtret på samma sätt som `ProgressFilter`:

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

Du kan ange en konverterings strategi som gäller för alla kolumner genom att använda [Gson][3] -API: et. Klient biblioteket för Android använder [Gson][3] bakom bakgrunden för att serialisera Java-objekt till JSON-data innan data skickas till Azure App Service.  I följande kod används metoden **setFieldNamingStrategy ()** för att ställa in strategin. I det här exemplet raderas det inledande (a "m") och sedan visas gemener och versaler för varje fält namn. Den skulle till exempel förvandla "mId" till "ID".  Implementera en konverterings strategi för att minska behovet av att `SerializedName()`-kommentarer på de flesta fälten.

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

Den här koden måste köras innan du skapar en mobil klient referens med hjälp av **MobileServiceClient**.

<!-- URLs. -->
[Get started with Azure Mobile Apps]: app-service-mobile-android-get-started.md
[ASCII control codes C0 and C1]: https://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[Mobile Services SDK for Android]: https://go.microsoft.com/fwlink/p/?LinkID=717033
[Azure portal]: https://portal.azure.com
[Kom igång med autentisering]: app-service-mobile-android-get-started-users.md
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
