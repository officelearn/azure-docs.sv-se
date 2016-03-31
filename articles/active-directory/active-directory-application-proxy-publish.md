<properties
    pageTitle="Veröffentlichen von Apps mit Azure AD-Anwendungsproxy | Microsoft Azure"
    description="Erläutert die Veröffentlichung lokaler Anwendungen mit dem Azure AD-Anwendungsproxy."
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
    ms.date="12/02/2015"
    ms.author="kgremban"/>


# Veröffentlichen von Anwendungen mit Azure AD-Anwendungsproxy

> [AZURE.NOTE] Anwendungsproxy ist ein Feature, ist nur verfügbar, wenn Sie auf die Premium oder Basic Edition von Azure Active Directory aktualisiert. Weitere Informationen finden Sie unter [Azure Active Directory-Editionen](active-directory-editions.md).

Nachdem Sie den Microsoft Azure AD-Anwendungsproxy aktiviert haben, können Sie Anwendungen veröffentlichen, um sie für Ihre Benutzer von außerhalb Ihres privaten Netzwerks zugänglich zu machen.

Dieser Artikel führt Sie durch die Schritte zum Veröffentlichen von Anwendungen, die in Ihrem lokalen Netzwerk ausgeführt werden und für die Sie sicheren Remotezugriff von außerhalb Ihres Netzwerks aktivieren möchten.

> [AZURE.NOTE] Um sicherzustellen, dass der Connector ordnungsgemäß ausgeführt wird, sollte die erste Anwendung, die Sie veröffentlichen eine beliebige Website innerhalb Ihres privaten Netzwerks, um sicherzustellen, dass Benutzer es über das Internet zugreifen können vor dem Veröffentlichen einer Anwendung zugegriffen werden.


## Veröffentlichen einer App mithilfe des Assistenten

1. Öffnen Sie einen Browser Ihrer Wahl, und wechseln Sie zum klassischen Azure-Portal.
2. Klicken Sie im linken Bereich des klassischen Azure-Portals auf die Registerkarte „Active Directory“.
3. Klicken Sie auf das Verzeichnis, in dem Sie den Anwendungsproxy aktiviert haben, und für das Sie eine Anwendung (z. B. Wingtip Toys) veröffentlichen möchten.
4. Klicken Sie auf die **Applikationen** Registerkarte, und klicken Sie dann auf die **Hinzufügen** am unteren Bildschirmrand
    ![Anwendung hinzufügen](./media/active-directory-application-proxy-publish/aad_appproxy_selectdirectory.png)
5. Was möchten Sie tun? Klicken Sie im Dialogfeld klicken Sie auf **Veröffentlichen einer Anwendung, die außerhalb Ihres Netzwerks zugegriffen werden**.
    ![Neue Anwendung, die von außerhalb Ihres Netzwerks zugegriffen werden kann](./media/active-directory-application-proxy-publish/aad_appproxy_addapp.png)

6. Befolgen Sie die Anweisungen auf dem Bildschirm, um die folgenden Informationen über Ihre Anwendung bereitzustellen:
    ![Anwendungseigenschaften](./media/active-directory-application-proxy-publish/aad_appproxy_appproperties.png)

**Einstellung** | **Details**
---|---
Externe URL | Dies ist die URL des Clouddiensts, die zum Zugriff auf die Anwendung von außerhalb Ihres privaten Netzwerks verwendet wird. Die URL wird basierend auf dem bereitgestellten Namen automatisch mit dem Suffix "msappproxy.net" generiert.
Präauthentifizierungsmethode | <p>Legen Sie den Typ der präauthentifizierungsmethode der Anwendung verwendet werden soll:</p><p>a. Microsoft Azure Active Directory (Microsoft Azure AD) – Wenn ein Benutzer versucht, eine Anwendung zuzugreifen, leitet der Anwendungsproxy den Benutzer anmelden mit Azure AD die wird der Benutzer authentifiziert, um sicherzustellen, dass der Benutzer die erforderlichen Berechtigungen für das Verzeichnis und die Anwendung.</p> <p>b. Passthrough: Es findet keine Präauthentifizierung statt.</p>
Externes URL-Protokoll | <p>Standardmäßig werden Anwendungen mithilfe des HTTPS-Protokolls veröffentlicht. Der Dienst leitet automatisch Benutzer um, die die URL mit „http“ eingeben.</p> <p>Zum Aktivieren von HTTP für eine interne Anwendung müssen Sie die präauthentifizierungsmethode auf Passthrough gesetzt, und dann kann die Externalurl-Protokoll von HTTPS in HTTP ändern werden. Beachten Sie, dass das Veröffentlichen von Anwendungen mithilfe von HTTP Sicherheitsprobleme für Ihre Anwendung und Ihre Benutzer hervorrufen kann.</p> <p>Sie können mithilfe der Standard-Suffix msappproxy.net, anstatt eine benutzerdefinierte Domäne einfügen. Weitere Informationen finden Sie unter [Arbeiten mit benutzerdefinierten Domänen](active-directory-application-proxy-custom-domains.md).</p>
Interne URL | <p>Dies ist die interne URL, die der Anwendungsproxyconnector verwendet, um intern auf die Anwendung zuzugreifen. Dies sollte die URL der veröffentlichten Anwendung sein, die von innerhalb Ihres privaten Netzwerks zum Zugriff auf die Anwendung verwendet wird. Dies ist eine gültige URL ohne Leerzeichen oder Symbole.</p> <p>Sie können einen bestimmten Pfad auf dem Back-End-Server für die Veröffentlichung angeben, während der Rest des Servers nicht veröffentlicht wird. So können Sie beispielsweise verschiedene Websites, die sich auf demselben SharePoint-Server befinden, mit unterschiedlichen Namen und Zugriffsregeln veröffentlichen.</p> <p>Der Pfad wird im Feld für die interne URL angegeben, und ist in der externen URL sichtbar. Der interne und externe Pfad müssen identisch sein.</p>

