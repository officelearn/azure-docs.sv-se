<properties 
    pageTitle="Funktionsweise: Azure AD-Kennwortverwaltung | Microsoft Azure" 
    description="Lernen Sie die verschiedenen Komponenten der Azure AD-Kennwortverwaltung kennen und erfahren Sie, wo Benutzer sich registrieren, ihre Kennwörter zurücksetzen und ändern, wo Administratoren die Verwaltung lokaler Active Directory-Kennwörter konfigurieren, aktivieren und Berichte zur Kennwortverwaltung anzeigen können." 
    services="active-directory" 
    documentationCenter="" 
    authors="asteen" 
    manager="kbrint" 
    editor="billmath"/>

<tags 
    ms.service="active-directory" 
    ms.workload="identity" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/16/2015" 
    ms.author="asteen"/>

# Funktionsweise der Kennwortverwaltung

Die Kennwortverwaltung in Azure Active Directory umfasst verschiedene logische Komponenten, nachfolgend beschrieben werden.  Klicken Sie auf jeden Link, um mehr über die jeweilige Komponente zu erfahren.

- [**Kennwort-Konfiguration Verwaltungsportal**](#password-management-configuration-portal) – Administratoren können verschiedene Aspekte der kennwortverwaltung ihrer Mandanten werden durch Navigieren zur Registerkarte Konfigurieren ihres Verzeichnisses steuern die [Azure-Verwaltungsportal](https://manage.windowsazure.com).
- [**Benutzerregistrierungsportal**](#user-registration-portal) – Benutzer können für die kennwortzurücksetzung, die über dieses Webportal registrieren.
- [**Benutzer-Kennwortwiederherstellungsportals**](#user-password-reset-portal) – Benutzer können ihre eigenen Kennwörter über eine Reihe von verschiedenen Problemen in Übereinstimmung mit den vom Administrator gesteuerte kennwortzurücksetzungsrichtlinie zurücksetzen
- [**Kennwort ändern Benutzerportal**](#user-password-change-portal) – Benutzer können ihre eigenen Kennwörter jederzeit ändern, indem Sie ihr altes Kennwort eingeben und dann ein neues Kennwort mit diesem Webportal
- [**Kennwortverwaltungsberichte**](#password-management-reports) – Administratoren können anzeigen und Analysieren von Aktivitäten zu kennwortzurücksetzung und Registrierung in ihrem Mandanten durch Navigieren zum Abschnitt "Aktivitätsberichte" der Registerkarte "Berichte" ihres Verzeichnisses die [Azure-Verwaltungsportal](https://manage.windowsazure.com)
- [**Komponente zur Kennwortrückschreibung von Azure AD Connect**](#password-writeback-component-of-azure-ad-connect) – Administratoren können die Funktion zur Kennwortrückschreibung aktivieren, bei der Installation von Azure AD Connect für die Verwaltung von verbundkennwörtern oder synchronisierten Benutzerkennwörtern aus der Cloud.

## Portal für die Konfiguration der Kennwortverwaltung
Sie können konfigurieren, Password Management-Richtlinien für ein bestimmtes Verzeichnis mit der [Azure-Verwaltungsportal](https://manage.windowsazure.com) auf der **Richtlinie zum Zurücksetzen des** Abschnitt in des Verzeichnis **konfigurieren** Registerkarte.  Auf dieser Konfigurationsseite können Sie zahlreiche Aspekte der Kennwortverwaltung für Ihre Organisation steuern, darunter:

- Aktivieren und Deaktivieren der Kennwortzurücksetzung für alle Benutzer in einem Verzeichnis
- Festlegen der Mindestanzahl von Überprüfungsschritten (einer oder zwei), die ein Benutzer zum Zurücksetzen des Kennworts durchlaufen muss
- Festlegen der Art von Überprüfungsschritten, die Sie für Benutzer in Ihrer Organisation aktivieren möchten. Es stehen folgende Optionen zur Auswahl:
 - Mobiltelefon (Prüfcode per SMS oder Sprachanruf)
 - Bürotelefon (Sprachanruf)
 - Alternative E-Mail-Adresse (Prüfcode per E-Mail)
 - Sicherheitsfragen (informationsbasierte Authentifizierung)
- Festlegen der Anzahl von Fragen, die ein Benutzer registrieren muss, damit die Authentifizierungsmethode "Sicherheitsfragen" verwenden kann (nur sichtbar, wenn Sicherheitsfragen aktiviert sind)
- Festlegen der Anzahl von Fragen, die ein Benutzer beim Zurücksetzen beantworten muss, um die Authentifizierungsmethode "Sicherheitsfragen" zu verwenden (nur sichtbar, wenn Sicherheitsfragen aktiviert sind)
- Anhand vordefinierter, lokalisierter Sicherheitsfragen, aus denen ein Benutzer beim Registrieren für das Kennwortzurücksetzen auswählen kann (nur sichtbar, wenn Sicherheitsfragen aktiviert sind)
- Anhand der benutzerdefinierten Sicherheitsfragen, die ein Benutzer beim Registrieren für das Kennwortzurücksetzen auswählen kann (nur sichtbar, wenn Sicherheitsfragen aktiviert sind)
- Benutzer auffordern, registrieren Sie sich für das Kennwort zurückzusetzen, wenn sie auf die Anwendung zugreifen Zugriffsbereich unter [http://myapps.microsoft.com](http://myapps.microsoft.com).
- Erzwingen der erneuten Bestätigung von zuvor registrierten Daten durch die Benutzer, nachdem eine bestimmte Anzahl von Tagen vergangen ist (nur sichtbar, wenn die erzwungene Registrierung aktiviert ist)
- Bereitstellen einer benutzerdefinierten Helpdesk-E-Mail-Adresse oder -URL, die Benutzern angezeigt wird, wenn beim Zurücksetzen von Kennwörtern Probleme auftreten
- Aktivieren oder Deaktivieren der Kennwortrückschreibung (wenn die Kennwortrückschreibung mithilfe von AAD Connect bereitgestellt wurde)
- Anzeigen des Status für den Kennwortrückschreibung-Agent (wenn die Kennwortrückschreibung mithilfe von AAD Connect bereitgestellt wurde)
- Aktivieren e-Mail-Benachrichtigungen für Benutzer aus, wenn ihr eigenes Kennwort zurückgesetzt wurde (finden Sie in der **Benachrichtigungen** Teil der [Azure-Verwaltungsportal](https://manage.windowsazure.com))
- Aktivieren von e-Mail-Benachrichtigungen für Administratoren, wenn andere Administratoren ihre eigenen Kennwörter zurücksetzen (finden Sie in der **Benachrichtigungen** Teil der [Azure-Verwaltungsportal](https://manage.windowsazure.com)
- Branding des Portals und Kennwort zurückgesetzt mit Logo und den Namen Ihres Unternehmens-e-Mails mithilfe der Anpassungsfunktion (finden Sie in der **Verzeichniseigenschaften** Teil der [Azure-Verwaltungsportal](https://manage.windowsazure.com)

Weitere Informationen zum Konfigurieren der Kennwortverwaltung in Ihrer Organisation finden Sie unter [Erste Schritte: Azure AD-Kennwortverwaltung](active-directory-passwords-getting-started.md).

##Portal für die Benutzerregistrierung
Bevor Benutzer ihre Kennwörter zurücksetzen können, müssen ihre Cloudbenutzerkonten mit den richtigen Authentifizierungsdaten aktualisiert werden. Auf diese Weise wird sichergestellt, dass sie die geeignete Anzahl von Überprüfungsschritten für die Kennwortzurücksetzung durchlaufen können, die vom Administrator festgelegt wurde.  Administratoren können diese Authentifizierungsinformationen unter Verwendung des Azure- oder Office-Verwaltungsportals, mit DirSync / Azure AD Connect oder Windows PowerShell auch im Namen der Benutzer definieren. 

Wenn Sie möchten, dass Benutzer eigene Daten registrieren, wird auch eine Webseite bereitgestellt, auf der Benutzer diese Informationen selbst eingeben können.  Auf dieser Seite können Benutzer Authentifizierungsinformationen gemäß den Richtlinien für die Kennwortzurücksetzung eingeben, die in der Organisation aktiviert wurde.  Nachdem diese Daten überprüft wurden, werden sie im zugehörigen Cloudbenutzerkonto gespeichert, damit sie zu einem späteren Zeitpunkt wiederhergestellt werden können. Das Registrierungsportal sieht folgendermaßen aus:

  ![][001]

Weitere Informationen finden Sie unter [Erste Schritte: Azure AD-Kennwortverwaltung](active-directory-passwords-getting-started.md) und [Best Practices: Azure AD-Kennwortverwaltung](active-directory-passwords-best-practices.md). 

##Portal für das Zurücksetzen von Benutzerkennwörtern
Wenn Sie Self-service-Kennwort zurücksetzen, Self-service-kennwortzurücksetzungsrichtlinie Ihrer Organisation eingerichtet und sichergestellt, dass Ihre Benutzer über geeigneten Kontaktdaten im Verzeichnis haben aktiviert haben, werden Benutzer in Ihrer Organisation können ihre eigenen Kennwörter automatisch auf jeder Webseite zurücksetzen, die ein Geschäfts- oder Schulkonto für die Anmeldung verwendet werden (z. B. [portal.microsoftonline.com](https://portal.microsoftonline.com)). Auf diesen Seiten, Benutzer sehen ein **kann nicht auf Ihr Konto zugreifen?** Link. 

  ![][002]

Durch Klicken auf diesen Link wird das Self-Service-Portal für die Kennwortzurücksetzung gestartet.

  ![][003]

Um weitere Informationen dazu, wie Benutzer ihre eigenen Kennwörter zurücksetzen können, finden Sie unter [Erste Schritte: Azure AD-Kennwortverwaltung](active-directory-passwords-getting-started.md).

##Portal zum Ändern von Benutzerkennwörtern
Wenn Benutzer ihre eigenen Kennwörter ändern möchten, können sie hierzu jederzeit das Portal zum Ändern von Benutzerkennwörtern verwenden.  Benutzer können über die Profilseite im Zugriffsbereich auf das Portal zur Kennwortänderung zugreifen, oder indem sie auf den Link "Kennwort ändern" aus einer Office 365-Anwendung klicken.  Wenn ein Kennwort abläuft, werden Benutzer bei der Anmeldung automatisch aufgefordert, ihr Kennwort zu ändern. 

  ![][004]

In beiden Fällen werden die geänderten Kennwörter – sofern die Kennwortrückschreibung aktiviert ist und der Benutzer entweder ein Verbundkennwort oder ein synchronisiertes Kennwort verwendet – in das lokale Active Directory zurückgeschrieben. Das Portal zum Ändern von Benutzerkennwörtern sieht folgendermaßen aus: 

  ![][005]

Um weitere Informationen dazu, wie Benutzer ihre eigenen lokalen Active Directory-Kennwörter ändern können, finden Sie unter [Erste Schritte: Azure AD-Kennwortverwaltung](active-directory-passwords-getting-started.md).

##Berichte zur Kennwortverwaltung
Auf der **Berichte** Registerkarte der **Aktivitätsprotokolle** Abschnitt zwei Berichte zur Kennwortverwaltung angezeigt: **kennwortzurücksetzungsaktivität** und **Registrierungsaktivität für die kennwortzurücksetzung**.  Diese zwei Berichte liefern einen Überblick über Benutzer innerhalb Ihrer Organisation, die sich für das Zurücksetzen von Kennwörtern registrieren und die Kennwortzurücksetzung verwenden. Hier wird diese Berichte Aussehen der [Azure-Verwaltungsportal](https://manage.windowsazure.com):

  ![][006]

Weitere Informationen finden Sie unter [Einblicke: Kennwortverwaltung in Azure AD-Berichte](active-directory-passwords-get-insights.md).

##Komponente zur Kennwortrückschreibung von Azure AD Connect
Wenn die Kennwörter von Benutzern in Ihrer Organisation aus Ihrer lokalen Umgebung stammen (Verbund oder Kennwortsynchronisierung), können Sie die aktuelle Version von Azure AD Connect installieren, um diese Kennwörter direkt aus der Cloud zu aktualisieren.  Dies bedeutet, dass Benutzer ihre AD-Kennwörter direkt über das Web zurücksetzen oder ändern können, wenn sie sie vergessen haben oder ändern möchten.  Die Kennwortrückschreibung wird an dieser Stelle im Installations-Assistenten von Azure AD Connect aktiviert:

  ![][007]

Weitere Informationen zu Azure AD Connect finden Sie unter [Erste Schritte: Azure AD Connect](active-directory-aadconnect.md). Weitere Informationen zum Rückschreiben des Kennworts finden Sie unter [Erste Schritte: Azure AD-Kennwortverwaltung](active-directory-passwords-getting-started.md).


<br/>
<br/>
<br/>

## Links zur Dokumentation für die Kennwortzurücksetzung
Im Folgenden finden Sie Links zu allen Webseiten mit Informationen zur Kennwortzurücksetzung für Azure AD: 

* [**Ihr eigenes Kennwort zurücksetzen**](active-directory-passwords-update-your-own-password.md) -erfahren Sie mehr über zurücksetzen oder ändern Ihr eigenes Kennwort als Benutzer des Systems
* [**Erste Schritte**](active-directory-passwords-getting-started.md) -erfahren Sie, wie Sie Benutzern das Zurücksetzen und Ändern ihrer Cloud- oder lokalen Kennwörter erlauben
* [**Anpassen von**](active-directory-passwords-customize.md) -erfahren Sie, wie das Aussehen und Verhalten des Diensts, um den Bedürfnissen Ihres Unternehmens anpassen.
* [**Bewährte Methoden**](active-directory-passwords-best-practices.md) -erfahren Sie, wie schnell bereitstellen und effektiv verwalten von Kennwörtern in Ihrer Organisation
* [**Einblicke**](active-directory-passwords-get-insights.md) -erfahren Sie mehr über unsere integrierten Berichtsfunktionen
* [**Häufig gestellte Fragen zu**](active-directory-passwords-faq.md) – Hier erhalten Sie Antworten auf häufig gestellte Fragen
* [**Problembehandlung bei**](active-directory-passwords-troubleshoot.md) -erfahren Sie, wie Probleme mit dem Dienst schnell beheben
* [**Erfahren Sie mehr**](active-directory-passwords-learn-more.md) – erhalten Sie tiefgehende technische Details zur Funktionsweise des Diensts



[001]: ./media/active-directory-passwords-how-it-works/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-how-it-works/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-how-it-works/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-how-it-works/004.jpg "Image_004.jpg"
[005]: ./media/active-directory-passwords-how-it-works/005.jpg "Image_005.jpg"
[006]: ./media/active-directory-passwords-how-it-works/006.jpg "Image_006.jpg"
[007]: ./media/active-directory-passwords-how-it-works/007.jpg "Image_007.jpg"


