<properties
   pageTitle="Azure Privileged Identity Management: Verwenden des Überwachungsprotokolls"
   description="Erfahren Sie, wie Sie in der Erweiterung Azure Privileged Identity Management das Überwachungsprotokoll verwenden."
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

# Azure Privileged Identity Management: Verwenden des Überwachungsprotokolls

## Verwenden des Überwachungsprotokolls
Im Privileged Identity Management-Überwachungsprotokoll werden alle Benutzerzuweisungen und Aktivierungen in einem angegebenen Zeitraum angezeigt.

## Navigieren zum Überwachungsprotokoll
Sie können das Überwachungsprotokoll aufrufen, indem Sie im PIM-Dashboard auf "Überwachungsverlauf" klicken.

## Der Graph des Überwachungsprotokolls
Sie können mithilfe des Überwachungsprotokolls die Gesamtzahl der Aktivierungen, die maximale Anzahl der Aktivierungen pro Tag und die durchschnittliche Anzahl der Aktivierungen pro Tag in einem Liniendiagramm anzeigen.  Sie können auch die Daten nach Rolle filtern, wenn der Überwachungsverlauf mehrere Rollen enthält.

Sortieren nach Uhrzeit, Aktion oder mithilfe der **Zeit**, **Aktion** oder **Rolle** Schaltflächen.

## Die Überwachungsprotokollliste
Die Überwachungsprotokollliste enthält die folgenden Spalten:

- **Requestor** -Wiederherstellungsschlüsseln die rollenaktivierung.
- **Benutzer** -der Benutzer der Rolle Aktivierung.
- **Rolle** -die Rolle für den Benutzer.
- **Aktion** – die Aktionen, die mit der Rolle/Benutzer.
- **Zeit** – Wenn die Aktion aufgetreten ist.
- **Schlussfolgerungen** -Wenn Text in das Feld Grund während der Aktivierung eingegeben wurde, wird er hier angezeigt.
- **Ablauf des** – Wenn das Ablaufjahr 9999 ist, dann hat der Benutzer die Rolle dauerhaft.

## Filtern des Überwachungsprotokolls

Sie können auch die Informationen, die durch Klicken auf in das Überwachungsprotokoll wird Filtern die **Filter** Schaltfläche.  Die **Update Diagramm Parameter Blatt** wird angezeigt.

### Ändern des Datumsbereichs
Ändern Sie den Zeitraum des Überwachungsprotokolls durch Auswählen einer von der **heute**, **Vorwoche**, **vergangenen Monat**, oder **benutzerdefinierte** Schaltflächen.
Bei der Auswahl der **benutzerdefinierte** klicken, erhalten Sie eine **aus** Datumsfeld und ein **zu** Date-Felds den Datumsbereich angeben möchten, für das Protokoll.  Sie können die Datumsangaben im Format MM/TT/JJJJ eingeben oder auf die **Kalender** Symbol, und wählen Sie das Datum aus dem Kalender.

### Ändern der Rollen im Protokoll

Aktivieren oder deaktivieren Sie die **Rolle** Kontrollkästchen neben jeder Rolle werden sollen oder davon ausgeschlossen des Protokolls.

Wenn Sie alle Filter für das Überwachungsprotokoll festgelegt haben, klicken Sie auf "Aktualisieren", um die Daten im Protokoll zu filtern.  Wenn die Daten nicht sofort angezeigt werden, klicken Sie auf die **Aktualisieren** Schaltfläche.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Nächste Schritte
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]


