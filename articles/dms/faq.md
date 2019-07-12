---
title: Vanliga frågor och svar om hur du använder Azure Database Migration Service | Microsoft Docs
description: Läs vanliga frågor och svar om hur du använder Azure Database Migration Service för att utföra migrering av databaser.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 07/10/2019
ms.openlocfilehash: 5077539f6f80784f865bd4c1b52e3b4c147107ed
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2019
ms.locfileid: "67717993"
---
# <a name="faq-about-using-azure-database-migration-service"></a>Vanliga frågor och svar om hur du använder Azure Database Migration Service

Den här artikeln innehåller vanliga frågor om hur du använder Azure Database Migration Service tillsammans med tillhörande svar.

## <a name="overview"></a>Översikt

**FRÅGOR OCH. Vad är Azure Database Migration Service?**
Azure Database Migration Service är en fullständigt hanterad tjänst som utformats för att aktivera sömlös migrering från flera databaskällor till Azure-Dataplattformar med minimal avbrottstid. Tjänsten är för närvarande allmänt tillgängliga, med pågående arbete fokuserar på:

* Tillförlitlighet och prestanda.
* Iterativ tillägg av källa / mål-par.
* Fortsatt investeringar i problemfri migrering.

**FRÅGOR OCH. Vilken källa/mål-par Azure Database Migration Service stöder för närvarande?**
Tjänsten stöder för närvarande en mängd källa/mål-par och Migreringsscenarier. En fullständig lista över statusen för varje tillgänglig Migreringsscenario finns i artikeln [Status för migreringsscenarier som stöds av Azure Database Migration Service](https://docs.microsoft.com/azure/dms/resource-scenario-status).

Andra Migreringsscenarier är i förhandsversion och kräver att skicka en utnämning via webbplatsen DMS-förhandsversion. En fullständig lista över scenarier i en förhandsversion och för att registrera dig för att delta i ett av dessa erbjudanden, finns det [plats DMS förhandsversion](https://aka.ms/dms-preview/).

**FRÅGOR OCH. Vilka versioner av SQL Server stöder Azure Database Migration Service som en källa?**
När du migrerar från SQL Server, är SQL Server 2005 till SQL Server 2017 i källor som stöds för Azure Database Migration Service.

**F: När du använder Azure Database Migration Service, vad är skillnaden mellan ett offline och en online-migrering?**
Du kan använda Azure Database Migration Service för att utföra online och offline-migrering. Med en *offline* migrering, programmet avbrottstiden startar när migreringen startar. Med en *online* migrering, avbrottstiden är begränsad till dess att klippa ut över i slutet av migreringen. Vi rekommenderar att du testar en offlinemigrering för att avgöra om det frånkopplade tillståndet är godtagbart. Om det inte är det kan du utföra en onlinemigrering.

> [!NOTE]
> Med hjälp av Azure Database Migration Service för att utföra en online-migrering måste du skapa en instans baserat på premiumprisnivån. Mer information finns i på [prissättningssidan](https://azure.microsoft.com/pricing/details/database-migration/) för Azure Database Migration Service.

**FRÅGOR OCH. Hur Azure Database Migration Service jämfört med andra Microsoft-databasmigreringsverktyg, till exempel Database Migration Assistant (DMA) eller SQL Server Migration Assistant (SSMA)?**
Azure Database Migration Service är den bästa metoden för Databasmigreringen till Microsoft Azure i skala. Mer information om hur Azure Database Migration Service Jämför med andra Microsoft-databasen Migreringsverktyg och rekommendationer om hur du använder tjänsten för olika scenarier finns i bloggen bokföring [särskiljer Microsofts Databasmigrering Verktyg och tjänster](https://blogs.msdn.microsoft.com/datamigration/2017/10/13/differentiating-microsofts-database-migration-tools-and-services/).

**FRÅGOR OCH. Hur Azure Database Migration Service jämfört med Azure Migrate-erbjudandet?**
Azure Migrate hjälper till med migrering av lokala virtuella datorer till Azure IaaS. Tjänsten utvärderar migreringslämplighet och prestandabaserad storleksändring och den tillhandahåller kostnadsuppskattningar för att köra dina lokala virtuella datorer i Azure. Azure Migrate är användbart för lift and shift-migrering av lokala VM-baserade arbetsbelastningar till Azure IaaS-VM. Men till skillnad från Azure Database Migration Service, Azure Migrate är inte en specialiserad databasmigreringstjänsten erbjudande för Azure PaaS relationsdatabas plattformar, till exempel Azure SQL Database eller Azure SQL Database Managed Instance.

## <a name="setup"></a>Konfiguration

**FRÅGOR OCH. Vilka är kraven för att använda Azure Database Migration Service?**
Det finns flera förutsättningar som krävs för att säkerställa att Azure Database Migration Service körs smidigt när du utför migrering av databaser. Några av förutsättningarna gäller för alla scenarier (källa / mål-par) som stöds av tjänsten, medan andra krav är unika för ett specifikt scenario.

Krav för Azure Database Migration Service som är gemensamma för alla migreringsscenarier som stöds är behovet av att:

* Skapa ett virtuellt nätverk för Azure Database Migration Service med hjälp av Azure Resource Manager-distributionsmodellen, som tillhandahåller plats-till-plats-anslutning till dina lokala källservrar genom att använda antingen [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) eller [ VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
* Se till att Azure Virtual Network (VNet) nätverkssäkerhetsgruppsregler inte blockera följande kommunikationsportar 443, 53, 9354, 445, 12000. Mer information om Azure VNet NSG-trafikfiltrering finns i artikeln [filtrera nätverkstrafik med nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* När du använder en brandväggsinstallation framför din källdatabaserna, kan du behöva lägga till brandväggsregler som tillåter Azure Database Migration Service åtkomst till källdatabaserna för migrering.

En lista över alla förutsättningar som krävs för att tävla specifika migreringsscenarier som använder Azure Database Migration Service, finns i de relaterade guider i Azure Database Migration Service [dokumentation](https://docs.microsoft.com/azure/dms/dms-overview) på docs.microsoft.com.

**FRÅGOR OCH. Hur hittar jag IP-adressen för Azure Database Migration Service så att jag kan skapa en lista över tillåtna för brandväggsregler som används för att komma åt min källdatabasen för migrering?**
Du kan behöva lägga till brandväggsregler som tillåter Azure Database Migration Service får tillgång till källdatabasen för migrering. IP-adressen för tjänsten är dynamisk, men om du använder Express Route kan den här adressen tilldelas privat av företagets nätverk. Det enklaste sättet att identifiera lämplig IP-adress är att titta i samma resursgrupp som ditt etablerade Azure Database Migration Service-resurs att hitta det associerade nätverksgränssnittet. Vanligtvis namnet på nätverksgränssnittet resursen börjar med prefixet NIC och följt av ett tecken och nummerserie, till exempel NIC-jj6tnztnmarpsskr82rbndyp. Du kan se IP-adressen som måste tas med i listan över tillåtna på Resursöversikt Azure-portalen sidan genom att välja det här gränssnittet nätverksresurs.

Du kan också behöva innehåller källan för porten som SQL Server lyssnar på listan över tillåtna. Som standard är den port 1433, men källan SQL Server kan konfigureras för att lyssna på samt andra portar. Du måste i så fall inkluderar dessa portar på listan Tillåt. Du kan bestämma den port som SQL Server lyssnar på med hjälp av en dynamisk hanteringsvy-fråga.

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

**FRÅGOR OCH. Hur konfigurerar jag ett Azure Virtual Network?**
När flera Microsoft-självstudier som kan vägleder dig genom processen med att konfigurera ett virtuellt Azure nätverk, den officiella dokumentationen visas i artikeln [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

## <a name="usage"></a>Användning

**FRÅGOR OCH. Vad är en sammanfattning av de steg som krävs för att använda Azure Database Migration Service för att utföra en Databasmigrering?**
Under en typisk, enkel databas-migrering, du:

1. Skapa en mål-databaserna.
2. Utvärdera dina käll-databaser.
    * Utvärdera dina befintliga databaser med hjälp av för homogen migreringar [DMA](https://www.microsoft.com/download/details.aspx?id=53595).
    * Utvärdera dina befintliga databaser med för heterogena migreringar (från fullständig källor) [SSMA](https://aka.ms/get-ssma). Du kan också använda SSMA för att konvertera databasobjekt och migrera schemat till din målplattform.
3. Skapa en instans av Azure Database Migration Service.
4. Skapa ett migreringsprojekt att ange källdatabaserna, mål-databaser och tabeller som ska migreras.
5. Starta full belastning.
6. Välj efterföljande verifieringen.
7. Utföra en manuell övergång för produktionsmiljön till den nya molnbaserade databasen.

## <a name="troubleshooting-and-optimization"></a>Felsökning och optimering

**FRÅGOR OCH. Jag ställer in ett migreringsprojekt i DMS och jag har problem med att ansluta till min källdatabasen. Vad ska jag göra?**
Om du har problem med att ansluta till databasen källsystemet när den arbetade om migrering kan du skapa en virtuell dator i det virtuella nätverket som du konfigurerar din DMS-instans. I den virtuella datorn, bör du kunna köra ett test på connect, till exempel använda en UDL-fil för att testa en anslutning till SQL Server eller hämta Robo 3T för att testa MongoDB-anslutningar. Om Anslutningstestet lyckas, bör inte du har ett problem med att ansluta till din källdatabas. Kontakta din nätverksadministratör om Anslutningstestet inte lyckas.

**FRÅGOR OCH. Varför är min Azure Database Migration Service inte tillgänglig eller är stoppad?**
Om användaren uttryckligen slutar Azure Database Migration Service (DMS) eller om tjänsten är inaktiv under en period på 24 timmar, kommer tjänsten vara i ett stoppat eller automatiskt pausläge. I båda fallen kan är tjänsten inte tillgänglig och en Stoppad status.  Starta om tjänsten om du vill återuppta aktiva migreringar.

**FRÅGOR OCH. Finns det några rekommendationer för att optimera prestanda för Azure Database Migration Service?**
Du kan göra några saker att snabba upp din Databasmigrering med hjälp av tjänsten:

* Använda flera CPU allmänt syfte Pricing Tier när du skapar din tjänstinstans för att tjänsten ska kunna utnyttja flera virtuella processorer för parallellisering och dataöverföringen snabbare.
* Tillfälligt kan skala upp din Azure SQL Database-målinstans som Premium-nivån SKU under data migrerades att minimera Azure SQL Database begränsning som påverka aktiviteter för överföring av data när du använder på lägre nivå SKU: er.

## <a name="next-steps"></a>Nästa steg

En översikt över Azure Database Migration Service och regional tillgänglighet finns i artikeln [vad är Azure Database Migration Service](dms-overview.md).
