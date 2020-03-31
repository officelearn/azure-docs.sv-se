---
title: Distribuera om SSIS-paket till en enda SQL-databas
titleSuffix: Azure Database Migration Service
description: Lär dig hur du migrerar eller distribuerar om SQL Server Integration Services-paket och projekt till en enda Azure SQL-databas med hjälp av Azure Database Migration Service och Data Migration Assistant.
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
ms.openlocfilehash: 90a39b8fe3604a05f1d35a875ae4e34491b47d72
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77648537"
---
# <a name="redeploy-ssis-packages-to-azure-sql-database-with-azure-database-migration-service"></a>Distribuera om SSIS-paket till Azure SQL Database med Azure Database Migration Service

Om du använder SQL Server Integration Services (SSIS) och vill migrera dina SSIS-projekt/paket från den källa SSISDB som finns på SQL Server till målet SSISDB som finns i Azure SQL Database, kan du distribuera om dem med hjälp av distributionsguiden för integrationstjänster. Du kan starta guiden inifrån SQL Server Management Studio (SSMS).

Om den version av SSIS du använder är tidigare än 2012, innan du distribuerar om dina SSIS-projekt/paket till projektdistributionsmodellen, måste du först konvertera dem med hjälp av guiden Projektkonvertering av Integrationstjänster, som också kan startas från SSMS. Mer information finns i artikeln [Konvertera projekt till projektdistributionsmodellen](https://docs.microsoft.com/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-2017#convert).

> [!NOTE]
> Azure Database Migration Service (DMS) stöder för närvarande inte migrering av en källa SSISDB till en Azure SQL Database-server, men du kan distribuera om dina SSIS-projekt/paket med hjälp av följande process.

I den här artikeln kan du se hur du:
> [!div class="checklist"]
>
> * Utvärdera ssis-projekt/-paket för källa.
> * Migrera SSIS-projekt/paket till Azure.

## <a name="prerequisites"></a>Krav

För att kunna utföra de här stegen behöver du:

* SSMS version 17.2 eller senare.
* En instans av måldatabasservern som är värd för SSISDB. Om du inte redan har en, skapa en Azure SQL Database server (utan en databas) med hjälp av Azure-portalen genom att navigera till SQL Server (logisk server bara) [form](https://ms.portal.azure.com/#create/Microsoft.SQLServer).
* SSIS måste etableras i Azure Data Factory (ADF) som innehåller Azure-SSIS Integration Runtime (IR) med målet SSISDB som finns i instansen av Azure SQL Database-servern (enligt beskrivningen i artikeln [Etablera Azure-SSIS-integrationskörningen i Azure Data Factory](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)).

## <a name="assess-source-ssis-projectspackages"></a>Utvärdera ssis-projekt/-paket för källa

Även om utvärdering av käll-SSISDB ännu inte är integrerad i DMA (Database Migration Assistant) eller Azure Database Migration Service (DMS), kommer dina SSIS-projekt/-paket att utvärderas/valideras när de distribueras om till målet SSISDB som finns på en Azure SQL-databasserver.

## <a name="migrate-ssis-projectspackages"></a>Migrera SSIS-projekt/paket

Om du vill migrera SSIS-projekt/paket till Azure SQL Database-servern utför du följande steg.

1. Öppna SSMS och välj sedan **Alternativ** för att visa dialogrutan **Anslut till server.**

2. På fliken **Inloggning** anger du den information som krävs för att ansluta till Azure SQL Database-servern som ska vara värd för målet SSISDB.

    ![Fliken SSIS-inloggning](media/how-to-migrate-ssis-packages/dms-ssis-login-tab.png)

3. Markera eller ange **SSISDB**i textrutan **Anslut till databas** på fliken **Anslutningsegenskaper** och välj sedan **Anslut**.

    ![Fliken Egenskaper för SSIS-anslutning](media/how-to-migrate-ssis-packages/dms-ssis-conncetion-properties-tab.png)

4. Expandera noden **Integration Services Catalogs i** SSMS-objektutforskaren, expandera **SSISDB**och om det inte finns några befintliga mappar högerklickar du på **SSISDB** och skapar en ny mapp.

5. Expandera valfri mapp under **SSISDB,** högerklicka på **Projekt**och välj sedan **Distribuera projekt**.

    ![SSIS SSISDB-noden expanderad](media/how-to-migrate-ssis-packages/dms-ssis-ssisdb-node-expanded.png)

6. Granska informationen på sidan Introduktion på sidan **Introduktion** i distributionsguiden för integrationstjänster och välj sedan **Nästa**.

    ![Introduktionssida för distributionsguiden](media/how-to-migrate-ssis-packages/dms-deployment-wizard-introduction-page.png)

7. På sidan **Välj källa** anger du det befintliga SSIS-projekt som du vill distribuera.

    Om SSMS också är anslutet till SQL Server som är värd för källan SSISDB väljer du **Integration Services-katalogen**och anger sedan servernamn och projektsökväg i katalogen för att distribuera projektet direkt.

    Välj automatiskt **Projektdistributionsfil**och ange sedan sökvägen till en befintlig projektdistributionsfil (.ispac) för att distribuera projektet.

    ![Sidan Välj källa för distributionsguiden](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-source-page.png)
 
8. Välj **Nästa**.
9. På sidan **Välj mål** anger du målet för projektet.

    a. Ange det fullständigt kvalificerade Azure SQL Database-servernamnet (<server_name>.database.windows.net) i textrutan Servernamn.

    b. Ange autentiseringsinformation och välj sedan **Anslut**.

    ![Sidan Välj mål i distributionsguiden](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-destination-page.png)

    c. Välj **Bläddra** om du vill ange målmappen i SSISDB och välj sedan **Nästa**.

    > [!NOTE]
    > **Knappen Nästa** aktiveras först när du har valt **Anslut**.

10. På sidan **Verifiera** visar du eventuella fel/varningar och ändrar sedan paketen därefter om det behövs.

    ![Validera sida för distributionsguide](media/how-to-migrate-ssis-packages/dms-deployment-wizard-validate-page.png)

11. Välj **Nästa**.

12. Granska distributionsinställningarna på sidan **Granska.**

    > [!NOTE]
    > Du kan ändra inställningarna genom att välja **Föregående** eller genom att välja någon av steglänkarna i den vänstra rutan.

13. Välj **Distribuera för** att starta distributionsprocessen.

14. När distributionsprocessen har slutförts kan du visa resultatsidan, som visar lyckade eller misslyckade distributionsåtgärder.
    a. Om någon åtgärd misslyckades väljer du **Det gick** inte att visa en förklaring av felet i kolumnen **Resultat.**
    b. Du kan också välja **Spara rapport om** du vill spara resultaten i en XML-fil.

15. Välj **Stäng** om du vill avsluta distributionsguiden för integrationstjänster.

Om distributionen av projektet lyckas utan fel kan du välja alla paket som det innehåller för att köras på din Azure-SSIS IR.

## <a name="next-steps"></a>Nästa steg

* Läs migreringsvägledningen i [Migreringsguiden för](https://datamigration.microsoft.com/)Microsoft Database .
