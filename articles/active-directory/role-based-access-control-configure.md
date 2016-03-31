<properties
    pageTitle="Rollenbasierte Zugriffssteuerung in Azure Active Directory | Microsoft Azure"
    description="In diesem Artikel wird die rollenbasierte Zugriffssteuerung in Azure beschrieben."
    services="active-directory"
    documentationCenter=""
    authors="IHenkel"
    manager="stevenpo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="12/14/2015"
    ms.author="inhenk"/>

# Rollenbasierte Access Control in Azure

## Rollenbasierte Zugriffssteuerung
Die rollenbasierte Access Control in Azure (RBAC) ermöglicht eine präzise Zugriffsverwaltung für Azure. Mithilfe von RBAC können Sie Aufgaben in Ihrem DevOps-Team verteilen und Benutzern nur den Zugriff gewähren, den sie zur Ausführung ihrer Aufgaben benötigen.

### Grundlagen zur Zugriffsverwaltung in Azure
Jedes Azure-Abonnement befindet sich in einem Azure Active Directory. Nur Benutzern, Gruppen und Anwendungen aus diesem Verzeichnis kann Verwaltungszugriff auf Ressourcen im zugehörigen Azure-Abonnement gewährt werden. Die Verwaltung kann über das Azure-Portal, Azure-Befehlszeilentools und Azure-Verwaltungs-APIs erfolgen.

Der Zugriff wird gewährt, indem Benutzern, Gruppen und Anwendungen die jeweils geeignete RBAC-Rolle auf der richtigen Ebene zugewiesen wird. Um Zugriff auf das gesamten Abonnement zu gewähren, weisen Sie eine Rolle auf Abonnementebene zu. Um Zugriff auf eine bestimmte Ressourcengruppe innerhalb eines Abonnements zu gewähren, weisen Sie eine Rolle auf Ressourcengruppenebene zu. Sie können Rollen auch für bestimmte Ressourcen zuweisen, wie z. B.für Websites, virtuelle Computer und Subnetze, um nur Zugriff auf eine Ressource zu gewähren.

![](./media/role-based-access-control-configure/overview.png)

Die RBAC-Rolle, die Sie Benutzern, Gruppen und Anwendungen zuweisen, legt fest, welche Ressourcen der Benutzer (bzw. die Gruppe oder Anwendung) auf dieser Ebene verwalten kann.

### Integrierte RBAC-Rollen in Azure
Die rollenbasierte Zugriffssteuerung in Azure verfügt über drei grundlegende Rollen, die für alle Ressourcentypen gelten: Besitzer, Mitwirkender und Leser. Besitzer verfügen über vollständigen Zugriff auf alle Ressourcen, einschließlich des Rechts, den Zugriff an andere Personen zu delegieren. Mitwirkende können alle Arten von Azure-Ressourcen erstellen und verwalten, aber keinen anderen Personen Zugriff gewähren. Leser können nur vorhandene Azure-Ressourcen anzeigen. Die verbleibenden RBAC-Rollen in Azure ermöglichen die Verwaltung von bestimmten Azure-Ressourcen. Beispielsweise ermöglicht die Rolle "Mitwirkender für virtuelle Computer" die Erstellung und Verwaltung von virtuellen Computern, nicht jedoch des virtuellen Netzwerks oder des Subnetzes, mit dem der virtuelle Computer eine Verbindung herstellt.

[RBAC integrierte Rollen](role-based-access-built-in-roles.md) listet der integrierten RBAC-Rollen, die in Azure verfügbar sind. Für jede Rolle werden die Vorgänge angegeben, für die eine integrierte Rolle den Zugriff gewährt.

### Ressourcenhierarchie und Zugriffsvererbung in Azure
Jedes Abonnement in Azure gehört zu einem einzigen Verzeichnis, jede Ressourcengruppe gehört zu einem einzigen Abonnement, und jede Ressource gehört zu einer einzigen Ressourcengruppe. Der Zugriff, den Sie auf übergeordneter Ebene gewähren, wird auf untergeordneter Ebene geerbt. Wenn Sie einer Azure AD-Gruppe die Rolle "Leser" auf Abonnementebene zuweisen, können die Mitglieder dieser Rolle alle Ressourcengruppen und alle Ressourcen in diesem Abonnement anzeigen. Wenn Sie einer Anwendung auf Ressourcengruppenebene die Rolle "Mitwirkender" zuweisen, kann diese Anwendung alle Ressourcentypen in dieser Ressourcengruppe verwalten. Sie kann jedoch keine anderen Ressourcengruppen im Abonnement verwalten.

