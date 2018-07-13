
1. I den [Azure-portalen](https://portal.azure.com/)väljer **Bläddra bland alla** > **Apptjänster**. Sedan väljer Mobile Apps serverdel. Under **inställningar**väljer **App Service Push**. Välj sedan namnet på din meddelandehubb.
2. Gå till **Windows (WNS)**. Ange sedan den **säkerhetsnyckel** (klienthemlighet) och **paket-SID** som du fick från webbplatsen för Live-tjänster. Välj sedan **spara**.

    ![Ange WNS-nyckeln i portalen](./media/app-service-mobile-configure-wns/mobile-push-wns-credentials.png)

Serverdelen har nu konfigurerats så att WNS för att skicka push-meddelanden.
