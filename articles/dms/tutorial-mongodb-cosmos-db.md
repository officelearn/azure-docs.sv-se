---
title: 'Självstudiekurs: Migrera MongoDB offline till Azure Cosmos DB API för MongoDB'
titleSuffix: Azure Database Migration Service
description: Lär dig att migrera från MongoDB lokalt till Azure Cosmos DB API för MongoDB offline med hjälp av Azure Database Migration Service.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: 08fa94dbe71299a6653df0b40aa5083375526172
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255580"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-dbs-api-for-mongodb-offline-using-dms"></a>Självstudiekurs: Migrera MongoDB till Azure Cosmos DB:s API för MongoDB offline med DMS

Du kan använda Azure Database Migration Service för att utföra en offlinemigrering (engångs) av databaser från en lokal eller molninstans av MongoDB till Azure Cosmos DB:s API för MongoDB.

I den här självstudiekursen får du lära du dig att:
> [!div class="checklist"]
>
> * Skapa en instans av Azure Database Migration Service.
> * Skapa ett migreringsprojekt med hjälp av Azure Database Migration Service.
> * Köra migreringen.
> * Övervaka migreringen.

I den här självstudien migrerar du en datauppsättning i MongoDB som finns i en virtuell Azure-dator till Azure Cosmos DB:s API för MongoDB med hjälp av Azure Database Migration Service. Om du inte har konfigurerat någon MongoDB-källa, kan du läsa artikeln [Installera och konfigurera MongoDB på en virtuell Windows-dator i Azure](https://docs.microsoft.com/azure/virtual-machines/windows/install-mongodb).

## <a name="prerequisites"></a>Krav

För att slutföra den här kursen behöver du:

* Slutför stegen [före migreringen,](../cosmos-db/mongodb-pre-migration.md) till exempel uppskatta dataflödet, välja en partitionsnyckel och indexeringsprincipen.
* [Skapa ett Azure Cosmos DB-API för MongoDB-konto](https://ms.portal.azure.com/#create/Microsoft.DocumentDB).
* Skapa en Microsoft Azure Virtual Network for Azure Database Migration Service med hjälp av Azure Resource Manager-distributionsmodell, som tillhandahåller anslutning från plats till plats till dina lokala källservrar med hjälp av [Antingen ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) eller [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Mer information om hur du skapar ett virtuellt nätverk finns i [dokumentationen](https://docs.microsoft.com/azure/virtual-network/)till det virtuella nätverket och särskilt snabbstartsartiklarna med steg-för-steg-information.

    > [!NOTE]
    > Om du använder ExpressRoute med nätverks peering i Microsoft under installationen av virtuella nätverk lägger du till följande [tjänstslutpunkter](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) i undernätet där tjänsten ska etableras:
    >
    > * Slutpunkt för måldatabas (till exempel SQL-slutpunkt, Cosmos DB-slutpunkt och så vidare)
    > * Slutpunkt för lagring
    > * Ändpunkt för servicebuss
    >
    > Den här konfigurationen är nödvändig eftersom Azure Database Migration Service saknar internetanslutning.

* Kontrollera att NSG-reglerna (Virtual Network Network Security Group) inte blockerar följande kommunikationsportar: 53, 443, 445, 9354 och 10000-20000. Mer information om filtrering av NSG-trafik i det virtuella nätverket finns i artikeln [Filtrera nätverkstrafik med nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* Öppna Windows-brandväggen så att Azure Database Migration Service kan komma åt källmongoDB-servern, som som standard är TCP-port 27017.
* När du använder en brandväggsinstallation framför källdatabasen eller källdatabaserna kan du behöva lägga till brandväggsregler så att Azure Database Migration Service kan komma åt källdatabaserna för migrering.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrera resursprovidern Microsoft.DataMigration

1. Logga in på Azure Portal och välj **Alla tjänster** och sedan **Prenumerationer**.

   ![Visa portalprenumerationer](media/tutorial-mongodb-to-cosmosdb/portal-select-subscription1.png)

2. Välj den prenumeration där du vill skapa en instans av Azure Database Migration Service och välj sedan **Resursprovidrar**.

    ![Visa resursprovidrar](media/tutorial-mongodb-to-cosmosdb/portal-select-resource-provider.png)

3. Sök efter migrering och välj sedan **Registrera**till höger om **Microsoft.DataMigration**.

    ![Registrera resursprovider](media/tutorial-mongodb-to-cosmosdb/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Skapa en instans

1. I Azure Portal väljer du + **Skapa en resurs**, söker efter Azure Database Migration Service och väljer sedan **Azure Database Migration Service** i listrutan.

    ![Azure Marketplace](media/tutorial-mongodb-to-cosmosdb/portal-marketplace.png)

2. På sidan **Azure Database Migration Service** väljer du **Skapa**.

    ![Skapa Azure Database Migration Service-instans](media/tutorial-mongodb-to-cosmosdb/dms-create1.png)
  
3. På sidan **Create Migration Service** anger du ett namn för tjänsten, prenumerationen och en ny eller befintlig resursgrupp.

4. Välj den plats där du vill skapa instansen av Azure Database Migration Service. 

5. Välj ett befintligt virtuellt nätverk eller skapa ett nytt.

    Det virtuella nätverket ger Azure Database Migration Service åtkomst till källan MongoDB-instans och målet Azure Cosmos DB-konto.

    Mer information om hur du skapar ett virtuellt nätverk i Azure-portalen finns i artikeln [Skapa ett virtuellt nätverk med Azure-portalen](https://aka.ms/DMSVnet).

6. Välj en prisnivå.

    Mer information om kostnader och prisnivåer finns på [sidan med priser](https://aka.ms/dms-pricing).

    ![Konfigurera Azure Database Migration Service-instansinställningar](media/tutorial-mongodb-to-cosmosdb/dms-settings2.png)

7. Välj **Skapa** för att skapa tjänsten.

## <a name="create-a-migration-project"></a>Skapa ett migreringsprojekt

När tjänsten har skapats letar du reda på den i Azure Portal, öppnar den och skapar sedan ett nytt migreringsprojekt.

1. I Azure Portal väljer du **Alla tjänster**, söker efter Azure Database Migration Service och väljer sedan **Azure Database Migration Services**.

      ![Leta reda på alla instanser av Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb/dms-search.png)

2. På skärmen **Azure Database Migration Services** söker du efter namnet på Azure Database Migration Service-instansen som du skapade och väljer sedan instansen.

3. Välj + **Nytt migreringsprojekt**.

4. På sidan **Nytt migreringsprojekt** anger du namnet på projektet. I textrutan **Typ av källserver** väljer du **MongoDB**, i textrutan **Målservertyp** väljer du **CosmosDB (MongoDB API)** och i **Välj typ av aktivitet** väljer du sedan **Offline-datamigrering**. 

    ![Skapa ett Database Migration Service-projekt](media/tutorial-mongodb-to-cosmosdb/dms-create-project.png)

5. Välj **Skapa och kör aktivitet** för att skapa projektet och köra migreringsaktiviteten.

## <a name="specify-source-details"></a>Ange källinformation

1. På sidan **Källinformation** anger du anslutningsinformationen för MongoDB-källservern.

   > [!IMPORTANT]
   > Azure Database Migration Service stöder inte Azure Cosmos DB som källa.

    Det finns tre lägen för att ansluta till en datakälla:
   * **Standardläge** som accepterar ett fullständigt kvalificerat domännamn eller en IP-adress, portnummer och autentiseringsuppgifter för anslutningen.
   * **Anslutningssträngsläge** som tar emot en MongoDB-anslutningssträng som beskrivs i artikeln [URI-anslutningssträngens format](https://docs.mongodb.com/manual/reference/connection-string/).
   * **Data från Azure-lagring** som tar emot en blobcontainers SAS-URL. Välj **Bloben innehåller BSON-dumpar** om blobcontainern innehåller BSON-dumpar som producerats av MongoDB:s [bsondump-verktyg](https://docs.mongodb.com/manual/reference/program/bsondump/) och avmarkera den om containern innehåller JSON-filer.

     Om du väljer det här alternativet ska du se till att anslutningssträngen för lagringskontot har följande format:

     ```
     https://blobnameurl/container?SASKEY
     ```

     Den här SAS-anslutningssträngen för blob-behållaren finns i Utforskaren för Azure Storage. Om du skapar SAS för den berörda behållaren får du webbadressen i ovanstående format.
     
     Dessutom, baserat på typdumpinformationen i Azure Storage, ha följande detalj i åtanke.

     * För BSON-dumpar måste data i blob-containern vara i bsondump-format, så att datafilerna placeras i mappar som namnges efter de innehållande databaserna i formatet collection.bson. Metadatafiler (om sådana finns) bör namnges med formatet *samling*.metadata.json.

     * För JSON-dumpar måste filerna i blob-containern placeras i mappar som namnges efter de innehållande databaserna. I varje databasmapp måste datafiler placeras i en undermapp som heter ”data” och namnges med formatet *samling*.json. Metadatafiler (om sådana finns) måste placeras i en undermapp som heter ”metadata” och namnges med samma format, *samling*.json. Metadatafilerna måste vara i samma format som det som skapas av verktyget MongoDB bsondump.

    > [!IMPORTANT]
    > Det rekommenderas att använda ett självsignerat certifikat på mongoservern. Om en sådan används, anslut till servern med **anslutningssträngläge** och se till att anslutningssträngen har ""
    >
    >```
    >&sslVerifyCertificate=false
    >```

   Du kan även använda IP-adressen i situationer då DNS-namnmatchning inte är möjlig.

   ![Ange källinformation](media/tutorial-mongodb-to-cosmosdb/dms-specify-source.png)

2. Välj **Spara**.

## <a name="specify-target-details"></a>Ange målinformation

1. På sidan **Information om migreringsmål** anger du anslutningsinformationen för Azure Cosmos DB-målkontot, vilket är det företablerade Azure Cosmos DB-API för MongoDB-kontot som du ska migrera dina MongoDB-data till.

    ![Ange målinformation](media/tutorial-mongodb-to-cosmosdb/dms-specify-target.png)

2. Välj **Spara**.

## <a name="map-to-target-databases"></a>Mappa till måldatabaser

1. På skärmen **Mappa till måldatabaser** mappar du käll- och måldatabasen för migreringen.

    Om måldatabasen innehåller samma databasnamn som källdatabasen väljer Azure Database Migration Service måldatabasen som standard.

    Om strängen **Skapa** visas bredvid databasnamnet anger den att Azure Database Migration Service inte hittade måldatabasen och tjänsten skapar databasen åt dig.

    I det här läget i migreringen, kan du [etablera ett dataflöde](https://docs.microsoft.com/azure/cosmos-db/set-throughput). I Cosmos DB kan du etablera dataflöden på databasnivå eller separat för varje samling. Dataflödet mäts i [enheter för programbegäran](https://docs.microsoft.com/azure/cosmos-db/request-units) (RU:er). Mer information om [Azure Cosmos DB-prissättning](https://azure.microsoft.com/pricing/details/cosmos-db/).

    ![Mappa till måldatabaser](media/tutorial-mongodb-to-cosmosdb/dms-map-target-databases.png)

2. Välj **Spara**.
3. På skärmen **Mängdinställning** expanderar du samlingslistan och granskar sedan listan över de samlingar som ska migreras.

    Azure Database Migration Service väljer automatiskt alla samlingar som finns på källan MongoDB-instans som inte finns på målet Azure Cosmos DB-konto. Om du vill migrera samlingar som redan innehåller data på nytt, måste du uttryckligen välja samlingarna på det här bladet.

    Du kan ange hur många RU:er som du vill att samlingarna ska använda. Azure Database Migration Service föreslår smarta standardvärden baserat på samlingsstorleken.

    > [!NOTE]
    > Utför databasmigrering och samling parallellt med flera instanser av Azure Database Migration Service, om det behövs, för att påskynda körningen.

    Du kan också ange en shardnyckel för att kunna utnyttja [partitionering i Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview) för optimal skalbarhet. Läs igenom [metodtipsen för att välja en shard-/partitionsnyckel](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey).

    ![Välja tabeller för samlingar](media/tutorial-mongodb-to-cosmosdb/dms-collection-setting.png)

4. Välj **Spara**.

5. På sidan **Migreringssammanfattning**, i textrutan **Aktivitetsnamn** anger du ett namn på migreringsaktiviteten.

    ![Migreringssammanfattning](media/tutorial-mongodb-to-cosmosdb/dms-migration-summary.png)

## <a name="run-the-migration"></a>Köra migreringen

* Välj **Kör migrering**.

    Migreringsaktivitetsfönstret visas och **Status** för aktiviteten är **Inte startad**.

    ![Aktivitetsstatus](media/tutorial-mongodb-to-cosmosdb/dms-activity-status.png)

## <a name="monitor-the-migration"></a>Övervaka migreringen

* På migreringsaktivitetssidan väljer du **Uppdatera** för att uppdatera visningen tills **Status** för migreringen är **Slutförd**.

   > [!NOTE]
   > Du kan välja Aktivitet för att få information om databasens och samlingsnivåns migreringsmått.

    ![Aktivitetsstatus Slutförd](media/tutorial-mongodb-to-cosmosdb/dms-activity-completed.png)

## <a name="verify-data-in-cosmos-db"></a>Kontrollera data i Cosmos DB

* När migreringen är klar kan du kontrollera ditt Azure Cosmos DB-konto för att se om alla samlingar migrerades korrekt.

    ![Aktivitetsstatus Slutförd](media/tutorial-mongodb-to-cosmosdb/dms-cosmosdb-data-explorer.png)

## <a name="post-migration-optimization"></a>Optimering efter migrering

När du har migrerat data som lagras i MongoDB-databasen till Azure Cosmos DB:s API för MongoDB kan du ansluta till Azure Cosmos DB och hantera data. Du kan också utföra andra optimeringssteg efter migrering, till exempel optimera indexeringsprincipen, uppdatera standardkonsekvensnivån eller konfigurera global distribution för ditt Azure Cosmos DB-konto. Mer information finns i [optimeringsartikeln efter migrering.](../cosmos-db/mongodb-post-migration.md)

## <a name="additional-resources"></a>Ytterligare resurser

* [Information om Cosmos DB-tjänsten](https://azure.microsoft.com/services/cosmos-db/)

## <a name="next-steps"></a>Nästa steg

* Se fler scenarier i migreringsvägledningen i Microsofts [Guide för databasmigrering](https://datamigration.microsoft.com/).
