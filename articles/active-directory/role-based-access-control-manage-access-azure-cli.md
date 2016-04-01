<properties
    pageTitle="Verwalten der rollenbasierten Zugriffssteuerung (RBAC) mit der Azure-CLI | Microsoft Azure"
    description="Erfahren Sie, wie Sie den rollenbasierten Zugriff (RBAC) mit der Azure-Befehlszeilenschnittstelle verwalten, indem Sie Rollen und Rollenaktionen auflisten und Rollen im Kontext von Abonnements und Anwendungen zuweisen."
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
    ms.date="12/04/2015"
    ms.author="inhenk"/>

# Verwalten der rollenbasierten Zugriffssteuerung (RBAC) mit der Azure-Befehlszeilenschnittstelle (CLI)
<!-- Azure Selector -->
> [AZURE.SELECTOR]
- [PowerShell](role-based-access-control-manage-access-powershell.md)
- [Azure-Befehlszeilenschnittstelle](role-based-access-control-manage-access-azure-cli.md)

## Auflisten von RBAC-Rollen
### Auflisten aller verfügbarer Rollen
Um alle verfügbaren Rollen aufzulisten, verwenden Sie Folgendes:

        azure role list

Das folgende Beispiel zeigt die Liste der *alle verfügbaren Rollen*.

![](./media/role-based-access-control-manage-access-azure-cli/1-azure-role-list.png)

### Auflisten der Aktionen einer Rolle
Um die Aktionen einer Rolle aufzulisten, verwenden Sie Folgendes:

    azure role show <role in quotes>

Das folgende Beispiel zeigt die Aktionen der *Contributor* und *Mitwirkender von virtuellen Computern* Rollen.

![](./media/role-based-access-control-manage-access-azure-cli/1-azure-role-show.png)

##  Auflisten des Zugriffs
### Auflisten der effektiven Rollenzuweisungen für eine Ressourcengruppe
Liste rollenzuweisungen für eine Ressourcengruppe verwenden:

    azure role assignment list --resource-group <resource group name>

Das folgende Beispiel zeigt die rollenzuweisungen für den *Pharma-Sales-Projecforcast* Gruppe.

![](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-assignment-list-1.png)

### Auflisten von Rollenzuweisungen für einen Benutzer, einschließlich derer, die den Gruppen des Benutzers zugewiesen sind

Das folgende Beispiel zeigt die rollenzuweisungen für den *Pharma-Sales-Projecforcast* Gruppe.

![](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-assignment-list-2.png)

##  Gewähren von Zugriff
Nachdem Sie die Rolle, die Sie zuweisen möchten, identifiziert haben, verwenden Sie Folgendes, um den Zugriff zu gewähren:

    azure role assignment create

### Zuweisen einer Rolle zu einer Gruppe im Abonnementkontext
Um einer Gruppe im Abonnementkontext eine Rolle zuzuweisen, verwenden Sie Folgendes:

   Azure-rollenzuweisung zu erstellen – ObjId < Objekt-Id >--Rolle <name of role> --Bereich < Abonnement-Abonnement-Id >

Das folgende Beispiel weist die *Reader* Rolle *Christine Koch Team* an die *Abonnement* Bereich.

![](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-1.png)

### Zuweisen einer Rolle zu einer Anwendung im Abonnementkontext
Um einer Anwendung im Abonnementkontext eine Rolle zuzuweisen, verwenden Sie Folgendes:

    azure role assignment create --objId  <applications's object id> --role <name of role> --scope <subscription/subscription id>

Folgende Beispiel gewährt der *Contributor* Rolle eine *Azure AD* auf das ausgewählte Abonnement.

 ![](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-2.png)

### Zuweisen einer Rolle zu einem Benutzer im Ressourcengruppenkontext
Um einem Benutzer im Ressourcengruppenkontext eine Rolle zuzuweisen, verwenden Sie Folgendes:

    azure role assignment create --signInName  <user's email address> --roleName <name of role in quotes> --resourceGroup <resource group name>

Folgende Beispiel gewährt der *Mitwirkender von virtuellen Computern* Rolle Benutzer *samert@aaddemo.com* an die *Pharma-Sales-ProjectForcast* Ressource des Bereichs.

