## <a name="register-mobile-service-aad"></a>Registrieren des mobilen Diensts in Azure Active Directory


In diesem Abschnitt registrieren Sie den mobilen Dienst in Azure Active Directory und konfigurieren Berechtigungen, um Identitätswechsel für das einmalige Anmelden zu ermöglichen.

1. Registrieren Ihrer Anwendung in Azure Active Directory anhand der [How to Register with the Azure Active Directory] Thema.

2. In der [Clasic Azure-Portal](https://manage.windowsazure.com/), wechseln Sie zurück zur Azure Active Directory-Erweiterung, und klicken Sie auf active Directory

3. Klicken Sie auf die **Applikationen** Registerkarte, und klicken Sie dann auf Ihre Anwendung.

4. Klicken Sie auf **Manifest verwalten**. Klicken Sie dann auf **Manifest herunterladen** und speichern Sie das Anwendungsmanifest in einem lokalen Verzeichnis.

   ![](./media/mobile-services-dotnet-adal-register-service/mobile-services-aad-app-manage-manifest.png)

5. Öffnen Sie die Anwendungsmanifestdatei in Visual Studio. Suchen Sie am Anfang der Datei die Zeile mit den Anwendungsberechtigungen, die etwas wie folgt aussieht:

        "oauth2Permissions": [],

    Ersetzen Sie diese Zeile durch die folgenden Anwendungsberechtigungen, und speichern Sie die Datei.

        "oauth2Permissions": [
            {
                "adminConsentDescription": "Allow the application access to the mobile service",
                "adminConsentDisplayName": "Have full access to the mobile service",
                "id": "b69ee3c9-c40d-4f2a-ac80-961cd1534e40",
                "isEnabled": true,
                "origin": "Application",
                "type": "User",
                "userConsentDescription": "Allow the application full access to the mobile service on your behalf",
                "userConsentDisplayName": "Have full access to the mobile service",
                "value": "user_impersonation"
            }
        ],

6. In der [klassischen Azure-Portal](https://manage.windowsazure.com/), klicken Sie auf **Manifest verwalten** für die Anwendung erneut aus und klicken Sie auf **Manifest hochladen**.  Navigieren Sie zum Speicherort des Anwendungsmanifests, das Sie gerade aktualisiert haben, und laden Sie das Manifest hoch.

<!-- URLs. -->
[How to Register with the Azure Active Directory]: ../articles/mobile-services/mobile-services-how-to-register-active-directory-authentication.md


