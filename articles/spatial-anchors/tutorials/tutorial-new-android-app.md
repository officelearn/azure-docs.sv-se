---
title: 'Självstudiekurs: Skapa en ny Android-app'
description: I den här självstudien får du lära dig hur du skapar en ny Android-app med Azure Spatial Anchors.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 04/03/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: e1773ef81a5b727187a9a69ccc7ce7ad0421fb2c
ms.sourcegitcommit: 940e16ff194d5163f277f98d038833b1055a1a3e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2020
ms.locfileid: "80246780"
---
# <a name="tutorial-step-by-step-instructions-to-create-a-new-android-app-using-azure-spatial-anchors"></a>Självstudiekurs: Steg-för-steg-instruktioner för att skapa en ny Android-app med Azure Spatial Anchors

Den här självstudien visar hur du skapar en ny Android-app som integrerar ARCore-funktioner med Azure Spatial Anchors.

## <a name="prerequisites"></a>Krav

För att kunna följa den här självstudien måste du ha:

- En Windows- eller macOS-maskin med <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.4+</a>.
- En <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">utvecklaraktiverad</a> och <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore-kapabel</a> Android-enhet.

## <a name="getting-started"></a>Komma igång

Starta Android Studio. I fönstret **Välkommen till Android Studio** klickar du på Starta ett nytt Android **Studio-projekt**. Om du redan har öppnat ett projekt väljer du **Arkiv**->**nytt projekt**.

Välj **Tom aktivitet**under avsnittet **Telefon och surfplatta** i fönstret Skapa nytt **projekt** och klicka på **Nästa**. Välj sedan under Lägsta `API 26: Android 8.0 (Oreo)` **API-nivå**och se `Java`till att **språket** är inställt på . Du kanske vill ändra projektnamnet & plats och paketnamnet. Lämna de andra alternativen som de är. Klicka på **Slutför**. **Komponentinstallationsprogrammet** körs. När det är klart klickar du på **Slutför**. Efter viss bearbetning kommer Android Studio att öppna IDE.

## <a name="trying-it-out"></a>Prova det

Om du vill testa din nya app ansluter du utvecklaraktiverade enheter till utvecklingsdatorn med en USB-kabel. Klicka på **Kör**->**kör 'app'**. Markera enheten i fönstret **Välj distributionsmål** och klicka på **OK**. Android Studio installerar appen på din anslutna enhet och startar den. Du bör nu se "Hello World!" visas i appen som körs på enheten. Klicka på **Kör**->**stopp 'app'**.

## <a name="integrating-_arcore_"></a>Integrera _ARCore_

<a href="https://developers.google.com/ar/discover/" target="_blank">_ARCore_</a> är Googles plattform för att bygga Augmented Reality-upplevelser, vilket gör att din enhet kan spåra sin position när den rör sig och bygger sin egen förståelse av den verkliga världen.

Ändra `app\manifests\AndroidManifest.xml` så att följande poster `<manifest>` ingår i rotnoden. Detta kodavsnitt gör några saker:

- Det gör att appen kan komma åt enhetens kamera.
- Det säkerställer också att din app bara visas i Google Play Butik för enheter som stöder ARCore.
- Google Play Butik konfigureras så att ARCore hämtas och installeras, om det inte redan är installerat, när appen är installerad.

```xml
<uses-permission android:name="android.permission.CAMERA" />
<uses-feature android:name="android.hardware.camera.ar" />

<application>
    ...
    <meta-data android:name="com.google.ar.core" android:value="required" />
    ...
</application>
```

Ändra `Gradle Scripts\build.gradle (Module: app)` så att följande post inkluderas. Den här koden säkerställer att din app är inriktad på ARCore version 1.8. Efter den här ändringen kan du få ett meddelande från Gradle som ber dig att synkronisera: klicka på **Synkronisera nu**.

```
dependencies {
    ...
    implementation 'com.google.ar:core:1.11.0'
    ...
}
```

## <a name="integrating-_sceneform_"></a>Integrera _scenform_

