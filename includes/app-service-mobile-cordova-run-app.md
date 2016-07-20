
1. Besök [Azure Portal]. Klicka på **Bläddra alla** > **Mobile Apps** > serverdelen som du nyss skapade. I inställningarna för mobilappar, klickar du på **Snabbstart** > **Cordova**. Under **Konfigurera klientprogrammet**, väljer du **Skapa en ny app**, och klickar sedan på **Hämta**. Då hämtas ett fullständigt Cordova-projekt för en app som är förkonfigurerad att ansluta till din serverdel.

2. Packa upp den hämtade ZIP-filen till en katalog på din hårddisk.

3. Öppna projektet med **Visual Studio**.  Klicka på **Öppna** > **Projekt/lösning...**.

4. Sök efter filen _sitename_.sln och klicka på **Öppna**.

5. Standard-emulatorn är **Ripple – Nexus (Galaxy)**.  Klicka på listrutepilen bredvid emulatorn och välj **Google Android-emulator**.

6. Klicka på **Google Android-emulator**.  Projektet kommer att skapas och sedan köras.  Det är möjligt att en nätverkssäkerhetsvarning visas från Google Android-emulatorn som ber om åtkomst till nätverket.  Till slut kommer Google Android-emulatorn att visas och ditt program kommer att köras.

7. Ange en beskrivande text i appen, till exempel _Slutför guiden_, och klicka sedan på Lägg till-knappen. Detta skickar en POST-begäran till den Azure-serverdel som du distribuerade tidigare. Serverdelen infogar data från begäran i TodoItem-SQL-tabellen och returnerar information om det nyligen lagrade objektet tillbaka till mobilappen. Mobilappen visar dessa data i listan.

    ![](./media/app-service-mobile-cordova-quickstart/quickstart-startup.png)

[Azure Portal]: https://portal.azure.com/



<!--HONumber=Jun16_HO2-->


