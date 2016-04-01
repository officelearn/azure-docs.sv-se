<properties
    pageTitle="Bedingter Zugriff für Anwendungen, die mit dem Azure AD-Anwendungsproxy veröffentlicht wurden"
    description="Stellt Informationen dazu bereit, wie der bedingte Zugriff für veröffentlichte Anwendungen eingerichtet wird, auf die mit dem Azure AD-Anwendungsproxy remote zugegriffen wird. "
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
    ms.date="10/12/2015"
    ms.author="kgremban"/>

# Arbeiten mit bedingtem Zugriff
> [AZURE.NOTE] Anwendungsproxy ist ein Feature, ist nur verfügbar, wenn Sie auf die Premium oder Basic Edition von Azure Active Directory aktualisiert. Weitere Informationen finden Sie unter [Azure Active Directory-Editionen](active-directory-editions.md).

Sie können ab sofort Zugriffsregeln aktivieren, mit denen Benutzern und Gruppen bedingter Zugriff auf Anwendungen gewährt wird, die mit dem Anwendungsproxy veröffentlicht werden. Hierdurch wird Folgendes ermöglicht:

- Erfordern einer Multi-Factor-Authentication pro Anwendung
- Erfordern einer Multi-Factor Authentication nur dann, wenn die Benutzer nicht am Arbeitsplatz sind
- Blockieren des Zugriffs der Benutzer auf Anwendungen, wenn die Benutzer nicht am Arbeitsplatz sind

Diese Regeln können auf alle Benutzer und Gruppen oder lediglich auf bestimmte Benutzer und Gruppen angewendet werden.
Standardmäßig gilt die Regel für alle Benutzer, die Zugriff auf die Anwendung haben. Die Regel kann jedoch auch auf Benutzer beschränkt werden, die Mitglied einer angegebenen Sicherheitsgruppe sind.  

Zugriffsregeln werden ausgewertet, wenn ein Benutzer auf eine Verbundanwendung zugreift, die OAuth 2.0, OpenID Connect, SAML oder WS-Federation verwendet. Darüber hinaus werden Zugriffsregeln ausgewertet, wenn von OAuth 2.0 und OpenID Connect ein Aktualisierungstoken zum Abrufen eines Zugriffstokens verwendet wird.

## Voraussetzungen für den bedingten Zugriff

- Azure Active Directory Premium-Abonnement
- Ein Verbundmandant oder verwalteter Azure Active Directory-Mandant
- Verbundmandanten erfordern die Aktivierung von Multi-Factor Authentication (MFA) 

![](./media/active-directory-application-proxy-conditional-access/application-proxy-conditional-access.png)

