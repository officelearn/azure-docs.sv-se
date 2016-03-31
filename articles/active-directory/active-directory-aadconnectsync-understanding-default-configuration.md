<properties
   pageTitle="Azure AD Connect-Synchronisierung: Grundlegendes zur Standardkonfiguration | Microsoft Azure"
   description="Dieser Artikel beschreibt die Standardkonfiguration der Azure AD Connect-Synchronisierung."
   services="active-directory"
   documentationCenter=""
   authors="andkjell"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="11/10/2015"
   ms.author="andkjell"/>

# Azure AD Connect-Synchronisierung: Grundlegendes zur Standardkonfiguration

Dieser Artikel führt Sie durch die Standardkonfiguration der Azure AD Connect-Synchronisierung. Der Leser soll verstehen, wie das als deklarative Bereitstellung bezeichnete Konfigurationsmodell in einem realistischen Beispiel funktioniert. Dieser Artikel setzt voraus, dass Sie die Azure AD-Synchronisierung bereits mit dem Installations-Assistenten installiert und konfiguriert haben.

## Beschreibung des Szenarios

In diesem Beispiel wird eine Bereitstellung mit einer Kontogesamtstruktur (A), einer Ressourcengesamtstruktur (R) und einem Azure AD-Verzeichnis verwendet.

![Szenario](./media/active-directory-aadconnectsync-understanding-default-configuration/scenario.png)

Bei dieser Konfiguration wird vorausgesetzt, dass die Kontogesamtstruktur ein aktiviertes Konto und die Ressourcengesamtstruktur ein deaktiviertes Konto mit einem verknüpften Postfach enthält.

Unser Ziel bei der Standardkonfiguration ist:

- Mit der Anmeldung zusammenhängende Attributinformationen werden von der Gesamtstruktur aus mit dem aktivierten Konto synchronisiert.
- Attribute, die in der GAL (Global Address List, globale Adressliste) gefunden werden können, werden von der Gesamtstruktur aus mit dem Postfach synchronisiert. Wenn kein Postfach gefunden werden kann, wird jede beliebige andere Gesamtstruktur verwendet.
- Wenn ein verknüpftes Postfach gefunden wird, muss das verknüpfte aktivierte Konto für das Objekt gefunden werden, das in Azure AD exportiert wird.

## Synchronisierungsregel-Editor

Die Konfiguration kann mit dem Tool Synchronisierungsregel-Editor (Synchronization Rules Editor, SRE) angezeigt und geändert werden – eine entsprechende Verknüpfung befindet sich im Startmenü.

![Synchronisierungsregel-Editor](./media/active-directory-aadconnectsync-understanding-default-configuration/sre.png)

Der SRE ist ein Resource Kit-Tool und wird mit der Azure AD Connect-Synchronisierung installiert. Um es starten zu können, müssen Sie Mitglied der Gruppe „ADSyncAdmins“ sein. Beim Start sollte Folgendes angezeigt werden:

![Eingehende Synchronisierungsregeln](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulesinbound.png)

In diesem Bereich sehen Sie alle für die Konfiguration erstellten Synchronisierungsregeln. Jede Zeile in der Tabelle stellt eine Synchronisierungsregel dar. Auf der linken Seite werden unter Regeltypen die beiden verschiedenen Arten aufgeführt: eingehend und ausgehend. Eingehend und ausgehend bedeutet aus Sicht vom Metaverse. Wir konzentrieren uns in dieser Übersicht hauptsächlich auf die eingehenden Regeln. Die tatsächliche Liste von Synchronisierungsregeln hängt von dem in AD erkannten Schema ab. Die Kontogesamtstruktur (fabrikamonline.com) in der obigen Abbildung weist keine Dienste wie Exchange und Lync auf, und es wurden keine Synchronisierungsregeln für diese Dienste erstellt. In der Ressourcengesamtstruktur (res.fabrikamonline.com) sind jedoch Synchronisierungsregeln für diese Dienste vorhanden. Der Inhalt der Regeln hängt von der erkannten Version ab. In einer Bereitstellung mit Exchange 2013 werden z. B. mehr Attributflüsse konfiguriert als in Exchange 2010 und Exchange 2007.

