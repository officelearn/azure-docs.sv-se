<properties
   pageTitle="Integration von Azure Active Directory mit erste Schritte |  Microsoft Azure"
   description="In diesem Artikel sind die ersten Schritte für die Integration von lokalen Anwendungen und Cloudanwendungen in Azure Active Directory (AD)  aufgeführt."
   services="active-directory"
   documentationCenter=""
   authors="ihenkel"
   manager="stevenpo"
   editor=""/>

   <tags
      ms.service="active-directory"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="identity"
      ms.date="10/16/2015"
      ms.author="inhenk"/>

# Erste Schritte zur Integration von Anwendungen in Azure Active Directory
## Übersicht
Dieses Thema bietet Ihnen eine Roadmap für die Integration von Anwendungen in Azure Active Directory (AD). Jeder der folgenden Abschnitte enthält eine kurze Zusammenfassung eines ausführlicheren Themas, damit Sie feststellen können, welche Teile dieses Erste-Schritte-Leitfadens für Sie relevant sind.  Folgen Sie den Links, um eingehendere Informationen zu den einzelnen Themen zu erhalten.

## Voraussetzungen – Bestandsaufnahme
Vor der Integration von Anwendungen in Azure AD sollten Sie unbedingt den aktuellen Stand ermitteln und festlegen, was Sie erreichen möchten.  Die folgenden Fragen helfen Ihnen bei Ihren Überlegungen zu Ihrem Azure AD-Projekt zur Anwendungsintegration.

### Anwendungsbestand
- Wo befinden sich all Ihre Anwendungen? Wer ist der Eigentümer?
- Welche Art der Authentifizierung ist für Ihre Anwendungen erforderlich?
- Wer benötigt Zugriff auf welche Anwendungen?
- Möchten Sie eine neue Anwendung bereitstellen?
  - Wird diese innerbetrieblich erstellt und auf einer Azure Compute-Instanz bereitgestellt?
  - Möchten Sie eine Anwendung verwenden, die im Azure-Anwendungskatalog verfügbar ist?

### Benutzer- und Gruppenbestand
- Wo befinden sich Ihre Benutzerkonten?
 - Lokales Active Directory
 - Azure AD
 - In einer separaten Anwendungsdatenbank, die Sie besitzen
 - In ungenehmigten Anwendungen
 - Alle oben genannten Möglichkeiten
- Welche Berechtigungen und Rollenzuweisungen gelten derzeit für die einzelnen Benutzer? Möchten Sie den Zugriff überprüfen, oder sind Sie sicher, dass die aktuellen Zugriffs- und Rollenzuweisungen angemessen sind?
- Sind in Ihrem lokalen Active Directory bereits Gruppen eingerichtet?
 - Wie sind Ihre Gruppen organisiert?
 - Wer sind die Mitglieder der Gruppen?
 - Welche Berechtigungen/Rollenzuweisungen gelten derzeit für die Gruppen?
- Müssen Sie vor der Integration Benutzer-/Gruppendatenbanken bereinigen?  (Dies ist eine ziemlich wichtige Frage. Bei einer fehlerhaften Ausgangslage lässt sich kein optimales Ergebnis erzielen – „Garbage In, Garbage Out“.)

### Bestand der Zugriffsverwaltung
- Wie verwalten Sie derzeit den Benutzerzugriff auf Anwendungen? Muss daran etwas geändert werden?  Haben Sie überlegt, andere Methoden zum Verwalten des Zugriffs, z. B. mit [RBAC](role-based-access-control-configure.md) z. B.?
- Wer muss worauf zugreifen?

Vielleicht können Sie nicht alle Fragen im Voraus beantworten, aber das ist kein Problem.  Dieser Leitfaden hilft Ihnen, einige dieser Fragen zu beantworten und einige fundierte Entscheidungen zu treffen.

