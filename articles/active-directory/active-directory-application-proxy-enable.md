<properties
    pageTitle="Aktivieren des Azure AD-Anwendungsproxys | Microsoft Azure"
    description="Erläutert, wie Sie den Azure AD-Anwendungsproxy betriebsbereit machen."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="StevenPo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/02/2015"
    ms.author="kgremban"/>

# Aktivieren des Azure AD-Anwendungsproxys
> [AZURE.NOTE] Anwendungsproxy ist ein Feature, ist nur verfügbar, wenn Sie auf die Premium oder Basic Edition von Azure Active Directory aktualisiert. Weitere Informationen finden Sie unter [Azure Active Directory-Editionen](active-directory-editions.md).

Der Microsoft Azure Active Directory-Anwendungsproxy ermöglicht es Ihnen, Anwendungen wie z. B. SharePoint-Websites, Outlook Web Access und IIS-basierte Apps innerhalb Ihres privaten Netzwerks zu veröffentlichen, und er stellt für Benutzer außerhalb Ihres Netzwerks einen sicheren Zugriff auf diese Apps bereit. Mitarbeiter können sich auf ihren eigenen Geräten von daheim aus bei Ihren Apps anmelden und sich über diesen cloudbasierten Proxy authentifizieren.

Um den Anwendungsproxy nutzen zu können, müssen Sie einen als Connector bezeichneten schlanken Windows-Dienst in Ihrem Netzwerk installieren. Der Connector verwaltet eine Verbindung für ausgehenden Datenverkehr zwischen dem Netzwerk und dem Proxy-Dienst. Wenn Benutzer auf eine veröffentlichte Anwendung zugreifen, verwendet der Proxy diese Verbindung, um den Zugriff auf die Anwendung bereitzustellen.

Dieser Artikel erläutert Schritt für Schritt, wie Sie den Microsoft Azure AD-Anwendungsproxy für Ihr Cloudverzeichnis in Azure AD aktivieren, wie Sie den Anwendungsproxyconnector in Ihrem privaten Netzwerk installieren und wie Sie den Connector bei Ihrem Microsoft Azure AD-Mandantenabonnement registrieren.

##Voraussetzungen für den Anwendungsproxy
Bevor Sie die Anwendungsproxydienste aktivieren und verwenden können, benötigen Sie Folgendes:

- Microsoft Azure AD [Basic- oder Premium-Abonnement](active-directory-editions.md) und Azure AD-Verzeichnis für die Sie als globaler Administrator angemeldet sind.
- Einen Server, auf dem Windows Server 2012 R2 bzw. Windows 8.1 oder höher installiert ist, auf dem Sie den Anwendungsproxyconnector installieren können. Der Server muss in der Lage sein, HTTPS-Anforderungen an die Anwendungsproxydienste in der Cloud zu senden, und er muss über eine HTTPS-Verbindung mit den Anwendungen verfügen, die Sie veröffentlichen möchten.
- Wenn im Pfad eine Firewall platziert ist, vergewissern Sie sich, dass die Firewall HTTPS-Anforderungen (TCP) zulässt, die vom Connector für den Anwendungsproxy stammen. Der Connector verwendet diese Ports zusammen mit untergeordneten Domänen, die Teil der Domäne auf hoher Ebene sind: msappproxy.net. Öffnen Sie unbedingt **alle** die folgenden ports für **ausgehende** Datenverkehr:

Portnummer | Beschreibung
--- | ---
80 | Ermöglicht ausgehenden HTTP-Verkehr für die Sicherheitsüberprüfung.
443 | Ermöglicht die Benutzerauthentifizierung für Azure AD (nur für den Connectorregistrierungsprozess erforderlich).
10100 - 10120 | Ermöglicht das Zurücksenden von LOB HTTP-Antworten an den Proxy. 
9352, 5671 | Ermöglicht eine Kommunikation zwischen Connector und Azure-Dienst für eingehende Anforderungen.
9350 | Optional; verbessert die Leistung für eingehende Anforderungen.
8080 | Aktiviert die Bootstrapsequenz des Connectors sowie eine automatische Aktualisierung des Connectors.
9090 | Ermöglicht die Connectorregistrierung (nur für den Connectorregistrierungsprozess erforderlich).
9091 | Ermöglicht die automatische Erneuerung des Vertrauenszertifikats für den Connector.

Wenn Ihre Firewall Datenverkehr gemäß Ursprungsbenutzern erzwingt, öffnen Sie diese Ports für den Datenverkehr aus Windows-Diensten, die als Netzwerkdienst ausgeführt werden. Stellen Sie außerdem sicher, dass Port 8080 für "NT Authority\System" aktiviert ist.


##Schritt 1: Aktivieren des Anwendungsproxys in Azure AD
1. Melden Sie sich als Administrator beim klassischen Azure-Portal an.
2. Gehen Sie zu Active Directory, und wählen Sie das Verzeichnis aus, in dem Sie den Anwendungsproxy aktivieren möchten.
3. Klicken Sie auf **konfigurieren**, einen Bildlauf nach unten zu Anwendungsproxy und schalten Sie Anwendungsproxydienste für dieses Verzeichnis aktivieren **aktiviert**.

    ![Aktivieren des Anwendungsproxys](./media/active-directory-application-proxy-enable/app_proxy_enable.png) <p>
