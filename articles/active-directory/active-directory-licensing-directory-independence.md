<properties
   pageTitle="Hinzufügen und Verwalten von mehreren Azure Active Directory-Verzeichnissen | Microsoft Azure"
   description="Anleitungen und bewährte Methoden für das Hinzufügen und Verwalten von Azure Active Directory-Verzeichnissen, wobei Verzeichnisse als vollständig unabhängige Ressource betrachtet werden"
   services="active-directory"
   documentationCenter=""
   authors="curtand"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="12/01/2015"
   ms.author="curtand"/>

# Hinzufügen und Verwalten von mehreren Azure Active Directory-Verzeichnissen

In Azure Active Directory (Azure AD) ist jedes Verzeichnis eine vollständig unabhängige Ressource: gleichgestellt, voll funktionsfähig und logisch unabhängig von anderen Verzeichnissen, die Sie verwalten. Es gibt keine unter- und übergeordneten Beziehungen zwischen den Verzeichnissen. Diese Unabhängigkeit zwischen den Verzeichnissen beinhaltet Ressourcen-, Verwaltungs- und Synchronisierungsunabhängigkeit.

##Ressourcenunabhängigkeit

Wenn Sie eine Ressource in einem Verzeichnis erstellen oder löschen, hat dies keine Auswirkungen auf Ressourcen in einem anderen Verzeichnis. Eine teilweise geltende Ausnahme bilden externe Benutzer, wie unten beschrieben. Wenn Sie eine benutzerdefinierte Domäne "contoso.com" in einem Verzeichnis verwenden, kann sie in keinem anderen Verzeichnis verwendet werden.

##Verwaltungsunabhängigkeit

Wenn ein Benutzer ohne Administratorrechte des Verzeichnisses Contoso ein Testverzeichnis "Test" dann erstellt:
- Standardmäßig ist der Benutzer, der ein Verzeichnis erstellt als externer Benutzer in diesem neuen Verzeichnis hinzugefügt und in das Verzeichnis die globalen Administratorrolle zugewiesen.
- Die Administratoren des Verzeichnisses "Contoso" haben keine direkten Administratorberechtigungen in Verzeichnis "Test", es sei denn, ein Administrator von 'Test' speziell ihnen diese Berechtigungen erteilt. Administratoren von "Contoso" können Zugriff auf Verzeichnis "Test" steuern, wenn sie das Benutzerkonto steuern, das welche die erstellten 'Test'.
- Wenn Sie ändern (hinzufügen oder entfernen) eine Administratorrolle für einen Benutzer in einem Verzeichnis, die Änderung wirkt sich kein Administratorrollen, die der Benutzer in einem anderen Verzeichnis besitzt.

##Synchronisierungsunabhängigkeit

Sie können jedes Azure AD-Verzeichnis unabhängig voneinander zum Abrufen von Daten aus einer einzelnen Instanz von entweder synchronisiert konfigurieren:
  - Das Tool Verzeichnissynchronisierung (DirSync) zum Synchronisieren von Daten mit einer AD-Gesamtstruktur.
  - Die Azure Active Directory Connector für Forefront Identity Manager zum Synchronisieren von Daten mit einer oder mehreren lokalen Gesamtstrukturen und/oder nicht-Azure AD-Datenquellen.

##Hinzufügen eines Azure AD-Verzeichnisses

Um Azure AD-Verzeichnis im klassischen Azure-Portal hinzuzufügen, wählen Sie die Azure Active Directory-Erweiterung auf der linken Seite, und tippen Sie auf **Hinzufügen**.

> [AZURE.NOTE]   Im Gegensatz zu anderen Azure-Ressourcen sind Ihre Verzeichnisse keine untergeordneten Ressourcen eines Azure-Abonnements. Wenn Sie kündigen oder Ihr Azure-Abonnement ablaufen lassen, können Sie weiterhin auf Ihre Verzeichnisdaten mithilfe von Azure PowerShell, der Azure Graph-API oder anderen Schnittstellen wie Office 365 Admin Center zugreifen. Sie können dem Verzeichnis auch ein anderes Abonnement zuordnen.

Eine allgemeine Übersicht über Azure AD-Lizenzierungsprobleme und bewährte Methoden finden Sie unter [Was ist Azure Active Directory-Lizenzierung?](active-directory-licensing-what-is.md).


