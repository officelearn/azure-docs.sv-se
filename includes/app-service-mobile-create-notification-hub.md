Um einen neuen Notification Hub zur Verwendung für Pushbenachrichtigungen zu erstellen, gehen Sie wie folgt vor. Wenn Sie bereits über einen Notification Hub verfügen, können Sie ihn auch mit dem Back-End Ihrer Mobile App verbinden. 

1. In der [Azure Portal], klicken Sie auf **Durchsuchen** > **Anwendungsdienste**, klicken Sie dann auf Ihre Mobile App-Back-End > **Alle Einstellungen**, und wählen Sie unter **Mobile** Klicken Sie auf **Push** > **Notification Hub**.

2. Klicken Sie auf **+ Notification Hub**, geben Sie einen neuen **Notification Hub** Name kann derselbe sein wie Ihre Mobile App-Back-End, geben Sie einen neuen Namespacenamen oder einen vorhandenen zu verwenden und dann auf **OK** und schließlich **Erstellen**.

    ![](./media/app-service-mobile-create-notification-hub/create-new-hub-flow.png)

    Dadurch wird ein neuer Notification Hub erstellt und verbindet ihn mit Ihrer mobilen App. Wenn Sie über einen vorhandenen Notification Hub verfügen, können Sie diesen mit Ihrem Mobile App-Back-End verbinden, statt einen neuen zu erstellen.

Jetzt haben Sie einen Nofitication Hub mit Ihrem Mobile App-Back-End verbunden. Später konfigurieren Sie diesen Notification Hub so, dass eine Verbindung mit einem Platform Notification Service (PNS) hergestellt wird, der an das systemeigene Gerät Pushbenachrichtigungen sendet.

[Azure Portal]: https://portal.azure.com/

