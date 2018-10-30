---
title: Hantera databasbehörigheter för Azure Data Explorer
description: Den här artikeln beskriver rollbaserade åtkomstkontroller för databaser och tabeller i Datautforskaren i Azure.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 287b95b59b0ec2b308d3e455c4f6ffce4baf4ff7
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2018
ms.locfileid: "50212887"
---
# <a name="manage-azure-data-explorer-database-permissions"></a>Hantera databasbehörigheter för Azure Data Explorer

Azure Data Explorer kan du styra åtkomsten till databaser och tabeller, med hjälp av en *rollbaserad åtkomstkontroll* modellen. Med den här modellen *huvudkonton* (användare, grupper och appar) mappas till *roller*. Huvudnamn komma åt resurser enligt de roller som de tilldelats.

Den här artikeln beskriver de tillgängliga rollerna och tilldela huvudnamn till dessa roller med hjälp av Azure-portalen och kommandon för hantering av Azure Data Explorer.

## <a name="roles-and-permissions"></a>Roller och behörigheter

Azure Data Explorer har följande roller:

|Roll                       |Behörigheter                                                                        |
|---------------------------|-----------------------------------------------------------------------------------|
|Databas-administratör             |Göra allt inom omfånget för en viss databas.|
|Databasanvändare              |Kan läsa alla data och metadata i databasen. De kan även skapa tabeller (bli tabell administratören för tabellen) och funktioner i databasen.|
|Visningsprogrammet för databasen            |Kan läsa alla data och metadata i databasen.|
|Databas-lösning          |Kan mata in data till alla befintliga tabeller i databasen, men inte fråga efter data.|
|Databasövervakning           |Kan utföra '.show... ”-kommandon i kontexten för databasen och dess underordnade entiteter.|
|Tabell-administratör                |Göra allt inom omfånget för en viss tabell. |
|Tabell-lösning             |Kan mata in data i omfånget för en viss tabell, men inte fråga efter data.|

## <a name="manage-permissions-in-the-azure-portal"></a>Hantera behörigheter i Azure portal

1. Logga in på [Azure Portal](https://portal.azure.com/).

1. Gå till Datautforskaren i Azure-kluster.

1. I den **översikt** väljer du den databas där du vill hantera behörigheter.

    ![Välj databas](media/manage-database-permissions/select-database.png)

1. Välj **behörigheter** sedan **lägga till**.

    ![Databasbehörighet](media/manage-database-permissions/database-permissions.png)

1. Under **lägga till databasbehörigheter**, Välj den roll som du vill tilldela huvudkontot till, sedan **Välj huvudkonton**.

    ![Lägg till databasbehörigheter](media/manage-database-permissions/add-permission.png)

1. Leta upp huvudnamnet, markera den sedan **Välj**.

    ![Hantera behörigheter i Azure portal](media/manage-database-permissions/new-principals.png)

1. Välj **Spara**.

    ![Hantera behörigheter i Azure portal](media/manage-database-permissions/save-permission.png)

## <a name="manage-permissions-with-management-commands"></a>Hantera behörigheter med kommandon för hantering

1. Logga in på [ https://dataexplorer.azure.com ](https://dataexplorer.azure.com), och Lägg till ditt kluster om den inte redan är tillgängliga.

1. I den vänstra rutan väljer du lämplig databas.

1. Använd den `.add` kommando för att tilldela roller huvudnamn: `.add database databasename rolename ('aaduser | aadgroup=user@domain.com')`. Om du vill lägga till en användare till rollen databasen kör du följande kommando, och ersätt dina databasens namn och en användare.

    ```Kusto
    .add database <TestDatabase> users ('aaduser=<user@contoso.com>')
    ```

    Utdata från kommandot visar listan över befintliga användare och roller som de är tilldelade till i databasen.

## <a name="next-steps"></a>Nästa steg

[Skriv frågor](write-queries.md)
