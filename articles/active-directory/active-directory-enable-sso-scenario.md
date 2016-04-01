<properties
   pageTitle="Verwalten von Anwendungen mit Azure Active Directory | Microsoft Azure"
   description="Dieser Artikel erläutert die Vorteile der Integration von Azure Active Directory in lokale, Cloud- und SaaS-Anwendungen."
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

# Verwalten von Anwendungen mit Azure Active Directory (AD)

## Übersicht

Abgesehen vom eigentlichen Workflow oder Inhalt gibt es für Unternehmen zwei grundlegende Anforderungen für alle Anwendungen:

1. Für eine gesteigerte Produktivität sollte es einfach sein, Anwendungen zu ermitteln und darauf zuzugreifen.

2. Um Sicherheit und Governance zu ermöglichen, benötigt die Organisation die Kontrolle und einen Überblick darüber, wer auf die einzelnen Anwendungen zugreifen kann und diese Möglichkeit auch nutzt.

Das Wort von Cloudanwendungen Dies kann am besten erreicht werden mit Identität steuern "*wer was tun*".

In Computerterminologie:

- *Die* bezeichnet man als *Identität* -einen Datenspeicher, die von Benutzern und Gruppen besteht.

- *Was* bezeichnet man als *zugriffsverwaltung* – Verwaltung des Zugriffs auf geschützte Ressourcen