## Synchronisierungsregel

Eine Regel ist ein Konfigurationsobjekt mit einem Satz von Attributen, die übertragen werden, wenn eine Bedingung erfüllt ist. Damit wird auch beschrieben, welche Beziehung ein Objekt in einem Connectorbereich zu einem Objekt im Metaverse hat. Dies wird als Verknüpfung oder Übereinstimmung bezeichnet. Die Synchronisierungsregeln besitzen einen Rangfolgenwert, der über ihre Beziehungen untereinander Aufschluss gibt. Eine Synchronisierungsregel mit einem niedrigeren numerischen Rangfolgenwert hat eine höhere Rangfolge, und im Falle eines Attributflusskonflikts hat die höhere Rangfolge Vorrang bei der Konfliktauflösung.

Als Beispiel betrachten wir die Synchronisierungsregel **In from AD – User AccountEnabled**. Wir markieren diese Zeile im Synchronisierungsregel-Editor, und wählen **Bearbeiten**.

Da dies eine vordefinierte Regel ist, erhalten wir eine Warnung, wenn wir die Regel öffnen. Sie nehmen nicht [ändert die Out-of-Box-Regeln](active-directory-aadconnectsync-best-practices-changing-default-configuration.md) sodass Sie was Ihre Absichten sind aufgefordert werden. In diesem Fall möchten nur die Regel anzuzeigen, wählen Sie also **Nr.**.

![Eingehende Synchronisierungsregeln](./media/active-directory-aadconnectsync-understanding-default-configuration/warningeditrule.png)

Eine Synchronisierungsregel verfügt über vier Konfigurationsabschnitte: Beschreibung, Bereichsfilter, Verknüpfungsregeln und Transformationen.

### Beschreibung

Der erste Bereich bietet grundlegende Informationen wie Name und Beschreibung.

![Eingehende Synchronisierungsregel bearbeiten ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncruledescription.png)

Es sind auch Informationen dazu enthalten, auf welches verbundene System sich diese Regel bezieht, auf welchen Objekttyp sie im verbundenen System angewendet wird und welcher Metaverse-Objekttyp vorliegt. Der Metaverse-Objekttyp ist immer eine Person, unabhängig davon, ob der Quellobjekttyp ein Benutzer, iNetOrgPerson oder ein Kontakt ist. Der Metaverse-Objekttyp sollte nie geändert werden, daher wird er als generischer Typ erstellt. Für den Verknüpfungstyp kann "Join", "StickyJoin" oder "Provision" festgelegt werden. Diese Einstellung arbeitet mit dem Bereich der Verknüpfungsregeln zusammen. Dies wird später behandelt.

Sie können auch sehen, dass diese Synchronisierungsregel zur Kennwortsynchronisierung verwendet wird. Wenn ein Benutzer im Bereich dieser Synchronisierungsregel liegt, wird das Kennwort für die lokale Verwendung und Verwendung in der Cloud synchronisiert (sofern Sie das Kennwortsynchronisierungsfeature aktiviert haben).

### Bereichsfilter

Der Abschnitt "Bereichsfilter" wird zum Konfigurieren verwendet, wann eine Synchronisierungsregel angewendet werden soll. Der Bereich ist konfiguriert, da der Name der betrachteten Synchronisierungsregel anzeigt, sollte nur für aktivierte Benutzer angewendet werden, damit der Active Directory-Attribute **UserAccountControl** darf nicht das Bit 2 festgelegt. Wenn wir einen Benutzer in Active Directory werden wir diese Synchronisierungsregel anwenden, wenn **UserAccountControl** festgelegt ist der Dezimalwert Wert 512 (aktivierter normaler Benutzer), aber dies gilt nicht, verfügt der Benutzer, die wir finden **UserAccountControl** 514 (deaktivierter normaler Benutzer) festgelegt.

![Eingehende Synchronisierungsregel bearbeiten ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulescopingfilter.png)

