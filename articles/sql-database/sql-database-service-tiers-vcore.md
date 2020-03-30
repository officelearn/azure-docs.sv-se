---
title: Översikt över modell för virtuell kärna
description: Med vCore-inköpsmodellen kan du självständigt skala beräknings- och lagringsresurser, matcha lokala prestanda och optimera priset.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 11/27/2019
ms.openlocfilehash: 5fd69dcd30292630862887ab5434764ba377b396
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481090"
---
# <a name="vcore-model-overview"></a>Översikt över modell för virtuell kärna

Den virtuella kärnan (vCore) modellen ger flera fördelar:

- Högre beräknings-, minnes-, IO- och lagringsgränser.
- Kontroll över maskinvarugenereringen för att bättre matcha beräknings- och minneskrav för arbetsbelastningen.
- Prisrabatter för [Azure Hybrid Benefit (AHB)](sql-database-azure-hybrid-benefit.md) och [reserverad instans (RI)](sql-database-reserved-capacity.md).
- Större transparens i maskinvaruinformationen som driver beräkningen. underlättar planering för migreringar från lokala distributioner.

## <a name="service-tiers"></a>Tjänstnivåer

Tjänstnivåalternativen i vCore-modellen inkluderar allmänt syfte, affärskritiskt och hyperskala. Tjänstnivån definierar i allmänhet lagringsarkitektur, utrymme och IO-gränser och alternativ för affärskontinuitet som är relaterade till tillgänglighet och haveriberedskap.

