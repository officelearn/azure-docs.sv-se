
1. Högerklicka på Windows Store-app-projekt i Visual Studio Solution Explorer och klicka på **Store** > **associera appen med butiken**.

    ![Associera appen med Windows Store](./media/app-service-mobile-register-wns/notification-hub-associate-win8-app.png)
2. I guiden klickar du på **nästa**, och logga in med ditt Microsoft-konto. Skriv ett namn för din app i **reservera ett nytt namn för appen**, och klicka sedan på **reservera**.
3. Efter registreringen app har skapats, Välj ny programnamn, klicka på **nästa**, och klicka sedan på **associera**. Detta lägger till den registreringsinformation som krävs för Windows Store i programmanifestet.
4. Upprepa steg 1 och 3 för Windows Phone Store-app-projekt med samma registrering som du skapade tidigare för Windows Store-app.  
5. Bläddra till den [Windows Dev Center](https://dev.windows.com/en-us/overview), och logga in med ditt Microsoft-konto. Klicka på den nya app registreringen i **Mina appar**, och expandera sedan **Services** > **Push-meddelanden**.
6. På den **Push-meddelanden** klickar du på **webbplatsen Live-tjänster** under **Windows Push Notification Services (WNS) och Microsoft Azure Mobile Apps**. Anteckna värdena för den **paket-SID** och *aktuella* värde i **Programhemlighet**. 

    ![Appinställningen i developer center](./media/app-service-mobile-register-wns/mobile-services-win8-app-push-auth.png)

   > [!IMPORTANT]
   > Programhemligheten och paket-SID:et är viktiga säkerhetsuppgifter. Lämna aldrig ut dessa uppgifter till någon och distribuera dem inte tillsammans med din app.
   >
   >
