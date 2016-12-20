
1. Besök [Azure Portal].
2. Klicka på **Bläddra alla** > **Mobile Apps** > serverdelen som du har skapat.
3. I inställningarna för mobilappar, klickar du på **Snabbstart** > **Cordova**.
4. Under **Konfigurera klientprogrammet**, väljer du **Skapa en ny app**, och klickar sedan på **Hämta**.
2. Packa upp den hämtade ZIP-filen i en katalog på hårddisken, navigera till lösningsfilen (.sln) och öppna den med hjälp av Visual Studio.
3. I Visual Studio väljer du lösningsplattform (Android, iOS eller Windows) från listrutan bredvid startpilen. Välj en viss distributionsenhet eller emulator genom att klicka på den gröna pilen i listrutan. Du kan använda Android-plattformen och Ripple-emulatorn av standardtyp. För mer avancerade självstudier (t.ex, push-meddelanden) krävs att du väljer en enhet eller emulator som stöds.
4. Tryck på F5 eller klicka på den gröna pilen för att skapa och köra din Cordova-app. Om du ser en säkerhetsdialogruta i emulatorn som begär åtkomst till nätverket, accepterar du begäran.
5. Efter att appen har startats på enheten eller emulatorn, skriver du in en beskrivande text i **Ange ny text**, exempelvis *Genomför självstudierna*, och klickar sedan på **Lägg till**-knappen.

Serverdelen infogar data från begäran i TodoItem-tabellen i SQL Database och returnerar information om det nyligen lagrade objekten tillbaka till mobilappen. Mobilappen visar dessa data i listan.

![](./media/app-service-mobile-cordova-quickstart/quickstart-startup.png)

Du kan upprepa steg 3 till 5 för andra plattformar.

[Azure Portal]: https://portal.azure.com/


<!--HONumber=Dec16_HO1-->