||**Generellt syfte**|**Affärskritiskt**|**Hyperskala**|
|---|---|---|---|
|Bäst för|De flesta arbetsbelastningar för företag. Erbjuder budgetorienterade, balanserade och skalbara beräknings- och lagringsalternativ. |Erbjuder affärsprogram högsta motståndskraft mot fel med hjälp av flera isolerade repliker och ger högsta I/O-prestanda per databasreplik.|De flesta affärsarbetsbelastningar med mycket skalbara lagrings- och lässkalningskrav.  Erbjuder högre återhämtning till fel genom att tillåta konfiguration av mer än en isolerad databasreplik. |
|Lagring|Använder fjärrlagring.<br/>**Enstaka databaser och elastiska pooler etablerade beräkning:**<br/>5 GB – 4 TB<br/>**Serverlös beräkning:**<br/>5 GB - 3 TB<br/>**Hanterad instans:** 32 GB - 8 TB |Använder lokal SSD-lagring.<br/>**Enstaka databaser och elastiska pooler etablerade beräkning:**<br/>5 GB – 4 TB<br/>**Hanterad instans:**<br/>32 GB - 4 TB |Flexibel automatisk lagring efter behov. Stöder upp till 100 TB lagringsutrymme. Använder lokal SSD-lagring för lokal buffertpoolcache och lokal datalagring. Använder Azure-fjärrlagring som slutligt långsiktigt datalager. |
|IOPS och dataflöde (ungefärlig)|**Enskilda databaser och elastiska pooler:** Se resursgränser för [enskilda databaser](../sql-database/sql-database-vcore-resource-limits-single-databases.md) och [elastiska pooler](../sql-database/sql-database-vcore-resource-limits-elastic-pools.md).<br/>**Hanterad instans:** Se [Översikt Azure SQL Database hanterade instansresursgränser](../sql-database/sql-database-managed-instance-resource-limits.md#service-tier-characteristics).|Se resursgränser för [enskilda databaser](../sql-database/sql-database-vcore-resource-limits-single-databases.md) och [elastiska pooler](../sql-database/sql-database-vcore-resource-limits-elastic-pools.md).|Hyperskala är en flerskiktad arkitektur med cachelagring på flera nivåer. Effektiv IOPS och dataflöde beror på arbetsbelastningen.|
|Tillgänglighet|1 replik, inga lässkalerepliker|3 repliker, 1 [lässkala replik](sql-database-read-scale-out.md),<br/>zon-redundant hög tillgänglighet (HA)|1 läs-skriv replika, plus 0-4 [lässkala repliker](sql-database-read-scale-out.md)|
|Säkerhetskopior|[Geo redundant lagring med läsåtkomst (RA-GRS),](../storage/common/storage-designing-ha-apps-with-ragrs.md)7-35 dagar (7 dagar som standard)|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7-35 dagar (7 dagar som standard)|Ögonblicksbildbaserade säkerhetskopior i Azure-fjärrlagring. Återställningar använder dessa ögonblicksbilder för snabb återställning. Säkerhetskopior är momentana och påverkar inte i/o-prestanda för beräkning. Återställningar är snabba och är inte en storlek-of-data-åtgärd (tar minuter i stället för timmar eller dagar).|
|Minnesintern|Stöds inte|Stöds|Stöds inte|
|||


### <a name="choosing-a-service-tier"></a>Välja tjänstnivå

Information om hur du väljer en tjänstnivå för just din arbetsbelastning finns i följande artiklar:

- [När du ska välja tjänstnivån Allmänt ändamål](sql-database-service-tier-general-purpose.md#when-to-choose-this-service-tier)
- [När du ska välja tjänstnivå För affärskritisk](sql-database-service-tier-business-critical.md#when-to-choose-this-service-tier)
- [När du ska välja tjänstnivån Hyperskala](sql-database-service-tier-hyperscale.md#who-should-consider-the-hyperscale-service-tier)


## <a name="compute-tiers"></a>Beräkningsnivåer

Alternativ på beräkningsnivå i vCore-modellen inkluderar de etablerade och serverlösa beräkningsnivåerna.


### <a name="provisioned-compute"></a>Etablerad beräkning

Den etablerade beräkningsnivån tillhandahåller en viss mängd beräkningsresurser som kontinuerligt etableras oberoende av arbetsbelastningsaktivitet och fakturor för mängden beräkning som etablerats till ett fast pris per timme.


### <a name="serverless-compute"></a>Serverlös databearbetning

Den [serverlösa beräkningsnivån](sql-database-serverless.md) skalar automatiskt beräkningsresurser baserat på arbetsbelastningsaktivitet och räkningar för mängden beräkning som används per sekund.



## <a name="hardware-generations"></a>Hårdvara generationer

Alternativ för maskinvarugenerering i vCore-modellen inkluderar Gen 4/5, M-serien (förhandsversion) och Fsv2-serien (förhandsversion). Maskinvarugenereringen definierar i allmänhet beräknings- och minnesgränserna och andra egenskaper som påverkar arbetsbelastningens prestanda.

### <a name="gen4gen5"></a>Gen4/Gen5

- Gen4/Gen5-maskinvara ger balanserade beräknings- och minnesresurser och är lämplig för de flesta databasarbetsbelastningar som inte har högre minne, högre vCore eller snabbare enkla vCore-krav enligt Fsv2-serien eller M-serien.

För regioner där Gen4/Gen5 är tillgängligt finns i [Gen4/Gen5 tillgänglighet](#gen4gen5-1).

### <a name="fsv2-seriespreview"></a>Fsv2-serien (förhandsgranskning)

- Fsv2-serien är ett beräkningsoptimerat maskinvarualternativ som ger låg CPU-latens och hög klockfrekvens för de mest CPU-krävande arbetsbelastningar.
- Beroende på arbetsbelastningen kan Fsv2-serien leverera mer CPU-prestanda per vCore än Gen5, och 72 vCore-storleken kan ge mer CPU-prestanda för mindre kostnad än 80 virtuella kärnor på Gen5. 
- Fsv2 ger mindre minne och tempdb per vCore än annan maskinvara så arbetsbelastningar som är känsliga för dessa gränser kanske vill överväga Gen5 eller M-serien istället.  

Fsv2-serien stöds endast på nivån Allmänt syfte.  För regioner där Fsv2-serien är tillgänglig finns i [Tillgänglighet i Fsv2-serien](#fsv2-series).


### <a name="m-seriespreview"></a>M-serien (förhandsgranskning)

- M-serien är ett minnesoptimerat maskinvarualternativ för arbetsbelastningar som kräver mer minne och högre beräkningsgränser än i Gen5.
- M-serien ger 29 GB per vCore och 128 virtuella kärnor, vilket ökar minnesgränsen i förhållande till Gen5 med 8x till nästan 4 TB.

M-serien stöds endast på den affärskritiska nivån och stöder inte zonredundans.

Om du vill aktivera maskinvara i M-serien för en prenumeration och region måste en supportbegäran öppnas. Prenumerationen måste vara en betald erbjudandetyp inklusive Pay-As-You-Go eller Enterprise Agreement (EA).  Om supportbegäran godkänns följer urvals- och etableringsupplevelsen av M-serien samma mönster som för andra maskinvarugenerationer. För regioner där M-serien är tillgänglig finns i [M-seriens tillgänglighet](#m-series).


### <a name="compute-and-memory-specifications"></a>Beräknings- och minnesspecifikationer


|Generering av maskinvara  |Compute  |Minne  |
|:---------|:---------|:---------|
|Gen4 (På andra)     |- Intel E5-2673 v3 (Haswell) 2,4 GHz-processorer<br>- Etablera upp till 24 virtuella kärnor (1 vCore = 1 fysisk kärna)  |- 7 GB per vCore<br>- Etablera upp till 168 GB|
|Gen5 (På andra)     |**Etablerad beräkning**<br>- Intel E5-2673 v4 (Broadwell) 2,3 GHz och Intel SP-8160 (Skylake)* processorer<br>- Etablera upp till 80 virtuella kärnor (1 vCore = 1 hypertråd)<br><br>**Serverlös databearbetning**<br>- Intel E5-2673 v4 (Broadwell) 2,3 GHz och Intel SP-8160 (Skylake)* processorer<br>- Automatisk skala upp till 16 vCores (1 vCore = 1 hyper-tråd)|**Etablerad beräkning**<br>- 5,1 GB per vCore<br>- Etablera upp till 408 GB<br><br>**Serverlös databearbetning**<br>- Automatisk skala upp till 24 GB per vCore<br>- Automatisk skala upp till 48 GB max|
|Fsv2-serien     |- Intel Xeon Platinum 8168 (SkyLake) processorer<br>- Med en bibehållen turbo klockhastighet på 3,4 GHz och en maximal turbo klockhastighet med en kärna på 3,7 GHz.<br>- Etablering 72 virtuella kärnor (1 vCore = 1 hyper-tråd)|- 1,9 GB per vCore<br>- Avsättning 136 GB|
|M-serien     |- Intel Xeon E7-8890 v3 2,5 GHz-processorer<br>- Etablering 128 virtuella kärnor (1 vCore = 1 hypertråd)|- 29 GB per vCore<br>- Avsättning 3,7 TB|

\*I [vyn sys.dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) dynamisk hantering visas maskinvarugenerering för Gen5-databaser med Intel SP-8160-processorer (Skylake) som Gen6. Resursgränserna för alla Gen5-databaser är desamma oavsett processortyp (Broadwell eller Skylake).

Mer information om resursbegränsningar finns i [Resursgränser för enskilda databaser (vCore)](sql-database-vcore-resource-limits-single-databases.md)eller [Resursgränser för elastiska pooler (vCore).](sql-database-vcore-resource-limits-elastic-pools.md)

### <a name="selecting-a-hardware-generation"></a>Välja en maskinvarugenerering

I Azure-portalen kan du välja maskinvarugenerering för en SQL-databas eller pool när den skapas, eller så kan du ändra maskinvarugenereringen för en befintlig SQL-databas eller pool.

**Så här väljer du en maskinvarugenerering när du skapar en SQL-databas eller -pool**

Detaljerad information finns i [Skapa en SQL-databas](sql-database-single-database-get-started.md).

På fliken **Grunderna** väljer du länken **Konfigurera databas** i avsnittet Beräkna **+ lagring** och väljer sedan länken **Ändra konfiguration:**

  ![konfigurera databasen](media/sql-database-service-tiers-vcore/configure-sql-database.png)

Välj önskad maskinvarugenerering:

  ![välj maskinvara](media/sql-database-service-tiers-vcore/select-hardware.png)


**Så här ändrar du maskinvarugenereringen för en befintlig SQL-databas eller -pool**

För en databas väljer du länken **Prisnivå** på sidan Översikt:

  ![ändra maskinvara](media/sql-database-service-tiers-vcore/change-hardware.png)

Välj **Konfigurera**på sidan Översikt på sidan Översikt .

Följ stegen för att ändra konfigurationen och välj maskinvarugenerering enligt beskrivningen i föregående steg.

**Så här väljer du en maskinvarugenerering när du skapar en hanterad instans**

Detaljerad information finns i [Skapa en hanterad instans](sql-database-managed-instance-get-started.md).

På fliken **Grunderna** väljer du länken **Konfigurera databas** i avsnittet Beräkna **+ lagring** och väljer sedan önskad maskinvarugenerering:

  ![konfigurera hanterad instans](media/sql-database-service-tiers-vcore/configure-managed-instance.png)
  
**Så här ändrar du maskinvarugenereringen för en befintlig hanterad instans**

# <a name="portal"></a>[Portal](#tab/azure-portal)

På sidan Hanterad instans väljer du länken **Prisnivå** som placeras under avsnittet Inställningar

![ändra maskinvara för hanterade instanser](media/sql-database-service-tiers-vcore/change-managed-instance-hardware.png)

På sidan **Prisnivå** kan du ändra maskinvarugenerering enligt beskrivningen i föregående steg.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Använd följande PowerShell-skript:

```powershell-interactive
Set-AzSqlInstance -Name "managedinstance1" -ResourceGroupName "ResourceGroup01" -ComputeGeneration Gen5
```

Mer information finns i kommandot [Set-AzSqlInstance.](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Använd följande CLI-kommando:

```azurecli-interactive
az sql mi update -g mygroup -n myinstance --family Gen5
```

Mer information finns i kommandot [az sql mi update.](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)

---

### <a name="hardware-availability"></a>Maskinvarutillgänglighet

#### <a name="gen4gen5"></a><a name="gen4gen5-1"></a>Gen4/Gen5

Gen4-maskinvaran [håller på att fasas ut](https://azure.microsoft.com/updates/gen-4-hardware-on-azure-sql-database-approaching-end-of-life-in-2020/) och är inte längre tillgänglig för de nya distributionerna. Alla nya databaser måste distribueras på Gen5-maskinvara.

Gen5 finns i de flesta regioner över hela världen.

#### <a name="fsv2-series"></a>Fsv2-serien

Fsv2-serien finns i följande regioner: Australia Central, Australia Central 2, Australia East, Australia Southeast, Brazil South, Canada Central, East Asia, East Us, France Central, India Central, India West, Korea Central, Korea South, North Europa, Sydafrika Nord, Sydostasien, Storbritannien Södra, Västra Storbritannien, Västeuropa, Västra Usa 2.


#### <a name="m-series"></a>M-serien

M-serien finns i följande regioner: Östra USA, Norra Europa, Västeuropa, Västra USA 2.
M-serien kan också ha begränsad tillgänglighet i ytterligare regioner. Du kan begära en annan region än vad som anges här, men uppfyllelse i en annan region kanske inte är möjlig.

Om du vill aktivera M-seriens tillgänglighet i en prenumeration måste åtkomst begäras genom [att en ny supportbegäran lämnas in](#create-a-support-request-to-enable-m-series).


##### <a name="create-a-support-request-to-enable-m-series"></a>Skapa en supportbegäran för att aktivera M-serien: 

1. Välj **Hjälp + stöd** i portalen.
2. Välj **Ny supportbegäran**.

På sidan **Grunderna** anger du följande:

1. För **Ärendetyp**väljer du **Tjänst- och prenumerationsgränser (kvoter)**.
2. För **Prenumeration** = välj prenumerationen för att aktivera M-serien.
3. För **Kvottyp**väljer du **SQL-databas**.
4. Välj **Nästa** om du vill gå till sidan **Information.**

Ange följande på sidan **Information:**

1. I avsnittet **PROBLEMINFORMATION** väljer du länken **Ange information.** 
2. För **SQL Database kvottyp** välj **M-serien**.
3. För **Region**väljer du den region som ska aktivera M-serien.
    För regioner där M-serien är tillgänglig finns i [M-seriens tillgänglighet](#m-series).

Godkända supportbegäranden uppfylls vanligtvis inom 5 arbetsdagar.


## <a name="next-steps"></a>Nästa steg

- Information om hur du skapar en SQL-databas finns i [Skapa en SQL-databas med Azure-portalen](sql-database-single-database-get-started.md).
- De specifika beräkningsstorlekar och lagringsstorleksalternativ som är tillgängliga för enskilda databaser finns i [SQL Database vCore-baserade resursgränser för enskilda databaser](sql-database-vcore-resource-limits-single-databases.md).
- För de specifika beräkningsstorlekar och lagringsstorleksalternativ som är tillgängliga för elastiska pooler finns i [SQL Database vCore-baserade resursgränser för elastiska pooler](sql-database-vcore-resource-limits-elastic-pools.md).
- Information om priser finns på [prissidan](https://azure.microsoft.com/pricing/details/sql-database/single/)för Azure SQL Database .
