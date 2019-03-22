---
title: Migrera SQL Server Integration Services-paket till Azure | Microsoft Docs
description: Lär dig mer om att migrera SQL Server Integration Services-paket till Azure.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 03/12/2019
ms.openlocfilehash: 884af4624c1e92ee765353c90fd189220664381d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58183432"
---
# <a name="migrate-sql-server-integration-services-packages-to-azure"></a>Migrera SQL Server Integration Services-paket till Azure
Om du använder SQL Server Integration Services (SSIS) och vill migrera dina SSIS-projekt /-paket från källan SSISDB installation av SQL Server till målet SSISDB med Azure SQL Database-server eller Azure SQL Database Managed Instance, du kan distribuera dem på nytt med hjälp av guiden för användning av Integration Services. Du kan starta guiden från inom SQL Server Management Studio (SSMS).

Om versionen av SSIS som du använder är äldre än 2012 innan omdistribuera dina SSIS-projekt /-paket till projektet distributionsmodellen måste du först konvertera dem med hjälp av Integration Services-projekt Konverteringsguiden, som också kan startas från SSMS. Mer information finns i artikeln [konvertering projekt till projektet distributionsmodellen](https://docs.microsoft.com/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-2017#convert).

> [!NOTE]
> Azure Database Migration Service (DMS) för närvarande stöder inte migreringen av en källa SSISDB, men du kan distribuera om dina SSIS-projekt /-paket på följande sätt. 

I den här artikeln kan du se hur du:
> [!div class="checklist"]
> * Utvärdera källa SSIS-projekt /-paket.
> * Migrera SSIS-projekt /-paket till Azure.

## <a name="prerequisites"></a>Förutsättningar
Du behöver följande för att slutföra dessa steg:

- SSMS 17.2 eller senare.
- En instans av din mål-database-server som värd för SSISDB.
 
  Om du inte redan har en:
    - Skapa en Azure SQL Database-server (utan en databas) med Azure portal genom att gå till SQL Server (endast logisk server) för Azure SQL Database, [formuläret](https://ms.portal.azure.com/#create/Microsoft.SQLServer).
    - Azure SQL Database Managed Instance, Följ i detalj i artikeln [skapa en Azure SQL Database Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started).

- SSIS måste etableras i Azure Data Factory (ADF) som innehåller Azure-SSIS Integration Runtime (IR) med målet SSISDB som värd för instansen av Azure SQL Database-server (enligt beskrivningen i artikeln [etablera Azure-SSIS-Integration Runtime i Azure Data Factory](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)) eller Azure SQL Database Managed Instance (enligt beskrivningen i artikeln [skapa Azure-SSIS integration runtime i Azure Data Factory](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)). 

## <a name="assess-source-ssis-projectspackages"></a>Utvärdera källa SSIS-projekt/paket
Även om utvärdering av källa SSISDB ännu inte ingår i den Database Migration Assistant (DMA) eller Azure Database Migration Service (DMS), verifieras dina SSIS-projekt /-paket för att utvärdera/som de är omdistribueras till målet SSISDB finns på Azure SQL Database-server eller Azure SQL Database Managed Instance.

## <a name="migrate-ssis-projectspackages"></a>Migrera SSIS-projekt /-paket
Utför följande steg om du vill migrera SSIS-projekt /-paket till Azure SQL Database-server eller Azure SQL Database Managed Instance.

1.  Öppna SSMS och välj sedan **alternativ** att visa den **Anslut till Server** dialogrutan.

2.  På den **inloggning** fliken, anger du information som behövs för att ansluta till Azure SQL Database-server eller Azure SQL Database Managed Instance som är värd för SSISDB målet.

    ![Fliken SSIS-inloggning](media/how-to-migrate-ssis-packages/dms-ssis-login-tab.png)
 
3.  På den **anslutningsegenskaper** fliken den **Anslut till databas** textrutan, Välj eller ange **SSISDB**, och välj sedan **Connect**.

    ![Anslutningsegenskaper för SSIS-fliken](media/how-to-migrate-ssis-packages/dms-ssis-conncetion-properties-tab.png)

4.  I SSMS Object Explorer expanderar du den **Integration Services kataloger** nod, expandera **SSISDB**, och om det finns inga befintliga mappar, högerklicka på **SSISDB** och Skapa en ny mapp.

5.  Under **SSISDB**och valfri mapp, högerklickar på **projekt**, och välj sedan **distribuera projektet**.

    ![SSIS SSISDB nod expanderas](media/how-to-migrate-ssis-packages/dms-ssis-ssisdb-node-expanded.png)

6.  I distributionsguiden för Integration-tjänster på den **introduktion** sidan Granska informationen och välj sedan **nästa**.

    ![Guiden introduktion distributionssida](media/how-to-migrate-ssis-packages/dms-deployment-wizard-introduction-page.png)

7.  På den **Välj källa för** anger du det befintliga SSIS-projekt som du vill distribuera.

    Om SSMS är också ansluten till SQL Server som är värd för SSISDB källan, Välj **Integration Services-katalogen**, och ange sökvägen för server namn och projekt i din katalog för att distribuera projektet direkt.

    Alternativt, Välj **distribution projektfilen**, och ange sökvägen till en befintlig distribution projektfil (.ispac) att distribuera projektet.

    ![Välj källa för guiden distributionssida](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-source-page.png)
 
8.  Välj **Nästa**.
9.  På den **Välj mål** anger målet för ditt projekt.

       a. I textrutan för servernamn anger du det fullständigt kvalificerade Azure SQL Database-servernamnet (< server_name >. database.windows.net) eller Azure SQL Database Managed Instance name (< server_name.dns_prefix >. database.windows.net).
 
       b. Ange autentiseringsinformationen och välj sedan **Connect**.
    
       ![Guiden Välj mål distributionssida](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-destination-page.png)

    c. Välj Bläddra för att ange målmappen i SSISDB och sedan klicka på Nästa.

    > [!NOTE]
    > Den **nästa** knappen aktiveras bara när du har valt **Connect**. 

10. På den **verifiera** sidan, visa alla fel/varningar, och om det behövs ändrar dina paket i enlighet med detta.

    ![Guiden Verifiera distributionssida](media/how-to-migrate-ssis-packages/dms-deployment-wizard-validate-page.png)

11. Välj **Nästa**.

12. På den **granska** granskar du distributionsinställningarna för.

    > [!NOTE]
    > Du kan ändra dina inställningar genom att välja föregående eller genom att välja någon av steg länkar i den vänstra rutan.

13. Välj **distribuera** att starta distributionsprocessen.

14. När distributionsprocessen är klar kan visa du sidan resultat, som visar lyckad eller misslyckad åtgärd för varje distribution.
    a. Om något misslyckades i den **resultatet** kolumnen, markerar **misslyckades** att visa en förklaring av felet.
    b. Alternativt kan du välja **Spara rapport** spara resultaten i en XML-fil.

15. Välj **Stäng** avsluta distributionsguiden för Integration Services.

Om distributionen av projektet lyckas utan fel, kan du välja alla paket som den innehåller för att köras på din Azure-SSIS IR.

## <a name="next-steps"></a>Nästa steg
- Granska migreringsvägledningen i Microsofts [Guide för Databasmigrering](https://datamigration.microsoft.com/).