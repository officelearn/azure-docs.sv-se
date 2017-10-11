---
title: "Migrera lösningen till SQL Data Warehouse | Microsoft Docs"
description: "Riktlinjer för migrering för att skapa din lösning för Azure SQL Data Warehouse-plattformen."
services: sql-data-warehouse
documentationcenter: NA
author: sqlmojo
manager: jhubbard
editor: 
ms.assetid: 198365eb-7451-4222-b99c-d1d9ef687f1b
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: migrate
ms.date: 06/27/2017
ms.author: joeyong;barbkess
ms.openlocfilehash: 771b9456e66b8a1e41f72340b695b19e2adaf793
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="migrate-your-solution-to-azure-sql-data-warehouse"></a>Migrera lösningen till Azure SQL Data Warehouse
Se vad ingår i att migrera en befintlig databaslösning till Azure SQL Data Warehouse. 

## <a name="profile-your-workload"></a>Profilen din arbetsbelastning
Innan du migrerar, som du vill att vissa SQL Data Warehouse är den rätta lösningen för din arbetsbelastning. SQL Data Warehouse är ett distribuerat system som utformats för att utföra analyser på stora mängder data.  Migrera till SQL Data Warehouse kräver vissa ändringar av design som inte är hårt för att förstå men kan ta lite tid att genomföra. Om ditt företag kräver ett informationslager i företagsklass, är fördelarna värt att arbete. Om du inte behöver kraften i SQL Data Warehouse, är det dock mer kostnadseffektivt att använda SQL Server eller Azure SQL Database.

Överväg att använda SQL Data Warehouse när du:
- Har en eller flera terabyte data
- Planera att köra analytics på stora mängder data
- Ha möjlighet att skala beräkning och lagring 
- Vill du spara kostnader genom att pausa beräkningsresurser när de inte behövs.

Använd inte SQL Data Warehouse för operativa (OLTP) arbetsbelastningar som har:
- Hög frekvens läser och skriver
- Stort antal singleton väljer
- Stora mängder enradig infogningar
- Rad för rad bearbetning behov
- Inkompatibla format (JSON, XML)


## <a name="plan-the-migration"></a>Planera för migrering

När du har valt att migrera en befintlig lösning till SQL Data Warehouse, är det viktigt att planera för migrering för att komma igång. 

Ett mål av planering är att säkerställa att dina data, din tabellscheman och koden är kompatibla med SQL Data Warehouse. Det finns vissa skillnader kompatibilitet undvika mellan din aktuella systemet och SQL Data Warehouse. Dessutom att migrera stora mängder data till Azure tar tid. Noggrann planering påskyndar hämtar dina data till Azure. 

Ett annat mål av planering är att justera design så din lösning drar nytta av de hög frågeprestanda SQL Data Warehouse är utformad att ge. Designa datalager för att skala introducerar olika design mönster och så traditionella metoder är inte alltid bäst. Även om du kan göra vissa design justeringar efter migreringen, sparar att göra ändringar i processen snabbare tid senare.

För att genomföra en framgångsrik migrering, måste du migrera din tabellscheman, koden och dina data. Information om dessa migreringsavsnitt finns:

-  [Migrera dina scheman](sql-data-warehouse-migrate-schema.md)
-  [Migrera koden](sql-data-warehouse-migrate-code.md)
-  [Migrera dina data](sql-data-warehouse-migrate-data.md). 

<!--
## Perform the migration


## Deploy the solution


## Validate the migration

-->

## <a name="next-steps"></a>Nästa steg
CAT (Customer Advisory Team) har också bra vägledning SQL Data Warehouse, som de publicerar via bloggar.  Ta en titt på deras artikel [migrera data till Azure SQL Data Warehouse i praktiken] [ Migrating data to Azure SQL Data Warehouse in practice] för ytterligare information om migrering.

<!--Image references-->

<!--Article references-->

<!--MSDN references-->

<!--Other Web references-->
[Migrating data to Azure SQL Data Warehouse in practice]: https://blogs.msdn.microsoft.com/sqlcat/2016/08/18/migrating-data-to-azure-sql-data-warehouse-in-practice/
