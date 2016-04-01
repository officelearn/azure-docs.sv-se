<properties
    pageTitle="Einrichten von Azure Active Directory zur Self-Service-Verwaltung des Anwendungszugriffs | Microsoft Azure"
    description="Übersicht der Self-Service-Gruppenverwaltung, die Benutzern das Erstellen und Verwalten von Sicherheitsgruppen in Microsoft Azure Active Directory ermöglicht und die Möglichkeit bietet, die Mitgliedschaft in Sicherheitsgruppen anzufordern."
    services="active-directory"
    documentationCenter=""
  authors="curtand"
    manager="stevenpo"
    editor=""
    />

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/17/2015"
    ms.author="curtand"/>

# Einrichten von Azure Active Directory zur Self-Service-Verwaltung des Anwendungszugriffs

Die Self-Service-Gruppenverwaltung ermöglicht Benutzern das Erstellen und Verwalten von Sicherheitsgruppen in Microsoft Azure Active Directory (Azure AD) und bietet die Möglichkeit, die Mitgliedschaft in Sicherheitsgruppen anzufordern, die anschließend vom Besitzer der Gruppe genehmigt oder abgelehnt werden kann. Mithilfe der Funktionen für die Self-Service-Gruppenverwaltung kann die tagtägliche Steuerung der Gruppenmitgliedschaft an Personen delegiert werden, die den Geschäftskontext für die betreffende Mitgliedschaft verstehen. Self-Service-Funktionen zur Gruppenverwaltung sind nur für Sicherheitsgruppen und Office 365-Gruppen, und nicht für E-Mail-aktivierte Sicherheitsgruppen oder Verteilerlisten verfügbar.

Die Self-Service-Gruppenverwaltung unterstützt zurzeit zwei grundlegende Szenarien: delegierte Gruppenverwaltung und Self-Service-Gruppenverwaltung.


- **Delegierte gruppenverwaltung** -Beispiel ist ein Administrator, der Zugriff auf eine SaaS-Anwendung verwaltet, die ihr Unternehmen verwendet wird. Die Verwaltung dieser Zugriffsrechte wird immer mühsamer, und der Administrator bittet den Geschäftsinhaber, eine neue Gruppe zu erstellen. Der Administrator weist den Zugriff auf die Anwendung einer neuen Gruppe zu, die der Geschäftsinhaber erstellt hat, und fügt dieser Gruppe alle Personen hinzu, die gegenwärtig Zugriff auf die Anwendung haben. Der Geschäftsinhaber kann dann weitere Benutzer hinzufügen, und die Benutzer erhalten wenige Augenblicke später automatisch zugriff auf die Anwendung. Der Geschäftsinhaber muss nicht warten, bis der Administrator diese Aufgabe erledigt, sondern kann den Zugriff für seine Benutzer selbst verwalten. Der Administrator kann den gleichen Prozess für einen Verwaltungsmitarbeiter einer anderen Geschäftsgruppe ausführen. Sowohl der Geschäftsinhaber als auch der Verwaltungsmitarbeiter können jetzt den Zugriff für ihre Benutzer verwalten, ohne dass sie die Benutzer des jeweils anderen anzeigen oder bearbeiten können. Der Administrator kann weiterhin alle Benutzer anzeigen, die auf die Anwendung zugreifen können, und kann die Zugriffsrechte bei Bedarf blockieren.


- **Self-service-gruppenverwaltung** – ein Beispiel hierfür wäre zwei Benutzer, die beide SharePoint Online-Websites verfügen, die unabhängig voneinander eingerichtet, aber Zugriff teams, die eigentlich gewähren des jeweils anderen erleichtern möchten. Sie erstellen eine Gruppe in Azure AD, und in SharePoint Online wählt jeder der beiden Benutzer diese Gruppe, um Zugriff auf ihre Websites bereitzustellen. Wenn ein weiterer Benutzer Zugriff benötigt, fordert er diesen einfach über den Zugriffsbereich an und erhält nach Genehmigung automatisch Zugriff auf beide SharePoint Online-Websites. Später entscheidet einer der Hauptbenutzer, dass alle Personen, die auf seine Website zugreifen, auch Zugriff auf eine bestimmte SaaS-Anwendung erhalten sollen. Er bittet den Administrator dieser SaaS-Anwendung, Zugriffsrechte für diese Anwendung auf seiner Website hinzuzufügen. Anschließend können alle Personen, deren Zugriff genehmigt wird, auf die beiden SharePoint Online-Websites und diese SaaS-Anwendung zugreifen.

