På grund av pågående utveckling, kan den Android SDK-version som installeras i Android Studio inte matchar versionen i koden. Android SDK som hänvisas till i den här kursen är version 26, senast vid tidpunkten för skrivning. Versionsnumret kan öka när nya versioner av SDK visas, och vi rekommenderar att du använder den senaste versionen.

Två symptom på versionsmatchningsfel är:

- När du skapar eller återskapa projektet, kan du få Gradle felmeddelanden som `Gradle sync failed: Failed to find target with hash string 'android-XX'`.
- Android Standardobjekt i koden som ska matcha baserat på `import` instruktioner kan generera felmeddelanden.

Om något av dessa visas överensstämmer SDK-målet för det hämta projektet inte med versionen av Android SDK installerad i Android Studio. Om du vill kontrollera vilken version du göra följande ändringar:

1. I Android Studio klickar du på **verktyg** > **Android** > **SDK Manager**. Om du inte har installerat den senaste versionen av SDK-plattformen, klicka om du vill installera den. Anteckna versionsnumret.

2. På den **Projektutforskaren** fliken, under **Gradle skript**, öppna filen **build.gradle (modul: app)**. Se till att den **compileSdkVersion** och **targetSdkVersion** är inställda på att SDK-version som är installerad. Den `build.gradle` kan se ut så här:

    ```gradle
    android {
        compileSdkVersion 26
        defaultConfig {
            targetSdkVersion 26
        }
    }
    ```
