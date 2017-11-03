---
title: "Utforma och implementera en Oracle-databas på Azure | Microsoft Docs"
description: "Utforma och implementera en Oracle-databas i Azure-miljön."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: v-shiuma
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 6/22/2017
ms.author: rclaus
ms.openlocfilehash: c8f858bf249c4b56ad4fe60654ab489676eceb1f
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2017
---
# <a name="design-and-implement-an-oracle-database-in-azure"></a>Utforma och implementera en Oracle-databas i Azure

## <a name="assumptions"></a>Antaganden

- Du planerar att migrera en Oracle-databas från lokal till Azure.
- Du har en förståelse av de olika mätvärdena i Oracle AWR rapporter.
- Du har en grundläggande förståelse för programmets prestanda och användning av plattform.

## <a name="goals"></a>Mål

- Förstå hur du optimerar distributionen Oracle i Azure.
- Utforska prestandajustering alternativ för en Oracle-databas i en Azure-miljö.

## <a name="the-differences-between-an-on-premises-and-azure-implementation"></a>Skillnader mellan en lokal och Azure-implementering 

Följande är några viktiga saker att tänka på när du migrerar lokala program till Azure. 

En viktig skillnad är att resurser som virtuella datorer, diskar och virtuella nätverk i en Azure-implementering måste delas med andra klienter. Dessutom kan resurser vara begränsas baserat på kraven. I stället för att fokusera på att undvika misslyckas (MTBF), fokuserar Azure mer på kvarvarande felet (MTTR).

I följande tabell visas några av skillnaderna mellan en lokal implementering och en Azure implementering av en Oracle-databas.

