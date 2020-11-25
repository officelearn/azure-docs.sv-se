---
title: 'Självstudie: skapa en ny Android-app'
description: I den här självstudien får du lära dig hur du skapar en ny Android-app med hjälp av Azure spatiala ankare.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: af0d01a20728d2332d4a8d71819f73baf68a65a4
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/23/2020
ms.locfileid: "95998402"
---
# <a name="tutorial-step-by-step-instructions-to-create-a-new-android-app-using-azure-spatial-anchors"></a>Självstudie: steg-för-steg-instruktioner för att skapa en ny Android-app med hjälp av Azure spatiala ankare

I den här självstudien får du lära dig hur du skapar en ny Android-app som integrerar ARCore-funktionen med avstånds ankare för Azure.

## <a name="prerequisites"></a>Förutsättningar

För att kunna följa den här självstudien måste du ha:

- En Windows-eller macOS-dator med <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.4 +</a>.
- En <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">utvecklaraktiverad</a> och <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore-kapabel</a> Android-enhet.

## <a name="getting-started"></a>Komma igång

Starta Android Studio. Klicka på **starta ett nytt Android Studio projekt** i fönstret **Välkommen till Android Studio** . Eller, om du har ett projekt som redan har öppnats, väljer du **fil** -> **nytt projekt**.

I fönstret **Skapa nytt projekt** under avsnittet **telefon och surfplatta** väljer du **Tom aktivitet** och klickar på **Nästa**. Välj, under **lägsta API-nivå**, `API 26: Android 8.0 (Oreo)` och se till att **språket** är inställt på `Java` . Du kanske vill ändra projekt namnet & plats och paket namnet. Lämna de andra alternativen som de är. Klicka på **Finish**. **Komponent installations programmet** körs. När den är klar klickar du på **Slutför**. Vid viss bearbetning öppnar Android Studio IDE-filen.

## <a name="trying-it-out"></a>Testa

Testa din nya app genom att ansluta din enhet för utvecklare till din utvecklings dator med en USB-kabel. Klicka på **Kör** -> **Kör appen**. I fönstret **Välj distributions mål** väljer du din enhet och klickar på **OK**. Android Studio installerar appen på den anslutna enheten och startar den. Nu bör du se "Hello World!" visas i appen som körs på enheten. Klicka på **Kör** -> **stoppa "app"**.

## <a name="integrating-_arcore_"></a>Integrera _ARCore_

<a href="https://developers.google.com/ar/discover/" target="_blank">_ARCore_</a> är Googles plattform för att skapa förhöjda verklighets upplevelser, vilket gör att enheten kan spåra sin position när den flyttas och bygger sin egen förståelse för den verkliga världen.

Ändra `app\manifests\AndroidManifest.xml` för att inkludera följande poster i rotnoden `<manifest>` . Det här kodfragmentet gör några saker:

- Det gör att din app kan komma åt din enhets kamera.
- Det ser också till att din app endast visas i Google Play Butik till enheter som stöder ARCore.
- Den konfigurerar Google Play Butik att ladda ned och installera ARCore, om den inte redan är installerad, när din app installeras.

```xml
<uses-permission android:name="android.permission.CAMERA" />
<uses-feature android:name="android.hardware.camera.ar" />

<application>
    ...
    <meta-data android:name="com.google.ar.core" android:value="required" />
    ...
</application>
```

Ändra `Gradle Scripts\build.gradle (Module: app)` för att inkludera följande post. Den här koden ser till att appens mål ARCore version 1,8. Efter den här ändringen kan du få ett meddelande från Gradle som ber dig synkronisera: Klicka på **Synkronisera nu**.

```
dependencies {
    ...
    implementation 'com.google.ar:core:1.11.0'
    ...
}
```

## <a name="integrating-_sceneform_"></a>Integrera _Sceneform_

[_Sceneform_](https://developers.google.com/sceneform/develop/) gör det enkelt att återge realistiska 3D-scener i förhöjda verklighets appar, utan att behöva lära sig OpenGL.

Ändra `Gradle Scripts\build.gradle (Module: app)` för att inkludera följande poster. Den här koden gör att din app kan använda språk konstruktioner från Java 8, vilket `Sceneform` kräver. Det ser också till att appen är riktad till `Sceneform` version 1,8, eftersom den ska matcha den version av ARCore som din app använder. Efter den här ändringen kan du få ett meddelande från Gradle som ber dig synkronisera: Klicka på **Synkronisera nu**.

```
android {
    ...

    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}

dependencies {
    ...
    implementation 'com.google.ar.sceneform.ux:sceneform-ux:1.11.0'
    ...
}
```

Öppna din `app\res\layout\activity_main.xml` och Ersätt det befintliga Hello Wolrd- `<TextView>` elementet med följande ArFragment. Den här koden leder till att kamera flödet visas på skärmen som aktiverar ARCore för att spåra enhetens position när den flyttas.

```xml
<fragment android:name="com.google.ar.sceneform.ux.ArFragment"
    android:id="@+id/ux_fragment"
    android:layout_width="match_parent"
    android:layout_height="match_parent" />
```

[Distribuera](#trying-it-out) om din app till enheten för att verifiera den en gång till. Den här gången bör du uppmanas att ange kamera behörigheter. När du har godkänt bör du se åter givningen av ditt kamera flöde på skärmen.

## <a name="place-an-object-in-the-real-world"></a>Placera ett objekt i den verkliga världen

Nu ska vi skapa & placera ett objekt med din app. Lägg först till följande importer i din `app\java\<PackageName>\MainActivity` :

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=23-33)]