4. Klicken Sie auf **jetzt** am unteren Bildschirmrand. Damit gelangen Sie zur Downloadseite. Lesen und akzeptieren Sie die Lizenzbedingungen aus, und klicken Sie auf **herunterladen** zum Speichern der Windows Installer-Datei (.exe) für den Anwendungsproxy-Connector.

##Schritt 2: Installieren und Registrieren des Connectors
1. Führen Sie `AADApplicationProxyConnectorInstaller.exe` auf dem vorbereiteten Server aus (siehe oben unter „Voraussetzungen für den Anwendungsproxy“).
2. Befolgen Sie die Anweisungen des Assistenten für die Installation.
3. Während der Installation werden Sie aufgefordert, den Connector beim Anwendungsproxy Ihres Azure AD-Mandanten zu registrieren.
<p>- Geben Sie Ihre Azure AD globaler Administrator-Anmeldeinformationen.
<p>- Stellen Sie sicher, dass der Administrator, der den Connector registriert ist, im gleichen Verzeichnis, in dem Sie den Anwendungsproxy, z. B. aktiviert, wenn die mandantendomäne "contoso.com" lautet, der Administrator admin@contoso.com oder mit einem anderen Aliasnamen in dieser Domäne werden soll. Außerdem ist ein globaler Administrator des Azure AD-Mandanten erforderlich. Ihr globaler Administratorenmandant kann von Ihren Microsoft Azure-Anmeldeinformationen abweichen.
<p>- Wenn die verstärkte Sicherheitskonfiguration für IE festgelegt ist **auf** auf dem Server, in dem Sie die Azure AD-Connector installieren, der Registrierungsbildschirm möglicherweise blockiert. Befolgen Sie in diesem Fall die Anweisungen in der Fehlermeldung, um den Zugriff zuzulassen. Stellen Sie sicher, dass die erweiterte Sicherheit von Internet Explorer deaktiviert ist.
<p>- Wenn der Connector-Registrierung nicht erfolgreich ist, finden Sie unter [Troubleshoot Application Proxy](active-directory-application-proxy-troubleshoot.md).

4. Nach Abschluss der Installation werden zwei neue Dienste auf dem Server hinzugefügt, wie unten dargestellt. Diese sind der Connectordienst, der Konnektivität ermöglicht, und ein automatischer Updatedienst, der in regelmäßigen Abständen prüft, ob neue Versionen des Connectors verfügbar sind, und den Connector bei Bedarf aktualisiert. Klicken Sie im Installationsfenster zum Abschließen der installation
    ![Anwendungsproxy-Connectordienst](./media/active-directory-application-proxy-enable/app_proxy_services.png) <p>
5. Sie sind nun bereit für den Schritt "Veröffentlichen von Anwendungen mit einem Anwendungsproxy".

Wenn hohe Verfügbarkeit gewünscht wird, müssen Sie mindestens einen weiteren Connector bereitstellen. Wiederholen Sie zum Bereitstellen eines weiteren Connectors die oben beschriebenen Schritte 2 und 3. Jeder Connector muss separat registriert werden.

Wenn Sie den Connector deinstallieren möchten, deinstallieren Sie sowohl den Connector- als auch den Updatedienst, und stellen Sie anschließend sicher, dass der Computer neu gestartet wird, um den Dienst vollständig zu entfernen.


## Weitere Informationen
Der Anwendungsproxy bietet Ihnen noch viele weitere Möglichkeiten:

- [Veröffentlichen von Anwendungen mit dem Anwendungsproxy](active-directory-application-proxy-publish.md)
- [Veröffentlichen von Anwendungen mit Ihrem eigenen Domänennamen](active-directory-application-proxy-custom-domains.md)
- [Aktivieren der einmaligen Anmeldung](active-directory-application-proxy-sso-using-kcd.md)
- [Aktivieren des bedingten Zugriffs](active-directory-application-proxy-conditional-access.md)
- [Arbeiten mit Anwendungen, die Ansprüche unterstützen](active-directory-application-proxy-claims-aware-apps.md)
- [Beheben Sie Probleme, mit dem Anwendungsproxy aufgetreten ist](active-directory-application-proxy-troubleshoot.md)

## Weitere Informationen zum Anwendungsproxy
- [Onlinehilfe anzeigen](active-directory-application-proxy-enable.md)
- [Blog zum Anwendungsproxy aufrufen](http://blogs.technet.com/b/applicationproxyblog/)
- [Sehen Sie sich unsere Videos auf Channel 9 an!](http://channel9.msdn.com/events/Ignite/2015/BRK3864)

## Zusätzliche Ressourcen
* [Als Unternehmen für Azure registrieren](sign-up-organization.md)
* [Azure-Identität](fundamentals-identity.md)
* [Veröffentlichen von Anwendungen mit dem Anwendungsproxy](active-directory-application-proxy-publish.md)


