---
title: Uppgradera till den senaste generationen Azure SQL Data Warehouse | Microsoft Docs
description: Steg för att uppgradera Azure SQL Data Warehouse till senaste generationens Azure maskin- och storage-arkitektur.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.services: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/02/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 42b716274e655bf91f72c1b3ab207b8a5f1ccee0
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="upgrade-to-latest-generation-of-azure-sql-data-warehouse-in-the-azure-portal"></a>Uppgradera till senaste generationens Azure SQL Data Warehouse i Azure-portalen

Använda Azure-portalen för att uppgradera din Azure SQL Data Warehouse om du vill använda den senaste generationen Azure maskin- och storage-arkitektur. Genom att uppgradera, kan du dra nytta av bättre prestanda, bättre skalbarhet och obegränsad lagring för columnstore-index.  

## <a name="applies-to"></a>Gäller för
Den här uppgraderingen gäller datalager i optimerad för elasticitet prestandanivå.  Instruktionerna uppgradera ett data warehouse från optimerad för elasticitet prestandanivån till optimerad för beräkning prestandanivå. 

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="before-you-begin"></a>Innan du börjar

1. Om optimerad för elasticitet datalagret som ska uppgraderas är pausad [återuppta datalagret](pause-and-resume-compute-portal.md).
2. Att förbereda för ett par minuter stillestånd. 
3. Uppgraderingsprocessen stoppar alla sessioner och släpper alla anslutningar. Kontrollera dina frågor har slutförts innan du uppgraderar. Om du startar en uppgradering med pågående transaktioner kan rollback-tid vara omfattande. 

## <a name="start-the-upgrade"></a>Starta uppgraderingen

1. Öppna ditt data warehouse i Azure-portalen och på **uppgradering till optimerad för beräkning**.
2. Observera optimerad för beräkning prestanda nivå val. Standardalternativet är jämförbar med den aktuella nivån före uppgraderingen.
3. Välj en nivå med prestanda. Priset för optimerad för beräkning prestandanivån är halv för närvarande inaktiverat i förhandsversionen.
4. Klicka på **uppgradera**.
5. Kontrollera statusen i Azure-portalen.
6. Vänta tills datalagret för att ändra till Online.

## <a name="rebuild-columnstore-indexes"></a>Återskapa columnstore-index

När ditt data warehouse är online kan du läsa in data och köra frågor. Prestanda kan dock vara långsamma först eftersom bakgrunden håller på att migrera data till ny maskinvara. 

Om du vill göra data att migrera så snart som möjligt rekommenderar vi att återskapa columnstore-index. Om du vill göra detta finns riktlinjer för [återskapa columnstore-index för att förbättra kvaliteten segment](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality). 

## <a name="next-steps"></a>Nästa steg
Ditt data warehouse är online. Om du vill använda de nya funktionerna för prestanda finns [resursklasser för hantering av arbetsbelastning](resource-classes-for-workload-management.md).
 