Der Bereichsfilter verfügt über Gruppen und Klauseln, die geschachtelt werden können. Alle Klauseln innerhalb einer Gruppe müssen erfüllt werden, damit eine Synchronisierungsregel angewendet wird. Wenn mehrere Gruppen definiert sind, muss mindestens eine Gruppe die Anforderungen erfüllen, damit die Regel angewendet wird. Das heißt: ein logisches OR zwischen Gruppen und eine logische und innerhalb einer Gruppe ausgewertet wird. Ein Beispiel hierfür finden Sie in der Regel für die ausgehende **Out to AAD – Gruppenverknüpfung**, wie unten gezeigt. Es gibt mehrere Synchronisierungsfiltergruppen: z. B. eine für Sicherheitsgruppen (securityEnabled EQUAL True) und eine für Verteilergruppen (securityEnabled EQUAL False).

![Ausgehende Synchronisierungsregel bearbeiten ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulescopingfilterout.png)

Diese Regel wird zur Definition verwendet, welche Gruppen für AAD bereitgestellt werden sollen. Verteilergruppen müssen E-Mail-aktiviert sein, damit sie mit AAD synchronisiert werden können, aber für Sicherheitsgruppen ist dies nicht erforderlich. Wie Sie ebenfalls erkennen können, werden zudem viele zusätzliche Attribute ausgewertet.

### Verknüpfungsregeln

Der dritte Bereich wird für die Konfiguration verwendet, wie sich Objekte im Connectorbereich auf Objekte im Metaverse beziehen. Die Regel, die wir zuvor angeschaut haben keine Konfiguration für Verknüpfungsregeln, stattdessen werden wir betrachten **In from AD – User Join**.

![Eingehende Synchronisierungsregel bearbeiten ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulejoinrules.png)

Der Inhalt der Verknüpfungsregeln hängt von der vergleichenden Option ab, die im Installations-Assistenten ausgewählt ist. Für eine eingehende Regel beginnt die Auswertung mit einem Objekt im Quellconnectorbereich, und jede Gruppe in den Verknüpfungsregeln wird nacheinander ausgewertet. Wenn die Auswertung eines Quellobjekts mit einer der Verknüpfungsregeln ergibt, dass es genau mit einem Objekt im Metaverse übereinstimmt, dann werden die Objekte verknüpft. Wenn alle Regeln ausgewertet wurden und es keine Übereinstimmung gibt, wird der Verknüpfungstyp auf der Beschreibungsseite verwendet. Wenn für diese Einstellung "Provision" festgelegt ist, wird im Ziel (dem Metaverse) ein neues Objekt erstellt. Die Bereitstellung eines neuen Objekts im Metaverse wird auch als Entwurf eines Objekts im Metaverse bezeichnet.

Die Verknüpfungsregeln werden nur einmal ausgewertet. Wenn ein Connectorbereichsobjekt und ein Metaverse-Objekt verknüpft werden, bleiben sie verknüpft, so lange der Gültigkeitsbereich der Synchronisierungsregel weiterhin gegeben ist.

Bei der Auswertung von Synchronisierungsregeln muss sich nur eine Synchronisierungsregel mit Verknüpfungsregeln im Gültigkeitsbereich befinden. Wenn mehrere Synchronisierungsregeln mit Verknüpfungsregeln für ein Objekt gefunden werden, wird ein Fehler ausgelöst. Aus diesem Grund besteht die bewährte Methode darin, nur eine Synchronisierungsregel mit definierter Verknüpfung zu besitzen, wenn sich mehrere Synchronisierungsregeln im Gültigkeitsbereich eines Objekts befinden. Um diese Regeln in der standardmäßigen Konfiguration für die Azure Active Directory-Synchronisierung zu finden, suchen Sie nach den Regeln, deren Name auf „Verknüpfung“ endet. Eine Synchronisierungsregel ohne definierte Verknüpfungsregeln wendet die Attributflüsse an, wenn eine andere Synchronisierungsregel die Objekte verknüpft oder ein neues Objekt im Ziel bereitgestellt hat.

