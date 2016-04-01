<properties
    pageTitle="Kennwortrichtlinien und -einschränkungen in Azure Active Directory | Microsoft Azure"
    description="Beschreibt die Richtlinien, die in Azure Active Directory für Kennwörter gelten, einschließlich der zulässigen Zeichen, der Länge und dem Ablauf."
  services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="stevenpo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/03/2015"
    ms.author="curtand"/>


# Kennwortrichtlinien und -einschränkungen in Azure Active Directory

Dieser Artikel beschreibt die Kennwortrichtlinien und Komplexitätsanforderungen im Zusammenhang mit den in Ihrem Azure AD-Verzeichnis gespeicherten Benutzerkonten.

## UserPrincipalName-Richtlinien, die für alle Benutzerkonten gelten

Jedes Benutzerkonto, das sich beim Azure AD-Authentifizierungssystem anmeldet, muss über ein eindeutiges, diesem Konto zugeordnetes Benutzerprinzipalnamenattribut (UPN, User Principal Name) verfügen. Die folgende Tabelle enthält die Richtlinien, gelten für beide lokale Active Directory basierende Benutzerkonten (mit der Cloud synchronisiert) und nur-Cloud-Benutzerkonten.

|   Eigenschaft           |     UserPrincipalName-Richtlinien  |
|   ----------------------- |   ----------------------- |
|  Zulässige Zeichen    |  <ul> <li>A – Z</li> <li>-z </li><li>0 – 9</li> <li> . - \_ ! \# ^ \~</li></ul> |
|  Unzulässige Zeichen  | <ul> <li>@</li> <li>Darf keine Punkte enthalten '.' unmittelbar vor dem "@" Symbol</li></ul> |
| Längenbeschränkungen  |       <ul> <li>Die Gesamtlänge darf 113 Zeichen nicht überschreiten.</li><li>64 Zeichen vor dem "@" Symbol</li><li>48 Zeichen nach dem ' @' Symbol</li></ul>

## Kennwortrichtlinien, die nur für Cloudbenutzerkonten gelten

Die folgende Tabelle beschreibt die Einstellungen für Kennwortrichtlinien, die auf Benutzerkonten angewendet werden können, die erstellt und in Azure AD verwaltet werden.

|  Eigenschaft       |    Anforderungen          |
|   ----------------------- |   ----------------------- |
|  Zulässige Zeichen   |   <ul><li>A – Z</li><li>-z </li><li>0 – 9</li> <li>@ # $ % ^ & * - _ ! + = [ ] { } &#124; \ : ‘ , . ? / ` ~ “ ( ) ;</li></ul> |
|  Unzulässige Zeichen   |       <ul><li>Unicode-Zeichen</li><li>Leerzeichen</li><li>Leerzeichen</li><li> **Nur sichere Kennwörter**: Punkt enthalten darf nicht '.' unmittelbar vor der ' @' Symbol</li></ul> |
|   Kennworteinschränkungen | <ul><li>mindestens 8 Zeichen und höchstens 16 Zeichen</li><li>**Nur sichere Kennwörter**: erfordert 3 aus 4 der folgenden:<ul><li>Kleinbuchstaben</li><li>Großbuchstaben</li><li>Zahlen (0-9)</li><li>Symbole (Siehe kennworteinschränkungen oben)</li></ul></li></ul> |
| Zeitraum bis zum Ablauf des Kennworts      | <ul><li>Standardwert: **90** Tage </li><li>Wert ist mit dem Set-MsolPasswordPolicy-Cmdlet aus dem Azure Active Directory-Modul für Windows PowerShell konfigurierbar.</li></ul> |
| Benachrichtigung bei Ablauf des Kennworts |  <ul><li>Standardwert: **14** Tage (bevor das Kennwort abläuft)</li><li>Wert ist konfigurierbar mit dem Set-MsolPasswordPolicy-Cmdlet.</li></ul> |
| Ablauf des Kennworts |  <ul><li>Standardwert: **false** Tage (gibt an, dass Kennwortablauf aktiviert ist) </li><li>Wert kann für einzelne Benutzerkonten mithilfe des Set-MsolUser-Cmdlet konfiguriert werden. </li></ul> |
|  Kennwortverlauf  | Das letzte Kennwort kann nicht erneut verwendet werden. |
|  Zeitraum für Kennwortverlauf | Endlos |
|  Kontosperrung | <ul><li>Nach 10 erfolglosen Versuchen (Falsches Kennwort) anmelden, muss der Benutzer ein CAPTCHA-Dialogfeld als Teil der Anmeldung lösen.</li><li>Nach weiteren 10 erfolglosen Anmeldeversuchen (Falsches Kennwort) und richtiger Lösung des CAPTCHA-Dialogfelds Benutzer werden gesperrt für einen bestimmten Zeitraum. Weitere führen einen exponentiellen Anstieg in der Sperrung Zeitraum falsche Kennwörter.</li></ul> |


## Nächste Schritte

* [Verwalten von Kennwörtern von einem beliebigen Ort aus](active-directory-passwords.md)
* [Funktionsweise der Kennwortverwaltung
](active-directory-passwords-how-it-works.md)
* [Erste Schritte mit der Kennwortverwaltung](active-directory-passwords-getting-started.md)
* [Anpassen der Kennwortverwaltung](active-directory-passwords-customize.md)
* [Best Practices für die Kennwortverwaltung](active-directory-passwords-best-practices.md)
* [Operative Einblicke durch Berichte zur Kennwortverwaltung](active-directory-passwords-get-insights.md)
* [Häufig gestellte Fragen zur Kennwortverwaltung](active-directory-passwords-faq.md)
* [Problembehandlung für die Kennwortverwaltung](active-directory-passwords-troubleshoot.md)
* [Weitere Informationen](active-directory-passwords-learn-more.md)