![](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-3.png)

### Zuweisen einer Rolle zu einer Gruppe im Ressourcenkontext
Um einer Gruppe im Ressourcenkontext eine Rolle zuzuweisen, verwenden Sie Folgendes:

    azure role assignment create --objId  <group id> --roleName <name of role in quotes> --resource-name <resource group name> --resource-type <resource group type> --parent <resource group parent> --resource-group <resource group>

Folgende Beispiel gewährt der *Mitwirkender von virtuellen Computern* Rolle eine *Azure AD* auf eine *Subnetz*.

![](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-4.png)

##  Zugriff entfernen
Um eine Rollenzuordnung zu entfernen, verwenden Sie Folgendes:

    azure role assignment delete --objId <object id to from which to remove role> --roleName <role name>

Folgende Beispiel entfernt die *Mitwirkender von virtuellen Computern* rollenzuweisung von *sammert@aaddemo.com* auf der *Pharma-Sales-ProjectForcast* Ressourcengruppe.
Anschließend wird die Rollenzuweisung aus einer Gruppe im Abonnement entfernt.

![](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-assignment-delete.png)

## Erstellen einer benutzerdefinierten Rolle
Verwenden Sie zum Erstellen einer benutzerdefinierten Rolle den Befehl `azure role create`.

Das folgende Beispiel erstellt eine benutzerdefinierte Rolle namens *Virtual Machine-Operator* gewährt Zugriff auf alle Lesevorgänge des *Microsoft.Compute*, *Microsoft.Storage*, und *Microsoft.Network* Ressourcenanbieter gewährt Zugriff gestartet werden, neu starten und Überwachen von virtuellen Maschinen. Die benutzerdefinierte Rolle kann in zwei Abonnements verwendet werden. In diesem Beispiel wird eine JSON-Datei als Eingabe genutzt.

![](./media/role-based-access-control-manage-access-azure-cli/2-azure-role create-1.png)

![](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-create-2.png)

## Ändern einer benutzerdefinierten Rolle

Um eine benutzerdefinierte Rolle zunächst zu ändern, verwenden Sie den Befehl zum Anzeigen der Azure-Rolle, um die Rollendefinition abzurufen. Nehmen Sie dann die gewünschten Änderungen an der Rollendefinition vor. Verwenden Sie abschließend den Befehl zum Festlegen der Azure-Rolle, um die geänderte Rollendefinition zu speichern.

Im folgenden Beispiel wird der Vorgang „Microsoft.Insights/diagnosticSettings/*“ den Aktionen und ein Azure-Abonnement dem Element „AssignableScopes“ der benutzerdefinierten Rolle „Virtual Machine Operator“ hinzugefügt.

![](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-set-1.png)

![](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-set2.png)

## Löschen einer benutzerdefinierten Rolle

Verwenden Sie zum Löschen einer benutzerdefinierten Rolle zuerst die `azure role show` Befehl aus, um zu bestimmen die **Id** der Rolle. Verwenden Sie dann `azure role delete` Befehl zum Löschen der Rolle durch Angabe der **Id**.

Im folgenden Beispiel wird die *Virtual Machine-Operator* benutzerdefinierte Rolle.

![](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-delete.png)

## Auflisten benutzerdefinierter Rollen 

Zum Auflisten der Rollen, die in einem Bereich für die Zuweisung verfügbar sind, wählen Sie den Befehl `azure role list`.

Im folgenden Beispiel werden alle Rollen aufgelistet, die für die Zuweisung im ausgewählten Abonnement verfügbar sind.

![](./media/role-based-access-control-manage-access-azure-cli/5-azure-role-list1.png)

Im folgenden Beispiel die *Virtual Machine-Operator* benutzerdefinierte Rolle ist nicht verfügbar in der *Production4* Abonnement, da in diesem Abonnement ist nicht der **AssignableScopes** der Rolle.

![](./media/role-based-access-control-manage-access-azure-cli/5-azure-role-list2.png)





## RBAC-Themen
[AZURE.INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]