[_Scenform_](https://developers.google.com/sceneform/develop/) gör det enkelt att rendera realistiska 3D-scener i Augmented Reality-appar, utan att behöva lära sig OpenGL.

Ändra `Gradle Scripts\build.gradle (Module: app)` om du vill inkludera följande poster. Med den här koden kan appen använda språkkonstruktioner `Sceneform` från Java 8, vilket kräver. Det kommer också att `Sceneform` se till att din app mål version 1.8, eftersom det bör matcha den version av ARCore din app använder. Efter den här ändringen kan du få ett meddelande från Gradle som ber dig att synkronisera: klicka på **Synkronisera nu**.

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

Öppna `app\res\layout\activity_main.xml`ditt och ersätt det `<TextView>` befintliga Hello Wolrd-elementet med följande ArFragment. Den här koden gör att kameraflödet visas på skärmen så att ARCore kan spåra enhetens position när den rör sig.

```xml
<fragment android:name="com.google.ar.sceneform.ux.ArFragment"
    android:id="@+id/ux_fragment"
    android:layout_width="match_parent"
    android:layout_height="match_parent" />
```

[Distribuera](#trying-it-out) om appen till enheten för att verifiera den en gång till. Den här gången bör du bli tillfrågad om kamerabehörigheter. När du har godkänts bör du se hur kameraflödet återges på skärmen.

## <a name="place-an-object-in-the-real-world"></a>Placera ett objekt i den verkliga världen

Nu ska vi skapa & placera ett objekt med hjälp av appen. Lägg först till följande `app\java\<PackageName>\MainActivity`import i din:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=23-33)]

Lägg sedan till följande medlemsvariabler i klassen: `MainActivity`

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=52-57)]

Lägg sedan till följande `app\java\<PackageName>\MainActivity` `onCreate()` kod i metoden. Denna kod kommer att ansluta `handleTap()`en lyssnare, kallas , som kommer att upptäcka när användaren trycker på skärmen på din enhet. Om kranen råkar vara på en verklig yta som redan har erkänts av ARCore spårning, kommer lyssnaren att köras.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=68-74,85&highlight=6-7)]

Slutligen, lägg `handleTap()` till följande metod, som kommer att knyta ihop allt. Det kommer att skapa en sfär, och placera den på den avlyssnade platsen. Sfären kommer inledningsvis att `this.recommendedSessionProgress` vara svart, eftersom är satt till noll just nu. Det här värdet justeras senare.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=150-158,170-171,174-182,198-199)]

[Distribuera](#trying-it-out) om appen till enheten för att verifiera den en gång till. Den här gången kan du flytta runt enheten för att få ARCore att börja känna igen din miljö. Tryck sedan på skärmen för att skapa & placera din svarta sfär över valfri yta.

## <a name="attach-a-local-azure-spatial-anchor"></a>Koppla ett lokalt Azure Spatial Anchor

Ändra `Gradle Scripts\build.gradle (Module: app)` så att följande post inkluderas. Den här koden säkerställer att din app är inriktad på Azure Spatial Anchors version 2.2.0. Med detta sagt, refererar till alla nya versioner av Azure Spatial Anchors bör fungera. Du hittar viktig information [här.](https://github.com/Azure/azure-spatial-anchors-samples/releases)

```
dependencies {
    ...
    implementation "com.microsoft.azure.spatialanchors:spatialanchors_jni:[2.2.0]"
    implementation "com.microsoft.azure.spatialanchors:spatialanchors_java:[2.2.0]"
    ...
}
```

Högerklicka på `app\java\<PackageName>` -> **Ny**->**Java-klass**. Ange **namn** till _MyFirstApp_och **Superclass** till _android.app.Application_. Lämna de andra alternativen som de är. Klicka på **OK**. En fil `MyFirstApp.java` som anropas skapas. Lägg till följande import i den:

```java
import com.microsoft.CloudServices;
```

Lägg sedan till följande kod `MyFirstApp` i den nya klassen, vilket säkerställer att Azure Spatial Anchors initieras med programmets kontext.

```java
    @Override
    public void onCreate() {
        super.onCreate();
        CloudServices.initialize(this);
    }
```

Ändra nu `app\manifests\AndroidManifest.xml` för att inkludera följande `<application>` post i rotnoden. Den här koden ansluter klassen Program som du skapade i appen.

```xml
    <application
        android:name=".MyFirstApp"
        ...
    </application>
```

Tillbaka `app\java\<PackageName>\MainActivity`i , lägg till följande import i den:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=33-40&highlight=2-8)]

Lägg sedan till följande medlemsvariabler i klassen: `MainActivity`

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=57-60&highlight=3-4)]

Nu ska vi lägga `initializeSession()` till följande `mainActivity` metod i klassen. När den har anropats säkerställer den att en Azure Spatial Anchors-session skapas och initieras korrekt under starten av din app.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=89-97,146)]

Nu, låt oss `initializeSession()` koppla in `onCreate()` din metod i din metod. Vi ser också till att ramar från kameraflödet skickas till Azure Spatial Anchors SDK för bearbetning.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=68-85&highlight=9-17)]

Lägg slutligen till följande `handleTap()` kod i din metod. Det kommer att bifoga ett lokalt Azure Spatial Anchor till den svarta sfären som vi placerar i den verkliga världen.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=150-158,170-182,198-199&highlight=12-13)]