## Konfigurieren von Multi-Factor Authentication pro Anwendung
1. Melden Sie sich als Administrator beim Azure-Verwaltungsportal an.
2. Gehen Sie zu Active Directory, und wählen Sie das Verzeichnis aus, in dem Sie den Anwendungsproxy aktivieren möchten.
3. Klicken Sie auf **Anwendungen** und führen Sie einen Bildlauf nach unten, um die **Zugriffsregeln** Abschnitt. Der Abschnitt mit den Zugriffsregeln wird nur für Anwendungen angezeigt, die mit dem Anwendungsproxy veröffentlicht wurden und die Verbundauthentifizierung nutzen.
4. Aktivieren Sie dazu die Regel **Zugriffsregeln aktivieren** zu **auf**.
5. Geben Sie die Benutzer und Gruppen an, auf die die Regeln angewendet werden sollen. Verwenden der **Gruppe hinzufügen** klicken, um eine oder mehrere Gruppen auszuwählen, auf die die Regel angewendet wird. Dieses Dialogfeld kann auch zum Entfernen ausgewählter Gruppen verwendet werden.  Bei Anwendung der ausgewählten Regeln auf Gruppen werden die Zugriffsregeln nur für Benutzer erzwungen, die einer der angegebenen Sicherheitsgruppen angehören. <br> Um die Regel explizit Sicherheitsgruppen ausschließen, überprüfen Sie **außer**  und eine oder mehrere Gruppen angeben. Benutzer, die Mitglieder einer der in der Ausnahmeliste aufgeführten Gruppen sind, müssen keine mehrstufige Authentifizierung durchführen. <br>Wenn ein Benutzer unter Verwendung der Multi-Factor Authentication-Funktion auf Benutzerebene konfiguriert wurde, hat diese Einstellung Vorrang vor den Multi-Factor Authentication-Regeln auf Anwendungsebene. Dies bedeutet, dass Benutzer, die auf Benutzerebene für die mehrstufige Authentifizierung konfiguriert wurden, auch dann eine mehrstufige Authentifizierung durchlaufen müssen, wenn sie auf Anwendungsebene von den Multi-Factor Authentication-Regeln ausgenommen wurden. [Erfahren Sie mehr über Multi-Factor Authentication und benutzerspezifische Einstellungen](../multi-factor-authentication/multi-factor-authentication.md).
6. Wählen Sie die Zugriffsregel, die Sie festlegen möchten:
    - **Mehrstufige Authentifizierung verlangen**: Benutzer, die für die Zugriffsregeln gelten werden erforderlich sein, um vollständige mehrstufige Authentifizierung vor dem Zugriff auf die Anwendung, für die die Regel gilt.
    - **Mehrstufige Authentifizierung nicht am Arbeitsplatz**: Benutzer versuchen, Zugriff auf die Anwendung von einer vertrauenswürdigen IP-Adresse ist nicht erforderlich, um mehrstufige Authentifizierung durchzuführen. Die Bereiche für vertrauenswürdige IP-Adressen können auf den Einstellungsseiten für die mehrstufige Authentifizierung konfiguriert werden.
    - **Blockieren des Zugriffs nicht am Arbeitsplatz**: Benutzer versuchen, Zugriff auf die Anwendung von außerhalb des Unternehmensnetzwerks werden nicht in der Lage, auf die Anwendung zugreifen.


## Konfigurieren von MFA für Verbunddienste
Für Verbundmandanten kann die mehrstufige Authentifizierung von Azure Active Directory oder den lokalen AD FS-Server durchgeführt werden. Standardmäßig wird MFA auf allen Seiten durchgeführt, die von Azure Active Directory gehostet werden. Führen Sie zur lokalen Konfiguration von MFA Windows PowerShell aus, und verwenden Sie die –SupportsMFA-Eigenschaft zum Festlegen des Azure AD-Moduls.
Das folgende Beispiel zeigt, wie lokale MFA mithilfe von ermöglichen die [Set-MsolDomainFederationSettings-Cmdlet](https://msdn.microsoft.com/library/azure/dn194088.aspx) des Mandanten "contoso.com": `Set-MsolDomainFederationSettings -DomainName contoso.com -SupportsMFA $true `
Zusätzlich zum Festlegen dieses Kennzeichens muss die AD FS-Instanz des Verbundmandanten für die Ausführung der mehrstufigen Authentifizierung konfiguriert werden. Führen Sie die Schritte zum [Bereitstellen von Microsoft Azure Multi-Factor Authentication lokalen](../multi-factor-authentication/multi-factor-authentication-get-started-server.md).
## Weitere Informationen
Der Anwendungsproxy bietet Ihnen noch viele weitere Möglichkeiten:

- [Veröffentlichen von Anwendungen mit dem Anwendungsproxy](active-directory-application-proxy-publish.md)
- [Veröffentlichen von Anwendungen mit Ihrem eigenen Domänennamen](active-directory-application-proxy-custom-domains.md)
- [Aktivieren der einmaligen Anmeldung](active-directory-application-proxy-sso-using-kcd.md)
- [Arbeiten mit Anwendungen, die Ansprüche unterstützen](active-directory-application-proxy-claims-aware-apps.md)
- [Beheben Sie Probleme, mit dem Anwendungsproxy aufgetreten ist](active-directory-application-proxy-troubleshoot.md)

## Weitere Informationen zum Anwendungsproxy
- [Onlinehilfe anzeigen](active-directory-application-proxy-enable.md)
- [Blog zum Anwendungsproxy aufrufen](http://blogs.technet.com/b/applicationproxyblog/)
- [Sehen Sie sich unsere Videos auf Channel 9 an!](http://channel9.msdn.com/events/Ignite/2015/BRK3864)

## Zusätzliche Ressourcen

* [Anmelden bei Azure als Organisation](sign-up-organization.md)
* [Azure-Identität](fundamentals-identity.md)


