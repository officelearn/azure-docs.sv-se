---
title: Hantera databasbehörigheter i Azure Data Explorer
description: I den här artikeln beskrivs rollbaserade åtkomstkontroller för databaser och tabeller i Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: b4d5e56e990c0353f44209c6b19ae2d1727de27a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76030103"
---
# <a name="manage-azure-data-explorer-database-permissions"></a>Hantera behörigheter för Azure Data Explorer-databas

Med Azure Data Explorer kan du styra åtkomsten till databaser och tabeller med hjälp av en *rollbaserad åtkomstkontrollmodell.* Under den här modellen *mappas huvudnamn* (användare, grupper och appar) till *roller*. Huvudnamn kan komma åt resurser enligt de roller de har tilldelats.

I den här artikeln beskrivs tillgängliga roller och hur du tilldelar huvudnamn till dessa roller med hjälp av azure-portalen och Azure Data Explorer-hanteringskommandona.

## <a name="roles-and-permissions"></a>Roller och behörigheter

Azure Data Explorer har följande roller:

|Roll                       |Behörigheter                                                                        |
|---------------------------|-----------------------------------------------------------------------------------|
|Databasadministratör             |Kan göra vad som helst inom ramen för en viss databas.|
|Databasanvändare              |Kan läsa alla data och metadata i databasen. Dessutom kan de skapa tabeller (blir tabelladministratör för den tabellen) och funktioner i databasen.|
|Databasvisare            |Kan läsa alla data och metadata i databasen.|
|Databas ingestor          |Kan använda data till alla befintliga tabeller i databasen, men inte fråga data.|
|Databasövervakare           |Kan köra '.show ...' kommandon i databasens och dess underordnade enheter.|
|Tabell admin                |Kan göra vad som helst inom ramen för en viss tabell. |
|Tabell ingestor             |Kan inta data i omfattningen av en viss tabell, men inte fråga data.|

## <a name="manage-permissions-in-the-azure-portal"></a>Hantera behörigheter i Azure-portalen

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Navigera till azure data explorer-klustret.

1. I avsnittet **Översikt** väljer du den databas där du vill hantera behörigheter.

    ![Välj databas](media/manage-database-permissions/select-database.png)

1. Välj **Behörigheter** och lägg sedan **till**.

    ![Behörigheter för databaser](media/manage-database-permissions/database-permissions.png)

1. Under **Lägg till databasbehörigheter**väljer du den roll som du vill tilldela huvudmannen till och välj sedan **huvudnamn**.

    ![Lägga till databasbehörigheter](media/manage-database-permissions/add-permission.png)

1. Slå upp huvudmannen, markera det och **välj**sedan .

    ![Hantera behörigheter i Azure-portalen](media/manage-database-permissions/new-principals.png)

1. Välj **Spara**.

    ![Hantera behörigheter i Azure-portalen](media/manage-database-permissions/save-permission.png)

## <a name="manage-permissions-with-management-commands"></a>Hantera behörigheter med hanteringskommandon

1. Logga in [https://dataexplorer.azure.com](https://dataexplorer.azure.com)på och lägg till klustret om det inte redan är tillgängligt.

1. Välj lämplig databas i den vänstra rutan.

1. Använd `.add` kommandot för att tilldela `.add database databasename rolename ('aaduser | aadgroup=user@domain.com')`huvudnamn till roller: . Om du vill lägga till en användare i användarrollen Databas kör du följande kommando och ersätter databasnamnet och användaren.

    ```Kusto
    .add database <TestDatabase> users ('aaduser=<user@contoso.com>')
    ```

    Utdata för kommandot visar listan över befintliga användare och de roller som de har tilldelats i databasen.
    
    Exempel som rör Azure Active Directory och Kusto-auktoriseringsmodellen finns i [Principer och identitetsleverantörer](https://docs.microsoft.com/azure/kusto/management/access-control/principals-and-identity-providers)

## <a name="next-steps"></a>Nästa steg

[Skriva frågor](write-queries.md)
