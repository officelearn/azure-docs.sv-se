1. Registrieren Sie Ihre Mobile App-Back-End mit Ihrem Azure Active Directory-Mandanten anhand der [How to configure your Mobile App with Azure Active Directory] Thema.

2. Navigieren Sie zu **Active Directory** in der [Azure classic portal]

   ![](./media/app-service-mobile-adal-register-app/app-service-navigate-aad.png)

3. Wählen Sie Ihr Verzeichnis, und wählen Sie dann die **Applikationen** oben auf die Registerkarte. Klicken Sie auf **Hinzufügen** unten, um eine neue app-Registrierung zu erstellen. 

4. Klicken Sie auf **Hinzufügen einer Anwendung ist das Entwickeln von meinem Unternehmen**.

5. Geben Sie im Assistenten zum Hinzufügen einer Anwendung eine **Namen** für Ihre Anwendung und klicken Sie auf die  **systemeigene Clientanwendung** Typ. Klicken Sie dann auf Continue.

6. In der **Umleitungs-URI** Geben Sie den/Login/done-Endpunkt für Ihr App Service-Gateway. Dieser Wert sollte so ähnlich lauten wie https://contoso.azurewebsites.net/login/done.

7. Nachdem die systemeigene Anwendung hinzugefügt wurde, klicken Sie auf die **konfigurieren** Registerkarte. Kopieren der **Client-ID**. Sie benötigen sie später.

8. Blättern Sie nach unten, um die **Berechtigungen für andere Anwendungen** Abschnitt, und klicken Sie auf **Anwendung hinzufügen**.

9. Suchen Sie nach der zuvor registrierten Web-App, und klicken Sie auf das Pluszeichen. Klicken Sie dann auf das Häkchen, um das Dialogfeld zu schließen.

10. Für den neuen Eintrag, die Sie gerade hinzugefügt haben, öffnen Sie die **delegierte Berechtigungen** Dropdown-Liste, und wählen Sie **Zugriff (Anwendungsname)**. Klicken Sie dann auf **Speichern**

   ![](./media/app-service-mobile-adal-register-app/aad-native-client-add-permissions.png)

Die Anwendung wird jetzt in AAD so konfiguriert, dass Benutzer über AAD die einmalige Anmeldung nutzen können.

[Azure classic portal]: https://manage.windowsazure.com/
[How to configure your Mobile App with Azure Active Directory]: ../articles/app-service-how-to-configure-active-directory-authentication.md


