<properties
   pageTitle="Azure Privileged Identity Management: Ausführen einer Sicherheitsüberprüfung"
   description="Erfahren Sie, wie Sie mit der Erweiterung Azure Privileged Identity Management privilegierten Identitäten Rollen hinzufügen."
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

# Azure Privileged Identity Management: Ausführen einer Sicherheitsüberprüfung

## Ausführen einer Sicherheitsüberprüfung
Es ist sehr einfach, überprüfen Sie die privilegierten Zugriff einmal eine [Security-Überprüfung wurde gestartet](active-directory-privileged-identity-management-how-to-start-security-review.md).

## Für Prüfer: Genehmigen oder Verweigern des Zugriffs

### Überprüfen
1. Klicken Sie im Hauptmenü von PIM **Überprüfen Sie administrativen Zugriff**. Eine Liste mit Sicherheitsüberprüfungen wird angezeigt.
2. Wählen Sie die **Benutzer** in der Liste aus, für die Sie ändern möchten. HINWEIS: Der Zugriff wird tatsächlich geändert.  Dieser Vorgang besteht lediglich aus dem Erstellen einer Prüfliste für Personen, die den Zugriff für die Rolle möglicherweise ändern.  Nachdem mindestens ein Benutzer ausgewählt wurde die **Zugriff** und **Zugriff verweigern** Schaltflächen aktiviert.
3. Klicken Sie auf eine  **Zugriff** oder **Zugriff verweigern** für die Benutzer Sie ausgewählt haben.  Im Hauptmenü des Portals wird eine Benachrichtigung angezeigt, und die Überprüfungsliste wird ausgeblendet.  Schließen der **Überprüfung Azure AD-Rollen** Blatt.

### Selbstüberprüfung
1. Der Benutzer empfängt eine E-Mail mit der Aufforderung, seinen Zugriff zu überprüfen.  Die E-Mail enthält einen Link zum Anmelden beim Portal.
2. Sobald vorhanden ist, kann die Benutzer genehmigen oder ihre eigenen Zugriff verweigern, indem Sie auf die  **Zugriff** oder **Zugriff verweigern** Schaltflächen.  Sein Name wird aus der Liste entfernt.

## Für Überprüfungs-Manager: Verwalten von Sicherheitsüberprüfungen

## Abschließen oder Beenden einer Überprüfung
1. Kehren Sie zum PIM-Dashboard zurück.
2. Klicken Sie auf die Überprüfung der Sicherheit in ausfüllen soll die **sicherheitsüberprüfungen** Liste. Das Detailblatt der Sicherheitsüberprüfung wird angezeigt.
3. Klicken Sie auf **Überprüfung beenden** Ausführen oder beenden die Überprüfung.  Dadurch wird die Überprüfung archiviert und das Blatt nicht mehr angezeigt.

## Exportieren einer Überprüfung
Sie können eine Überprüfung exportieren, um sie in Excel oder in einem anderen Programm zu verwenden, das mit CSV-Dateien kompatibel ist.

1. Kehren Sie zum PIM-Dashboard zurück.
2. Klicken Sie auf die **sicherheitsüberprüfungen** des Dashboards.  Die **sicherheitsüberprüfungen** Blatt wird angezeigt.
3. Klicken Sie auf die Sicherheitsüberprüfung, die Sie exportieren möchten. Das Detailblatt der Sicherheitsüberprüfung wird angezeigt.
4. Klicken Sie auf die **exportieren** Schaltfläche. Der Download einer CSV-Datei wird gestartet.

## Löschen einer Überprüfung

> AZURE. Warnung [Sie eine Warnung zu erhalten, bevor die Löschung, achten Sie also nicht, die Sie *möchten* überprüfen Sie tatsächlich gelöscht.]

1. Kehren Sie zum PIM-Dashboard zurück.
2. Klicken Sie auf die **sicherheitsüberprüfungen** des Dashboards.  Die **sicherheitsüberprüfungen** Blatt wird angezeigt.
3. Klicken Sie auf die Sicherheitsüberprüfung, die Sie löschen möchten. Das Detailblatt der Sicherheitsüberprüfung wird angezeigt.
4. Klicken Sie auf die **Löschen** Schaltfläche.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Nächste Schritte
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]


