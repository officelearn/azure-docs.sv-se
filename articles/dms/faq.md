---
title: "Vanliga frågor och svar om hur du använder tjänsten Azure Database migrering | Microsoft Docs"
description: "Läs vanliga frågor om hur du använder tjänsten Azure Database migrering för att utföra migrering av databasen."
services: database-migration
author: HJToland3
ms.author: jtoland
manager: 
ms.reviewer: 
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 01/25/2018
ms.openlocfilehash: 3c1c259cc58eb1adab39d9c0ca376726b798186e
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="faq-about-using-the-azure-database-migration-service"></a>Vanliga frågor och svar om hur du använder tjänsten Azure Database migrering
Den här artikeln innehåller vanliga frågor om hur du använder tjänsten Azure Database migrering tillsammans med tillhörande svar.

### <a name="q-what-is-azure-database-migration-service"></a>FRÅGOR. Vad är Azure databastjänst för migrering?
Tjänsten Azure Database migrering är en helt hanterad tjänst som utformats för att aktivera sömlös migrering från flera databaskällor till Azure Data plattformar med minimal avbrottstid. Tjänsten är för närvarande i förhandsversion, med utvecklingsarbete fokuserar på:
- Tillförlitlighet och prestanda.
- Iterativ tillägg av käll-och mål-par.
- Fortsatt investeringar i friktion problemfri migrering.

