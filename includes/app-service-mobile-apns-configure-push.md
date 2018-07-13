

1. Starta på en Mac **Nyckelhanterare**. I det vänstra navigeringsfältet under **kategori**öppnar **mina certifikat**. Hitta SSL-certifikatet som du hämtade i föregående avsnitt och lämna ut innehållet. Välj endast certifikatet (Välj inte den privata nyckeln). Sedan [exportera det](https://support.apple.com/kb/PH20122?locale=en_US).
2. I den [Azure-portalen](https://portal.azure.com/)väljer **Bläddra bland alla** > **Apptjänster**. Sedan väljer Mobile Apps serverdel. 
3. Under **inställningar**väljer **App Service Push**. Välj sedan namnet på din meddelandehubb. 
3. Gå till **Apple Push Notification Services** > **överför certifikat**. Ladda upp .p12-filen som att välja rätt **läge** (beroende på om dina SSL-klientcertifikat från tidigare är produktions- eller sandbox). Spara ändringarna.

Din tjänst har nu konfigurerats för att fungera med push-meddelanden på iOS.

[1]: ./media/app-service-mobile-apns-configure-push/mobile-push-notification-hub.png
