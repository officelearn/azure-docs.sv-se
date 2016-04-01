<properties
   pageTitle="Azure AD Connect Sync: Operative Aufgaben und Überlegungen | Microsoft Azure"
   description="In diesem Thema werden die operativen Aufgaben für Azure AD Connect Sync und die Vorbereitung dieser Komponente für den Betrieb beschrieben."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="11/24/2015"
   ms.author="andkjell"/>

# Azure AD Connect Sync: Operative Aufgaben und Überlegungen
Das Ziel dieses Themas ist die Beschreibung der operativen Aufgaben für Azure AD Connect Sync.

## Stagingmodus
Der Stagingmodus kann für verschiedene Szenarien genutzt werden, darunter:

-   Hohe Verfügbarkeit.
-   Testen und Bereitstellen neuer Konfigurationsänderungen.
-   Einführen eines neuen Servers und Außerbetriebnahme des alten Servers.

Mit einem Server im Stagingmodus können Sie Änderungen an der Konfiguration vornehmen und eine Vorschau der Änderungen anzeigen, bevor Sie den Server aktiv schalten. Ein Server im Stagingmodus ermöglicht es Ihnen außerdem, einen vollständigen Import und eine vollständige Synchronisierung durchzuführen und so sicherzustellen, dass alle Änderungen wie erwartet durchgeführt werden, bevor Sie diese in Ihrer Produktionsumgebung implementieren.

Während der Installation können Sie auswählen, die Server im **stagingmodus**. Auf diese Weise wird der Server für Import und Synchronisierung aktiviert, es werden jedoch keine Exporte durchgeführt. Ein Server im Stagingmodus führt keine Kennwortsynchronisierung oder Kennwortrückschreibung durch – selbst dann nicht, wenn Sie diese Funktionen aktivieren. Wenn Sie den Stagingmodus deaktivieren, beginnt der Server mit dem Export und aktiviert die Kennwortsynchronisierung und die Kennwortrückschreibung (sofern ausgewählt).

Ein Server im Stagingmodus empfängt weiterhin Änderungen von Active Directory und Azure AD. Er verfügt deshalb stets über eine Kopie der aktuellen Änderungen und kann die Funktionen eines anderen Servers sehr schnell übernehmen. Wenn Sie Konfigurationsänderungen an Ihrem primären Server vornehmen, liegt es in Ihrer Verantwortung, dieselben Änderungen auch auf den Servern im Stagingmodus durchzuführen.

Wenn Sie sich bisher nur mit älteren Synchronisierungstechnologien auskennen, müssen Sie wissen, dass Änderungen für den Stagingmodus gelten, seit dieser über eine eigene SQL-Datenbank verfügt. Dies ermöglicht es, dass der Server im Stagingmodus in einem anderen Datencenter platziert werden kann.

### Überprüfen der Konfiguration eines Servers
Führen Sie zum Anwenden dieser Methode die folgenden Schritte aus:

1. Vorbereiten
2. Importieren und Synchronisieren
3. Überprüfen
4. Wechseln des aktiven Servers

**Vorbereiten**

1. Azure AD Connect installieren, wählen Sie **stagingmodus**, und heben Sie die Auswahl **Starten Sie die Synchronisierung** auf der letzten Seite im Installations-Assistenten. Auf diese Weise kann das Synchronisierungsmodul manuell ausgeführt werden.
![ReadyToConfigure](./media/active-directory-aadconnectsync-operations/readytoconfigure.png)
2. / Abmeldung und über das Startmenü aus **Synchronisierungsdienst**.

**Importieren und Synchronisieren**

1. Wählen Sie **Connectors**, und wählen Sie den ersten Connector mit dem Typ **Active Directory Domain Services**. Klicken Sie auf **Ausführen**, Option **Vollständiger Import**, und **OK**. Führen Sie diese Schritte für alle Connectors von diesem Typ aus.
2. Wählen Sie den Connector mit dem Typ **Azure Active Directory (Microsoft)**. Klicken Sie auf **Ausführen**, Option **Vollständiger Import**, und **OK**.
4. Sicherstellen, dass Connectors ausgewählt ist und für jeden Connector mit dem Typ **Active Directory Domain Services**, klicken Sie auf **Ausführen**, Option **Delta-Synchronisierung**, und **OK**.
5. Wählen Sie den Connector mit dem Typ **Azure Active Directory (Microsoft)**. Klicken Sie auf **Ausführen**, Option **Delta-Synchronisierung**, und klicken Sie dann auf OK.

Sie haben einen Stagingexport der Änderungen an Azure AD und der lokalen Active Directory-Umgebung durchgeführt (wenn Sie eine Exchange-Hybridbereitstellung verwenden). In den nächsten Schritten können Sie überprüfen, was geändert wird, bevor Sie mit dem Export in die Verzeichnisse beginnen.

**Überprüfen**

1. Starten Sie eine Eingabeaufforderung, und wechseln Sie zu: `%Program Files%\Microsoft Azure AD Sync\bin`
2. Führen Sie diesen Befehl aus: `csexport "Name of Connector" %temp%\export.xml /f:x`<BR/>
Der Name des Connectors kann in Synchronisierungsdienst gefunden werden. Für Azure AD lautet dieser ähnlich wie "contoso.com – AAD".
3. Führen Sie diesen Befehl aus: `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv`
4. Sie verfügen jetzt im Ordner "%temp%" über eine Datei namens "export.csv", die in Microsoft Excel untersucht werden kann. Diese Datei enthält alle Änderungen, die exportiert werden sollen.
5. Nehmen Sie erforderliche Änderungen an den Daten oder der Konfiguration vor, und führen Sie die oben genannten Schritte erneut aus (Importieren, Synchronisieren, Überprüfen), bis Sie die Änderungen erhalten, die Sie exportieren möchten.

