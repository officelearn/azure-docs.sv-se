<properties
    pageTitle="Häufig gestellte Fragen zu Azure AD Connect | Microsoft Azure"
    description="Auf dieser Seite finden Sie häufig gestellte Fragen zu Azure AD Connect."
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/02/2015"
    ms.author="billmath"/>

# Häufig gestellte Fragen zu Azure AD Connect

## Allgemeine Installation
**F: Ist die Installation möglich, wenn der globale Azure AD-Administrator 2FA aktiviert hat?**

Die Installation ist in diesem Fall nicht möglich. Der globale Administrator, der Azure AD Connect installiert, darf MFA nicht aktiviert haben. Wir arbeiten daran, diese Funktion in Zukunft zu unterstützen.

**F: Gibt es eine Möglichkeit, Azure AD Connect unbeaufsichtigt zu installieren?**

Azure AD Connect kann nur mit dem Installationsassistenten installiert werden. Eine unbeaufsichtigte und automatische Installation wird nicht unterstützt.

**F: Ich habe eine Gesamtstruktur, in dem eine Domäne nicht erreichbar ist. Wie installiere ich Azure AD Connect?**

Wir werden dieses Feedback in zukünftigen Versionen berücksichtigen.

## Netzwerk
**F: Ich habe eine Firewall, das Netzwerkgerät, oder etwas, das die maximale Zeit Verbindungen beschränkt bleiben im Netzwerk geöffnet. Wie lange soll mein Timeout-Schwellenwert für Client-Seite sein, wenn Azure AD Connect verwendet wird?**

Für Netzwerksoftware, physische Geräte und alle anderen Komponenten, durch die die maximale Länge von offenen Verbindungen eingeschränkt wird, sollte ein Schwellenwert von mindestens 5 Minuten (300 Sekunden) für die Konnektivität zwischen dem Server, auf dem der Azure AD Connect-Client installiert ist, und Azure Active Directory verwendet werden. Dies gilt auch für alle zuvor veröffentlichten Microsoft Identity-Synchronisierungswerkzeuge.

**F: Was muss ich tun, wenn ich eine E-Mail erhalte, die mich auffordert, mein Office 365-Zertifikat zu erneuern?**

Verwenden Sie die Anweisungen, die in diesem Artikel aufgelöst beschriebenen [hier](active-directory-aadconnect-o365-certs.md) das Zertifikat zu erneuern.

**F: Werden SLDs (einteilige Domänen) unterstützt?**

Nein. Azure AD Connect unterstützt keine lokalen Gesamtstrukturen/Domänen mit SLDs.

**F: Werden NetBios mit punktierten Namen unterstützt?**

Nein. Azure AD Connect unterstützt keine lokalen Gesamtstrukturen/Domänen, deren NetBios-Name einen Punkt enthält.

## Environment

**F: Kann der Server nach der Installation von Azure AD umbenannt werden?**

Nr. Ändern den Namen des Servers führt dazu, dass das Synchronisierungsmodul nicht mit der SQL-Datenbank herstellen können, und der Dienst wird nicht gestartet werden.

## Identitätsdaten

**F: Warum entspricht das UPN (UserPrincipalName)-Attribut in Azure AD dem lokalen UPN?**

Informationen hierzu finden Sie in diesen Artikeln:

- [Benutzernamen in Office 365, Azure oder Intune entsprechen nicht dem lokalen UPN oder der alternativen Anmelde-ID.](https://support.microsoft.com/en-us/kb/2523192)
- [Änderungen werden nicht mehr vom Azure Active Directory-Synchronisierungstool synchronisiert, wenn der UPN eines Benutzerkontos für die Verbindung mit einer anderen Verbunddomäne geändert wird.](https://support.microsoft.com/en-us/kb/2669550)

## Benutzerdefinierte Konfiguration

**F: Wo sind die PowerShell-Cmdlets für Azure AD Connect dokumentiert?**

Mit Ausnahme der auf dieser Website dokumentierten Cmdlets werden keine PowerShell-Cmdlets in Azure AD Connect zur Verwendung für Kunden unterstützt.

**F: Kann ich mithilfe von „Serverexport/Serverimport“ in Service Manager die Konfiguration zwischen Servern verschieben?**

Nr. Diese Option werden nicht alle Einstellungen abgerufen werden und sollte nicht verwendet werden. Verwenden Sie stattdessen den Assistenten zum Erstellen der Basiskonfiguration auf dem zweiten Server und verwenden Sie den Synchronisierungsregel-Editor, um PowerShell-Skripts zum Verschieben benutzerdefinierter Regeln zwischen Servern zu erstellen.

## Problembehandlung

**F: Wie erhalte ich Hilfe zu Azure AD Connect?**

[Durchsuchen der Microsoft Knowledge Base (KB)](https://www.microsoft.com/en-us/Search/result.aspx?q=azure%20active%20directory%20connect&form=mssupport)

- Suchen Sie in der Microsoft Knowledge Base (KB) nach technischen Lösungen für häufige Probleme im Zusammenhang mit der Unterstützung von Azure AD Connect.

[Microsoft Azure Active Directory-Foren](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)

- Sie durchsuchen und für technische Fragen und aus der Community Antworten oder eine eigene Frage stellen, indem Sie auf Suchen, können [hier](https://social.msdn.microsoft.com/Forums/azure/en-US/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).


[Azure AD Connect-Kundensupport](https://manage.windowsazure.com/?getsupport=true)

- Verwenden Sie diesen Link, um Support über das Azure-Portal zu erhalten.


