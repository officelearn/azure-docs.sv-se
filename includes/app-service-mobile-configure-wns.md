
1. I den [Azure-portalen](https://portal.azure.com/), klickar du på **Bläddra bland alla** > **Apptjänster**, och klicka på din Mobile Apps-serverdel. Under **inställningar**, klickar du på **App Service Push**, och klicka sedan på namnet på din meddelandehubb.
2. Gå till **Windows (WNS)**, ange den **säkerhetsnyckeln** (klienthemlighet) och **paket-SID** som du fick från webbplatsen för Live-tjänster och klicka sedan på **spara** .

    ![Ange nyckeln WNS i portalen](./media/app-service-mobile-configure-wns/mobile-push-wns-credentials.png)

Din serverdel har nu konfigurerats för att använda WNS för att skicka push-meddelanden.
