---
title: Migrera SQL Server Integration Services-paket till en Azure SQL Database managed instance | Microsoft Docs
description: Lär dig mer om att migrera SQL Server Integration Services-paket till en Azure SQL Database managed instance.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 06/08/2019
ms.openlocfilehash: 82a047616c199e37bfa22f53e02f3f7b224b47c1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67083189"
---
# <a name="migrate-sql-server-integration-services-packages-to-an-azure-sql-database-managed-instance"></a>Migrera SQL Server Integration Services-paket till en Azure SQL Database managed instance
Om du använder SQL Server Integration Services (SSIS) och vill migrera dina SSIS-projekt /-paket från källan SSISDB installation av SQL Server till målet SSISDB som värd för en hanterad Azure SQL Database-instans, kan du använda Azure Database Migration Service.

Om versionen av SSIS som du använder är äldre än 2012 eller om du använder icke-SSISDB paketet store typer innan migrera dina SSIS-projekt /-paket, måste man konverterar dem med hjälp av Integration Services-projekt Konverteringsguiden, som också kan startas från SSMS. Mer information finns i artikeln [konvertering projekt till projektet distributionsmodellen](https://docs.microsoft.com/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-2017#convert).

> [!NOTE]
> Azure Database Migration Service (DMS) stöder för närvarande inte Azure SQL Database som mål för en target-migrering. Om du vill distribuera om SSIS-projekt /-paket till Azure SQL Database finns i artikeln [distribuera om SQL Server Integration Services-paket till Azure SQL Database](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

I den här artikeln kan du se hur du:
> [!div class="checklist"]
>
> * Utvärdera källa SSIS-projekt /-paket.
> * Migrera SSIS-projekt /-paket till Azure.

## <a name="prerequisites"></a>Nödvändiga komponenter

Du behöver följande för att slutföra dessa steg:

* Skapa ett Azure-nätverk (VNet) för Azure Database Migration Service med hjälp av Azure Resource Manager-distributionsmodellen, som tillhandahåller plats-till-plats-anslutning till dina lokala källservrar genom att använda antingen [ExpressRoute ](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) eller [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Mer information finns i artikeln [nätverkstopologier för Azure SQL Database managed instance-migrering med hjälp av Azure Database Migration Service]( https://aka.ms/dmsnetworkformi). Mer information om hur du skapar ett virtuellt nätverk finns i den [dokumentation om virtuella nätverk](https://docs.microsoft.com/azure/virtual-network/), och särskilt artiklarna i snabbstarten med stegvis information.
* Så här säkerställer att dina regler för Nätverkssäkerhetsgrupp kopplad till virtuella nätverk inte blockerar följande portar för inkommande kommunikation till Azure Database Migration Service: 443, 53, 9354, 445, 12000. Mer information om Azure VNet NSG-trafikfiltrering finns i artikeln [filtrera nätverkstrafik med nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm).
* Så här konfigurerar du din [Windows brandvägg för databasmotoråtkomst för källan](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access?view=sql-server-2017).
* Öppna din Windows-brandväggen så att Azure Database Migration Service att få åtkomst till källan SQL Server, vilket som standard är TCP-port 1433.
* Om du kör flera namngivna SQL Server-instanser med dynamiska portar kan du vilja aktivera SQL Browser Service och tillåta åtkomst till UDP-port 1434 via dina brandväggar så att Azure Database Migration Service kan ansluta till en namngiven instans på källservern.
* Om du använder en brandväggsinstallation framför dina källdatabaser kanske du måste lägga till brandväggsregler för att tillåta Azure Database Migration Service att komma åt källdatabaserna för migrering samt filer via SMB-port 445.
* En Azure SQL Database-hanterad instans som värd för SSISDB. Följ detaljerat i artikeln om du vill skapa en [skapa en Azure SQL Database Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started).
* SQL Server- och mål-hanterad instans är medlemmar i serverrollen sysadmin för att säkerställa att inloggningar som används för att ansluta källan.
* Kontrollera att SSIS etableras i Azure Data Factory (ADF) som innehåller Azure-SSIS Integration Runtime (IR) med målet SSISDB som värd för en Azure SQL Database-hanterad instans (enligt beskrivningen i artikeln [skapa Azure-SSIS Integration runtime i Azure Data Factory](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)).

## <a name="assess-source-ssis-projectspackages"></a>Utvärdera källa SSIS-projekt/paket

Även om utvärdering av källa SSISDB inte är ännu integrerad i den Database Migration Assistant (DMA), verifieras dina SSIS-projekt /-paket för att utvärdera/som de är omdistribueras till målet SSISDB finns på en hanterad Azure SQL Database-instans.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrera resursprovidern Microsoft.DataMigration

1. Logga in på Azure-portalen och välj **Alla tjänster** och sedan **Prenumerationer**.

    ![Visa portalprenumerationer](media/how-to-migrate-ssis-packages-mi/portal-select-subscriptions.png)

2. Välj den prenumeration som du vill skapa en instans av Azure Database Migration Service och välj sedan **resursprovidrar**.

    ![Visa resursprovidrar](media/how-to-migrate-ssis-packages-mi/portal-select-resource-provider.png)

3. Sök efter migreringen och välj sedan **Registrera** till höger om **Microsoft.DataMigration**.

    ![Registrera resursprovider](media/how-to-migrate-ssis-packages-mi/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Skapa en Azure Database Migration Service-instans

1. I Azure Portal väljer du + **Skapa en resurs**, söker efter **Azure Database Migration Service** och väljer sedan **Azure Database Migration Service** i listrutan.

     ![Azure Marketplace](media/how-to-migrate-ssis-packages-mi/portal-marketplace.png)

2. På sidan **Azure Database Migration Service** väljer du **Skapa**.

    ![Skapa Azure Database Migration Service-instans](media/how-to-migrate-ssis-packages-mi/dms-create1.png)

3. På sidan **Create Migration Service** anger du ett namn för tjänsten, prenumerationen och en ny eller befintlig resursgrupp.

4. Välj den plats där du vill skapa instansen av DMS.

5. Välj ett befintligt virtuellt nätverk eller skapa en.

    Det virtuella nätverket ger Azure Database Migration Service åtkomst till SQL Server-källans och målets Azure SQL Database hanterad instans.

    Mer information om hur du skapar ett virtuellt nätverk i Azure-portalen finns i artikeln [skapa ett virtuellt nätverk med Azure portal](https://aka.ms/DMSVnet).

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

4. På den **nytt migreringsprojekt** skärmen, ange ett namn för projektet, i den **typ av källserver** textrutan **SQL Server**i den **målserver typ** textrutan **Azure SQL Database Managed Instance**, och sedan för **Välj typ av aktivitet**väljer **SSIS paketera migrering**.

   ![Skapa DMS-projekt](media/how-to-migrate-ssis-packages-mi/dms-create-project2.png)

5. Välj **Skapa** för att skapa projektet.

## <a name="specify-source-details"></a>Ange källinformation

1. På sidan **Migreringskällinformation** anger du anslutningsinformationen för SQL Server-källan.

2. Om du inte har installerat ett betrott certifikat på servern markerar du kryssrutan **Lita på servercertifikatet**.

    När ett betrott certifikat inte har installerats genererar SQL Server ett självsignerat certifikat när instansen har startats. Detta certifikat används till att kryptera autentiseringsuppgifterna för klientanslutningar.

    > [!CAUTION]
    > SSL-anslutningar som har krypterats med ett självsignerat certifikat ger inte stark säkerhet. De är sårbara för man-in-the-middle-attacker. Du bör inte lita på SSL som använder självsignerade certifikat i en produktionsmiljö eller på servrar som är anslutna till internet.

   ![Källinformation](media/how-to-migrate-ssis-packages-mi/dms-source-details1.png)

3. Välj **Spara**.

## <a name="specify-target-details"></a>Ange målinformation

1. På den **migreringsmål** skärmen, Ange anslutningsinformation för målet.

     ![Målinformation](media/how-to-migrate-ssis-packages-mi/dms-target-details2.png)

2. Välj **Spara**.

## <a name="review-the-migration-summary"></a>Granska migreringssammanfattningen

1. På sidan **Migreringssammanfattning**, i textrutan **Aktivitetsnamn** anger du ett namn på migreringsaktiviteten.

2. För den **SSIS-projektet och environment(s) överskrivningsalternativ**, ange om du vill skriva över eller ignorera befintliga SSIS-projekt och miljöer.

    ![Sammanfattning av migreringsprojekt](media/how-to-migrate-ssis-packages-mi/dms-project-summary2.png)

3. Granska och verifiera informationen som är kopplad till migreringsprojektet.

## <a name="run-the-migration"></a>Köra migreringen

* Välj **Kör migrering**.

## <a name="next-steps"></a>Nästa steg

* Granska migreringsvägledningen i Microsofts [Guide för Databasmigrering](https://datamigration.microsoft.com/).
