---
title: "Övervaka databasprestanda i Azure SQL Database | Microsoft Docs"
description: "Läs mer om alternativ för att övervaka din databas med Azure-verktyg och dynamiska hanteringsvyer."
keywords: "databasövervakning, molndatabasprestanda"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: a2e47475-c955-4a8d-a65c-cbef9a6d9b9f
ms.service: sql-database
ms.custom: monitor and tune
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 09/27/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 5cda0c5b491f71999da0b7bd70156256ab2d5908


---
# <a name="monitoring-database-performance-in-azure-sql-database"></a>Övervaka databasprestanda i Azure SQL Database
Prestandaövervakning för en SQL-databas i Azure startar med att övervaka resursutnyttjandet i förhållande till nivån på databasprestanda som du valt. Övervakning hjälper dig att avgöra om din databas har överflödig kapacitet eller har problem på grund av att resurserna är överutnyttjade och därefter bestämma när det är dags att justera prestandanivån och [tjänstnivån](sql-database-service-tiers.md) för databasen. Du kan övervaka din databas med grafiska verktyg i [Azure-portalen](https://portal.azure.com) eller med SQL [dynamiska hanteringsvyer](https://msdn.microsoft.com/library/ms188754.aspx).

## <a name="monitor-databases-using-the-azure-portal"></a>Övervaka databaser med Azure-portalen
I [Azure-portalen](https://portal.azure.com/), kan du övervaka en enskild databas resursutnyttjning genom att välja din databas och klicka på **Övervaknings**-diagrammet. Det öppnar **Mått**-fönstret, vilket du kan ändra genom att klicka på **Redigera diagram**-knappen. Lägg till följande mått:

* CPU-procent
* DTU-procent
* Data IO-procent
* Databasstorlek i procent

När du väl lagt till de här måtten, kan du fortsätta att se dem i **Övervaknings**-diagrammet med mer information i **Mått**-fönstret. Alla fyra mätvärdena visar ett snittvärde för utnyttjandeprocent i förhållande till din databas **DTU:er**. Se artikeln [tjänstnivåer](sql-database-service-tiers.md) för mer information om DTU:er.

![Tjänstnivå-övervakning av databasprestanda.](./media/sql-database-service-tiers/sqldb_service_tier_monitoring.png)

Du kan också konfigurera aviseringar på prestandamåtten. Klicka på knappen **Lägg till avisering** i **mått**-fönstret. Följ guiden för att konfigurera aviseringen. Du har möjlighet att avisera om måttet överskrider ett visst tröskelvärde eller om måttet faller under ett visst tröskelvärde.

Om du exempelvis förväntar dig att arbetsbelastningen på din databas kommer att öka, kan du välja att konfigurera en e-postavisering när din databas kommer upp i 80 % för något av prestandamåtten. Du kan använda det här som en tidig varningssignal för att veta när du kan behöva byta till en högre prestandanivå.

Prestandamåtten kan också hjälpa dig att bestämma om kan nedgradera till en lägre prestandanivå. Anta att du använder en Standard S2-databas och alla prestandamått visar att databasen i snitt inte använder mer än 10 % vid något tillfälle. Det är då troligt att databasen skulle fungera bra i Standard S1. Var dock medveten om arbetsbelastningar som varierar kraftigt innan du beslutar att flytta till en lägre prestandanivå.

## <a name="monitor-databases-using-dmvs"></a>Övervaka databaser med hjälp av DMV:er
Samma mått som exponeras i portalen, går också att få fram i systemvyer: [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) i den logiska **huvud**-databasen på din server, samt [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) i användardatabasen. Använd **sys.resource_stats** om du behöver övervaka mindre detaljerad data över en längre tidsperiod. Använd **sys.dm_db_resource_stats** om du behöver övervaka mer detaljerad data inom ett kortare tidsintervall. Mer information finns i [Azure SQL Database-prestandaråd](sql-database-performance-guidance.md#monitor-resource-use).

> [!NOTE]
> **sys.dm_db_resource_stats** returnerar en tom resultatuppsättning när den används i databaser av Webb- och Business-utgåva som är föråldrade.
>
>

För elastiska databaspooler, kan du övervaka individuella databaser i poolen med de tekniker som beskrivs i det här avsnittet. Men du kan också övervaka poolen som helhet. Mer information finns i [Övervaka och hantera en elastisk databaspool](sql-database-elastic-pool-manage-portal.md).



<!--HONumber=Dec16_HO1-->


