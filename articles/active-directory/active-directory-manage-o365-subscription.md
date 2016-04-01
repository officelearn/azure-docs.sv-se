<properties
   pageTitle="Verwalten des Verzeichnisses für Ihr Office 365-Abonnement in Azure | Microsoft Azure"
   description="Verwalten eines Office 365-Abonnement-Kontoverzeichnisses mithilfe von Azure Active Directory und dem klassischen Azure-Portal"
   services="active-directory"
   documentationCenter=""
   authors="curtand"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="12/01/2015"
   ms.author="curtand"/>

#Verwalten des Verzeichnisses für Ihr Office 365-Abonnement in Azure

Dieser Artikel beschreibt, wie ein Verzeichnis verwaltet wird, das für ein Office 365-Abonnement im klassischen Azure-Portal erstellt wurde. Die Schritte zum Ausführen dieser Aufgabe variieren, je nachdem, ob Sie bereits über ein Azure-Abonnement verfügen. Sie müssen der Dienstadministrator oder Co-Administrator eines Azure-Abonnements sein, um sich am klassischen Azure-Portal anzumelden.

Wenn Sie noch nicht über ein Azure-Abonnement verfügen, müssen Sie sich lediglich mit Ihrem Geschäfts- oder Schulkonto anmelden, mit dem Sie sich auch bei Office 365 anmelden.

![](./media/active-directory-manage-o365-subscription/AAD_O365_01.png)

Ein entsprechendes Abonnement für Azure wird nicht gefunden werden, Sie können jedoch auf **für Azure registrieren** und relevante Informationen aus dem Office 365-Konto in das Anmeldeformular bereits eingetragen. Das gleiche Konto wird standardmäßig der Rolle des Dienstadministrators zugewiesen.

![](./media/active-directory-manage-o365-subscription/AAD_O365_02.png)

Nach Abschluss des Azure-Abonnements können Sie sich beim klassischen Azure-Portal anmelden und auf Azure-Dienste zugreifen. Klicken Sie auf die Active Directory-Erweiterung, um das gleiche Verzeichnis zu verwalten, das Ihre Office 365-Benutzer authentifiziert.

Wenn Sie bereits über ein Azure-Abonnement verfügen, ist der Prozess zum Verwalten eines weiteren Verzeichnisses ebenfalls ganz einfach. Das folgende Diagramm veranschaulicht diesen Prozess.

In diesem Beispiel hat Michael Smith ein Office 365-Abonnement für "contoso.com". Er verfügt über ein Azure-Abonnement, das er mit seinem Microsoft-Konto für signiert msmith@hotmail.com. In diesem Fall verwaltet er zwei Verzeichnisse.

|  Abonnement |  Office 365  |  Azure |
|  -------------- | ------------- | ------------------------------- |
|  Anzeigename |  Contoso  |     Standardverzeichnis |
|  Domänenname  |  contoso.com  | msmithhotmail.onmicrosoft.com |

Er möchte die Benutzeridentitäten im Verzeichnis "Contoso" verwalten, während er mit seinem Microsoft-Konto bei Azure angemeldet ist, damit er Azure AD-Funktionen wie die mehrstufige Authentifizierung aktivieren kann.

![](./media/active-directory-manage-o365-subscription/AAD_O365_03.png)

In diesem Fall sind die beiden Verzeichnisse unabhängig voneinander.

##Zwei unabhängige Verzeichnisse verwalten
In der Reihenfolge für Michael Smith beide Verzeichnisse verwalten, während er in Azure als msmith@hotmail.com angemeldet ist muss er die folgenden Schritte ausführen:

> [AZURE.NOTE]
> Diese Schritte können nur ausgeführt werden, wenn ein Benutzer mit einem Microsoft-Konto angemeldet ist. Wenn der Benutzer, sich mit eines Geschäfts- oder schulkonto, ist die Option angemeldet ist **vorhandenes Verzeichnis** ist nicht verfügbar, da ein Geschäfts- oder schulkonto nur durch sein Basisverzeichnis authentifiziert werden können (d. h. das Verzeichnis, in dem das Geschäfts- oder schulkonto gespeichert wird, und das im Besitz der Organisation oder Schule).

1.  Melden Sie sich beim klassischen Azure-Portal als msmith@hotmail.com an.
2.  Klicken Sie auf **Neu** > **Anwendungsdienste** > **Active Directory** > **Verzeichnis** > **Erstellen Sie benutzerdefinierte**.
3.  Klicken Sie auf vorhandene und wählen die **bin ich nun die Abmeldung bereit** Kontrollkästchen.
4.  Melden Sie sich beim klassischen Azure-Portal als globaler Administrator von Contoso.onmicrosoft.com (z. B. msmith@contoso.com) an.
5.  Bei der Aufforderung zum **Verzeichnis "Contoso" mit Azure verwenden?**, klicken Sie auf **Weiter**.
6.  Klicken Sie auf **jetzt abmelden**.
7.  Melden Sie sich beim klassischen Azure-Portal als msmith@hotmail.com an. Das Verzeichnis "Contoso" und das Standardverzeichnis werden in der Active Directory-Erweiterung angezeigt.

Nach Abschluss dieser Schritte ist msmith@hotmail.com globaler Administrator im Verzeichnis "Contoso".

##Verwalten von Ressourcen als globaler Administrator
Nun nehmen wir an, dass John Doe muss zum klassischen Azure-Portal anmelden und Verwalten von Websites und -Ressourcen, die mit der Azure-Abonnement von msmith@hotmail.com verknüpft sind. Zu diesem Zweck muss Michael Smith folgende zusätzliche Schritte ausführen:

1.  Melden Sie sich beim klassischen Azure-Portal, mithilfe des Dienstadministratorkontos des Azure-Abonnements (in diesem Beispiel msmith@hotmail.com).
2.  Übertragen Sie das Abonnement im Verzeichnis Contoso: Klicken Sie auf **Einstellungen** > **Abonnements** > Wählen Sie das Abonnement > **Verzeichnis bearbeiten** > Wählen Sie **Contoso (Contoso.com)**. Alle Geschäfts- oder Schulonten, die Co-Administratoren des Abonnements sind, werden als Teil der Übertragung entfernt.
3.  John Doe als Co-Administrator des Abonnements hinzufügen: Klicken Sie auf **Einstellungen** > **Administratoren** > Wählen Sie das Abonnement > **Hinzufügen** > Typ **JohnDoe@Contoso.com**.

##Nächste Schritte
Weitere Informationen über die Beziehung zwischen Abonnements und Verzeichnissen finden Sie unter [wie ein Abonnement einem Verzeichnis zugeordnet ist](active-directory-how-subscriptions-associated-directory.md).


