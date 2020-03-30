---
title: Migrera SSIS-paket till SQL-hanterad instans
titleSuffix: Azure Database Migration Service
description: Lär dig hur du migrerar SSIS-paket (SQL Server Integration Services) och projekt till en hanterad Azure SQL-databas-hanterad instans med hjälp av Migreringstjänsten för Azure Database eller DataMigreringsassistenten.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: 97a466ab033a42016c0d82465d1f98e2dcae8080
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297185"
---
# <a name="migrate-sql-server-integration-services-packages-to-an-azure-sql-database-managed-instance"></a>Migrera SQL Server Integration Services till en hanterad Azure SQL Database-instans
Om du använder SQL Server Integration Services (SSIS) och vill migrera dina SSIS-projekt/paket från källan SSISDB som finns på SQL Server till målet SSISDB som finns i en hanterad Azure SQL-databas-hanterad instans kan du använda Azure Database Migration Service.

Om den version av SSIS du använder är tidigare än 2012 eller om du använder icke-SSISDB-paketarkivtyper, innan du migrerar dina SSIS-projekt/paket, måste du konvertera dem med hjälp av guiden Projektkonvertering av integrationstjänster, som också kan startas från SSMS. Mer information finns i artikeln [Konvertera projekt till projektdistributionsmodellen](https://docs.microsoft.com/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-2017#convert).

> [!NOTE]
> Azure Database Migration Service (DMS) stöder för närvarande inte Azure SQL Database som målmigreringsmål. Information om hur du distribuerar om SSIS-projekt/paket till Azure SQL Database finns i artikeln [Omdedistribution av SQL Server Integration Services-paket till Azure SQL Database](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

I den här artikeln kan du se hur du:
> [!div class="checklist"]
>
> * Utvärdera ssis-projekt/-paket för källa.
> * Migrera SSIS-projekt/paket till Azure.

## <a name="prerequisites"></a>Krav

För att kunna utföra de här stegen behöver du:

* Så här skapar du ett Virtuellt Nätverk för Microsoft Azure För Azure Database ID med hjälp av distributionsmodellen för Azure Resource Manager, som tillhandahåller anslutning från plats till plats till dina lokala källservrar med hjälp av [Antingen ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) eller [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Mer information finns i artikeln [Nätverkstopologier för Azure SQL Database-hanterade instansmigreringar med Hjälp av Azure Database Migration Service]( https://aka.ms/dmsnetworkformi). Mer information om hur du skapar ett virtuellt nätverk finns i [dokumentationen](https://docs.microsoft.com/azure/virtual-network/)till det virtuella nätverket och särskilt snabbstartsartiklarna med steg-för-steg-information.
* Så här säkerställer du att dina regler för nätverkssäkerhetsgrupp för virtuella nätverk inte blockerar följande inkommande kommunikationsportar till Azure Database Migration Service: 443, 53, 9354, 445, 12000. Mer information om filtrering av NSG-trafik i det virtuella nätverket finns i artikeln [Filtrera nätverkstrafik med nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm).
* Så här konfigurerar du [Windows-brandväggen för åtkomst till källdatabasmotorn](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access?view=sql-server-2017).
* Så här öppnar du Windows-brandväggen så att Azure Database Migration Service kan komma åt källan SQL Server, som som standard är TCP-port 1433.
* Om du kör flera namngivna SQL Server-instanser med dynamiska portar kan du vilja aktivera SQL Browser Service och tillåta åtkomst till UDP-port 1434 via dina brandväggar så att Azure Database Migration Service kan ansluta till en namngiven instans på källservern.
* Om du använder en brandväggsinstallation framför dina källdatabaser kanske du måste lägga till brandväggsregler för att tillåta Azure Database Migration Service att komma åt källdatabaserna för migrering samt filer via SMB-port 445.
* En Azure SQL Database-hanterad instans som värd för SSISDB. Om du behöver skapa en följer du detaljerna i artikeln [Skapa en Hanterad Azure SQL-databasinstans](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started).
* För att säkerställa att de inloggningar som används för att ansluta källan SQL Server och målhanterad instans är medlemmar i sysadmin-serverrollen.
* Så här verifierar du att SSIS har etablerats i Azure Data Factory (ADF) som innehåller Azure-SSIS Integration Runtime (IR) med målet SSISDB som finns i en hanterad Azure SQL-databas-instans (enligt beskrivningen i artikeln [Skapa Azure-SSIS-integreringskörningen i Azure Data Factory](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)).

## <a name="assess-source-ssis-projectspackages"></a>Utvärdera ssis-projekt/-paket för källa

Även om utvärdering av käll-SSISDB ännu inte är integrerad i DMA (Database Migration Assistant), utvärderas/valideras dina SSIS-projekt/-paket när de distribueras om till målet SSISDB som finns på en hanterad Azure SQL-databas-hanterad instans.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrera resursprovidern Microsoft.DataMigration

1. Logga in på Azure Portal och välj **Alla tjänster** och sedan **Prenumerationer**.

    ![Visa portalprenumerationer](media/how-to-migrate-ssis-packages-mi/portal-select-subscriptions.png)

2. Välj den prenumeration där du vill skapa instansen av Azure Database Migration Service och välj sedan **Resursleverantörer**.

    ![Visa resursprovidrar](media/how-to-migrate-ssis-packages-mi/portal-select-resource-provider.png)

3. Sök efter migrering och välj sedan **Registrera**till höger om **Microsoft.DataMigration**.

    ![Registrera resursprovider](media/how-to-migrate-ssis-packages-mi/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Skapa en Azure Database Migration Service-instans

1. I Azure-portalen väljer du + **Skapa en resurs**, söker efter Azure Database Migration **Service**och väljer sedan Azure Database **Migration Service** i listrutan.

     ![Azure Marketplace](media/how-to-migrate-ssis-packages-mi/portal-marketplace.png)

2. På sidan **Azure Database Migration Service** väljer du **Skapa**.

    ![Skapa Azure Database Migration Service-instans](media/how-to-migrate-ssis-packages-mi/dms-create1.png)

3. På sidan **Create Migration Service** anger du ett namn för tjänsten, prenumerationen och en ny eller befintlig resursgrupp.

4. Välj den plats där du vill skapa instansen av DMS.

5. Välj ett befintligt virtuellt nätverk eller skapa ett.

    Det virtuella nätverket ger Azure Database Migration Service åtkomst till källan SQL Server och rikta Azure SQL Database hanterad instans.

    Mer information om hur du skapar ett virtuellt nätverk i Azure-portalen finns i artikeln [Skapa ett virtuellt nätverk med Azure-portalen](https://aka.ms/DMSVnet).

    Mer information finns i artikeln om [nätverkstopologier för migreringar av hanterade Azure SQL DB-instanser med Azure Database Migration Service](https://aka.ms/dmsnetworkformi).

6. Välj en prisnivå.

    Mer information om kostnader och prisnivåer finns på [sidan med priser](https://aka.ms/dms-pricing).

    ![Skapa DMS-tjänst](media/how-to-migrate-ssis-packages-mi/dms-create-service2.png)

7. Välj **Skapa** för att skapa tjänsten.

## <a name="create-a-migration-project"></a>Skapa ett migreringsprojekt

När en instans av tjänsten har skapats letar du reda på den i Azure Portal, öppnar den och skapar sedan ett nytt migreringsprojekt.

1. I Azure Portal väljer du **Alla tjänster**, söker efter Azure Database Migration Service och väljer sedan **Azure Database Migration Services**.

    ![Leta reda på alla instanser Azure Database Migration Service](media/how-to-migrate-ssis-packages-mi/dms-search.png)

2. På **Azure Database Migration Service-sidan** söker du efter namnet på instansen som du har skapat och väljer sedan instansen.

3. Välj + **Nytt migreringsprojekt**.

4. På skärmen **Nytt migreringsprojekt** anger du ett namn för projektet, i textrutan **Källservertyp** väljer du **SQL Server**, i textrutan **Målservertyp,** väljer **Azure SQL Database Managed Instance**och väljer sedan **SSIS-paketmigrering för Välj SSIS-paketmigrering**för **val.**

   ![Skapa DMS-projekt](media/how-to-migrate-ssis-packages-mi/dms-create-project2.png)

5. Välj **Skapa** för att skapa projektet.

## <a name="specify-source-details"></a>Ange källinformation

1. På sidan **Migreringskällinformation** anger du anslutningsinformationen för SQL Server-källan.

2. Om du inte har installerat ett betrott certifikat på servern markerar du kryssrutan **Lita på servercertifikatet**.

    När ett betrott certifikat inte har installerats genererar SQL Server ett självsignerat certifikat när instansen har startats. Detta certifikat används till att kryptera autentiseringsuppgifterna för klientanslutningar.

    > [!CAUTION]
    > TLS-anslutningar som krypteras med ett självsignerat certifikat ger inte stark säkerhet. De är sårbara för man-in-the-middle-attacker. Du bör inte förlita dig på TLS med självsignerade certifikat i en produktionsmiljö eller på servrar som är anslutna till Internet.

   ![Källinformation](media/how-to-migrate-ssis-packages-mi/dms-source-details1.png)

3. Välj **Spara**.

## <a name="specify-target-details"></a>Ange målinformation

1. Ange anslutningsinformation för målet på skärmen **Migrationsmålsinformation.**

     ![Målinformation](media/how-to-migrate-ssis-packages-mi/dms-target-details2.png)

2. Välj **Spara**.

## <a name="review-the-migration-summary"></a>Granska migreringssammanfattningen

1. På sidan **Migreringssammanfattning**, i textrutan **Aktivitetsnamn** anger du ett namn på migreringsaktiviteten.

2. För **alternativet SSIS-projekt och miljöer anger**du om du vill skriva över eller ignorera befintliga SSIS-projekt och miljöer.

    ![Sammanfattning av migreringsprojekt](media/how-to-migrate-ssis-packages-mi/dms-project-summary2.png)

3. Granska och verifiera informationen som är kopplad till migreringsprojektet.

## <a name="run-the-migration"></a>Köra migreringen

* Välj **Kör migrering**.

## <a name="next-steps"></a>Nästa steg

* Läs migreringsvägledningen i [Migreringsguiden för](https://datamigration.microsoft.com/)Microsoft Database .
