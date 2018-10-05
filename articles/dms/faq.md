---
title: Vanliga frågor och svar om hur du använder Azure Database Migration Service | Microsoft Docs
description: Läs vanliga frågor och svar om hur du använder Azure Database Migration Service för att utföra migrering av databaser.
services: database-migration
author: HJToland3
ms.author: rajpo
manager: ''
ms.reviewer: ''
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 06/19/2018
ms.openlocfilehash: 08158843d0dfb3e5eb0635b5f5b5d015938d895e
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2018
ms.locfileid: "48804420"
---
# <a name="faq-about-using-the-azure-database-migration-service"></a>Vanliga frågor och svar om hur du använder Azure Database Migration Service
Den här artikeln innehåller vanliga frågor om hur du använder Azure Database Migration Service tillsammans med tillhörande svar.

### <a name="q-what-is-azure-database-migration-service"></a>F. Vad är Azure Database Migration Service?
Azure Database Migration Service är en fullständigt hanterad tjänst som utformats för att aktivera sömlös migrering från flera databaskällor till Azure-Dataplattformar med minimal avbrottstid. Tjänsten är för närvarande allmänt tillgänglig med pågående arbete fokuserar på:
- Tillförlitlighet och prestanda.
- Iterativ tillägg av källa / mål-par.
- Fortsatt investeringar i problemfri migrering.

