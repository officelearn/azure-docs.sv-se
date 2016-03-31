<properties 
    pageTitle="Erweitern von Cloudfunktionen auf Windows 10-Geräte über Azure Active Directory Join| Microsoft Azure" 
    description="Bietet eine ausführliche Übersicht darüber, wie Windows 10-Geräte mithilfe von Azure AD bei Azure Active Directory registriert werden können." 
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
    ms.date="12/07/2015" 
    ms.author="femila"/>

# Erweitern von Cloudfunktionen auf Windows 10-Geräte über Azure Active Directory Join

## Was ist Azure Active Directory Join? 
Azure Active Directory (Azure AD) Join ist die Funktionalität, die ein zum Unternehmen gehörendes Gerät in Azure Active Directory registriert, um die zentralisierte Verwaltung zu ermöglichen. Dadurch können Benutzer (Mitarbeiter, Schüler) nun über Azure Active Directory mit der Unternehmenscloud verbunden werden. Dies ermöglicht vereinfachte Windows-Bereitstellungen, den Zugriff auf Organisations-Apps und Ressourcen von einem beliebigen Windows-Gerät aus, sowohl unternehmenseigene als auch persönliche Geräte (BYOD). 

Azure AD Join ist für Unternehmen gedacht, die hauptsächlich oder ausschließlich in der Cloud arbeiten (in der Regel kleine und mittelständische Unternehmen, die über keine lokale Active Directory-Infrastruktur verfügen). Allerdings kann und wird Azure AD auch von großen Organisationen genutzt, die keine herkömmliche Domänenverknüpfung einrichten können (z. B. für mobile Geräte), oder von Benutzern, die primär Zugriff auf Office 365 oder andere Azure AD-Saas-Apps benötigen. 

Während die herkömmliche Domänenverknüpfung die beste lokale Benutzererfahrung für Geräte bietet, die mit einer Domäne verknüpft werden können, ist Azure AD Join für solche Geräte geeignet, bei denen dies nicht möglich ist, sowie für Bereiche, in denen Sie Benutzer in der Cloud mit MDM-Funktionen anstelle der herkömmlichen Domänenverwaltung mit einer Gruppenrichtlinie und SCCM verwalten möchten. 

![](./media/active-directory-azureadjoin/active-directory-azureadjoin-overview.png)


## Warum Unternehmen Azure AD Join übernehmen sollten 

* **Wenn Ihr Unternehmen zum größten Teil in der Cloud ist**: Wenn Sie in ein Modell, in dem Ihre lokalen Fußabdruck reduzieren und mehr in der Cloud arbeiten möchten, verschieben, Azure AD Join profitieren könnten Sie. Sie haben Azure AD-Konten vielleicht manuell oder über die Synchronisierung des lokalen Active Directory erstellt. In jedem Fall besitzen Sie ein Konto in Azure AD und können es zur Anmeldung bei Windows 10 verwenden. Ihre Benutzer können ihre Computer mit Azure AD verknüpfen, entweder über den OOBE-Prozess oder über den Vorgang in den Einstellungen. Ihre Benutzer profitieren nun vom SSO-Zugriff auf ihre Cloudressourcen wie Office 365 entweder im Browser oder in den Office-Anwendungen. 
* **Bildungseinrichtungen**: eines der großen Szenarien, die wir wissen gerne, ist wie Bildungseinrichtungen zwei Benutzertypen haben: Fakultät und Schüler. Lehrkräfte werden als längerfristige Mitglieder der Organisation betrachtet, sodass lokale Konten für diese Benutzer empfohlen werden. Schüler werden jedoch als kurzfristigere Mitglieder der Organisation angesehen und können somit in Azure AD verwaltet werden, sodass Verzeichnisse in der Cloud statt lokal abgelegt werden können. Studenten können sich jetzt mit ihrem Azure AD-Konto bei Windows anmelden und erhalten Zugriff auf die Office 365-Ressourcen in den Office-Anwendungen. 
* **Retail Unternehmen**: einen anderen Bereich an uns von Kunden ist ihre Entwurf, einfachere Verwaltung der saisonale Worker haben.  Für feste Mitarbeiter in Vollzeit werden auch hier lokale Konten erstellt. Sie verwenden in der Regel Computer, die in die Domäne eingebunden sind. Saisonbedingte Mitarbeiter sind jedoch kurzfristigere Mitglieder der Organisation, sodass sie dort verwaltet werden sollten, wo Benutzerlizenzen leichter verschoben werden können. Das Erstellen dieser Benutzer in der Cloud mit Office 365-Lizenzen ermöglicht diesen, von den Vorteilen bei der Anmeldung an Windows- und Office-Anwendungen mit einem Azure AD-Konto zu profitieren. Gleichzeitig sind die Lizenzen flexibler, nachdem diese Benutzer das Unternehmen verlassen haben. 
* **Andere Unternehmen**: und umfangreicher als diese bestimmten Szenarien, möglicherweise auch, wenn Sie in Ihrer lokalen Benutzer verwalten finden AD-Verzeichnis Sie weiterhin profitieren könnten mit Benutzer Azure AD Join aufgrund der vereinfachten verknüpfen auftreten, die Verwaltung der Geräte in Azure AD, automatische MDM-Registrierung und einmaliges Anmelden bei Azure AD und lokalen Ressourcen.  

