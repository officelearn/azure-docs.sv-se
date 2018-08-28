1. Besök [Azure-portalen] på din Mac. Klicka på **Alla tjänster** > **App Services** > serverdelen du precis har skapat. I mobilappens inställningar väljer du det språk du vill använda:

    - Objective-C &ndash; **Snabbstart** > **iOS (Objective-C)**
    - Swift &ndash; **Snabbstart** > **iOS (Swift)**

    Under **3. Konfigurera klientprogrammet** klickar du på **Ladda ned**. Ett fullständigt Xcode-projekt som förkonfigurerats att ansluta till din serverdel hämtas. Öppna projektet med Xcode.

1. Klicka på **Kör**-knappen för att skapa projektet och starta appen i iOS-simulatorn.

1. Ange en beskrivande text i appen, till exempel *Slutföra kursen*, och klicka sedan på plustecknet (**+**). Detta skickar en POST-begäran till den Azure-serverdel som du distribuerade tidigare. Serverdelen infogar data från begäran i TodoItem-SQL-tabellen och returnerar information om det nyligen lagrade objektet tillbaka till mobilappen. Mobilappen visar dessa data i listan.

   ![Snabbstart för appar som körs i iOS](./media/app-service-mobile-ios-quickstart/mobile-quickstart-startup-ios.png)

[Azure-portalen]: https://portal.azure.com/
