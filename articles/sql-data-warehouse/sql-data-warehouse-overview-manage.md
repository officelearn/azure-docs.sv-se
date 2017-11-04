---
title: Hantera databaser i Azure SQL Data Warehouse | Microsoft Docs
description: "Översikt över hantering av SQL Data Warehouse-databaser. Innehåller hanteringsverktyg, dwu: er och skalbar prestanda, felsökning frågeprestanda, etablera bra säkerhetsprinciper och återställa en databas från skadade data eller från ett regionalt strömavbrott."
services: sql-data-warehouse
documentationcenter: NA
author: kevinvngo
manager: jhubbard
editor: 
ms.assetid: 8576fdb3-71fe-4b3b-a4e0-5e8a7f148acf
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 10/31/2016
ms.author: kevin;barbkess
ms.openlocfilehash: b14d0aad5a1f50c225391dbab27ec6240423a65a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="manage-databases-in-azure-sql-data-warehouse"></a>Hantera databaser i Azure SQL Data Warehouse
SQL Data Warehouse automatiserar många aspekter av att hantera dina databaser. Om du vill skala prestanda behöver du bara att justera och betala för rätt nivå av beräkningsresurser och därefter låta SQL Data Warehouse gör allt arbete med skala ut och skala tillbaka.

Säkerligen kommer du vilja övervaka din arbetsbelastning för att identifiera dina prestandabehov samt felsöka tidskrävande frågor. Du måste också utföra några säkerhetsaktiviteter för att hantera behörigheter för användare och inloggningar.

Den här översikten beskrivs dessa aspekter av att hantera SQL Data Warehouse.

* Hanteringsverktyg
* Skala bearbetning
* Pausa och återuppta
* Bästa praxis för prestanda
* Övervakning av frågan
* Säkerhet
* Säkerhetskopiering och återställning

## <a name="management-tools"></a>Hanteringsverktyg
Du kan använda en mängd olika verktyg för att hantera databaser i SQL Data Warehouse. När du hanterar databaser utvecklar du inställningar för varje typ av uppgift som du behöver utföra.

### <a name="azure-portal"></a>Azure Portal
Den [Azure-portalen] [ Azure portal] är en webbaserad portal där du kan skapa, uppdatera, och ta bort databaser och övervaka databasresurser. Det här verktyget är bra om du precis har börjat med Azure, hantera ett litet antal databaser för datalager, eller gör så snabbt.

Om du vill komma igång med Azure-portalen, se [skapa ett SQL Data Warehouse (Azure portal)][Create a SQL Data Warehouse (Azure portal)].

### <a name="sql-server-data-tools-in-visual-studio"></a>SQL Server Data Tools i Visual Studio
[SQL Server Data Tools] [ SQL Server Data Tools] (SSDT) i Visual Studio kan du ansluta till, hantera och utveckla dina databaser. Om du är programutvecklare som är bekant med Visual Studio eller andra integrerad utvecklingsmiljöer (IDEs) kan du använda SSDT i Visual Studio.

SSDT innehåller SQL Server Object Explorer, som gör det möjligt att visualisera, ansluta och köra skript mot SQL Data Warehouse-databaser. För att snabbt ansluta till SQL Data Warehouse, klickar du bara den **öppna i Visual Studio** knapp i kommandofältet när Visa databasen information i den klassiska Azure-portalen.  

Om du vill komma igång med SSDT i Visual Studio, se [frågan Azure SQL Data Warehouse med Visual Studio][Query Azure SQL Data Warehouse with Visual Studio].

### <a name="command-line-tools"></a>Kommandoradsverktyg
Kommandoradsverktyg lämpar sig för att automatisera dina arbetsbelastningar.  PowerShell och sqlcmd finns två bra sätt att automatisera dina processer.  Vi rekommenderar att dessa verktyg för att hantera ett stort antal logiska servrar och distribuera resursändringar i en produktionsmiljö som nödvändiga åtgärder kan användas av skriptet och sedan automatiseras.

### <a name="dynamic-management-views"></a>Dynamiska hanteringsvyer
Av DMV: er är bröd och smör för att hantera SQL Data Warehouse. Nästan alla information som hämtar i portalen är beroende av DMV: er. Om du vill se en lista över SQL Data Warehouse av DMV: er Se [SQL Data Warehouse systemvyer][SQL Data Warehouse system views].

Om du vill komma igång finns [Anslut och fråga med sqlcmd][Connect and query with sqlcmd], och [skapa en databas (PowerShell)][Create a database (PowerShell)].