## Azure AD Join-Funktionen
Azure AD Join bietet folgende Funktionen: 

* **Automatische Bereitstellung von corp-Geräte**: mit Windows 10, können Endbenutzer einen völlig neuen, eingeschweißt Gerät in der Out-of-Box-Experience, ohne Beteiligung der IT konfigurieren.


* **Unterstützung für moderne Formfaktoren**: Azure AD Join funktioniert auf Geräten, die traditionelle die Domäne beitreten Funktionen verfügen.  


* **Vorhandene Organisations-Konten verwendet**: Endbenutzer nicht mehr benötigen, erstellen und Verwalten eines persönlichen Microsoft-Kontos, um optimale Ergebnisse auf Unternehmen ausgestellt Geräten, wie unter Windows 8 zu erhalten. Sie können stattdessen ihr vorhandenes Arbeitskonto in Azure AD verwenden. Für viele Organisationen bedeutet dies im Grunde das Einrichten und Anmelden bei Windows mit den gleichen Anmeldeinformationen wie bei Office 365. 


* **Automatische MDM-Registrierung**: Geräte automatisch in der Verwaltung, die beim Verbinden mit Azure AD registriert abrufen können. Dies funktioniert mit Microsoft Intune und MDMs anderer Anbieter. Bei der Verwaltung durch Intune kann die IT mit Azure AD verbundene Geräte gemeinsam mit in Domänen verbundenen Geräte in der SCCM-Verwaltungskonsole überwachen und verwalten.


