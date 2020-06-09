---
title: Azure Synapse Analytics (tidigare SQL DW) vanliga frågor och svar
description: Den här artikeln innehåller vanliga frågor och svar om Azure Synapse Analytics (tidigare SQL DW) från kunder och utvecklare
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 8b9a1caa750c7c27b2a6c845843b8bb6b779086d
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/09/2020
ms.locfileid: "84561330"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-frequently-asked-questions"></a>Azure Synapse Analytics (tidigare SQL DW) vanliga frågor och svar

## <a name="general"></a>Allmänt

F. Vad är Azure Synapse?

A. Azure Synapse är en analys tjänst som samlar ihop data lager och stor data analys. Azure Synapse kombinerar dessa två världar med en enhetlig upplevelse för att mata in, förbereda, hantera och hantera data för BI-och maskin inlärnings behov. Mer information finns i, [Vad är Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md).

F. Vad hände med att Azure SQL Data Warehouse?

A. Azure Synapse är Azure SQL Data Warehouse (SQL DW) har utvecklats. Vi har tagit samma branschledande informations lager till en helt ny nivå av prestanda och kapacitet. Du kan fortsätta att köra dina befintliga arbets belastningar för data lager i produktion med Azure Synapse. Mer information finns i [Vad är Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md).

F. Vad är Synapse SQL-pool?

A. Synapse SQL-pool syftar på de företags data lager funktioner som är allmänt tillgängliga med Azure Synapse. Mer information finns i, [Vad är Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md).

F. Hur gör jag för att kom igång med Azure Synapse?

A. Du kan komma igång med ett [kostnads fritt Azure-konto](https://azure.microsoft.com/free/sql-data-warehouse/) eller en [kontakt försäljning för mer information](https://info.microsoft.com/ww-landing-azure-sql-data-warehouse-contactme.html).

F. Vad erbjuder Azure Synapse för data säkerhet?

A. Azure Synapse erbjuder flera lösningar för att skydda data, till exempel TDE och granskning. Mer information finns i [säkerhet](sql-data-warehouse-overview-manage-security.md).

F. Var kan jag ta reda på vilka juridiska eller affärs standarder som Azure Synapse är kompatibel med?

A. Besök sidan [Microsoft Compliance (efterlevnad](https://www.microsoft.com/trustcenter/compliance/complianceofferings) ) om du vill ha olika regelefterlevnad efter produkt som SOC och ISO. Välj först efter rubrik för efterlevnad. Expandera sedan Azure i avsnittet moln tjänster för Microsoft i området till höger på sidan för att se vilka tjänster som är kompatibla med Azure Synapse.

F. Kan jag ansluta Power BI?

A. Ja! Även om Power BI stöder direkt frågor med Azure Synapse är det inte avsett för ett stort antal användare eller real tids data. Om du vill optimera Power BI prestanda ytterligare bör du överväga att använda Power BI ovanpå Azure Analysis Services-eller Analysis Service-IaaS.

F. Vad är kapacitets gränser för Synapse i SQL-pool?

A. Se vår aktuella [kapacitets begränsning](sql-data-warehouse-service-capacity-limits.md) .

F. Varför tar min skala/pausa/återupptas så länge?

A. Flera faktorer kan påverka tiden för beräknings hanterings åtgärder. Ett vanligt fall för tids krävande åtgärder är transaktionell återställning. När en skalnings-eller paus åtgärd initieras blockeras alla inkommande sessioner och frågor töms. För att datorn ska vara i ett stabilt tillstånd måste transaktionerna återställas innan en åtgärd kan påbörjas. Ju större siffra och större logg storlek för transaktionerna, desto längre tid kommer åtgärden att stoppas för att återställa systemet till ett stabilt tillstånd.

F: Hur gör jag för att byta namn på en publicerad artefakt (data uppsättning, Notebook, SQL-skript och så vidare) i Synapse?

A: om du vill byta namn på en publicerad artefakt fil ska du först klona filen och byta namn på det nya fil namn som du föredrar. Du måste manuellt uppdatera alla referenserna för artefakten till den nya filen och ta bort den gamla.

## <a name="user-support"></a>Stöd för användare

F. Jag har en funktions förfrågan, där jag skickar den?

A. Om du har en funktions förfrågan skickar du den på vår [UserVoice](https://feedback.azure.com/forums/307516-sql-data-warehouse) -sida

F. Hur kan jag göra x?

A. Om du vill ha hjälp med att utveckla med Azure Synapse kan du ställa frågor på vår [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw) sida.

F. Hur gör jag för att skicka ett support ärende?

A. [Support biljetter](sql-data-warehouse-get-started-create-support-ticket.md) kan arkiveras via Azure Portal.

## <a name="sql-languagefeature-support"></a>Stöd för SQL-språk/-funktioner

F. Vilka data typer stöds?

A. Se [data typer](sql-data-warehouse-tables-data-types.md).

F. Vilka tabell funktioner stöder?

A. Många funktioner stöds. Funktioner som inte stöds finns i [tabell funktioner som inte stöds](sql-data-warehouse-tables-data-types.md).

## <a name="tooling-and-administration"></a>Verktyg och administration

F. Stöder SQL-Synapse REST API: er?

A. Ja. De flesta REST-funktioner som kan användas med SQL Database är också tillgängliga med Synapse SQL-pool. Du kan hitta API-information i REST-dokumentationens sidor eller [databaser](/rest/api/sql/databases?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="loading"></a>Vid

F. Vilka klient driv rutiner stöder du?

A. Driv rutins stöd för Synapse-SQL-pool finns på sidan [anslutnings strängar](../sql/connection-strings.md)

F: vilka fil format stöds av PolyBase?

A: Orc, RC, Parquet och Flat-avgränsad text

F: vilka data källor kan jag ansluta till med PolyBase?

A: [Azure Data Lake Storage](sql-data-warehouse-load-from-azure-data-lake-store.md) och [Azure Storage blobbar](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)

F: är beräknings mottagnings möjlig vid anslutning till Azure Storage blobbar eller ADLS?

A: Nej, PolyBase samverkar bara med lagrings komponenterna.

F: kan jag ansluta till HDI?

A: HDI kan använda antingen ADLS eller WASB som HDFS-skiktet. Om du har antingen ditt HDFS-lager kan du läsa in dessa data i en Synapse SQL-pool. Du kan dock inte generera mottagnings-beräkning till HDI-instansen.

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Synapse som helhet finns på vår [översikts](sql-data-warehouse-overview-faq.md) sida.
