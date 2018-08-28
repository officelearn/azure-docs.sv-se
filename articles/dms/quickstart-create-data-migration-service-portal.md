---
title: Skapa en instans av Azure Database Migration Service med hjälp av Azure-portalen | Microsoft Docs
description: Använd Azure-portalen och skapa en instans av Azure Database Migration Service
services: database-migration
author: edmacauley
ms.author: jtoland
manager: craigg
ms.reviewer: ''
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: quickstart
ms.date: 08/13/2018
ms.openlocfilehash: f4dcc659d72edff1d8c2523cce1de059f1cf3fdf
ms.sourcegitcommit: 0fcd6e1d03e1df505cf6cb9e6069dc674e1de0be
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/14/2018
ms.locfileid: "42023541"
---
# <a name="create-an-instance-of-the-azure-database-migration-service-by-using-the-azure-portal"></a>Skapa en instans av Azure Database Migration Service med hjälp av Azure-portalen
I den här snabbstarten använder du Azure-portalen för att skapa en instans av Azure Database Migration Service.  När du har skapat tjänsten kan du använda den för att migrera data från en lokal SQL Server till en Azure SQL-databas.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="log-in-to-the-azure-portal"></a>Logga in på Azure-portalen
Öppna webbläsaren, gå till [Microsoft Azure-portalen](https://portal.azure.com/) och logga in genom att ange dina autentiseringsuppgifter.

Standardvyn är instrumentpanelen.

## <a name="register-the-resource-provider"></a>Registrera resursprovidern
Registrera resursprovidern Microsoft.DataMigration innan du skapar din första instans av Database Migration Service.

1. Gå till Azure-portalen och välj **Alla tjänster** och sedan **Prenumerationer**.

2. Välj den prenumeration där du vill skapa en instans av Azure Database Migration Service och välj sedan **Resursprovidrar**.

3. Sök efter migreringen och välj sedan **Registrera** till höger om **Microsoft.DataMigration**.

    ![Registrera resursprovider](media/quickstart-create-data-migration-service-portal/dms-register-provider.png)

## <a name="create-an-instance-of-the-service"></a>Skapar en instans av tjänsten
1. Välj +**Skapa en resurs** för att skapa en instans av Azure Database Migration Service.

2. Sök efter ”migration” på Marketplace och välj **Azure Database Migration Service**. På skärmen **Azure Database Migration Service** väljer du sedan **Skapa**.

3. På skärmen **Skapa migreringstjänst**: 

    - Välj ett unikt **tjänstnamn** som är lätt att komma ihåg, som identifierar din instans av Azure Database Migration Service.
    - Välj den Azure-**prenumeration** där du vill skapa instansen.
    - Välj en befintlig **resursgrupp** eller skapa en ny.
    - Välj den **plats** som ligger närmast din käll- eller målserver.
    - Välj ett befintligt **virtuellt nätverk** eller skapa ett nytt.

        Det virtuella nätverket ger Azure Database Migration Service åtkomst till källdatabasen och målmiljön.

        Mer information om hur du skapar ett virtuellt nätverk på Azure Portal finns i artikeln [Skapa ett virtuellt nätverk med hjälp av Azure Portal](https://aka.ms/vnet).

    - Välj Basic: 1 virtuell kärna för **Prisnivå**.

        ![Skapa migreringstjänsten](media/quickstart-create-data-migration-service-portal/dms-create-service1.png)

4. Välj **Skapa**.

    Efter en liten stund har din instans av Azure Database Migration Service skapats och är redo att användas. Database Migration Service visas som på den här bilden:

    ![Migreringstjänsten har skapats](media/quickstart-create-data-migration-service-portal/dms-service-created.png)

## <a name="clean-up-resources"></a>Rensa resurser
Rensa alla resurser som du har skapat i den här snabbstarten genom att ta bort [Azure-resursgruppen](../azure-resource-manager/resource-group-overview.md). Ta bort resursgruppen genom att navigera till instansen av Azure Database Migration Service som du skapade. Markera **resursgruppsnamnet** och välj sedan **Ta bort resursgrupp**. Den här åtgärden tar bort alla resurser i resursgruppen samt själva gruppen.

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Migrera lokal SQL Server till Azure SQL Database](tutorial-sql-server-to-azure-sql.md)