### <a name="q-what-source-target-pairs-does-the-azure-database-migration-service-currently-support"></a>F. Vilken källa / mål-par Azure Database Migration Service stöder för närvarande?
Tjänsten stöder för närvarande migreringar från SQL Server till Azure SQL Database och du kan gå till Azure-portalen nu att börja använda Azure Database Migration Service för det här scenariot. Andra källa / mål-par, som t.ex Oracle till Azure SQL Database är tillgängliga via en begränsad privat förhandsversion. Registrera dig för en möjlighet att delta i begränsad privat förhandsgranskning av dessa scenarier [här](https://aka.ms/dms-preview/).

### <a name="q-how-does-the-azure-database-migration-service-compare-to-other-microsoft-database-migration-tools-such-as-the-database-migration-assistant-dma-or-sql-server-migration-assistant-ssma"></a>F. Hur Azure Database Migration Service jämfört med andra Microsoft-databasmigreringsverktyg, till exempel Database Migration Assistant (DMA) eller SQL Server Migration Assistant (SSMA)?
Azure Database Migration Service är den bästa metoden för Databasmigreringen till Microsoft Azure i skala. Mer information om hur Azure Database Migration Service Jämför med andra Microsoft-databasen Migreringsverktyg och rekommendationer om hur du använder tjänsten för olika scenarier finns i bloggen bokföring [särskiljer Microsofts databas Migreringsverktyg och tjänster](https://blogs.msdn.microsoft.com/datamigration/2017/10/13/differentiating-microsofts-database-migration-tools-and-services/).

### <a name="q-how-does-the-azure-database-migration-service-compare-to-the-azure-migrate-offering"></a>F. Hur Azure Database Migration Service jämfört med Azure Migrate-erbjudandet?
Tjänsten Azure Migrate hjälper till med migrering av lokala virtuella datorer till Azure IaaS. Tjänsten utvärderar migreringslämplighet och prestandabaserad storleksändring och den tillhandahåller kostnadsuppskattningar för att köra dina lokala virtuella datorer i Azure. Azure Migrate är användbart för lift and shift-migrering av lokala VM-baserade arbetsbelastningar till Azure IaaS-VM. Men till skillnad från Azure Database Migration Service, Azure Migrate är inte en specialiserad databasmigreringstjänsten erbjudande för Azure PaaS relationsdatabas plattformar, till exempel Azure SQL Database eller SQL Azure eller Azure SQL Database Managed Instance.

### <a name="q-what-versions-of-sql-server-does-the-azure-database-migration-service-support-as-a-source"></a>F. Vilka versioner av SQL Server stöder Azure Database Migration Service som en källa?
När du migrerar från SQL Server stöder SQL Server 2005 till SQL Server 2017 i Azure Database Migration Service.

### <a name="q-what-is-a-summary-of-the-steps-required-to-use-the-azure-database-migration-service-to-perform-a-database-migration"></a>F. Vad är en sammanfattning av de steg som krävs för att använda Azure Database Migration Service för att utföra en Databasmigrering?
Under en typisk, enkel databas-migrering, du:
1.  Skapa en mål-databaserna.
2.  Migrera databaser schemat genom att använda den [Database Migration Assistant](https://www.microsoft.com/en-us/download/details.aspx?id=53595).
3.  Skapa en instans av Azure Database Migration Service.
4.  Skapa ett migreringsprojekt att ange källdatabaserna, mål-databaser och tabeller som ska migreras.
5.  Initiera full belastning.
6.  Välj efterföljande verifieringen.
7.  Utföra en manuell övergång för produktionsmiljön till den nya molnbaserade databasen. 

### <a name="q-what-are-the-prerequisites-for-using-the-azure-database-migration-service"></a>F. Vilka är kraven för att använda Azure Database Migration Service?
Det finns flera förutsättningar som krävs för att säkerställa att Azure Database Migration Service körs smidigt när du utför migrering av databaser. Några av förutsättningarna gäller för alla scenarier (källa / mål-par) som stöds av tjänsten, medan andra krav är unika för ett specifikt scenario.
Krav för Azure Database Migration Service som är gemensamma för alla migreringsscenarier som stöds är behovet av att:
- Skapa virtuellt nätverk för Azure Database Migration Service genom att använda Azure Resource Manager-distributionsmodellen, som ger plats-till-plats-anslutning för dina lokala källservrar genom att använda [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) eller [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Kontrollera att dina regler för nätverkssäkerhetsgrupp (NSG) för Azure Virtual Network (VNET) inte blockerar följande kommunikationsportar: 443, 53, 9354, 445, 12000. Mer information om trafikfiltrering för Azure VNET NSG finns i artikeln om att [filtrera nätverkstrafik med nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- När du använder en brandväggsinstallation framför dina källdatabaser kanske du måste lägga till brandväggsregler för att tillåta Azure Database Migration Service att komma åt källdatabaserna för migrering.
 
En lista över alla förutsättningar som krävs för att tävla specifika migreringsscenarier som använder Azure Database Migration Service, finns i de relaterade guider i Azure Database Migration Service [dokumentation](https://docs.microsoft.com/azure/dms/dms-overview) på docs.microsoft.com.

### <a name="q-how-do-i-find-the-ip-address-for-the-azure-database-migration-service-so-that-i-can-create-an-allow-list-for-the-firewall-rules-used-to-access-my-source-database-for-migration"></a>F. Hur hittar jag IP-adressen för Azure Database Migration Service så att jag kan skapa en lista över tillåtna för brandväggsregler som används för att komma åt min källdatabasen för migrering?
Du kan behöva lägga till brandväggsregler som tillåter Azure Database Migration Service får tillgång till källdatabasen för migrering. IP-adressen för tjänsten är dynamisk, men om du använder Express Route kan den här adressen tilldelas privat av företagets nätverk. Det enklaste sättet att identifiera lämplig IP-adress som det är att titta i samma resursgrupp som ditt etablerade Azure Database Migration Service-resurs att hitta det associerade nätverksgränssnittet. Vanligtvis namnet på nätverksgränssnittet resursen börjar med prefixet NIC och följt av ett tecken och nummerserie, exempel NIC-jj6tnztnmarpsskr82rbndyp. Du kan se IP-adressen som måste tas med i listan över tillåtna på Resursöversikt Azure-portalen sidan genom att välja det här gränssnittet nätverksresurs.

Du kan också behöva innehåller källan för porten som SQL Server lyssnar på listan över tillåtna. Det är port 1433 som standard, men SQL Server-källans kan konfigureras för att lyssna på samt andra portar. Du måste i så fall inkluderar dessa portar på listan Tillåt. Du kan bestämma den port som SQL Server lyssnar på med hjälp av en dynamisk hanteringsvy-fråga.

```sql
    SELECT DISTINCT 
        local_tcp_port 
    FROM sys.dm_exec_connections 
    WHERE local_tcp_port IS NOT NULL
```
Du kan också bestämma porten som SQL Server lyssnar genom att fråga i SQL Server-felloggen.

```sql
    USE master
    GO
    xp_readerrorlog 0, 1, N'Server is listening on' 
    GO
```

### <a name="q-are-there-any-recommendations-for-optimizing-the-performance-of-the-azure-database-migration-service"></a>F. Finns det några rekommendationer för att optimera prestanda för Azure Database Migration Service?
Du kan göra några saker att snabba upp din Databasmigrering med hjälp av tjänsten:
- Använda flera CPU allmänt syfte Pricing Tier när du skapar din tjänstinstans för att tjänsten ska kunna utnyttja flera virtuella processorer för parallellisering och dataöverföringen snabbare.
- Tillfälligt kan skala upp din Azure SQL Database-målinstans som Premium-nivån SKU under data migrerades att minimera Azure SQL Database begränsning som påverka aktiviteter för överföring av data när du använder på lägre nivå SKU: er.

### <a name="q-how-do-i-set-up-an-azure-virtual-network"></a>F. Hur konfigurerar jag ett Azure Virtual Network?
När flera Microsoft-självstudier som kan vägleder dig genom processen med att konfigurera ett virtuellt Azure nätverk, den officiella dokumentationen visas i artikeln [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

### <a name="q-why-is-my-azure-database-migration-service-unavailable-or-stopped"></a>F. Varför är min Azure Database Migration Service inte tillgänglig eller är stoppad?
Om användaren uttryckligen slutar Azure Database Migration Service (DMS) eller om tjänsten är inaktiv under en period på 24 timmar, kommer tjänsten vara i ett stoppat eller automatiskt pausläge. I båda fallen kan är tjänsten inte tillgänglig och en Stoppad status.  Starta om tjänsten om du vill återuppta aktiva migreringar.

### <a name="q-where-can-i-leave-feedback-about-the-azure-database-migration-service"></a>F. Var kan jag lämna feedback om Azure Database Migration Service?
Vi vill gärna höra från dig. Skicka feedback och / idéer som du har om Azure Database Migration Service via User Voice [här](https://feedback.azure.com/forums/906100-azure-database-migration-service).

## <a name="next-steps"></a>Nästa steg
En översikt över Azure Database Migration Service och regional tillgänglighet finns i artikeln [vad är Azure Database Migration Service](dms-overview.md). 
