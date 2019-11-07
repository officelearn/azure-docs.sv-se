---
title: Azure Synapse Analytics (tidigare SQL DW) vanliga frågor och svar
description: Den här artikeln innehåller vanliga frågor och svar om Azure Synapse Analytics (tidigare SQL DW) från kunder och utvecklare
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: f7b4f926bb9611d87c67276f754a6b596850b59d
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73645598"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-frequently-asked-questions"></a>Azure Synapse Analytics (tidigare SQL DW) vanliga frågor och svar

## <a name="general"></a>Allmänt

F. Vad är Azure Synapse?

A. Azure Synapse är en obegränsad analys tjänst som samlar ihop data lager och stor data analys. Det ger dig friheten att fråga efter data på dina villkor, med hjälp av antingen server utan på begäran eller etablerade resurser – i stor skala. Azure synapse ger de här två världar tillsammans med en enhetlig upplevelse för att mata in, förbereda, hantera och hantera data för omedelbara BI-och maskin inlärnings behov. Mer information finns i, [Vad är Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md).

F. Vad hände med att Azure SQL Data Warehouse?

A. Azure Synapse är Azure SQL Data Warehouse (SQL DW) har utvecklats. Vi har tagit samma bransch ledande informations lager till en helt ny nivå av prestanda och kapacitet. Du kan fortsätta att köra dina befintliga arbets belastningar för data lager i produktion idag med Azure Synapse och automatiskt dra nytta av de nya funktionerna som finns i för hands versionen. Mer information finns i, [Vad är Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md).

F. Vad är SQL Analytics?

A. SQL Analytics syftar på de företags data lager funktioner som är allmänt tillgängliga med Azure Synapse. Mer information finns i, [Vad är Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md).

F. Hur gör jag för att kom igång med Azure Synapse?

A. Du kan komma igång med ett [kostnads fritt Azure-konto](https://azure.microsoft.com/free/sql-data-warehouse/) eller en [kontakt försäljning för mer information](https://info.microsoft.com/ww-landing-azure-sql-data-warehouse-contactme.html). 

F. Vad erbjuder Azure Synapse för data säkerhet?

A. Azure Synapse erbjuder flera lösningar för att skydda data, till exempel TDE och granskning. Mer information finns i [säkerhet].

F. Var kan jag ta reda på vilka juridiska eller affärs standarder som Azure Synapse är kompatibel med?

A. Besök sidan [Microsoft Compliance] ) om du vill ha olika regelefterlevnad efter produkt som SOC och ISO. Börja med att välja efter rubrik för efterlevnad och expandera sedan Azure i avsnittet Microsoft-omfånget moln tjänster till höger på sidan för att se vilka tjänster som är kompatibla med Azure Synapse.

F. Kan jag ansluta Power BI?

A. Visst! Även om Power BI stöder direkt frågor med Azure Synapse är det inte avsett för ett stort antal användare eller real tids data. Om du vill optimera Power BI prestanda ytterligare bör du överväga att använda Power BI ovanpå Azure Analysis Services-eller Analysis Service-IaaS.

F. Vad är kapacitets gränser för SQL Analytics?

A. Se vår aktuella [kapacitets begränsning] . 

F. Varför tar min skala/pausa/återupptas så länge?

A. En mängd olika faktorer kan påverka tiden för beräknings hanterings åtgärder. Ett vanligt fall för tids krävande åtgärder är transaktionell återställning. När en skalnings-eller paus åtgärd initieras blockeras alla inkommande sessioner och frågor töms. För att datorn ska vara i ett stabilt tillstånd måste transaktionerna återställas innan en åtgärd kan påbörjas. Ju större siffra och större logg storlek för transaktionerna, desto längre tid kommer åtgärden att stoppas för att återställa systemet till ett stabilt tillstånd.

## <a name="user-support"></a>Stöd för användare

F. Jag har en funktions förfrågan, där jag skickar den?

A. Om du har en funktions förfrågan skickar du den på vår [UserVoice] -sida

F. Hur kan jag göra x?

A. Om du vill ha hjälp med att utveckla med Azure Synapse kan du ställa frågor på vår [Stack Overflow] sida. 

F. Hur gör jag för att skicka ett support ärende?

A. [Support biljetter] kan arkiveras via Azure Portal.

## <a name="sql-languagefeature-support"></a>Stöd för SQL-språk/-funktioner 

F. Vilka data typer stöds?

A. Se [data typer].

F. Vilka tabell funktioner stöder?

A. Många funktioner stöds, de som inte stöds och dokumenteras i [Tabell funktioner som inte stöds]som inte stöds.

## <a name="tooling-and-administration"></a>Verktyg och administration

F. Har stöd för databas projekt i Visual Studio.

A. Vi stöder för närvarande inte databas projekt i Visual Studio. Om du vill omvandla en röst för att få den här funktionen kan du gå till vår [Funktions förfrågan för databas projekt]användar rösten.

F. Stöder SQL Analytics REST-API: er?

A. Ja. De flesta REST-funktioner som kan användas med SQL Database finns också i SQL Analytics. Du kan hitta API-information i REST-dokumentations sidor eller [MSDN].


## <a name="loading"></a>Läser in

F. Vilka klient driv rutiner stöder du?

A. Driv rutins stöd för DW hittar du på sidan [Anslutningssträngar]

F: vilka fil format stöds av PolyBase?

A: Orc, RC, Parquet och Flat-avgränsad text

F: vilka data källor kan jag ansluta till med PolyBase? 

A: [Azure Data Lake Store] och [Azure Storage blobbar]

F: är beräknings mottagnings möjlig vid anslutning till Azure Storage blobbar eller ADLS? 

A: Nej, PolyBase samverkar bara med lagrings komponenterna. 

F: kan jag ansluta till HDI?

A: HDI kan använda antingen ADLS eller WASB som HDFS-skiktet. Om du har antingen ditt HDFS-lager kan du läsa in dessa data i SQL DW. Du kan dock inte generera mottagnings-beräkning till HDI-instansen. 

## <a name="next-steps"></a>Nästa steg
Mer information om Azure Synapse som helhet finns på vår [Översikt] sida.


<!-- Article references -->
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Anslutningssträngar]: ./sql-data-warehouse-connection-strings.md
[Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Support biljetter]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Säkerhet]: ./sql-data-warehouse-overview-manage-security.md
[Microsoft Compliance]: https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings
[kapacitets begränsning]: ./sql-data-warehouse-service-capacity-limits.md
[data typer]: ./sql-data-warehouse-tables-data-types.md
[Tabell funktioner som inte stöds]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[Azure Data Lake Store]: ./sql-data-warehouse-load-from-azure-data-lake-store.md
[Azure Storage blobbar]: ./sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md
[Funktions förfrågan för databas projekt]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
[MSDN]: https://msdn.microsoft.com/library/azure/mt163685.aspx
[Översikt]: ./sql-data-warehouse-overview-faq.md
