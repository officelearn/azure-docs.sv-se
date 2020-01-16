---
title: Hantera databas behörigheter i Azure Datautforskaren
description: I den här artikeln beskrivs rollbaserade åtkomst kontroller för databaser och tabeller i Azure Datautforskaren.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: b4d5e56e990c0353f44209c6b19ae2d1727de27a
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "76030103"
---
# <a name="manage-azure-data-explorer-database-permissions"></a>Hantera behörigheter för Azure Datautforskaren-databasen

Med Azure Datautforskaren kan du styra åtkomsten till databaser och tabeller med hjälp av en *rollbaserad åtkomst kontroll* modell. Under den här modellen mappas *huvud konton* (användare, grupper och appar) till *roller*. Huvud konton kan komma åt resurser enligt de roller som de har tilldelats.

I den här artikeln beskrivs tillgängliga roller och hur du tilldelar huvud konton till dessa roller med hjälp av kommandona Azure Portal och Azure Datautforskaren Management.

## <a name="roles-and-permissions"></a>Roller och behörigheter

Azure Datautforskaren har följande roller:

|Roll                       |Behörigheter                                                                        |
|---------------------------|-----------------------------------------------------------------------------------|
|Databas administratör             |Kan göra vad som helst i en viss databas omfång.|
|Databas användare              |Kan läsa alla data och metadata i databasen. Dessutom kan de skapa tabeller (som blir tabell administratör för tabellen) och funktioner i-databasen.|
|Databas visare            |Kan läsa alla data och metadata i databasen.|
|Databas insamlare          |Kan mata in data till alla befintliga tabeller i databasen, men inte fråga efter data.|
|Databas övervakare           |Kan köra ". show..." kommandon i databasens kontext och dess underordnade entiteter.|
|Tabell administratör                |Kan göra vad som helst i omfånget för en viss tabell. |
|Tabell inhämtning             |Kan mata in data i omfånget för en viss tabell, men inte fråga efter data.|

## <a name="manage-permissions-in-the-azure-portal"></a>Hantera behörigheter i Azure Portal

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Navigera till ditt Azure Datautforskaren-kluster.

1. I avsnittet **Översikt** väljer du den databas som du vill hantera behörigheter för.

    ![Välj databas](media/manage-database-permissions/select-database.png)

1. Välj **behörigheter** och sedan **Lägg till**.

    ![Databas behörigheter](media/manage-database-permissions/database-permissions.png)

1. Under **Lägg till databas behörigheter**väljer du den roll som du vill tilldela huvudobjektet till och **väljer sedan huvud konton**.

    ![Lägg till databas behörigheter](media/manage-database-permissions/add-permission.png)

1. Leta upp huvud kontot, Välj det och **Välj**sedan.

    ![Hantera behörigheter i Azure Portal](media/manage-database-permissions/new-principals.png)

1. Välj **Spara**.

    ![Hantera behörigheter i Azure Portal](media/manage-database-permissions/save-permission.png)

## <a name="manage-permissions-with-management-commands"></a>Hantera behörigheter med hanterings kommandon

1. Logga in till [https://dataexplorer.azure.com](https://dataexplorer.azure.com)och Lägg till klustret om det inte redan är tillgängligt.

1. Välj lämplig databas i den vänstra rutan.

1. Använd `.add`-kommandot för att tilldela huvud konton till roller: `.add database databasename rolename ('aaduser | aadgroup=user@domain.com')`. Om du vill lägga till en användare i databas användar rollen kör du följande kommando och ersätter ditt databas namn och din användare.

    ```Kusto
    .add database <TestDatabase> users ('aaduser=<user@contoso.com>')
    ```

    Kommandots utdata visar listan över befintliga användare och de roller som de är kopplade till i databasen.
    
    Exempel som rör Azure Active Directory och Kusto-auktorisering finns i [principer och identitets leverantörer](https://docs.microsoft.com/azure/kusto/management/access-control/principals-and-identity-providers)

## <a name="next-steps"></a>Nästa steg

[Skriva frågor](write-queries.md)