[Distribuera](#trying-it-out) om appen en gång till. Flytta runt enheten, tryck på skärmen och placera en svart sfär. Men den här gången kommer din kod att skapa och koppla ett lokalt Azure Spatial Anchor till din sfär.

Innan du fortsätter vidare måste du skapa en Azure Spatial Anchors-kontoidentifierare och nyckel, om du inte redan har dem. Följ följande avsnitt för att hämta dem.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="upload-your-local-anchor-into-the-cloud"></a>Ladda upp ditt lokala ankare i molnet

När du har ditt Azure Spatial Anchors-kontoidentifierare `app\java\<PackageName>\MainActivity`och nyckel kan vi gå in igen och lägga till följande import i det:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=40-45&highlight=3-6)]

Lägg sedan till följande medlemsvariabler i klassen: `MainActivity`

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=60-65&highlight=3-6)]

Lägg nu till följande `initializeSession()` kod i din metod. Först tillåter den här koden din app att övervaka de framsteg som Azure Spatial Anchors SDK gör när den samlar in ramar från kameraflödet. Som den gör, kommer färgen på din sfär börja förändras från sin ursprungliga svarta, till grått. Sedan blir det vitt när tillräckligt många ramar samlas in för att skicka in ditt ankare till molnet. För det andra kommer den här koden att tillhandahålla de autentiseringsuppgifter som behövs för att kommunicera med molnet back-end. Här konfigurerar du appen så att den använder kontoidentifieraren och nyckeln. Du kopierade dem till en textredigerare när [du konfigurerade resursen Spatial Anchors](#create-a-spatial-anchors-resource).

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=89-120,142-146&highlight=11-36)]

Lägg sedan till `uploadCloudAnchorAsync()` följande `mainActivity` metod i klassen. När den här metoden anropas väntar den asynkront tills tillräckligt många ramar samlas in från enheten. Så fort det händer växlar den färgen på din sfär till gult, och sedan börjar ladda upp ditt lokala Azure Spatial Anchor i molnet. När överföringen är klar returnerar koden en ankaridentifierare.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=uploadCloudAnchorAsync)]

Slutligen, låt oss koppla ihop allt. Lägg `handleTap()` till följande kod i metoden. Det kommer att `uploadCloudAnchorAsync()` åberopa din metod så snart din sfär skapas. När metoden återvänder, koden nedan kommer att utföra en slutlig uppdatering till din sfär, ändra dess färg till blå.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=150-158,170-199&highlight=24-37)]

[Distribuera](#trying-it-out) om appen en gång till. Flytta runt enheten, tryck på skärmen och placera din sfär. Den här gången kommer dock din sfär ändra sin färg från svart mot vitt, som kamerabilder samlas in. När vi har tillräckligt med ramar, kommer sfären förvandlas till gult, och molnet uppladdningen kommer att starta. När uppladdningen är klar blir din sfär blå. Du kan också använda `Logcat` fönstret i Android Studio för att övervaka de loggmeddelanden som appen skickar. Till exempel sessionsstatus under bildruteinsamlingar och ankaridentifieraren som molnet returnerar när överföringen är klar.

## <a name="locate-your-cloud-spatial-anchor"></a>Hitta ditt rumsliga ankare i molnet

Ett ankare överförs till molnet, vi är redo att försöka hitta det igen. Låt oss först lägga till följande import i koden.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=45-48&highlight=3-4)]

Sedan ska vi lägga till följande `handleTap()` kod i din metod. Denna kod kommer att:

- Ta bort vår befintliga blå sfär från skärmen.
- Initiera vår Azure Spatial Anchors-session igen. Den här åtgärden kommer att se till att ankaret vi ska hitta kommer från molnet i stället för det lokala ankaret vi skapade.
- Utfärda en fråga för ankaret som vi laddade upp till molnet.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=handleTap&highlight=10-19)]

Nu ska vi koppla koden som kommer att anropas när ankaret vi frågar efter finns. Lägg `initializeSession()` till följande kod i metoden. Det här kodavsnittet skapar & placera en grön sfär när molnets rumsliga ankare finns. Det kommer också att aktivera skärmavtryck igen, så att du kan upprepa hela scenariot en gång till: skapa ett annat lokalt ankare, ladda upp det och hitta det igen.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=initializeSession&highlight=34-53)]

Klart! [Distribuera](#trying-it-out) om appen en sista gång för att testa hela scenariot från början till. Flytta runt enheten och placera din svarta sfär. Fortsätt sedan att flytta enheten för att fånga kamerabildrutor tills sfären blir gul. Ditt lokala ankare laddas upp och din sfär blir blå. Tryck slutligen på skärmen en gång till, så att ditt lokala ankare tas bort och sedan frågar vi efter dess molnmotsvarighet. Fortsätt att flytta runt enheten tills molnets rumsliga ankare finns. En grön sfär ska visas på rätt plats och du kan skölja & upprepa hela scenariot igen.

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-new-android-app-finished.md)]