## <a name="scale-compute"></a>Skala bearbetning
Du kan snabbt skala ut eller igen genom att öka eller minska beräkningsresurser av CPU, minne och i/o-bandbredd i SQL Data Warehouse. Om du vill skala prestanda är allt du behöver göra justera antalet informationslagerenheter (dwu: er) som SQL Data Warehouse allokerar till din databas. SQL Data Warehouse snabbt gör ändringen och hanterar alla underliggande ändringar av maskinvara eller programvara.

Mer information om att skala dwu: er finns [skala prestanda].

## <a name="pause-and-resume"></a>Pausa och återuppta
Du kan pausa och återuppta beräkning resurser på begäran för att spara kostnader. Till exempel om du inte använder databasen under natten och helger, kan du pausa under dessa tider och återuppta under dagen. Du kommer inte att debiteras för dwu: er när databasen har pausats.

Mer information finns i [pausa beräkning][Pause compute], och [återuppta databearbetning][Resume compute].

## <a name="performance-best-practices"></a>Bästa praxis för prestanda
När du börjar arbeta med en ny teknik, kan identifiera tips och råd som fungerar bäst direkt från början spara mycket tid.  Du hittar bästa praxis i många av våra artiklar.

Många en sammanfattning av de viktigaste överväganden när du utvecklar din arbetsbelastning finns [Metodtips för SQL Data Warehouse][SQL Data Warehouse Best Practices].

## <a name="query-monitoring"></a>Övervakning av frågan
En fråga körs ibland för länge, men du är inte säker på vilket som är sällan. SQL Data Warehouse har dynamiska hanteringsvyer (av DMV: er) som du kan använda för att ta reda vilka frågan tar för lång.

Du hittar tidskrävande frågor [övervaka din arbetsbelastning med av DMV: er][Monitor your workload using DMVs].

## <a name="security"></a>Säkerhet
För att underhålla en säker, måste du vara på aviseringen och skydda datorn mot alla typer av obehörig åtkomst. Ett säkerhetssystem måste se till att brandväggsreglerna på plats så att endast auktoriserade IP-adresser kan ansluta. Den måste korrekt autentisering av användarautentiseringsuppgifter. När en användare har anslutit till databasen kan bör användaren endast behörighet att utföra ett minimalt antal åtgärder. Du kan använda kryptering för att skydda data. Det är också viktigt att granska och spåra så att du kan gå händelser om det inte finns några misstänkt aktivitet.

Mer information om att hantera säkerhet, gå till den [Säkerhetsöversikt][Security overview].

## <a name="backup-and-restore"></a>Säkerhetskopiering och återställning
Med tillförlitlig backps av dina data är en viktig del av en produktionsdatabas. SQL Data Warehouse hålls data säker genom att säkerhetskopiera active databaserna automatiskt med jämna mellanrum. Dessa säkerhetskopior kan du återställa från scenarier där du har skadade data eller av misstag bort data eller databasen.  Data schemat för säkerhetskopiering, bevarandeprincip och hur du återställer en databas, finns i [återställning från ögonblicksbild][Restore from snapshot].

## <a name="next-steps"></a>Nästa steg
Med bra design principer gör det enklare att hantera dina databaser i SQL Data Warehouse. Mer information, gå till den [utvecklingsöversikt][Development overview].

<!--Image references-->

<!--Article references-->
[Create a SQL Data Warehouse (Azure Portal)]: sql-data-warehouse-get-started-provision.md
[Create a database (PowerShell)]: sql-data-warehouse-get-started-provision-powershell.md
[connection]: sql-data-warehouse-develop-connections.md
[Query Azure SQL Data Warehouse with Visual Studio]: sql-data-warehouse-query-visual-studio.md
[Connect and query with sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md
[Development overview]: sql-data-warehouse-overview-develop.md
[Monitor your workload using DMVs]: sql-data-warehouse-manage-monitor.md
[Pause compute]: sql-data-warehouse-manage-compute-overview.md#pause-compute-bk
[Restore from snapshot]: sql-data-warehouse-restore-database-overview.md
[Resume compute]: sql-data-warehouse-manage-compute-overview.md#resume-compute-bk
[skala prestanda]: sql-data-warehouse-manage-compute-overview.md#scale-compute
[Security overview]: sql-data-warehouse-overview-manage-security.md
[SQL Data Warehouse Best Practices]: sql-data-warehouse-best-practices.md
[SQL Data Warehouse system views]: sql-data-warehouse-reference-tsql-system-views.md

<!--MSDN references-->
[SQL Server Data Tools]: https://msdn.microsoft.com/library/mt204009.aspx

<!--Other web references-->
[Azure portal]: http://portal.azure.com/