Lägg sedan till följande medlemsvariabler i `MainActivity` klassen:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=52-57)]

Lägg sedan till följande kod i din- `app\java\<PackageName>\MainActivity` `onCreate()` metod. Den här koden kopplar upp en lyssnare som kallas `handleTap()` , som kommer att identifiera när användaren trycker på skärmen på enheten. Om tryckningen ska finnas på en verklig världs yta som redan har identifierats av ARCore ' s tracking, kommer lyssnaren att köras.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=68-74,85&highlight=6-7)]

Slutligen lägger du till följande `handleTap()` metod som knyter samman allt. En sfär skapas och placeras på den infogade platsen. Klotet blir inlednings vis svart, eftersom `this.recommendedSessionProgress` har angetts till noll just nu. Det här värdet kommer att justeras senare.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=151-159,171-172,175-183,199-200)]

[Distribuera](#trying-it-out) om din app till enheten för att verifiera den en gång till. Den här gången kan du flytta runt enheten för att få ARCore att börja identifiera din miljö. Tryck sedan på skärmen för att skapa & placera din svarta sfär över valfri yta.

## <a name="attach-a-local-azure-spatial-anchor"></a>Koppla en lokal Azure-spatial fäst punkt

Ändra `Gradle Scripts\build.gradle (Module: app)` för att inkludera följande post. Med den här koden ser du till att appen är riktad mot Azure spatiala ankare version 2.2.0. Det sägs att hänvisning till eventuella nya versioner av Azures spatialdata bör fungera. Du hittar viktig information [här.](https://github.com/Azure/azure-spatial-anchors-samples/releases)

```
dependencies {
    ...
    implementation "com.microsoft.azure.spatialanchors:spatialanchors_jni:[2.2.0]"
    implementation "com.microsoft.azure.spatialanchors:spatialanchors_java:[2.2.0]"
    ...
}
```

Högerklicka på `app\java\<PackageName>` -> **ny** -> **Java-klass**. Ange **namnet** _MyFirstApp_ och **superklass** till _Android. app. Application_. Lämna de andra alternativen som de är. Klicka på **OK**. En fil som heter `MyFirstApp.java` kommer att skapas. Lägg till följande import:

```java
import com.microsoft.CloudServices;
```

Lägg sedan till följande kod inuti den nya `MyFirstApp` klassen, som ser till att Azure-spatialdata initieras med programmets kontext.

```java
    @Override
    public void onCreate() {
        super.onCreate();
        CloudServices.initialize(this);
    }
```

Ändra nu `app\manifests\AndroidManifest.xml` till att inkludera följande post i rotnoden `<application>` . Den här koden kopplar samman program klassen som du skapade i din app.

```xml
    <application
        android:name=".MyFirstApp"
        ...
    </application>
```

Lägg tillbaka `app\java\<PackageName>\MainActivity` till följande importer i den:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=33-40&highlight=2-8)]

Lägg sedan till följande medlemsvariabler i `MainActivity` klassen:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=57-60&highlight=3-4)]

Nu ska vi lägga till följande `initializeSession()` metod i `mainActivity` klassen. När den har anropats ser den till att en session med Azure-ankare skapas och initieras korrekt under starten av din app.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=89-97,147)]

Nu ska vi ansluta din `initializeSession()` metod till din `onCreate()` metod. Vi kommer också att se till att ramar från din Camera-feed skickas till Azure spatiala fäst punkter SDK för bearbetning.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=68-85&highlight=9-17)]

Slutligen lägger du till följande kod i din- `handleTap()` metod. Den ansluter ett lokalt Azure-ankare till den svarta sfär som vi placerar i den verkliga världen.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=151-159,171-183,199-200&highlight=12-13)]

