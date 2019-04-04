---
title: Självstudie – stegvisa anvisningar för att skapa en ny Android-app med Azure Spatial ankare | Microsoft Docs
description: I den här självstudien får du lära dig hur du skapar en ny Android-app med Azure Spatial fästpunkter.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 04/03/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 64f78b04d433c81302499addf15c3d19621bbf9f
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/04/2019
ms.locfileid: "58919604"
---
# <a name="tutorial-step-by-step-instructions-to-create-a-new-android-app-using-azure-spatial-anchors"></a>Självstudier: Stegvisa instruktioner för att skapa en ny Android-app med Azure Spatial ankare

Den här självstudien visar hur du skapar en ny Android-app som integrerar ARCore funktioner med Azure Spatial fästpunkter.

## <a name="prerequisites"></a>Förutsättningar

För att kunna följa den här självstudien måste du ha:

- En Windows- eller macOS-dator med <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.3+</a>.
- En <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">utvecklaraktiverad</a> och <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore-kapabel</a> Android-enhet.

## <a name="getting-started"></a>Komma igång

Starta Android Studio. I den **Välkommen till Android Studio** fönstret klickar du på **starta ett nytt Android Studio-projekt**. Eller, om du har ett projekt som redan har öppnat väljer **filen**->**nytt projekt**.

I den **Skapa nytt projekt** fönstret under den **Telefoner och surfplattor** väljer **tom aktivitet**, och klicka på **nästa**. Sedan, under **minsta API-nivå**, Välj `API 26: Android 8.0 (Oreo)`, och se till att den **språk** är inställd på `Java`. Du kanske vill ändra på projektets namn och plats och paketets namn. Lämna de andra alternativen som de är. Klicka på **Slutför**. Den **komponenten Installer** ska köras. När det är klart klickar du på **Slutför**. När du har viss bearbetning öppnas Android Studio IDE.

## <a name="trying-it-out"></a>Att använda

Anslut din developer-aktiverad enhet till din utvecklingsdator med en USB-kabel för att testa den nya appen. Klicka på **kör**->**köra appar**. I den **Välj distributionsmålet** fönstret väljer du enheten och klickar på **OK**. Android Studio installerar apparna på den anslutna enheten och startar den. Du bör nu se ”Hello World”! visas i appen som körs på din enhet. Klicka på **kör**->**Stop ”app”**.

## <a name="integrating-arcore"></a>Integrera _ARCore_

<a href="https://developers.google.com/ar/discover/" target="_blank">_ARCore_ </a> är Googles plattform för att skapa förhöjd verklighet upplevelser, aktiverar enheten för att spåra dess position när det flyttar och skapar en egen förståelse för den verkliga världen.

Ändra `app\manifests\AndroidManifest.xml` att inkludera följande poster i roten `<manifest>` noden. Det här kodfragmentet gör några saker:

- Det kan din app åtkomst till enhetens kamera.
- Det säkerställer också att din app visas bara i Google Play Store till enheter som har stöd för ARCore.
- Det kommer att konfigurera Google Play Store om du vill hämta och installera ARCore, om den inte installeras redan när appen är installerad.

```xml
<uses-permission android:name="android.permission.CAMERA" />
<uses-feature android:name="android.hardware.camera.ar" />

<application>
    ...
    <meta-data android:name="com.google.ar.core" android:value="required" />
    ...
</application>
```

Ändra `Gradle Scripts\build.gradle (Module: app)` att inkludera följande post. Den här koden ser till att din app används på ARCore version 1.5 (stöd för ARCore 1.6 + på Azure Spatial ankare kommer snart). Efter den här ändringen, du kan få ett meddelande från Gradle där du uppmanas att synkronisera: Klicka på **synkronisera nu**.

```
dependencies {
    ...
    implementation 'com.google.ar:core:1.5.0'
    ...
}
```

## <a name="integrating-sceneform"></a>Integrera _Sceneform_

<a href="https://developers.google.com/ar/develop/java/sceneform/" target="_blank">_Sceneform_ </a> gör det enkelt att återge realistisk 3D-scener i förhöjd verklighet appar utan att lära dig OpenGL.

Ändra `Gradle Scripts\build.gradle (Module: app)` att inkludera följande poster. Den här koden gör att din app att använda språkkonstruktioner från Java 8, som `Sceneform` kräver. Det säkerställer också att appens mål `Sceneform` version 1.5, eftersom den måste matcha versionen av ARCore som din app använder. Efter den här ändringen, du kan få ett meddelande från Gradle där du uppmanas att synkronisera: Klicka på **synkronisera nu**.

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
    implementation 'com.google.ar.sceneform.ux:sceneform-ux:1.5.0'
    ...
}
```

Öppna din `app\res\layout\activity_main.xml`, och Ersätt den befintliga Hello Wolrd `<TextView>` element med följande ArFragment. Den här koden genereras kamerans användning om du vill ska visas på skärmen aktivera ARCore att spåra din enhet position som flyttas.

```xml
<fragment android:name="com.google.ar.sceneform.ux.ArFragment"
    android:id="@+id/ux_fragment"
    android:layout_width="match_parent"
    android:layout_height="match_parent" />
