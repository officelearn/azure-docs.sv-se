---
title: Instanspooler (förhandsgranskning)
description: I den här artikeln beskrivs Azure SQL Database-instanspooler (förhandsversion).
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab
ms.date: 09/05/2019
ms.openlocfilehash: c1e740fbfa4bf1e8a77a2d9d6060ab39dba7ae7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256216"
---
# <a name="what-are-sql-database-instance-pools-preview"></a>Vad är SQL Database-instanspooler (förhandsversion)?

Instanspooler är en ny resurs i Azure SQL Database som ger ett bekvämt och kostnadseffektivt sätt att migrera mindre SQL-instanser till molnet i stor skala.

Med instanspooler kan du företablera beräkningsresurser enligt dina totala migreringskrav. Du kan sedan distribuera flera enskilda hanterade instanser upp till din företablerade beräkningsnivå. Om du till exempel företablerar 8 virtuella kärnor kan du distribuera två 2 vCore- och en 4-vCore-instanser och sedan migrera databaser till dessa instanser. Innan instanspooler är tillgängliga måste mindre och mindre beräkningsintensiva arbetsbelastningar ofta konsolideras till en större hanterad instans när du migrerar till molnet. Behovet av att migrera grupper av databaser till en stor instans krävde vanligtvis noggrann kapacitetsplanering och resursstyrning, ytterligare säkerhetsöverväganden och lite extra datakonsolideringsarbete på instansnivå.

Dessutom stöder instanspooler inbyggd VNet-integrering så att du kan distribuera flera instanspooler och flera enskilda instanser i samma undernät.


## <a name="key-capabilities-of-instance-pools"></a>Viktiga funktioner för instanspooler

Instanspooler ger följande fördelar:

1. Möjlighet att vara värd för 2 vCore-instanser. *Endast för exempel i instans pooler . \**
2. Förutsägbar och snabb instansdistributionstid (upp till 5 minuter).
3. Minimal IP-adressallokering.

Följande diagram illustrerar en instanspool med flera instanser som distribueras i ett virtuellt nätverksundernät.

![instanspool med flera instanser](./media/sql-database-instance-pools/instance-pools1.png)

Instanspooler möjliggör distribution av flera instanser på samma virtuella dator där den virtuella datorns beräkningsstorlek baseras på det totala antalet virtuella kärnor som allokerats för poolen. Den här arkitekturen gör det möjligt *att partitionera* den virtuella datorn i flera instanser, vilket kan vara valfri storlek som stöds, inklusive 2 virtuella kärnor (2 vCore-instanser är endast tillgängliga för instanser i pooler).

