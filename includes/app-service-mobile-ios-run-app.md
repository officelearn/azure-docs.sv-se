
1. Besök [Azure Portal] på din Mac. Klicka på **Bläddra alla** > **Mobile Apps** > serverdelen som du nyss skapade. I inställningarna för mobilappar klickar du på **Snabbstart** > **iOS (Objective-C)**. Om du föredrar Swift klickar du på **Snabbstart** > **iOS (Swift)** istället. Under **Hämta och kör ditt iOS-projekt** klickar du på **Hämta**. Ett fullständigt Xcode-projekt för en app som förkonfigurerats att ansluta till din serverdel hämtas. Öppna projektet med Xcode.
2. Klicka på **Kör**-knappen för att skapa projektet och starta appen i iOS-simulatorn.
3. Ange en beskrivande text i appen, till exempel *Slutföra kursen*, och klicka sedan på plustecknet (**+**). Detta skickar en POST-begäran till den Azure-serverdel som du distribuerade tidigare. Serverdelen infogar data från begäran i TodoItem-SQL-tabellen och returnerar information om det nyligen lagrade objektet tillbaka till mobilappen. Mobilappen visar dessa data i listan. 

   ![Snabbstart för appar som körs i iOS](./media/app-service-mobile-ios-quickstart/mobile-quickstart-startup-ios.png)

[Azure Portal]: https://portal.azure.com/


<!--HONumber=Nov16_HO3-->