```

[Distribuera om](#trying-it-out) din app på din enhet för att verifiera den en gång till. Den här gången ska du be dig om kameran behörigheter. När den godkänts, bör du se din kamera flöde återgivning på skärmen.

## <a name="place-an-object-in-the-real-world"></a>Placera ett objekt i den verkliga världen

Nu ska vi skapa och placera ett objekt som använder din app. Lägg först till följande importer i din `app\java\<PackageName>\MainActivity`:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=23-33)]

Lägg sedan till följande Medlemsvariabler i din `MainActivity` klass:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=52-57)]

Lägg sedan till följande kod till din `app\java\<PackageName>\MainActivity` `onCreate()` metod. Den här koden kommer att koppla samman en lyssnare som kallas `handleTap()`, som ska känna av när användaren trycker på skärmen på din enhet. Om och tryck sedan på råkar vara på en verkliga yta som redan har identifierats av Arcores spårning, körs lyssnaren.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=68-74,85&highlight=6-7)]

Slutligen lägger du till följande `handleTap()` metod som binder allt tillsammans. Den skapar en sfär och placera den på tapped platsen. Området är inledningsvis svart sedan `this.recommendedSessionProgress` nollställs just nu. Det här värdet kommer att justeras vid ett senare tillfälle.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=150-158,170-171,174-182,198-199)]

[Distribuera om](#trying-it-out) din app på din enhet för att verifiera den en gång till. Den här tiden kan kan du flytta runt enheten att få ARCore att starta eftersom din miljö. Tryck på skärmen för att skapa och placera din svart sphere över ytan på ditt val.

## <a name="attach-a-local-azure-spatial-anchor"></a>Bifoga en lokal Azure Spatial ankare

Ändra `Gradle Scripts\build.gradle (Module: app)` att inkludera följande post. Den här koden säkerställer att din app mål Azure Spatial ankare version 1.0.2. Det finns dock refererar till någon ny version av Azure Spatial ankare ska fungera.

```
dependencies {
    ...
    implementation "com.microsoft.azure.spatialanchors:spatialanchors_jni:[1.0.2]"
    implementation "com.microsoft.azure.spatialanchors:spatialanchors_java:[1.0.2]"
    ...
}
```

Högerklicka på `app\java\<PackageName>` -> **nya**->**Java-klass**. Ange **namn** till _MyFirstApp_, och **superklass** till _android.app.Application_. Lämna de andra alternativen som de är. Klicka på **OK**. En fil som heter `MyFirstApp.java` kommer att skapas. Lägg till följande import i den:

```java
import com.microsoft.CloudServices;
```

Lägg sedan till följande kod i den nya `MyFirstApp` klass som säkerställer Azure Spatial fästpunkter har initierats med kontext för ditt program.

```java
    @Override
    public void onCreate() {
        super.onCreate();
        CloudServices.initialize(this);
    }
```

Anpassa `app\manifests\AndroidManifest.xml` att inkludera följande post i roten `<application>` noden. Den här koden kommer att koppla samman programklassen som du har skapat i din app.

```xml
    <application
        android:name=".MyFirstApp"
        ...
    </application>