**Grundlegendes zur Datei "export.csv"**

Die meisten Bestandteile der Datei sind selbsterklärend. Nachfolgend werden zum Verständnis des Inhalts einige Abkürzungen erläutert:

- OMODT: Object Modification Type. Gibt an, ob es sich bei dem Vorgang auf Objektebene um einen Hinzufüge-, Aktualisierungs- oder Löschvorgang handelt.
- AMODT: Attribute Modification Type. Gibt an, ob es sich bei dem Vorgang auf Attributebene um einen Hinzufüge-, Aktualisierungs- oder Löschvorgang handelt.

Wenn das Attribut mehrere Werte umfassen kann, wird nicht jede Änderung angezeigt. Es wird nur die Anzahl von hinzugefügten und entfernten Werten angezeigt.

**Wechseln des aktiven Servers**

1. Schalten Sie den derzeit aktiven Server entweder aus (DirSync/FIM/Azure AD Sync), damit kein Export nach Azure AD stattfindet, oder versetzen Sie ihn in den Stagingmodus (Azure AD Connect).
2. Führen den Installations-Assistenten auf dem Server in **stagingmodus** und Deaktivieren von **stagingmodus**.
![ReadyToConfigure](./media/active-directory-aadconnectsync-operations/additionaltasks.png)

## Notfallwiederherstellung
Ein Bestandteil des Implementierungsentwurfs besteht darin, für den Fall zu planen, dass der Synchronisierungsserver aufgrund eines Notfalls ausfällt. Es stehen mehrere Modelle zur Verfügung, und die Auswahl des Modells richtet sich nach verschiedenen Faktoren, darunter:

-   Wie hoch ist die Toleranz, wenn während der Ausfallzeit keine Objektänderungen in Azure AD durchgeführt werden können?
-   Bei Verwendung der Kennwortsynchronisierung: Ist es für die Benutzer akzeptabel, dass sie in Azure AD ihr altes Kennwort verwenden müssen, wenn sie ihr lokales Kennwort ändern?
-   Besteht eine Abhängigkeit von Echtzeitvorgängen, beispielsweise dem Kennwortrückschreiben?

Abhängig von Ihren Antworten auf diese Fragen und Ihrer Organisationsrichtlinie kann eine der folgenden Strategien implementiert werden:

-   Führen Sie ggf. eine Neuerstellung durch.
-   Einen spare Standbyserver genannt haben **stagingmodus**.
-   Verwenden Sie virtuelle Computer.

Da Azure AD Connect Sync von einer SQL-Datenbank abhängt, sollten Sie auch den Abschnitt "Hohe Verfügbarkeit von SQL" lesen, sofern Sie nicht SQL Express verwenden (im Lieferumfang von Azure AD Connect enthalten).

### Neuerstellung, falls erforderlich
Eine sinnvolle Strategie besteht darin, eine ggf. erforderliche Neuerstellung des Servers zu planen. In vielen Fällen können die Installation des Synchronisierungsmoduls und der anfängliche Import sowie die Synchronisierung innerhalb weniger Stunden abgeschlossen werden. Wenn kein Ersatzserver verfügbar ist, kann vorübergehend ein Domänencontroller zum Hosten des Synchronisierungsmoduls eingesetzt werden.

Das Synchronisierungsmodul speichert keine Statusinformationen zu den Objekten, deshalb kann die Datenbank mithilfe der Daten in Active Directory und Azure AD neu erstellt werden. Die **SourceAnchor** Attribut wird verwendet, um die Objekte aus der lokalen und der Cloud zu verknüpfen. Wenn Sie den Server mit vorhandenen lokalen Objekten und Cloudobjekten neu erstellen, werden diese vom Synchronisierungsmodul bei der Neuinstallation abgeglichen.
Dokumentieren und speichern Sie die am Server vorgenommenen Änderungen, beispielsweise die Filter- und Synchronisierungsregeln. Diese müssen erneut angewendet werden, bevor Sie die Synchronisierung starten.

### Stellen Sie einen Reservestandbyserver bereit (Stagingmodus)
Wenn Sie über eine komplexere Umgebung verfügen, wird empfohlen, mindestens einen Standbyserver einzurichten. Während der Installation können Sie einen Server im **stagingmodus**.

Weitere Informationen finden Sie unter [stagingmodus](#staging-mode).

### Einsatz virtueller Computer
Eine gängige und unterstützte Methode ist die Ausführung des Synchronisierungsmoduls auf einem virtuellen Computer. Wenn ein Problem mit dem Host vorliegt, kann das Image mit dem Synchronisierungsmodulserver zu einem anderen Server migriert werden.

### Hohe Verfügbarkeit von SQL
Falls Sie nicht SQL Server Express verwenden, das im Lieferumfang von Azure AD Connect enthalten ist, sollten Sie auch eine hohe Verfügbarkeit für SQL Server berücksichtigen. Die einzige unterstützte Hochverfügbarkeitslösung ist das SQL-Clustering. Nicht unterstützt werden Spiegelung und Always On-Lösungen.

## Nächste Schritte
Erfahren Sie mehr über die [Azure AD Connect-Synchronisierung](active-directory-aadconnectsync-whatis.md) Konfiguration.

Erfahren Sie mehr über [Integrieren Ihrer lokalen Identitäten in Azure Active Directory](active-directory-aadconnect.md).


