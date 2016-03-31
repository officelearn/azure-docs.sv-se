<properties

    pageTitle="Managing security groups in Azure Active Directory | Microsoft Azure"
    description="How to create and manage security groups to manage Azure resource access using Azure Active Directory."
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


#Verwalten von Sicherheitsgruppen in Azure Active Directory

Eines der wichtigsten Features in Azure Active Directory (Azure AD) ist die Möglichkeit, den Zugriff auf Ressourcen zu verwalten. Diese Ressourcen können Teil des Verzeichnisses sein, wie im Fall von Berechtigungen zum Verwalten von Objekten mithilfe von Rollen im Verzeichnis, oder externe Ressourcen, z. B. SaaS-Anwendungen, Azure-Dienste und SharePoint-Websites oder lokale Ressourcen. Eine Gruppe kann einer Ressource durch den Besitzer der Ressource zugewiesen werden, wodurch den Mitgliedern dieser Gruppe Zugriff auf die Ressource gewährt wird. Die Mitgliedschaft der Gruppe kann dann vom Besitzer der Gruppe verwaltet werden. Der Besitzer der Ressource delegiert die Berechtigung, dieser Ressource Benutzer zuzuweisen, an den Besitzer der Gruppe.


##Erstellen und Verwalten einer Sicherheitsgruppe

**Erstellen einer Gruppe im Azure-Portal**

1. Klicken Sie in der Azure-Portal auf **Active Directory**, und klicken Sie dann auf den Namen des Verzeichnisses Ihrer Organisation.
2. Klicken Sie auf der **Gruppen** Registerkarte.
3. Klicken Sie auf der Seite Gruppen **Gruppe hinzufügen**.
4. In der **Gruppe hinzufügen** Fenster, geben Sie den Namen und die Beschreibung einer Gruppe.
5. Diese Aufgabe kann unter Verwendung des Office 365-Kontoportals, des Windows Intune-Kontoportals oder des Azure-Verwaltungsportals ausgeführt werden – je nachdem, welche Dienste Ihre Organisation abonniert hat. Weitere Informationen zur Verwendung von Portalen zum Verwalten Ihrer Azure Active Directory finden Sie unter [Verwalten Ihres Azure AD-Verzeichnisses](active-directory-administer).

## Zuweisen von Benutzern zu einer Sicherheitsgruppe oder Entfernen von Benutzern aus einer Sicherheitsgruppe

**Hinzufügen von Mitgliedern zu einer Gruppe im Azure-Portal**

1. Klicken Sie in der Azure-Portal auf **Active Directory**, und klicken Sie dann auf den Namen des Verzeichnisses Ihrer Organisation.
2. Klicken Sie auf der **Gruppen** Registerkarte.
3. Auf der **Gruppen** Seite, klicken Sie auf den Namen der Gruppe, der Sie Mitglieder hinzufügen möchten. Standardmäßig zeigt dies die **Mitglieder** Registerkarte der ausgewählten Gruppe.
4. Klicken Sie auf der Seite, auf **Mitglieder hinzufügen**.
5. Auf der **Mitglieder hinzufügen** Seite, klicken Sie auf den Namen des Benutzers oder einer Gruppe, die als ein Mitglied dieser Gruppe hinzugefügt, und stellen Sie sicher, dass dieser Name wird in den Bereich ausgewählte hinzugefügt werden sollen.


**Entfernen von Mitgliedern aus einer Gruppe im Azure-Portal**

1. Klicken Sie in der Azure-Portal auf **Active Directory**, und klicken Sie dann auf den Namen des Verzeichnisses Ihrer Organisation.
2. Klicken Sie auf der **Gruppen** Registerkarte.
3. Klicken Sie auf der Seite "Gruppen" auf den Namen der Gruppe, aus der Sie Mitglieder entfernen möchten.
4. Klicken Sie auf der Seite, auf die **Elemente** Registerkarte.
5. Klicken Sie auf der Seite, auf den Namen des Mitglieds, das Sie verwenden möchten, aus dieser Gruppe entfernen, und klicken Sie dann auf **Entfernen**.
6. Stellen Sie sicher, dass Sie durch Klicken auf diesen Member aus der Gruppe entfernen möchten **Ja** als Antwort auf die Aktion.


## Verwenden einer Regel zur dynamischen Verwaltung der Mitglieder einer Sicherheitsgruppe

**Zur Aktivierung der dynamischen Mitgliedschaft für eine bestimmte Gruppe führen Sie die folgenden Schritte aus:**

1. In Azure-Portal unter der **Gruppen** Registerkarte, wählen Sie die Gruppe, die Sie bearbeiten möchten, und klicken Sie dann in dieser Gruppe **konfigurieren** Legen Sie die **dynamische Mitgliedschaften aktivieren** Wechseln Sie zu **Ja**.
2. Jetzt können Sie eine einfache Regel für die Gruppe einrichten, um die Funktionsweise dynamischer Mitgliedschaften für diese Gruppe zu steuern. Stellen Sie sicher, dass die **Benutzer hinzufügen, in denen** Optionsfeld aktiviert ist, und wählen Sie dann eine Benutzereigenschaft aus dem Pulldownmenü (z. B. Abteilung, Position usw.),
3. Anschließend wählen Sie eine Bedingung (Not Equals, Equals, Not Starts With, Starts With, Not Contains, Contains, Not Match, Match) und geben zum Schluss den Wert für die ausgewählte Benutzereigenschaft an.

Beispiel: Wenn eine Gruppe einer SaaS-Anwendung zugewiesen ist (weitere Informationen hierzu finden Sie unter "Zuweisen des Zugriffs auf eine SaaS-Anwendung für eine Gruppe in Azure AD") und Sie die dynamische Mitgliedschaft für diese Gruppe aktivieren, indem Sie eine Regel einrichten, in der"Benutzer hinzufügen, wobei" auf die Position festgelegt ist, für die "Equals(-eq)Sales Rep" gilt, erhalten alle Benutzer in Ihrem Azure AD-Verzeichnis Zugriff auf diese SaaS-Anwendung, deren jobTitle-Eigenschaft auf "Sales Rep" festgelegt ist.

## Zusätzliche Informationen

Diese Artikel enthalten zusätzliche Informationen zu Azure Active Directory.

* [Verwalten des Zugriffs auf Ressourcen mit Azure Active Directory-Gruppen](active-directory-manage-groups.md)

* [Was ist Azure Active Directory?](active-directory-whatis.md)

* [Integrieren Ihrer lokalen Identitäten in Azure Active Directory](active-directory-aadconnect.md)