```

Tillbaka i `app\java\<PackageName>\MainActivity`, Lägg till följande importer i den:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=33-40&highlight=2-8)]

Lägg sedan till följande Medlemsvariabler i din `MainActivity` klass:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=57-60&highlight=3-4)]

Nu ska vi lägga till följande `initializeSession()` metod i din `mainActivity` klass. När anropas säkerställer en Spatial fästpunkter för Azure-session skapas och initierats korrekt vid start av din app.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=89-97,146)]

Nu kan vi koppla din `initializeSession()` metod till din `onCreate()` metod. Vi ska kontrollera också att bildrutor från kameran feed skickas till Azure SDK för Spatial fästpunkter för bearbetning.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=68-85&highlight=9-17)]

Slutligen lägger du till följande kod till din `handleTap()` metod. Det ska kopplas en lokal Azure Spatial ankare till svart sphere som vi monterar i verkligheten.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=150-158,170-182,198-199&highlight=12-13)]

[Distribuera om](#trying-it-out) appen en gång till. Flytta runt enheten, tryck på skärmen och placera en svart sfär. Den här tiden kan dock ska koden skapa och koppla en lokal Azure Spatial ankare till din sphere.

Innan du fortsätter, måste du skapa en Azure-Spatial fästpunkter konto-ID och nyckel, om inte redan har. Se följande avsnitt för att få dem.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="upload-your-local-anchor-into-the-cloud"></a>Ladda upp ankaret lokalt till molnet

När du har din Azure Spatial ankare konto identifieraren och nyckel kan vi går tillbaka `app\java\<PackageName>\MainActivity`, Lägg till följande importer i den:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=40-45&highlight=3-6)]

Lägg sedan till följande Medlemsvariabler i din `MainActivity` klass:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=60-65&highlight=3-6)]

Lägg till följande kod till din `initializeSession()` metod. Först måste den här koden gör att din app för att övervaka förloppet som Azure-SDK: N för Spatial ankare gör eftersom den samlar in bildrutor från kameran feed. Som det gör startar färgen på din sphere ändra från dess ursprungliga svart till grå. Sedan inaktiveras det vit när tillräckligt med bildrutor har samlats in om du vill skicka ankaret till molnet. Den här koden kommer dessutom ange de autentiseringsuppgifter som krävs för att kommunicera med molnet backend. Här är där du konfigurerar din app att använda ditt konto-ID och nyckel. Du kopierade dem i en textredigerare när [konfigurerar Spatial ankare resursen](#create-a-spatial-anchors-resource).

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=89-120,142-146&highlight=11-36)]

Lägg sedan till följande `uploadCloudAnchorAsync()` metod i din `mainActivity` klass. När anropas den här metoden asynkront att vänta tills tillräckligt många bildrutor samlas in från din enhet. När det händer färgen på din sphere växlar till gult och sedan den börjar ladda upp din lokala Azure Spatial ankare till molnet. När överföringen är klar returnerar koden en fästpunkt identifierare.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=uploadCloudAnchorAsync)]

Slutligen ska vi anslutas allt tillsammans. I din `handleTap()` metoden Lägg till följande kod. Den anropar din `uploadCloudAnchorAsync()` metoden så snart din sphere har skapats. När metoden returnerar, koden nedan utför en slutlig uppdatering av din sfär, ändra dess färg till blått.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=150-158,170-199&highlight=24-37)]

[Distribuera om](#trying-it-out) appen en gång till. Flytta runt enheten, tryck på skärmen och placera din sphere. Den här tiden kan dock ändrar din sphere färg från svart till vitt, som kamera ramar som ska samlas in. När vi har tillräckligt med bildrutor området inaktiveras i gult och molnet överföringen startar. När överföringen är klar blir din sphere blå. Du kan också använda den `Logcat` fönster i Android Studio för att övervaka loggmeddelanden appen skickar. Till exempel session förloppet under ramens samlar in och ankar-ID att molnet returnerar när överföringen har slutförts.

## <a name="locate-your-cloud-spatial-anchor"></a>Leta upp ankaret molnet spatial

En ankaret överförs till molnet, vi är redo att försöka hitta det igen. Först ska vi lägga till följande importer i din kod.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=45-48&highlight=3-4)]

Sedan ska vi lägga till följande kod till din `handleTap()` metod. Den här koden kommer att:

- Ta bort våra befintliga blå sphere från skärmen.
- Initiera vår Azure Spatial ankare sessionen igen. Den här åtgärden säkerställer att till ankaret som vi kommer att hitta kommer från molnet istället för till lokala ankaret som vi skapade.
- Skicka en fråga om fästpunkten vi överförs till molnet.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=handleTap&highlight=10-19)]

Nu kan vi koppla koden som kommer att anropas när vi frågar för ankaret finns. I din `initializeSession()` metoden Lägg till följande kod. Det här kodfragmentet kommer Skapa & Placera en grön sfär när molnet spatial fästpunkten är placerad. Det gör även att skärmen trycka igen, så att du kan upprepa hela scenariot en gång till: skapa en annan lokal fästpunkt, ladda upp den och leta upp den igen.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=initializeSession&highlight=34-53)]

Klart! [Distribuera om](#trying-it-out) din app en sista gång du prova att använda hela scenariot från slutpunkt till slutpunkt. Flytta din enhet och placera din svart sphere. Sedan att flytta din enhet för att fånga kamera ramar tills området blir gul. Ankaret lokala ska överföras och din sphere inaktiveras blå. Knacka på skärmen en gång till så att ankaret lokala tas bort och sedan ska vi frågar för motparten molnet. Fortsätta flytta enheten förrän ankaret molnet spatial finns. En grön sfär ska visas på rätt plats och du kan spola och upprepa hela scenariot igen.

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-new-android-app-finished.md)]
