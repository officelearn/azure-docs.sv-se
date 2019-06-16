---
title: Distribuera om SQL Server Integration Services-paket till Azure SQL Database | Microsoft Docs
description: Lär dig mer om att migrera SQL Server Integration Services-paket till Azure SQL Database.
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
ms.openlocfilehash: 603a9df8e3f499c832bbfdcbef966de86003d6b7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67080639"
---
# <a name="redeploy-sql-server-integration-services-packages-to-azure-sql-database"></a>Distribuera om SQL Server Integration Services-paket till Azure SQL Database

Om du använder SQL Server Integration Services (SSIS) och vill migrera dina SSIS-projekt /-paket från källan SSISDB installation av SQL Server till målet SSISDB med Azure SQL Database, du kan distribuera om dem med hjälp av Integration Services-distribution Guiden. Du kan starta guiden från inom SQL Server Management Studio (SSMS).

Om versionen av SSIS som du använder är äldre än 2012 innan omdistribuera dina SSIS-projekt /-paket till projektet distributionsmodellen måste du först konvertera dem med hjälp av Integration Services-projekt Konverteringsguiden, som också kan startas från SSMS. Mer information finns i artikeln [konvertering projekt till projektet distributionsmodellen](https://docs.microsoft.com/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-2017#convert).

> [!NOTE]
> Azure Database Migration Service (DMS) för närvarande stöder inte migreringen av en källa SSISDB till en Azure SQL Database-server, men du kan distribuera om dina SSIS-projekt /-paket på följande sätt.

I den här artikeln kan du se hur du:
> [!div class="checklist"]
>
> * Utvärdera källa SSIS-projekt /-paket.
> * Migrera SSIS-projekt /-paket till Azure.

## <a name="prerequisites"></a>Nödvändiga komponenter

Du behöver följande för att slutföra dessa steg:

* SSMS 17.2 eller senare.
* En instans av din mål-database-server som värd för SSISDB. Om du inte redan har en, skapa en Azure SQL Database-server (utan en databas) med Azure portal genom att gå till SQL Server (endast logisk server) [formuläret](https://ms.portal.azure.com/#create/Microsoft.SQLServer).
* SSIS måste etableras i Azure Data Factory (ADF) som innehåller Azure-SSIS Integration Runtime (IR) med målet SSISDB som värd för instansen av Azure SQL Database-server (enligt beskrivningen i artikeln [etablera Azure-SSIS-Integration Runtime i Azure Data Factory](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)).

## <a name="assess-source-ssis-projectspackages"></a>Utvärdera källa SSIS-projekt/paket

Även om utvärdering av källa SSISDB ännu inte ingår i den Database Migration Assistant (DMA) eller Azure Database Migration Service (DMS), dina SSIS-projekt /-paket för att utvärdera/verifieras som de är omdistribueras till målet SSISDB finns på en Azure SQL Database-server.

## <a name="migrate-ssis-projectspackages"></a>Migrera SSIS-projekt /-paket

Utför följande steg om du vill migrera SSIS-projekt /-paket till Azure SQL Database-server.

1. Öppna SSMS och välj sedan **alternativ** att visa den **Anslut till Server** dialogrutan.

2. På den **inloggning** fliken, anger du information som behövs för att ansluta till Azure SQL Database-server som är värd för SSISDB målet.

    ![Fliken SSIS-inloggning](media/how-to-migrate-ssis-packages/dms-ssis-login-tab.png)

3. På den **anslutningsegenskaper** fliken den **Anslut till databas** textrutan, Välj eller ange **SSISDB**, och välj sedan **Connect**.

    ![Anslutningsegenskaper för SSIS-fliken](media/how-to-migrate-ssis-packages/dms-ssis-conncetion-properties-tab.png)

4. I SSMS Object Explorer expanderar du den **Integration Services kataloger** nod, expandera **SSISDB**, och om det finns inga befintliga mappar, högerklicka på **SSISDB** och Skapa en ny mapp.

5. Under **SSISDB**och valfri mapp, högerklickar på **projekt**, och välj sedan **distribuera projektet**.

    ![SSIS SSISDB nod expanderas](media/how-to-migrate-ssis-packages/dms-ssis-ssisdb-node-expanded.png)

6. I distributionsguiden för Integration-tjänster på den **introduktion** sidan Granska informationen och välj sedan **nästa**.

    ![Guiden introduktion distributionssida](media/how-to-migrate-ssis-packages/dms-deployment-wizard-introduction-page.png)

7. På den **Välj källa för** anger du det befintliga SSIS-projekt som du vill distribuera.

    Om SSMS är också ansluten till SQL Server som är värd för SSISDB källan, Välj **Integration Services-katalogen**, och ange sökvägen för server namn och projekt i din katalog för att distribuera projektet direkt.

    Alternativt, Välj **distribution projektfilen**, och ange sökvägen till en befintlig distribution projektfil (.ispac) att distribuera projektet.

    ![Välj källa för guiden distributionssida](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-source-page.png)
 
8. Välj **Nästa**.
9. På den **Välj mål** anger målet för ditt projekt.

    a. I textrutan för servernamn anger du det fullständigt kvalificerade Azure SQL Database-servernamnet (< server_name >. database.windows.net).

    b. Ange autentiseringsinformationen och välj sedan **Connect**.

    ![Guiden Välj mål distributionssida](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-destination-page.png)

    c. Välj **Bläddra** att ange målmappen i SSISDB och välj sedan **nästa**.

    > [!NOTE]
    > Den **nästa** knappen aktiveras bara när du har valt **Connect**.

10. På den **verifiera** sidan, visa alla fel/varningar, och om det behövs ändrar dina paket i enlighet med detta.

    ![Guiden Verifiera distributionssida](media/how-to-migrate-ssis-packages/dms-deployment-wizard-validate-page.png)

11. Välj **Nästa**.

12. På den **granska** granskar du distributionsinställningarna för.

    > [!NOTE]
    > Du kan ändra dina inställningar genom att välja **föregående** eller genom att välja någon av steg länkar i den vänstra rutan.

13. Välj **distribuera** att starta distributionsprocessen.

14. När distributionsprocessen är klar kan visa du sidan resultat, som visar lyckad eller misslyckad åtgärd för varje distribution.
    a. Om något misslyckades i den **resultatet** kolumnen, markerar **misslyckades** att visa en förklaring av felet.
    b. Alternativt kan du välja **Spara rapport** spara resultaten i en XML-fil.

15. Välj **Stäng** avsluta distributionsguiden för Integration Services.

Om distributionen av projektet lyckas utan fel, kan du välja alla paket som den innehåller för att köras på din Azure-SSIS IR.

## <a name="next-steps"></a>Nästa steg

* Granska migreringsvägledningen i Microsofts [Guide för Databasmigrering](https://datamigration.microsoft.com/).