## Voraussetzungen
- Ein Azure-Abonnement und ein Azure Active Directory-Verzeichnis.  Wenn Sie noch kein Azure-Abonnement besitzen, können Sie Azure für 30 Tage kostenlos testen. [Probieren Sie es aus!](https://azure.microsoft.com/trial/get-started-active-directory/)

## Anwendungsintegration in Azure AD
### Suchen nach nicht genehmigten Cloudanwendungen per Cloud App Discovery
Wie oben erwähnt sind möglicherweise Anwendungen im Einsatz, die bis jetzt von Ihrer Organisation noch nicht verwaltet wurden.  Im Rahmen der Bestandsaufnahme können Sie nicht genehmigte Cloudanwendungen finden. Anweisungen finden Sie unter
[Suchen nach nicht genehmigte Cloud-Anwendung mit Cloud App Discovery](active-directory-cloudappdiscovery-whatis.md).

### Authentifizierungstypen
Für jede Ihrer Anwendungen gelten möglicherweise unterschiedliche Authentifizierungsanforderungen. Bei Azure AD können Signaturzertifikate mit Anwendungen verwendet werden, die die Protokolle SAML 2.0, WS-Verbund oder OpenID Connect sowie das einmalige Anmelden per Kennwort verwenden. Weitere Informationen zur Anwendung Authentifizierungstypen für die Verwendung mit Azure AD finden Sie unter [Verwalten von Zertifikaten für Verbundbenutzer einmaliges Anmelden in Azure Active Directory](active-directory-sso-certs.md) und [Kennwort einmaliges Anmelden auf der Grundlage](active-directory-appssoaccess-whatis.md).

### Aktivieren von SSO mit Azure AD-App-Proxy
Mit dem Microsoft Azure AD-Anwendungsproxy können Sie sicheren Zugriff von jedem Ort und mit jedem Gerät auf Anwendungen bereitstellen, die sich in Ihrem privaten Netzwerk befinden. Nachdem Sie einen Anwendungsproxy-Connector in Ihrer Umgebung installiert haben, können Sie ihn mit Azure AD leicht konfigurieren. Finden Sie unter [Aktivieren von SSO mit Azure AD-Anwendungsproxy](active-directory-appssoaccess-enable-hybrid-access.md) und [veröffentlichen neue Anwendungen mit Azure AD-Anwendungsproxy](active-directory-application-proxy-configure.md).

### Integrieren von Anwendungen in Azure Active Directory
In den folgenden Artikeln werden die verschiedenen Methoden zur Integration von Anwendungen in Azure AD erläutert und Anleitungen bereitgestellt.

- [Bestimmen des zu verwendenden Active Directory-Verzeichnisses](active-directory-administer.md)
- [Integration in vorhandene Anwendungen](active-directory-sso-integrate-existing-apps.md)
- [Veröffentlichen neuer Anwendungen mit Azure AD-App-Proxy](active-directory-application-proxy-configure.md)
- [Verwenden von Anwendungen im Azure-Anwendungskatalog](active-directory-appssoaccess-whatis.md/#get-started-with-the-azure-ad-application-gallery.md)
- [Liste der Tutorials zur Integration von SaaS-Anwendungen](active-directory-saas-tutorial-list.md)

## Verwalten des Zugriffs auf Anwendungen
Die folgenden Artikel beschreiben Methoden, die Sie den Zugriff auf Anwendungen verwalten können, sobald sie in Azure AD mithilfe von Azure AD-Connectors und Azure AD im Azure-Portal integriert wurden.

- [Verwalten des Zugriffs auf Apps mit Azure AD](active-directory-managing-access-to-apps.md)
- [Automatisieren mit Azure AD-Connectors](active-directory-saas-app-provisioning.md)
- [Zuweisen von Benutzern zu einer Anwendung](active-directory-applications-guiding-developers-assigning-users.md) mit Azure-Portal.
- [Zuweisen von Gruppen zu einer Anwendung](active-directory-applications-guiding-developers-assigning-groups.md) mit Azure-Portal.
- [Gemeinsames Verwenden von Konten](active-directory-sharing-accounts.md)

## Integrieren benutzerdefinierter Anwendungen
Wenn Sie schreiben eine neue Anwendung zur Unterstützung der Entwickler Dank der Leistungsfähigkeit von Azure AD finden Sie unter [Guiding Entwickler](active-directory-applications-guiding-developers-for-lob-applications.md).

Wenn Sie Ihre benutzerdefinierte Anwendung im Katalog der Azure-Anwendung hinzufügen möchten, finden Sie unter ["Bring eine eigene app" mit Azure AD Self-Service-SAML-Konfiguration](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx).