Wenn Sie die oben stehende Abbildung betrachten, sehen Sie, dass die Regel beitreten möchte **ObjectSID** mit **MsExchMasterAccountSid** (Exchange) und **MsRTCSIP-OriginatorSid** (Lync), also erwarten wir in einer kontoressourcengesamtstruktur-Topologie-Konto. Die gleiche Regel finden wir bei allen Gesamtstrukturen, d. h. die Annahme ist, dass jede Gesamtstruktur entweder eine Konto- oder Ressourcengesamtstruktur sein könnte. Dies funktioniert auch, wenn Sie über Konten verfügen, die sich in einer einzelnen Gesamtstruktur befinden und nicht verknüpft werden müssen.

### Transformationen

Der Transformationsbereich definiert alle Attributflüsse, die sich auf das Zielobjekt beziehen, wenn die Objekte verknüpft werden und der Bereichsfilter zutrifft. Wechseln zur unserer **In from AD – User AccountEnabled** Synchronisierungsregel finden wir die folgenden Transformationen:

![Eingehende Synchronisierungsregel bearbeiten ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncruletransformations.png)

Zur Orientierung: In einer Kontoressourcen-Gesamtstrukturbereitstellung erwarten wir, ein aktiviertes Konto in der Kontogesamtstruktur und ein deaktiviertes Konto in der Ressourcengesamtstruktur mit Exchange- und Lync-Einstellungen vorzufinden. Die von uns betrachtete Synchronisierungsregel enthält die für die Anmeldung erforderlichen Attribute und diese sollen von der Gesamtstruktur übermittelt werden, in der wir ein aktiviertes Konto gefunden haben. Alle diese Attributflüsse sind in einer Synchronisierungsregel zusammengestellt.

Eine Transformation kann unterschiedliche Typen aufweisen: „Konstant“, „Direkt“ und „Ausdruck“.

- Ein konstanter Ablauf übermittelt immer einen bestimmten Wert. Im obigen Fall wird im Metaverse-Attribut "accountEnabled" immer der Wert "True" festgelegt.
- Ein direkter Ablauf übermittelt den Wert des Attributs der Quelle an das Zielattribut.
- Der dritte Ablauftyp ist "Expression", der fortgeschrittenere Konfigurationen gestattet.

Die Ausdruckssprache ist VBA (Visual Basic for Applications), daher sollten Benutzer, die über Microsoft Office- oder VBScript-Kenntnisse verfügen, mit diesem Format vertraut sein. Attribute werden in eckige Klammern eingeschlossen: "[Attributname]". Bei Attributnamen und Funktionsnamen wird die Groß-/Kleinschreibung berücksichtigt, aber der Synchronisierungsregel-Editor wertet die Ausdrücke aus und zeigt eine Warnung an, wenn der Ausdruck nicht zulässig ist. Alle Ausdrücke werden in einer einzelnen Zeile mit verschachtelten Funktionen ausgedrückt. Zur Veranschaulichung der Leistung der Konfigurationssprache folgt hier der Ablauf für "pwdLastSet", jedoch mit zusätzlich eingefügten Kommentaren:

```
// If-then-else
IIF(
// (The evaluation for IIF) Is the attribute pwdLastSet present in AD?
IsPresent([pwdLastSet]),
// (The True part of IIF) If it is, then from right to left, convert the AD time format to a .Net datetime, change it to the time format used by AAD, and finally convert it to a string.
CStr(FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")),
// (The False part of IIF) Nothing to contribute
NULL
)
```

Das Thema der Transformation ist umfangreich und umfasst einen großen Teil der benutzerdefinierten Konfiguration, die mit der Azure AD Connect-Synchronisierung möglich ist. Weitere Informationen hierzu finden Sie in den anderen Artikeln zur Azure AD Connect-Synchronisierung.

## Rangfolge

