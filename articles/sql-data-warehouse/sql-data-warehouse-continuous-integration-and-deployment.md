---
title: Kontinuerlig integrering och distribution | Microsoft Docs
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
ms.openlocfilehash: 7afb616fffaf01b1efa4f5d5a4af9dddd9683781
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2019
ms.locfileid: "70143151"
---
# <a name="continuous-integration-and-deployment-for-azure-sql-data-warehouse"></a>Kontinuerlig integrering och distribution för Azure SQL Data Warehouse

Den här enkla själv studie kursen beskriver hur du integrerar SQL Server ditt SSDT-databas projekt med Azure DevOps och utnyttjar Azure-pipeliner för att konfigurera kontinuerlig integrering och distribution. Den här självstudien är det andra steget i att skapa en pipeline för kontinuerlig integrering och distribution med SQL Data Warehouse. 

## <a name="before-you-begin"></a>Innan du börjar

- Gå igenom självstudien för [käll kontrolls integrering](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-source-control-integration)

- Skapa en [egen värdbaserad agent](https://docs.microsoft.com/azure/devops/pipelines/agents/agents?view=azure-devops#install) som har SSDT Preview-bitar (16,3 Preview 2 och senare) installerad för SQL Data Warehouse (för hands version)

- Konfigurera och Anslut till Azure-DevOps

  > [!NOTE]
  > SSDT är för närvarande en för hands version där du kommer att behöva använda en egen värd agent. Microsofts värdbaserade agenter kommer att uppdateras under de kommande månaderna.

## <a name="continuous-integration-with-visual-studio-build"></a>Kontinuerlig integrering med Visual Studio build

1. Navigera till Azure-pipeliner och skapa en ny versions pipeline

      ![Ny pipeline](media/sql-data-warehouse-continuous-integration-and-deployment/1-new-build-pipeline.png "Ny pipeline")

2. Välj din käll kods lagrings plats (Azure databaser git) och välj mallen .NET Desktop app

      ![Pipeline-installation](media/sql-data-warehouse-continuous-integration-and-deployment/2-pipeline-setup.png "Pipeline-installation") 

3. Redigera din YAML-fil och Använd rätt pool för din agent. Din YAML-fil bör se ut ungefär så här:

      ![YAML](media/sql-data-warehouse-continuous-integration-and-deployment/3-yaml-file.png "YAML")

Nu har du en enkel miljö där all incheckning av huvud grenen för käll kontrollens lagrings plats bör automatiskt utlösa en lyckad Visual Studio-version av ditt databas projekt. Verifiera att automatiseringen fungerar och slutar genom att göra en ändring i ditt lokala databas projekt och kontrol lera ändringen i huvud grenen.


## <a name="continuous-deployment-with-the-azure-sql-database-deployment-task"></a>Kontinuerlig distribution med Azure SQL Database distributions aktivitet

1. Lägg till en ny aktivitet med [Azure SQL Database distributions uppgift](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/sql-azure-dacpac-deployment?view=azure-devops) och fyll i de obligatoriska fälten för att ansluta till mål informations lagret. När den här aktiviteten körs distribueras DACPAC som genererades från den tidigare Bygg processen till mål informations lagret.

      ![Distributions aktivitet](media/sql-data-warehouse-continuous-integration-and-deployment/4-deployment-task.png "Distributions aktivitet")

2. När du använder en lokal agent kontrollerar du att du ställer in din miljö variabel så att den använder rätt SqlPackage. exe för SQL Data Warehouse. Sökvägen bör se ut ungefär så här:

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
