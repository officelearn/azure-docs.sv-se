<properties
    pageTitle="Installieren des Azure AD-Anwendungsproxyconnectors im Hintergrund | Microsoft Azure"
    description="Erläutert das Durchführen einer Installation des Azure AD-Anwendungsproxyconnectors im Hintergrund, um sicheren Remotezugriff auf lokale Apps zu gewähren."
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
    ms.date="10/19/2015"
    ms.author="kgremban"/>

# Installieren des Azure AD-Anwendungsproxyconnectors im Hintergrund

Sie möchten ein Installationsskript an mehrere Windows-Server senden können oder an Windows-Server, auf denen keine Benutzeroberfläche aktiviert ist. In diesem Thema wird das Erstellen eines Windows PowerShell-Skripts erläutert, das eine unbeaufsichtigte Installation aktiviert und den Azure AD-Anwendungsproxyconnector installiert und registriert.

## Aktivieren des Zugriffs
Um den Anwendungsproxy nutzen zu können, müssen Sie einen als Connector bezeichneten schlanken Windows Server-Dienst in Ihrem Netzwerk installieren. Für das Funktionieren des Anwendungsproxyconnectors muss dieser im Azure AD-Verzeichnis durch einen globalen Administrator mit Kennwort registriert werden. Dieses wird normalerweise während der Installation des Connectors in einem Popupdialogfeld eingegeben. Stattdessen können Sie mit Windows PowerShell ein Anmeldeinformationsobjekt erstellen, um die Registrierungsinformationen einzugeben. Sie können jedoch auch ein eigenes Token erstellen, und dieses zur Eingabe der Registrierungsinformationen verwenden.

## Schritt 1: Installieren des Connectors ohne Registrierung


Installieren Sie die Connector-MSIs wie folgt, ohne den Connector zu registrieren:


1. Öffnen Sie eine Eingabeaufforderung.
2. Führen Sie den folgenden Befehl aus, in dem "/q" für die Installation im Hintergrund steht – bei der Installation werden Sie nicht aufgefordert, den Endbenutzer-Lizenzvertrag zu akzeptieren.

        AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q

## Schritt 2: Registrieren des Connectors in Azure Active Directory
Dies lässt sich mit einer folgenden Methoden bewirken:


- Registrieren des Connectors mit einem Windows PowerShell-Anmeldeinformationsobjekts
- Registrieren des Connectors mithilfe eines offline erstellten Tokens

### Registrieren des Connectors mit einem Windows PowerShell-Anmeldeinformationsobjekts


1. Erstellen Sie das Windows PowerShell-Anmeldeinformationsobjekt durch Ausführen des folgenden Befehls, wobei „Benutzername“ und „Kennwort“ durch den Benutzernamen und das Kennwort für das betreffende Verzeichnis ersetzt werden muss:

        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword

2. Wechseln Sie zu **AAD App-Anwendungsproxy-Connector C:\Program Files\Microsoft** und führen Sie das Skript mithilfe von PowerShell-Anmeldeinformationen Objekt erstellen: $cred wird, in dem der Name des PowerShell Anmeldeinformationen-Objekts, das Sie erstellt haben:

        RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred


### Registrieren des Connectors mithilfe eines offline erstellten Tokens

1. Erstellen Sie mithilfe der AuthenticationContext-Klasse ein Offlinetoken, indem Sie die Werte in dem Codeausschnitt verwenden:


        using System;
        using System.Diagnostics;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

        class Program
        {
        #region constants
        /// <summary>
        /// The AAD authentication endpoint uri
        /// </summary>
        static readonly Uri AadAuthenticationEndpoint = new Uri("https://login.windows.net/common/oauth2/token?api-version=1.0");

        /// <summary>
        /// The application ID of the connector in AAD
        /// </summary>
        static readonly string ConnectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489";

        /// <summary>
        /// The reply address of the connector application in AAD
        /// </summary>
        static readonly Uri ConnectorRedirectAddress = new Uri("urn:ietf:wg:oauth:2.0:oob");

        /// <summary>
        /// The AppIdUri of the registration service in AAD
        /// </summary>
        static readonly Uri RegistrationServiceAppIdUri = new Uri("https://proxy.cloudwebappproxy.net/registerapp");

        #endregion

        #region private members
        private string token;
        private string tenantID;
        #endregion

        public void GetAuthenticationToken()
        {
            AuthenticationContext authContext = new AuthenticationContext(AadAuthenticationEndpoint.AbsoluteUri);

            AuthenticationResult authResult = authContext.AcquireToken(RegistrationServiceAppIdUri.AbsoluteUri,
                ConnectorAppId,
                ConnectorRedirectAddress,
                PromptBehavior.Always);

            if (authResult == null || string.IsNullOrEmpty(authResult.AccessToken) || string.IsNullOrEmpty(authResult.TenantId))
            {
                Trace.TraceError("Authentication result, token or tenant id returned are null");
                throw new InvalidOperationException("Authentication result, token or tenant id returned are null");
            }

            token = authResult.AccessToken;
            tenantID = authResult.TenantId;
        }





2. Nachdem Sie das Token mit dem Token SecureString erstellen haben: <br>
`$SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force`
3. Führen Sie den folgenden Windows PowerShell-Befehl, wobei SecureToken ist der Name des Tokens, die Sie soeben erstellt haben und Mandanten-ID des Mandanten-GUID: <br>
`RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID>`



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