Hanteringsåtgärder i instanser i en pool är mycket snabbare när poolen först har distribuerats. Dessa åtgärder går snabbare eftersom distribution eller tillägg av ett [virtuellt kluster](sql-database-managed-instance-connectivity-architecture.md#high-level-connectivity-architecture) (dedikerad uppsättning virtuella datorer) inte är en del av etableringen av den hanterade instansen.

Eftersom alla instanser i en pool delar samma virtuella dator beror den totala IP-allokeringen inte på antalet instanser som distribueras, vilket är praktiskt för distribution i undernät med ett smalt IP-intervall.

Varje pool har en fast IP-allokering av endast nio IP-adresser (exklusive de fem IP-adresser i undernätet som är reserverade för sina egna behov). Mer information finns i [storlekskrav för undernät för enskilda instanser](sql-database-managed-instance-determine-size-vnet-subnet.md).

## <a name="application-scenarios-for-instance-pools"></a>Programscenarier för till exempel pooler

Följande lista innehåller de huvudsakliga användningsfall där instanspooler bör beaktas:

- Migrering av *en grupp SQL-instanser* samtidigt, där majoriteten är en mindre storlek (till exempel 2 eller 4 virtuella kärnor).
- Scenarier där *förutsägbar och kort instans skapande eller skalning* är viktigt. Till exempel distribution av en ny klient i en SaaS-programmiljö med flera innehavare som kräver funktioner på instansnivå.
- Scenarier där det är viktigt att ha en *fast kostnad* eller *utgiftsgräns.* Till exempel kör delade utvecklingstest- eller demomiljöer med en fast (eller sällan föränderlig) storlek, där du regelbundet distribuerar hanterade instanser när det behövs.
- Scenarier där *minimal IP-adressallokering* i ett VNet-undernät är viktigt. Alla instanser i en pool delar en virtuell dator, så antalet allokerade IP-adresser är lägre än när det gäller enskilda instanser.


## <a name="architecture-of-instance-pools"></a>Förekomstpoolernas arkitektur

Instanspooler har liknande arkitektur som vanliga hanterade instanser (*enstaka instanser*). Om du vill stödja [distributioner inom Virtuella Azure-nätverk (VNets)](../virtual-network/virtual-network-for-azure-services.md#deploy-azure-services-into-virtual-networks) och för att tillhandahålla isolering och säkerhet för kunder, är instanspooler också beroende av [virtuella kluster](sql-database-managed-instance-connectivity-architecture.md#high-level-connectivity-architecture). Virtuella kluster representerar en dedikerad uppsättning isolerade virtuella datorer som distribueras i kundens virtuella nätverksundernät.

Den största skillnaden mellan de två distributionsmodellerna är att instanspooler tillåter flera SQL Server-processdistributioner på samma klientdatornod, som styrs med hjälp av [Windows-jobbobjekt](https://docs.microsoft.com/windows/desktop/ProcThread/job-objects), medan enskilda instanser alltid är ensamma på en nod för virtuella datorer.

I följande diagram visas en instanspool och två enskilda instanser som distribueras i samma undernät och den huvudsakliga arkitektoniska informationen för båda distributionsmodellerna:

![instanspool och två enskilda instanser](./media/sql-database-instance-pools/instance-pools2.png)

Varje instanspool skapar ett separat virtuellt kluster under. Instanser i en pool och enstaka instanser som distribueras i samma undernät delar inte beräkningsresurser som allokerats till SQL Server-processer och gateway-komponenter, vilket säkerställer prestandaförutsägbarhet.

## <a name="instance-pools-resource-limitations"></a>Resursbegränsningar för instanspooler

Det finns flera resursbegränsningar för instanspooler och instanser inuti pooler:

- Instanspooler är endast tillgängliga på Gen5-maskinvara.
- Instanser i en pool har dedikerad CPU och RAM, så det aggregerade antalet virtuella kärnor i alla instanser måste vara mindre än eller lika med antalet virtuella kärnor som allokerats till poolen.
- Alla [instansnivågränser](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) gäller för instanser som skapas i en pool.
- Förutom gränser på instansnivå finns det också två gränser som införts *på instanspoolnivå:*
  - Total lagringsstorlek per pool (8 TB).
  - Totalt antal databaser per pool (100).

Total lagringsallokering och antal databaser i alla instanser måste vara lägre eller lika med de gränser som exponeras av instanspooler.

- Instanspooler stöder 8, 16, 24, 32, 40, 64 och 80 virtuella kärnor.
- Hanterade instanser inuti pooler stöder 2, 4, 8, 16, 24, 32, 40, 64 och 80 virtuella kärnor.
- Hanterade instanser inuti pooler stöder lagringsstorlekar mellan 32 GB och 8 TB, förutom:
  - 2 vCore-instanser stöder storlekar mellan 32 GB och 640 GB
  - 4 vCore-instanser stöder storlekar mellan 32 GB och 2 TB

[Egenskapen servicenivå](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) är associerad med instanspoolresursen så att alla instanser i en pool måste vara samma tjänstnivå som tjänstnivån för poolen. För närvarande är endast tjänstnivån för allmänt ändamål tillgänglig (se följande avsnitt om begränsningar i den aktuella förhandsversionen).

### <a name="public-preview-limitations"></a>Offentliga begränsningar i förhandsversionen

Den offentliga förhandsversionen har följande begränsningar:

- För närvarande är endast tjänstnivån allmänt syfte tillgänglig.
- Instanspooler kan inte skalas under den offentliga förhandsversionen, så noggrann kapacitetsplanering innan distributionen är viktig.
- Azure portal-stöd för till exempel pool skapande och konfiguration är ännu inte tillgänglig. Alla åtgärder i instanspooler stöds endast via PowerShell. Inledande instansdistribution i en förskapad pool stöds också endast via PowerShell. När de har distribuerats till en pool kan hanterade instanser uppdateras med Hjälp av Azure-portalen.
- Hanterade instanser som skapats utanför poolen kan inte flyttas till en befintlig pool och instanser som skapas i en pool kan inte flyttas utanför som en enda instans eller till en annan pool.
- Reserverad instansprissättning (licens ingår eller med Azure Hybrid Benefit) är inte tillgänglig.

## <a name="sql-features-supported"></a>SQL-funktioner som stöds

Instanser som skapas i pooler stöder samma [kompatibilitetsnivåer och funktioner som stöds i enskilda hanterade instanser](sql-database-managed-instance.md#sql-features-supported).

Varje hanterad instans som distribueras i en pool har en separat instans av SQL Agent.

Valfria funktioner eller funktioner som kräver att du väljer specifika värden (t.ex. collation på instansnivå, tidszon, offentlig slutpunkt för datatrafik, redundansgrupper) konfigureras på instansnivå och kan vara olika för varje instans i en pool.

## <a name="performance-considerations"></a>Saker att tänka på gällande prestanda

Även om hanterade instanser i pooler har dedikerade virtuella kärnor och RAM-minne, delar de lokal disk (för tempdb-användning) och nätverksresurser. Det är inte troligt, men det är möjligt att uppleva den *bullriga granneffekten* om flera instanser i poolen har hög resursförbrukning samtidigt. Om du observerar det här beteendet kan du överväga att distribuera dessa instanser till en större pool eller som enstaka instanser.

## <a name="security-considerations"></a>Säkerhetsöverväganden

Eftersom instanser som distribueras i en pool delar samma virtuella dator kan du överväga att inaktivera funktioner som medför högre säkerhetsrisker eller för att kontrollera åtkomstbehörigheter för dessa funktioner. Till exempel CLR integration, native backup och återställning, databas e-post, etc.

## <a name="instance-pool-support-requests"></a>Begäran om instanspoolstöd

Skapa och hantera supportbegäranden för instanspooler i [Azure-portalen](https://portal.azure.com).

Om du har problem med instanspooldistribution (skapande eller borttagning) kontrollerar du att du anger **instanspooler** i **fältet Problemundertyp.**

![instanspooler stöder begäran](./media/sql-database-instance-pools/support-request.png)

Om du har problem relaterade till enskilda instanser eller databaser i en pool bör du skapa en vanlig supportbiljett för hanterade Azure SQL-databasinstanser.

Om du vill skapa större hanterade instansdistributioner (med eller utan instanspooler) kan du behöva skaffa en större regional kvot. Mer information finns i [Begär kvotökningar för Azure SQL Database](quota-increase-request.md). Observera att om du använder instanspooler jämför distributionslogiken den totala vCore-förbrukningen *på poolnivå* med kvoten för att avgöra om du får skapa nya resurser utan att öka kvoten ytterligare.

## <a name="instance-pool-billing"></a>Fakturering av instanspool

Instanspooler tillåter skalning av beräkning och lagring oberoende av varandra. Kunder betalar för beräkning som är associerade med poolresursen mätt i virtuella kärnor och lagring som är associerad med varje instans mätt i gigabyte (de första 32 GB är kostnadsfria för varje instans).

vCore-priset för en pool debiteras oavsett hur många instanser som distribueras i poolen.

För beräkningspriset (mätt i virtuella kärnor) finns två prisalternativ:

  1. *Licens ingår:* Priset på SQL-licenser ingår. Detta gäller för kunder som väljer att inte tillämpa befintliga SQL Server-licenser med Software Assurance.
  2. *Azure Hybrid Benefit*: Ett reducerat pris som inkluderar Azure Hybrid Benefit för SQL Server. Kunder kan välja detta pris genom att använda sina befintliga SQL Server-licenser med Software Assurance. Information om berättigande och annan information finns i [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/).

Det går inte att ange olika prisalternativ för enskilda instanser i en pool. Alla instanser i den överordnade poolen måste vara antingen till priset Licens inkluderad eller Azure Hybrid Benefit-priset. Licensmodellen för poolen kan ändras när poolen har skapats.

> [!IMPORTANT]
> Om du anger en licensmodell för den instans som skiljer sig från i poolen används poolpriset och instansnivåvärdet ignoreras.

Om du skapar instanspooler på [prenumerationer som är berättigade till dev-test-förmån](https://azure.microsoft.com/pricing/dev-test/)får du automatiskt rabatterade priser på upp till 55 procent på Azure SQL-hanterad instans.

Fullständig information om instanspoolpriser finns i *avsnittet instanspooler* på prissidan för [hanterade instanser](https://azure.microsoft.com/pricing/details/sql-database/managed/).

## <a name="next-steps"></a>Nästa steg

- Information om hur du kommer igång med instanspooler finns i [instruktioner för SQL Database-instanspooler](sql-database-instance-pools-how-to.md).
- Mer information om hur du skapar den första hanterade instansen finns i [Snabbstartsguiden](sql-database-managed-instance-get-started.md).
- En lista över funktioner och jämförelser finns i [vanliga SQL-funktioner](sql-database-features.md).
- Mer information om VNet-konfiguration finns i [VNet-konfiguration för hanterad instans](sql-database-managed-instance-connectivity-architecture.md).
- En snabbstart som skapar en hanterad instans och återställer en databas från en säkerhetskopia finns i [skapa en hanterad instans](sql-database-managed-instance-get-started.md).
- En självstudiekurs med HJÄLP av Azure Database Migration Service (DMS) för migrering finns i [tjänstring för hanterade instanser med DMS](../dms/tutorial-sql-server-to-managed-instance.md).
- Avancerad övervakning av prestanda för hanterade instansdatabaser med inbyggd felsökningsinformation finns i [Övervaka Azure SQL Database med Azure SQL Analytics](../azure-monitor/insights/azure-sql.md).
- Prisinformation finns i [SQL Database-priser för hanterade instanser](https://azure.microsoft.com/pricing/details/sql-database/managed/).
