## <a name="register-app-aad"></a>Registrieren der Client-App in Azure Active Directory

1. Navigieren Sie zu **Active Directory** in die [klassischen Azure-Portal](https://manage.windowsazure.com/), klicken Sie dann auf Ihr Verzeichnis.

   ![](./media/mobile-services-dotnet-adal-register-client/mobile-services-select-aad.png)

2. Klicken Sie auf die **Applikationen** oben die Registerkarte, und klicken Sie dann auf **Hinzufügen** einer app. 

   ![](./media/mobile-services-dotnet-adal-register-client/mobile-services-aad-applications-tab.png)

3. Klicken Sie auf **Hinzufügen einer Anwendung ist das Entwickeln von meinem Unternehmen**.

4. Geben Sie im Assistenten zum Hinzufügen einer Anwendung eine **Namen** für Ihre Anwendung und klicken Sie auf die  **systemeigene Clientanwendung** Typ. Klicken Sie dann auf Continue.

   ![](./media/mobile-services-dotnet-adal-register-client/mobile-services-native-selection.png)

5. In der **Umleitungs-URI** Geben Sie den/Login/done-Endpunkt für Ihren mobilen Dienst. Dieser Wert sollte so ähnlich lauten wie https://todolist.azure-mobile.net/login/done.

   ![](./media/mobile-services-dotnet-adal-register-client/mobile-services-native-redirect-uri.png)

6. Klicken Sie auf die **konfigurieren** Registerkarte für die systemeigene Anwendung, und kopieren die **Client-ID**. Sie benötigen sie später.

   ![](./media/mobile-services-dotnet-adal-register-client/mobile-services-native-client-id.png)

7. Blättern Sie nach unten, um die **Berechtigungen für andere Anwendungen** Abschnitt, und klicken Sie auf die **-Anwendung hinzufügen** Schaltfläche. Wählen Sie **andere** aus dem Menü "anzeigen" und suchen Sie nach Todo. Klicken Sie auf **TodoList** Hinzufügen des mobilen Diensts, die Sie zuvor registriert haben, und klicken Sie auf das Häkchen, zum Abschluss. Gewähren Sie Zugriff auf die mobile Dienstanwendung. Klicken Sie dann auf **Speichern**

   ![](./media/mobile-services-dotnet-adal-register-client/mobile-services-native-add-permissions.png)

Der mobile Dienst ist jetzt in AAD so konfiguriert, dass er SSO-Anmeldungen von Ihrer Anwendung empfangen kann.


