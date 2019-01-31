---
title: Azure SQL Data Warehouse viktig oktober 2018 | Microsoft Docs
description: Viktig information för Azure SQL Data Warehouse.
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 12/04/2018
ms.author: mausher
ms.reviewer: twounder
ms.openlocfilehash: 81096eeb1edcd6681bdc887d267ee477d78a8578
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55472090"
---
# <a name="whats-new-in-azure-sql-data-warehouse-october-2018"></a>Vad är nytt i Azure SQL Data Warehouse? Oktober 2018
Azure SQL Data Warehouse tar emot förbättringar kontinuerligt. Den här artikeln beskriver nya funktioner och ändringar som har införts i oktober 2018.

## <a name="devops-for-data-warehousing"></a>DevOps för datalager
Mycket begärda funktionen för SQL Data Warehouse (SQL DW) är nu i förhandsversion med stöd för SQL Server Data verktyget (SSDT) i Visual Studio! Team med utvecklare kan nu samarbeta över ett enda, version-kontrollerade kodbasen och snabbt distribuera ändringarna till en valfri instans i världen. Är du intresserad av att ansluta till? Den här funktionen är tillgänglig för förhandsgranskning i dag! Du kan registrera genom att besöka den [SQL Data Warehouse Visual Studio SQL Server Data Tools (SSDT) - registrering för förhandsversionen formuläret](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR4-brmKy3TZOjoktwuHd7S1UODkwQ1lVMEw1NDBGRjNLRDNWOFlQRUpIRi4u). Med hög efterfrågan kan hanterar vi får vara med i förhandsversionen för bästa möjliga upplevelse för våra kunder. När du har registrerat dig är vårt mål att bekräfta din status inom sju dagar.

## <a name="row-level-security-generally-available"></a>Säkerhet på radnivå är allmänt tillgänglig
Azure SQL Data Warehouse (SQL DW) har nu stöd för säkerhet på radnivå (RLS) att lägga till en kraftfull funktion för att skydda känsliga data. Du kan implementera säkerhetsprinciper för att styra åtkomsten till rader i tabeller, som i som har åtkomst till vilka rader med introduktionen av RLS. RLS låter den här detaljerad åtkomstkontroll utan att behöva ändra designen på ditt informationslager. RLS förenklar övergripande säkerhetsmodellen genom att logiken för begränsningen finns på databasnivån själva i stället för från data i ett annat program. RLS eliminerar också behovet av att introducera vyer för att filtrera bort rader för hantering av åtkomstkontroll. Det finns ingen extra kostnad för den här säkerhetsfunktionen i företagsklass för alla våra kunder.

## <a name="advanced-advisors"></a>Avancerade rådgivare
Avancerade inställningen för Azure SQL Data Warehouse (SQL DW) bara fick enklare med ytterligare data warehouse rekommendationer och mått. Det finns ytterligare avancerade prestandarekommendationer via Azure Advisor till ditt förfogande, inklusive:
1.  Anpassningsbar cache – vara medveten om att optimera användningen av cache.
2.  Tabelldistribution – avgöra när du ska replikera tabeller för att minska dataförflyttning och öka arbetsbelastningens prestanda. 
3.  TempDB – Förstå när skala och konfigurerar resurs klasser för att minska konkurrensen i tempdb.