* **Einmaliges Anmelden auf Unternehmensressourcen**: Benutzer genießen Sie einmaliges Anmelden aus dem Windows-Desktop-apps und Ressourcen in der Cloud, z. B. Office 365 und Tausenden von Business Applications, die für die Authentifizierung über Azure AD benötigen [Azure AD Connect](active-directory-azureadjoin-deployment-aadjoindirect.md). Unternehmen gehörende Geräte, Azure AD angehören, werden SSO auch auf lokale Ressourcen nutzen werden, wenn das Gerät ist auf Corpnet, und von überall aus, wenn diese Ressourcen verfügbar, über gemacht werden die [Azure AD-Anwendungsproxy](https://msdn.microsoft.com/library/azure/Dn768219.aspx). 


* **OS Zustand Roaming**: z. B. Eingabehilfen, Websites und Wi-Fi-Kennwörter über Unternehmen gehörende Geräte synchronisiert werden ohne ein persönlichen Microsoft-Konto.


* **Unternehmen windowsstore**: Windows Store app-Erwerb und Lizenzierung mit Azure AD-Konten unterstützt. Organisationen können Volumenlizenz-Apps verwenden und den Benutzern in ihrer Organisation verfügbar machen.

## Funktionsweise von verschiedene Geräten mit Azure AD Join

| Unternehmensgeräte (mit lokaler Domäne verknüpft)                                                                                                                                                                                         | Unternehmensgerät (verknüpft mit der Cloud)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     | Persönliche Geräte                                                                                                         |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------|
| Benutzer melden Sie sich bei Windows mit den Anmeldeinformationen ihrer Arbeit an (wie dies aktuell der Fall ist).                                                                                                                                                                        | Benutzer können sich bei Windows mit den Anmeldeinformationen Ihrer Arbeit anmelden, die in Azure AD verwaltet werden. Dies ist relevant für unternehmenseigene Geräte in drei Fällen: die Organisation keinen AD vor Ort (z. B. small Business) die Organisation nicht erstellen, alle Benutzerkonten in AD (z. B. Studenten, Berater, saisonale Worker) unternehmenseigene Geräte, die mit einer Domäne (lokal) wie Smartphones oder Tablets mit einer Mobile SKU verknüpft werden können. Beispielsweise funktioniert ein sekundäres Gerät auf Fabrik-/Einzelhandelsebene bei verwalteten und verbundenen Organisationen. | Benutzer melden Sie sich bei Windows mit ihren persönlichen MSA-Anmeldeinformationen an (keine Änderung).                                                |
| Benutzer haben Zugriff auf das Roaming von Einstellungen und auf Windows Store. Diese Dienste funktionieren mit Arbeitskonten (eine persönliche MSA ist nicht erforderlich). Erfordert, dass Unternehmen ihre lokale AD mit Azure AD verknüpfen.                                        | Self-Service-Einrichtung – Benutzer können den Eindruck beim ersten Ausführen über ihr Arbeitskonto durchgehen (als Alternative dazu, dass die IT die Geräte bereitstellt, wobei beide Methoden unterstützt werden).                                                                                                                                                                                                                                                                                                                                                                             | Es ist spielend leicht, ein Arbeitskonto hinzuzufügen, dass in AD oder Azure AD verwaltet wird.                                                      |
| Einmaliges Anmelden vom Desktop, um mit Apps, Websites oder Ressourcen zu arbeiten. Dies funktioniert sowohl lokal als auch in der Cloud für Apps, die Azure AD für die Authentifizierung nutzen.                                                                                                            | Automatische Registrierung im Unternehmensverzeichnis (Azure AD) und für das MDM. (Azure AD Premium-Funktion)                                                                                                                                                                                                                                                                                                                                                                                                                                                  | Liefert einmaliges Anmelden für Apps und für Websites oder Ressourcen mit diesem Arbeitskonto                                              |
| Benutzer können ihre persönliche MSA für den Zugriff auf eigene Bilder oder Dateien hinzufügen, ohne Unternehmensdaten zu beeinträchtigen (die Roamingeinstellungen funktionieren weiterhin mit dem Konto). Das MSA-Konto ermöglicht einmaliges Anmelden und führt nicht länger das Roaming der Einstellungen aus.  | Self-Service-Kennwortzurücksetzung für die Windows-Anmeldung (die Möglichkeit, ein vergessenes Kennwort zurückzusetzen). (Hierfür benötigen Sie Azure AD Premium.)                                                                                                                                                                                                                                                                                                                                                                                                                                    | Ermöglicht den Zugriff auf den Enterprise Store, damit Benutzer Branchen-Apps erwerben und auf ihren persönlichen Geräten verwenden können. |                                                               |


## Zusätzliche Informationen
* [Windows 10 für Unternehmen: Möglichkeiten der geschäftlichen Nutzung von Geräten](active-directory-azureadjoin-windows10-devices-overview.md)
* [Erweitern von Cloudfunktionen auf Windows 10-Geräte über Azure Active Directory Join](active-directory-azureadjoin-user-upgrade.md)
* [Authentifizieren von Identitäten ohne Kennwörter über Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Weitere Informationen zu Verwendungsszenarios für Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Verbinden von in die Domäne eingebundenen Geräten mit Azure AD für Windows 10-Funktionen](active-directory-azureadjoin-devices-group-policy.md)
* [Einrichten von Azure AD Join](active-directory-azureadjoin-setup.md)



