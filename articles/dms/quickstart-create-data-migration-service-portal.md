---
title: "Skapa Azure-databas migrering tjänstinstansen med Azure-portalen | Microsoft Docs"
description: "Använda Azure portal för att skapa en instans av tjänsten Azure Database migrering"
services: database-migration
author: edmacauley
ms.author: edmaca
manager: craigg
ms.reviewer: 
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: quickstart
ms.date: 11/08/2017
ms.openlocfilehash: 4213a0ae73c38dcad403140e3d45e28f2d338020
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2017
---
# <a name="create-a-database-migration-service-instance-using-the-azure-portal"></a>Skapa en instans av databasen migreringstjänsten med hjälp av Azure portal
I den här snabbstartsguide använder du Azure-portalen för att skapa en instans av tjänsten Azure Database migrering.  När du har skapat tjänsten kommer du att kunna använda den för att migrera data från SQL Server på lokala till en Azure SQL database.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="log-in-to-the-azure-portal"></a>Logga in på Azure Portal
Öppna webbläsaren och gå till [Microsoft Azure Portal](https://portal.azure.com/). Ange dina autentiseringsuppgifter och logga in på portalen. Standardvyn är instrumentpanelen.

## <a name="create-azure-database-migration-service"></a>Skapa Migreringstjänst för Azure-databas
1. Klicka på  **+**  att skapa en ny tjänst.  Migreringstjänst för databasen är fortfarande under förhandsgranskning.  

1. Sök marketplace för ”migreringen”, Välj ”migrering databastjänsten (förhandsgranskning)” och sedan på **skapa**.

    ![Skapa migreringstjänst](media/quickstart-create-data-migration-service-portal/dms-create-service.png)

    - Välj en **tjänstnamnet** som är lätt att komma ihåg och unikt identifiera din Azure-databas migrering Service-instans.
    - Välj din Azure **prenumeration** i som du vill skapa databastjänsten för migrering.
    - Skapa en ny **nätverk** med ett unikt namn.
    - Välj den **plats** som ligger närmast din käll- eller server.
    - Välj Basic: 1 vCore för den **prisnivå**.

1. Klicka på **Skapa**.

Efter en liten stund blir tjänsten Azure Databasmigrering skapade och redo att användas.  Migrering databastjänsten visas som visas i bilden.

![Migreringstjänsten har skapats](media/quickstart-create-data-migration-service-portal/dms-service-created.png)

## <a name="clean-up-resources"></a>Rensa resurser
Du kan rensa de resurser som du skapade i Snabbstart genom att ta bort den [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md).  Om du vill ta bort resursgruppen navigerar du till migrering databastjänsten du har skapat, klicka på den **resursgruppen** namn och välj sedan **ta bort resursgruppen**.  Den här åtgärden tar bort alla tillgångar i resursgruppen samt själva gruppen.

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Migrera SQL Server lokalt till Azure SQL DB](tutorial-sql-server-to-azure-sql.md)