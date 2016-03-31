<properties 
    pageTitle="Einrichten von Azure AD Join für Ihre Benutzer| Microsoft Azure" 
    description="Hier wird erklärt, wie Administratoren Azure AD Join für das lokale Verzeichnis und die Geräteregistrierung einrichten können."
    services="active-directory" 
    documentationCenter="" 
    authors="femila" 
    manager="stevenpo" 
    editor=""
    tags="azure-classic-portal"/>

<tags 
    ms.service="active-directory" 
    ms.workload="identity" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/19/2015" 
    ms.author="femila"/>

# Einrichten von Azure AD Join in Ihrer Organisation

Vor dem Einrichten von Azure AD Join müssen Sie entweder das lokale Verzeichnis der Benutzer mit der Cloud synchronisieren oder verwaltete Konten in Azure AD manuell erstellen. 

Detaillierte Informationen für Ihre Benutzer lokal an Azure AD synchronisieren behandelt wird [Integrieren Ihrer lokalen Identitäten in Azure Active Directory](active-directory-aadconnect.md).


Um manuell erstellen und Verwalten von Benutzern in Azure AD, finden Sie unter [benutzerverwaltung in Azure AD](https://msdn.microsoft.com/library/azure/hh967609.aspx).

## Einrichten der Geräteregistrierung 
1. Melden Sie sich als Administrator beim Azure-Portal an.
2. Wählen Sie im linken Bereich "Active Directory" aus.
3. Auf der **Verzeichnis** Registerkarte, wählen Sie Ihr Verzeichnis.
4. Wählen Sie die **konfigurieren** Registerkarte.
5. Führen Sie einen Bildlauf zum Abschnitt **Geräte**.
6. Auf der **Geräte** Legen Sie die folgenden:  
   * **Maximale Anzahl der Geräte pro Benutzer**: Wählen Sie die maximale Anzahl der Geräte, die ein Benutzer kann in Azure AD verfügen.  Wenn ein Benutzer diese Anzahl erreicht, können keine zusätzlichen Geräte hinzugefügt werden, bis eines oder mehrere der vorhandenen Geräte entfernt werden.
   * **Mehrstufige Authentifizierung zum Hinzufügen von Geräten erforderlich**: aktivieren, wenn Benutzer eine zweite Stufe der Authentifizierung auf ihrem Gerät in Azure AD einbinden bereitstellen sollen. Weitere Informationen über Multi-Factor Authentication, finden Sie unter [Erste Schritte mit Multi-Factor Authentication in der Cloud](multi-factor-authentication-get-started-cloud/)
   * **Benutzer können Azure AD Join Geräte**: Wählen Sie die Benutzer und Gruppen, die zum Hinzufügen von Geräten zu Azure AD zulässig sind.
   * **Weitere Administratoren für Azure AD eingebundene Geräte**: Azure AD Premium oder Enterprise Mobility Suite (EMS), können Sie auswählen, welchen Benutzern lokale Administratorrechte auf dem Gerät erteilt werden. Globale Administratoren und der Gerätebesitzer erhalten standardmäßig lokale Administratorrechte.

<center>![](./media/active-directory-azureadjoin/active-directory-aadjoin-configure-devices.png) </center>
 
Nachdem Sie Azure AD Join für die Benutzer eingerichtet haben, können diese sich über ihre unternehmenseigenen oder persönlichen Geräte mit Azure AD verbinden. 

Mithilfe der folgenden drei Szenarien können Sie es Ihren Benutzern ermöglichen, Azure AD Join einzurichten:

- Benutzer verknüpfen ein Unternehmensgerät direkt mit Azure AD.
- Benutzer verknüpfen ein unternehmenseigenes Gerät über eine Domäne mit dem lokalen Active Directory und erweitern dann auf Azure AD.
- Benutzer fügen auf einem persönlichen Gerät Arbeitskonten zu Windows hinzu. 

## Zusätzliche Informationen
* [Windows 10 für Unternehmen: Möglichkeiten der geschäftlichen Nutzung von Geräten](active-directory-azureadjoin-windows10-devices-overview.md)
* [Erweitern von Cloudfunktionen auf Windows 10-Geräte über Azure Active Directory Join](active-directory-azureadjoin-user-upgrade.md)
* [Weitere Informationen zu Verwendungsszenarios für Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Verbinden von in die Domäne eingebundenen Geräten mit Azure AD für Windows 10-Funktionen](active-directory-azureadjoin-devices-group-policy.md)
* [Einrichten von Azure AD Join](active-directory-azureadjoin-setup.md)