### <a name="q-what-source-target-pairs-does-the-azure-database-migration-service-currently-support"></a>FRÅGOR. Vilken källa mål par tjänsten Azure Database migrering stöder för närvarande?
Tjänsten i Public Preview för tillfället stöder migrering från SQL Server till Azure SQL Database och du kan gå till Azure-portalen nu att börja använda tjänsten Azure Database migrering för det här scenariot. Andra källa mål-par, till exempel SQL Server till hanterade Azure SQL Database-instans och Oracle till Azure SQL Database är tillgängliga via en begränsad privat förhandsgranskning. Registrera dig för en möjlighet att delta i begränsad privat förhandsgranskning av dessa scenarier [här](https://sqldatabase-migrationpreview.azurewebsites.net/).

### <a name="q-how-does-the-azure-database-migration-service-compare-to-other-microsoft-database-migration-tools-such-as-the-database-migration-assistant-dma-or-sql-server-migration-assistant-ssma"></a>FRÅGOR. Hur tjänsten Azure Database migrering Jämför med andra Microsoft-databasen Migreringsverktyg som databasen migrering Assistant (DMA) eller SQL Server Migration Assistant (SSMA)?
Tjänsten Azure Database migrering är den bästa metoden för Databasmigrering till Microsoft Azure i större skala. Mer information om hur tjänsten Azure Database migrering Jämför med andra Microsoft-databasen Migreringsverktyg och rekommendationer om hur tjänsten för olika scenarier finns i bloggen bokföring [skilja Microsoft-databas Migreringsverktyg och tjänster](https://blogs.msdn.microsoft.com/datamigration/2017/10/13/differentiating-microsofts-database-migration-tools-and-services/).

### <a name="q-how-does-the-azure-database-migration-service-compare-to-the-azure-migrate-offering"></a>FRÅGOR. Hur jämför tjänsten Azure Database migrering till Azure migrera erbjudande?
Tjänsten Azure migrera hjälper till med migrering av lokala virtuella datorer till Azure IaaS. Tjänsten utvärderar migrering lämplighet och prestandabaserad storlek och ger den uppskattade kostnaden för din lokala virtuella datorer som körs i Azure. Azure migrera är användbart för lift och SKIFT migrering av lokala VM-baserade arbetsbelastningar till Azure IaaS-VM. Men till skillnad från tjänsten Azure Database migrering Azure migrera är inte en särskild databas migrering tjänsterbjudande för Azure PaaS relationsdatabas plattformar, till exempel Azure SQL Database eller SQL Azure eller hanterad Azure SQL Database-instans.

### <a name="q-what-is-a-summary-of-the-steps-required-to-use-the-azure-database-migration-service-to-perform-a-database-migration"></a>FRÅGOR. Vad är en översikt över de steg som krävs för att använda tjänsten Azure Database migrering du utför en Databasmigrering?
Vid migrering vanliga, enkel databas måste du:
1.  Skapa en mål-databaserna.
2.  Migrera databaserna schemat med hjälp av den [databasen Migration Assistant](https://www.microsoft.com/en-us/download/details.aspx?id=53595).
3.  Skapa en instans av tjänsten Azure Database migrering.
4.  Skapa ett migreringsprojekt att ange källa databaser, mål-databaser och tabeller för att migrera.
5.  Initiera full belastning.
6.  Välj efterföljande verifieringen.
7.  Utföra en manuell övergång för produktionsmiljön till den nya molnbaserade databasen. 

### <a name="q-what-are-the-prerequisites-for-using-the-azure-database-migration-service"></a>FRÅGOR. Vilka är kraven för att använda tjänsten Azure Database migrering?
Det finns flera förutsättningar som krävs för att se till att tjänsten Azure Database migrering smidigt körs när du utför en migrering av databasen. Vissa krav tillämpa över alla scenarier (källa mål par) som stöds av tjänsten, medan andra förutsättningar är unika för ett specifikt scenario.
Krav för Azure Migreringstjänst för databasen som är gemensamma för alla migreringsscenarier som stöds är behovet av att:
- Skapa ett virtuellt nätverk för tjänsten Azure Database migrering med hjälp av Azure Resource Manager distributionsmodell, som ger plats-till-plats-anslutning till din lokala källservrar genom att använda antingen [ExpressRoute](https://docs.microsoft.com/en-us/azure/expressroute/expressroute-introduction) eller [VPN](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Se till att din Azure virtuella nätverk (VNET) Nätverkssäkerhetsgruppen regler gör inte blockera följande meddelande portarna 443, 53, 9354, 445, 12000. Mer information om Azure VNET NSG trafikfiltrering finns i artikeln [filtrera nätverkstrafik med nätverkssäkerhetsgrupper](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-nsg).
- När du använder en brandväggsinstallation framför käll-databaserna, kanske du måste lägga till brandväggsregler som tillåter tjänsten Azure Database migrering till databaserna källa för migrering.
 
En lista över alla förutsättningar för konkurrerar specifika migreringsscenarier som använder tjänsten Azure Database migrering, finns relaterade självstudiekurser i tjänsten Azure Database migrering [dokumentationen](https://docs.microsoft.com/en-us/azure/dms/dms-overview) på docs.microsoft.com.

### <a name="q-how-do-i-find-the-ip-address-for-the-azure-database-migration-service-so-that-i-can-create-an-allow-list-for-the-firewall-rules-used-to-access-my-source-database-for-migration"></a>FRÅGOR. Hur hittar jag den IP-adressen för tjänsten Azure Database migreringen så att jag kan skapa en lista över tillåtna för brandväggsregler som används för att komma åt min källdatabasen för migrering?
Du kan behöva lägga till brandväggsregler som tillåter tjänsten Azure databas migrering att komma åt källdatabasen för migrering. IP-adressen för tjänsten är dynamiska, men om du använder Express Route kan den här adressen tilldelas privat av företagets nätverk. Det enklaste sättet att identifiera rätt IP-adress som det är att söka i samma resursgrupp som din etablerade Azure databastjänsten migrering resurs att hitta det associerade nätverksgränssnitt. Vanligtvis namnet på resursen nätverksgränssnittet börjar med prefixet NIC och följt av ett tecken och antalet sekvens, exempel NIC-jj6tnztnmarpsskr82rbndyp. Du kan se IP-adressen som ska tas med i listan över tillåtna på Resursöversikt Azure Portalsida genom att välja det här gränssnittet nätverksresurs.

Du kan också behöva inkludera porten källa som SQL Server lyssnar på listan över tillåtna. Det är port 1433 som standard, men källan SQL Server kan konfigureras för att lyssna på andra portar samt. Du måste i så fall inkluderar dessa portar på listan Tillåt. Du kan bestämma porten som SQL Server lyssnar på med hjälp av en fråga med dynamiska Hanteringsvyn.

```sql
    SELECT DISTINCT 
        local_tcp_port 
    FROM sys.dm_exec_connections 
    WHERE local_tcp_port IS NOT NULL
```
Du kan också bestämma porten som SQL Server lyssnar genom att fråga SQL Server-felloggen.

```sql
    USE master
    GO
    xp_readerrorlog 0, 1, N'Server is listening on' 
    GO
```

### <a name="q-are-there-any-recommendations-for-optimizing-the-performance-of-the-azure-database-migration-service"></a>FRÅGOR. Finns det några rekommendationer för att optimera prestanda för tjänsten Azure Database migrering?
Du kan göra några saker att snabba upp din Databasmigrering med hjälp av tjänsten:
- Använda flera CPU allmänna ändamål prisnivån när du skapar din tjänstinstansen så att tjänsten kan dra nytta av flera vCPUs för parallellisering och snabbare dataöverföring.
- Tillfälligt kan skala upp din Azure SQL Database målinstansen till Premium-nivån SKU under data migrerades att minimera Azure SQL Database begränsning som påverka aktiviteter för överföring av data när du använder på lägre nivå SKU: er.

### <a name="q-how-do-i-set-up-an-azure-virtual-network"></a>FRÅGOR. Hur ställer jag in ett Azure Virtual Network?
När flera Microsoft-självstudier som kan vägleder dig genom processen för att skapa ett Azure VNET, den officiella dokumentationen visas i artikeln [Azure Virtual Network](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-overview).

### <a name="q-why-is-my-azure-database-migration-service-unavailable-or-stopped"></a>FRÅGOR. Varför är min Azure Database migrering-tjänsten inte tillgänglig eller är stoppad?
Om användaren uttryckligen slutar Azure databas migrering Service (DMS) eller om tjänsten är inaktiv under en period på 24 timmar, vara i ett stoppat tjänsten eller automatiskt pausläge. I varje fall blir tjänsten otillgänglig och Stoppad status.  Starta om tjänsten om du vill återuppta aktiva migreringar.

### <a name="q-where-can-i-leave-feedback-about-the-azure-database-migration-service"></a>FRÅGOR. Var kan jag lämna feedback om tjänsten Azure Database migrering?
Vi vill gärna höra av dig. Skicka feedback och / idéer har om tjänsten Azure Database migrering via-feedback [här](https://feedback.azure.com/forums/906100-azure-database-migration-service).

## <a name="next-steps"></a>Nästa steg
En översikt över Azure databastjänst för migrering och regional tillgänglighet under förhandsversion, finns i artikeln [vad är Azure databas migrering Service Preview](dms-overview.md). 