Beide Komponenten zusammen bezeichnet man als *Identity and Access Management (IAM)*, definiert durch die [Gartner](http://www.gartner.com/it-glossary/identity-and-access-management-iam) Gruppe als "*die Sicherheit nachlässig, das die richtigen Personen Zugriff auf die richtigen Ressourcen auf der rechten Seite ermöglicht Zeiten für den richtigen Gründen*".

Wo liegt also das Problem? Wenn die IAM- *nicht verwaltete* an einer Stelle mit einer integrierten Lösung:

- Identitätsadministratoren müssen die einzelnen Benutzerkonten in allen Anwendungen separat erstellen und aktualisieren – eine redundante und zeitaufwändig Aktivität.

- Benutzer müssen sich für den Zugriff auf die Anwendungen, die sie für ihre Arbeit benötigen, mehrere Anmeldeinformationen merken, da sie insbesondere dazu aufgefordert werden, aus Sicherheitsgründen nicht dasselbe Kennwort für jede Anwendung zu verwenden. Demzufolge neigen Benutzer dazu, ihre Kennwörter aufzuschreiben oder andere Lösungen zur Kennwortverwaltung zu verwenden, was zu weiteren Risiken für die Datensicherheit führt.

- Durch redundante, zeitaufwändige Aktivitäten verringert sich der Zeitraum, in dem Benutzer und Administratoren sich den Geschäftsaktivitäten widmen, die das Betriebsergebnis Ihres Unternehmens erhöhen.

Was hält Organisationen im Allgemeinen davon ab, integrierte IAM-Lösungen einzuführen?

- Die technischsten Lösungen basieren auf Softwareplattformen, die bereitgestellt und von jeder Organisation für ihre eigenen Anwendungen angepasst werden müssen.

- Cloudanwendungen werden häufig schneller eingeführt als die IT-Organisation vorhandene IAM-Lösungen integrieren kann.

- Tools für Sicherheit und Überwachung erfordern eine zusätzliche Anpassung und Integration, um umfassende E2E-Szenarien zu erzielen.

## Azure Active Directory-Integration in Anwendungen

Azure Active Directory (AD) ist Microsofts umfassende Identität (IDaaS) eine Lösung für einen IAM als Cloud-Dienst aktivieren und über die Access-Verwaltung für einmaliges Anmelden (SSO) und reporting erstellt [vorinstalliert mit Tausenden von Clientanwendungen](https://azure.microsoft.com/marketplace/active-directory/), einschließlich Salesforce, Google Apps, Box, Concur und vieles mehr. Mit Azure AD gelten für Anwendungen, die Sie für Ihre Partner und Kunden (Business oder Consumer) veröffentlichen, dieselben Identitäts- und Zugriffsverwaltungsfunktionen, sodass Sie sich auf Ihr Hauptgeschäft konzentrieren können.

Der Nutzen von Azure AD geht über Cloudanwendungen hinaus. Sie können es auch mit lokalen Anwendungen einsetzen, indem Sie sicheren Remotezugriff bereitstellen. Somit sind VPNs oder andere herkömmliche Systeme zur Remotezugriffsverwaltung nicht erforderlich.

Was geschieht, wenn Sie eine Anwendung implementieren müssen, die im Anwendungskatalog noch nicht aufgeführt ist? Auch wenn es etwas zeitaufwändiger ist als das Konfigurieren von SSO für Anwendungen über den Katalog, bietet Azure AD einen Assistenten, der Sie bei der Konfiguration unterstützt.

Durch die Bereitstellung einer zentralen Zugriffsverwaltung und der einmaligen Anmeldung (SSO) für all Ihre Anwendungen bietet Azure AD die Lösung für Probleme mit der Datensicherheit und Produktivität.

- Benutzer können mit einer einzigen Anmeldung auf mehrere Anwendungen zugreifen und mehr Zeit auf Verdienstmöglichkeiten oder Geschäftsvorgänge verwenden.

- Identitätsadministratoren können den Zugriff auf Anwendungen an zentraler Stelle verwalten.

Der Vorteil für den Benutzer und für Ihr Unternehmen liegt auf der Hand. Werfen wir einen Blick auf die Vorteile für einen Identitätsadministrator und die Organisation.

## Vorteile der integrierten Anwendung

Der SSO-Vorgang besteht aus zwei Schritten:

- Authentifizierung: der Prozess zur Überprüfung der Benutzeridentität.

- Autorisierung: die Entscheidung, den Zugriff auf eine Ressource durch eine Zugriffsrichtlinie zu ermöglichen oder zu sperren.

Bei Verwendung von Azure AD zum Verwalten von Anwendungen und zum Aktivieren der einmaligen Anmeldung:

- Die Authentifizierung erfolgt über das lokale (z. B. AD) oder das Azure AD-Konto des Benutzers.

- Die Autorisierung wird anhand der Azure AD-Zuweisungs- und Schutzrichtlinie ausgeführt und gewährleistet eine konsistente Endbenutzeroberfläche. Sie ermöglicht Ihnen das Hinzufügen von Zuweisungs-, Standort- und MFA-Bedingungen für beliebige Anwendungen, unabhängig von deren internen Funktionen.

Es wichtig zu verstehen, dass die Anwendung der Autorisierung auf die Zielanwendung davon abhängt, wie die Anwendung in Azure AD integriert wurde.

- **Integrierte Anwendungen vorab-Anbieter** wie Office 365 und Azure, sind diese Anwendungen direkt auf Azure AD erstellt und sich darauf verlassen, für die umfassende Funktionen zur Identitäts- und Zugriff. Der Zugriff auf diese Anwendungen wird über Verzeichnisinformationen und die Ausstellung von Token aktiviert.

- **Bereits integrierte Anwendungen von Microsoft und andere Programme** Hierbei handelt es sich um unabhängige Cloudanwendungen, die auf einem internen Anwendungsverzeichnis und können unabhängig von Azure AD ausgeführt werden. Der Zugriff auf diese Anwendungen wird durch das Ausgeben anwendungsspezifischer Anmeldeinformationen ermöglicht, die einem Anwendungskonto zugeordnet werden. Abhängig von den Anwendungsfunktionen handelt es sich bei den Anmeldeinformationen um ein Verbundtoken oder um einen Benutzernamen und ein Kennwort für ein Konto, das zuvor in der Anwendung bereitgestellt wurde.

- **Lokale Anwendungen** Applications über das Aktivieren des Zugriffs auf lokale Anwendungen in erster Linie Azure AD-Anwendungsproxy veröffentlicht. Diese Anwendungen basieren auf einem zentralen lokalen Verzeichnis wie Windows Server Active Directory. Der Zugriff auf diese Anwendungen wird durch Auslösen des Proxys ermöglicht, um den Anwendungsinhalt für den Endbenutzer bereitzustellen und dabei die lokale Anmeldungsanforderung zu berücksichtigen.

Wenn ein Benutzer Ihrer Organisation beitritt, müssen Sie z. B. für die primären Anmeldevorgänge ein Konto für den Benutzer in Azure AD erstellen. Wenn dieser Benutzer den Zugriff auf eine verwaltete Anwendung wie Salesforce benötigt, müssen Sie auch ein Konto für diesen Benutzer in Salesforce erstellen und mit dem Azure-Konto verknüpfen, damit SSO funktioniert. Wenn der Benutzer die Organisation verlässt, ist es ratsam, das Azure AD-Konto und alle entsprechenden Konten in den IAM-Speichern der Anwendungen zu löschen, auf die der Benutzer zugreifen konnte.

## Zugriffserkennung

In modernen Unternehmen sind die IT-Abteilungen häufig nicht über alle verwendeten Cloudanwendungen unterrichtet. Mit Cloud App Discovery bietet Azure AD Ihnen auch eine Lösung zum Erkennen dieser Anwendungen.

## Kontenverwaltung

Ursprünglich ist das Verwalten von Konten in den verschiedenen Anwendungen ein manueller Prozess, der von IT- oder Supportpersonal in der Organisation durchgeführt wird. Azure AD hat die Kontenverwaltung vollständig über alle von Dienstanbietern integrierten Anwendungen und über solche vorinstallierten Anwendungen von Microsoft hinweg automatisiert, die eine automatische Benutzerbereitstellung oder SAML JIT unterstützen.

## Automatisierte Benutzerbereitstellung

Einige Anwendungen bieten Automatisierungsschnittstellen für die Erstellung und Entfernung (oder Deaktivierung) von Konten. Wenn ein Anbieter eine solche Schnittstelle anbietet, wird sie von Azure AD genutzt. Dadurch werden die Betriebskosten reduziert, da Verwaltungsaufgaben automatisch ausgeführt werden, und die Sicherheit Ihrer Umgebung verbessert sich, da das Risiko eines unbefugten Zugriffs sinkt.

## Zugriffsverwaltung

Mithilfe von Azure AD können Sie den Zugriff auf Anwendungen mit individuellen oder regelgesteuerten Zuweisungen verwalten. Sie können die Zugriffsverwaltung auch an die richtigen Personen in der Organisation delegieren, um eine optimale Aufsicht sicherzustellen und die Belastung des Helpdesks zu reduzieren.

## Lokale Anwendungen

Mit dem integrierten Anwendungsproxy können Sie lokale Anwendungen für Ihre Benutzer veröffentlichen, um sowohl von einer konsistenten Zugriffsoberfläche mit moderner Cloudanwendung als auch von den Vorteilen der Azure AD-Überwachungs-, Berichterstellungs- und Sicherheitsfunktionen zu profitieren.

## Berichterstellung und Überwachung

Azure AD bietet integrierte Berichterstellungs- und Überwachungsfunktionen, mit denen Sie feststellen können, wer auf Anwendungen zugreifen kann und wer sie verwendet hat.

## Zugehörige Funktionen

Mit Azure AD können Sie Ihre Anwendungen mit fein abgestimmten Zugriffsrichtlinien und vorinstallierter MFA sichern. Weitere Informationen zu Azure MFA finden Sie unter [Azure MFA](https://azure.microsoft.com/services/multi-factor-authentication/).

## Erste Schritte

Zum Einstieg Integrieren von Anwendungen in Azure AD, sehen Sie sich die [Integration von Azure Active Directory mit erste Schritte mit](active-directory-integrating-applications-getting-started.md).


