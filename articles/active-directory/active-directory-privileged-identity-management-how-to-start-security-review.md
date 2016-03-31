<properties
   pageTitle="Azure Privileged Identity Management: Starten einer Sicherheitsüberprüfung"
   description="Erfahren Sie, wie Sie mit der Erweiterung Azure Privileged Identity Management eine Sicherheitsüberprüfung für privilegierte Identitäten erstellen."
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
   ms.date="09/21/2015"
   ms.author="inhenk"/>

# Azure Privileged Identity Management: Starten einer Sicherheitsüberprüfung

## Starten einer Sicherheitsüberprüfung
Letztendlich können Sie Sicherheitsüberprüfungen an anderen Stellen im Azure-Portal durchführen.  Dieses Dokument beschreibt die Schritte zum Starten einer Überprüfung der Sicherheit innerhalb der **Privileged Identity Management (PIM)** Schnittstelle.

Möglicherweise gibt es Benutzer, die Sie nicht erkennen, oder vielleicht hat ein Benutzer seine Stelle oder das Projekt gewechselt und benötigt in der neuen Position keinen privilegierten Zugriff mehr.  Um die Risiken im Zusammenhang mit diesen "veralteten" Rollenzuweisungen zu verringern, können Sie und andere Administratoren die den Benutzern erteilten Rollen überprüfen, indem Sie eine Sicherheitsüberprüfung starten.

## Möglichkeiten zum Starten einer Sicherheitsüberprüfung
> [AZURE.NOTE] Wenn Sie ein PIM-Dashboard im Azure-Portal noch erstellt haben, finden Sie in den Schritten  [Erste Schritte mit Azure Privileged Identity Management](active-directory-privileged-identity-management-getting-started.md)

Vom Azure-PIM-Dashboard aus können Sie eine Ansicht über folgende Optionen starten:

- Dashboard > Sicherheitüberprüfungen > Überprüfen > Schaltfläche "Überprüfen"
- Dashboard > Rollen > Schaltfläche "Überprüfen"
- Dashboard > Klicken Sie auf die Rolle, die in der Rollenliste überprüft werden soll > Schaltfläche "Überprüfen"

## Starten einer Sicherheitsüberprüfung

Klicken auf die **überprüfen** Schaltfläche der **rollenüberprüfung starten** und die **Wählen Sie eine Rolle aus, um zu überprüfen** Blades angezeigt und die **Wählen Sie eine Rolle für die Überprüfung** Element ausgewählt werden.

### Auswählen der zu überprüfenden Rolle

1. Wählen Sie Rolle aus der Liste der Rollen in der **Wählen Sie eine Rolle für die Überprüfung** Blatt.  Sie können jeweils nur eine Rolle auswählen.  Die **Wählen Sie eine Rolle für die Überprüfung** Blatt wird ersetzt durch die **Wählen Bearbeiter Blatt**.  Bei der Auswahl der Prüfer stehen Ihnen zwei Optionen zur Verfügung:
  - Ich: Verwenden Sie diese Funktion, wenn Sie eine Vorschau zur Funktionsweise von Sicherheitsüberprüfungen anzeigen möchten, ohne andere Administratoren einzubeziehen.
  - Selbstüberprüfung durch Rollenmitglieder: Mit diesem Feature können Benutzer ihre eigenen Rollenzuweisungen selbst überprüfen.
2. Wählen Sie eine der beiden Optionen aus, um die Arbeit mit den Überprüfungsdetails zu beginnen. Die **Standardeinstellungen ändern** Blatt wird angezeigt.

### Überprüfen mit Option "Selbstüberprüfung"

1. Benennen Sie die Überprüfung durch Eingabe der Überprüfung in den **Namen** Feld.  Es empfiehlt sich, der Überprüfung einen eindeutigen Namen zu geben, der die Überprüfung beschreibt und deren Verfolgung vereinfacht.
2. Geben Sie ein Startdatum für die Überprüfung in der **Startdatum** Feld.
3. Geben Sie ein Enddatum für die Überprüfung in der **Enddatum** Feld.  Im Folgenden sind einige Aspekte aufgeführt, die Sie beim Festlegen des Enddatums für die Überprüfung beachten sollten:
  - Wie viele Personen werden überprüft?
  - Wie schnell können die Benutzer die Erweiterung hinzufügen und die Überarbeitung abschließen?
4. Klicken Sie auf die Schaltfläche OK in der **Standardeinstellungen ändern** Blatt. Das Blatt wird geschlossen.
5. Klicken Sie auf die Schaltfläche OK in der **Starten eine Überprüfung einer Rolle** Blatt.  Das Blatt wird geschlossen. Eine Benachrichtigung wird im Portal klicken Sie im Hauptmenü angezeigt. aktualisieren Sie das Dashboard, indem Sie auf die **Aktualisieren** Schaltfläche und die Überprüfung der Sicherheit werden in der **sicherheitsüberprüfungen** Abschnitt.
6. Benachrichtigen Sie die Personen in der Rolle, dass sie die Erweiterung hinzufügen und anschließend ihren eigenen Administratorzugriff überprüfen sollen.  Weitere Informationen finden Sie im nächsten Schritt.
6. [Prüfen Sie den Administratorzugriff.](active-directory-privileged-identity-management-how-to-perform-security-review.md)

### Überprüfung mit Option "Ich"

Wenn Sie die Option "Ich" als Prüfer ausgewählt haben, fahren Sie mit der Sicherheitsüberprüfung fort. Weitere Informationen zum Abschließen der Überprüfung finden Sie unter [Azure Privileged Identity Management: Gewusst wie: Ausführen einer Sicherheit überprüfen](active-directory-privileged-identity-management-how-to-perform-security-review.md)

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Inhaltsverzeichnis zu PIM
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]


