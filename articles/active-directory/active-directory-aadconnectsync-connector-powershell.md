<properties
   pageTitle="Azure AD Connect-Synchronisierung: PowerShell-Connector"
   description="Dieser Artikel beschreibt die Konfiguration des Microsoft Windows PowerShell-Connectors."
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
   ms.author="andkjell"/>

# Technische Referenz für den Windows PowerShell-Connector

Dieser Artikel beschreibt den Windows PowerShell-Connector. Der Artikel bezieht sich auf folgende Produkte:

- Microsoft Identity Manager 2016 (MIM2016)
- Forefront Identity Manager 2010 R2 (FIM2010R2)
    -   Hotfix 4.1.3461.0 müssen verwenden oder höher [KB2870703](https://support.microsoft.com/kb/2870703).

Der Connector steht als Download für MIM2016 und FIM2010R2 der [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=717495).

## Übersicht über den PowerShell-Connector

Der PowerShell-Connector ermöglicht die Integration des Synchronisierungsdiensts in externe Systeme mit Windows PowerShell-basierten Anwendungsprogrammierschnittstellen (Application Programming Interfaces, APIs). Der Connector stellt eine Brücke zwischen den Funktionen des aufrufbasierten ECMA2-Frameworks und Windows PowerShell dar. Weitere Informationen über das ECMA-Framework finden Sie unter der [Extensible Connectivity 2.2 Management Agent Verweis](https://msdn.microsoft.com/library/windows/desktop/hh859557.aspx).

### Voraussetzungen

Zur Verwendung des Connectors muss auf dem Synchronisierungsserver neben den weiter oben ggf. erwähnten Hotfixes Folgendes vorhanden sein:

- Microsoft .NET 4.5.2 Framework oder eine höhere Version
- Windows PowerShell 2.0, 3.0 oder 4.0

Die Ausführungsrichtlinie auf dem Synchronisierungsdienst-Server muss so konfiguriert sein, dass der Connector Windows PowerShell-Skripts ausführen kann. Falls die vom Connector ausgeführten Skripts nicht digital signiert sind, konfigurieren Sie die Ausführungsrichtlinie durch Ausführen des folgenden Befehls:

`Set-ExecutionPolicy -ExecutionPolicy RemoteSigned`

## Erstellen eines neuen Connectors

Zur Erstellung eines Windows PowerShell-Connectors im Synchronisierungsdienst müssen Sie eine Reihe von Windows PowerShell-Skripts bereitstellen, die die vom Synchronisierungsdienst angeforderten Schritte ausführen. Die Skripts sind abhängig von der Datenquelle, mit der Sie eine Verbindung herstellen, sowie von den benötigten Funktionen. Dieser Abschnitt informiert über die einzelnen implementierbaren Skripts sowie darüber, wann sie jeweils benötigt werden.

Der Windows PowerShell-Connector speichert die einzelnen Skripts in der Datenbank des Synchronisierungsdiensts. Zwar können auch im Dateisystem gespeicherte Skripts ausgeführt werden, es ist jedoch deutlich einfacher, den Text der jeweiligen Skripts direkt in die Konfiguration des Connectors einzufügen.

So erstellen Sie einen PowerShell-Connector in **Synchronisierungsdienst** wählen **Management Agent** und **Erstellen**. Wählen Sie die **PowerShell (Microsoft)** Connectors.

![Connectorerstellung](./media/active-directory-aadconnectsync-connector-powershell/createconnector.png)

### Konnektivität

Im nächsten Schritt können Sie Konfigurationsparameter für die Verbindung mit einem Remotesystem angeben. Diese Parameter werden vom Synchronisierungsdienst sicher gespeichert und beim Ausführen des Connectors für Ihre Windows PowerShell-Skripts verfügbar gemacht.

![Konnektivität](./media/active-directory-aadconnectsync-connector-powershell/connectivity.png)

Folgende Konnektivitätsparameter können konfiguriert werden:

**Konnektivität**

| Parameter | Standardwert | Zweck |
| --- | --- | --- |
| Server | <Blank> | Der Name des Servers, mit dem der Connector eine Verbindung herstellen soll. |
| Domäne | <Blank> | Die Domäne der zu speichernden Anmeldeinformationen, die beim Ausführen des Connectors verwendet werden sollen. |
| Benutzer | <Blank> | Der Benutzername der zu speichernden Anmeldeinformationen, die beim Ausführen des Connectors verwendet werden sollen. |
| Kennwort | <Blank> | Das Kennwort der zu speichernden Anmeldeinformationen, die beim Ausführen des Connectors verwendet werden sollen. |
| Identität des Connector-Kontos annehmen | False | Bei Verwendung von „True“ führt der Synchronisierungsdienst die Windows PowerShell-Skripts im Kontext der oben angegebenen Anmeldeinformationen aus. Es wird empfohlen, anstelle des Identitätswechsels den an die einzelnen Skripts übergebenen $Credentials-Parameter zu verwenden. Weitere Informationen zu erforderlichen Zusatzberechtigungen für die Verwendung dieses Parameters finden Sie unter „Zusätzliche Konfiguration für den Identitätswechsel“. |
| Beim Identitätswechsel Benutzerprofil laden | False | Weist Windows an, beim Identitätswechsel das Benutzerprofil der Anmeldeinformationen des Connectors zu laden. Falls der Benutzer, dessen Identität übernommen werden soll, über ein servergespeichertes Profil verfügt, wird das Profil vom Connector nicht geladen. Weitere Informationen zu erforderlichen Zusatzberechtigungen für die Verwendung dieses Parameters finden Sie unter „Zusätzliche Konfiguration für den Identitätswechsel“. |
| Anmeldetyp beim Identitätswechsel | Keine | Der Anmeldetyp beim Identitätswechsel. Weitere Informationen finden Sie in der [DwLogonType][dw] Dokumentation. |
| Nur digital signierte Skripts | False | Bei Verwendung von „True“ überprüft der Windows PowerShell-Connector, ob jedes Skript über eine gültige digitale Signatur verfügt. Stellen Sie bei Verwendung von „False“ sicher, dass die Windows PowerShell-Ausführungsrichtlinie des Synchronisierungsdienst-Servers „RemoteSigned“ oder „Unrestricted“ ist. |

**Allgemeines Modul**

Mithilfe des Connectors kann der Administrator ein gemeinsam genutztes Windows PowerShell-Modul in der Konfiguration speichern. Wenn der Connector ein Skript ausführt, wird das Windows PowerShell-Modul in das Dateisystem extrahiert, damit es von jedem Skript importiert werden kann.

Für Import-, Export- und Kennwortsynchronisierungsskripts wird das allgemeine Modul in den Ordner „MAData“ des Connectors extrahiert. Für Schema-, Validierungs-, Hierarchie- und Partitionsermittlungsskripts wird das allgemeine Modul in den Ordner „%TEMP%“ extrahiert. In beiden Fällen wird das Skript für das allgemeine Modul gemäß der dafür festgelegten Skriptnameneinstellung benannt.

Um ein Modul namens FIMPowerShellConnectorModule.psm1 aus dem Ordner MAData zu laden, verwenden Sie die folgende Anweisung aus:
`Import-Module (Join-Path -Path [Microsoft.MetadirectoryServices.MAUtils]::MAFolder -ChildPath "FIMPowerShellConnectorModule.psm1")`

Um ein Modul namens FIMPowerShellConnectorModule.psm1 aus dem Ordner "% Temp%" zu laden, verwenden Sie die folgende Anweisung aus:
`Import-Module (Join-Path -Path $env:TEMP -ChildPath "FIMPowerShellConnectorModule.psm1")`

**Parameterüberprüfung**

Das Überprüfungsskript ist ein optionales Windows PowerShell-Skript, mit dem die Gültigkeit der vom Administrator angegebenen Connector-Konfigurationsparameter sichergestellt werden kann. Mit dem Überprüfungsskript werden häufig Anmeldeinformationen für den Server oder die Verbindung sowie Konnektivitätsparameter überprüft. Das Überprüfungsskript wird nach Anpassung folgender Registerkarten und Dialogfelder aufgerufen:

- Konnektivität
- Globale Parameter
- Partitionskonfiguration

Das Überprüfungsskript erhält vom Connector folgende Parameter:

| Name | Datentyp | Beschreibung |
| --- | --- | --- |
| ConfigParameterPage | [ConfigParameterPage][cpp] | Die Konfigurationsregisterkarte oder das Konfigurationsdialogfeld, von der bzw. dem die Überprüfungsanforderung ausgelöst wurde. |
| ConfigParameters | [KeyedCollection][keyk] [Zeichenfolge [ConfigParameter][cp]] | Tabelle mit Konfigurationsparametern für den Connector. |
| Anmeldeinformationen | [PSCredential][pscred] | Enthält alle Anmeldeinformationen, die der Administrator auf der Registerkarte „Konnektivität“ eingegeben hat. |

Das Überprüfungsskript muss ein einzelnes ParameterValidationResult-Objekt an die Pipeline zurückgeben.

**Schema-Ermittlung**

Das Schema-Ermittlungsskript ist obligatorisch. Dieses Skript gibt die Objekttypen und Attribute sowie die Attributeinschränkungen zurück, die der Synchronisierungsdienst beim Konfigurieren von Attributflussregeln verwendet. Das Schema-Ermittlungsskript wird bei der Connector-Erstellung ausgeführt und füllt das Connector-Schema auf. Danach wird es dann von der Schemaaktualisierungsfunktion im Synchronisierungsdienst-Manager ausgeführt.

Das Schema-Ermittlungsskript erhält vom Connector folgende Parameter:

| Name | Datentyp | Beschreibung |
| --- | --- | --- |
| ConfigParameters | [KeyedCollection][keyk] [Zeichenfolge [ConfigParameter][cp]] | Tabelle mit Konfigurationsparametern für den Connector. |
| Anmeldeinformationen | [PSCredential][pscred] | Enthält alle Anmeldeinformationen, die der Administrator auf der Registerkarte „Konnektivität“ eingegeben hat. |

Das Skript muss eine einzelne zurückgeben [Schema][schema] an die Pipeline. Das Schemaobjekt besteht [SchemaType][schemaT] Objekte, die Objekttypen (z. B. Benutzer, Gruppen usw.) darstellen. Das SchemaType-Objekt enthält eine Auflistung von [SchemaAttribute][schemaA] Objekte, die die Attribute des Typs (z. B. Vorname, Nachname, Adresse usw.) darstellen.

**Zusätzliche Parameter**

Zusätzlich zu den bereits behandelten Standardkonfigurationseinstellungen können Sie auch benutzerdefinierte Konfigurationseinstellungen definieren, die speziell für die betreffende Connectorinstanz gelten. Diese Parameter können auf Connector-, Partitions- oder Ausführungsschrittebene angegeben werden und stehen über die entsprechenden Windows PowerShell-Skripts zur Verfügung. Benutzerdefinierte Konfigurationseinstellungen können in der Datenbank des Synchronisierungsdiensts im Nur-Text-Format gespeichert oder auch verschlüsselt werden. Sichere Konfigurationseinstellungen werden vom Synchronisierungsdienst automatisch bei Bedarf ver- und entschlüsselt.

Trennen Sie beim Angeben benutzerdefinierter Konfigurationseinstellungen die Namen der einzelnen Parameter durch ein Komma (,).

Um benutzerdefinierte Einstellungen aus einem Skript zuzugreifen, müssen Sie den Namen und Unterstrich (\) und den Bereich des Parameters (Global, Partitions- oder RunStep)-Suffix. Mit dem folgenden Codeausschnitt können Sie beispielsweise auf den globalen FileName-Parameter zugreifen: `$ConfigurationParameters["FileName_Global"].Value`

### Funktionen

Die Registerkarte „Funktionen“ des Verwaltungs-Agent-Designers dient zum Definieren des Verhaltens und der Funktionen des Connectors. Die auf dieser Registerkarte ausgewählten Optionen können nach der Erstellung des Connectors nicht mehr geändert werden. In der folgenden Tabelle werden die einzelnen Funktionseinstellungen aufgeführt:

![Funktionen](./media/active-directory-aadconnectsync-connector-powershell/capabilities.png)

| Funktion | Beschreibung |
| --- | --- |
| [Distinguished Name-Format][dnstyle] | Gibt an, ob der Connector Distinguished Names unterstützt, und wenn ja, in welchem Format. |
| [Exporttyp][exportT] | Bestimmt den Typ von Objekten, die dem Exportskript präsentiert werden. <li>AttributeReplace – enthält den vollständigen Satz von Werten für ein mehrwertiges Attribut, wenn das Attribut geändert wird.</li><li>AttributeUpdate – enthält nur die Deltas ein mehrwertiges Attribut, wenn das Attribut geändert wird.</li><li>MultivaluedReferenceAttributeUpdate - enthält einen vollständigen Satz von Werten für mehrwertige Attribute ohne Verweis und nur Deltas für mehrwertige Attribute.</li><li>ObjectReplace – enthält alle Attribute für ein Objekt, wenn alle Änderungen Attribut</li> |
| [Datennormalisierung][DataNorm] | Weist den Synchronisierungsdienst an, Ankerattribute vor der Bereitstellung für Skripts zu normalisieren. |
| [Objektbestätigung][oconf] | Dient zum Konfigurieren des Verhaltens in Verbindung mit ausstehenden Importen des Synchronisierungsdiensts. <li>Normal – Standardverhalten, das alle exportierte Änderungen mittels Import bestätigt werden erwartet</li><li>NoDeleteConfirmation – wird Wenn ein Objekt gelöscht wird, kein ausstehender Import generiert.</li><li>NoAddAndDeleteConfirmation – ist wenn ein Objekt erstellt oder gelöscht, es keine ausstehende importieren generiert.</li>
| DN als Anker verwenden | Wenn das Format für den Distinguished Name auf LDAP festgelegt ist, ist das Ankerattribut für den Connectorbereich gleichzeitig der Distinguished Name. |
| Parallele Vorgänge für mehrere Connectors | Ist diese Option aktiviert, können mehrere Windows PowerShell-Connectors gleichzeitig ausgeführt werden. |
| Partitionen | Ist diese Option aktiviert, unterstützt der Connector mehrere Partitionen und die Partitionsermittlung. |
| Hierarchie | Ist diese Option aktiviert, unterstützt der Connector eine hierarchische Struktur im LDAP-Stil. |
| Import aktivieren | Ist diese Option aktiviert, importiert der Connector Daten über Importskripts. |
| Deltaimport aktivieren | Ist diese Option aktiviert, kann der Connector Deltas aus den Importskripts anfordern. |
| Export aktivieren | Ist diese Option aktiviert, exportiert der Connector Daten über Exportskripts. |
| Vollständigen Export aktivieren | Ist diese Option aktiviert, unterstützen die Exportskripts das Exportieren des gesamten Connectorbereichs. Hierzu muss auch „Export aktivieren“ aktiviert sein.|
| Keine Referenzwerte im ersten Exportdurchlauf | Ist diese Option aktiviert, werden Verweisattribute in einem zweiten Exportdurchlauf exportiert. |
| Objektumbenennung aktivieren | Ist diese Option aktiviert, können Distinguished Names geändert werden. |
| Löschen/Hinzufügen als Ersetzung | Ist diese Option aktiviert, werden Lösch-/Hinzufügevorgänge als einzelne Ersetzung exportiert. |
| Kennwortvorgänge aktivieren | Ist diese Option aktiviert, werden Kennwortsynchronisierungsskripts unterstützt. |
| Kennwortexport im ersten Durchlauf aktivieren | Ist diese Option aktiviert, werden während der Bereitstellung festgelegte Kennwörter bei der Objekterstellung exportiert. |

### Globale Parameter

Auf der Registerkarte „Globale Parameter“ des Verwaltungs-Agent-Designers kann der Administrator die einzelnen vom Connector ausgeführten Windows PowerShell-Skripts sowie globale Werte für benutzerdefinierte Konfigurationseinstellungen konfigurieren, die auf der Registerkarte „Konnektivität“ definiert wurden.

**Partitionsermittlung**

Bei einer Partition handelt es sich um einen separaten Namespace innerhalb eines freigegebenen Schemas. So ist in Active Directory beispielsweise jede Domäne eine Partition innerhalb einer Gesamtstruktur. Eine Partition ist die logische Gruppierung für Import- und Exportvorgänge. Die Partition stellt den Kontext für Import- und Exportvorgänge dar, und alle Vorgänge müssen in diesem Kontext ausgeführt werden. Partitionen müssen eine Hierarchie in LDAP darstellen. Anhand des Distinguished Name einer Partition wird beim Importieren sichergestellt, dass sich alle zurückgegebenen Objekte innerhalb des Bereichs einer Partition befinden. Der Distinguished Name der Partition wird auch bei Metaverse-basierten Bereitstellungen für den Connectorbereich verwendet, um zu bestimmen, welcher Partition ein Objekt beim Exportieren zugeordnet werden soll.

Das Partitionsermittlungsskript erhält vom Connector folgende Parameter:

| Name | Datentyp | Beschreibung |
| --- | --- | --- |
| ConfigParameters  | [KeyedCollection][keyk][Zeichenfolge [ConfigParameter][cp]] | Tabelle mit Konfigurationsparametern für den Connector.
| Anmeldeinformationen | [PSCredential][pscred] | Enthält alle Anmeldeinformationen, die der Administrator auf der Registerkarte „Konnektivität“ eingegeben hat. |

Das Skript muss entweder ein einzelnes zurückgeben [Partition][part] Objekt oder eine Liste [T] der Partition-Objekte an die Pipeline.

**Hierarchieermittlung**

Das Hierarchieermittlungsskript wird nur verwendet, wenn für den Distinguished Name das LDAP-Format verwendet wird. Mithilfe des Skripts können Administratoren eine Gruppe von Containern suchen und auswählen, die bei Import- und Exportvorgängen als innerhalb oder außerhalb des gültigen Bereichs betrachtet werden sollen. Das Skript darf nur eine Liste mit Knoten bereitstellen, bei denen es sich um direkt untergeordnete Elemente des für das Skript angegebenen Stammknotens handelt.

Das Hierarchieermittlungsskript erhält vom Connector folgende Parameter:

| Name | Datentyp | Beschreibung |
| --- | --- | --- |
| ConfigParameters | [KeyedCollection][keyk][Zeichenfolge [ConfigParameter][cp]] | Tabelle mit Konfigurationsparametern für den Connector. |
| Anmeldeinformationen | [PSCredential][pscred] | Enthält alle Anmeldeinformationen, die der Administrator auf der Registerkarte „Konnektivität“ eingegeben hat. |
| ParentNode | [HierarchyNode][hn] | Der Stammknoten der Hierarchie, unter dem das Skript direkt untergeordnete Elemente zurückgeben soll. |

Das Skript muss entweder ein einzelnes untergeordnetes HierarchyNode-Objekt oder eine Liste (List[T]) mit untergeordneten HierarchyNode-Objekten an die Pipeline zurückgeben.

#### Importieren

Connectors, die Importvorgänge unterstützen, müssen drei Skripts implementieren:

**Import starten**

Das Skript „Import starten“ wird zu Beginn eines Importausführungsschritts ausgeführt. Im Rahmen dieses Schritts können Sie eine Verbindung mit Quellsystemen herstellen und alle Vorbereitungsschritte für den Import von Daten aus dem verbundenen System durchführen.

Das Skript „Import starten“ erhält vom Connector folgende Parameter:

| Name | Datentyp | Beschreibung |
| --- | --- | --- |
| ConfigParameters | [KeyedCollection][keyk][Zeichenfolge [ConfigParameter][cp]] | Tabelle mit Konfigurationsparametern für den Connector. |
| Anmeldeinformationen | [PSCredential][pscred] | Enthält alle Anmeldeinformationen, die der Administrator auf der Registerkarte „Konnektivität“ eingegeben hat. |
| OpenImportConnectionRunStep | [OpenImportConnectionRunStep][oicrs] | Informiert das Skript über Art der Importausführung (Delta oder vollständig) Partition, Hierarchie, Wasserzeichen und erwartete Seitengröße.
| Typen | [Schema][schema] | Schema für den zu importierenden Connectorbereich. |

Das Skript muss eine einzelne zurückgeben [OpenImportConnectionResults][oicres] an die Pipeline. Der folgende Beispielcode zeigt, wie ein OpenImportConnectionResults-Objekt an die Pipeline zurückgegeben wird:

`Write-Output (New-Object Microsoft.MetadirectoryServices.OpenImportConnectionResults)`

**Daten importieren**

Das Skript „Daten importieren“ wird vom Connector aufgerufen, bis das Skript angibt, dass keine weiteren Daten zum Importieren vorhanden sind und der Synchronisierungsdienst bei einem Deltaimport keine vollständigen Objektimporte anfordern muss. Die maximale Seitengröße des Windows PowerShell-Connectors beträgt 9.999 Objekte. Falls Ihr Skript mehr als 9.999 Objekte für den Import zurückgibt, ist eine Auslagerung erforderlich. Der Connector macht eine benutzerdefinierte Dateneigenschaft verfügbar, mit deren Hilfe Sie ein Wasserzeichen speichern können. Dadurch kann der Objektimport bei jedem Aufruf des Datenimportskripts an der Stelle fortgesetzt werden, an der er zuvor unterbrochen wurde.

Das Skript „Daten importieren“ erhält vom Connector folgende Parameter:

| Name | Datentyp | Beschreibung |
| --- | --- | --- |
| ConfigParameters | [KeyedCollection][keyk][Zeichenfolge [ConfigParameter][cp]] | Tabelle mit Konfigurationsparametern für den Connector. |
| Anmeldeinformationen | [PSCredential][pscred] | Enthält alle Anmeldeinformationen, die der Administrator auf der Registerkarte „Konnektivität“ eingegeben hat. |
| GetImportEntriesRunStep | [ImportRunStep][irs] | Enthält das Wasserzeichen (CustomData), das bei ausgelagerten Importvorgängen und bei Deltaimportvorgängen verwendet werden kann. |
| OpenImportConnectionRunStep | [OpenImportConnectionRunStep][oicrs] | Informiert das Skript über Art der Importausführung (Delta oder vollständig) Partition, Hierarchie, Wasserzeichen und erwartete Seitengröße. |
| Typen | [Schema][schema] | Schema für den zu importierenden Connectorbereich. |

Das Importskript Daten muss eine Liste schreiben [[CSEntryChange][csec]] an die Pipeline. Diese Sammlung enthält CSEntryChange-Attribute, die die einzelnen zu importierenden Objekte darstellen. Bei einem vollständigen Import muss diese Sammlung einen vollständigen Satz an CSEntryChange-Objekten enthalten, die über sämtliche Attribute für die einzelnen Objekte verfügen. Bei einem Deltaimport muss das CSEntryChange-Objekt entweder die Deltas auf Attributebene für die einzelnen zu importierenden Objekte oder eine vollständige Darstellung der geänderten Objekte (Ersetzungsmodus) enthalten.

**Import beenden**

Zum Abschluss der Importausführung wird das Skript „Import beenden“ ausgeführt. Dieses Skript dient zum Ausführen sämtlicher erforderlicher Bereinigungsaufgaben (wie etwa Trennen von Systemverbindungen, Reagieren auf Fehler usw.).

Das Skript „Import beenden“ erhält vom Connector folgende Parameter:

| Name | Datentyp | Beschreibung |
| --- | --- | --- |
| ConfigParameters | [KeyedCollection][keyk][Zeichenfolge [ConfigParameter][cp]] | Tabelle mit Konfigurationsparametern für den Connector. |
| Anmeldeinformationen | [PSCredential][pscred] | Enthält alle Anmeldeinformationen, die der Administrator auf der Registerkarte „Konnektivität“ eingegeben hat. |
| OpenImportConnectionRunStep | [OpenImportConnectionRunStep][oicrs] | Informiert das Skript über Art der Importausführung (Delta oder vollständig) Partition, Hierarchie, Wasserzeichen und erwartete Seitengröße. |
| CloseImportConnectionRunStep | [CloseImportConnectionRunStep][cecrs] | Informiert das Skript darüber, warum der Importvorgang beendet wurde. |

Das Skript muss eine einzelne zurückgeben [CloseImportConnectionResults][cicres] an die Pipeline. Im Beispielcode unten zeigt, wie ein CloseImportConnectionResults-Objekt an die Pipeline zurückgegeben wird:
`Write-Output (New-Object Microsoft.MetadirectoryServices.CloseImportConnectionResults)`

#### Export

Analog zur Importarchitektur müssen Connectors mit Exportunterstützung drei Skripts implementieren:

**Export starten**

Das Skript „Export starten“ wird zu Beginn eines Exportausführungsschritts ausgeführt. Im Rahmen dieses Schritts können Sie eine Verbindung mit Quellsystemen herstellen und alle Vorbereitungsschritte für den Export von Daten aus dem verbundenen System durchführen.

Das Skript „Export starten“ erhält vom Connector folgende Parameter:

| Name | Datentyp | Beschreibung |
| --- | --- | --- |
| ConfigParameters | [KeyedCollection][keyk][Zeichenfolge [ConfigParameter][cp]] | Tabelle mit Konfigurationsparametern für den Connector. |
| Anmeldeinformationen | [PSCredential][pscred] | Enthält alle Anmeldeinformationen, die der Administrator auf der Registerkarte „Konnektivität“ eingegeben hat. |
| OpenExportConnectionRunStep | [OpenExportConnectionRunStep][oecrs] | Informiert das Skript über Art der Exportausführung (Delta oder vollständig) Partition, Hierarchie und erwartete Seitengröße. |
| Typen | [Schema][schema] | Schema für den zu exportierenden Connectorbereich. |

Das Skript darf keine Ausgabe an die Pipeline zurückgeben.

**Daten exportieren**

Der Synchronisierungsdienst ruft das Skript „Daten exportieren“ so oft auf, bis alle ausstehenden Exportvorgänge verarbeitet wurden. Abhängig davon, ob die Anzahl der ausstehenden Exporte im Connectorbereich die Seitengröße des Connectors übersteigt und ob Verweisattribute oder Kennwörter vorhanden sind, wird das Skript „Daten exportieren“ unter Umständen mehrmals aufgerufen (ggf. auch für das gleiche Objekt).

Das Skript „Daten exportieren“ erhält vom Connector folgende Parameter:

| Name | Datentyp | Beschreibung |
| --- | --- | --- |
| ConfigParameters | [KeyedCollection][keyk][Zeichenfolge [ConfigParameter][cp]] | Tabelle mit Konfigurationsparametern für den Connector.|
| Anmeldeinformationen | [PSCredential][pscred] | Enthält alle Anmeldeinformationen, die der Administrator auf der Registerkarte „Konnektivität“ eingegeben hat.|
| CSEntries | IList[CSEntryChange][csec] | Liste mit allen Connectorbereichsobjekten mit ausstehenden Exportvorgängen, die während dieser Phase verarbeitet werden sollen. |
| OpenExportConnectionRunStep | [OpenExportConnectionRunStep][oecrs] | Informiert das Skript über Art der Exportausführung (Delta oder vollständig) Partition, Hierarchie und erwartete Seitengröße. |
| Typen | [Schema][schema] | Schema für den zu exportierenden Connectorbereich. |

Das Exportskript Daten zurückgeben muss ein [PutExportEntriesResults][peeres] an die Pipeline. Dieses Objekt muss nur dann Ergebnisinformationen für die einzelnen exportierten Connectors enthalten, wenn ein Fehler auftritt oder die Ankerattribute geändert werden.

Im Beispielcode unten zeigt, wie ein PutExportEntriesResults-Objekt an die Pipeline zurückgegeben wird:
`Write-Output (New-Object Microsoft.MetadirectoryServices.PutExportEntriesResults)`

**Export beenden**

Zum Abschluss der Exportausführung wird das Skript „Export beenden“ ausgeführt. Dieses Skript dient zum Ausführen sämtlicher erforderlicher Bereinigungsaufgaben (wie etwa Trennen von Systemverbindungen, Reagieren auf Fehler usw.).

Das Skript „Export beenden“ erhält vom Connector folgende Parameter:

| Name | Datentyp | Beschreibung |
| --- | --- | --- |
| ConfigParameters | [KeyedCollection][keyk][Zeichenfolge [ConfigParameter][cp]] | Tabelle mit Konfigurationsparametern für den Connector. |
| Anmeldeinformationen | [PSCredential][pscred] | Enthält alle Anmeldeinformationen, die der Administrator auf der Registerkarte „Konnektivität“ eingegeben hat. |
| OpenExportConnectionRunStep | [OpenExportConnectionRunStep][oecrs] | Informiert das Skript über Art der Exportausführung (Delta oder vollständig) Partition, Hierarchie und erwartete Seitengröße. |
| CloseExportConnectionRunStep | [CloseExportConnectionRunStep][cecrs] | Informiert das Skript darüber, warum der Exportvorgang beendet wurde. |

Das Skript darf keine Ausgabe an die Pipeline zurückgeben.

#### Kennwortsynchronisierung

Windows PowerShell-Connectors können als Ziel für Kennwortänderungen/-zurücksetzungen verwendet werden.

Das Kennwortskript erhält vom Connector folgende Parameter:

| Name | Datentyp | Beschreibung |
| --- | --- | --- |
| ConfigParameters | [KeyedCollection][keyk][Zeichenfolge [ConfigParameter][cp]] | Tabelle mit Konfigurationsparametern für den Connector. |
| Anmeldeinformationen | [PSCredential][pscred] | Enthält alle Anmeldeinformationen, die der Administrator auf der Registerkarte „Konnektivität“ eingegeben hat. |
| Partition | [Partition][part] | Die Verzeichnispartition mit dem CSEntry-Element. |
| CSEntry | [CSEntry][cse] | Der Connectorbereichseintrag für das Objekt, für das eine Kennwortänderung oder -zurücksetzung vorliegt. |
| OperationType | String | Gibt an, ob der Vorgang ein Zurücksetzen (**SetPassword**) oder eine Änderung (**ChangePassword**). |
| PasswordOptions | [PasswordOptions][pwdopt] | Flags zum Angeben des gewünschten Verhaltens bei der Kennwortzurücksetzung. Dieser Parameter ist nur verfügbar, wenn OperationType **SetPassword**. |
| OldPassword | String | Enthält das alte Kennwort des Objekts (bei Kennwortänderungen). Dieser Parameter ist nur verfügbar, wenn OperationType **ChangePassword**. |
| NewPassword | String | Enthält das neue Kennwort des Objekts, das durch das Skript festgelegt werden soll. |

Vom Kennwortskript wird keine Ergebnisrückgabe an die Windows PowerShell-Pipeline erwartet. Bei einem Fehler im Kennwortskript muss das Skript eine der folgenden Ausnahmen auslösen, um den Synchronisierungsdienst über das Problem zu informieren:

- [PasswordPolicyViolationException][pwdex1] – wird ausgelöst, wenn das Kennwort nicht die Kennwortrichtlinie im verbundenen System erfüllt.
- [PasswordIllFormedException][pwdex2] – wird ausgelöst, wenn das Kennwort nicht für das verbundene System zulässig ist.
- [PasswordExtension][pwdex3] – für alle anderen Fehler im Skript Kennwort ausgelöst.

## Beispielconnectors

Eine vollständige Übersicht der verfügbaren beispielconnectors finden Sie unter [Windows PowerShell-Connector-Beispiel-Connector-Auflistung][samp].

## Sonstige Hinweise

### Zusätzliche Konfiguration für den Identitätswechsel

Gewähren Sie dem Benutzer, dessen Identität angenommen wird, auf dem Synchronisierungsdienst-Server die folgenden Berechtigungen:

Lesezugriff auf die folgenden Registrierungsschlüssel:

- \Software\Microsoft\PowerShell HKEY_USERS\\ [SynchronizationServiceServiceAccountSID]
- \Environment HKEY_USERS\\ [SynchronizationServiceServiceAccountSID]

Führen Sie zur Ermittlung der Sicherheits-ID (SID) des Dienstkontos des Synchronisierungsdiensts die folgenden PowerShell-Befehle aus:

```
$account = New-Object System.Security.Principal.NTAccount "<domain>\<username>"
$account.Translate([System.Security.Principal.SecurityIdentifier]).Value
```

Lesezugriff auf die folgenden Dateisystemordner:

- %ProgramFiles%\Microsoft Forefront Identity Manager\2010\Synchronization Service\Extensions
- %ProgramFiles%\Microsoft Forefront Identity Manager\2010\Synchronization Service\ExtensionsCache
- %ProgramFiles%\Microsoft Forefront Identity Manager\2010\Synchronization Service\MaData\ < ConnectorName >

Ersetzen Sie den Namen des Windows PowerShell-Connectors für die <ConnectorName> Platzhalter.

## Problembehandlung

-   Informationen zum Aktivieren der Protokollierung, um den Connector zu beheben, finden Sie unter der [How to Enable ETW-Tracing für Connectors](http://go.microsoft.com/fwlink/?LinkId=335731).

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[cpp]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.configparameterpage.aspx
[keyk]: https://msdn.microsoft.com/library/ms132438.aspx
[cp]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.configparameter.aspx
[pscred]: https://msdn.microsoft.com/library/system.management.automation.pscredential.aspx
[schema]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.schema.aspx
[schemaT]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.schematype.aspx
[schemaA]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.schemaattribute.aspx
[dnstyle]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.madistinguishednamestyle.aspx
[exportT]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.maexporttype.aspx
[DataNorm]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.manormalizations.aspx
[oconf]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.maobjectconfirmation.aspx
[dw]: https://msdn.microsoft.com/library/windows/desktop/aa378184.aspx
[part]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.partition.aspx
[hn]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.hierarchynode.aspx
[oicrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.openimportconnectionrunstep.aspx
[cecrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.closeexportconnectionrunstep.aspx
[oicres]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.openimportconnectionresults.aspx
[cecrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.closeexportconnectionrunstep.aspx
[cicres]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.closeimportconnectionresults.aspx
[oecrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.openexportconnectionrunstep.aspx
[irs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.importrunstep.aspx
[cse]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.csentry.aspx
[csec]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.csentrychange.aspx
[peeres]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.putexportentriesresults.aspx
[pwdopt]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordoptions.aspx
[pwdex1]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordpolicyviolationexception.aspx
[pwdex2]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordillformedexception.aspx
[pwdex3]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordextensionexception.aspx
[samp]: http://go.microsoft.com/fwlink/?LinkId=394291


