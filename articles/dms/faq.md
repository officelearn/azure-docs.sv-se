---
title: Vanliga frågor och svar – Tjänsten för migrering av Azure-databas
description: Vanliga frågor och svar om hur du använder Azure Database Migration Service för att utföra databasmigreringar.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: a664f12843585ac7524cf8d51aef156d15d32504
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650989"
---
# <a name="faq-about-using-azure-database-migration-service"></a>Vanliga frågor och svar om hur du använder Azure Database Migration Service

I den här artikeln visas vanliga frågor om hur du använder Azure Database Migration Service tillsammans med relaterade svar.

## <a name="overview"></a>Översikt

**F. Vad är Azure Database Migration Service?**
Azure Database Migration Service är en fullständigt hanterad tjänst som utformats för att möjliggöra sömlösa migreringar från flera databaskällor till Azure Data-plattformar med minimal stilleståndstid. Tjänsten är för närvarande i allmän tillgänglighet, med pågående utvecklingsarbete inriktade på:

* Tillförlitlighet och prestanda.
* Iterativa tillägg av källa-mål par.
* Fortsatta investeringar i friktionsfria migrationer.

**F. Vilka käll-/målpar stöder Azure Database Migration Service för närvarande?**
Tjänsten stöder för närvarande en mängd olika käll-/målpar eller migreringsscenarier. En fullständig lista över status för varje tillgängligt migreringsscenario finns i artikeln [Status för migreringsscenarier som stöds av Azure Database Migration Service](https://docs.microsoft.com/azure/dms/resource-scenario-status).

Andra migreringsscenarier är i förhandsversion och kräver att du skickar in en nominering via webbplatsen DMS Preview. En fullständig lista över scenarierna i förhandsversionen och registrera dig för att delta i något av dessa erbjudanden finns på [webbplatsen FÖR DMS Preview](https://aka.ms/dms-preview/).

**F. Vilka versioner av SQL Server stöder Azure Database Migration Service som källa?**
När du migrerar från SQL Server är källor som stöds för Azure Database Migration Service SQL Server 2005 via SQL Server 2019.

**F: Vad är skillnaden mellan en offline- och onlinemigrering när du använder Azure Database Migration Service?**
Du kan använda Azure Database Migration Service för att utföra offline- och onlinemigreringar. Med *offline* en offlinemigrering startar programavvikningen när migreringen startar. Med *online* en onlinemigrering är driftstopp begränsat till tiden för att minska i slutet av migreringen. Vi rekommenderar att du testar en offlinemigrering för att avgöra om det frånkopplade tillståndet är godtagbart. Om det inte är det kan du utföra en onlinemigrering.

> [!NOTE]
> För att använda Azure Database Migration Service för att utföra en onlinemigrering måste du skapa en instans baserat på premiumprisnivån. Mer information finns i på [prissättningssidan](https://azure.microsoft.com/pricing/details/database-migration/) för Azure Database Migration Service.

**F. Hur jämför Azure Database Migration Service med andra Microsoft-databasmigreringsverktyg, till exempel DMA (Database Migration Assistant) eller SQL Server Migration Assistant (SSMA)?**
Azure Database Migration Service är den metod som föredras för databasmigrering till Microsoft Azure i stor skala. Mer information om hur Azure Database Migration Service jämför med andra microsoft-verktyg för migrering av databaser och rekommendationer om hur du använder tjänsten för olika scenarier finns i blogginlägget [Differentiera Microsofts verktyg och tjänster för databasmigrering](https://techcommunity.microsoft.com/t5/microsoft-data-migration/differentiating-microsoft-s-database-migration-tools-and/ba-p/368529).

**F. Hur jämför Azure Database Migration Service med Azure Migrate-erbjudandet?**
Azure Migrate hjälper till med migrering av lokala virtuella datorer till Azure IaaS. Tjänsten utvärderar migreringsanlighet och prestandabaserad storlek och ger kostnadsuppskattningar för att köra lokala virtuella datorer i Azure. Azure Migrate är användbart för lift-and-shift-migreringar av lokala VM-baserade arbetsbelastningar till virtuella Azure IaaS-datorer. Till skillnad från Azure Database Migration Service är Azure Migrate inte ett specialiserat databasmigreringstjänst som erbjuder Azure PaaS relationsdatabasplattformar som Azure SQL Database eller Azure SQL Database Managed Instance.

## <a name="setup"></a>Installation

**F. Vilka är förutsättningarna för att använda Azure Database Migration Service?**
Det finns flera förutsättningar som krävs för att säkerställa att Azure Database Migration Service fungerar smidigt när du utför databasmigreringar. Vissa av förutsättningarna gäller i alla scenarier (källmålpar) som stöds av tjänsten, medan andra förutsättningar är unika för ett visst scenario.

Azure Database Migration Service förutsättningar som är vanliga i alla scenarier för migrering som stöds inkluderar behovet av att:

* Skapa en Microsoft Azure Virtual Network for Azure Database Migration Service med hjälp av distributionsmodellen för Azure Resource Manager, som tillhandahåller anslutning från plats till plats till dina lokala källservrar med hjälp av [Antingen ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) eller [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
* Kontrollera att reglerna för nätverkssäkerhetsgruppen för virtuellt nätverk inte blockerar följande kommunikationsportar 443, 53, 9354, 445, 12000. Mer information om filtrering av NSG-trafik i det virtuella nätverket finns i artikeln [Filtrera nätverkstrafik med nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* När du använder en brandväggsinstallation framför källdatabasen eller källdatabaserna kan du behöva lägga till brandväggsregler så att Azure Database Migration Service kan komma åt källdatabaserna för migrering.

En lista över alla förutsättningar som krävs för att konkurrera med specifika migreringsscenarier med hjälp av Azure Database Migration Service finns i relaterade självstudier i [dokumentationen](https://docs.microsoft.com/azure/dms/dms-overview) till Azure Database Migration Service på docs.microsoft.com.

**F. Hur hittar jag IP-adressen för Azure Database Migration Service så att jag kan skapa en tillåt-lista för brandväggsregler som används för att komma åt min källdatabas för migrering?**
Du kan behöva lägga till brandväggsregler som gör att Azure Database Migration Service kan komma åt källdatabasen för migrering. IP-adressen för tjänsten är dynamisk, men om du använder ExpressRoute tilldelas den här adressen privat av företagets nätverk. Det enklaste sättet att identifiera lämplig IP-adress är att titta i samma resursgrupp som din etablerade Azure Database Migration Service-resurs för att hitta det associerade nätverksgränssnittet. Vanligtvis börjar namnet på nätverksgränssnittsresursen med NIC-prefixet och följs av en unik tecken- och nummerserie, till exempel NIC-jj6tnztnmarpsskr82rbndyp. Genom att välja den här nätverksgränssnittsresursen kan du se den IP-adress som måste inkluderas i listan tillåt på Azure-portalsidan för resursöversikt.

Du kan också behöva inkludera portkällan som SQL Server lyssnar på listan över tillåtna. Som standard är det port 1433, men källan SQL Server kan vara konfigurerad för att lyssna på andra portar också. I det här fallet måste du inkludera dessa portar på listan över tillåt också. Du kan bestämma vilken port SQL Server lyssnar på med hjälp av en fråga om dynamisk hanteringsvy:

```sql
    SELECT DISTINCT
        local_tcp_port
    FROM sys.dm_exec_connections
    WHERE local_tcp_port IS NOT NULL
```

Du kan också bestämma vilken port SQL Server lyssnar på genom att fråga SQL Server-felloggen:

```sql
    USE master
    GO
    xp_readerrorlog 0, 1, N'Server is listening on'
    GO
```

**F. Hur konfigurerar jag ett virtuellt Microsoft Azure-nätverk?**
Medan flera Microsoft-självstudier som kan gå igenom processen för att konfigurera ett virtuellt nätverk, visas den officiella dokumentationen i artikeln [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

## <a name="usage"></a>Användning

**F. Vad är en sammanfattning av de steg som krävs för att använda Azure Database Migration Service för att utföra en databasmigrering?**
Under en typisk, enkel databasmigrering kan du:

1. Skapa en måldatabas(er).
2. Utvärdera källdatabaserna.
    * För homogena migreringar bör du bedöma dina befintliga databaser med hjälp av [DMA](https://www.microsoft.com/download/details.aspx?id=53595).
    * För heterogena migreringar (från konkurrerande källor) utvärdera din befintliga databas(er) med [SSMA](https://aka.ms/get-ssma). Du kan också använda SSMA för att konvertera databasobjekt och migrera schemat till målplattformen.
3. Skapa en instans av Azure Database Migration Service.
4. Skapa ett migreringsprojekt som anger källdatabaser, måldatabaser och tabeller som ska migreras.
5. Starta hela lasten.
6. Välj efterföljande validering.
7. Utför en manuell övergång av din produktionsmiljö till den nya molnbaserade databasen.

## <a name="troubleshooting-and-optimization"></a>Felsökning och optimering

**F. Jag ställer in ett migreringsprojekt i DMS, och jag har svårt att ansluta till min källdatabas. Vad ska jag göra?**
Om du har problem med att ansluta till källdatabassystemet när du arbetar med migrering skapar du en virtuell dator i det virtuella nätverk som du konfigurerar DMS-instansen med. I den virtuella datorn bör du kunna köra ett anslutningstest, till exempel använda en UDL-fil för att testa en anslutning till SQL Server eller hämta Robo 3T för att testa MongoDB-anslutningar. Om anslutningstestet lyckas bör du inte ha problem med att ansluta till källdatabasen. Om anslutningstestet inte lyckas kontaktar du nätverksadministratören.

**F. Varför är min Azure Database Migration Service inte tillgänglig eller stoppad?**
Om användaren uttryckligen stoppar Azure Database Migration Service (DMS) eller om tjänsten är inaktiv under en period av 24 timmar, kommer tjänsten att vara i ett stoppat eller automatiskt pausat tillstånd. I varje enskilt fall kommer tjänsten inte att vara tillgänglig och i en stoppad status.  Starta om tjänsten om du vill återuppta aktiva migreringar.

**F. Finns det några rekommendationer för att optimera prestanda för Azure Database Migration Service?**
Du kan göra några saker för att påskynda databasmigrering med hjälp av tjänsten:

* Använd multi CPU General Purpose Pricing Tier när du skapar din tjänstinstans för att tillåta tjänsten att dra nytta av flera virtuella processorer för parallellisering och snabbare dataöverföring.
* Skala tillfälligt upp din Azure SQL Database-målinstans till Premium-nivå SKU under datamigreringsåtgärden för att minimera Azure SQL Database-begränsning som kan påverka dataöverföringsaktiviteter när du använder SKU:er på lägre nivå.

## <a name="next-steps"></a>Nästa steg

En översikt över Azure Database Migration Service och regional tillgänglighet finns i artikeln [Vad är Migreringstjänsten för Azure Database](dms-overview.md).
