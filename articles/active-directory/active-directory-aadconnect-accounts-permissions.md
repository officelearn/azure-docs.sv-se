<properties
   pageTitle="Azure AD Connect-Konten und -Berechtigungen | Microsoft Azure"
   description="Dieses Thema beschreibt die verwendeten und erstellten Konten sowie die erforderlichen Berechtigungen."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"  
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="12/16/2015"
   ms.author="andkjell;billmath"/>


# Erforderliche Konten und Berechtigungen für Azure AD Connect

Der Azure AD Connect-Installations-Assistent bietet zwei verschiedene Methoden:

- Für die Express-Einstellungen benötigen Sie mehr Berechtigungen, um die Konfiguration einfach einrichten zu können, ohne Benutzern erstellen oder Berechtigungen einzeln konfigurieren zu müssen.

- Für die benutzerdefinierten Einstellungen stehen mehr Auswahlmöglichkeiten und Optionen zur Verfügung. Es gibt aber einige Situationen, in denen Sie sicherstellen müssen, dass Sie selbst über die richtigen Berechtigungen verfügen.

## Verwandte Dokumentation
Wenn Sie die Dokumentation auf nicht lesen [Integrieren Ihrer lokalen Identitäten in Azure Active Directory](active-directory-aadconnect.md), die folgende Tabelle enthält Links zu verwandten Themen.

| Thema |  |
| --------- | --------- |
| Installieren mit den Express-Einstellungen | [Expressinstallation von Azure AD Connect](active-directory-aadconnect-get-started-express.md) |
| Installieren mit benutzerdefinierten Einstellungen | [Benutzerdefinierte Installation von Azure AD Connect](active-directory-aadconnect-get-started-custom.md) |
| Upgrade von DirSync | [Upgrade von Azure AD-Synchronisierungstools (DirSync)](active-directory-aadconnect-dirsync-upgrade-get-started.md) |


## Installation mit Express-Einstellungen
In den Express-Einstellungen fordert der Installations-Assistent zur Angabe von Enterprise-Administratoranmeldeinfos auf, daher kann Ihr lokales Active Directory mit den erforderlichen Berechtigungen für Azure AD Connect konfiguriert werden. Bei einem Upgrade von DirSync werden die Enterprise-Administratoranmeldeinfos verwendet, um das Kennwort für das von Dirsync verwendete Konto zurückzusetzen.