### Azure RBAC im Vergleich zu klassischen Administratoren und Co-Admins für Abonnements
Klassische Administratoren und Co-Admins für Abonnements verfügen über vollständigen Zugriff auf das Azure-Abonnement. Sie können Ressourcen mithilfe von klassischen Azure-Portal (https://manage.windowsazure.com), und Azure Service Manager-APIs, als auch der Azure-Portal (https://portal.azure.com) sowohl neue APIs der Azure-Ressourcen-Manager verwalten. Im RBAC-Modell wird klassischen Administratoren die Besitzerrolle auf Abonnementebene zugewiesen.

Das Modell eine präzisere Autorisierung (Azure RBAC) wird nur von Azure-Portal (https://portal.azure.com) und Azure-Ressourcen-Manager-APIs unterstützt. Benutzer und Anwendungen, die RBAC-Rollen (am Subscription-Ressource bzw. des Bereichs) zugewiesen werden können nicht im klassischen-Verwaltungsportal (http://manage.windowsazure.com) und der Azure-Dienst-APIs verwenden.

### Autorisierung für Verwaltungsvorgänge im Vergleich zu Datenvorgängen
Das präzisere Autorisierungsmodell (Azure RBAC) wird nur für Verwaltungsvorgänge für die Azure-Ressourcen im Azure-Portal und in den Azure-Ressourcen-Manager-APIs unterstützt. Über RBAC können nicht alle Vorgänge auf Datenebene für Azure-Ressourcen autorisiert werden. Während das Erstellen/Lesen/Aktualisieren/Löschen von Speicherkonten über RBAC gesteuert werden kann, lassen sich dieselben Vorgänge für Blobs oder Tabellen in einem Speicherkonto noch nicht über RBAC steuern. Ebenso kann das Erstellen/Lesen/Aktualisieren/Löschen einer SQL-Datenbank über RBAC gesteuert werden, während sich dieselben Vorgänge noch nicht für SQL-Tabellen in einer Datenbank über RBAC steuern lassen.

## Verwalten des Zugriffs über das Azure-Portal
### Zugriff anzeigen
Wählen Sie die Zugriffseinstellungen auf dem Blatt der Ressourcengruppe im Abschnitt mit den grundlegenden Informationen aus. Die **Benutzer** Blatt Listet alle Benutzer, Gruppen und Anwendungen, die Zugriff erteilt der Ressourcengruppe. Der Zugriff wird entweder in der Ressourcengruppe zugewiesen oder aus einer Zuweisung im übergeordneten Abonnement geerbt.

![](./media/role-based-access-control-configure/view-access.png)

> [AZURE.NOTE] Klassische abonnementadministratoren und co-Administratoren gelten Besitzer des Abonnements, in das neue RBAC-Modell.

### Zugriff hinzufügen
1. Klicken Sie auf die **Hinzufügen** Symbol auf der **Benutzer** Blatt. ![](./media/role-based-access-control-configure/grant-access1.png)
2. Wählen Sie die Rolle aus, die Sie zuweisen möchten.
3. Suchen Sie nach dem Benutzer, der Gruppe oder der Anwendung, dem oder der Sie Zugriff gewähren möchten, und wählen Sie das entsprechende Element aus.
4. Durchsuchen Sie das Verzeichnis anhand von Anzeigenamen, E-Mail-Adressen und Objektbezeichnern nach Benutzern, Gruppen und Anwendungen.![](./media/role-based-access-control-configure/grant-access2.png)

### Zugriff entfernen
1. In der **Benutzer** Blatt, wählen Sie die rollenzuweisung, die Sie entfernen möchten.
2. Klicken Sie auf die **Entfernen** Symbol im Fenster Details Zuweisung.
3. Klicken Sie auf **Ja** zu bestätigen.

![](./media/role-based-access-control-configure/remove-access1.png)


> [AZURE.NOTE] Geerbte Zuweisungen können nicht von untergeordneten Bereiche entfernt werden. Wechseln Sie auf die übergeordnete Ebene, und entfernen Sie die entsprechenden Zuweisungen.


![](./media/role-based-access-control-configure/remove-access2.png)

## Verwalten des Zugriffs mit Azure PowerShell
Der Zugriff kann mithilfe von Azure RBAC-Befehlen in den Azure PowerShell-Tools verwaltet werden.

-   Verwenden Sie `Get-AzureRmRoleDefinition` zum Auflisten von RBAC-Rollen für die Zuweisung und zum Überprüfen der Vorgänge, auf die sie Zugriff gewähren.

-   Verwenden Sie `Get-AzureRmRoleAssignment` zum Auflisten der RBAC-Zugriffszuweisungen, die im angegebenen Abonnement bzw. in der angegebenen Ressource oder Ressourcengruppe gelten. Verwenden Sie `ExpandPrincipalGroups` zum Auflisten der Zugriffszuweisungen zum angegebenen Benutzer sowie zu den Gruppen, denen der Benutzer angehört. Verwenden Sie den `IncludeClassicAdministrators`-Parameter, um auch die klassischen Administratoren und Co-Admins für Abonnements aufzulisten.

-   Verwenden Sie `New-AzureRmRoleAssignment` zum Gewähren des Zugriffs für Benutzer, Gruppen und Anwendungen.

-   Verwenden Sie `Remove-AzureRmRoleAssignment` zum Entfernen des Zugriffs.

Finden Sie unter [Verwalten des Zugriffs mit Azure PowerShell](role-based-access-control-manage-access-powershell.md) Ausführlichere Beispiele für die Verwaltung des Zugriffs mit Azure PowerShell.

## Verwalten des Zugriffs über die Azure-Befehlszeilenschnittstelle
Der Zugriff kann mithilfe von Azure RBAC-Befehlen in der Azure-Befehlszeilenschnittstelle verwaltet werden.

-   Verwenden Sie `azure role list` zum Auflisten der für die Zuweisung verfügbaren RBAC-Rollen. Verwenden Sie "azure role show" zum Überprüfen der Vorgänge, auf welche die Rollen Zugriff gewähren.

-   Verwenden Sie `azure role assignment list` zum Auflisten der RBAC-Zugriffszuweisungen, die im angegebenen Abonnement bzw. in der angegebenen Ressource oder Ressourcengruppe gelten. Verwenden Sie die `expandPrincipalGroups`-Option zum Auflisten der Zugriffszuweisungen zum angegebenen Benutzer sowie zu den Gruppen, denen der Benutzer angehört. Verwenden der  `includeClassicAdministrators` Parameter für klassische Abonnementadministrator und -Co-Administratoren auch auflisten.

-   Verwenden Sie `azure role assignment create` zum Gewähren des Zugriffs für Benutzer, Gruppen und Anwendungen.

-   Verwenden Sie `azure role assignment delete` zum Entfernen des Zugriffs.

Finden Sie unter [Verwalten des Zugriffs mithilfe der Azure-Befehlszeilenschnittstelle](role-based-access-control-manage-access-azure-cli.md) Ausführlichere Beispiele für die Verwaltung des Zugriffs mithilfe der Azure-Befehlszeilenschnittstelle.

## Verwenden des Verlaufsberichts zu Zugriffsänderungen
Alle Zugriffsänderungen, die in Ihrem Azure-Abonnement vorgenommen werden, werden in Azure-Ereignissen protokolliert.

### Erstellen eines Berichts mit Azure PowerShell
Verwenden Sie folgenden PowerShell-Befehl, um einen Bericht zu erstellen, der darüber informiert, wer welche Art von Zugriff auf welcher Ebene in Ihren Azure-Abonnements gewährt oder widerrufen hat.

    `Get-AzureAuthorizationChangeLog`

### Erstellen eines Berichts über die Azure-Befehlszeilenschnittstelle
Verwenden Sie folgenden Befehl in der Azure-Befehlszeilenschnittstelle, um einen Bericht zu erstellen, der darüber informiert, wer welche Art von Zugriff auf welcher Ebene in Ihren Azure-Abonnements gewährt oder widerrufen hat.

    `azure authorization changelog`

> [AZURE.NOTE] Access-Änderungen können für die letzten 90 Tage (in Batches von 15 Tagen) abgefragt werden.

Unten sind alle Zugriffsänderungen im Abonnement für die letzten 7 Tage aufgeführt.

![](./media/role-based-access-control-configure/access-change-history.png)

### Exportieren der Zugriffsänderungen in eine Kalkulationstabelle
Zugriffsänderungen lassen sich zum einfacheren Überprüfen in eine Kalkulationstabelle exportieren.

![](./media/role-based-access-control-configure/change-history-spreadsheet.png)

## Benutzerdefinierte Rollen in Azure RBAC
Erstellen Sie eine benutzerdefinierte Rolle in Azure RBAC, falls keine der integrierten Rollen Ihre speziellen Zugriffsanforderungen erfüllt. Benutzerdefinierte Rollen können mit RBAC-Befehlszeilentools in Azure PowerShell und in der Azure-Befehlszeilenschnittstelle erstellt werden. Wie integrierte Rollen auch, können benutzerdefinierte Rollen Benutzern, Gruppen und Anwendungen auf Abonnement-, Ressourcengruppen- und Ressourcenebene zugewiesen werden.

Es folgt ein Beispiel für die Definition einer benutzerdefinierten Rolle, die das Überwachen und Neustarten virtueller Maschinen ermöglicht:

```
{
  "Name": "Virtual Machine Operator",
  "Id": "cadb4a5a-4e7a-47be-84db-05cad13b6769",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624",
    "/subscriptions/34370e90-ac4a-4bf9-821f-85eeedeae1a2"
  ]
}
```
### Actions
Mit der Actions-Eigenschaft einer benutzerdefinierten Rolle werden die Azure-Vorgänge angegeben, auf die mit der Rolle Zugriff gewährt wird. Es handelt sich um eine Sammlung von Vorgangszeichenfolgen, mit denen sicherungsfähige Vorgänge von Azure-Ressourcenanbietern identifiziert werden. Vorgang Zeichenfolgen mit Platzhaltern (\ *) Zugriff gewähren gehen alle Vorgänge, die die Operationszeichenfolge entsprechen. Beispiel:

-   `*/read` gewährt Zugriff auf Lesevorgänge für alle Ressourcentypen aller Azure-Ressourcenanbieter.
-   `Microsoft.Network/*/read` gewährt Zugriff auf Lesevorgänge für alle Ressourcentypen im Microsoft.Network-Ressourcenanbieter von Azure.
-   `Microsoft.Compute/virtualMachines/*` gewährt Zugriff auf alle Vorgänge virtueller Maschinen und die dazugehörigen untergeordneten Ressourcentypen.
-   `Microsoft.Web/sites/restart/Action` gewährt Zugriff zum Neustarten von Websites.

Verwenden Sie den Befehl `Get-AzureRmProviderOperation` oder `azure provider operations show`, um die Vorgänge von Ressourcenanbietern aufzulisten. Sie können diese Befehle auch verwenden, um zu überprüfen, ob eine Vorgangszeichenfolge gültig ist, und um Zeichenfolgen für Platzhaltervorgänge zu erweitern.

![](./media/role-based-access-control-configure/1-get-azurermprovideroperation-1.png)

![](./media/role-based-access-control-configure/1-azure-provider-operations-show.png)

### NotActions
Wenn der Satz von Vorgängen, die Sie zulassen möchten einfach ausgedrückt wird, durch Ausschließen bestimmter Vorgänge anstatt aller Vorgänge Vorgänge außer als derjenigen, die Sie ausschließen möchten, verwenden Sie dann die **NotActions** Eigenschaft einer benutzerdefinierten Rolle. Der effektive Zugriff durch eine benutzerdefinierte Funktion wird berechnet, indem Sie außer der **NotActions** Vorgänge von den Aktionen-Vorgängen.

Beachten Sie, dass, wenn ein Benutzer eine Rolle zugewiesen ist, die einen Vorgang in schließt **NotActions** und eine zweite Rolle den Zugriff auf den gleichen Vorgang – gewährt die Benutzer zum Ausführen der Operation zugewiesen wird. **NotActions** ist kein Deny Regel – es ist einfach eine bequeme Möglichkeit, einen Satz von zulässigen Vorgänge erstellen, wenn bestimmte Operationen ausgeschlossen werden müssen.

### AssignableScopes
Die **AssignableScopes** -Eigenschaft der benutzerdefinierten Rolle gibt die Bereiche (Abonnements, oder Ressourcengruppen und Ressourcen), in dem die benutzerdefinierte Rolle für die Zuweisung zu Benutzern, Gruppen und Anwendungen verfügbar ist. Mithilfe von **AssignableScopes** können Sie die benutzerdefinierte Rolle für die Zuordnung in der nur die Abonnements oder Ressourcengruppen, die dies erfordern, und nicht überlasten Benutzerfunktionalität für den Rest des Abonnements oder Ressourcengruppen. **AssignableScopes** einer benutzerdefinierten Rolle auch steuern, wer berechtigt ist, anzuzeigen, aktualisieren und löschen Sie die Rolle. Im Folgenden sind einige gültige zuweisbare Bereiche aufgeführt:

-   „/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e“, „/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624“: Macht die Rolle für die Zuweisung in zwei Abonnements verfügbar.
-   „/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e“: Macht die Rolle für die Zuweisung in einem einzelnen Abonnement verfügbar.
-   „/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network“: Macht die Rolle für die alleinige Zuweisung in der Netzwerkressourcengruppe verfügbar.

### Zugriffssteuerung für benutzerdefinierte Rollen
Die **AssignableScopes** -Eigenschaft der benutzerdefinierten Rolle bestimmt die, die anzeigen, ändern und löschen Sie die Rolle.

**Wer kann eine benutzerdefinierte Rolle erstellen?**
Erstellen von benutzerdefinierten Rollen nur erfolgreich, wenn der Erstellung der Rolle zum Erstellen einer benutzerdefinierten Rolle für alle angegebenen Benutzer **AssignableScopes**. Erstellen von benutzerdefinierten Rollen nur erfolgreich, wenn der Benutzer mit dem Erstellen der Rolle ausführen kann `Microsoft.Authorization/roleDefinition/write operation` auf allen die **AssignableScopes** der Rolle. Besitzer (und Benutzerzugriff-Administratoren) von Abonnements, Ressourcengruppen und Ressourcen können also benutzerdefinierte Rollen für die Verwendung in diesen Bereichen erstellen.

**Wer kann eine benutzerdefinierte Rolle ändern?**
Benutzer, die berechtigt sind, benutzerdefinierte Rollen für alle aktualisieren **AssignableScopes** einer Rolle können dieser benutzerdefinierten Rolle ändern. Benutzer, die ausgeführt werden können `Microsoft.Authorization/roleDefinition/write` -Vorgang für alle der **AssignableScopes** einer benutzerdefinierten Rolle dieser benutzerdefinierten Rolle ändern können. Z. B. wenn eine benutzerdefinierte Funktion in zwei Azure-Abonnements zugewiesen werden (d. h. hat zwei Abonnements in seiner **AssignableScopes** Eigenschaft)-ein Benutzer muss Besitzer (oder Benutzeradministratoren) beide Abonnements verwenden, um die benutzerdefinierte Rolle ändern können.

**Wer kann benutzerdefinierte Rollen anzeigen, die für die Zuweisung in einem Bereich verfügbar sind?**
Benutzer, die den Vorgang `Microsoft.Authorization/roleDefinition/read` für einen Bereich durchführen können, können die RBAC-Rollen anzeigen, die für die Zuweisung in diesem Bereich verfügbar sind. Alle integrierten Rollen in Azure RBAC ermöglichen das Anzeigen von Rollen, die für die Zuweisung verfügbar sind.


