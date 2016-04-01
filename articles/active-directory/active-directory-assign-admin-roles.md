<properties
    pageTitle="Zuweisen von Administratorrollen in Azure Active Directory | Microsoft Azure"
    description="Erläutert, welche Administratorrollen in Azure Active Directory verfügbar sind und wie sie zugewiesen werden."
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="stevenpo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/01/2015"
    ms.author="curtand"/>

# Zuweisen von Administratorrollen in Azure Active Directory (Azure AD)

Abhängig von der Größe Ihres Unternehmens möchten Sie möglicherweise mehrere Administratoren ernennen, die verschiedene Funktionen ausüben. Diese Administratoren haben Zugriff auf verschiedene Funktionen im Azure-Portal oder im klassischen Azure-Portal und können abhängig von ihrer Rolle unter anderem Benutzer erstellen oder bearbeiten, anderen administrative Rollen zuweisen, Benutzerkennwörter zurücksetzen, Benutzerlizenzen verwalten und Domänen verwalten.

Es ist wichtig zu verstehen, dass ein Benutzer eine Administratorrolle zugewiesen ist dieselben in allen Cloud-Dienste verfügen über die Berechtigungen, die Ihre Organisation abonniert hat, unabhängig davon, ob Sie die Rolle im Office 365-Portal oder im klassischen Azure-Portal oder mithilfe des Azure AD-Moduls für Windows PowerShell zuweisen.

Die folgenden Administratorrollen sind verfügbar:

- **Abrechnungsadministrator**: erledigt Käufe, verwaltet Abonnements, verwaltet supporttickets und überwacht den Dienststatus.
- **Globaler Administrator**: hat Zugriff auf alle administrativen Funktionen. Die Person, die die Anmeldung für das Azure-Konto vornimmt, wird ein globaler Administrator. Nur globale Administratoren können weitere Administratorrollen zuweisen. In Ihrem Unternehmen können mehrere globale Administratoren vorhanden sein.
- **Kennwortadministrator**: setzt Kennwörter zurück, verwaltet Serviceanfragen und überwacht den Dienststatus. Kennwortadministratoren können Kennwörter nur für Benutzer und andere Kennwortadministratoren zurücksetzen.
- **Dienstadministrator**: verwaltet Serviceanfragen und überwacht den Dienststatus.
> [AZURE.NOTE]
> Um einem Benutzer die Dienstadministratorrolle zuzuweisen, muss der globale Administrator zunächst dem Benutzer Administratorberechtigungen im Dienst (z. B. Exchange Online) zuweisen, und anschließend die Dienstadministratorrolle im klassischen Azure-Portal.

- **Benutzeradministrator**: setzt Kennwörter zurück, überwacht den Dienststatus und verwaltet Benutzerkonten, Benutzergruppen und Serviceanfragen. Für die Berechtigungen eines Benutzerverwaltungsadministrators gelten einige Einschränkungen. Sie können z. B. keinen globalen Administrator löschen oder andere Administratoren erstellen. Sie können außerdem keine Kennwörter für Abrechnungs-, globale und Dienstadministratoren zurücksetzen.

## Administratorberechtigungen

### Abrechnungsadministrator

Möglich | Nicht möglich
------------- | -------------
<p>Anzeigen von Unternehmens- und Benutzerinformationen Informationen</p><p>Verwalten von Office-Support-tickets</p><p>Abrechnung und Einkauf für Office-Produkte ausführen</p> | <p>Zurücksetzen von Benutzerkennwörtern</p><p>Erstellen und Verwalten von Benutzeransichten</p><p>Erstellen, bearbeiten und Löschen von Benutzern und Gruppen und Verwalten von Benutzerlizenzen</p><p>Verwalten von Domänen</p><p>Verwalten von Unternehmensinformationen</p><p>Delegieren von Administratorrollen an andere Benutzer</p><p>Verwenden der verzeichnissynchronisierung</p>

### Globaler Administrator

Möglich | Nicht möglich
------------- | -------------
<p>Anzeigen von Unternehmens- und Benutzerinformationen Informationen</p><p>Verwalten von Office-Support-tickets</p><p>Abrechnung und Einkauf für Office-Produkte ausführen</p> <p>Zurücksetzen von Benutzerkennwörtern</p><p>Erstellen und Verwalten von Benutzeransichten</p><p>Erstellen, bearbeiten und Löschen von Benutzern und Gruppen und Verwalten von Benutzerlizenzen</p><p>Verwalten von Domänen</p><p>Verwalten von Unternehmensinformationen</p><p>Delegieren von Administratorrollen an andere Benutzer</p><p>Verwenden der verzeichnissynchronisierung</p><p>Aktivieren Sie oder deaktivieren Sie die mehrstufige Authentifizierung</p> | N/V

