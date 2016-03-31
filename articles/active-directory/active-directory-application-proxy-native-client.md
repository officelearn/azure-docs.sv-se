<properties
    pageTitle="Veröffentlichen von systemeigenen Client-Apps mit Proxyanwendungen | Microsoft Azure"
    description="Erläutert, wie Sie es systemeigenen Client-Apps ermöglichen, mit einem Azure AD-Anwendungsproxyconnector zu kommunizieren und so einen sicheren Remotezugriff auf lokale Anwendungen bereitzustellen."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="stevenpo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/02/2015"
    ms.author="kgremban"/>

# Aktivieren von systemeigenen Client-Apps für die Interaktion mit Proxyanwendungen
Mit Azure Active Directory-Anwendungsproxy werden häufig Browseranwendungen wie SharePoint, Outlook Web Access und benutzerdefinierte Branchenanwendungen veröffentlicht. Außerdem können damit HTTP-Back-End-Anwendungen veröffentlicht werden, die über systemeigene Clients genutzt werden. Dies erfolgt durch die Unterstützung von durch Azure AD ausgegebenen Token, die in standardmäßigen Authorize- HTTP-Headern gesendet werden.


![](./media/active-directory-application-proxy-native-client/richclientflow.png)


Zum Veröffentlichen solcher Anwendungen wird empfohlen, die Azure AD-Authentifizierungsbibliothek zu verwenden. Sie übernimmt die aufwendige Authentifizierung und unterstützt viele verschiedene Clientumgebungen. Anwendungsproxy passt in die [systemeigene Anwendung zu Web-API-Szenario](active-directory-authentication-scenarios.md#native-application-to-web-api). Die Vorgehensweise hierfür ist wie folgt:

1. Veröffentlichen Sie Ihre Proxyanwendung wie jede andere Anwendung, weisen Sie Benutzer zu und geben Sie ihnen Premium- oder Basic-Lizenzen. Weitere Informationen finden Sie unter  [Veröffentlichen von Anwendungen mit dem Anwendungsproxy](active-directory-application-proxy-publish.md).
2. Konfigurieren Sie Ihre systemeigene Anwendung wie folgt:
  3. Melden Sie sich beim Azure-Verwaltungsportal an.
  4. Klicken Sie im linken Menü auf das Active Directory-Symbol, und klicken Sie dann auf das gewünschte Verzeichnis.
  5. Klicken Sie im oberen Menü auf Anwendungen. Wenn Ihrem Verzeichnis keine Apps hinzugefügt wurden, wird auf dieser Seite der Link "App hinzufügen" angezeigt. Klicken Sie auf den Link, oder klicken Sie alternativ auf der Befehlsleiste auf die Schaltfläche "Hinzufügen".
  4. Auf der **Was möchten Sie tun** Seite, klicken Sie auf den Link zum **eine Anwendung ist das Entwickeln von meinem Unternehmen hinzufügen**.
  5. Auf der **Teilen Sie uns Ihre Anwendung** Seite, geben Sie einen Namen für die Anwendung aus, und wählen Sie **Native Client-Anwendung** darstellt, die eine Anwendung, die auf einem Gerät wie z. B. einem Telefon oder Computer installiert ist. Klicken Sie danach auf das Pfeilsymbol in der rechten unteren Ecke der Seite.
  6. Auf der **Anwendungseigenschaften** Seite der **Redirect URI** für die native Clientanwendung, klicken Sie dann auf das Kontrollkästchen in der unteren rechten Ecke der Seite. </br>Ihre Anwendung wird hinzugefügt, und Sie werden auf die Seite "Schnellstart" für Ihre Anwendung umgeleitet.
8. Machen Sie die systemeigene Anwendung für andere Anwendungen in Ihrem Verzeichnis verfügbar:
  9. Klicken Sie im oberen Menü auf **Applikationen**, wählen Sie die neue systemeigene Anwendung, und klicken Sie dann auf **konfigurieren**.
  10. Führen Sie einen Bildlauf nach unten, um die **Berechtigungen für andere Anwendungen** Abschnitt. Klicken Sie auf die **-Anwendung hinzufügen** Schaltfläche und wählen Sie die Proxyanwendung, die Sie den einheitlichen Zugriff auf gewähren möchten, und klicken Sie auf das Häkchen in der unteren rechten Ecke. Aus der **delegierte Berechtigungen** Dropdown-Menü Wählen Sie die neue Berechtigung. </br>

![](./media/active-directory-application-proxy-native-client/delegate_native_app.png) </br></br>
4. Bearbeiten Sie den Code für systemeigene Anwendungen im Authentifizierungskontext der Active Directory-Authentifizierungsbibliothek (ADAL, Active Directory Authentication Library), sodass er Folgendes enthält:

        // Acquire Access Token from AAD for Proxy Application
        AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/<TenantId>");
        AuthenticationResult result = authContext.AcquireToken("< Frontend Url of Proxy App >",
                                                        "< Client Id of the Native app>",
                                                        new Uri("< Redirect Uri of the Native App>"),
                                                        PromptBehavior.Never);

        //Use the Access Token to access the Proxy Application
        HttpClient httpClient = new HttpClient();
        httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
        HttpResponseMessage response = await httpClient.GetAsync("< Proxy App API Url >");

Die Variablen müssen wie folgt ersetzt werden:


- **Mandanten-ID** befinden sich die GUID in die URL der Anwendung **Konfiguration** Seite unmittelbar nach "/ Directory /".
- **Front-End-URL** ist die Front-End-URL eingegeben haben, in der Proxy-Anwendung und befindet sich auf der **Konfiguration** der Proxy-app.
- **Client-Id** der systemeigenen app finden Sie auf der **konfigurieren** auf der Seite die systemeigene Anwendung.
- **Umleitungs-URI der systemeigenen app** finden Sie auf der **konfigurieren** auf der Seite die systemeigene Anwendung.

![](./media/active-directory-application-proxy-native-client/new_native_app.png)
</br> </br>Weitere Informationen zu den Fluss der systemeigenen Anwendung, finden Sie unter [systemeigene Anwendung zu Web-API-](active-directory-authentication-scenarios.md#native-application-to-web-api).






## Wie geht es weiter?
Der Anwendungsproxy bietet Ihnen noch viele weitere Möglichkeiten:


- [Veröffentlichen von Anwendungen mit Ihrem eigenen Domänennamen](active-directory-application-proxy-custom-domains.md)
- [Aktivieren der einmaligen Anmeldung](active-directory-application-proxy-sso-using-kcd.md)
- [Arbeiten mit Anwendungen, die Ansprüche unterstützen](active-directory-application-proxy-claims-aware-apps.md)
- [Aktivieren des bedingten Zugriffs](active-directory-application-proxy-conditional-access.md)


### Weitere Informationen zum Anwendungsproxy
- [Onlinehilfe anzeigen](active-directory-application-proxy-enable.md)
- [Blog zum Anwendungsproxy aufrufen](http://blogs.technet.com/b/applicationproxyblog/)
- [Sehen Sie sich unsere Videos auf Channel 9 an!](http://channel9.msdn.com/events/Ignite/2015/BRK3864)

## Zusätzliche Ressourcen
* [Als Unternehmen für Azure registrieren](sign-up-organization.md)
* [Azure-Identität](fundamentals-identity.md)


