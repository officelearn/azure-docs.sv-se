
<properties
    pageTitle="Verwenden einer Gruppe zum Verwalten des Zugriffs auf SaaS-Anwendungen | Microsoft Azure"
    description="Erfahren Sie, wie Sie in Azure Active Directory Premium oder Basic Gruppen verwenden, um den Zugriff auf SaaS-Anwendungen zuzuweisen, die in Azure Active Directory integriert sind."
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


# Verwenden einer Gruppe zum Verwalten des Zugriffs auf SaaS-Anwendungen

Mit Azure Active Directory (Azure AD) Premium können Sie Gruppen verwenden, um Zugriff auf eine SaaS-Anwendung zu gewähren, die in Azure AD integriert ist. Wenn Sie beispielsweise der Marketingabteilung Zugriff auf fünf verschiedene SaaS-Anwendungen erteilen möchten, können Sie eine Gruppe erstellen, die die Benutzer der Marketingabteilung enthält, und diese Gruppe dann diesen fünf SaaS-Anwendungen zuweisen, die von der Marketingabteilung benötigt werden. Auf diese Weise sparen Sie Zeit, da Sie die Mitgliedschaft für die Marketingabteilung zentral verwalten können. Benutzer werden der Anwendung zugewiesen, wenn sie als Mitglieder der Marketinggruppe hinzugefügt werden. Ebenso wird die Zuweisung der Benutzer zur Anwendung entfernt, wenn sie als Mitglieder aus der Marketinggruppe entfernt werden.

Diese Funktion kann für Hunderte von Anwendungen verwendet werden, die Sie aus dem Azure AD-Anwendungskatalog hinzufügen können.

**So weisen Sie einer Gruppe den Zugriff auf eine SaaS-Anwendung zu**

1. Öffnen Sie einen Browser Ihrer Wahl, und wechseln Sie zum Azure-Portal. Suchen Sie im Azure-Portal auf der Navigationsleiste links nach der Active Directory-Erweiterung. Unter dem **Verzeichnis** auf das Verzeichnis, in dem Sie Zugriff auf eine Saas-Anwendung für eine Gruppe zuweisen möchten.


2. Klicken Sie auf die **Applikationen** Registerkarte für Ihr Verzeichnis. Wählen Sie eine Anwendung, die Sie aus der Galerie hinzugefügt haben, klicken Sie auf die **Benutzer und Gruppen** Registerkarte.

3. Auf der **Benutzer und Gruppen** Registerkarte der **ab** Feld Geben Sie den Namen der Gruppe, der Sie Zugriff gewähren möchten, und klicken Sie auf das Häkchen in der oberen rechten Ecke. Sie müssen nur den ersten Teil des Gruppennamens eingeben. Anschließend klicken Sie auf die Gruppe, um sie zu markieren, und klicken Sie dann auf die **Zugriff zuweisen** Schaltfläche, und klicken Sie auf **Ja** Wenn Sie im Meldungsfeld zur Bestätigung angezeigt wird.


4. Sie können auch sehen, welche Benutzer der Anwendung entweder direkt oder durch die Mitgliedschaft in einer Gruppe zugewiesen sind. Zu diesem Zweck ändern Sie die **Anzeige der Dropdownliste von 'Gruppen'** zu **"Alle Benutzer"**. Die Liste zeigt die Benutzer im Verzeichnis und zeigt an, ob ein Benutzer der Anwendung zugewiesen ist oder nicht. Die Liste zeigt außerdem an, ob die Benutzer der Anwendung direkt (angezeigter Zuweisungstyp "Direkt") oder aufgrund der Gruppenmitgliedschaft (angezeigter Zuweisungstyp "Geerbt") zugewiesen sind.


> [AZURE.NOTE]
>Die Registerkarte "Benutzer und Gruppen" wird nur angezeigt, wenn Sie Azure AD Premium oder Azure AD Basic aktiviert haben.

Diese Artikel enthalten zusätzliche Informationen zu Azure Active Directory.

* [Verwalten des Zugriffs auf Ressourcen mit Azure Active Directory-Gruppen](active-directory-manage-groups.md)

* [Was ist Azure Active Directory?](active-directory-whatis.md)

* [Integrieren Ihrer lokalen Identitäten in Azure Active Directory](active-directory-aadconnect.md)


