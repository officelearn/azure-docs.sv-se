---
title: 'Snabbstart: Skapa en instans av Azure Database Migration Service med hjälp av Azure-portalen | Microsoft Docs'
description: Använd Azure-portalen och skapa en instans av Azure Database Migration Service
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: quickstart
ms.date: 03/12/2019
ms.openlocfilehash: af5ffdb1c1f030c2bbc0616d027c06b59f1a34de
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60767783"
---
# <a name="quickstart-create-an-instance-of-the-azure-database-migration-service-by-using-the-azure-portal"></a>Snabbstart: Skapa en instans av Azure Database Migration Service med hjälp av Azure-portalen
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

    - Välj Grundläggande: 1 virtuell kärna för **prisnivån**.

        ![Skapa migreringstjänsten](media/quickstart-create-data-migration-service-portal/dms-create-service1.png)

4. Välj **Skapa**.

    Efter en liten stund har din instans av Azure Database Migration Service skapats och är redo att användas. Database Migration Service visas som på den här bilden:

    ![Migreringstjänsten har skapats](media/quickstart-create-data-migration-service-portal/dms-service-created.png)

## <a name="clean-up-resources"></a>Rensa resurser
Rensa alla resurser som du har skapat i den här snabbstarten genom att ta bort [Azure-resursgruppen](../azure-resource-manager/resource-group-overview.md). Ta bort resursgruppen genom att navigera till instansen av Azure Database Migration Service som du skapade. Markera **resursgruppsnamnet** och välj sedan **Ta bort resursgrupp**. Den här åtgärden tar bort alla resurser i resursgruppen samt själva gruppen.

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Migrera lokal SQL Server till Azure SQL Database](tutorial-sql-server-to-azure-sql.md)