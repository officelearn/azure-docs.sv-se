<properties
    pageTitle="Erstellen einer einfachen Regel zum Konfigurieren von dynamischen Mitgliedschaften für eine Gruppe | Microsoft Azure"
    description="Erklärt, wie Sie eine einfache Regel zum Konfigurieren von dynamischer Mitgliedschaften für eine Gruppe erstellen."
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
    ms.date="11/17/2015"
    ms.author="curtand"/>


# Erstellen einer einfachen Regel zum Konfigurieren von dynamischen Mitgliedschaften für eine Gruppe

Zur Aktivierung der dynamischen Mitgliedschaft für eine bestimmte Gruppe führen Sie die folgenden Schritte aus:

1. In Azure-Portal unter der **Gruppen** Registerkarte, wählen Sie die Gruppe, die Sie bearbeiten möchten, und klicken Sie dann in dieser Gruppe **konfigurieren** Legen Sie die **dynamische Mitgliedschaften aktivieren** Wechseln Sie zu **Ja**.

2. Jetzt können Sie eine einfache Regel für die Gruppe einrichten, um die Funktionsweise dynamischer Mitgliedschaften für diese Gruppe zu steuern. Stellen Sie sicher, dass die **Benutzer hinzufügen, in denen** Option ausgewählt ist, und wählen Sie dann eine Benutzereigenschaft aus der Liste (z. B. Abteilung, Position usw.),

3. Anschließend wählen Sie eine Bedingung (Not Equals, Equals, Not Starts With, Starts With, Not Contains, Contains, Not Match, Match) und geben zum Schluss den Wert für die ausgewählte Benutzereigenschaft an. Angenommen, eine Gruppe einer SaaS-Anwendung zugewiesen wird und Sie dynamische Mitgliedschaften für diese Gruppe aktivieren, indem Sie eine Regel festlegen, bei **Benutzer hinzufügen, in dem** wird die jobtitle festgelegt, dass Vertriebsmitarbeiter Equals(-eq), Zugriff auf diese SaaS-Anwendung alle Benutzer innerhalb Ihres Azure AD-Verzeichnisses, deren Titel Vertriebsmitarbeiter festgelegt, haben.

Diese Artikel enthalten zusätzliche Informationen zu Azure Active Directory.

* [Verwalten des Zugriffs auf Ressourcen mit Azure Active Directory-Gruppen](active-directory-manage-groups.md)
* [Was ist Azure Active Directory?](active-directory-whatis.md)
* [Integrieren Ihrer lokalen Identitäten in Azure Active Directory](active-directory-aadconnect.md)


