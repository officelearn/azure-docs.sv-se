---
title: Kontinuerlig integrering och distribution
description: Databasutvecklar i företagsklass för datalagring med inbyggt stöd för kontinuerlig integrering och distribution med Azure Pipelines.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: f73fbbc42085b3a0cb6ab7dd1c0e32838a166ccf
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350642"
---
# <a name="continuous-integration-and-deployment-for-data-warehousing"></a>Kontinuerlig integrering och distribution för datalagring

Den här enkla självstudien beskriver hur du integrerar ditt SSDT-databasprojekt (SQL Server Data Tools) med Azure DevOps och använder Azure Pipelines för att konfigurera kontinuerlig integrering och distribution. Den här självstudien är det andra steget för att skapa din kontinuerliga integrations- och distributionspipeline för datalagring. 

## <a name="before-you-begin"></a>Innan du börjar

- Gå igenom [självstudien för källkontrollintegration](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-source-control-integration)

- Konfigurera och ansluta till Azure DevOps


## <a name="continuous-integration-with-visual-studio-build"></a>Kontinuerlig integrering med Visual Studio-build

1. Navigera till Azure Pipelines och skapa en ny build pipeline.

      ![Ny pipeline](./media/sql-data-warehouse-continuous-integration-and-deployment/1-new-build-pipeline.png "Ny pipeline")

2. Välj källkodsdatabasen (Azure Repos Git) och välj .NET Desktop-appmallen.

      ![Installation av pipeline](./media/sql-data-warehouse-continuous-integration-and-deployment/2-pipeline-setup.png "Installation av pipeline") 

3. Redigera YAML-filen så att den använder rätt pool på agenten. Yaml-filen ska se ut ungefär så här:

      ![YAML](./media/sql-data-warehouse-continuous-integration-and-deployment/3-yaml-file.png "YAML")

Nu har du en enkel miljö där alla incheckningar till källkontrolldatabasens huvudgren automatiskt ska utlösa en lyckad Visual Studio-version av databasprojektet. Verifiera automatiseringen fungerar från till genom att göra en ändring i det lokala databasprojektet och checka in ändringen till huvudgrenen.


## <a name="continuous-deployment-with-the-azure-sql-data-warehouse-or-database-deployment-task"></a>Kontinuerlig distribution med distributionsuppgiften för Azure SQL Data Warehouse (eller Databas)

1. Lägg till en ny uppgift med hjälp av [distributionsuppgiften för Azure SQL Database](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/sql-azure-dacpac-deployment?view=azure-devops) och fyll i de obligatoriska fälten för att ansluta till ditt måldatalager. När den här aktiviteten körs distribueras DACPAC som genererats från den tidigare byggprocessen till måldatalagret. Du kan också använda [distributionsuppgiften för Azure SQL Data Warehouse](https://marketplace.visualstudio.com/items?itemName=ms-sql-dw.SQLDWDeployment). 

      ![Distributionsaktivitet](./media/sql-data-warehouse-continuous-integration-and-deployment/4-deployment-task.png "Distributionsaktivitet")

2. Om du använder en självvärdställd agent kontrollerar du att du ställer in miljövariabeln så att den använder rätt SqlPackage.exe för SQL Data Warehouse. Sökvägen ska se ut ungefär så här:

      ![Miljövariabel](./media/sql-data-warehouse-continuous-integration-and-deployment/5-environment-variable-preview.png "Miljövariabel")

   C:\Program-filer (x86)\Microsoft Visual Studio\2019\Preview\Common7\IDE\Extensions\Microsoft\SQLDB\DAC\150  

   Kör och validera din pipeline. Du kan göra ändringar lokalt och checka in ändringar i källkontrollen som ska generera en automatisk version och distribution.

## <a name="next-steps"></a>Nästa steg

- Utforska [SQL Analytics MPP-arkitektur](massively-parallel-processing-mpp-architecture.md)
- Skapa snabbt [en SQL-pool](create-data-warehouse-portal.md)
- [Läsa in exempeldata](load-data-from-azure-blob-storage-using-polybase.md)
- Utforska [videoklipp](/azure/sql-data-warehouse/sql-data-warehouse-videos)
