Kunden von Azure können monatlich 25.000 kostenlose E-Mails freischalten. Diesen kostenlosen 25.000 monatlichen e-Mails erhalten Sie Zugriff auf Erweiterte Berichterstattung und Analyse und [alle APIs][] (Web, SMTP, Ereignis, Analyse und mehr). Informationen über die zusätzlichen Dienste von SendGrid finden Sie unter der [SendGrid Features][] Seite.

### So registrieren Sie ein SendGrid-Konto

1. Melden Sie sich auf die [Azure-Verwaltungsportal][].

2. Klicken Sie im unteren Bereich des Verwaltungsportals auf **Neu**.

    ![command-bar-new][command-bar-new]

3. Klicken Sie auf **Marketplace**.

    ![sendgrid-store][sendgrid-store]

4. In der **Wählen Sie eine Anwendung und Dienst** Dialogfeld **SendGrid** und klicken Sie auf den Pfeil nach rechts.

5. In der **Personalize Application and Service** Dialogfeld Wählen Sie die **SendGrid** Plan, die Sie registrieren möchten.

6. Geben Sie einen Namen zur Identifizierung Ihrer **SendGrid** -Diensts in Ihren Azure-Einstellungen, oder verwenden Sie den Standardwert **SendGridEmailDelivery.Simplified.SMTPWebAPI**. Namen müssen zwischen 1 und 100 Zeichen lang sein und dürfen nur alphanumerische Zeichen, Gedankenstriche, Punkte und Unterstriche enthalten. Der Name muss in der Liste der abonnierten Azure Store-Artikel eindeutig sein.

    ![store-screen-2][store-screen-2]

7. Wählen Sie einen Wert für die Region aus; z. B. West-USA.

8. Klicken Sie auf den Pfeil nach rechts.

9. Auf der **Kauf überprüfen** Registerkarte, überprüfen Sie den Plan und Preisinformationen sowie die rechtlichen Bedingungen. Aktivieren Sie das Kontrollkästchen, wenn Sie den Bedingungen zustimmen. Klicken Sie auf das Häkchen, beginnt der SendGrid-Kontos die [SendGrid provisioning process].

    ![store-screen-3][store-screen-3]

10. Nach der Bestätigung Ihres Kaufs werden Sie zum Add-ons-Dashboard umgeleitet, und Sie sehen die Meldung **Purchasing SendGrid**.

    ![sendgrid-purchasing-message][sendgrid-purchasing-message]

    Ihr SendGrid-Konto wird sofort bereitgestellt, und Sie sehen die Meldung **Successfully purchased Add-On SendGrid**. Jetzt erfolgt die Erstellung Ihres Kontos und Ihrer Anmeldedaten. Von nun an können Sie E-Mails versenden. 

    Wenn Sie Ihren Abonnementplan ändern oder die SendGrid-Kontakteinstellungen anzeigen möchten, klicken Sie auf den Namen des SendGrid-Diensts, um das SendGrid-Marketplace-Dashboard zu öffnen. 

    ![sendgrid-add-on-dashboard][sendgrid-add-on-dashboard]

    Um eine e-Mail über SendGrid zu senden, müssen Sie Ihre Anmeldeinformationen (Benutzername und Kennwort) angeben.

### So finden Sie Ihre SendGrid-Anmeldeinformationen ###

1. Klicken Sie auf **Verbindungsinformationen**.

    ![sendgrid-connection-info-button][sendgrid-connection-info-button]

2. In der *Verbindungsinformationen* Kopieren der **Kennwort** und der Benutzername, der später in diesem Lernprogramm verwenden.

    ![sendgrid-connection-info][sendgrid-connection-info]

    Um Ihre e-Mail-Adresse Einstellungen für die Zustellung festzulegen, klicken Sie auf die **verwalten** Schaltfläche. Hiermit werden Sie zur SendGrid-Systemsteuerung umgeleitet. 

    ![sendgrid-control-panel][sendgrid-control-panel]

    Weitere Informationen zu den ersten Schritten mit SendGrid finden Sie unter [SendGrid Getting Started][].

<!--images-->

[command-bar-new]: ./media/sendgrid-sign-up/sendgrid_BAR_NEW.PNG
[sendgrid-store]: ./media/sendgrid-sign-up/sendgrid_offerings_store.png
[store-screen-2]: ./media/sendgrid-sign-up/sendgrid_store_scrn2.png
[store-screen-3]: ./media/sendgrid-sign-up/sendgrid_store_scrn3.png
[sendgrid-purchasing-message]: ./media/sendgrid-sign-up/sendgrid_purchasing_message.png
[sendgrid-add-on-dashboard]: ./media/sendgrid-sign-up/sendgrid_add-on_dashboard.png
[sendgrid-connection-info]: ./media/sendgrid-sign-up/sendgrid_connection_info.png
[sendgrid-connection-info-button]: ./media/sendgrid-sign-up/sendgrid_connection_info_button.png
[sendgrid-control-panel]: ./media/sendgrid-sign-up/sendgrid_control_panel.png

<!--Links-->

[SendGrid Features]: http://sendgrid.com/features
[Azure Management Portal]: https://manage.windowsazure.com
[SendGrid Getting Started]: http://sendgrid.com/docs
[SendGrid Provisioning Process]: https://support.sendgrid.com/hc/articles/200181628-Why-is-my-account-being-provisioned-
[all APIs]: https://sendgrid.com/docs/API_Reference/index.html



