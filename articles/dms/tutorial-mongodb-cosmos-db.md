---
title: 'Självstudie: Använda Azure Database Migration Service till att migrera MongoDB till Mongo API för Azure Cosmos DB offline | Microsoft Docs'
description: Lär dig att migrera från MongoDB lokalt till Mongo API för Azure Cosmos DB offline, genom att använda Azure Database Migration Service.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: ''
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 12/11/2018
ms.openlocfilehash: 46e8ca97411e9dce77c0c82c4e0f5dd164bce01b
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2018
ms.locfileid: "53320742"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-db-mongo-api-offline-using-dms"></a>Självstudie: Migrera MongoDB till Mongo API för Azure Cosmos DB offline med DMS
Du kan använda Azure Database Migration Service till att utföra en offlinemigrering (en gång) av databaser från en lokal instans eller en molninstans av MongoDB till Mongo API för Azure Cosmos DB.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Skapa en instans av Azure Database Migration Service.
> * Skapa ett migreringsprojekt med hjälp av Azure Database Migration Service.
> * Köra migreringen.
> * Övervaka migreringen.

I den här självstudien migrerar du datamängden **Wingtips** i MongoDB på en virtuell Azure-dator, till MongoDB API för Cosmos DB med hjälp av Azure Database Migration Service. Om du inte har konfigurerat någon MongoDB-källa, kan du läsa artikeln [Installera och konfigurera MongoDB på en virtuell Windows-dator i Azure](https://docs.microsoft.com/azure/virtual-machines/windows/install-mongodb).

## <a name="prerequisites"></a>Nödvändiga komponenter
För att slutföra den här kursen behöver du:
- [Skapa ett MongoDB API-konto för Azure Cosmos DB](https://ms.portal.azure.com/#create/Microsoft.DocumentDB).
- Skapa virtuellt nätverk för Azure Database Migration Service genom att använda Azure Resource Manager-distributionsmodellen, som ger plats-till-plats-anslutning för dina lokala källservrar genom att använda [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) eller [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Kontrollera att dina NSG-regler för Azure Virtual Network (VNET) inte blockerar följande kommunikationsportar: 443, 53, 9354, 445 och 12000. Mer information om trafikfiltrering för Azure VNET NSG finns i artikeln om att [filtrera nätverkstrafik med nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Öppna Windows-brandväggen så att Azure Database Migration Service kommer åt MongoDB-källservern, med standardinställningen TCP-port 27017.
- När du använder en brandväggsinstallation framför dina källdatabaser kanske du måste lägga till brandväggsregler för att tillåta Azure Database Migration Service att komma åt källdatabaserna för migrering.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrera resursprovidern Microsoft.DataMigration
1. Logga in på Azure-portalen och välj **Alla tjänster** och sedan **Prenumerationer**.

   ![Visa portalprenumerationer](media/tutorial-mongodb-to-cosmosdb/portal-select-subscription1.png)
       
2. Välj den prenumeration där du vill skapa en instans av Azure Database Migration Service och välj sedan **Resursprovidrar**.
 
    ![Visa resursprovidrar](media/tutorial-mongodb-to-cosmosdb/portal-select-resource-provider.png)
    
3.  Sök efter migreringen och välj sedan **Registrera** till höger om **Microsoft.DataMigration**.
 
    ![Registrera resursprovider](media/tutorial-mongodb-to-cosmosdb/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Skapa en instans
1.  I Azure Portal väljer du + **Skapa en resurs**, söker efter Azure Database Migration Service och väljer sedan **Azure Database Migration Service** i listrutan.

    ![Azure Marketplace](media/tutorial-mongodb-to-cosmosdb/portal-marketplace.png)

2.  På sidan **Azure Database Migration Service** väljer du **Skapa**.
 
    ![Skapa Azure Database Migration Service-instans](media/tutorial-mongodb-to-cosmosdb/dms-create1.png)
  
3.  På sidan **Create Migration Service** anger du ett namn för tjänsten, prenumerationen och en ny eller befintlig resursgrupp.

4. Välj den plats där du vill skapa instansen av Azure Database Migration Service. 

5. Välj ett befintligt virtuellt nätverk eller skapa ett nytt.

    Det virtuella nätverket ger Azure Database Migration Service åtkomst till käll-SQL Server och målinstansen av Azure SQL Database.

    Mer information om hur du skapar ett virtuellt nätverk i Azure Portal finns i artikeln [Skapa ett virtuellt nätverk med hjälp av Azure-portalen](https://aka.ms/DMSVnet).

6. Välj en prisnivå.

    Mer information om kostnader och prisnivåer finns på [sidan med priser](https://aka.ms/dms-pricing).

    Om du behöver hjälp med att välja rätt Azure Database Migration Service-nivå, kan du läsa rekommendationerna i blogginlägget [här](https://go.microsoft.com/fwlink/?linkid=861067).  

     ![Konfigurera Azure Database Migration Service-instansinställningar](media/tutorial-mongodb-to-cosmosdb/dms-settings2.png)

7.  Välj **Skapa** för att skapa tjänsten.

## <a name="create-a-migration-project"></a>Skapa ett migreringsprojekt
När tjänsten har skapats letar du reda på den i Azure Portal, öppnar den och skapar sedan ett nytt migreringsprojekt.

1. I Azure Portal väljer du **Alla tjänster**, söker efter Azure Database Migration Service och väljer sedan **Azure Database Migration Services**.
 
      ![Leta reda på alla instanser Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb/dms-search.png)

2. På sidan **Azure Database Migration Services** söker du efter namnet på Azure Database Migration Service-instansen du har skapat och väljer sedan instansen.

3. Välj + **Nytt migreringsprojekt**.

4. På sidan **Nytt migreringsprojekt** anger du namnet på projektet. I textrutan **Typ av källserver** väljer du **MongoDB**, i textrutan **Målservertyp** väljer du **CosmosDB (MongoDB API)** och i **Välj typ av aktivitet** väljer du sedan **Offline-datamigrering**. 

    ![Skapa ett Database Migration Service-projekt](media/tutorial-mongodb-to-cosmosdb/dms-create-project.png)

5.  Välj **Skapa och kör aktivitet** för att skapa projektet och köra migreringsaktiviteten.

## <a name="specify-source-details"></a>Ange källinformation
1. På sidan **Källinformation** anger du anslutningsinformationen för MongoDB-källservern.
    
   Du kan också använda anslutningssträngsläget och ange en plats för en container till blogglagerfilen, där du har säkerhetskopierade samlingsdata som du planerar att migrera.

   > [!NOTE]
   > Azure Database Migration Service kan även migrera bson-dokument eller json-dokument till Mongo API-samlingar för Azure Cosmos DB.
    
   Du kan även använda IP-adressen i situationer då DNS-namnmatchning inte är möjlig.

   ![Ange källinformation](media/tutorial-mongodb-to-cosmosdb/dms-specify-source.png)

2. Välj **Spara**.

## <a name="specify-target-details"></a>Ange målinformation
1. På sidan **Information om migreringsmål** anger du anslutningsinformationen för Azure Cosmos DB-målkontot, vilket är det företablerade MongoDB-konto för Azure Cosmos DB som du ska migrera dina MongoDB-data till.

    ![Ange målinformation](media/tutorial-mongodb-to-cosmosdb/dms-specify-target.png)

2. Välj **Spara**.

## <a name="map-to-target-databases"></a>Mappa till måldatabaser
1. På skärmen **Mappa till måldatabaser** mappar du käll- och måldatabasen för migreringen.

    Om måldatabasen innehåller samma databasnamn som källdatabasen väljer Azure Database Migration Service måldatabasen som standard.

    Om strängen **Skapa** visas bredvid namnet på databasen, betyder det att Azure Database Migration Service inte kunde hitta måldatabasen och att tjänsten kommer att skapa databasen åt dig.

    I det här läget i migreringen, kan du [etablera ett dataflöde](https://docs.microsoft.com/azure/cosmos-db/set-throughput). I Cosmos DB kan du etablera dataflöden på databasnivå eller separat för varje samling. Dataflödet mäts i [enheter för programbegäran](https://docs.microsoft.com/azure/cosmos-db/request-units) (RU:er). Mer information om [Azure Cosmos DB-prissättning](https://azure.microsoft.com/pricing/details/cosmos-db/).

    ![Mappa till måldatabaser](media/tutorial-mongodb-to-cosmosdb/dms-map-target-databases.png)

2. Välj **Spara**.
3. På skärmen **Mängdinställning** expanderar du samlingslistan och granskar sedan listan över de samlingar som ska migreras.

    Observera att Azure Database Migration Service automatiskt väljer alla samlingar som finns på källans MongoDB-instans och som inte finns på målets Azure Cosmos DB-konto. Om du vill migrera samlingar som redan innehåller data på nytt, måste du uttryckligen välja samlingarna på det här bladet.

    Du kan ange hur många RU:er som du vill att samlingarna ska använda. Azure Database Migration Service föreslår smarta standardvärden som baseras på samlingens storlek.

    Du kan också ange en shardnyckel för att kunna utnyttja [partitionering i Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview) för optimal skalbarhet. Läs igenom [metodtipsen för att välja en shard-/partitionsnyckel](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey).

    ![Välja tabeller för samlingar](media/tutorial-mongodb-to-cosmosdb/dms-collection-setting.png)

4. Välj **Spara**.

5. På sidan **Migreringssammanfattning**, i textrutan **Aktivitetsnamn** anger du ett namn på migreringsaktiviteten.

    ![Migreringssammanfattning](media/tutorial-mongodb-to-cosmosdb/dms-migration-summary.png)

## <a name="run-the-migration"></a>Köra migreringen
- Välj **Kör migrering**.

    Migreringsaktivitetsfönstret visas och **Status** för aktiviteten är **Inte startad**.

    ![Aktivitetsstatus](media/tutorial-mongodb-to-cosmosdb/dms-activity-status.png)

## <a name="monitor-the-migration"></a>Övervaka migreringen
- På migreringsaktivitetssidan väljer du **Uppdatera** för att uppdatera visningen tills **Status** för migreringen är **Slutförd**.

   > [!NOTE]
   > Du kan välja Aktivitet för att få information om databasens och samlingsnivåns migreringsmått.

    ![Aktivitetsstatus Slutförd](media/tutorial-mongodb-to-cosmosdb/dms-activity-completed.png)

## <a name="verify-data-in-cosmos-db"></a>Kontrollera data i Cosmos DB

- Du kan kontrollera ditt MongoDB API-konto för Cosmos DB om du vill se att alla samlingar har migrerats.

    ![Aktivitetsstatus Slutförd](media/tutorial-mongodb-to-cosmosdb/dms-cosmosdb-data-explorer.png)

## <a name="additional-resources"></a>Ytterligare resurser

 * [Information om Cosmos DB-tjänsten](https://azure.microsoft.com/services/cosmos-db/)

## <a name="next-steps"></a>Nästa steg
- Se fler scenarier i migreringsvägledningen i Microsofts [Guide för databasmigrering](https://datamigration.microsoft.com/).