[Distribuera](#trying-it-out) om din app en gång till. Flytta runt enheten, tryck på skärmen och placera en svart sfär. Den här gången kommer din kod att skapas och bifoga en lokal Azure-spatial fäst punkt i din sfär.

Innan du fortsätter måste du skapa ett konto för ett Azure-ankare för att hämta konto identifierare, nyckel och domän, om du inte redan har dem. Använd följande avsnitt för att hämta dem.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="upload-your-local-anchor-into-the-cloud"></a>Ladda upp ditt lokala ankare i molnet

När du har en konto identifierare, nyckel och domän för ditt Azure-spatialdata kan vi gå tillbaka och `app\java\<PackageName>\MainActivity` lägga till följande import i den:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=40-45&highlight=3-6)]

Lägg sedan till följande medlemsvariabler i `MainActivity` klassen:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=60-65&highlight=3-6)]

Lägg nu till följande kod i din- `initializeSession()` metod. Först kommer den här koden att tillåta att din app övervakar förloppet för Azures fäst punkter SDK: er som samlar in ramar från ditt kamera flöde. Som det gör börjar färgen på klotet att ändras från dess ursprungliga svarta till grått. Sedan blir den vit när tillräckligt många ramar samlas in för att skicka ditt ankare till molnet. Sedan anger den här koden de autentiseringsuppgifter som krävs för att kommunicera med moln server delen. Här konfigurerar du din app för att använda din konto identifierare, nyckel och domän. Du kopierade dem till en text redigerare när du [ställer in resursen för spatiala ankare](#create-a-spatial-anchors-resource).

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=89-120,142-148&highlight=11-37)]

Lägg sedan till följande `uploadCloudAnchorAsync()` metod i `mainActivity` klassen. När den här metoden har anropats väntar den här metoden asynkront tills tillräckligt många ramar samlas in från enheten. Så snart som händer byter den färg på din sfär till gul, och sedan börjar den att ladda upp din lokala Azure-spatiala fäst punkt i molnet. När uppladdningen är klar returnerar koden en Anchor-identifierare.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=uploadCloudAnchorAsync)]

Slutligen är det dags att koppla ihop allt. `handleTap()`Lägg till följande kod i metoden. Den anropar `uploadCloudAnchorAsync()` metoden så snart din sfär skapas. När metoden returnerar utför koden nedan en slutgiltig uppdatering av din sfär och ändrar färgen till blå.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=151-159,171-200&highlight=24-37)]

[Distribuera](#trying-it-out) om din app en gång till. Flytta runt enheten, tryck på skärmen och placera din sfär. Den här gången kommer din sfär att ändra färgen från svart till vitt, medan kamera bild rutor samlas in. När vi har tillräckligt många ramar förvandlas klotet till gult och moln överföringen startar. När uppladdningen är klar blir klotet blå. Alternativt kan du också använda `Logcat` fönstret i Android Studio för att övervaka de logg meddelanden som appen skickar. Till exempel kommer sessionen att förloppet under bild rutorna och ankar-ID: t som molnet returnerar när överföringen är klar.

## <a name="locate-your-cloud-spatial-anchor"></a>Hitta molnets avstånds ankare

En fäst punkt överförs till molnet, vi är redo att försöka hitta den igen. Först lägger vi till följande importer i din kod.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=45-48&highlight=3-4)]

Sedan lägger vi till följande kod i din `handleTap()` metod. Den här koden kommer att:

- Ta bort vår befintliga blå sfär från skärmen.
- Initiera vår Azure-spatiala ankare igen. Med den här åtgärden ser du till att ankaret som vi ska hitta kommer från molnet i stället för det lokala ankare som vi har skapat.
- Skicka en fråga för den fäst punkt som vi laddade upp till molnet.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=handleTap&highlight=10-19)]

Nu ska vi koppla koden som ska anropas när ankaret som vi frågar efter finns. I din `initializeSession()` metod lägger du till följande kod. Det här kodfragmentet skapar & att placera en grön sfär när molnets spatiala ankare befinner sig. Det aktiverar också skärm tryckning igen, så du kan upprepa hela scenariot en gång till: skapa ett annat lokalt ankare, ladda upp det och hitta det igen.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=initializeSession&highlight=34-53)]

Klart! [Distribuera](#trying-it-out) din app en sista gången för att testa hela scenariot från början till slut. Flytta runt enheten och placera din svarta sfär. Fortsätt sedan att flytta enheten för att avbilda kamera bild rutor tills klotet blir gult. Ditt lokala ankare laddas upp och sfären blir blå. Till sist trycker du på skärmen en gång till, så att ditt lokala ankare tas bort och vi kommer att fråga efter dess moln motsvarighet. Fortsätt att flytta enheten tills molnets spatiala ankare är belägen. En grön sfär bör visas på rätt plats och du kan skölja & upprepa hela scenariot igen.

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-new-android-app-finished.md)]
