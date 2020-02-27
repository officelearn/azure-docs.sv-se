---
title: Distribuera om SSIS-paket till en SQL-databas
titleSuffix: Azure Database Migration Service
description: Lär dig hur du migrerar eller distribuerar om SQL Server Integration Services-paket och-projekt till Azure SQL Database enskild databas med Azure Database Migration Service och Data Migration Assistant.
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
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77648537"
---
# <a name="redeploy-ssis-packages-to-azure-sql-database-with-azure-database-migration-service"></a>Distribuera om SSIS-paket till Azure SQL Database med Azure Database Migration Service

Om du använder SQL Server Integration Services (SSIS) och vill migrera dina SSIS-projekt/-paket från käll-SSISDB som är värd för SQL Server till målet SSISDB som är värdbaserade av Azure SQL Database kan du distribuera om dem med hjälp av distributions guiden för integrerings tjänster. Du kan starta guiden inifrån SQL Server Management Studio (SSMS).

Om den version av SSIS som du använder är tidigare än 2012, innan du distribuerar om dina SSIS-projekt/-paket till projekt distributions modellen, måste du först konvertera dem med hjälp av konverterings guiden för integrerings tjänster, som också kan startas från SSMS. Mer information finns i artikeln [konvertera projekt till projekt distributions modellen](https://docs.microsoft.com/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-2017#convert).

> [!NOTE]
> Azure Database Migration Service (DMS) stöder för närvarande inte migrering av en käll-SSISDB till en Azure SQL Database-Server, men du kan distribuera om SSIS-projekt/-paketen med följande process.

I den här artikeln kan du se hur du:
> [!div class="checklist"]
>
> * Utvärdera käll SSIS projekt/paket.
> * Migrera SSIS-projekt/-paket till Azure.

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande för att slutföra de här stegen:

* SSMS version 17,2 eller senare.
* En instans av mål databas servern som ska vara värd för SSISDB. Om du inte redan har en, skapar du en Azure SQL Database Server (utan en databas) med hjälp av Azure Portal genom att gå till [formuläret](https://ms.portal.azure.com/#create/Microsoft.SQLServer)SQL Server (endast logisk server).
* SSIS måste etableras i Azure Data Factory (ADF) som innehåller Azure-SSIS Integration Runtime (IR) med mål-SSISDB som finns på Azure SQL Database Server-instansen (enligt beskrivningen i artikeln [etablera Azure-SSIS integration runtime i Azure Data Factory](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)).

## <a name="assess-source-ssis-projectspackages"></a>Utvärdera käll SSIS projekt/paket

Även om utvärderingen av käll-SSISDB inte har integrerats i databasen Migration Assistant (DMA) eller Azure Database Migration Service (DMS) kommer dina SSIS-projekt/-paket att bedömas/verifieras när de distribueras om till mål SSISDB som finns på en Azure SQL Database-Server.

## <a name="migrate-ssis-projectspackages"></a>Migrera SSIS-projekt/-paket

Utför följande steg om du vill migrera SSIS-projekt/-paket till Azure SQL Database Server.

1. Öppna SSMS och välj sedan **alternativ** för att Visa dialog rutan **Anslut till Server** .

2. På fliken **inloggning** anger du den information som krävs för att ansluta till den Azure SQL Database-Server som ska vara värd för mål-SSISDB.

    ![Fliken SSIS login](media/how-to-migrate-ssis-packages/dms-ssis-login-tab.png)

3. På fliken **anslutnings egenskaper** i text rutan **Anslut till databas** väljer eller anger du **SSISDB**och väljer sedan **Anslut**.

    ![SSIS-fliken Egenskaper för anslutning](media/how-to-migrate-ssis-packages/dms-ssis-conncetion-properties-tab.png)

4. I SSMS Object Explorer expanderar du noden **Integration Services-kataloger** , expanderar **SSISDB**och om det inte finns några befintliga mappar, högerklickar du på **SSISDB** och skapar en ny mapp.

5. Under **SSISDB**, expanderar du en mapp, högerklickar på **projekt**och väljer sedan **distribuera projekt**.

    ![SSIS SSISDB-noden expanderad](media/how-to-migrate-ssis-packages/dms-ssis-ssisdb-node-expanded.png)

6. Granska informationen på sidan **Introduktion** i distributions guiden för integrerings tjänster och välj sedan **Nästa**.

    ![Distributions guidens introduktions sida](media/how-to-migrate-ssis-packages/dms-deployment-wizard-introduction-page.png)

7. På sidan **Välj källa** anger du det befintliga SSIS-projekt som du vill distribuera.

    Om SSMS också är ansluten till SQL Server som är värd för käll-SSISDB väljer du **Integration Services-katalogen**och anger sedan Server namnet och projekt Sök vägen i katalogen för att distribuera projektet direkt.

    Alternativt väljer du **projekt distributions fil**och anger sedan sökvägen till en befintlig projekt distributions fil (. ispac) för att distribuera projektet.

    ![Sidan Välj källa i distributions guiden](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-source-page.png)
 
8. Välj **Nästa**.
9. På sidan **Välj mål** anger du målet för projektet.

    a. I text rutan Server Namn anger du det fullständigt kvalificerade Azure SQL Database Server namnet (< server_name >. Database. Windows. net).

    b. Ange autentiseringsinformation och välj sedan **Anslut**.

    ![Sidan för distributions guiden Välj målsidan](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-destination-page.png)

    c. Välj **Bläddra** för att ange målmappen i SSISDB och välj sedan **Nästa**.

    > [!NOTE]
    > Knappen **Nästa** aktive ras bara när du har valt **Anslut**.

10. Visa eventuella fel och varningar på sidan **Verifiera** och ändra sedan paketen efter behov.

    ![Sidan verifiera distributions guiden](media/how-to-migrate-ssis-packages/dms-deployment-wizard-validate-page.png)

11. Välj **Nästa**.

12. På sidan **Granska** granskar du distributions inställningarna.

    > [!NOTE]
    > Du kan ändra inställningarna genom att välja **föregående** eller genom att välja någon av steg länkarna i det vänstra fönstret.

13. Välj **distribuera** för att starta distributions processen.

14. När distributions processen har slutförts kan du Visa resultat sidan som visar att varje distributions åtgärd lyckades eller Miss lyckas.
    a. Om en åtgärd Miss lyckas väljer du **misslyckades** i kolumnen **resultat** för att visa en förklaring av felet.
    b. Du kan också välja **Spara rapport** om du vill spara resultatet i en XML-fil.

15. Välj **Stäng** för att avsluta distributions guiden för integrerings tjänster.

Om distributionen av projektet lyckas utan problem kan du välja de paket som det innehåller för att köra på din Azure-SSIS IR.

## <a name="next-steps"></a>Nästa steg

* Läs igenom vägledningen för migrering i Microsoft [Database migration guide](https://datamigration.microsoft.com/).
