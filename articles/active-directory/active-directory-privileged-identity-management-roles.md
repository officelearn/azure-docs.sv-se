<properties
   pageTitle="Azure Privileged Identity Management: Rollen"
   description="Erfahren Sie, welche Rollen mit der Erweiterung Azure Privileged Identity Management für privilegierte Identitäten verwendet werden."
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

# Azure Privileged Identity Management: Rollen

<!-- **PLACEHOLDER: Need description of how this works. Azure PIM uses roles from MSODS objects.**-->

## Rollen aus Active Directory Azure, Office 365 und anderen Quellen

Azure PIM verwendet die folgenden Rollen als Standardadministratorrollen.

- Globaler Administrator
- Abrechnungsadministrator
- Dienstadministrator
- Benutzeradministrator
- Kennwortadministrator

Weitere Informationen zu Funktionen von Office 365, Exchange Online, Sharepoint Online und Skype for Business klicken Sie hier.[Zuweisen von Administratorrollen in Office 365](https://support.office.com/en-us/article/Assigning-admin-roles-in-Office-365-eac4d046-1afd-4f1a-85fc-8219c79e1504?ui=en-US&rs=en-US&ad=US)

<!--**PLACEHOLDER: The above article may not be the one we want since PIM gets roles from places other that Office 365**-->


<!-- ## The PIM Security Administrator Role **PLACEHOLDER: Need description of the Security Administrator role.**-->

## Benutzerrollen und Durchführen der Anmeldung
> [AZURE.NOTE]Für einen Benutzer an Azure PIM anmelden können müssen sie eine Lizenz für Azure verfügen.

## Zuweisen einer Lizenz an einen Benutzer in Azure AD

> [AZURE.NOTE] Die Lizenzoption wird nur angezeigt, wenn Lizenzen tatsächlich für dieses Abonnement beenden.

1. Ein globales Administratorkonto oder ein CO-Administrator-Konto melden Sie sich [http://manage.windowsazure.com] (http://manage.windowsazure.com).
2. Klicken Sie auf **alle Elemente** im Hauptmenü.
3. Wählen Sie das Verzeichnis aus, das Sie verwenden möchten, und dem Lizenzen zugeordnet sind.
4. Klicken Sie auf **Lizenzen**. Die Liste der verfügbaren Lizenzen wird angezeigt.
5. Klicken Sie auf den Lizenzplan, der die zu verteilenden Lizenzen enthält.
6. Klicken Sie auf **Zuweisen von Benutzern**.
7. Wählen Sie den Benutzer aus, dem Sie eine Lizenz zuweisen möchten.
8. Klicken Sie auf die **Zuweisen** Schaltfläche.  Der Benutzer kann sich jetzt bei Azure anmelden.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Nächste Schritte
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]