Wir haben nun einige individuelle Synchronisierungsregeln betrachtet, aber die Regeln wirken in der Konfiguration zusammen. In einigen Fällen tragen mehrere Synchronisierungsregeln einen Attributwert zum gleichen Zielattribut bei. In diesem Fall wird die Attributrangfolge verwendet, um zu bestimmen, welches Attribut gewinnt. Als Beispiel werfen wir einen Blick auf das sourceAnchor-Attribut. Dieses Attribut ist eine wichtige Voraussetzung zur Anmeldung bei Azure AD. Wir können einen Attributfluss für dieses Attribut in zwei verschiedenen Synchronisierungsregeln finden **In von AD – User AccountEnabled** und **In von AD – User Common**. Aufgrund des Synchronisierungsregelvorrangs trägt die Gesamtstruktur das sourceAnchor-Attribut mit einem aktivierten Konto erst dann bei, wenn mehrere Objekte mit dem Metaverse-Objekt verknüpft sind. Wenn keine aktivierten Konten vorhanden sind, verwenden wir die Synchronisierung Sammelfehlerregel **In from AD – User Common**. So wird sichergestellt, dass sogar noch für deaktivierte Konten ein sourceAnchor-Attrbut bereitgestellt wird.

![Eingehende Synchronisierungsregeln](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulesinbound.png)

Die Rangfolge für Synchronisierungsregeln wird vom Installations-Assistenten in Gruppen festgelegt. Eine Gruppe von Regeln, die alle den gleichen Namen besitzen, aber mit anderen verbundenen Verzeichnissen verbunden sind. Geben Sie der Installations-Assistenten wird der Regel **In from AD – User Join** Vorrang und Iteration über alle verbundenen AD-Verzeichnissen. Er fährt dann mit der nächsten Gruppen von Regeln in einer vordefinierten Reihenfolge fort. Innerhalb einer Gruppe werden die Regeln in der Reihenfolge hinzugefügt, in der die Connectors dem Assistenten hinzugefügt wurden. Wenn ein weiterer Connector durch den Assistenten hinzugefügt wird, werden die Synchronisierungsregeln neu geordnet, und die Regeln für den neuen Connector werden zuletzt in jede Gruppe eingefügt.

## Zusammenfügen des Gesamtbilds

Jetzt wissen wir genug über Synchronisierungsregeln, um die Funktionsweise der Konfiguration mit verschiedenen Synchronisierungsregeln zu verstehen. Wenn wir einen Benutzer und die Attribute betrachten, die zum Metaverse beigetragen werden, werden die Regeln in der folgenden Reihenfolge angewendet:

| Name | Kommentar |
| :------------- | :------------- |
| Ein von AD – Benutzerverknüpfung | Regel für die Verknüpfung von Connectorbereichobjekten mit Metaverse. |
| Ein von AD – Benutzer AccountEnabled | Erforderliche Attribute für eine Anmeldung bei Azure AD und Office 365. Diese Attribute sollen aus dem aktivierten Konto kommen. |
| Ein von AD – Benutzer allgemein aus Exchange | In der globalen Adressliste gefundene Attribute. Es wird vorausgesetzt, dass die Qualität der Daten in der Gesamtstruktur am besten ist, in der wir das Postfach des Benutzers gefunden haben. |
| Ein von AD – Benutzer allgemein | In der globalen Adressliste gefundene Attribute. Für den Fall, dass ein Postfach nicht gefunden wurde, kann jedes andere verknüpfte Objekt den Attributwert beitragen. |
| Ein von AD – Benutzer Exchange | Ist nur vorhanden, wenn Exchange erkannt wurde. Fluss aller Exchange-Attribute der Infrastruktur wird ausgeführt. |
| Ein von AD – Benutzer Lync | Ist nur vorhanden, wenn Lync erkannt wurde. Fluss aller Lync-Attribute der Infrastruktur wird ausgeführt. |

## Zusätzliche Ressourcen

* [Azure AD Connect-Synchronisierung: Anpassen von Synchronisierungsoptionen](active-directory-aadconnectsync-whatis.md)
* [Integrieren Ihrer lokalen Identitäten in Azure Active Directory](active-directory-aadconnect.md)