Um den Assistenten zu beenden, klicken Sie auf das Häkchen unten im Bildschirm. Die Anwendung ist jetzt in Azure AD definiert.


## Zuweisen von Benutzern und Gruppen zur Anwendung

1. Für präauthentifizierte Apps müssen Sie Benutzer und Gruppen zuweisen, die Zugriff auf die Anwendung haben. <p>Der Zugriff auf Passthrough-Apps ist für alle Benutzer verfügbar. Damit jedoch ein Benutzer die App in seiner Anwendungsliste sehen kann, müssen Sie die App diesem Benutzer zuweisen.
2. Nach dem Beenden des Assistenten für das Hinzufügen von Apps wird die Schnellstartseite für den Anwendungsproxy angezeigt. Klicken Sie zum Zuweisen von Benutzern auf **Zuweisen von Benutzern**.
    ![Anwendung Proxy quick Start-Bildschirm](./media/active-directory-application-proxy-publish/quickstart.png)
3. Wählen Sie alle Benutzer oder Gruppen, die zu dieser app und klicken Sie auf Zuweisen **Zuweisen**.

> [AZURE.NOTE] Für integrierte Windows-Authentifizierung-apps können Sie nur Benutzer und Gruppen, die synchronisiert werden zuweisen, aus Ihrem lokalen Active Directory. Benutzer, die sich über ein Microsoft-Konto anmelden, und Gäste können nicht für Apps zugewiesen werden, die mit dem Azure Active Directory-Anwendungsproxy veröffentlicht werden. Stellen Sie sicher, dass die zugewiesenen Benutzer sich mit den Anmeldeinformationen anmelden, die Teil der gleichen Domäne sind wie die Anwendung, die Sie veröffentlichen.


## Erweiterte Konfiguration

1. Auf der Seite „Konfigurieren“ können Sie veröffentlichte Apps ändern oder erweiterte Optionen konfigurieren, z. B. SSO für lokale Anwendungen.
    ![Erweiterte Konfiguration](./media/active-directory-application-proxy-publish/advancedconfig.png)

2. Wählen Sie die Anwendung, und klicken Sie auf **konfigurieren**. Folgende Optionen sind verfügbar:

**Einstellung** | **Details**
---|---
Name | Geben Sie einen beschreibenden Namen für Ihre Anwendung ein.
Externe URL | Dies ist die URL des Clouddiensts, die zum Zugriff auf die Anwendung von außerhalb Ihres privaten Netzwerks verwendet wird. Die URL wird basierend auf dem bereitgestellten Namen automatisch mit dem Suffix "msappproxy.net" generiert.
Präauthentifizierungsmethode | <p>Legen Sie den Typ der präauthentifizierungsmethode der Anwendung verwendet werden soll:</p><p>a. Microsoft Azure Active Directory (Microsoft Azure AD) – Wenn ein Benutzer versucht, eine Anwendung zuzugreifen, leitet der Anwendungsproxy den Benutzer anmelden mit Azure AD die wird der Benutzer authentifiziert, um sicherzustellen, dass der Benutzer die erforderlichen Berechtigungen für das Verzeichnis und die Anwendung.</p> <p>b. Passthrough: Es findet keine Präauthentifizierung statt.</p>
Externes URL-Protokoll | <p>Standardmäßig werden Anwendungen mithilfe des HTTPS-Protokolls veröffentlicht. Der Dienst leitet automatisch Benutzer um, die die URL mit „http“ eingeben.</p> <p>Zum Aktivieren von HTTP für eine interne Anwendung müssen Sie die präauthentifizierungsmethode auf Passthrough gesetzt, und dann kann die Externalurl-Protokoll von HTTPS in HTTP ändern werden. Beachten Sie, dass das Veröffentlichen von Anwendungen mithilfe von HTTP Sicherheitsprobleme für Ihre Anwendung und Ihre Benutzer hervorrufen kann.</p> <p>Sie können mithilfe der Standard-Suffix msappproxy.net, anstatt eine benutzerdefinierte Domäne einfügen. Weitere Informationen finden Sie unter [Arbeiten mit benutzerdefinierten Domänen](active-directory-application-proxy-custom-domains.md).</p>
Interne URL | <p>Dies ist die interne URL, die der Anwendungsproxyconnector verwendet, um intern auf die Anwendung zuzugreifen. Dies sollte die URL der veröffentlichten Anwendung sein, die von innerhalb Ihres privaten Netzwerks zum Zugriff auf die Anwendung verwendet wird. Dies ist eine gültige URL ohne Leerzeichen oder Symbole.</p> <p>Sie können einen bestimmten Pfad auf dem Back-End-Server für die Veröffentlichung angeben, während der Rest des Servers nicht veröffentlicht wird. So können Sie beispielsweise verschiedene Websites, die sich auf demselben SharePoint-Server befinden, mit unterschiedlichen Namen und Zugriffsregeln veröffentlichen.</p> <p>Der Pfad wird im Feld für die interne URL angegeben, und ist in der externen URL sichtbar. Der interne und externe Pfad müssen identisch sein.</p>
URL in Headern übersetzen | Für Anwendungen (z. B. einige SharePoint-Konfigurationen), die erfordern, dass die HTTP-Hostheader nicht übersetzt werden, legen Sie diese auf **Nr.**. Dadurch wird die Übersetzung sowohl für Anforderungs- als auch für Antwortheader deaktiviert.
Interne Authentifizierungsmethode | <p>Wenn Sie den Anwendungsproxy für die Präauthentifizierung verwenden, können Sie eine interne Authentifizierungsmethode festlegen, um Ihren Benutzern die einmalige Anmeldung (SSO) für diese Anwendung zu ermöglichen.</p> <p> Wählen Sie **integrierte Windows-Authentifizierung (IWA)** Wenn Ihre Anwendung IWA verwendet und Sie Kerberos eingeschränkte Delegierung (KCD) zum Aktivieren von SSO für diese Anwendung konfigurieren können. Anwendungen, die IWA verwenden, müssen mithilfe von KCD konfiguriert werden. Ansonsten kann der Anwendungsproxy diese Anwendungen nicht veröffentlichen.</p> <p>Wählen Sie **keine** wenn Ihre Anwendung IWA nicht verwendet.</p> <p>Weitere Informationen finden Sie unter [einmaliges Anmelden mit Anwendungsproxy](active-directory-application-proxy-sso-using-kcd.md).</p>
Interner Anwendungs-SPN | <p>Dies ist der Dienstprinzipalname (SPN) der internen Anwendung gemäß Konfiguration im lokalen Anwendungsproxy. Der SPN wird vom Anwendungsproxyconnector verwendet, um Kerberos-Token für die Anwendung mit der eingeschränkten Kerberos-Delegierung abzurufen.</p> <p>Weitere Informationen finden Sie unter [Aktivieren von einmaligem Anmelden auf](active-directory-application-proxy-sso-using-kcd.md).</p>

Nachdem Sie Anwendungen mit dem Azure Active Directory-Anwendungsproxy veröffentlicht haben, werden sie in der Liste der Anwendungen in Azure AD aufgeführt, und Sie können sie dort verwalten.

Wenn Sie Anwendungsproxydienste deaktivieren, nachdem Sie Anwendungen veröffentlicht haben, werden die Anwendungen zwar nicht gelöscht, aber der Zugriff darauf von außerhalb Ihres privaten Netzwerks ist nicht mehr möglich.

Um eine Anwendung anzeigen und den Zugriff darauf sicherzustellen, doppelklicken Sie auf den Namen der Anwendung. Wenn der Anwendungsproxydienst deaktiviert und die Anwendung nicht verfügbar ist, wird am oberen Bildschirmrand eine Warnmeldung angezeigt.

Um eine Anwendung zu löschen, wählen Sie eine Anwendung in der Liste, und klicken Sie dann auf **Löschen**.

## Weitere Informationen
Der Anwendungsproxy bietet Ihnen noch viele weitere Möglichkeiten:

- [Aktivieren des Anwendungsproxys](active-directory-application-proxy-enable.md)
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

* [Informationen zur eingeschränkten Kerberos-Delegierung](http://technet.microsoft.com/library/cc995228.aspx)