### Kennwortadministrator

Möglich | Nicht möglich
------------- | -------------
<p>Anzeigen von Unternehmens- und Benutzerinformationen Informationen</p><p>Verwalten von Office-Support-tickets</p><p>Zurücksetzen von Benutzerkennwörtern</p> | <p>Abrechnung und Einkauf für Office-Produkte ausführen</p><p>Erstellen und Verwalten von Benutzeransichten</p><p>Erstellen, bearbeiten und Löschen von Benutzern und Gruppen und Verwalten von Benutzerlizenzen</p><p>Verwalten von Domänen</p><p>Verwalten von Unternehmensinformationen</p><p>Delegieren von Administratorrollen an andere Benutzer</p><p>Verwenden der verzeichnissynchronisierung</p>

### Dienstadministrator

Möglich | Nicht möglich
------------- | -------------
<p>Anzeigen von Unternehmens- und Benutzerinformationen Informationen</p><p>Verwalten von Office-Support-tickets</p> | <p>Zurücksetzen von Benutzerkennwörtern</p><p>Abrechnung und Einkauf für Office-Produkte ausführen</p><p>Erstellen und Verwalten von Benutzeransichten</p><p>Erstellen, bearbeiten und Löschen von Benutzern und Gruppen und Verwalten von Benutzerlizenzen</p><p>Verwalten von Domänen</p><p>Verwalten von Unternehmensinformationen</p><p>Delegieren von Administratorrollen an andere Benutzer</p><p>Verwenden der verzeichnissynchronisierung</p>

### Benutzeradministrator

Möglich | Nicht möglich
------------- | -------------
<p>Anzeigen von Unternehmens- und Benutzerinformationen Informationen</p><p>Verwalten von Office-Support-tickets</p><p>Zurücksetzen von Benutzerkennwörtern, eingeschränkt. Er kann keine Kennwörter für rechnungsadministratoren, globale und Dienstadministratoren zurücksetzen.</p><p>Erstellen und Verwalten von Benutzeransichten</p><p>Erstellen, bearbeiten und Löschen von Benutzern und Gruppen und Verwalten von Benutzerlizenzen mit Einschränkungen. Er kann keinen globalen Administrator löschen und andere Administratoren erstellen.</p> | <p>Abrechnung und Einkauf für Office-Produkte ausführen</p><p>Verwalten von Domänen</p><p>Verwalten von Unternehmensinformationen</p><p>Delegieren von Administratorrollen an andere Benutzer</p><p>Verwenden der verzeichnissynchronisierung</p><p>Aktivieren Sie oder deaktivieren Sie die mehrstufige Authentifizierung</p>

## Details zur globalen Administratorrolle

Der globale Administrator hat Zugriff auf alle administrativen Funktionen. Standardmäßig ist die Person, die für ein Azure-Abonnement registriert für das Verzeichnis die globalen Administratorrolle zugewiesen. Nur globale Administratoren können weitere Administratorrollen zuweisen.

## Zuweisen oder Entfernen von Administratorrollen


1. Klicken Sie in der klassischen Azure-Portal auf **Active Directory**, und klicken Sie dann auf den Namen des Verzeichnisses Ihrer Organisation.
2. Auf der **Benutzer** auf den Anzeigenamen des Benutzers, die Sie bearbeiten möchten.
3. In der **Organisationsrolle** wählen Sie die Administratorrolle, die zu diesem Benutzer zugewiesen werden soll, oder wählen Sie **Benutzer** wenn Sie eine vorhandene Administratorrolle entfernen möchten.
4. In der **Alternative e-Mail-Adresse** Geben Sie eine e-Mail-Adresse. Diese E-Mail-Adresse wird für wichtige Benachrichtigungen, einschließlich des automatischen Zurücksetzens des Kennworts verwendet, so dass der Benutzer Zugriff auf das E-Mail-Konto benötigt, und zwar unabhängig davon, ob er auf Azure zugreifen kann oder nicht.
5. Wählen Sie **Zulassen** oder **Block** angeben, ob der Benutzer sich anmelden und den Zugriff auf Dienste.
6. Geben Sie einen Speicherort aus der **Verwendungsort** Dropdown-Liste.
7. Wenn Sie fertig sind, klicken Sie auf **Speichern**.

## Nächste Schritte

- [Verwalten von Benutzern](../active-directory-manage-users.md)
- [Verwalten von Kennwörtern](active-directory-manage-passwords.md)
- [Gruppen verwalten](active-directory-manage-groups.md)


