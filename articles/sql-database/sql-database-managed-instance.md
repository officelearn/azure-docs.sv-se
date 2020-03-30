---
title: Översikt över SQL-hanterade instanser
description: I den här artikeln beskrivs hanterad instans av Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab, vanto
ms.date: 01/21/2020
ms.openlocfilehash: b9fdd1b25e53e1cdc8aa76564304a61adaa8d804
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268787"
---
# <a name="what-is-azure-sql-database-managed-instance"></a>Vad är Azure SQL Database-hanterad instans?

Hanterad instans är ett nytt distributionsalternativ för Azure SQL Database, vilket ger nära 100 % kompatibilitet med den senaste sql server-lokala databasmotorn (Enterprise Edition), som tillhandahåller en inbyggd [virtuell nätverksimplementering (VNet)](../virtual-network/virtual-networks-overview.md) som åtgärdar vanliga säkerhetsproblem och en [affärsmodell](https://azure.microsoft.com/pricing/details/sql-database/) som är gynnsam för lokala SQL Server-kunder. Distributionsmodellen för hanterade instanser gör det möjligt för befintliga SQL Server-kunder att lyfta och flytta sina lokala program till molnet med minimala program- och databasändringar. Samtidigt bevarar distributionsalternativet för hanterade instanser alla PaaS-funktioner (automatisk korrigering och versionsuppdateringar, [automatiska säkerhetskopior](sql-database-automated-backups.md), [hög tillgänglighet),](sql-database-high-availability.md) som drastiskt minskar hanteringskostnader och TCO.

> [!IMPORTANT]
> En lista över regioner där distributionsalternativet för hanterade instanser är tillgängligt finns i [regioner som stöds](sql-database-managed-instance-resource-limits.md#supported-regions).

I följande diagram beskrivs viktiga funktioner i hanterade instanser:

![viktiga funktioner](./media/sql-database-managed-instance/key-features.png)

Distributionsmodellen för hanterade instanser är utformad för kunder som vill migrera ett stort antal appar från lokala eller IaaS, egenbyggda eller ISV som miljö till fullständigt hanterad PaaS-molnmiljö, med så låg migreringsinsats som möjligt. Med hjälp av den helt automatiserade [Data Migration Service (DMS)](../dms/tutorial-sql-server-to-managed-instance.md#create-an-azure-database-migration-service-instance) i Azure kan kunder lyfta och flytta sin lokala SQL Server till en hanterad instans som erbjuder kompatibilitet med SQL Server lokalt och fullständig isolering av kundinstanser med inbyggt VNet-stöd.  Med Software Assurance kan du byta ut dina befintliga licenser mot rabatterade priser på en hanterad instans med [Azure Hybrid Benefit för SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/).  En hanterad instans är det bästa migreringsmålet i molnet för SQL Server-instanser som kräver hög säkerhet och en omfattande programmerbarhetsyta.

Distributionsalternativet för hanterade instanser ger nära 100 % kompatibilitet med ytan med den senaste lokala SQL Server-versionen via en stegvis versionsplan.

Information om hur du väljer mellan distributionsalternativen för Azure SQL Database: en enda databas, poolad databas och hanterad instans och SQL Server som finns på den virtuella datorn finns i [hur du väljer rätt version av SQL Server i Azure](sql-database-paas-vs-sql-server-iaas.md).

## <a name="key-features-and-capabilities"></a>Viktiga funktioner och funktioner

Hanterad instans kombinerar de bästa funktionerna som är tillgängliga både i Azure SQL Database och SQL Server Database Engine.

> [!IMPORTANT]
> En hanterad instans körs med alla funktioner i den senaste versionen av SQL Server, inklusive onlineåtgärder, automatiska abonnemangskorrigeringar och andra prestandaförbättringar för företag. En jämförelse av de funktioner som är tillgängliga förklaras i [Jämförelse med funktioner: Azure SQL Database kontra SQL Server](sql-database-features.md).

| **PaaS-förmåner** | **Verksamhetskontinuitet** |
| --- | --- |
|Inga inköp och hantering av maskinvara <br>Inga hanteringskostnader för hantering av underliggande infrastruktur <br>Snabb etablering och serviceskalning <br>Automatisk korrigering och versionsuppgradering <br>Integration med andra PaaS-datatjänster |99.99% upptid SLA  <br>Inbyggd [hög tillgänglighet](sql-database-high-availability.md) <br>Data skyddas med [automatiska säkerhetskopior](sql-database-automated-backups.md) <br>Kundkonfigurerbar lagringsperiod för säkerhetskopiering <br>Säkerhetskopierade [säkerhetskopieringar som initierats av](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql?view=azuresqldb-mi-current) användaren <br>[Tidsgränsen för återställning av databas](sql-database-recovery-using-backups.md#point-in-time-restore) |
|**Säkerhet och efterlevnad** | **Hantering**|
|Isolerad miljö ([VNet-integrering,](sql-database-managed-instance-connectivity-architecture.md)enklienttjänst, dedikerad beräkning och lagring) <br>[Transparent datakryptering (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)<br>[Azure AD-autentisering](sql-database-aad-authentication.md), enkel inloggningsstöd <br> <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Azure AD-serverhuvudnamn (inloggningar)</a>  <br>Följer efterlevnadsstandarder på samma sätt som Azure SQL-databas <br>[SQL-granskning](sql-database-managed-instance-auditing.md) <br>[Advanced Threat Protection](sql-database-managed-instance-threat-detection.md) |Api för Azure Resource Manager-API för automatisering av tjänstetablering och skalning <br>Azure-portalfunktioner för manuell tjänstetablering och skalning <br>Datamigreringstjänst

> [!IMPORTANT]
> Azure SQL Database (alla distributionsalternativ) har certifierats mot ett antal efterlevnadsstandarder. Mer information finns i [Microsoft Azure Trust Center](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) där du hittar den senaste listan över SQL Database-efterlevnadscertifieringar.

De viktigaste funktionerna i hanterade instanser visas i följande tabell:

|Funktion | Beskrivning|
|---|---|
| SQL Server version / bygga | SQL Server Database Engine (senaste stabila) |
| Hanterade automatiska säkerhetskopieringar | Ja |
| Inbyggd instans- och databasövervakning och mått | Ja |
| Automatisk korrigering av programvara | Ja |
| De senaste funktionerna i databasmotorn | Ja |
| Antal datafiler (ROWS) per databas | Flera |
| Antal loggfiler (LOG) per databas | 1 |
| VNet - Azure Resource Manager-distribution | Ja |
| VNet - Klassisk distributionsmodell | Inga |
| Stöd för Portal | Ja|
| Inbyggd integrationstjänst (SSIS) | Nej - SSIS är en del av [Azure Data Factory PaaS](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure) |
| Inbyggd analystjänst (SSAS) | Nej - SSAS är separat [PaaS](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview) |
| Inbyggd rapporteringstjänst (SSRS) | Nej - använd Power BI eller SSRS IaaS |
|||

## <a name="vcore-based-purchasing-model"></a>Köpmodell baserad på virtuell kärna

Den [vCore-baserade inköpsmodellen](sql-database-service-tiers-vcore.md) för hanterade instanser ger dig flexibilitet, kontroll, transparens och ett enkelt sätt att översätta lokala arbetsbelastningskrav till molnet. Med den här modellen kan du ändra beräkning, minne och lagring baserat på dina arbetsbelastningsbehov. VCore-modellen är också berättigad till upp till 55 procents besparingar med [Azure Hybrid Benefit för SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/).

I vCore-modellen kan du välja mellan generationer av maskinvara.

- **Gen4 (På andra)** Logiska processorer baseras på Intel E5-2673 v3 (Haswell) 2,4 GHz-processorer, anslutna SSD,fysiska kärnor, 7 GB RAM per kärna och beräkningsstorlekar mellan 8 och 24 virtuella kärnor.
- **Gen5 (På andra)** Logiska processorer är baserade på Intel E5-2673 v4 (Broadwell) 2,3 GHz och Intel SP-8160 (Skylake) processorer, snabba NVMe SSD, hyper-threaded logisk kärna och beräkningsstorlekar mellan 4 och 80 kärnor.

Mer information om skillnaden mellan maskinvarugenerationer i [resursgränser för hanterade instanser](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics).

## <a name="managed-instance-service-tiers"></a>Tjänstnivåer för hanterad instans

Hanterad instans är tillgänglig på två tjänstnivåer:

- **Allmänt syfte**: Utformad för program med typiska prestanda- och IO-svarstidskrav.
- **Affärskritisk:** Utformad för program med låga IO-svarstidskrav och minimal påverkan av underliggande underhållsåtgärder på arbetsbelastningen.

Båda tjänstnivåerna garanterar 99,99 % tillgänglighet och gör att du självständigt kan välja lagringsstorlek och beräkningskapacitet. Mer information om arkitekturen för hög tillgänglighet i Azure SQL Database finns i [Hög tillgänglighet och Azure SQL Database](sql-database-high-availability.md).

### <a name="general-purpose-service-tier"></a>Tjänstnivå för allmänt ändamål

I följande lista beskrivs nyckelegenskaper för tjänstnivån För allmänt ändamål:

- Design för de flesta affärsapplikationer med typiska prestandakrav
- Högpresterande Azure Blob-lagring (8 TB)
- Inbyggd hög tillgänglighet baserat på tillförlitlig Azure [Blob-lagring](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) och [Azure Service Fabric](../service-fabric/service-fabric-overview.md)

Mer information finns i bästa praxis och överväganden för [lagringsnivåer i allmänna ändamål](https://medium.com/azure-sqldb-managed-instance/file-layout-in-general-purpose-azure-sql-managed-instance-cf21fff9c76c) och [bästa praxis och överväganden för lagringsprestanda för hanterade instanser (allmänt ändamål).](https://blogs.msdn.microsoft.com/sqlcat/2018/07/20/storage-performance-best-practices-and-considerations-for-azure-sql-db-managed-instance-general-purpose/)

Hitta mer information om skillnaden mellan tjänstnivåer i [resursgränser för hanterade instanser](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).

### <a name="business-critical-service-tier"></a>Affärskritisk tjänstnivå

Business Critical service-nivå är byggd för program med höga IO-krav. Det ger högsta motståndskraft mot fel med hjälp av flera isolerade repliker.

I följande lista beskrivs de viktigaste egenskaperna för tjänstnivån Affärskritisk:

- Utformad för affärsprogram med högsta prestanda och HA-krav
- Levereras med supersnabba lokala SSD-lagring (upp till 1 TB på Gen4 och upp till 4 TB på Gen5)
- Inbyggd [hög tillgänglighet](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) baserat på alltid [på tillgänglighetsgrupper](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) och Azure Service [Fabric](../service-fabric/service-fabric-overview.md).
- Inbyggd ytterligare [skrivskyddad databasreplik](sql-database-read-scale-out.md) som kan användas för rapportering och andra skrivskyddade arbetsbelastningar
- [OLTP i minnet](sql-database-in-memory.md) som kan användas för arbetsbelastning med höga prestandakrav  

Hitta mer information om skillnaden mellan tjänstnivåer i [resursgränser för hanterade instanser](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).


## <a name="managed-instance-management-operations"></a>Hanterade instanshanteringsåtgärder

Azure SQL Database innehåller hanteringsåtgärder som du kan använda för att automatiskt distribuera nya hanterade instanser, uppdatera instansegenskaper och ta bort instanser när de inte längre behövs. Det här avsnittet innehåller information om hanteringsåtgärder och deras typiska varaktigheter.

För att stödja [distributioner inom Virtuella Azure-nätverk (VIRTUELLA nätverk)](../virtual-network/virtual-network-for-azure-services.md#deploy-azure-services-into-virtual-networks) och tillhandahålla isolering och säkerhet för kunder, förlitar sig hanterad instans på [virtuella kluster](sql-database-managed-instance-connectivity-architecture.md#high-level-connectivity-architecture), som representerar en dedikerad uppsättning isolerade virtuella datorer som distribueras i kundens virtuella nätverksundernät. I huvudsak resulterar varje hanterad instansdistribution i ett tomt undernät i en ny virtuell klusterversion.

Efterföljande åtgärder på distribuerade hanterade instanser kan också ha effekter på det underliggande virtuella klustret. Detta påverkar varaktigheten för hanteringsåtgärder, eftersom distribution av ytterligare virtuella datorer levereras med en overhead som måste beaktas när du planerar nya distributioner eller uppdateringar av befintliga hanterade instanser.

Alla hanteringsåtgärder kan kategoriseras på följande sätt:

- Instansdistribution (ny instansgenerering). 
- Instansuppdatering (ändra instansegenskaper, till exempel virtuella kärnor eller reserverad lagring.
- Instansborttagning.

Vanligtvis tar åtgärderna på virtuella kluster längst. Varaktigheten för åtgärderna i virtuella kluster varierar – nedan är de värden som du vanligtvis kan förvänta dig, baserat på befintliga tjänsttelemetridata:

- Skapande av virtuella kluster. Detta är ett synkront steg i instanshanteringsåtgärder. **90% av verksamheten avslutas i 4 timmar.**
- Ändra storlek på virtuella kluster (expandera eller krympa). Expansion är ett synkront steg, medan krympning utförs asynkront (utan påverkan på varaktigheten av instanshanteringsåtgärder). **90 % av klusterexpansionerna avslutas på mindre än 2,5 timmar.**
- Borttagning av virtuellt kluster. Borttagning är ett asynkront steg, men det kan också [initieras manuellt](sql-database-managed-instance-delete-virtual-cluster.md) på ett tomt virtuellt kluster, i vilket fall det körs synkront. **90 % av borttagningarna av virtuella kluster avslutas på 1,5 timmar**.

Dessutom kan hanteringen av instanser även omfatta en av åtgärderna i värdbaserade databaser, vilket resulterar i längre varaktigheter:

- Bifoga databasfiler från Azure Storage. Det här är ett synkront steg, till exempel beräkning (vCore) eller lagringsskalning upp eller ned på tjänstnivån Allmänt syfte. **90% av dessa operationer avslutas på 5 minuter.**
- Alltid På tillgänglighet grupp seedning. Det här är ett synkront steg, till exempel beräkning (vCore) eller lagringsskalning på tjänstnivån Affärskritisk samt när du ändrar tjänstnivån från allmänt syfte till affärskritisk (eller vice versa). Varaktigheten för den här åtgärden är proportionell mot den totala databasstorleken samt den aktuella databasaktiviteten (antal aktiva transaktioner). Databasaktivitet vid uppdatering av en instans kan medföra betydande avvikelse för den totala varaktigheten. **90% av dessa åtgärder körs på 220 GB / timme eller högre**.

I följande tabell sammanfattas åtgärder och typiska totala varaktigheter:

|Kategori  |Åtgärd  |Segmentet För lång drift  |Uppskattad varaktighet  |
|---------|---------|---------|---------|
|**Distribution** |Första instans i ett tomt undernät|Skapande av virtuellt kluster|90% av verksamheten avslutas i 4 timmar|
|Distribution |Första instansen av en annan maskinvarugenerering i ett icke-tomt undernät (till exempel första Gen 5-instansen i ett undernät med Gen 4-instanser)|Skapande av virtuellt kluster*|90% av verksamheten avslutas i 4 timmar|
|Distribution |Första instans skapande av 4 virtuella kärnor, i ett tomt eller icke-tomt undernät|Skapande av virtuellt kluster**|90% av verksamheten avslutas i 4 timmar|
|Distribution |Efterföljande förekomstskapande i det icke-tomma undernätet (2:a, 3:e, etc. instans)|Storlek på virtuellt kluster|90% av verksamheten avslutas på 2,5 timmar|
|**Uppdatering** |Ändring av instansegenskap (administratörslösenord, AAD-inloggning, Azure Hybrid-förmånsflagga)|Ej tillämpligt|Upp till 1 minut|
|Uppdatering |Upp/ned för instanslagring (tjänstnivå för allmänt ändamål)|Bifoga databasfiler|90% av verksamheten avslutas på 5 minuter|
|Uppdatering |Upp/ned för instanslagring (business critical service-nivå)|- Storlek på virtuellt kluster<br>- Alltid på tillgänglighet grupp sådd|90% av verksamheten avslutas i 2,5 timmar + tid att så alla databaser (220 GB / timme)|
|Uppdatering |Instansberäkning (vCores) skala upp och ned (allmänt syfte)|- Storlek på virtuellt kluster<br>- Bifoga databasfiler|90% av verksamheten avslutas på 2,5 timmar|
|Uppdatering |Instansberäkning (vCore) skala upp och ned (affärskritisk)|- Storlek på virtuellt kluster<br>- Alltid på tillgänglighet grupp sådd|90% av verksamheten avslutas i 2,5 timmar + tid att så alla databaser (220 GB / timme)|
|Uppdatering |Instans skala ner till 4 virtuella kärnor (allmänt syfte)|- Virtual cluster resizing (om det görs för första gången, kan det kräva virtuell kluster skapande **)<br>- Bifoga databasfiler|90% av verksamheten avslutas i 4 h 5 min **|
|Uppdatering |Instans skala ner till 4 virtuella kärnor (affärskritisk)|- Virtual cluster resizing (om det görs för första gången, kan det kräva virtuell kluster skapande **)<br>- Alltid på tillgänglighet grupp sådd|90% av verksamheten avslutas i 4 timmar + tid att så alla databaser (220 GB / timme)|
|Uppdatering |Ändring av instanstjänstnivå (allmänt syfte till affärskritisk och vice versa)|- Storlek på virtuellt kluster<br>- Alltid på tillgänglighet grupp sådd|90% av verksamheten avslutas i 2,5 timmar + tid att så alla databaser (220 GB / timme)|
|**Borttagning**|Borttagning av instans|Logga svans säkerhetskopiering för alla databaser|90% operationer avslutas i upp till 1 minut.<br>Om den sista instansen i undernätet tas bort schemaläggs virtuell klusterborttagning efter 12 timmar***|
|Borttagning|Borttagning av virtuellt kluster (som användarinitierad åtgärd)|Borttagning av virtuellt kluster|90% av verksamheten avslutas på upp till 1,5 timmar|

\*Virtuellt kluster skapas per maskinvarugenerering.

\*\*Distributionsalternativet 4 vCores släpptes i juni 2019 och kräver en ny virtuell klusterversion. Om du hade instanser i målundernätet som alla skapades före den 12 juni, distribueras ett nytt virtuellt kluster automatiskt till värd för 4 vCore-instanser.

\*\*\*12 timmar är den aktuella konfigurationen men som kan ändras i framtiden, så ta inte ett hårt beroende av den. Om du behöver ta bort ett virtuellt kluster tidigare (till exempel för att släppa undernätet) läser [du Ta bort ett undernät när du har tagit bort en hanterad Azure SQL-databas-hanterad instans](sql-database-managed-instance-delete-virtual-cluster.md).

### <a name="instance-availability-during-management"></a>Instanstillgänglighet under hantering

Hanterade instanser är inte tillgängliga för klientprogram under distributions- och borttagningsåtgärder.

Hanterade instanser är tillgängliga under uppdateringsåtgärder, men det finns en kort stilleståndstid som orsakas av redundansen som sker i slutet av uppdateringar som vanligtvis varar upp till 10 sekunder. Undantaget från detta är uppdatering av det reserverade lagringsutrymmet på tjänsten Allmänt syfte som inte medför redundans eller påverkar instanstillgängligheten.

> [!IMPORTANT]
> Varaktigheten för en redundans kan variera avsevärt vid långvariga transaktioner som sker i databaserna på grund av [förlängd återställningstid](sql-database-accelerated-database-recovery.md#the-current-database-recovery-process). Därför rekommenderas det inte att skala beräkning eller lagring av Azure SQL Database hanterad instans eller att ändra tjänstnivån samtidigt med de långvariga transaktionerna (dataimport, databearbetningsjobb, indexrekläggning osv.). Databas redundans som ska utföras i slutet av åtgärden kommer att avbryta pågående transaktioner och resultera i förlängd återställningstid.

> [!TIP]
> Uppdatering av reserverat lagringsutrymme på tjänstnivån allmänt syfte medför inte redundans eller påverkar instanstillgängligheten.

[Snabbare databasåterställning](sql-database-accelerated-database-recovery.md) är för närvarande inte tillgänglig för hanterade Azure SQL-databasinstanser. När funktionen är aktiverad minskar den avsevärt variabiliteten för redundanstid, även vid långvariga transaktioner.

### <a name="canceling-management-operations"></a>Avbryta hanteringsåtgärder

I följande tabell sammanfattas möjligheten att avbryta specifika hanteringsåtgärder och typiska totala varaktigheter:

Kategori  |Åtgärd  |Kan avbrytas  |Beräknad avbruten varaktighet  |
|---------|---------|---------|---------|
|Distribution |Skapa instans |Inga |  |
|Uppdatering |Upp/ned för instanslagring (allmänt syfte) |Inga |  |
|Uppdatering |Förekomstlagringsskalning upp/ned (affärskritiskt) |Ja |90% av verksamheten avslutas på 5 minuter |
|Uppdatering |Instansberäkning (vCores) skala upp och ned (allmänt syfte) |Ja |90% av verksamheten avslutas på 5 minuter |
|Uppdatering |Instansberäkning (vCore) skala upp och ned (affärskritisk) |Ja |90% av verksamheten avslutas på 5 minuter |
|Uppdatering |Ändring av instanstjänstnivå (allmänt syfte till affärskritisk och vice versa) |Ja |90% av verksamheten avslutas på 5 minuter |
|Ta bort |Borttagning av instans |Inga |  |
|Ta bort |Borttagning av virtuellt kluster (som användarinitierad åtgärd) |Inga |  |

För att avbryta hanteringsåtgärden, gå till översiktsbladet och klicka på meddelanderutan för pågående drift. Från höger sida visas en skärm med pågående drift och det kommer att finnas en knapp för att avbryta åtgärden. Efter första klicket blir du ombedd att klicka igen och bekräfta att du vill avbryta åtgärden.

[![](./media/sql-database-managed-instance/canceling-operation.png)](./media/sql-database-managed-instance/canceling-operation.png#lightbox)

När avbryt begäran har skickats och bearbetats, får du meddelande om avbryta inlämning har lyckats eller inte. 

I händelse av att avbryta lyckades avbryts hanteringsåtgärden på ett par minuter som resulterar i ett fel.

![avbryta åtgärdsresultat](./media/sql-database-managed-instance/canceling-operation-result.png)

Om knappen Avbryt begäran misslyckas eller avbryt inte är aktiv betyder det att hanteringsåtgärden inte har angetts för att avbrytas och att den avslutas om några minuter. Hanteringsåtgärden fortsätter körningen tills den är klar.

> [!IMPORTANT]
> Avbryt-åtgärden stöds för närvarande endast i Portal.

## <a name="advanced-security-and-compliance"></a>Avancerad säkerhet och efterlevnad

Distributionsalternativet för hanterade instanser kombinerar avancerade säkerhetsfunktioner som tillhandahålls av Azure cloud och SQL Server Database Engine.

### <a name="managed-instance-security-isolation"></a>Säkerhetsisolering för hanterade instanser

En hanterad instans ger ytterligare säkerhetsisolering från andra klienter i Azure-molnet. Säkerhetsisolering omfattar:

- [Inbyggd implementering](sql-database-managed-instance-connectivity-architecture.md) av virtuella nätverk och anslutning till din lokala miljö med Azure Express Route eller VPN Gateway.
- I en standarddistribution exponeras SQL-slutpunkten endast via en privat IP-adress, vilket möjliggör säker anslutning från privata Azure- eller hybridnätverk.
- En klient med dedikerad underliggande infrastruktur (beräkning, lagring).

I följande diagram beskrivs olika anslutningsalternativ för dina program:

![hög tillgänglighet](./media/sql-database-managed-instance/application-deployment-topologies.png)  

Mer information om VNet-integrering och efterlevnad av nätverksprincipen på undernätsnivå finns i [VNet-arkitektur för hanterade instanser](sql-database-managed-instance-connectivity-architecture.md) och [Anslut programmet till en hanterad instans](sql-database-managed-instance-connect-app.md).

> [!IMPORTANT]
> Placera flera hanterade instanser i samma undernät, oavsett var det är tillåtet enligt dina säkerhetskrav, eftersom det ger dig ytterligare fördelar. Samlokaliseringsinstanser i samma undernät förenklar avsevärt underhåll av nätverksinfrastruktur och minskar instansetableringstiden, eftersom lång etableringstid är associerad med kostnaden för att distribuera den första hanterade instansen i ett undernät.

### <a name="azure-sql-database-security-features"></a>Säkerhetsfunktioner för Azure SQL-databas

Azure SQL Database innehåller en uppsättning avancerade säkerhetsfunktioner som kan användas för att skydda dina data.

- [Granskning av hanterade instanser](sql-database-managed-instance-auditing.md) spårar databashändelser och skriver dem till en granskningsloggfil som placeras i ditt Azure-lagringskonto. Granskning kan bidra till att upprätthålla regelefterlevnad, förstå databasaktivitet och få insikt i avvikelser och avvikelser som kan tyda på affärsproblem eller misstänkta säkerhetsöverträdelser.
- Datakryptering i rörelse – en hanterad instans skyddar dina data genom att tillhandahålla kryptering för data i rörelse med hjälp av Transport Layer Security. Förutom transportlagersäkerhet erbjuder distributionsalternativet för hanterade instanser skydd av känsliga data under flygning, i vila och under frågebearbetning med [Alltid krypterad](/sql/relational-databases/security/encryption/always-encrypted-database-engine). Always Encrypted är först i branschen med att erbjuda oslagbar datasäkerhet mot överträdelser, inklusive stöld av viktiga data. Med alltid krypterade lagras kreditkortsnummer till exempel krypterade i databasen alltid, även under frågebearbetning, vilket möjliggör dekryptering vid användningspunkten av behörig personal eller program som behöver bearbeta dessa data.
- [Avancerat skydd mot hot](sql-database-managed-instance-threat-detection.md) kompletterar [granskningen](sql-database-managed-instance-auditing.md) genom att tillhandahålla ett extra lager av säkerhetsinformation som är inbyggd i tjänsten och som upptäcker ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser. Du får ett meddelande om misstänkta aktiviteter, potentiella sårbarheter och SQL-injektionsattacker samt avvikande databasåtkomstmönster. Avancerade hotskyddsaviseringar kan visas från [Azure Security Center](https://azure.microsoft.com/services/security-center/) och ge information om misstänkt aktivitet och rekommendera åtgärder för hur du undersöker och minskar hotet.  
- [Dynamisk datamaskering](/sql/relational-databases/security/dynamic-data-masking) begränsar känslig dataexponering genom att maskera den för icke-privilegierade användare. Dynamisk datamaskering hjälper till att förhindra obehörig åtkomst till känsliga data genom att du kan ange hur mycket av känsliga data som ska visas med minimal påverkan på programlagret. Det är en principbaserad säkerhetsfunktion som fungerar genom att dölja känslig data i resultatuppsättningen för en fråga över angivna databasfält, medan data i databasen förblir oförändrad.
- [Med säkerhet på radnivå](/sql/relational-databases/security/row-level-security) kan du styra åtkomsten till rader i en databastabell baserat på egenskaperna hos den användare som kör en fråga (till exempel efter gruppmedlemskap eller körningskontext). Säkerheten på radnivå (RLS) förenklar design och kodning av säkerheten i ditt program. RLS låter dig implementera begränsningar för dataåtkomst för raden. Se till exempel till att arbetstagare bara kan komma åt de datarader som är relevanta för deras avdelning, eller begränsa dataåtkomst till endast relevanta data.
- [Transparent datakryptering (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) krypterar hanterade instansdatafiler, så kallade krypteringsdata i vila. TDE utför I/O-kryptering i realtid och dekryptering av data- och loggfiler. Krypteringen använder en databaskrypteringsnyckel (DEK), som lagras i databasens startpost för tillgänglighet under återställningen. Du kan skydda alla dina databaser i en hanterad instans med transparent datakryptering. TDE är SQL Servers beprövade krypterings-at-rest-teknik som krävs av många efterlevnadsstandarder för att skydda mot stöld av lagringsmedia.

Migrering av en krypterad databas till en hanterad instans stöds via Azure Database Migration Service (DMS) eller inbyggd återställning. Om du planerar att migrera en krypterad databas med inbyggd återställning är det ett obligatoriskt steg att migrera det befintliga TDE-certifikatet från SQL Server lokalt eller SQL Server i en virtuell dator till en hanterad instans. Mer information om migreringsalternativ finns i [SQL Server-instansmigrering till hanterad instans](sql-database-managed-instance-migrate.md).

## <a name="azure-active-directory-integration"></a>Azure Active Directory-integrering

Distributionsalternativet för hanterade instanser stöder traditionella SQL-serverdatabasmotorinloggningar och inloggningar som är integrerade med Azure Active Directory (AAD). Azure AD-serverhuvudnamn (inloggningar) (**offentlig förhandsversion**) är Azure-molnversion av lokala databasinloggningar som du använder i din lokala miljö. Med Azure AD-serverobjekt (inloggningar) kan du ange användare och grupper från din Azure Active Directory-klient som verkliga instansomsbefattningar, som kan utföra alla instansnivåoperationer, inklusive flerdatabasfrågor inom samma hanterade Instans.

En ny syntax introduceras för att skapa Azure AD-serverhuvudnamn (inloggningar), **FRÅN EXTERN LEVERANTÖR**. Mer information om syntaxen finns i <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">SKAPA INLOGGNING</a>och granska etablera en Azure [Active Directory-administratör för den hanterade](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance) instansartikeln.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory-integrering och multifaktorautentisering

Med distributionsalternativet för hanterade instanser kan du centralt hantera identiteter för databasanvändare och andra Microsoft-tjänster med [Azure Active Directory-integrering](sql-database-aad-authentication.md). Den här funktionen förenklar hanteringen av behörighet och ger ökad säkerhet. Azure Active Directory stöder [multifaktorautentisering](sql-database-ssms-mfa-authentication-configure.md) (MFA) för att öka säkerheten för data och program med stöd för en process med enkel inloggning.

### <a name="authentication"></a>Autentisering

Hanterad instansautentisering refererar till hur användare bevisar sin identitet när de ansluter till databasen. SQL Database stöder två typer av autentisering:  

- **SQL-autentisering:**

  Den här autentiseringsmetoden använder ett användarnamn och lösenord.
- **Azure Active Directory-autentisering:**

  Den här autentiseringsmetoden använder identiteter som hanteras av Azure Active Directory och stöds för hanterade och integrerade domäner. Använd Active Directory-autentisering (integrerad säkerhet) [närhelst det går](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode).

### <a name="authorization"></a>Auktorisering

Auktorisering refererar till vad en användare kan göra i en Azure SQL-databas och styrs av ditt användarkontos databasrollmedlemskap och behörigheter på objektnivå. En hanterad instans har samma auktoriseringsfunktioner som SQL Server 2017.

## <a name="database-migration"></a>Databasmigrering

Alternativet för distribution av hanterade instanser är inriktat på användarscenarier med massdatabasmigrering från lokala implementeringar eller implementeringar av IaaS-databas. Hanterad instans stöder flera alternativ för databasmigrering:

### <a name="back-up-and-restore"></a>Säkerhetskopiera och återställa  

Migreringsmetoden utnyttjar SQL-säkerhetskopior till Azure Blob-lagring. Säkerhetskopior som lagras i Azure storage blob kan återställas direkt till en hanterad instans med hjälp av [T-SQL RESTORE kommandot](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql?view=azuresqldb-mi-current).

- En snabbstart som visar hur du återställer säkerhetskopian av Wide World Importers - Standarddatabassäkerhet finns i [Återställa en säkerhetskopia till en hanterad instans](sql-database-managed-instance-get-started-restore.md). Den här snabbstarten visar att du måste ladda upp en säkerhetskopia till Azure blob storage och skydda den med en SAS-nyckel (Shared Access Signature).
- Information om återställning från URL finns i [Native RESTORE från URL](sql-database-managed-instance-migrate.md#native-restore-from-url).

> [!IMPORTANT]
> Säkerhetskopior från en hanterad instans kan bara återställas till en annan hanterad instans. De kan inte återställas till en lokal SQL Server eller till en enda databas/elastisk pool.

### <a name="data-migration-service"></a>Datamigreringstjänst

Azure Database Migration Service är en fullständigt hanterad tjänst som utformats för att möjliggöra sömlösa migreringar från flera databaskällor till Azure Data-plattformar med minimal stilleståndstid. Den här tjänsten effektiviserar de uppgifter som krävs för att flytta befintliga tredjeparts- och SQL Server-databaser till Azure SQL Database (enskilda databaser, poolade databaser i elastiska pooler och instansdatabaser i en hanterad instans) och SQL Server i Azure VM. Se [Så här migrerar du den lokala databasen till hanterad instans med DMS](https://aka.ms/migratetoMIusingDMS).

## <a name="sql-features-supported"></a>SQL-funktioner som stöds

Distributionsalternativet för hanterade instanser syftar till att leverera nära 100 % ytkompatibilitet med lokal SQL Server som kommer i etapper fram till tjänstens allmänna tillgänglighet. En funktions- och jämförelselista finns i [JÄMFÖRELSEn med SQL Database-funktionen](sql-database-features.md)och en lista över T-SQL-skillnader i hanterade instanser jämfört med SQL Server finns i [T-SQL-skillnader i hanterade instanser från SQL Server](sql-database-managed-instance-transact-sql-information.md).

Distributionsalternativet för hanterade instanser stöder bakåtkompatibilitet till SQL 2008-databaser. Direktmigrering från SQL 2005-databasservrar stöds, kompatibilitetsnivå för migrerade SQL 2005-databaser uppdateras till SQL 2008.
  
I följande diagram beskrivs ytkompatibilitet i hanterade instanser:  

![Migration](./media/sql-database-managed-instance/migration.png)

### <a name="key-differences-between-sql-server-on-premises-and-in-a-managed-instance"></a>Viktiga skillnader mellan SQL Server lokalt och i en hanterad instans

Distributionsalternativet för hanterade instanser drar nytta av att vara alltid uppdaterad i molnet, vilket innebär att vissa funktioner i lokala SQL Server kan vara antingen föråldrade, pensionerade eller har alternativ. Det finns specifika fall när verktyg behöver känna igen att en viss funktion fungerar på ett något annorlunda sätt eller att tjänsten körs i en miljö som du inte har fullständig kontroll över:

- Hög tillgänglighet är inbyggd och förkonfigurerad med hjälp av teknik som liknar [alltid på tillgänglighetsgrupper](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server).
- Automatiska säkerhetskopior och tidsåterställning. Kunden kan `copy-only` initiera säkerhetskopior som inte stör den automatiska säkerhetskopieringskedjan.
- Hanterad instans tillåter inte att du anger fullständiga fysiska sökvägar så alla motsvarande scenarier måste stödjas på olika sätt: RESTORE DB stöder inte MED MOVE, CREATE DB tillåter inte fysiska sökvägar, BULK INSERT fungerar endast med Azure Blobbar osv.
- Hanterad instans stöder [Azure AD-autentisering](sql-database-aad-authentication.md) som molnalternativ till Windows-autentisering.
- Hanterad instans hanterar automatiskt XTP-filgrupp och filer för databaser som innehåller IN-Memory OLTP-objekt
- Hanterad instans stöder SQL Server Integration Services (SSIS) och kan vara värd för SSIS-katalog (SSISDB) som lagrar SSIS-paket, men de körs på en hanterad Azure-SSIS Integration Runtime (IR) i Azure Data Factory (ADF), se [Skapa Azure-SSIS IR i ADF](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). Information om hur du jämför SSIS-funktionerna i SQL Database finns i [Jämför en enda Azure SQL-databas, elastisk pool och hanterad instans](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance).

### <a name="managed-instance-administration-features"></a>Funktioner för administration av hanterade instanser

Distributionsalternativet för hanterade instanser gör det möjligt för systemadministratören att lägga mindre tid på administrativa uppgifter eftersom SQL Database-tjänsten antingen utför dem åt dig eller förenklar dessa uppgifter avsevärt. Till exempel [OS / RDBMS installation och korrigering,](sql-database-high-availability.md) [dynamisk instans storleksändring och konfiguration](sql-database-single-database-scale.md), [säkerhetskopior](sql-database-automated-backups.md), [databasreplikering](replication-with-sql-database-managed-instance.md) (inklusive systemdatabaser), [hög tillgänglighet konfiguration](sql-database-high-availability.md)och konfiguration av hälsa och prestanda [övervakning](../azure-monitor/insights/azure-sql.md) dataströmmar.

> [!IMPORTANT]
> En lista över funktioner som stöds, stöds delvis och som inte stöds finns i [SQL Database-funktioner](sql-database-features.md). En lista över T-SQL-skillnader i hanterade instanser jämfört med SQL Server finns i [T-SQL-skillnader i hanterad instans från SQL Server](sql-database-managed-instance-transact-sql-information.md)

### <a name="how-to-programmatically-identify-a-managed-instance"></a>Så här identifierar du en hanterad instans på ett programmatiskt sätt

I följande tabell visas flera egenskaper som är tillgängliga via Transact SQL och som du kan använda för att identifiera att ditt program arbetar med hanterad instans och hämta viktiga egenskaper.

|Egenskap|Värde|Kommentar|
|---|---|---|
|`@@VERSION`|Microsoft SQL Azure (RTM) – 12.0.2000.8 2018-03-07 Copyright (C) 2018 Microsoft Corporation.|Det här värdet är samma som i SQL Database. Detta **indikerar inte** SQL engine version 12 (SQL Server 2014). Hanterad instans kör alltid den senaste stabila SQL-motorversionen, vilket är lika med eller högre än den senaste tillgängliga RTM-versionen av SQL Server.  |
|`SERVERPROPERTY ('Edition')`|SQL Azure|Det här värdet är samma som i SQL Database.|
|`SERVERPROPERTY('EngineEdition')`|8|Det här värdet identifierar unikt en hanterad instans.|
|`@@SERVERNAME`, `SERVERPROPERTY ('ServerName')`|DNS-namn i fullständig instans`<instanceName>`i följande format: . `<dnsPrefix>`.database.windows.net, där `<instanceName>` är namnet som tillhandahålls av `<dnsPrefix>` kunden, medan är autogenererad del av namnet som garanterar globala DNS-namn unikhet ("wcus17662feb9ce98", till exempel)|Exempel: my-managed-instance.wcus17662feb9ce98.database.windows.net|

## <a name="next-steps"></a>Nästa steg

- Mer information om hur du skapar den första hanterade instansen finns i [Snabbstartsguiden](sql-database-managed-instance-get-started.md).
- En lista över funktioner och jämförelser finns i [vanliga SQL-funktioner](sql-database-features.md).
- Mer information om VNet-konfiguration finns i [VNet-konfiguration för hanterad instans](sql-database-managed-instance-connectivity-architecture.md).
- En snabbstart som skapar en hanterad instans och återställer en databas från en säkerhetskopia finns i [skapa en hanterad instans](sql-database-managed-instance-get-started.md).
- En självstudiekurs med HJÄLP av Azure Database Migration Service (DMS) för migrering finns i [tjänstring för hanterade instanser med DMS](../dms/tutorial-sql-server-to-managed-instance.md).
- Avancerad övervakning av prestanda för hanterade instansdatabaser med inbyggd felsökningsinformation finns i [Övervaka Azure SQL Database med Azure SQL Analytics](../azure-monitor/insights/azure-sql.md).
- Prisinformation finns i [SQL Database-priser för hanterade instanser](https://azure.microsoft.com/pricing/details/sql-database/managed/).
