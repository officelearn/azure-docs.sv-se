---
title: Vanliga frågor och svar om Azure SQL Data Warehouse | Microsoft Docs
description: Den här artikeln innehåller reda på vanliga frågor och svar om Azure SQL Data Warehouse från kunder och utvecklare
services: sql-data-warehouse
author: acomet
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 04/17/2018
ms.author: acomet
ms.reviewer: igorstan
ms.openlocfilehash: 7a570dfa6e0a2812a8dd3a25b8903c70fe07befc
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/12/2018
ms.locfileid: "38971711"
---
# <a name="sql-data-warehouse-frequently-asked-questions"></a>SQL Data Warehouse vanliga frågor och svar

## <a name="general"></a>Allmänt

FRÅGOR OCH. Vad erbjuder SQL DW för datasäkerhet?

A. SQL DW erbjuder flera lösningar för att skydda data, till exempel transparent Datakryptering och granskning. Mer information finns i [Säkerhet].

FRÅGOR OCH. Var hittar jag reda på vilka juridiska eller business-standarder SQL DW som är kompatibla med?

A. Gå till den [Microsoft-efterlevnad] för olika efterlevnadserbjudanden av produkten som SOC och ISO. Först väljer efter efterlevnad rubrik och sedan expandera Azure under Microsoft omfattade cloud services till höger på sidan för att se vilka tjänster som är Azure-tjänster är kompatibla.

FRÅGOR OCH. Kan jag ansluta Power BI?

A. Visst! Om Power BI stöder direkt fråga med SQL DW, är den inte avsedd för många användare eller data i realtid. För användning i produktion av Power BI bör du använda Power BI ovanpå Azure Analysis Services eller Analysis Service IaaS. 

FRÅGOR OCH. Vilka är Kapacitetsbegränsningarna för SQL Data Warehouse?

A. Se våra aktuella [kapacitetsbegränsningar] sidan. 

FRÅGOR OCH. Varför är min skala/pausa/återuppta tar så lång tid?

A. En mängd olika faktorer kan påverka tiden för hanteringsåtgärder för beräkning. Ett vanligt användningsfall för tidskrävande åtgärder är transaktionell återställning. När en skala eller pausa åtgärd initieras, blockeras alla inkommande sessioner och frågor är tömda. För att du lämnar systemet i ett stabilt tillstånd, måste transaktioner distribueras tillbaka innan en åtgärd kan påbörjas. Det största antalet och större loggstorleken av transaktioner, desto längre tid åtgärden kommer ha stoppats återställa systemet till ett stabilt tillstånd.

## <a name="user-support"></a>Stöd för användare

FRÅGOR OCH. Jag har en funktionsbegäran där skickar jag det?

A. Om du har en funktionsbegäran skickar du den på vår [UserVoice] sidan

FRÅGOR OCH. Hur gör jag x?

A. Behöver hjälp med att utveckla med SQL Data Warehouse, kan du ställa frågor på våra [Stack Overflow] sidan. 

FRÅGOR OCH. Hur gör jag för att skicka ett supportärende?

A. [Supportärenden] kan registreras via Azure-portalen.

## <a name="sql-languagefeature-support"></a>SQL language/funktioner som stöds 

FRÅGOR OCH. Vilka datatyper stöder SQL Data Warehouse?

A. Finns i SQL Data Warehouse [datatyper].

FRÅGOR OCH. Vilka table-funktioner stöder ni?

A. Medan SQL Data Warehouse stöder många funktioner, vissa stöds inte och dokumenteras i [funktioner som inte stöds tabell].

## <a name="tooling-and-administration"></a>Verktyg och administration

FRÅGOR OCH. Du har stöd för databas-projekt i Visual Studio.

A. Vi stöder för närvarande inte databasprojekt i Visual Studio för SQL Data Warehouse. Om du vill konvertera en röst att hämta den här funktionen kan du gå till vår User Voice [Funktionsförfrågan för databas-projekt].

FRÅGOR OCH. SQL Data Warehouse har stöd för REST API: er?

A. Ja. De flesta REST-funktioner som kan användas med SQL Database är också tillgängliga med SQL Data Warehouse. Du kan hitta API-information i REST-dokumentationssidorna eller [MSDN].


## <a name="loading"></a>Läser in

FRÅGOR OCH. Vilka klientdrivrutiner som stöder ni?

A. Stödet för DW kan hittas på den [anslutningssträngar] sidan

F: vilka filformat som stöds av PolyBase med SQL Data Warehouse?

S: Orc, RC, Parquet och fast avgränsad text

F: Vad kan jag ansluta till från SQL DW med PolyBase? 

S: [Azure Data Lake Store] och [Azure Storage-Blobbar]

F: är beräkning pushdown möjligt vid anslutning till Azure Storage-Blobbar eller ADLS? 

S: SQL DW PolyBase interagerar Nej, endast lagringskomponenter. 

F: kan jag ansluta till HDI?

S: HDI kan använda antingen ADLS eller WASB som det HDFS-lagret. Om du har antingen som HDFS-lager, kan du läsa in dessa data i SQL DW. Men kan inte du generera pushdown beräkning till HDI-instans. 

## <a name="next-steps"></a>Nästa steg
Mer information om SQL Data Warehouse som helhet finns i vår [översikt] sidan.


<!-- Article references -->
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Anslutningssträngar]: ./sql-data-warehouse-connection-strings.md
[Stack Overflow]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Supportärenden]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Säkerhet]: ./sql-data-warehouse-overview-manage-security.md
[Microsoft-efterlevnad]: https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings
[kapacitetsbegränsningar]: ./sql-data-warehouse-service-capacity-limits.md
[datatyper]: ./sql-data-warehouse-tables-data-types.md
[Funktioner som inte stöds tabell]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[Azure Data Lake Store]: ./sql-data-warehouse-load-from-azure-data-lake-store.md
[Azure Storage-Blobbar]: ./sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md
[Funktionsförfrågan för databas-projekt]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
[MSDN]: https://msdn.microsoft.com/library/azure/mt163685.aspx
[Översikt]: ./sql-data-warehouse-overview-faq.md
