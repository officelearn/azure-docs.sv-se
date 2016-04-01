<properties
    pageTitle="Dedizierte Gruppen in Azure Active Directory | Microsoft Azure"
    description="Übersicht über die Funktionsweise dedizierter Gruppen in Azure Active Directory und ihrer Erstellung."
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

# Dedizierte Gruppen in Azure Active Directory

In Azure Active Directory werden dedizierte Gruppen automatisch erstellt, und die Gruppenmitgliedschaft für die dedizierten Gruppen erfolgt ebenfalls automatisch. Sie können keine Mitglieder zu dedizierten Gruppen hinzufügen oder daraus entfernen, weder über das Azure-Portal noch mit Windows PowerShell-Cmdlets oder programmgesteuert. Um dedizierte Gruppen aktivieren möchten, In der Azure-Portal auf der Registerkarte Konfigurieren legen die **Wechseln Sie dedizierte Gruppen aktivieren auf Ja**.

Sobald der dedizierte Gruppen aktivieren-Schalter, um festgelegt ist **Ja**, weiter können Sie das Verzeichnis erstellen Sie dedizierte Gruppe alle Benutzer automatisch durch Festlegen der **"Alle Benutzer" aktivieren Gruppe** Wechseln Sie zu **Ja**. Sie können dann auch den Namen dieser dedizierten Gruppe durch Eingabe im Bearbeiten der **Anzeigename für "Alle Benutzer" Gruppe** Feld.

Die dedizierte Gruppe "Alle Benutzer" ist hilfreich, wenn Sie allen Benutzer in Ihrem Verzeichnis die gleichen Berechtigungen zuweisen möchten. Beispielsweise können Sie allen Benutzern in Ihrem Verzeichnis Zugriff auf eine SaaS-Anwendung gewähren, indem Sie der dedizierten Gruppe "Alle Benutzer" Zugriff auf diese Anwendung zuweisen.

Diese Artikel enthalten zusätzliche Informationen zu Azure Active Directory.

* [Verwalten des Zugriffs auf Ressourcen mit Azure Active Directory-Gruppen](active-directory-manage-groups.md)
* [Was ist Azure Active Directory?](active-directory-whatis.md)
* [Integrieren Ihrer lokalen Identitäten in Azure Active Directory](active-directory-aadconnect.md)