Seite des Assistenten  | Erfasste Anmeldeinformationen | Erforderliche Berechtigungen| Verwendung
------------- | ------------- |------------- |------------- |
N/V|Benutzer, der den Installations-Assistenten ausführt| Administrator des lokalen Servers| <li>Erstellt das lokale Konto, die als verwendet werden, die [Synchronisieren moduldienstkontos](#azure-ad-connect-sync-service-account).
Herstellen einer Verbindung mit Azure AD| Azure AD-Verzeichnisanmeldeinformationen | Globale Administratorrolle in Azure AD | <li>Aktivieren der Synchronisierung im Azure AD-Verzeichnis.</li>  <li>Erstellung der [Azure AD-Konto](#azure-ad-service-account) wird für die fortlaufenden Synchronisierungsvorgänge in Azure AD verwendet werden.</li>
Herstellen einer Verbindung mit AD DS | Lokale Active Directory-Anmeldeinformationen | Mitglied der  Gruppe "Unternehmensadministratoren" in Active Directory| <li>Erstellt ein [Konto](#active-directory-account) in Active Directory und Erteilen von Berechtigungen für sie. Dieses Konto erstellte wird zum Lesen und Schreiben von Verzeichnisinformationen während der Synchronisierung verwendet.</li>

### Enterprise-Administratoranmeldeinfos
Diese Anmeldeinformationen werden nur während der Installation verwendet und nicht mehr, nachdem die Installation abgeschlossen ist. Es handelt sich um Enterprise-Administratoren und nicht um Domänenadministratoren, um sicherzustellen, dass die Berechtigungen in Active Directory in allen Domänen festgelegt werden können.

### Globale Administratoranmeldeinfos
Diese Anmeldeinformationen werden nur während der Installation verwendet und nicht mehr, nachdem die Installation abgeschlossen ist. Dient zum Erstellen der [Azure AD-Kontos](#azure-ad-service-account) zum Synchronisieren der Änderungen in Azure AD verwendet. Das Konto aktiviert auch Synchronisierung als Feature in Azure AD. Für das verwendete Konto kann keine MFA aktiviert werden.

## Installation mit benutzerdefinierten Einstellungen
Wenn Sie benutzerdefinierte Einstellungen verwenden, muss das Konto für die Verbindung mit Active Directory vor der Installation erstellt werden.

Seite des Assistenten  | Erfasste Anmeldeinformationen | Erforderliche Berechtigungen| Verwendung
------------- | ------------- |------------- |-------------
N/V|Benutzer, der den Installations-Assistenten ausführt|<li>Administrator des lokalen Servers</li><li>Wenn eine Vollversion von SQL Server zu verwenden, muss der Benutzer System Administrator (SA) in SQL sein.</li>| Standardmäßig erstellt das lokale Konto, das als verwendet werden soll die [Synchronisieren moduldienstkontos](#azure-ad-connect-sync-service-account). Das Konto wird nur erstellt, wenn der Administrator kein bestimmtes Konto angegeben wird.
"Synchronisierungsdienste installieren", Option "Dienstkonto" | Anmeldeinformationen für Active Directory- oder lokale Konten | Benutzerberechtigungen werden vom Installations-Assistenten gewährt|Wenn der Administrator ein Konto angibt, wird dieses Konto als Dienstkonto für den Synchronisierungsdienst verwendet.
Herstellen einer Verbindung mit Azure AD|Azure AD-Verzeichnisanmeldeinformationen| Globale Administratorrolle in Azure AD| <li>Aktivieren der Synchronisierung im Azure AD-Verzeichnis.</li>  <li>Erstellung der [Azure AD-Konto](#azure-ad-service-account) wird für die fortlaufenden Synchronisierungsvorgänge in Azure AD verwendet werden.</li>
Verzeichnisse verbinden|Lokale Active Directory-Anmeldeinformationen für jede Gesamtstruktur, die mit Azure AD verbunden wird. | Hängen die Berechtigungen an, welche Funktionen Sie aktivieren, und finden Sie im [das AD DS-Konto erstellen](#create-the-ad-ds-account) |Dieses Konto dient zum Lesen und Schreiben von Verzeichnisinformationen während der Synchronisierung.
AD FS-Server|Für jeden Server in der Liste sammelt der Assistent Anmeldeinformationen, wenn die Anmeldeinformationen des Benutzers, der den Assistenten ausführt, nicht für die Verbindung ausreichen.|Domänenadministrator|Installieren und Konfigurieren der AD FS-Server-Rolle.
Webanwendungsproxy-Server |Für jeden Server in der Liste sammelt der Assistent Anmeldeinformationen, wenn die Anmeldeinformationen des Benutzers, der den Assistenten ausführt, nicht für die Verbindung ausreichen.|Lokaler Administrator auf dem Zielcomputer|Installieren und Konfigurieren der WAP-Server-Rolle.
Anmeldeinformationen der Proxyvertrauensstellung |Anmeldeinformationen der Verbunddienstvertrauensstellung (die Anmeldeinformationen, die der Proxy zur Registrierung für ein Zertifikat für die Vertrauensstellung vom FS verwendet) |Domänenkonto, dessen Benutzer ein lokaler Administrator des AD FS-Servers ist|Erste Registrierung des vertrauenswürdigen FS-WAP-Zertifikats.
Seite "AD FS-Dienstkonto", Option "Domänenbenutzerkonto verwenden"|Anmeldeinformationen für das Active Directory-Benutzerkonto|Domänenbenutzer|Das AD-Benutzerkonto, dessen Anmeldeinformationen bereitgestellt wurden, wird als das Anmeldekonto des AD FS-Diensts verwendet.

### Erstellen des AD DS-Kontos
Bei der Installation von Azure AD Connect das angegebene Konto auf dem **Verbinden Sie Ihre Verzeichnisse** Seite in Active Directory vorhanden sein und über den erforderlichen Berechtigungen. Der Installations-Assistent prüft die Berechtigungen nicht, und Probleme werden nur während der Synchronisierung gefunden.

Welche Berechtigungen Sie benötigen, hängt von den aktivierten optionalen Funktionen ab. Wenn Sie über mehrere Domänen verfügen, müssen die Berechtigungen für alle Domänen in der Gesamtstruktur erteilt werden. Wenn Sie keine Funktionen standardmäßig aktivieren **Domänenbenutzer** Berechtigungen ausreichen.

| Funktion | Berechtigungen |
| ------ | ------ |
| Kennwortsynchronisierung | <li>Verzeichnisänderungen replizieren</li>  <li>Replizieren Verzeichnis ändert alle |
| Exchange-Hybridbereitstellung | Schreibberechtigungen für die Attribute dokumentiert [Exchange Hybrid Rückschreiben](active-directory-aadconnectsync-attributes-synchronized.md#exchange-hybrid-writeback) für Benutzer, Gruppen und Kontakte. |
| Rückschreiben von Kennwörtern | Schreibberechtigungen für die Attribute dokumentiert [Erste Schritte mit der Verwaltung der Kennwörter](active-directory-passwords-getting-started.md#step-4-set-up-the-appropriate-active-directory-permissions) für Benutzer. |
| Geräterückschreiben | Berechtigungen, die mit einem Powershellskript unter [geräterückschreiben](active-directory-aadconnect-get-started-custom-device-writeback.md).|
| Gruppenrückschreiben | Lesen, Erstellen, Aktualisieren und Löschen von Gruppenobjekten in der Organisationseinheit, in der sich die Verteilergruppen befinden sollen.|

## Upgrade
Wenn Sie Azure AD Connect auf eine höhere Version aktualisieren, benötigen Sie folgende Berechtigungen:

| Prinzipal | Erforderliche Berechtigungen | Verwendung |
| ---- | ---- | ---- |
| Benutzer, der den Installations-Assistenten ausführt | Administrator des lokalen Servers | Aktualisieren von Binärdateien. |
| Benutzer, der den Installations-Assistenten ausführt | Mitglied von ADSyncAdmins | Vornehmen von Änderungen an den Synchronisierungsregeln und anderen Konfigurationen. |
| Benutzer, der den Installations-Assistenten ausführt | Bei Verwendung eines vollständigen SQL-Servers: DBO (oder ähnlich) der Datenbank für das Synchronisierungsmodul | Vornehmen von Änderungen auf Datenbankebene, z. B. Aktualisieren von Tabellen mit neuen Spalten. |

## Weitere Informationen zu den erstellten Konten

### Active Directory-Konto

Wenn Sie Express-Einstellungen verwenden, wird ein Konto für die Synchronisierung in Active Directory erstellt. Das erstellte Konto befindet sich im Benutzercontainer der Gesamtstruktur-Stammdomäne und weist den Namen mit dem Präfix **MSOL_**. Das Konto wird mit einem langen komplexen Kennwort erstellt, das nicht abläuft. Wenn Sie über eine Kennwortrichtlinie in der Domäne verfügen, stellen Sie sicher, dass lange und komplexe Kennwörter für dieses Konto zulässig sind.

![AD-Konto](./media/active-directory-aadconnect-accounts-permissions/adsyncserviceaccount.png)

### Azure AD Connect-Synchronisierungsdienstkonten
Der Installations-Assistent erstellt zwei lokale Dienstkonten (sofern das zu verwendende Konto nicht in den benutzerdefinierten Einstellungen angegeben wird). Das Konto, mit dem Präfix **AAD_** wird für den tatsächlichen Synchronisierungsdienst verwendet, um als ausführen. Wenn Sie Azure AD Connect auf einem Domänencontroller installieren, werden die Konten in der Domäne erstellt. Wenn Sie einen SQLServer auf einem Remoteserver verwenden die **AAD_** -Dienstkonto muss in der Domäne befinden. Das Konto, mit dem Präfix **AADSyncSched_** wird für den geplanten Task, der das Synchronisierungsmodul ausgeführt wird.

![Synchronisierungsdienstkonto](./media/active-directory-aadconnect-accounts-permissions/syncserviceaccount.png)

Die Konten werden mit einem langen komplexen Kennwort erstellt, das nicht abläuft.

Das Dienstkonto für das Synchronisierungsmodul wird von Windows verwendet, um die Verschlüsselungsschlüssel zu speichern. Darum darf das Kennwort für dieses Konto nicht zurückgesetzt oder geändert werden.

Bei Verwendung eines vollständigen SQL Servers wird das Dienstkonto zum DBO der Datenbank, die für das Synchronisierungsmodul erstellt wurde. Der Dienst funktioniert mit anderen Berechtigungen nicht wie vorgesehen. Eine SQL-Anmeldung wird ebenfalls erstellt.

Das Konto erhält auch Berechtigungen für Dateien, Registrierungsschlüssel und andere Objekte im Zusammenhang mit dem Synchronisierungsmodul.

### Azure AD-Dienstkonto
Ein Konto in Azure AD wird für die Verwendung mit dem Synchronisierungsdienst erstellt. Dieses Konto kann anhand des Anzeigenamens identifiziert werden.

![AD-Konto](./media/active-directory-aadconnect-accounts-permissions/aadsyncserviceaccount.png)

Der Name des Servers, auf dem das Konto verwendet wird, kann im zweiten Teil des Benutzernamens identifiziert werden. In der Abbildung oben heißt der Server "FABRIKAMCON". Wenn Sie über Stagingserver verfügen, erhält jeder Server ein eigenes Konto. Es besteht eine Beschränkung auf 10 Synchronisierungsdienstkonten in Azure AD.

Das Dienstkonto wird mit einem langen komplexen Kennwort erstellt, das nicht abläuft. Es wird eine besondere Rolle gewährt **Directory-Synchronisierung Konten** nur Berechtigungen zum Ausführen von Synchronisierungsaufgaben Verzeichnis ausübt. Diese spezielle integrierte Rolle kann nicht außerhalb des Azure AD Connect-Assistenten aufgerufen werden und der Azure-Portal wird nur angezeigt, dieses Konto mit der Rolle **Benutzer**.

![AD-Kontenrolle](./media/active-directory-aadconnect-accounts-permissions/aadsyncserviceaccountrole.png)

## Nächste Schritte

Erfahren Sie mehr über [Integrieren Ihrer lokalen Identitäten in Azure Active Directory](active-directory-aadconnect.md).


