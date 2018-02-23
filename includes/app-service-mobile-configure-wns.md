
1. I den [Azure-portalen](https://portal.azure.com/)väljer **Bläddra bland alla** > **Apptjänster**. Sedan väljer Mobile Apps serverdel. Under **inställningar**väljer **App Service Push**. Välj sedan namnet på din meddelandehubb.
2. Gå till **Windows (WNS)**. Ange den **säkerhetsnyckeln** (klienthemlighet) och **paket-SID** som du hämtade från webbplatsen för Live-tjänster. Välj därefter **spara**.

    ![Ange nyckeln WNS i portalen](./media/app-service-mobile-configure-wns/mobile-push-wns-credentials.png)

Din serverdel har nu konfigurerats för att använda WNS för att skicka push-meddelanden.
