<properties
   pageTitle="Der Sicherheits-Assistent von Azure Privileged Identity Management"
   description="Bei der ersten Verwendung der Erweiterung Azure Privileged Identity Management wird ein Sicherheits-Assistent angezeigt. Dieser Artikel beschreibt die Schritte zur Verwendung des Assistenten."
   services="active-directory"
   documentationCenter=""
   authors="IHenkel"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/31/2015"
   ms.author="inhenk"/>

# Der Sicherheits-Assistent von Azure Privileged Identity Management

Beim ersten Ausführen von Azure Privileged Identity Management wird ein Assistent angezeigt. Der Assistent bietet Ihnen Einblick in die Sicherheitsrisiken privilegierter Identitäten und hilft Ihnen, diese Risiken mithilfe von Privileged Identity Management zu reduzieren.

Es gibt drei Abschnitte, um zu überprüfen, **IHRE Administratoren möglicherweise versetzen SIE Risiko, ANGRIFFSFLÄCHE IHRE Administratoren verwalten**, und **temporäre ADMIN-EINSTELLUNGEN definieren**. Jeder Abschnitt bietet einen Überblick über die Konzepte und eine Erläuterung von durchzuführenden Maßnahmen.

Alle globalen Administratoren sind zunächst permanente Administratoren. Klicken auf **IHRE Administratoren möglicherweise versetzen SIE Risiko**, eine Liste der globalen Administratorrollen und wie viele von ihnen derzeit angezeigt.

Durch Klicken auf **Verwalten IHRE Administratoren ANGRIFFSFLÄCHE**, erhalten Sie Gelegenheit zum Ändern von Administratoren auf temporären, lassen sie die dauerhafte oder vollständig aus der Rolle entfernt wird.

**TEMPORÄRE ADMINISTRATOREINSTELLUNGEN definieren** können Sie bestimmen, wie lange ein temporärer Administrator berechtigt, Aktivieren von Benachrichtigungen und mehrstufige Authentifizierung erforderlich.

## Ändern globaler Administratorrollen in temporäre oder permanente Rollen

Ihnen stehen drei Optionen zum Ändern des Zeitfensters eines globalen Administrators zur Verfügung.

1.  Klicken Sie auf die **stellen alle temporären** Schaltfläche, um alle globalen Administratoren temporäre vornehmen.

2.  Klicken Sie auf die **stellen alle permanenten** Schaltfläche, um alle globalen Administratoren permanent zu machen.

3.  Wählen Sie **permanent beibehalten**, **Temp stellen**, oder **aus Rolle entfernen** für jeden globalen Administrator.

4.  Klicken Sie auf **OK**.

5.  Klicken Sie auf **übermitteln**.

## Ändern des Aktivierungszeitraums für eine globale Administratorrolle

1.  Verschieben der **Aktivierungszeitraum** Schieberegler nach links oder rechts vergrößern oder Verkleinern des Aktivierungszeitraums. Der Aktivierungszeitraum kann bis zu 72 Stunden betragen.

2.  Geben Sie die Anzahl der Stunden in der **Stunden** Feld rechts neben dem Schieberegler.

## Aktivieren von Benachrichtigungen

Damit Administratoren e-Mail-Nachrichten empfangen können, wenn Rollen active vorgenommen werden, aktivieren Sie Benachrichtigungen, indem Sie auf die **aktivieren** Schaltfläche. Sie können diese Funktion auch deaktivieren.

## Erfordern von Multi-Factor Authentication

Wenn Sie Administratoren erforderlich, um MFA zu ihren Konten anmelden und zum Anfordern einer Erweiterungs ihrer Rolle verwenden möchten, aktivieren Sie MFA, indem Sie auf die **aktivieren** Schaltfläche. Sie können diese Funktion auch deaktivieren.

Klicken Sie hier, um weitere Informationen zu MFA und PIM zu erhalten. PLACEHOLDER: NEED LINK TO MFA DOC.

Wählen Sie die Rollen aus, auf die diese Einstellungen angewendet werden. Klicken Sie auf **OK**.

> [AZURE.WARNING] Wenn ist wichtig, zu diesem Zeitpunkt haben mehr als ein Sicherheitsadministrator, da Wenn, einen Sicherheitsadministrator nicht auf permanente festgelegt ist und die rollenzuweisung abläuft und Sie verfügen nicht über die MFA für diesen Benutzer einrichten, der Benutzer nicht wird in der Lage PIM überhaupt zu verwalten.

Klicken Sie auf die **OK** Schaltfläche. wenn Sie die Änderungen abgeschlossen haben.

Nachdem Sie Änderungen vorgenommen haben, wird der Assistent nicht mehr angezeigt. Sie können ihn jedoch erneut aufrufen, indem Sie unter "Identitäten verwalten" auf die Schaltfläche "Assistent" klicken.

## Nächste Schritte
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]


