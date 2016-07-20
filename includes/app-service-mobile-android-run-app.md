
1. Besök [Azure Portal]. Klicka på **Bläddra alla** > **Mobile Apps** > serverdelen som du nyss skapade. I inställningarna för mobilappar klickar du på **Snabbstart** > **Android)**. Under **Konfigurera klientprogrammet** klickar du på **Hämta**. Då hämtas ett fullständigt Android-projekt för en app som är förkonfigurerad att ansluta till din serverdel. 

2. Öppna projektet med **Android Studio** med hjälp av **Importera projekt (Eclipse ADT, Gradle, osv.)**. Kontrollera att du har gjort det här importvalet för att undvika eventuella JDK-fel.

3. Tryck på knappen **Kör app** för att skapa projektet och starta appen i Android-simulatorn.

4. Ange en beskrivande text i appen, till exempel _Slutför guiden_, och klicka sedan på Lägg till-knappen. Detta skickar en POST-begäran till den Azure-serverdel som du distribuerade tidigare. Serverdelen infogar data från begäran i TodoItem-SQL-tabellen och returnerar information om det nyligen lagrade objektet tillbaka till mobilappen. Mobilappen visar dessa data i listan. 

    ![](./media/mobile-services-android-get-started/mobile-quickstart-startup-android.png)

[Azure Portal]: https://portal.azure.com/



<!--HONumber=Jun16_HO2-->


