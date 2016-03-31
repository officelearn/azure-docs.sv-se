<properties
   pageTitle="Problembehandlung: Element "Active Directory" fehlt oder ist nicht verfügbar | Microsoft Azure "
   description="Vorgehensweise, wenn das Active Directory-Menüelement nicht im Azure-Verwaltungsportal angezeigt wird."
   services="active-directory"
   documentationCenter="na"
   authors="msmbaldwin"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="12/04/2015"
   ms.author="mbaldwin"/>

# Problembehandlung: Element "Active Directory" fehlt oder ist nicht verfügbar

Viele der Anweisungen für die Verwendung von Azure Active Directory-Features und Dienste beginnen mit "Azure-Verwaltungsportal und klicken Sie auf **Active Directory**." Aber was tun Sie, wenn die Active Directory-Erweiterung oder Menüelement nicht angezeigt wird oder wenn es markiert ist **nicht verfügbar**? Dieses Thema soll Ihnen helfen. Es beschreibt die Bedingungen, unter denen **Active Directory** nicht angezeigt oder ist nicht verfügbar, und mögliche Abhilfen.

## Active Directory fehlt

In der Regel ein **Active Directory** Element wird im linken Navigationsmenü angezeigt. Die Anweisungen in Azure Active Directory-Prozeduren setzen voraus, dass dieses Element in der Ansicht vorhanden ist.

![Screenshot: Active Directory in Azure](./media/active-directory-troubleshooting/typical-view.png)

Das Active Directory-Element wird im linken Navigationsmenü angezeigt, wenn eine der folgenden Bedingungen zutrifft. Andernfalls wird das Element nicht angezeigt.

* Der aktuelle Benutzer ist mit einem Microsoft-Konto (früher Windows Live ID) angemeldet.

    OR

* Der Azure-Mandant hat ein Verzeichnis, und das aktuelle Konto ist ein Verzeichnisadministrator.

    OR

* Der Azure-Mandant hat mindestens einen Azure AD Access Control-Namespace (ACS). Weitere Informationen finden Sie unter [Access Control-Namespace](https://msdn.microsoft.com/library/azure/gg185908.aspx).

    OR

* Der Azure-Mandant hat mindestens einen Azure Multi-Factor Authentication-Anbieter. Weitere Informationen finden Sie unter [Verwalten von Azure Multi-Factor Authentication-Anbietern](multi-factor-authentication-get-started-cloud.md/creating-an-azure-multi-factor-auth-provider).

Klicken Sie zum Erstellen eines Access Control-Namespace oder ein Multi-Factor Authentication-Anbieter auf **+ New** > **Anwendungsdienste** > **Active Directory**.

Um Administratorrechte für ein Verzeichnis zu erhalten, muss ein Administrator Ihrem Konto eine Administratorrolle zuweisen. Weitere Informationen finden Sie unter [Zuweisen von Administratorrollen](active-directory-assign-admin-roles.md).

## Active Directory ist nicht verfügbar

Beim Klicken auf **+ New** > **Anwendungsdienste**, einem **Active Directory** Element angezeigt wird. Konkret wird das Active Directory-Element angezeigt, wenn eine der Active Directory-Funktionen wie Verzeichnis, Access Control oder Multi-Factor Authentication-Anbieter für den aktuellen Benutzer verfügbar ist.

Während die Seite geladen wird, das Element ist abgeblendet und markiert ist **nicht verfügbar**. Dies ist ein vorübergehender Zustand. Wenn Sie ein paar Sekunden warten, ist das Element verfügbar. Wenn der Zustand andauert, löst häufig das Aktualisieren die Webseite das Problem.

![Screenshot: Active Directory ist nicht verfügbar](./media/active-directory-troubleshooting/not-available.png)


