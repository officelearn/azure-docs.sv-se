<properties
   pageTitle="Azure AD Connect-Synchronisierung: Lotus Domino-Connector"
   description="Dieser Artikel beschreibt die Konfiguration des Lotus Domino-Connectors von Microsoft."
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

# Technische Referenz für den Lotus Domino-Connector

Dieser Artikel beschreibt den Lotus Domino-Connector. Der Artikel bezieht sich auf folgende Produkte:

- Microsoft Identity Manager 2016 (MIM2016)
- Forefront Identity Manager 2010 R2 (FIM2010R2)
    -   Hotfix 4.1.3461.0 müssen verwenden oder höher [KB2870703](https://support.microsoft.com/kb/2870703).

Der Connector steht als Download für MIM2016 und FIM2010R2 der [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=717495).

## Übersicht über den Lotus Domino-Connector

Der Lotus Domino-Connector ermöglicht die Integration des Synchronisierungsdiensts in den Lotus Domino-Server von IBM.

Im Anschluss finden Sie einen allgemeinen Überblick über die von der aktuellen Connectorversion unterstützten Features:

| Funktion | Support |
| --- | --- |
| Verbundene Datenquelle | Server: <li>Lotus Domino 8.5.x</li><li>Lotus Domino 9.x</li>Client:<li>Lotus Notes-8.5.x</li><li>Lotus Notes-9.x</li> |
| Szenarios | <li>Objektlebenszyklusverwaltung</li><li>Gruppenverwaltung</li><li>Kennwortverwaltung</li> |
| Vorgänge | <li>Vollständiger Import und Deltaimport</li><li>Export</li><li>Festlegen und das Kennwort für den HTTP-Kennwort ändern</li> |
| Schema | <li>Person (Roaming-Benutzer, Kontakt (Personen ohne Zertifikat))</li><li>Gruppe</li><li>Ressource (Raum-Ressource Online Besprechung)</li><li>Mail in Datenbank</li><li>Dynamische Erkennung von Attributen für unterstützte Objekte</li> |

Der Lotus Domino-Connector nutzt für die Kommunikation mit dem Lotus Domino-Server den Lotus Notes-Client. Daher muss auf dem Synchronisierungsserver ein unterstützter Lotus Notes-Client installiert sein. Die Kommunikation zwischen Client und Server wird über die .NET-Interop-Schnittstelle für Lotus Notes (Interop.domino.dll) implementiert. Diese Schnittstelle ermöglicht die Kommunikation zwischen der Microsoft-.NET-Plattform und dem Lotus Notes-Client und unterstützt den Zugriff auf Lotus Domino-Dokumente und -Sichten. Abhängig von der ausgewählten Deltaimportmethode kann für den Deltaimport ggf. auch die systemeigene C++-Schnittstelle verwendet werden.

### Voraussetzungen

Zur Verwendung des Connectors muss auf dem Synchronisierungsserver neben den weiter oben ggf. erwähnten Hotfixes Folgendes vorhanden sein:

- Microsoft .NET 4.5.2 Framework oder eine höhere Version
- Auf dem Synchronisierungsserver muss der Lotus Notes-Client installiert sein.
- Für den Lotus Domino-Connector muss auf dem Domino-Verzeichnisserver die standardmäßige Lotus Domino-LDAP-Schemadatenbank (schema.nsf) vorhanden sein. Vergewissern Sie sich, dass sie vorhanden ist. Sollte sie nicht vorhanden sein, können Sie sie installieren, indem Sie auf dem Domino-Server den LDAP-Dienst ausführen oder neu starten.

### Berechtigungen für die verbundene Datenquelle

Zum Ausführen der unterstützten Vorgänge des Lotus Domino-Connectors müssen Sie folgenden Gruppen angehören:

- Administratoren mit Vollzugriff
- Administratoren
- Datenbankadministratoren

Die folgende Tabelle gibt Aufschluss über die erforderlichen Berechtigungen für die einzelnen Vorgänge:

| Vorgang | Zugriffsrechte |
| --- | --- |
| Importieren | <li>Öffentliche Dokumente lesen</li><li> Access-Administrator-Vollständig (Wenn Sie Mitglied der Administratorengruppe vollständigen Zugriff sind, automatisch müssen den effektiven Zugriff auf in der ACL.)</li> |
| Exportieren und Kennwort festlegen | Effektiver Zugriff: <li>Erstellen eines Dokuments</li><li>Löschen von Dokumenten</li><li>Öffentliche Dokumente lesen</li><li>Öffentliche Dokumente schreiben</li><li>Die Replikation oder Kopie Dokumente</li>Zusätzlich zu den oben genannten Zugriff müssen für Export-Vorgänge die folgenden Rollen zugewiesen werden: <li>CreateResource</li><li>GroupCreator</li><li>GroupModifier</li><li>Benutzer</li><li>UserModifier</li> |

### Direkte Vorgänge und AdminP

Vorgänge werden entweder direkt oder aber über den AdminP-Prozess an das Domino-Verzeichnis weitergeleitet. Die folgenden Tabellen enthalten alle unterstützten Objekte, Vorgänge und (sofern zutreffend) die dazugehörige Implementierungsmethode:

**Primäres Adressbuch**

| Object | Erstellen | Aktualisieren | Löschen |
| --- | --- | --- | --- |
| Person | AdminP | Direkt | AdminP |
| Gruppe | AdminP | Direkt | AdminP |
| Mail-In-Datenbank | Direkt | Direkt | Direkt |
| Ressource | AdminP | Direkt | AdminP |

**Sekundäres Adressbuch**

| Object | Erstellen | Aktualisieren | Löschen |
| --- | --- | --- | --- |
| Person | – | Direkt | Direkt |
| Gruppe | Direkt | Direkt | Direkt |
| Mail-In-Datenbank | Direkt | Direkt | Direkt |
| Ressource | – | – | – |

Beim Erstellen einer Ressource wird ein Notes-Dokument erstellt. Analog dazu wird beim Löschen einer Ressource auch das Notes-Dokument gelöscht.

### Ports und Protokolle

Der IBM Lotus Notes-Client und die Domino-Server kommunizieren per NRPC (Notes Remote Procedure Call, NRPC). Für NRPC muss TCP/IP verwendet werden. Die Standardportnummer 1352 kann vom Domino-Administrator geändert werden.

### Nicht unterstützt

Die folgenden Vorgänge werden von der aktuellen Version des Lotus Domino-Connectors nicht unterstützt:

- Verschieben des Postfachs zwischen Servern

## Erstellen eines neuen Connectors

### Installieren und Konfigurieren der Clientsoftware

Lotus Notes müssen installiert sein, auf dem Server **vor** der Connector installiert ist.

Bei der Installation stellen Sie sicher, dass Sie führen einen **einzelne Benutzer installieren**. Die Standardeinstellung **mehrere Benutzer** funktioniert nicht.

![Notes1](./media/active-directory-aadconnectsync-connector-domino/notes1.png)

Installieren Sie auf der Seite Features nur die erforderlichen Lotus Notes-Features und **Client einmalige Anmeldung**. Einmaliges Anmelden ist für die Anmeldung des Connectors beim Domino-Server erforderlich.

![Notes2](./media/active-directory-aadconnectsync-connector-domino/notes2.png)

**Hinweis:** Lotus Notes muss gestartet werden, sobald Sie mit einem Benutzer, die auf dem gleichen Server wie das Konto als Dienstkonto für den Connector verwendet.

### Connectorerstellung

Erstellen Sie einen Connector für Lotus Domino, in **Synchronisierungsdienst** wählen **Management Agent** und **Erstellen**. Wählen Sie die **Lotus Domino (Microsoft)** Connectors.

![CreateConnector](./media/active-directory-aadconnectsync-connector-domino/createconnector.png)

Wenn Ihre Version des Synchronisierungsdiensts festlegen anbieten **Architektur**, sicherzustellen, dass der Connector auf den Standardwert festgelegt ist, für die Ausführung im **Prozess**.

### Konnektivität

Geben Sie auf der Konnektivitätsseite den Namen des Lotus Domino-Servers sowie die Anmeldeinformationen an.

![Konnektivität](./media/active-directory-aadconnectsync-connector-domino/connectivity.png)

Die Domino-Servereigenschaft unterstützt zwei Formate für den Server:

- Servername
- Servername/Verzeichnisname

Die **ServerName/Verzeichnisname** Format ist das bevorzugte Format für dieses Attribut aus, da sie schnellere Antwort bereitstellt, wenn der Connector den Domino-Server kontaktiert.

Die angegebene Benutzer-ID-Datei wird in der Konfigurationsdatenbank des Synchronisierungsdiensts gespeichert.

Für **Delta Import** Ihnen diese Optionen zur Verfügung:

- **Keine**. Der Connector führt keine Deltaimporte aus.
- **Hinzufügen/Aktualisieren**. Der Connector führt Deltaimporte für Hinzufüge- und Aktualisierungsvorgänge aus. Zum Löschen einer **Vollständiger Import** Vorgang erforderlich ist. Bei diesem Vorgang wird das .NET-Interop von Notes verwendet.
- **Hinzufügen/Aktualisieren/löschen**. Der Connector führt Deltaimporte für Hinzufüge-, Aktualisierungs- und Löschvorgänge aus. Bei diesem Vorgang werden die systemeigenen C++-Schnittstellen von Notes verwendet.

In **Schemaoptionen** Sie haben die folgenden Optionen:

- **Standardschema**. Die Standardoption. Der Connector erkennt das Schema über den Domino-Server.
- **DSML-Schema**. Wird nur verwendet, wenn der Domino-Server das Schema nicht verfügbar macht. In diesem Fall können Sie eine DSML-Datei mit dem Schema erstellen und das Schema stattdessen importieren. Weitere Informationen über DSML finden Sie unter [OASIS](https://www.oasis-open.org/committees/tc_home.php?wg_abbrev=dsml).

Wenn Sie auf „Weiter“ klicken, werden die Konfigurationsparameter für Benutzer-ID und Kennwort überprüft.

### Globale Parameter

Die Seite mit den globalen Parametern dient zum Konfigurieren der Zeitzone und der Option für Import-/Exportvorgänge.

![Globale Parameter](./media/active-directory-aadconnectsync-connector-domino/globalparameters.png)

Die **Domino Serverzeitzone** Parameter definiert den Speicherort der Domino-Server.

Diese Konfigurationsoption ist erforderlich, um die Unterstützung von **deltaimport** Vorgänge weil dadurch der Synchronisierungsdienst Ermitteln von Änderungen zwischen der letzten beiden Importe.

#### Importeinstellungen, Methode

Die **vollständigen Import Ausführen von** gibt es drei Optionen:

- Suche
- Sicht (empfohlen)

**Suche** wird mithilfe der Indizierung in Domino, aber es ist üblich, dass die Indizes werden nicht in Echtzeit aktualisiert, und die vom Server zurückgegebenen Daten nicht immer richtig ist.  Für Systeme mit vielen Änderungen ist diese Option in der Regel nicht sonderlich geeignet und führt in bestimmten Fällen sogar zu ungewollten Löschvorgängen. Allerdings **Suche** ist schneller als **Ansicht**.

**Ansicht** wird empfohlen, da sie den richtigen Zustand der Daten bereitstellt. Sie ist geringfügig langsamer als „Suche“.

#### Erstellen virtueller Contact-Objekte

Die **ermöglichen das Erstellen des Objekts \_Contact** gibt es drei Optionen:

- Keine
- Werte ohne Verweis
- Verweiswerte und Werte ohne Verweis

In Domino können Verweisattribute viele verschiedene Formate für den Verweis auf andere Objekte enthalten. Um verschiedene Varianten darstellen können implementiert der Connector \_Contact, auch bekannt als als virtuelle Kontakte (VC). Diese Objekte werden erstellt, um sie mit vorhandenen Metaverse-Objekten verknüpfen, als neue Objekte projizieren und dadurch Verweisattribute erhalten zu können.

Diese Einstellung aktivieren und wenn der Inhalt des ein Verweisattribut nicht DN-Format ist, wird ein \_Contact-Objekt erstellt. Ein Mitgliedsattribut einer Gruppe kann beispielsweise SMTP-Adressen enthalten. In Verweisattributen können auch „shortName“ und andere Attribute vorhanden sein. Wählen Sie für dieses Szenario **ohne Verweis Werte**. Dies ist die am häufigsten verwendete Einstellung für Domino-Implementierungen.

Wenn Lotus Domino konfiguriert ist, haben Sie separate Adressbücher mit verschiedenen distinguished Names, die das gleiche Objekt darstellt, ist es möglich, auch \_Contact-Objekte für alle Referenzwerte erstellen, die in einem Adressbuch gefunden werden. Wählen Sie für dieses Szenario die **Verweis und nicht-Referenzwerte** Option.

Wenn Sie mehrere Werte im Attribut haben **FullName** in Domino dann auch soll die Erstellung von virtuellen Kontakte zu aktivieren, damit Verweise aufgelöst werden können. Dieses Attribut kann nach einer Zusammenführung oder Trennung mehrere Werte aufweisen. Aktivieren Sie in diesem Szenario das Kontrollkästchen **Erstellen... aktivieren, wenn „FullName“ mehrere Werte besitzt**.

Verknüpfen über die richtigen Attribute, würden die \_Contact Objekte zum Metaverse-Objekt verbunden werden.

Diese Objekte müssen VC = \_Contact ihre DN hinzugefügt.

#### Importeinstellungen, Konfliktobjekt

**Konfliktobjekt ausschließen**

In einer großen Domino-Implementierung kann es aufgrund von Replikationsproblemen vorkommen, dass mehrere Objekte den gleichen DN besitzen. In diesen Fällen liegen dem Connector zwei Objekte mit unterschiedlichen UniversalID-Werten, aber gleichem DN vor. Dies hätte die Erstellung eines kurzlebigen Objekts im Connectorbereich zur Folge. Der Connector kann Objekte ignorieren, die in Domino als Replikationsopfer ausgewählt wurden. Es empfiehlt sich, dieses Kontrollkästchen nicht zu deaktivieren.

#### Exporteinstellungen

Wenn die Option **verwenden AdminP aktualisiert** Export der Attribute, z. B. Element, wird ein direkter Aufruf und verwendet nicht den AdminP Prozess deaktiviert ist. Diese Option sollte nur verwendet werden, wenn AdminP nicht konfiguriert wurde, um die referenzielle Integrität zu gewährleisten.

#### Routinginformationen

In Domino können Routinginformationen als DN-Suffix in ein Verweisattribut eingebettet werden. Beispielsweise kann das Mitgliedsattribut einer Gruppe enthalten **CN=example/organization@ABC**. Das Suffix „@ABC“ ist die Routinginformation. Routinginformationen dienen in Domino zum Senden von E-Mails an das richtige Domino-System. Hierbei kann es sich um ein System in einer anderen Organisation handeln. Im Feld für Routinginformationen können Sie die Routing-Suffixe angeben, die innerhalb der Organisation im Connectorbereich verwendet werden. Wird einer dieser Werte als Suffix in einem Verweisattribut gefunden, wird die Routinginformation aus dem Verweis entfernt, sodass er dem DN für das Objekt im Connectorbereich entspricht. Wenn das routing Suffix auf einen Wert für eine der angegebenen zugeordnet werden kann, wird ein \_Contact-Objekt erstellt. Mit dieser \_Contact-Objekte erstellt werden **RO = @<RoutingSuffix>** in den DN eingefügt. Für diese \_Contact Objekte die folgenden Attribute werden zu verknüpfen, ein tatsächliches Objekt bei Bedarf ebenfalls hinzugefügt: \_routingName, \_contactName, \_displayName und UniversalID.

#### Zusätzliche Adressbücher

Wenn Sie keinen **Nebenstellen** installiert, die wird der Name des sekundären Adressbüchern und dann können Sie diese Adressbücher manuell eingeben.

#### Mehrwertige Transformation

Viele Attribute in Lotus Domino sind mehrwertig. Die entsprechenden Metaverse-Attribute sind in der Regel einwertig. Durch Konfigurieren der Import- und Exportvorgangsoption kann der Connector zur erforderlichen Umwandlung der betroffenen Attribute beitragen und so die Konfiguration vereinfachen.

**Export**

Die Exportvorgangsoption unterstützt zwei Modi:

- Element anfügen
- Element ersetzen

**Element ersetzen** – bei Auswahl dieser Option der Connector wird immer die aktuellen Werte des Attributs in Domino und ersetzt werden mit den angegebenen Werten. Die angegebenen Werte können ein- oder mehrwertig sein.

Beispiel:
Das Assistentenattribut eines Personenobjekts hat folgende Werte:

- CN=Greg Winston/OU= Contoso /O=Americas,NAB=names.nsf
- CN=John Smith/OU= Contoso /O=Americas,NAB=names.nsf

Wenn ein neuer Assistent mit dem Namen **David Alexander** erhält diese Person-Objekt, das Ergebnis ist:

- CN=David Alexander/OU= Contoso /O=Americas,NAB=names.nsf

**Fügen Sie Element** – Wenn Sie diese Option Aktivieren der Connector die vorhandenen Werte des Attributs in Domino beibehalten und neue Werte am Anfang der Liste einfügen.

Beispiel:
Das Assistentenattribut eines Personenobjekts hat folgende Werte:

- CN=Greg Winston/OU= Contoso /O=Americas,NAB=names.nsf
- CN=John Smith/OU= Contoso /O=Americas,NAB=names.nsf

Wenn ein neuer Assistent mit dem Namen **David Alexander** erhält diese Person-Objekt, das Ergebnis ist:

- CN=David Alexander/OU= Contoso /O=Americas,NAB=names.nsf
- CN=Greg Winston/OU= Contoso /O=Americas,NAB=names.nsf
- CN=John Smith/OU= Contoso /O=Americas,NAB=names.nsf

**Importieren**

Die Exportvorgangsoption unterstützt zwei Modi:

- Standard
- Mehrwertig zu einwertig

**Standard-** – Wenn Sie die Standardoption auswählen aller Werte aller Attribute importiert werden.

**Mehrwertige auf einzelnen Wert** – bei Auswahl dieser Option ist ein mehrwertiges Attribut in ein einwertiges Attribut konvertiert. Sind mehrere Werte vorhanden, wird der oberste (üblicherweise der neueste) Wert verwendet.

Beispiel:

Das Assistentenattribut eines Personenobjekts hat folgende Werte:

- CN=David Alexander/OU= Contoso /O=Americas,NAB=names.nsf
- CN=Greg Winston/OU= Contoso /O=Americas,NAB=names.nsf
- CN=John Smith/OU= Contoso /O=Americas,NAB=names.nsf

Das aktuellste Update zu diesem Attribut wird **David Alexander**. Da die Option Import-Vorgang auf mehrwertig auf einzelnen Wert festgelegt ist, importiert Connector nur **David Alexander** in den Connectorbereich.

Die Logik für die Konvertierung mehrwertiger Attribute in einwertige Attribute gilt nicht für das Mitgliedsattribut eines Gruppenobjekts und für das FullName-Attribut eines Personenobjekts.

Es ist auch möglich, Import- und Exporttransformationsregeln für mehrwertige Attribute pro Attribut (als Ausnahme für die globale Regel) zu konfigurieren. Geben Sie zum Konfigurieren dieser Option [Objekttyp] aus. [Attributname] in die **Importieren Ausschlussliste Attribut** und **Exportieren Ausschlussliste Attribut** Textfelder. Wenn Sie also beispielsweise „Person.Assistant“ eingeben und das globale Flag auf den Import aller Werte festgelegt ist, wird für den Assistenten nur der erste Wert importiert.

#### Zertifizierer

Alle Unternehmen/Organisationseinheiten werden aufgeführt. Zum Exportieren von Personenobjekten in das primäre Adressbuch wird ein Zertifizierer mit dazugehörigem Kennwort benötigt.

Die **Kennwort für alle Certifers(s)** kann verwendet werden, wenn alle Zertifizierer dasselbe Kennwort haben. In diesem Fall können Sie hier das Kennwort eingeben und nur die Zertifiziererdatei angeben.

Bei reinen Importvorgängen muss kein Zertifizierer angegeben werden.

### Konfigurieren der Bereitstellungshierarchie

Beim Konfigurieren des Lotus Domino-Connectors können Sie diese Seite überspringen. Der Lotus Domino-Connector unterstützt keine hierarchische Bereitstellung.

![Bereitstellungshierarchie](./media/active-directory-aadconnectsync-connector-domino/provisioninghierarchy.png)

### Konfigurieren von Partitionen und Hierarchien

Wenn Sie Partitionen und Hierarchien konfigurieren, müssen Sie das primäre Adressbuch namens „NAB=names.nsf“ auswählen.

Zusätzlich zum primären Adressbuch können Sie auch sekundäre Adressbücher auswählen (sofern vorhanden).

![Partitionen](./media/active-directory-aadconnectsync-connector-domino/partitions.png)

### Auswählen von Attributen

Wenn Sie Ihre Attribute konfigurieren, müssen Sie alle Attribute, die mit dem Präfix auswählen **\_MMS\_**. Diese Attribute sind erforderlich, wenn Sie neue Objekte für Lotus Domino bereitstellen.

![Attribute](./media/active-directory-aadconnectsync-connector-domino/attributes.png)

## Objektlebenszyklusverwaltung

Dieser Abschnitt enthält eine Übersicht über die verschiedenen Objekte in Domino.

### Personenobjekte

Das Personenobjekt stellt Benutzer in der Organisation und in Organisationseinheiten dar. Zusätzlich zu den Standardattributen kann der Domino-Administrator einem Personenobjekt auch benutzerdefinierte Attribute hinzufügen. Ein Personenobjekt muss mindestens alle erforderlichen Attribute enthalten. Eine vollständige Liste der erforderlichen Attribute, finden Sie unter [Lotus Notes-Eigenschaften](#lotus-notes-properties). Die Registrierung eines Personenobjekts setzt Folgendes voraus:

- Das Adressbuch („names.nsf“) muss definiert worden sein, und es muss sich um das primäre Adressbuch handeln.
- Sie müssen über die O-/OE-Zertifizierer-ID und das Kennwort für die Registrierung eines bestimmten Benutzers in der Organisation/Organisationseinheit verfügen.
- Sie müssen einen spezifischen Satz von Lotus Notes-Eigenschaften für ein Personenobjekt definieren. Diese werden für die Bereitstellung des Personenobjekts verwendet. Weitere Informationen finden Sie im Abschnitt [Lotus Notes-Eigenschaften](#lotus-notes-properties) Weiter unten in diesem Dokument.
- Das ursprüngliche HTTP-Kennwort für eine Person ist ein Attribut und wird bei der Bereitstellung festgelegt.
- Für Personenobjekte werden folgende Typen unterstützt:
    1. Normaler Benutzer mit einer Mail- und einer Benutzer-ID-Datei
    2. Roamingbenutzer (ein normaler Benutzer mit allen Roamingdatenbankdateien)
    3. Kontakte (Benutzer ohne ID-Datei)

Personen (mit Ausnahme von Kontakten) können weiter in US-Benutzer und internationale Benutzer gruppiert werden, gemäß der Definition durch den Wert der Eigenschaft \_MMS\_IDRegType. Hierbei handelt es sich um die Personen, die mithilfe des Notes-Clients auf Lotus Domino-Server oder die Datenbank zugreifen, eine Notes-ID besitzen und über ein Personendokument verfügen. Bei Verwendung von Notes Mail besitzen sie außerdem eine Maildatei. Zur Aktivierung muss der Benutzer registriert werden. Weitere Informationen finden Sie unter:

- [Einrichten von Notes-Benutzern](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_SETTING_UP_NOTES_USERS.html)
- [Benutzerregistrierung](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_REGISTERING_USERS.html)
- [Verwalten von Benutzern](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_MANAGING_USERS_5151.html)
- [Umbenennen von Benutzern](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_RENAMING_A_USER_AUTOMATICALLY.html)

Alle diese Vorgänge werden in Lotus Domino ausgeführt und anschließend in den Synchronisierungsdienst importiert.

### Ressourcen und Räume

Eine Ressource ist eine andere Art von Datenbank in Lotus Domino. Bei Ressourcen kann es sich etwa um Konferenzräume mit unterschiedlichen Arten von Ausstattung (beispielsweise Projektoren) handeln. Der Lotus Domino-Connector unterstützt Unterarten von Ressourcen. Diese werden auf der Grundlage des Ressourcentypattributs definiert:

| Ressourcentyp | Ressourcentypattribut |
| --- | --- |
| Raum | 1 |
| Ressource (Sonstiges) | 2 |
| Onlinebesprechung | 3 |

Für die ordnungsgemäße Verwendung des Ressourcenobjekttyps ist Folgendes erforderlich:

- Die Ressourcenreservierungsdatenbank muss bereits auf dem verbundenen Domino-Server vorhanden sein.
- Der Standort für die Ressource muss bereits definiert sein.

Die Ressourcenreservierungsdatenbank enthält drei Arten von Dokumenten:

- Standortprofil
- Ressource
- Reservierung

Weitere Informationen zum Einrichten der Reservierung von Ressourcen-Datenbank finden Sie unter [Einrichten der Ressource Reservierungen Datenbank](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_USING_GROUPS_OVER.html).

**Erstellen, Aktualisieren und Löschen von Ressourcen**

Die Erstellungs-, Aktualisierungs- und Löschvorgänge werden vom Lotus Domino-Connector in der Ressourcenreservierungsdatenbank ausgeführt. Dadurch werden Ressourcen als Dokumente in „Names.nsf“ (dem primären Adressbuch) erstellt. Weitere Informationen zum Bearbeiten und Löschen von Ressourcen finden Sie unter [Bearbeiten und Löschen von Dokumenten Ressource](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_EDITING_AND_DELETING_RESOURCE_DOCUMENTS.html).

**Importieren und Exportieren von Ressourcen**

Die Ressourcen können genau wie andere Objekttypen in den Synchronisierungsdienst importiert und daraus exportiert werden. Der Objekttyp muss beim Konfigurieren als Ressource ausgewählt werden. Halten Sie für einen erfolgreichen Exportvorgang Details zu Ressourcentyp, Konferenzdatenbank und Standortname bereit.

### Mail-In-Datenbanken

Eine Mail-In-Datenbank ist eine Datenbank für den Empfang von E-Mails. Es handelt sich bei ihr um ein Lotus Domino-Postfach (auf der Mailvorlage basierende Notes-Datenbank), die keinem bestimmten Lotus Domino-Benutzerkonto zugeordnet ist (also keine eigene ID-Datei und kein eigenes Kennwort besitzt). Einer Mail-In-Datenbank sind eine eindeutige Benutzer-ID (Kurzname) und eine eigene E-Mail-Adresse zugeordnet.

Jeder Benutzer benötigt nur ein einzelnes Lotus Domino-Konto. Wenn muss für ein separates Postfach mit seiner eigenen e-Mail-Adresse, die von verschiedenen Benutzern gemeinsam verwendet werden kann (z. B.: group@contoso.com), wird eine e-Mail-Nachrichten in Datenbank anstelle eines zusätzlichen Notes-Kontos erstellt. Der Zugriff auf dieses Postfach wird mithilfe der entsprechenden Zugriffssteuerungsliste (Access Control List, ACL), gesteuert. Diese enthält die Namen der Notes-Benutzer, die unter Verwendung ihrer eigenen ID-Dateien und Kennwörter zum Öffnen des Postfachs berechtigt sind. Für den Zugriff auf eine Mail-In-Datenbank wird kein separates Kennwort benötigt.

Eine Liste der erforderlichen Attribute, finden Sie im Abschnitt [verbindliche Attribute](#mandatory-attributes) Weiter unten in diesem Artikel.

Wenn eine Datenbank für den E-Mail-Empfang konzipiert wird, wird in Lotus Domino ein Mail-In-Datenbankdokument erstellt. Dieses Dokument muss im Domino-Verzeichnis jedes Servers vorhanden sein, auf dem eine Kopie der Datenbank gespeichert wird. Eine ausführliche Beschreibung zum Erstellen eines Dokuments Mail in Datenbank finden Sie unter [Erstellen eines Dokuments Mail In Datenbank](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_CREATING_A_MAILIN_DATABASE_DOCUMENT_FOR_A_NEW_DATABASE_OVERVIEW.html).

Vor dem Erstellen einer Mail-In-Datenbank muss die Datenbank bereits auf dem Domino-Server vorhanden sein (sollte vom Lotus-Administrator erstellt worden sein).

### Gruppenverwaltung

Eine ausführliche Übersicht über die Lotus Domino-Gruppenverwaltung finden Sie in den folgenden Ressourcen:

- [Verwenden von Gruppen](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_USING_GROUPS_OVER.html)
- [Erstellen einer Gruppe](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_CREATING_AND_MODIFYING_GROUPS_STEPS_MIDTOPIC_55038956829238418.html)
- [Erstellen und Ändern von Gruppen](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_CREATING_AND_MODIFYING_GROUPS_STEPS.html)
- [Verwalten von Gruppen](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_MANAGING_GROUPS_1804.html)
- [Umbenennen einer Gruppe](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_RENAMING_A_GROUP_STEPS.html)

### Kennwortverwaltung

Für einen registrierten Lotus Domino-Benutzer gibt es zwei Arten von Kennwörtern:

1. Benutzerkennwort (gespeichert in der Datei „User.id“)
2. Internet-/HTTP-Kennwort

Der Lotus Domino-Connector unterstützt nur Vorgänge mit HTTP-Kennwort.

Zur Verwaltung von Kennwörtern müssen Sie im Verwaltungs-Agent-Designer die Kennwortverwaltung für den Connector aktivieren. Um das Kennwort-Management zu aktivieren, wählen **kennwortverwaltung aktivieren** auf die **Erweiterungen konfigurieren** Seite.

![Konfigurieren von Erweiterungen](./media/active-directory-aadconnectsync-connector-domino/configureextensions.png)

Der Lotus Domino-Connector unterstützt folgende Vorgänge für das Internetkennwort:

- Kennwort festlegen: Legt in Domino ein neues HTTP-/Internetkennwort für den Benutzer fest. Das Konto wird außerdem standardmäßig entsperrt. Das Entsperr-Flag wird über die WMI-Schnittstelle des Synchronisierungsmoduls verfügbar gemacht.
- Kennwort ändern: In diesem Szenario möchte ein Benutzer unter Umständen sein Kennwort ändern, oder er wird nach einer bestimmten Zeit zu einer Kennwortänderung aufgefordert. Bei diesem Vorgang muss sowohl das alte als auch das neue Kennwort angegeben werden. Nach erfolgter Änderung wird das neue Kennwort in Lotus Domino aktualisiert.

Weitere Informationen finden Sie unter:

- [Verwenden des Internetsperrfeatures](http://www.ibm.com/developerworks/lotus/library/domino8-lockout/)
- [Verwalten von Internetkennwörtern](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_NOTES_AND_INTERNET_PASSWORD_SYNCHRONIZATION_7570_OVER.html)

## Verweisinformationen

In diesem Abschnitt werden Attributbeschreibungen und Attributanforderungen für den Lotus Domino-Connector aufgeführt.

### Lotus Notes-Eigenschaften

Bei der Bereitstellung von Personenobjekten für Ihr Lotus Domino-Verzeichnis benötigen Ihre Objekte einen spezifischen Satz von Eigenschaften mit bestimmten Werten. Diese Werte sind nur bei Erstellvorgängen erforderlich.

In der folgenden Tabelle werden diese Eigenschaften aufgeführt und beschrieben:

| Eigenschaft | Beschreibung |
| --- | --- |
| \_MMS_AltFullName | Der alternative vollständige Name des Benutzers. |
| \_MMS_AltFullNameLanguage | Die Sprache für die Angabe des alternativen vollständigen Namens des Benutzers. |
| \_MMS_CertDaysToExpire | Die Anzahl von Tagen (ab dem aktuellen Datum) bis zum Ablauf des Zertifikats. Ohne Angabe wird standardmäßig ein Datum verwendet, das vom aktuellen Datum aus zwei Jahre in der Zukunft liegt. |
| \_MMS_Certifier | Eigenschaft, die den Organisationshierarchienamen des Zertifizierers enthält. Beispiel: OU=OrganizationUnit,O=Org,C=Country. |
| \_MMS_IDPath | Ist diese Eigenschaft leer, wird auf dem Synchronisierungsserver keine lokale Benutzer-ID-Datei erstellt. Enthält die Eigenschaft einen Dateinamen, wird eine Benutzer-ID-Datei im Ordner „madata“ erstellt. Die Eigenschaft kann auch einen vollständigen Pfad enthalten, an dem die Benutzer-ID-Datei erstellt werden soll. |
| \_MMS_IDRegType | Personen können in Kontakte, US-Benutzer und internationale Benutzer unterteilt werden. In der folgenden Tabelle sind die möglichen Werte aufgeführt:<li>0 - Kontakt</li><li>1 - USA user</li><li>2 - internationale Benutzer</li> |
| \_MMS_IDStoreType | Erforderliche Eigenschaft für US und internationalen Benutzern. Die Eigenschaft enthält einen Ganzzahlwert, der angibt, ob die Benutzer-ID als Anlage im Notes-Adressbuch oder in der Maildatei der Person gespeichert wird. Wenn die Benutzer-ID-Datei eine Anlage im Adressbuch ist, können sie optional als Datei mit \_MMS_IDPath erstellt werden.<li>Leer - Geschäfts-ID-Datei im ID-Tresor keine ID-Datei (verwendet für Kontakte).</li><li> 1: Anlage im Notes-Adressbuch. Die \_MMS_Password-Eigenschaft muss für die Dateien der Benutzer-ID festgelegt werden, die Anlagen</li><li>2 - speichern Sie ID in der Person e-Mail-Datei. Die \_MMS_UseAdminP muss auf "false" festgelegt werden, die e-Mail-Datei, die während der Registrierung Person erstellt werden können. Die \_MMS_Password-Eigenschaft muss für die Dateien der Benutzer-ID festgelegt werden.</li>
| \_MMS_MailQuotaSizeLimit | Die für die E-Mail-Dateidatenbank zulässige Anzahl von Megabytes. |
| \_MMS_MailQuotaWarningThreshold | Die Anzahl von Megabytes für die E-Mail-Dateidatenbank, bei deren Überschreitung eine Warnung ausgegeben wird. |
| \_MMS_MailTemplateName | Die E-Mail-Vorlagendatei, die zum Erstellen der E-Mail-Datei des Benutzers verwendet wird. Ist eine Vorlage angegeben, wird die Maildatei unter Verwendung der angegebenen Vorlage erstellt. Ist keine Vorlage angegeben, wird die Datei auf der Grundlage der Standardvorlagendatei erstellt. |
| \_MMS_OU | Optionale Eigenschaft zum Angeben des Namens der Organisationseinheit unter dem Zertifizierer. Bei Kontakten muss diese Eigenschaft leer sein. |
| \_MMS_Password | Erforderliche Eigenschaft für Benutzer. Die Eigenschaft enthält das Kennwort für die ID-Datei des Objekts. |
| \_MMS_UseAdminP | Legen Sie diese Eigenschaft auf „true“ fest, wenn die Maildatei durch den AdminP-Prozess auf dem Domino-Server (und damit asynchron zum Exportvorgang) erstellt werden soll. Ist die Eigenschaft auf „false“ festgelegt, wird die Maildatei mit dem Domino-Benutzer (synchron im Exportvorgang) erstellt. |

Für einen Benutzer mit einer zugeordneten ID-Datei muss die \_MMS_Password-Eigenschaft einen Wert enthalten. Für den E-Mail-Zugriff über den Lotus Notes-Client müssen die Eigenschaften „MailServer“ und „MailFile“ eines Benutzers einen Wert enthalten.

Für den E-Mail-Zugriff über einen Webbrowser müssen die folgenden Eigenschaften Werte enthalten:

- MailFile: Erforderliche Eigenschaft, die den Pfad auf dem Lotus Domino-Server enthält, an dem die Maildatei gespeichert ist.
- MailServer: Erforderliche Eigenschaft, die den Namen des Lotus Domino-Servers enthält. Dies ist der Name, der beim Erstellen der Lotus-Maildatei auf dem Domino-Server verwendet werden muss.
- HTTPPassword: Optionale Eigenschaft, die das Webzugriffskennwort für das Objekt enthält.

Für den Zugriff auf den Domino-Server ohne E-Mail-Funktion muss die Eigenschaft „HTTPPassword“ einen Wert enthalten, die Eigenschaften „MailFile“ und „MailServer“ können dagegen leer sein.

Mit \_MMS_ IDStoreType = 2 (Store-Id in der e-Mail-Datei), die MailSystem-Eigenschaft des NotesRegistrationclass auf REG_MAILSYSTEM_INOTES (3) festgelegt.

### Erforderliche Attribute

Der Lotus Domino-Connector unterstützt im Synchronisierungsdienst hauptsächlich vier Arten von Objekten (Dokumenttypen):

- Gruppe
- Mail-In-Datenbank
- Person
- Kontakt (Person ohne Zertifizierer)
- Ressource

In diesem Abschnitt werden die Attribute aufgeführt, die für die einzelnen unterstützten Objekttypen erforderlich sind, um das Objekt an einen Domino-Server exportieren zu können.

| Objekttyp | Erforderliche Attribute |
| --- | --- |
| Gruppe | <li>ListName</li> |
| Mail-In-Datenbank | <li>FullName</li><li>MailFile</li><li>MailServer</li><li>MailDomain</li> |
| Person | <li>Nachname</li><li>MailFile</li><li>Kurzname</li><li>\_MMS_Password</li><li>\_MMS_IDStoreType</li><li>\_MMS_Certifier</li><li>\_MMS_IDRegType</li><li>_MMS_UseAdminP</li> |
| Kontakt (Person ohne Zertifizierer) | <li>\_MMS_IDRegType</li> |
| Ressource | <li>FullName</li><li>ResourceType</li><li>ConfDB</li><li>ResourceCapacity</li><li>Website</li><li>DisplayName</li><li>MailFile</li><li>MailServer</li><li>MailDomain</li> |

## Allgemeine Probleme und Fragen

### Die Schema-Erkennung funktioniert nicht.

Für eine erfolgreiche Schema-Erkennung muss die Datei „schema.nsf“ auf dem Domino-Server vorhanden sein. Diese Datei erscheint nur, wenn auf dem Server LDAP installiert ist. Wird das Schema nicht erkannt, vergewissern Sie sich, dass Folgendes erfüllt ist:

- Die Datei „schema.nsf“ befindet sich im Stammordner des Domino-Servers.
- Der Benutzer ist zum Anzeigen der Datei „schema.nsf“ berechtigt.
- Erzwingen Sie einen Neustart des LDAP-Servers. Öffnen Sie die Lotus Domino-Konsole, und laden Sie das Schema mithilfe des Befehls „Tell LDAP ReloadSchema“ neu.

### Es werden nicht alle sekundären Adressbücher angezeigt.

Für die Suche nach sekundären Adressbüchern wird vom Domino-Connector das Directory Assistance-Feature benötigt. Wenn die sekundären Adressbücher fehlen, überprüfen Sie, ob [Nebenstellen](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_ABOUT_DIRECTORY_ASSISTANCE.html) aktiviert und auf dem Domino-Server konfiguriert wurde.

### Benutzerdefinierte Attribute in Domino

Das Schema kann in Domino auf unterschiedliche Arten erweitert werden, sodass es als benutzerdefiniertes Attribut erscheint, das vom Connector genutzt werden kann.

**Ansatz 1: Erweitern des Lotus Domino-Schemas**

1. Erstellen Sie eine Kopie der Vorlage für Domino Directory < PUBNAMES. NTF > gemäß [folgendermaßen](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_CREATING_A_COPY_OF_THE_DEFAULT_PUBIC_ADDRESS_BOOK_TEMPLATE.html) (sollten Sie nicht anpassen das Standardverzeichnis für IBM Lotus Domino Vorlage):
2. Kopieren von Domino-Verzeichnis-Vorlage öffnen < CONTOSO. NTF > Vorlage, die gerade, im Domino-Designer, und führen Sie erstellt wird die unten beschriebenen Schritte aus:
    - Klicken Sie auf „Freigegebene Elemente“, und erweitern Sie „Unterformulare“.
    - Doppelklicken Sie anschließend auf das Unterformular „$[Objektname]InheritableSchema“ (wobei „[Objektname]“ der Name der standardmäßigen strukturellen Objektklasse ist – beispielsweise „Person“).
    - Benennen Sie das Attribut, das in [MyPersonAtrribute]-Schema hinzugefügt werden soll und erstellen Sie ein Feld vom Menü "Erstellen" auswählen, und wählen Sie dann im Menü "Feld" entspricht.
    - Legen Sie die Eigenschaften des hinzugefügten Felds fest, indem Sie im Fenster mit den Feldeigenschaften den Typ, das Format, die Größe, die Schriftart und ähnliche Parameter auswählen.
    - Behalten Sie für den Standardwert des Attributs den für das Attribut vergebenen Namen bei. (Wenn der Attributname also beispielsweise „MyPersonAttribute“ lautet, behalten Sie den Standardwert mit dem gleichen Namen bei.)
    - Speichern Sie das Unterformular „$[Objektname]InheritableSchema“ mit den aktualisierten Werten.
3. Ersetzen Sie die Domino-Verzeichnis-Vorlage < PUBNAMES. NTF > mit der neuen benutzerdefinierten Vorlage [CONTOSO. NTF] gemäß [folgendermaßen](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_ABOUT_RULES_FOR_CUSTOMIZING_THE_PUBLIC_ADDRESS_BOOK.html).
4. Schließen Sie Domino Administrator, und öffnen Sie die Domino-Konsole, um den LDAP-Dienst neu zu starten und das LDAP-Schema neu zu laden:
    - Fügen Sie in der Domino-Konsole den Befehl unter **Domino-Befehl** Text, der den LDAP-Dienst - eingereicht [Aufgabe LDAP neu starten]( http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_STARTING_AND_STOPPING_THE_LDAP_SERVER_OVER.html).
    - Verwenden Sie zum Neuladen des LDAP-Schemas den Befehl „Tell LDAP ReloadSchema“.
5. Öffnen Sie Domino Administrator, und rufen Sie die Registerkarte für Personen und Gruppen auf, um sich zu vergewissern, dass das hinzugefügte Attribut beim Hinzufügen einer Person in Domino verfügbar ist.
6. Öffnen Sie auf der Dateiregisterkarte die Datei „Schema.nsf“, und vergewissern Sie sich, dass sich das hinzugefügte Attribut in der LDAP-Objektklasse „dominoPerson“ befindet.

**Ansatz 2: Erstellen einer Erweiterungsklasse mit benutzerdefiniertem Attribut und Zuordnen zur Objektklasse**

1. Erstellen Sie eine Kopie der Vorlage für Domino Directory [PUBNAMES. NTF] gemäß [folgendermaßen]()http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_CREATING_A_COPY_OF_THE_DEFAULT_PUBIC_ADDRESS_BOOK_TEMPLATE.html (nie das Standardverzeichnis für IBM Lotus Domino Vorlage anpassen):
2. Öffnen Sie die Kopie der Domino-Verzeichnisvorlage „[CONTOSO.NTF]“, die Sie soeben erstellt haben, in Domino Designer.
3. Wählen Sie im linken Bereich „Freigegebener Code“ und anschließend „Unterformulare“ aus.
4. Klicken Sie auf „Neues Unterformular“.
5. Führen Sie folgende Schritte aus, um die Eigenschaften für das neue Unterformular anzugeben:
    - Wählen Sie im geöffneten neuen Unterformular „Design“ und anschließend „Unterformulareigenschaften“ aus.
    - Geben Sie neben der Eigenschaft „Name“ einen Namen für die Erweiterungsobjektklasse ein (beispielsweise „TestSubform“).
    - Lassen Sie die Optionseigenschaft „In Einfügedialogfeld für das Unterformular einschließen“ aktiviert.
    - Deaktivieren Sie die Optionseigenschaft „Pass-through-HTML in Notes rendern“.
    - Lassen Sie die anderen Eigenschaften unverändert, und schließen Sie das Eigenschaftendialogfeld für das Unterformular.
    - Speichern und schließen Sie das neue Unterformular.
6. Gehen Sie wie folgt vor, um ein Feld zum Definieren der Erweiterungsobjektklasse hinzuzufügen:
    - Öffnen Sie das soeben erstellte Unterformular.
    - Wählen Sie „Erstellen“ und anschließend „Feld“ aus.
    - Geben Sie neben dem Namen auf der Registerkarte „Grundlagen“ des Felddialogfelds einen beliebigen Namen an (beispielsweise „[MyPersonTestAttribute]“).
    - Legen Sie die Eigenschaften des hinzugefügten Felds fest, indem Sie den Typ, das Format, die Größe, die Schriftart und ähnliche Parameter auswählen.
    - Behalten Sie für den Standardwert des Attributs den für das Attribut vergebenen Namen bei. (Wenn der Attributname also beispielsweise „MyPersonTestAttribute“ lautet, behalten Sie den Standardwert mit dem gleichen Namen bei.)
    - Speichern Sie das Unterformular mit den aktualisierten Werten, und gehen Sie dann wie folgt vor:
        - Wählen Sie im linken Bereich „Freigegebener Code“ und anschließend „Unterformulare“ aus.
        - Wählen Sie das neue Unterformular und anschließend „Design“ > „Designeigenschaften“ aus.
        - Klicken Sie auf die dritte Registerkarte von links, und wählen Sie **weitergeben dieses Verbot Designänderung**.
7. Öffnen Sie das Unterformular „$[Objektname]ExtensibleSchema“ (wobei „[Objektname]“ der Name der standardmäßigen strukturellen Objektklasse ist – beispielsweise „Person“).
8. Fügen Sie die Ressource ein, wählen Sie das soeben erstellte Unterformular (etwa „TestSubform“) aus, und speichern Sie das Unterformular „$[Objektname]ExtensibleSchema“.
9. Ersetzen Sie die Vorlage für Domino-Verzeichnis [PUBNAMES. NTF] mit der neuen benutzerdefinierten Vorlage [CONTOSO. NTF] gemäß [folgendermaßen](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_ABOUT_RULES_FOR_CUSTOMIZING_THE_PUBLIC_ADDRESS_BOOK.html).
10. Schließen Sie Domino Administrator, und öffnen Sie die Domino-Konsole, um den LDAP-Dienst neu zu starten und das LDAP-Schema neu zu laden:
    - Fügen Sie in der Domino-Konsole den Befehl unter **Domino-Befehl** Text, der den LDAP-Dienst - eingereicht [Aufgabe LDAP neu starten](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_STARTING_AND_STOPPING_THE_LDAP_SERVER_OVER.html).
    - Verwenden Sie zum Neuladen des LDAP-Schemas den Befehl „Tell LDAP ReloadSchema“.
11. Öffnen Sie Domino Administrator, und rufen Sie die Registerkarte für Personen und Gruppen auf, um sich zu vergewissern, dass das hinzugefügte Attribut beim Hinzufügen einer Person in Domino (auf der Registerkarte „Sonstiges“) verfügbar ist.
12. Öffnen Sie Schema.nsf aus **Dateien** Registerkarte und finden Sie unter TestSubform LDAP-Objekt Erweiterungsklasse hinzugefügtes Attribut wiedergegeben wird.

**Methode 3: Hinzufügen des benutzerdefinierten Attributs zur ExtensibleObject-Klasse**

1. Öffnen Sie die im Stammverzeichnis abgelegte Datei „[Schema.nsf]“.
2. Wählen Sie im linken Menü unter LDAP-Objektklassen **Alle Schemadokumente** und klicken Sie auf **Hinzufügen Objektklasse** Schaltfläche:
3. Geben Sie den LDAP-Namen im Format „[###ExtensibleSchema]“ (wobei „###“ der Name der standardmäßigen strukturellen Objektklasse ist – beispielsweise „Person“) und mit einem Verweis auf das Objekt an, für das Sie das Schema erweitern möchten. Wenn Sie das Schema also beispielsweise für die Objektklasse „Person“ erweitern möchten, geben Sie den LDAP-Namen „[PersonExtensibleSchema]“ an.
4. Geben Sie den übergeordneten Objektklassennamen an, für den Sie das Schema erweitern möchten. Wenn Sie das Schema also beispielsweise für die Objektklasse „Person“ erweitern möchten, geben Sie den übergeordneten Objektklassennamen „[dominoPerson]“ an.
5. Geben Sie eine gültige, der Objektklasse entsprechende OID an.
6. Wählen Sie unter den Feldern für obligatorische oder optionale Attributtypen nach Bedarf erweiterte/benutzerdefinierte Attribute aus.
7. Nachdem die ExtensibleObjectClass erforderlichen Attribute hinzugefügt haben, klicken Sie auf **Speichern und schließen** Schaltfläche.
8. Für die entsprechende Standardobjektklasse wird eine erweiterte Objektklasse mit erweiterten Attributen erstellt.

## Problembehandlung

-   Informationen zum Aktivieren der Protokollierung, um den Connector zu beheben, finden Sie unter der [How to Enable ETW-Tracing für Connectors](http://go.microsoft.com/fwlink/?LinkId=335731).


