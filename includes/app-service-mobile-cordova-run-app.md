
1. Besök [Azure Portal]. Klicka på **Bläddra alla** > **Mobile Apps** > serverdelen som du nyss skapade. I inställningarna för mobilappar, klickar du på **Snabbstart** > **Cordova**. Under **Konfigurera klientprogrammet**, väljer du **Skapa en ny app**, och klickar sedan på **Hämta**. Då hämtas ett fullständigt Cordova-projekt för en app som är förkonfigurerad att ansluta till din serverdel.

2. Packa upp den hämtade ZIP-filen i en katalog på hårddisken, navigera till lösningsfilen (.sln) och öppna den med hjälp av Visual Studio.

5. I Visual Studio väljer du lösningsplattform (Android, iOS eller Windows) från listrutan bredvid startpilen och väljer därefter en viss distributionsenhet eller emulator genom att klicka på listrutan vid den gröna pilen. Observera att du kan använda Android-plattformen och Ripple-emulatorn av standardtyp. Mer avancerade självstudier kräver att du väljer en enhet som stöds eller en emulator. 

6. Tryck på F5 eller klicka på den gröna pilen för att skapa och köra din Cordova-app. Om du ser en säkerhetsdialogruta i emulatorn som begär åtkomst till nätverket, accepterar du begäran.   

7. Efter att appen har startats på enheten eller emulatorn, skriver du in en beskrivande text i **Ange ny text**, exempelvis _Genomför självstudierna_, och klickar sedan på **Lägg till**-knappen.  
Detta skickar en POST-begäran till den Azure-serverdel som du distribuerade tidigare. Serverdelen infogar data från begäran i TodoItem-tabellen i SQL Database och returnerar information om det nyligen lagrade objekten tillbaka till mobilappen. Mobilappen visar dessa data i listan.

    ![](./media/app-service-mobile-cordova-quickstart/quickstart-startup.png)
    
8. Upprepa föregående tre steg för varje enhetsplattform som du planerar att stödja.

[Azure Portal]: https://portal.azure.com/


<!--HONumber=Sep16_HO3-->