> 
> |  | **Lokal implementering** | **Azure-implementering** |
> | --- | --- | --- |
> | **Nätverk** |LAN/WAN  |SDN (programvarudefinierat nätverk)|
> | **Säkerhetsgrupp** |IP-port begränsning verktyg |[Nätverkssäkerhetsgrupp (NSG)](https://azure.microsoft.com/blog/network-security-groups) |
> | **Återhämtning** |MTBF (Genomsnittlig tid mellan fel) |MTTR (tiden till återställning)|
> | **Planerat underhåll** |Uppdatering/uppgradering|[Tillgänglighetsuppsättningar](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines) (korrigering/uppgraderingar hanteras av Azure) |
> | **Resurs** |Dedikerad  |Delas med andra klienter|
> | **Regioner** |Datacenter |[Region-par](https://docs.microsoft.com/azure/virtual-machines/windows/regions-and-availability)|
> | **Storage** |SAN/fysiska diskar |[Hanteras av Azure storage](https://azure.microsoft.com/pricing/details/managed-disks/?v=17.23h)|
> | **Skalning** |Lodrät skala |Horisontell skalning|


### <a name="requirements"></a>Krav

- Bestämma storlek och tillväxt hastighet för databasen.
- Fastställ kraven som IOPS, vilket du kan beräkna baserat på Oracle AWR rapporter eller andra verktyg för nätverksövervakning.

## <a name="configuration-options"></a>Konfigurationsalternativ

Det finns fyra möjliga problemområden som du kan finjustera för att förbättra prestanda i en Azure-miljö:

- Storlek på virtuell dator
- Dataflödet i nätverket
- Disktyper och konfigurationer
- Inställningar för cachelagring av disk

### <a name="generate-an-awr-report"></a>Generera en rapport för AWR

Om du har en befintlig en Oracle-databas och planerar att migrera till Azure, har du flera alternativ. Du kan köra rapporten för Oracle AWR att hämta mätvärden (IOPS, Mbit/s, GiBs och så vidare). Välj den virtuella datorn utifrån de mätvärden som samlats in. Eller så kan du kontakta din infrastruktur-teamet för att få liknande information.

Du kan även köra rapporten AWR under både vanliga och högsta antal arbetsbelastningar, så kan du jämföra. Baserat på dessa rapporter, du kan ändra storlek på de virtuella datorerna baserat på den genomsnittliga arbetsbelastningen eller maximal arbetsbelastning.

Följande är ett exempel på hur du skapar en rapport över AWR:

```bash
$ sqlplus / as sysdba
SQL> EXEC DBMS_WORKLOAD_REPOSITORY.CREATE_SNAPSHOT;
SQL> @?/rdbms/admin/awrrpt.sql
```

### <a name="key-metrics"></a>Viktiga mått

Följande är de mätvärden som kan hämtas från AWR rapporten:

- Totalt antal kärnor
- CPU-klockfrekvens
- Totalt minne i GB
- CPU-användning
- Högsta överföringshastighet
- Antalet i/o-ändringar (läsa/skriva)
- Gör om loggen hastighet (MBPs)
- Dataflödet i nätverket
- Nätverket latens hastighet (låg/hög)
- Databasens storlek i GB
- Byte som tagits emot via SQL * Net från/till klienten

### <a name="virtual-machine-size"></a>Storlek på virtuell dator

#### <a name="1-estimate-vm-size-based-on-cpu-memory-and-io-usage-from-the-awr-report"></a>1. VM-storlek för uppskattning baserat på CPU, minne och i/o-användning i rapporten AWR

En sak som du kan titta på är översta fem tidsinställd förgrunden händelser som indikerar om systemet flaskhalsar är.

I följande diagram visas exempelvis filsynkronisering loggen överst. Anger antalet väntar som krävs innan LGWR skriver Loggningsbufferten till loggfilen gör om. Dessa resultat indikerar att bättre prestanda lagring eller diskar är nödvändiga. Diagrammet visar dessutom också antalet CPU (kärnor) och mängden minne.

![Skärmbild av sidan AWR](./media/oracle-design/cpu_memory_info.png)

Följande diagram visar den totala i/o för läsning och skrivning. Det fanns 59 läsa och 247.3 GB skrivs vid tidpunkten för rapporten.

![Skärmbild av sidan AWR](./media/oracle-design/io_info.png)

#### <a name="2-choose-a-vm"></a>2. Välj en virtuell dator

Baserat på information som du samlade in i rapporten AWR är nästa steg att välja en virtuell dator med samma storlek som uppfyller dina krav. Du hittar en lista med tillgängliga virtuella datorer i artikeln [Minnesoptimerade](../../linux/sizes-memory.md).

#### <a name="3-fine-tune-the-vm-sizing-with-a-similar-vm-series-based-on-the-acu"></a>3. Finjustera VM-storlek med liknande VM baserat på ACU

När du har valt den virtuella datorn, kan du vara uppmärksam på ACU för den virtuella datorn. Du kan välja en annan virtuell dator baserat på värdet ACU som bättre passar dina behov. Mer information finns i [Azure compute enhet](https://docs.microsoft.com/azure/virtual-machines/windows/acu).

![Skärmbild av sidan ACU enheter](./media/oracle-design/acu_units.png)

### <a name="network-throughput"></a>Dataflödet i nätverket

Följande diagram visar relationen mellan genomflöde och IOPS:

![Skärmbild av dataflöde](./media/oracle-design/throughput.png)

Det totala genomflödet beräknas baserat på följande information:
- SQL * Net trafik
- Mbit/s x antal servrar (till exempel Oracle Data Guard utgående ström)
- Andra faktorer, till exempel program replikering

![Skärmbild av SQL * Net genomflöde](./media/oracle-design/sqlnet_info.png)

Baserat på din kraven på nätverksbandbredd finns olika typer av gateway som du kan välja från. Dessa inkluderar basic VpnGw och Azure ExpressRoute. Mer information finns i [VPN-gateway sida med priser](https://azure.microsoft.com/en-us/pricing/details/vpn-gateway/?v=17.23h).

**Rekommendationer**

- Nätverksfördröjningen är högre jämfört med en lokal distribution. Minskar nätverket avrunda resor kan kraftigt förbättra prestanda.
- Om du vill minska turer konsolidera program som har hög transaktioner eller ”chatty” appar i samma virtuella dator.

### <a name="disk-types-and-configurations"></a>Disktyper och konfigurationer

- *OS-diskar som standard*: dessa disktyper erbjuder beständiga data och cachelagring. De är optimerade för OS åtkomst vid start och inte har utformats för antingen transaktionella eller datalager (Analytiska) arbetsbelastningar.

- *Ohanterad diskar*: dessa disktyper du hantera de lagringskonton som lagra filer för virtuell hårddisk (VHD) som motsvarar dina VM-diskar. VHD-filer lagras som sidblobbar i Azure storage-konton.

- *Hanterade diskar*: hanteras av Azure storage-konton som du använder för din Virtuella diskar. Anger typ av disk (premium eller standard) och storleken på den disk som du behöver. Azure skapar och hanterar disken åt dig.

- *Premium-lagringsdiskar*: dessa disktyper som passar bäst för produktionsarbetsbelastningar. Premium-lagring stöder Virtuella diskar som kan kopplas till specifika storlek-serien virtuella datorer, till exempel DS, DSv2, GS och F serien virtuella datorer. Premium-disk som levereras med olika storlekar och du kan välja mellan diskar mellan 32 GB och 4 096 GB. Varje diskstorleken har sin egen prestandakrav. Beroende på kraven för application kan du koppla en eller flera diskar till den virtuella datorn.

När du skapar en ny hanterade disk från portalen kan du välja den **kontotyp** för typ av disk du vill använda. Tänk på att inte alla tillgängliga diskar visas i den nedrullningsbara menyn. När du har valt en viss VM-storlek visas på menyn endast tillgängliga premium-lagring SKU: er som är baserade på att VM-storlek.

![Skärmbild av sidan hanterade diskar](./media/oracle-design/premium_disk01.png)

Mer information finns i [Premium-lagring med hög prestanda och hanterade diskar för virtuella datorer](https://docs.microsoft.com/azure/storage/storage-premium-storage).

När du har konfigurerat din lagring på en virtuell dator kanske du vill läsa in testa diskar innan du skapar en databas. Veta i/o-hastighet på både svarstid och genomströmning kan hjälpa dig att avgöra om de virtuella datorerna har stöd för det förväntade genomflödet med latens mål.

Det finns ett antal verktyg programtest belastning, till exempel Oracle Orion, Sysbench och Fio.

Kör testet Läs in igen när du har distribuerat en Oracle-databas. Starta din regelbundet och högsta antal arbetsbelastningar och resultaten visar baslinje för din miljö.

Det kan vara viktigare storleken baseras på IOPS-hastighet i stället för lagringsstorleken lagringen. Till exempel om det obligatoriska IOPs-värdet är 5 000, men du behöver bara 200 GB, kan du fortfarande få P30 klassen premium disken även om den innehåller fler än 200 GB lagringsutrymme.

Hastigheten med IOPS kan hämtas från AWR rapporten. Det bestäms av gör om-loggen, fysiska läsningar och skrivningar hastighet.

![Skärmbild av sidan AWR](./media/oracle-design/awr_report.png)

Gör om storleken är till exempel 12,200,000 byte per sekund som är lika med 11.63 Mbit/s.
IOPS är 12 200 000 / 2,358 = 5,174.

När du har en tydlig bild av i/o-kraven kan välja du en kombination av enheter som är bäst att uppfylla kraven.

**Rekommendationer**

- För data fördelade tabellutrymmet, i/o-arbetsbelastning på ett antal diskar med hjälp av hanterade lagringspoolen eller Oracle ASM.
- I/o-blockstorlek ökar för läsintensiva- och skrivåtgärder-intensiva, lägga till flera datadiskar.
- Öka blockstorleken för stora sekventiella processer.
- Du kan använda komprimering för att minska i/o (för både data och index).
- Avgränsa gör om loggfiler, system och temps och ångra TS på separata hårddiskar.
- Placera inte några programfiler på standard OS-diskar (/ dev/sda). Diskarna inte är optimerad för snabb VM Start gånger, och de kan inte ange goda prestanda för ditt program.

### <a name="disk-cache-settings"></a>Inställningar för cachelagring av disk

Det finns tre alternativ för värdcachelagring:

- *Skrivskyddad*: alla begäranden cachelagras för framtida läsningar. Alla skrivningar beständiga direkt till Azure Blob storage.

- *Skrivskyddad*: Detta är en ”read-ahead” algoritm. Läsning och skrivning cachelagras för framtida läsningar. Icke skrivning via skrivningar beständiga först till den lokala cachen. För SQL Server sparas skrivningar till Azure Storage eftersom den använder skrivning via. Det ger också den lägsta fördröjningen för disken för enstaka arbetsbelastningar.

- *Ingen* (inaktiverat): med det här alternativet kan du kringgå cachen. Alla data som överförs till disk och beständiga till Azure Storage. Den här metoden ger den högsta i/o-hastigheten för i/o-intensiv arbetsbelastning. Du måste också beakta ”transaktionen kostnad”.

**Rekommendationer**

För att maximera genomströmningen, rekommenderar vi att du börjar med **ingen** för cachelagring av värden. För Premium-lagring, Tänk på att du inaktiverar ”barriärer” när du monterar filsystemet med den **ReadOnly** eller **ingen** alternativ. Uppdatera filen /etc/fstab med UUID till diskarna.

Mer information finns i [Premium-lagring för virtuella Linux-datorer](https://docs.microsoft.com/azure/storage/storage-premium-storage#premium-storage-for-linux-vms).

![Skärmbild av sidan hanterade diskar](./media/oracle-design/premium_disk02.png)

- Använd standard för OS diskar **läsning och skrivning** cachelagring.
- Använd för SYSTEM, TEMP och ångra **ingen** för cachelagring.
- DATA, Använd **ingen** för cachelagring. Men om databasen är skrivskyddad eller läsintensiva använder **skrivskyddad** cachelagring.

När inställningen disk data sparas, kan du inte ändra värden cache-inställningen om du inte demontera enheten på OS-nivån och sedan montera när du har gjort ändringen.


## <a name="security"></a>Säkerhet

När du har skapat och konfigurerat Azure-miljön, är nästa steg att skydda nätverket. Här följer några rekommendationer:

- *NSG princip*: NSG kan definieras av ett undernät eller nätverkskort. Det är enklare att styra åtkomsten på undernätverksnivån både för säkerhet och kraft routning för till exempel brandväggar för programmet.

- *Jumpbox*: för säkrare åtkomst administratörer bör inte ansluter direkt till programtjänsten eller databasen. En jumpbox används som en media mellan administratören dator och Azure-resurser.
![Skärmbild av sidan Jumpbox-topologi](./media/oracle-design/jumpbox.png)

    Administratören datorn bör erbjuda IP tillgång till jumpbox endast. Jumpbox ska ha åtkomst till programmet och databasen.

- *Privat nätverk* (undernät): Vi rekommenderar att du har programtjänsten och databasen på olika undernät, så kan ställa in bättre kontroll av NSG principen.


## <a name="additional-reading"></a>Ytterligare resurser

- [Konfigurera Oracle ASM](configure-oracle-asm.md)
- [Konfigurera Oracle Data Guard](configure-oracle-dataguard.md)
- [Konfigurera Oracle guld Gate](configure-oracle-golden-gate.md)
- [Oracle-säkerhetskopiering och återställning](oracle-backup-recovery.md)

## <a name="next-steps"></a>Nästa steg

- [Självstudier: Skapa högtillgängliga virtuella datorer](../../linux/create-cli-complete.md)
- [Utforska VM distribution Azure CLI-exempel](../../linux/cli-samples.md)
