---
title: Vanliga frågor och svar i Azure Synapse Analytics (tidigare SQL DW) Vanliga frågor
description: I den här artikeln visas vanliga frågor om Azure Synapse Analytics (tidigare SQL DW) från kunder och utvecklare
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: dcefe6a6144c9a8f9c6ab7daf5b34b2d81a73c3f
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743012"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-frequently-asked-questions"></a>Vanliga frågor och svar i Azure Synapse Analytics (tidigare SQL DW) Vanliga frågor och svar

## <a name="general"></a>Allmänt

F. Vad är Azure Synapse?

A. Azure Synapse är en obegränsad analystjänst som sammanför datalagring och Big Data-analys. Det ger dig friheten att fråga data på dina villkor, med hjälp av antingen serverlösa on-demand eller etablerade resurser - i stor skala. Azure Synapse sammanför dessa två världar med en enhetlig upplevelse för att inta, förbereda, hantera och hantera data för omedelbara bi- och maskininlärningsbehov. Mer information finns i [Vad är Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md).

F. Vad har hänt med Azure SQL Data Warehouse?

A. Azure Synapse är Azure SQL Data Warehouse (SQL DW) utvecklats. Vi har tagit samma branschledande informationslager till en helt ny nivå av prestanda och kapacitet. Du kan fortsätta köra dina befintliga datalagerarbetsbelastningar i produktion med Azure Synapse och automatiskt dra nytta av de nya funktionerna, som är i förhandsversion. Mer information finns i [Vad är Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md).

F. Vad är Synapse SQL-pool?

A. Synapse SQL-pool refererar till de funktioner för företagsdatalagring som är allmänt tillgängliga med Azure Synapse. Mer information finns i [Vad är Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md).

F. Hur kommer jag igång med Azure Synapse?

A. Du kan komma igång med ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/sql-data-warehouse/) eller [en kontaktförsäljning för mer information](https://info.microsoft.com/ww-landing-azure-sql-data-warehouse-contactme.html).

F. Vad erbjuder Azure Synapse för datasäkerhet?

A. Azure Synapse erbjuder flera lösningar för att skydda data som TDE och granskning. Mer information finns i [Säkerhet](sql-data-warehouse-overview-manage-security.md).

F. Var kan jag ta reda på vilka juridiska standarder eller affärsstandarder Som Azure Synapse är kompatibelt med?

A. Besök sidan [Microsoft Compliance](https://www.microsoft.com/trustcenter/compliance/complianceofferings) för olika efterlevnadserbjudanden per produkt, till exempel SOC och ISO.
Välj först efter efterlevnadstitel. Expandera sedan Azure i avsnittet Microsofts molntjänster i omfattning till höger på sidan för att se vilka tjänster som är Azure Synapse-kompatibla.

F. Kan jag ansluta Power BI?

A. Visst! Även om Power BI stöder direkt fråga med Azure Synapse är det inte avsett för ett stort antal användare eller realtidsdata. Om du vill optimera Power BI-prestanda ytterligare bör du överväga att använda Power BI ovanpå Azure Analysis Services eller Analysis Service IaaS.

F. Vad är Kapacitetsbegränsningar för Synapse SQL-pool?

A. Se vår nuvarande [sida för kapacitetsbegränsningar.](sql-data-warehouse-service-capacity-limits.md)

F. Varför tar min skala/paus/meritförteckning så lång tid?

A. Flera faktorer kan påverka tiden för beräkningshanteringsåtgärder. Ett vanligt fall för tidskrävande åtgärder är transaktionsåterfällning. När en skalnings- eller pausåtgärd initieras blockeras alla inkommande sessioner och frågor töms. För att systemet ska kunna lämnas i stabilt tillstånd måste transaktioner återställas innan en operation kan påbörjas. Ju större antal och större loggstorleken för transaktioner, desto längre har åtgärden stoppats återställa systemet till ett stabilt tillstånd.

## <a name="user-support"></a>Stöd för användare

F. Jag har en funktionsförfrågan, var skickar jag in den?

A. Om du har en funktionsförfrågan skickar du den på vår [UserVoice-sida](https://feedback.azure.com/forums/307516-sql-data-warehouse)

F. Hur kan jag göra x?

A. Om du vill ha hjälp med att utveckla med Azure Synapse kan du ställa frågor på vår [stackspillsida.](https://stackoverflow.com/questions/tagged/azure-sqldw)

F. Hur skickar jag in en supportbiljett?

A. [Supportbiljetter](sql-data-warehouse-get-started-create-support-ticket.md) kan arkiveras via Azure-portalen.

## <a name="sql-languagefeature-support"></a>Stöd för SQL-språk/funktioner

F. Vilka datatyper stöds?

A. Se [datatyper](sql-data-warehouse-tables-data-types.md).

F. Vilka tabellfunktioner stöder du?

A. Många funktioner stöds. Funktioner som inte stöds finns i [Tabellfunktioner som inte stöds](sql-data-warehouse-tables-data-types.md).

## <a name="tooling-and-administration"></a>Verktyg och administration

F. Stöder Synapse SQL-pool REST API:er?

A. Ja. De flesta REST-funktioner som kan användas med SQL Database finns också med Synapse SQL-pool. Du hittar API-information i REST-dokumentationssidor eller [databaser](/rest/api/sql/databases?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="loading"></a>Laddar

F. Vilka klientdrivrutiner stöder du?

A. Drivrutinsstöd för Synapse SQL-pool finns på sidan [Anslutningssträngar](sql-data-warehouse-connection-strings.md)

F: Vilka filformat stöds av PolyBase?

A: Orc, RC, Parkett och platt avgränsad text

F: Vilka datakällor kan jag ansluta till med PolyBase?

S: [Azure Data Lake Storage](sql-data-warehouse-load-from-azure-data-lake-store.md) och Azure Storage [Blobbar](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)

F: Är beräknings pushdown möjligt när du ansluter till Azure Storage Blobbar eller ADLS?

S: Nej, PolyBase interagerar bara med lagringskomponenterna.

F: Kan jag ansluta till HDI?

S: HDI kan använda antingen ADLS eller WASB som HDFS-lager. Om du har antingen som ditt HDFS-lager kan du läsa in dessa data i en Synapse SQL-pool. Du kan dock inte generera pushdown-beräkning till HDI-instansen.

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Synapse som helhet finns på vår [översiktssida.](sql-data-warehouse-overview-faq.md)
