

Die neuen Twitter v1.1 APIs erfordern eine Authentifizierung Ihrer App, um auf Ressourcen zugreifen zu können. Zunächst müssen Sie sich die Anmeldedaten für Zugriff mithilfe von OAuth 2.0 besorgen. Anschließend speichern Sie diese sicher in den App-Einstellungen für Ihren mobilen Service.

1. Sofern noch nicht geschehen, führen Sie die Schritte im Thema <a href="../articles/mobile-services/mobile-services-how-to-register-twitter-authentication.md/" target="_blank">Registrieren Ihrer App für die Twitter-Anmeldung mit Mobile Services</a> durch. 
  
    Twitter erstellt die Anmeldedaten, mithilfe derer Sie auf Twitter v1.1 APIs zugreifen können. Diese Anmeldeinformationen erhalten Sie über die Website für Twitter-Entwickler. 

2. Navigieren Sie zur Website für <a href="http://go.microsoft.com/fwlink/p/?LinkId=268300" target="_blank">Twitter-Entwickler</a>, melden Sie sich mit den Anmeldeinformationen für Ihr Twitter-Konto an, und wählen Sie Ihre Twitter-App aus.

3. In der **Schlüssel und Zugriffstoken** Registerkarte für die app, notieren Sie die folgenden Werte:

    + **Consumer key**
    + **Consumer secret**
    + **Access token**
    + **Access token secret**

4. Melden Sie sich auf die [klassischen Azure-Portal](https://manage.windowsazure.com/), klicken Sie auf **Mobile Services**, und klicken Sie dann auf Ihren mobilen Dienst.

5. Klicken Sie auf die **Identität** Geben die **Consumer Key** und **Consumer Secret** Werte aus Twitter, und klicken Sie auf **Speichern**. 

    ![](./media/mobile-services-register-twitter-access/mobile-identity-tab-twitter-only.png)

2. Klicken Sie auf die **konfigurieren** Registerkarte, einen Bildlauf nach unten zum **App-Einstellungen** und geben Sie einen **Namen** und **Wert** Paar für jedes der folgenden Elemente, die Sie von der Twitter-Site erhalten haben, und klicken Sie dann **Speichern**.

    + `TWITTER_ACCESS_TOKEN`
    + `TWITTER_ACCESS_TOKEN_SECRET`

    ![](./media/mobile-services-register-twitter-access/mobile-schedule-job-app-settings.png)

    Dadurch wird das Twitter-Zugriffstoken in den App-Einstellungen gespeichert. Wie die Benutzeranmeldeinformationen auf der **Identität** Registerkarte Kontoanmeldeinformationen werden auch in app-Einstellungen verschlüsselt gespeichert, und Sie können darauf in Ihren Serverskripts zugreifen, ohne die Daten hart in der Skriptdatei. Weitere Informationen finden Sie unter [App settings].

<!-- URLs. -->
[Mobile Services server script reference]: http://go.microsoft.com/fwlink/?LinkId=262293
[Register your apps for Twitter login with Mobile Services]: ../articles/mobile-services/mobile-services-how-to-register-twitter-authentication.md
[Twitter Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[App settings]: http://msdn.microsoft.com/library/azure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7