## Einrichten einer Gruppe für Self-Service durch Endbenutzer

In Azure-Portal auf der **konfigurieren** Legen Sie die **delegierte gruppenverwaltung** Wechseln zu aktiviert, und legen Sie dann die **Benutzer können Gruppen erstellen** aktiviert.

Wenn die **Benutzer können Gruppen erstellen** Schalter wird festgelegt, um **aktiviert**, dürfen alle Benutzer in Ihrem Verzeichnis neue Sicherheitsgruppen erstellen und diesen Gruppen Mitglieder hinzuzufügen. Diese neuen Gruppen werden auch im Zugriffsbereich für alle anderen Benutzer angezeigt und andere Benutzer können Beitrittsanfragen für diese Gruppen erstellen, wenn die Richtlinieneinstellung für die Gruppe dies zulässt. Wenn diese Option auf "Deaktiviert" festgelegt ist, können Benutzer keine Gruppen erstellen und vorhandene Gruppen, deren Besitzer sie sind, nicht ändern. Sie können jedoch weiterhin die Mitgliedschaften in diesen Gruppen verwalten und Anforderungen anderer Benutzer zum Beitritt zu ihren Gruppen genehmigen.

Können Sie auch die **Benutzer, die Self-Service für Sicherheitsgruppen verwenden können** Switch, um eine differenziertere Steuerung des Zugriffs auf das Self-Service-Management-Funktionen für Ihre Benutzer zu erreichen. Wenn die **Benutzer können Gruppen erstellen** Switch auf aktiviert festgelegt ist, dürfen alle Benutzer in Ihrem Verzeichnis neue Sicherheitsgruppen erstellen und diesen Gruppen Mitglieder hinzuzufügen. Durch Festlegen der **Benutzer, die Self-Service für Sicherheitsgruppen verwenden können** Wechseln zu einigen werden einschränken Verwaltung von Sicherheitsgruppen auf nur eine eingeschränkte Gruppe von Benutzern. Wenn diese Option auf "Einige" festgelegt ist, wird eine Gruppe namens "SSGMSecurityGroupsUsers" in Ihrem Verzeichnis erstellt, und nur diejenigen Benutzer, die Sie als Mitglieder dieser Gruppe hinzugefügt haben, können in Ihrem Verzeichnis neue Sicherheitsgruppen erstellen und diesen Mitglieder hinzufügen. Durch Festlegen der **Benutzer, die Self-Service für Sicherheitsgruppen verwenden können** Wechseln Sie für alle, können Sie alle Benutzer in Ihrem Verzeichnis neue Sicherheitsgruppen erstellen.

Sie können auch die **Gruppe, die Self-Service für Sicherheitsgruppen verwenden kann** Feld (standardmäßig auf "SSGMSecurityGroupsUsers" auf Ihren eigenen benutzerdefinierten Namen für eine Gruppe angeben, die alle Benutzer mit der Option zur Verwendung von Self-Service- und in Ihrem Verzeichnis neue Sicherheitsgruppen erstellen.

## Zusätzliche Informationen

Diese Artikel enthalten zusätzliche Informationen zu Azure Active Directory.

* [Verwalten des Zugriffs auf Ressourcen mit Azure Active Directory-Gruppen](active-directory-manage-groups.md)

* [Was ist Azure Active Directory?](active-directory-whatis.md)

* [Integrieren Ihrer lokalen Identitäten in Azure Active Directory](active-directory-aadconnect.md)


