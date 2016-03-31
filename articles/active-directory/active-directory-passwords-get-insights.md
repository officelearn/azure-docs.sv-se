<properties 
    pageTitle="Operative Einblicke: Kennwortverwaltung in Azure AD | Microsoft Azure" 
    description="Dieser Artikel beschreibt, wie Sie Berichte verwenden, um einen Einblick in die Vorgänge zur Kennwortverwaltung in Ihrem Unternehmen zu erhalten." 
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

# Operative Einblicke durch Berichte zur Kennwortverwaltung
In diesem Abschnitt wird beschrieben, wie Sie Berichte zur Kennwortverwaltung in Azure Active Directory verwenden können, um die Verwendung der Kennwortzurücksetzung und -änderung durch Benutzer in Ihrer Organisation zu verfolgen.

- [**Übersicht über Berichte zur Kennwortverwaltung**](#overview-of-password-management-reports)
- [**Anzeigen von Berichten für die Kennwortverwaltung**](#how-to-view-password-management-reports)
- [**Anzeigen der Registrierungsaktivität für die kennwortzurücksetzung in Ihrer Organisation**](#view-password-reset-registration-activity)
- [**Ansicht Zurücksetzen des Kennworts in Ihrer Organisation**](#view-password-reset-activity)

## Übersicht über Berichte zur Kennwortverwaltung
Sobald Sie die Kennwortzurücksetzung bereitgestellt haben, besteht einer der nächsten Schritte üblicherweise darin, herauszufinden, wie diese in Ihrer Organisation verwendet wird.  Beispielsweise möchten Sie einen Einblick gewinnen, wie Benutzer sich für die Kennwortzurücksetzung registrieren oder wie viele Kennwortzurücksetzungen in den letzten paar Tagen stattgefunden haben.  Hier sind einige der häufigen Fragen, die mit den kennwortverwaltungsberichten beantworten, die in vorhanden sind, können die [Azure-Verwaltungsportal](https://manage.windowsazure.com) heute:

- Wie viele Personen haben sich für die Kennwortzurücksetzung registriert?
- Wer hat sich für das Zurücksetzen von Kennwörtern registriert?
- Welche Daten registrieren die Benutzer?
- Wie viele Personen haben ihre Kennwörter in den letzten 7 Tagen zurückgesetzt?
- Welche Methoden werden von Benutzern und Administratoren am häufigsten zum Zurücksetzen von Kennwörtern eingesetzt?
- Welche Probleme treten häufig für Benutzer oder Administratoren bei dem Versuch auf, das Kennwort zurückzusetzen?
- Welche Administratoren setzen häufig ihre eigenen Kennwörter zurück?
- Gibt es verdächtige Aktivitäten beim Zurücksetzen des Kennworts?


## Anzeigen von Berichten für die Kennwortverwaltung
Zum Finden der Berichte für die Kennwortverwaltung führen Sie die folgenden Schritte aus:

1.  Klicken Sie auf die **Active Directory** -Erweiterung in der [Azure-Verwaltungsportal](https://manage.windowsazure.com).
2.  Wählen Sie Ihr Verzeichnis aus der Liste aus, die im Portal angezeigt wird.
3.  Klicken Sie auf die **Berichte** Registerkarte.
4.  Suchen Sie unter den **Aktivitätsprotokolle** Abschnitt.
5.  Wählen Sie entweder die **kennwortzurücksetzungsaktivität** Bericht oder die **Registrierungsaktivität für die kennwortzurücksetzung** Bericht.

    ![][001]

## Zugreifen auf Berichte zur Kennwortverwaltung über eine API
Ab August 2015 unterstützen die Berichte und Ereignisse von Azure AD das Abrufen aller Informationen, die in den Berichten „Kennwortzurücksetzung“ und „Registrierung für Zurücksetzen des Kennworts“ enthalten sind.

Zum Zugreifen auf diese Daten müssen Sie eine kleine App oder ein Skript schreiben, um sie von unseren Servern abzurufen. [Enthält Informationen zum Einstieg in die Azure AD Reporting-API](active-directory-reporting-api-getting-started.md).

Nachdem Sie über ein funktionierendes Skript verfügen, sollten Sie sich als Nächstes mit den Kennwortzurücksetzungs- und Registrierungsereignissen befassen, die Sie zum Erfüllen Ihrer Szenarien abrufen können.

- [SsprActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent): Listet die verfügbaren Spalten für das Zurücksetzen von Ereignissen
- [SsprRegistrationActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprRegistrationActivityEvent): Listet die Spalten für das Zurücksetzen von Registrierungs-Ereignisse

## Anzeigen der Aktivität "Registrierung für Zurücksetzen des Kennworts"

Der Bericht "Aktivität "Registrierung für Zurücksetzen des Kennworts"" zeigt alle Registrierungen für die Kennwortzurücksetzung, die in Ihrer Organisation erfolgt sind.  In diesem Bericht wird eine Registrierung für die kennwortzurücksetzung angezeigt, jeder Benutzer, der erfolgreich Authentifizierungsinformationen beim den registriert wurde das Portal zur Registrierung für zurücksetzen ([http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)).

- **Max. Zeitraum**: 1 Monat
- **Maximale Anzahl von Zeilen**: unbegrenzt
- **Herunterladbare**: Ja, über eine CSV-Datei

    ![][002]

### Beschreibung der Berichtsspalten
In der folgende Liste werden alle Berichtsspalten im Detail beschrieben:

- **Benutzer** – der Benutzer, der versucht hat kennwortzurücksetzung zu registrieren.
- **Rolle** – die Rolle des Benutzers im Verzeichnis.
- **Datum und Uhrzeit** – das Datum und die Uhrzeit des Versuchs.
- **Registrierte Daten** – Authentifizierungsdaten, die vom Benutzer Kennwort während der bereitgestellten Registrierung zurückgesetzt.

### Beschreibung der Berichtswerte
Die folgende Tabelle beschreibt die verschiedenen Werte, die für die einzelnen Spalten zulässig sind:

Column|Zulässige Werte und ihre Bedeutung
---|---
Registrierte Daten| **Alternative e-Mail-Adresse** – Benutzer alternative e-Mail-Adresse oder Authentifizierung e-Mail-Adresse zur Authentifizierung verwendet.<p><p>**Telefon (Büro)**– Benutzer seine Bürotelefonnummer zur Authentifizierung<p>**Mobiltelefon** -Benutzer sein Mobiltelefon oder Telefon für Authentifizierung zum Authentifizieren<p>**Sicherheitsfragen** – Benutzer Sicherheitsfragen zur Authentifizierung verwendet.<p>**Eine beliebige Kombination der oben genannten (z. B. alternative e-Mail-Adresse und Mobiltelefon)** – tritt auf, wenn eine Richtlinie mit 2 Gates angegeben ist, und, welche zwei Methoden den Benutzer verwendet zeigt zur Authentifizierung seiner Anforderung Zurücksetzen des Kennworts.

## Anzeigen der Aktivität "Zurücksetzen des Kennworts"

Dieser Bericht zeigt alle Versuche der Kennwortzurücksetzung an, die in Ihrer Organisation erfolgt sind.

- **Max. Zeitraum**: 1 Monat
- **Maximale Anzahl von Zeilen**: unbegrenzt
- **Herunterladbare**: Ja, über eine CSV-Datei

    ![][003]

### Beschreibung der Berichtsspalten
In der folgende Liste werden alle Berichtsspalten im Detail beschrieben:

1. **Benutzer** – der Benutzer, der versucht ein Kennwort hat zurückzusetzen (basierend auf dem Feld Benutzer-ID bereitgestellt wird, wenn der Benutzer ein Kennwort zurückzusetzen).
2. **Rolle** – die Rolle des Benutzers im Verzeichnis.
3. **Datum und Uhrzeit** – das Datum und die Uhrzeit des Versuchs.
4. **Verwendete Methode(n)** – die Authentifizierungsmethoden, die der Benutzer dazu verwendete zurückzusetzen.
5. **Ergebnis** – das Endergebnis, das Kennwort zurückzusetzen.
6. **Details** – die Details, warum die kennwortzurücksetzung führte den Wert hat.  Enthält auch alle Maßnahmen, die Sie ergreifen können, um einen unerwarteten Fehler zu beheben.

### Beschreibung der Berichtswerte
Die folgende Tabelle beschreibt die verschiedenen Werte, die für die einzelnen Spalten zulässig sind:

Column|Zulässige Werte und ihre Bedeutung
---|---
Verwendete Methoden|**Alternative e-Mail-Adresse** – Benutzer alternative e-Mail-Adresse oder Authentifizierung e-Mail-Adresse zur Authentifizierung verwendet.<p>**Telefon (Büro)** – Benutzer seine Bürotelefonnummer zur Authentifizierung<p>**Mobiltelefon** – Benutzer verwendete das Mobiltelefon oder Telefon für Authentifizierung zum Authentifizieren<p>**Sicherheitsfragen** – Benutzer Sicherheitsfragen zur Authentifizierung verwendet.<p>**Eine beliebige Kombination der oben genannten (z. B. alternative e-Mail-Adresse und Mobiltelefon)** – tritt auf, wenn eine Richtlinie mit 2 Gates angegeben ist, und, welche zwei Methoden den Benutzer verwendet zeigt zur Authentifizierung seiner Anforderung Zurücksetzen des Kennworts.
Ergebnis|**Vorzeitig beendet** – Benutzer das Zurücksetzen des Kennworts gestartet, jedoch mittendrin beendet und nicht abgeschlossen<p>**Blockiert** -Konto des Benutzers konnte nicht zur kennwortzurücksetzung nicht verwenden, um die Seite "Kennwort zurücksetzen" zu verwenden oder ein Kennwort zurücksetzen Gate zu oft in einem Zeitraum von 24 Stunden<p>**Abgebrochen** – Benutzer das Zurücksetzen des Kennworts gestartet, aber dann auf die Schaltfläche "Abbrechen", um die Sitzung mittendrin abzubrechen geklickt <p>**Administrator kontaktiert** – Benutzer ist ein Problem aufgetreten, während seiner Sitzung, die er nicht auflösen konnte, sodass der Benutzer die "Administrator kontaktieren" Link geklickt, statt das Kennwort<p>**Fehler bei** – Benutzer konnte sich nicht um ein Kennwort zurückgesetzt, da der Benutzer nicht konfiguriert wurde, um die Funktion zu verwenden (z. B. keine Lizenz, fehlende Informationen für die Authentifizierung, lokal verwalteten Kennwort aber Rückschreiben ist deaktiviert).<p>**Erfolgreich** – das Zurücksetzen des Kennworts war erfolgreich.
Details|Beachten Sie die folgende Tabelle.

### Zulässige Werte für die Spalte "Details"
Nachfolgend finden Sie die Liste der Ergebnistypen, die Sie im Bericht zur Aktivität "Zurücksetzen des Kennworts" erwarten können:

Details | Ergebnistyp
----|----
Benutzer hat nach Abschluss der Überprüfung per E-Mail vorzeitig beendet.  | Vorzeitig beendet
Benutzer hat nach Abschluss der Überprüfung per Mobiltelefon-SMS vorzeitig beendet.|Vorzeitig beendet
Benutzer hat nach Abschluss der Überprüfung per Mobiltelefon-Sprachanruf vorzeitig beendet. | Vorzeitig beendet
Benutzer hat nach Abschluss der Überprüfung per Bürotelefon-Sprachanruf vorzeitig beendet. | Vorzeitig beendet
Benutzer hat nach Abschluss der Überprüfung per Sicherheitsfragen vorzeitig beendet.|Vorzeitig beendet
Benutzer hat nach Eingabe der Benutzer-ID vorzeitig beendet.| Vorzeitig beendet
Benutzer hat nach dem Start der Überprüfung per E-Mail vorzeitig beendet.|Vorzeitig beendet
Benutzer hat nach dem Start der Überprüfung per Mobiltelefon-SMS vorzeitig beendet.|Vorzeitig beendet
Benutzer hat nach dem Start der Überprüfung per Mobiltelefon-Sprachanruf vorzeitig beendet.|Vorzeitig beendet
Benutzer hat nach dem Start der Überprüfung per Bürotelefon-Sprachanruf vorzeitig beendet.|Vorzeitig beendet
Benutzer hat nach dem Start der Überprüfung per Sicherheitsfragen vorzeitig beendet.| Vorzeitig beendet
Benutzer hat vor Auswahl eines neuen Kennworts vorzeitig beendet.| Vorzeitig beendet
Benutzer hat während Auswahl eines neuen Kennworts vorzeitig beendet.| Vorzeitig beendet
Benutzer hat zu viele ungültige SMS-Überprüfungscodes eingegeben und ist für 24 Stunden blockiert.|Blockiert
Benutzer hat die Überprüfung per Mobiltelefon-Sprachanruf zu oft versucht und ist für 24 Stunden blockiert.|Blockiert
Benutzer hat die Überprüfung per Bürotelefon-Sprachanruf zu oft versucht und ist für 24 Stunden blockiert. |Blockiert
Benutzer hat zu häufig versucht, die Sicherheitsfragen zu beantworten, und ist für 24 Stunden blockiert.| Blockiert
Benutzer hat zu oft versucht, eine Telefonnummer zu überprüfen, und ist für 24 Stunden blockiert.|Blockiert
Benutzer hat vor Übergabe der erforderlichen Authentifizierungsmethoden abgebrochen.|Abgebrochen
Benutzer hat vor Übermittlung eines neuen Kennworts abgebrochen.|Abgebrochen
Benutzer hat nach Versuch der Überprüfung per E-Mail den Administrator kontaktiert. |Administrator kontaktiert
Benutzer hat nach Versuch der Überprüfung per Mobiltelefon-SMS den Administrator kontaktiert.|Administrator kontaktiert
Benutzer hat nach Versuch der Überprüfung per Mobiltelefon-Sprachanruf den Administrator kontaktiert.|Administrator kontaktiert
Benutzer hat nach Versuch der Überprüfung per Bürotelefon-Sprachanruf den Administrator kontaktiert. |Administrator kontaktiert
Benutzer hat nach Versuch der Überprüfung per Sicherheitsfrage den Administrator kontaktiert.|Administrator kontaktiert
Die Kennwortzurücksetzung ist für diesen Benutzer nicht aktiviert. Aktivieren Sie die Kennwortzurücksetzung auf der Konfigurationsregisterkarte, um dieses Problem zu beheben.|  Fehler
 Der Benutzer hat keine Lizenz. Sie können dem Benutzer eine Lizenz hinzufügen, um dieses Problems zu beheben. |Fehler
Benutzer hat versucht, die Zurücksetzung von einem Gerät ohne aktivierte Cookies durchzuführen.| Fehler
Im Benutzerkonto sind nicht genügend Authentifizierungsmethoden definiert. Fügen Sie Authentifizierungsinformationen hinzu, um dieses Problem zu beheben.|Fehler
Das Kennwort des Benutzers wird lokal verwaltet. Sie können die Kennwortrückschreibung aktivieren, um dieses Problem zu beheben.|Fehler
Wir konnten den Dienst zum Zurücksetzen Ihres lokalen Kennworts nicht erreichen. Überprüfen Sie das Ereignisprotokoll des Synchronisierungscomputers.|Fehler
Beim Zurücksetzen des lokalen Kennworts des Benutzers ist ein Problem aufgetreten. Überprüfen Sie das Ereignisprotokoll des Synchronisierungscomputers. | Fehler
Der Benutzer ist kein Mitglied der Benutzergruppe für Kennwortzurücksetzung. Fügen Sie diesen Benutzer dieser Gruppe hinzu, um dieses Problem zu beheben.|Fehler
Die Kennwortzurücksetzung wurde für diesen Mandanten vollständig deaktiviert. Finden Sie unter [hier](http://aka.ms/ssprtroubleshoot) zur Lösung des Problems. | Fehler
Benutzer hat das Kennwort erfolgreich zurückgesetzt.|Succeeded

## Links zu Informationen zur Kennwortzurücksetzung
Im Folgenden finden Sie Links zu allen Webseiten mit Informationen zur Kennwortzurücksetzung für Azure AD: 

* [**Ihr eigenes Kennwort zurücksetzen**](active-directory-passwords-update-your-own-password.md) -erfahren Sie mehr über zurücksetzen oder ändern Ihr eigenes Kennwort als Benutzer des Systems
* [**Funktionsweise**](active-directory-passwords-how-it-works.md) -erfahren Sie mehr über die sechs verschiedenen Komponenten der Dienst und die jeweilige
* [**Erste Schritte**](active-directory-passwords-getting-started.md) -erfahren Sie, wie Sie Benutzern das Zurücksetzen und Ändern ihrer Cloud- oder lokalen Kennwörter erlauben
* [**Anpassen von**](active-directory-passwords-customize.md) -erfahren Sie, wie das Aussehen und Verhalten des Diensts, um den Bedürfnissen Ihres Unternehmens anpassen.
* [**Bewährte Methoden**](active-directory-passwords-best-practices.md) -erfahren Sie, wie schnell bereitstellen und effektiv verwalten von Kennwörtern in Ihrer Organisation
* [**Häufig gestellte Fragen zu**](active-directory-passwords-faq.md) – Hier erhalten Sie Antworten auf häufig gestellte Fragen
* [**Problembehandlung bei**](active-directory-passwords-troubleshoot.md) -erfahren Sie, wie Probleme mit dem Dienst schnell beheben
* [**Erfahren Sie mehr**](active-directory-passwords-learn-more.md) – erhalten Sie tiefgehende technische Details zur Funktionsweise des Diensts



[001]: ./media/active-directory-passwords-get-insights/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-get-insights/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-get-insights/003.jpg "Image_003.jpg"


