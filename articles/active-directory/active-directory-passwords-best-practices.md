<properties 
    pageTitle="Best Practices: Azure AD-Kennwortverwaltung | Microsoft Azure" 
    description="Best Practices für die Bereitstellung und Nutzung, Beispieldokumentation für Endbenutzer und Schulungshandbücher für die Kennwortverwaltung in Azure Active Directory." 
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

# Bereitstellen der Kennwortverwaltung und Schulen der Benutzer zu deren Verwendung
Nachdem Sie die Kennwortzurücksetzung aktiviert haben, müssen Sie als Nächstes die Benutzer dazu anhalten, diesen Dienst in Ihrer Organisation zu verwenden. Zu diesem Zweck müssen Sie sicherstellen, dass Ihre Benutzer so konfiguriert sind, dass sie den Dienst ordnungsgemäß verwenden können. Außerdem müssen die Benutzer für die Verwaltung ihrer eigenen Kennwörter geschult werden. In diesem Artikel werden die folgenden Konzepte erläutert:

* [**Wie Sie Ihre Benutzer für die Verwaltung der Kennwörter abrufen**](#how-to-get-users-configured-for-password-reset)
  * [Erforderliche Konfiguration für die Kontozurücksetzung](#what-makes-an-account-configured)
  * [Methoden zum selbstständigen Auffüllen der Authentifizierungsdaten](#ways-to-populate-authentication-data)
* [**Die besten Methoden zum Einführen der kennwortzurücksetzung in Ihrer Organisation**](#what-is-the-best-way-to-roll-out-password-reset-for-users)
  * [E-Mail-basierte Einführung und Beispiele für die E-Mail-Kommunikation](#email-based-rollout)
  * [Erstellen eines benutzerdefinierten Portals zur Kennwortverwaltung für Ihre Benutzer](#creating-your-own-password-portal)
  * [Verwenden der erzwungenen Registrierung zum Durchsetzen der Benutzerregistrierung bei der Anmeldung](#using-enforced-registration)
  * [Hochladen von Authentifizierungsdaten für Benutzerkonten](#uploading-data-yourself)
* [**Für Benutzer und Support-Schulungsmaterialien (demnächst verfügbar!)**](#sample-training-materials)

## Konfigurieren von Benutzern für die Kennwortzurücksetzung
Dieser Abschnitt beschreibt verschiedene Methoden, mit denen Sie sicherstellen können, dass alle Benutzer in Ihrer Organisation ihre Kennwörter selbstständig und effektiv zurücksetzen können, falls sie ihre Kennwörter vergessen haben.

### Erforderliche Kontokonfiguration
Bevor ein Benutzer das Zurücksetzen von Kennwörtern, verwenden kann **alle** Folgendes erfüllt sein:

1.  Die Kennwortzurücksetzung muss im Verzeichnis aktiviert sein.  Enthält Informationen zum Aktivieren der kennwortzurücksetzung [können Benutzer ihre Azure AD-Kennwörter zurücksetzen](active-directory-passwords-getting-started.md#enable-users-to-reset-their-azure-ad-passwords) oder [ermöglichen Benutzern, zurücksetzen oder Ändern ihrer AD-Kennwörter](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords)
2.  Der Benutzer muss lizenziert sein.
 - Cloud-Benutzer müssen **eine beliebige kostenpflichtige Office 365-Lizenz**, oder ein **AAD Basic** oder **AAD Premium-Lizenz** zugewiesen.
 - Für lokale Benutzer (Verbund oder hashsynchronisiert), die **muss eine AAD Premium-Lizenz zugewiesen sein**.
3.  Die Benutzer müssen die **Mindestsatz an Authentifizierungsdaten definiert** in Übereinstimmung mit der aktuellen kontozurücksetzungsrichtlinie.
 - Authentifizierungsdaten gelten als definiert, wenn das entsprechende Feld im Verzeichnis wohlgeformte Daten enthält.
 - Ein Mindestsatz von Authentifizierungsdaten ist definiert als **mindestens eine** der aktivierten Authentifizierungsoptionen, wenn eine Richtlinie mit einem Gate konfiguriert ist, bzw. **mindestens zwei** der aktivierten Authentifizierungsoptionen, wenn zwei Richtlinie für die Überprüfung konfiguriert ist.
4.  Wenn der Benutzer ein lokales Konto verwendet [das Rückschreiben von Kennwörtern](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords) muss aktiviert und eingeschaltet sein

### Möglichkeiten zum Auffüllen von Authentifizierungsdaten
Sie haben mehrere Möglichkeiten, für Organisationsbenutzer Daten einzugeben, die für das Zurücksetzen von Kennwörtern verwendet werden.

- Bearbeiten von Benutzern in der [Azure-Verwaltungsportal](https://manage.windowsazure.com) oder [Verwaltungsportal von Office 365](https://portal.microsoftonline.com)
- Verwenden Sie Azure AD Sync, um Benutzereigenschaften aus einer lokalen Active Directory-Domäne mit Azure AD zu synchronisieren.
- Mithilfe von Windows PowerShell zum Bearbeiten der Benutzereigenschaften von [die Schritte hier](active-directory-passwords-learn-more.md#how-to-access-password-reset-data-for-your-users).
- Ermöglicht es Benutzern, ihre eigenen Daten zu registrieren, auf dem registrierungsportal unter [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)
- Müssen Benutzer für die kennwortzurücksetzung, wenn ihre Azure AD-Konto durch Festlegen im Anmeldung Registrieren der  [**erfordern, dass Benutzer bei der Anmeldung registrieren?**](active-directory-passwords-customize.md#require-users-to-register-when-signing-in) Konfigurationsoption **Ja**.

Benutzer müssen sich für die Kennwortzurücksetzung nicht registrieren, damit das System funktioniert.  Wenn Sie beispielsweise über vorhandene Mobil- oder geschäftliche Rufnummern in Ihrem lokalen Verzeichnis verfügen, können Sie diese in Azure AD synchronisieren, und wir verwenden sie automatisch für die Kennwortzurücksetzung.

Sie können auch erfahren Sie mehr über [wie Daten durch das Zurücksetzen von Kennwörtern verwendet werden](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset) und [wie Sie einzelne Authentifizierung Felder mit PowerShell füllen können](active-directory-passwords-learn-more.md#how-to-access-password-reset-data-for-your-users).

## Welche Möglichkeit ist am besten zum Einführen der Kennwortzurücksetzung für Benutzer geeignet?
Im Folgenden finden Sie allgemeine Schritte zur Einführung der Kennwortrücksetzung:

1.  Aktivieren Sie das Kennwort in Ihrem Verzeichnis zurücksetzen, indem Sie auf die **konfigurieren** Registerkarte der [Azure-Verwaltungsportal](https://manage.windowsazure.com) und **Ja** für die **Benutzer zum Zurücksetzen des Kennworts aktiviert** Option.
2.  Weisen Sie die entsprechenden Lizenzen für jeden Benutzer, denen Sie kennwortzurücksetzung bieten möchten, die durch das Aufrufen der **Lizenzen** Registerkarte der [Azure-Verwaltungsportal](https://manage.windowsazure.com).
3.  Beschränken Sie optional Kennwort zurücksetzen auf eine Gruppe von Benutzern, um das Feature langsam Zeit durch Festlegen der **Beschränken des Zugriffs auf das Zurücksetzen des Kennworts** Wechseln Sie zur **Ja** und wählen eine Sicherheitsgruppe für das Zurücksetzen des Kennworts (Beachten Sie diese Benutzer alle müssen Lizenzen zugewiesen) aktivieren.
4.  Weisen Sie die Benutzer mit Kennwort zurücksetzen, indem Sie entweder sendet ihnen eine e-Mail mit Anweisungen zur Registrierung Aktivieren der erzwungenen Registrierung im Zugriffsbereich oder indem Sie die entsprechenden Authentifizierungsdaten für diese Benutzer über DirSync, PowerShell, hochladen oder die [Azure-Verwaltungsportal](https://manage.windowsazure.com).  Weitere Informationen hierzu finden Sie im Folgenden.
5.  Überprüfen Sie im Laufe der Zeit registrierenden Benutzer, indem die Navigation auf der Registerkarte "Berichte" und die Anzeige der [**Registrierung Kennwortzurücksetzungsaktivität**](active-directory-passwords-get-insights.md#view-password-reset-registration-activity) Bericht.
6.  Wenn zahlreiche Benutzer registriert haben, beobachten sie das Kennwort zurücksetzen, indem Sie auf der Registerkarte "Berichte" navigieren und das Anzeigen von der [**Kennwortzurücksetzungsaktivität**](active-directory-passwords-get-insights.md#view-password-reset-activity) Bericht.

Es gibt mehrere Möglichkeiten, die Benutzer darüber informieren, dass sie sich in Ihrer Organisation für die Kennwortzurücksetzung registrieren und diese verwenden können.  Sie werden nachfolgend beschrieben.

### E-Mail-basierte Einführung
Der einfachste Ansatz zum Informieren Ihrer Benutzer über die Registrierung für die Kennwortzurücksetzung oder über deren Verwendung besteht wahrscheinlich darin, ihnen eine E-Mail mit entsprechenden Anweisungen zu senden.  Unten sehen Sie eine Vorlage, die Sie zu diesem Zweck verwenden können.  Gerne können Sie die Farben / Logos durch eigene ersetzen und die Vorlage nach Ihren spezifischen Anforderungen anpassen.

  ![][001]

Sie können [Herunterladen hier die e-Mail-Vorlage](http://1drv.ms/1xWFtQM).

### Erstellen eines eigenen Kennwortportals
Eine für größere Kunden gut geeignete Strategie zur Bereitstellung der Möglichkeit einer Kennwortverwaltung ist die Einrichtung eines einheitlichen „Kennwortportals“, auf dem die Benutzer alle Aufgaben in Bezug auf Kennwörter an einem zentralen Ort durchführen können.  

Viele unserer größten Kunden wählen Sie einen Stamm-DNS-Eintrag erstellen, wie https://passwords.contoso.com mit Links zu den Azure AD-Kennwort Portal zurücksetzen und Registrierung kennwortzurücksetzungsportal Kennwort Seiten ändern.  Auf diese Weise lässt sich in alle ausgesendeten E-Mails oder Flyer ein kurzer, einprägsamer URL einfügen, auf den Benutzer im Bedarfsfall klicken können, um die Nutzung des Diensts zu beginnen. 

Um einen Einstieg zu schaffen, haben wir eine einfache Seite erstellt, in der aktuelle Konzepte für reaktionsfähige Benutzeroberflächen eingesetzt werden und die auf allen Browsern und mobilen Geräten lauffähig ist.

  ![][007]
  
Sie können [Downloaden Sie die Website-Vorlage hier](https://github.com/kenhoff/password-reset-page).  Es wird empfohlen, das Logo und die Farben dem Bedarf Ihrer Organisation anpassen.

### Verwenden der erzwungenen Registrierung
Wenn Ihre Benutzer für die kennwortzurücksetzung selbst registrieren sollen, können Sie auch zwingen zur Registrierung bei der Anmeldung im Zugriffsbereich: [http://myapps.microsoft.com](http://myapps.microsoft.com).  Sie können diese Option aktivieren, aus des Verzeichnis **konfigurieren** Registerkarte durch die Aktivierung der **sollen sich Benutzer für die Registrierung bei der Anmeldung im Zugriffsbereich** Option.  

Sie können optional auch definieren, ob sie aufgefordert werden, nach einem konfigurierbaren Zeitraum ändern erneut registrieren der **Anzahl der Tage, bevor Benutzer ihre Kontaktdaten bestätigen müssen** Option ein Wert ungleich NULL ist. Finden Sie unter [Benutzerkennwortverwaltung anpassen](active-directory-passwords-customize.md#password-management-behavior) Weitere Informationen.

  ![][002]

Nachdem Sie diese Option aktiviert haben, wird den Benutzern bei der Anmeldung im Zugriffsbereich ein Popupfenster angezeigt, das sie über die vom Administrator vorgegebene Notwendigkeit zur Prüfung ihrer Kontaktinformationen informiert. Sie können darüber ihr Kennwort zurücksetzen, falls sie jemals den Zugriff auf ihr Konto verlieren.

  ![][003]

Auf **Jetzt überprüfen** bringt diese in die **Registrierung kennwortzurücksetzungsportal** an [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup) und müssen sie sich registrieren.  Registrierung über diese Methode kann verworfen werden, indem Sie auf der **Abbrechen** Schaltfläche oder schließen das Fenster, aber Benutzer daran erinnert sie, wenn sie sich nicht registrieren anmelden.

  ![][004]

### Selbstständiges Hochladen von Daten
Wenn Sie Authentifizierungsdaten selbst hochladen möchten, müssen Benutzer sich nicht für die Kennwortzurücksetzung registrieren, damit sie ihre Kennwörter zurücksetzen können.  Solange in den Konten der Benutzer Authentifizierungsdaten definiert sind, die der von Ihnen definierten Richtlinie für die Kontozurücksetzung entsprechen, können diese Benutzer ihre Kennwörter zurücksetzen.

Welche Eigenschaften Sie über AAD Connect oder Windows PowerShell festlegen können, finden Sie unter [welche Daten durch das Zurücksetzen von Kennwörtern verwendet](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset).

Sie können die Authentifizierungsdaten über Hochladen der [Azure-Verwaltungsportal](https://manage.windowsazure.com) durch die folgenden Schritte aus:

1.  Navigieren Sie zu Ihrem Verzeichnis in die **Active Directory-Erweiterung** in die [Azure-Verwaltungsportal](https://manage.windowsazure.com).
2.  Klicken Sie auf die **Benutzer** Registerkarte.
3.  Wählen Sie den für Sie relevanten Benutzer aus der Liste aus.
4.  Auf der ersten Registerkarte finden Sie **Alternative e-Mail-Adresse**, die als Eigenschaft so aktivieren Sie das Zurücksetzen des Kennworts verwendet werden können. 

    ![][005]

5.  Klicken Sie auf die **Arbeitsinformationen** Registerkarte.
6.  Auf dieser Seite finden Sie **Telefon (Büro)**, **Mobiltelefon**, **Authentifizierungstelefon**, und **Authentifizierung E-Mail**.  Diese Eigenschaften können auch so festgelegt werden, dass sie einem Benutzer das Zurücksetzen des Kennworts ermöglichen. 

    ![][006]

Finden Sie unter [welche Daten durch das Zurücksetzen von Kennwörtern verwendet](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset) zu sehen, wie jede dieser Eigenschaften verwendet werden kann.

Finden Sie unter [Kennwort Zugriff auf Daten für Ihre Benutzer über PowerShell zurücksetzen](active-directory-passwords-learn-more.md#how-to-access-password-reset-data-for-your-users) sehen, wie Sie lesen und diese Daten mit PowerShell festlegen können.

## Beispielschulungsmaterial
Wir arbeiten an Beispielschulungsmaterial, mit dem Sie Ihre IT-Organisation und Ihre Benutzer im Hinblick auf die Bereitstellung und Verwendung der Kennwortzurücksetzung schnell auf den neuesten Stand bringen können.  Halten Sie sich auf dem Laufenden.


<br/>
<br/>
<br/>

## Links zur Dokumentation für die Kennwortzurücksetzung
Im Folgenden finden Sie Links zu allen Webseiten mit Informationen zur Kennwortzurücksetzung für Azure AD: 

* [**Ihr eigenes Kennwort zurücksetzen**](active-directory-passwords-update-your-own-password.md) -erfahren Sie mehr über zurücksetzen oder ändern Ihr eigenes Kennwort als Benutzer des Systems
* [**Funktionsweise**](active-directory-passwords-how-it-works.md) -erfahren Sie mehr über die sechs verschiedenen Komponenten der Dienst und die jeweilige
* [**Erste Schritte**](active-directory-passwords-getting-started.md) -erfahren Sie, wie Sie Benutzern das Zurücksetzen und Ändern ihrer Cloud- oder lokalen Kennwörter erlauben
* [**Anpassen von**](active-directory-passwords-customize.md) -erfahren Sie, wie das Aussehen und Verhalten des Diensts, um den Bedürfnissen Ihres Unternehmens anpassen.
* [**Einblicke**](active-directory-passwords-get-insights.md) -erfahren Sie mehr über unsere integrierten Berichtsfunktionen
* [**Häufig gestellte Fragen zu**](active-directory-passwords-faq.md) – Hier erhalten Sie Antworten auf häufig gestellte Fragen
* [**Problembehandlung bei**](active-directory-passwords-troubleshoot.md) -erfahren Sie, wie Probleme mit dem Dienst schnell beheben
* [**Erfahren Sie mehr**](active-directory-passwords-learn-more.md) – erhalten Sie tiefgehende technische Details zur Funktionsweise des Diensts



[001]: ./media/active-directory-passwords-best-practices/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-best-practices/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-best-practices/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-best-practices/004.jpg "Image_004.jpg"
[005]: ./media/active-directory-passwords-best-practices/005.jpg "Image_005.jpg"
[006]: ./media/active-directory-passwords-best-practices/006.jpg "Image_006.jpg"
[007]: ./media/active-directory-passwords-best-practices/007.jpg "Image_007.jpg"


