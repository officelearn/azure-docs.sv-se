---
title: Kontinuerlig integration och distribution
description: Databas DevOps-upplevelse i företags klass för SQL Data Warehouse med inbyggt stöd för kontinuerlig integrering och distribution med hjälp av Azure pipelines.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: overview
ms.subservice: integration
ms.date: 08/28/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: e8d7e7764a01dbd0169efae093bac4d984982108
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74708657"
---
# <a name="continuous-integration-and-deployment-for-azure-sql-data-warehouse"></a>Kontinuerlig integrering och distribution för Azure SQL Data Warehouse

Den här enkla själv studie kursen beskriver hur du integrerar SQL Server ditt SSDT-databas projekt med Azure DevOps och utnyttjar Azure-pipeliner för att konfigurera kontinuerlig integrering och distribution. Den här självstudien är det andra steget i att skapa en pipeline för kontinuerlig integrering och distribution med SQL Data Warehouse. 

## <a name="before-you-begin"></a>Innan du börjar

- Gå igenom [självstudien för käll kontrolls integrering](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-source-control-integration)

- Konfigurera och Anslut till Azure-DevOps


## <a name="continuous-integration-with-visual-studio-build"></a>Kontinuerlig integrering med Visual Studio build

1. Navigera till Azure-pipeliner och skapa en ny versions pipeline

      ![Ny pipeline](media/sql-data-warehouse-continuous-integration-and-deployment/1-new-build-pipeline.png "Ny pipeline")

2. Välj din käll kods lagrings plats (Azure databaser git) och välj mallen .NET Desktop app

      ![Pipeline-installation](media/sql-data-warehouse-continuous-integration-and-deployment/2-pipeline-setup.png "Pipeline-installation") 

3. Redigera din YAML-fil och Använd rätt pool för din agent. Din YAML-fil bör se ut ungefär så här:

      ![YAML](media/sql-data-warehouse-continuous-integration-and-deployment/3-yaml-file.png "YAML")

Nu har du en enkel miljö där all incheckning av huvud grenen för käll kontrollens lagrings plats bör automatiskt utlösa en lyckad Visual Studio-version av ditt databas projekt. Verifiera att automatiseringen fungerar och slutar genom att göra en ändring i ditt lokala databas projekt och kontrol lera ändringen i huvud grenen.


## <a name="continuous-deployment-with-the-azure-sql-data-warehouse-or-database-deployment-task"></a>Kontinuerlig distribution med distributions aktiviteten Azure SQL Data Warehouse (eller databas)

1. Lägg till en ny aktivitet med [Azure SQL Database distributions uppgift](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/sql-azure-dacpac-deployment?view=azure-devops) och fyll i de obligatoriska fälten för att ansluta till mål informations lagret. När den här aktiviteten körs distribueras DACPAC som genererades från den tidigare Bygg processen till mål informations lagret. Du kan också använda [distributions uppgiften för Azure SQL DataWarehouse](https://marketplace.visualstudio.com/items?itemName=ms-sql-dw.SQLDWDeployment) 

      ![Distributions aktivitet](media/sql-data-warehouse-continuous-integration-and-deployment/4-deployment-task.png "Distributions aktivitet")

2. Om du använder en lokal agent, se till att du ställer in din miljö variabel så att den använder rätt SqlPackage. exe för SQL Data Warehouse. Sökvägen bör se ut ungefär så här:

      ![Miljö variabel](media/sql-data-warehouse-continuous-integration-and-deployment/5-environment-variable-preview.png "Miljö variabel")

   C:\Program Files (x86) \Microsoft Visual Studio\2019\Preview\Common7\IDE\Extensions\Microsoft\SQLDB\DAC\150  

   Kör och verifiera din pipeline. Du kan göra ändringar lokalt och kontrol lera ändringar i din käll kontroll som ska generera en automatisk version och distribution.

## <a name="next-steps"></a>Nästa steg

- Utforska [Azure SQL Data Warehouse-arkitekturen](/azure/sql-data-warehouse/massively-parallel-processing-mpp-architecture)
- [Skapa en SQL Data Warehouse][create a SQL Data Warehouse] snabbt
- [Läs in exempel data][load sample data].
- Utforska [videor](/azure/sql-data-warehouse/sql-data-warehouse-videos)



<!--Image references-->

[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[Create a support ticket]: ./sql-data-warehouse-get-started-create-support-ticket.md
[load sample data]: ./sql-data-warehouse-load-sample-databases.md
[create a SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md
[Migration documentation]: ./sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
[Integrated tools overview]: ./sql-data-warehouse-overview-integrate.md
[Backup and restore overview]: ./sql-data-warehouse-restore-database-overview.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md

<!--MSDN references-->

<!--Other Web references-->
[Blogs]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Customer Advisory Team blogs]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Feature requests]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[MSDN forum]: https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureSQLDataWarehouse
[Stack Overflow forum]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[SLA for SQL Data Warehouse]: https://azure.microsoft.com/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Service Level Agreements]: https://azure.microsoft.com/support/legal/sla/