Det finns en djupare integrering av måtten i informationslager med [Azure Monitor](https://azure.microsoft.com/blog/enhanced-capabilities-to-monitor-manage-and-integrate-sql-data-warehouse-in-the-azure-portal/) inklusive en förbättrad anpassningsbara Övervakningsdiagrammet för nästan i realtid mått i översiktsbladet. Du behöver inte längre lämna informationslagrets översiktsblad för att få tillgång till måtten i Azure Monitor när du övervakar användning eller utvärderar och använder informationslagerrekommendationer. Det finns dessutom nya mått som är tillgängliga, till exempel tempdb och anpassningsbar cache-användning för att komplettera din prestandarekommendationer.

## <a name="advanced-tuning-with-integrated-advisors"></a>Avancerade justering med integrerad rådgivare
Avancerade inställningen för Azure SQL Data Warehouse (SQL DW) bara fick enklare med ytterligare data warehouse rekommendationer och mått och en ny utformning av bladet översikt över portal som ger en integrerad upplevelse med Azure Advisor och Azure Monitor.

## <a name="accelerated-database-recovery-adr"></a>Accelererad databasåterställning (ADR)
Azure SQL Data Warehouse Accelerated Database Recovery (ADR) finns nu i offentlig förhandsversion. Regel för automatisk distribution är en ny SQL Server-motorn som avsevärt förbättrar databastillgänglighet, särskilt i förekomsten av långvariga transaktioner genom att göra om den aktuella återställningsprocessen från grunden upp helt. De främsta fördelarna med ADR är snabbt och konsekvent databasåterställning och omedelbar transaktionsåterställning.

## <a name="azure-monitor-diagnostics-logs"></a>Azure Monitor-diagnostikloggar
SQL Data Warehouse (SQL DW) kan nu bättre insikter om analytiska arbetsbelastningar genom att integrera direkt med Azure Monitor diagnostikloggar. Den här nya funktionen kan utvecklare Analysera arbetsbelastning beteende under en längre tid och fatta välgrundade beslut om frågan optimering eller kapacitet management. Nu har vi lagt en extern loggning process via [diagnostikloggar för Azure Monitor](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json#logs) som ger ytterligare insikter om din arbetsbelastning i informationslager. Med ett enda klick för en knapp, du kan nu konfigurera diagnostikloggar för historiksökning prestandafelsökning funktioner med hjälp av [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-queries). Azure Monitor-diagnostikloggar stöder anpassningsbara kvarhållningsperioder genom att spara loggarna till ett lagringskonto i granskningssyfte, möjlighet att strömningsloggar till event hubs nästan i realtid telemetri insikter och möjligheten att analysera loggar med Log Analytics med loggfrågor. Diagnostikloggarna består av telemetriska vyer av ditt informationslager. Vyerna som motsvarar de oftast använda DMV:erna för prestandafelsökning i SQL Data Warehouse. För den här första versionen har vi aktiverat vyer för vyer för dynamisk hantering av följande system:

- [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql)
- [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql)
- [sys.dm_pdw_dms_workers](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql)
- [sys.dm_pdw_waits](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql)
- [sys.dm_pdw_sql_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql)

## <a name="columnstore-memory-management"></a>Columnstore minneshantering
När antalet komprimerade kolumnen store radgrupper ökar, ökar det minne som krävs för att hantera interna kolumnmetadata segment för dessa radgrupper.  Därför kan försämra prestanda för frågor och frågor som körs mot vissa av Columnstore-dynamiska hanteringsvyer (DMV).  Förbättringar har gjorts i den här versionen att optimera storleken på interna metadata för dessa fall, vilket leder till bättre upplevelse och prestanda för sådana frågor. 

## <a name="azure-data-lake-storage-gen2-integration-ga"></a>Integrering med Azure Data Lake Storage Gen2 (GA)
Azure SQL Data Warehouse (SQL DW) har nu integrering med Azure Data Lake Storage Gen2. Kunder kan nu läsa in data med externa tabeller från ABFS till SQL DW. Den här funktionen gör det möjligt för kunder att integrera med sina datasjöar i Data Lake Storage Gen2. 

## <a name="bug-fixes"></a>Felkorrigeringar

| Rubrik | Beskrivning |
|:---|:---|
| **CETAS Parquet-fel i små resursklasser på informationslager DW2000 och mycket mer** | Den här snabbkorrigeringen identifierar en null-referens i den Skapa extern tabell som att Parquet kodsökvägar. |
|**Identity-kolumnvärdet kan förlora i CTAS åtgärder** | Värdet för en identifiera kolumnen bevaras inte när CTASed till en annan tabell. Rapporteras i en blogg: [ https://blog.westmonroepartners.com/azure-sql-dw-identity-column-bugs/ ](https://blog.westmonroepartners.com/azure-sql-dw-identity-column-bugs/). |
| **Internt fel i vissa fall när en session avslutas när en fråga körs fortfarande** | Den här korrigeringen utlöses en InvalidOperationException om en session avslutas när frågan körs. |
| **(Distribuerad i November 2018) Kunden drabbats av en icke-optimal prestanda vid försök att läsa in flera små filer från ADLS (Gen1) med Polybase.** | Skapa en systemprestanda har flaskhals eftersom under AAD Säkerhetsverifieringen för token. Problem med prestanda har du minimera genom att aktivera cachelagring av säkerhetstoken. |


## <a name="next-steps"></a>Nästa steg
Nu när du vet lite om SQL Data Warehouse, lär du dig hur du snabbt [skapa ett SQL Data Warehouse][create a SQL Data Warehouse]. Om du inte har erfarenhet av Azure kan [Azure-ordlistan][Azure glossary] vara till hjälp om du stöter på ny terminologi. Eller så kan du se över några av de övriga SQL Data Warehouse-resurserna.  

* [Kundernas framgångsberättelser]
* [Bloggar]
* [Funktionsbegäranden]
* [Videoklipp]
* [Customer Advisory Team-bloggar]
* [Stack Overflow-forum]
* [Twitter]


[Bloggar]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Customer Advisory Team-bloggar]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Kundernas framgångsberättelser]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Funktionsbegäranden]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Stack Overflow-forum]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videoklipp]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md
