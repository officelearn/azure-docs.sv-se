Det sista steget i den här kursen är att skapa och köra den nya appen.

### Läs in projektet i Android Studio och synkronisera Gradle

1. Bläddra till platsen där du sparade de komprimerade projektfilerna och expandera filerna på din dator till din Android Studio-projektmapp.

2. Öppna Android Studio. Om du jobbar med ett projekt och det visas, stänger du projektet (Arkiv = > Stäng projekt).

3. Välj **Öppna ett befintligt Android Studio-projekt**, bläddra till projektets plats och klicka sedan på **OK.** Projektet läses in och synkroniseras med Gradle.

    ![](./media/mobile-services-android-get-started/android-studio-import-project.png)

4. Vänta tills Gradle-synkroniseringen har slutförts. Om du ser felet "det gick inte att hitta målet", beror det på att versionen som används i Android Studio inte matchar exemplet. Det enklaste sättet att åtgärda detta är att klicka på länken **Installera saknade plattformar och synkronisera projektet** i felmeddelandet. Ytterligare felmeddelanden kan visas. Då upprepar du bara processen tills det inte finns några fel.
    - Det finns ett annat sätt att åtgärda detta, om du vill köra den "senaste och bästa" versionen av Android. Du kan uppdatera **targetSdkVersion** i *build.gradle*-filen i *app*-katalogen så att den matchar versionen som finns installerad på din dator. Du kan se versionen genom att klicka på ikonen för **SDK-hanteraren**. Därefter trycker du på **Synkronisera projekt med Gradle-filer**. Du kan få ett felmeddelande för versionen av Build Tools, vilket åtgärdas på samma sätt.

### Köra appen

Du kan köra appen med emulatorn eller med en faktisk enhet.

1. För att köra från en enhet, ansluter du den till din dator via en USB-kabel. Du måste [ställa in enheten i utvecklingsläge](https://developer.android.com/training/basics/firstapp/running-app.html). Om du utvecklar på en Windows-dator, måste du också hämta och installera en USB-drivrutin.

2. För att kunna köra via Android-emulatorn måste du definiera minst en Android Virtual Device (AVD). Klicka på ikonen för AVD-hanteraren för att skapa och hantera de här enheterna.

3. Från **Kör**-menyn klickar du på **Kör** för att starta projektet. och väljer en enhet eller emulator från dialogrutan som visas.

4. När appen visas anger du en beskrivande text, till exempel _Slutför guiden_ och klickar sedan på **Lägg till**.

    ![](./media/mobile-services-android-get-started/mobile-quickstart-startup-android.png)

    Därmed skickas en POST-begäran till den nya mobiltjänsten som finns på Azure. Data från begäran infogas i tabellen TodoItem. Objekt som lagras i tabellen returneras av mobiltjänsten och data visas i listan.

    > [AZURE.NOTE] Du kan granska koden som ansluter till din mobiltjänst för att fråga efter och infoga data som finns i ToDoActivity.java-filen.

8. I den klassiska Azure-portalen klickar du på **Data**-fliken och sedan på **TodoItems**-tabellen.

    ![](./media/mobile-services-android-get-started/mobile-data-tab1.png)

    Nu kan du bläddra bland de data som infogats i tabellen via appen.

    ![](./media/mobile-services-android-get-started/mobile-data-browse.png)


<!--HONumber=Sep16_HO3-->


