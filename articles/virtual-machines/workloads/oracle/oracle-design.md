---
title: Utforma och implementera en Oracle-databas på Azure | Microsoft Docs
description: Utforma och implementera en Oracle-databas i Azure-miljön.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: romitgirdhar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: rogirdh
ms.openlocfilehash: d4c0bbdfb1afcef33727ba4b5b432c5de79168d4
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2018
ms.locfileid: "39495228"
---
# <a name="design-and-implement-an-oracle-database-in-azure"></a>Utforma och implementera en Oracle-databas i Azure

## <a name="assumptions"></a>Antaganden

- Du planerar att migrera en Oracle-databas från en lokal plats till Azure.
- Du har en förståelse för de olika mått i Oracle AWR rapporter.
- Du har en grundläggande förståelse för programmets prestanda och användning av plattformen.

## <a name="goals"></a>Mål

- Förstå hur du optimerar dina Oracle-distribution i Azure.
- Utforska alternativen för en Oracle-databas i en Azure-miljö för prestandajustering.

## <a name="the-differences-between-an-on-premises-and-azure-implementation"></a>Skillnaderna mellan en lokal och Azure-implementering 

Här följer några viktiga saker att tänka på när du migrerar lokala program till Azure. 

En viktig skillnad är att resurser som virtuella datorer, diskar och virtuella nätverk delas med andra klienter i en Azure-implementering. Dessutom kan resurser vara begränsade baserat på kraven. I stället för att fokusera på att undvika misslyckas (MTBF), fokuserade Azure mer på kvarvarande fel (MTTR).

I följande tabell visas några av skillnaderna mellan en lokal implementering och en Azure implementering av en Oracle-databas.

> 
> |  | **Lokal implementering** | **Azure-implementering** |
> | --- | --- | --- |
> | **Nätverk** |LAN/WAN  |SDN (programvarudefinierade nätverk)|
> | **Säkerhetsgrupp** |IP-adress/port begränsning av verktyg |[Nätverkssäkerhetsgrupp (NSG)](https://azure.microsoft.com/blog/network-security-groups) |
> | **Återhämtning** |MTBF (Genomsnittlig tid mellan fel) |MTTR (tiden för återställning)|
> | **Planerat underhåll** |Uppdateringar/uppgraderingar|[Tillgänglighetsuppsättningar](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines) (uppdateringar/uppgraderingar hanteras av Azure) |
> | **Resurs** |Dedikerad  |Delas med andra klienter|
> | **Regioner** |Datacenter |[Regionpar](https://docs.microsoft.com/azure/virtual-machines/windows/regions-and-availability)|
> | **Storage** |SAN/fysiska diskar |[Azure-hanterade lagring](https://azure.microsoft.com/pricing/details/managed-disks/?v=17.23h)|
> | **Skalning** |Lodrät skalning |Horisontell skalning|


### <a name="requirements"></a>Krav

- Bestämma den databas storlek och tillväxt.
- Fastställa krav för IOPS, som du kan beräkna baserat på Oracle AWR rapporter eller andra verktyg för nätverksövervakning.

## <a name="configuration-options"></a>Konfigurationsalternativ

Det finns fyra potentiella områden som du kan finjustera för att förbättra prestanda i en Azure-miljö:

- Virtuell datorstorlek
- Nätverkets dataflöde
- Disktyper och konfigurationer
- Inställningar för cachelagring av disk

### <a name="generate-an-awr-report"></a>Generera en rapport för AWR

Om du har en befintlig en Oracle-databas och planerar att migrera till Azure, har du flera alternativ. Du kan köra Oracle AWR rapporten för att hämta mått (IOPS, Mbit/s, GiBs och så vidare). Välj sedan den virtuella datorn baserat på mått som du samlat in. Eller så kan du kontakta ditt team för infrastruktur för att få liknande information.

Kan du köra rapporten AWR under både vanliga och toppar arbetsbelastningar, så att du kan jämföra. Baserat på de här rapporterna kan du kan ändra storlek på de virtuella datorerna baserat på genomsnittlig arbetsbelastning eller maximal arbetsbelastning.

Nedan följer ett exempel på hur du skapar en AWR rapport:

```bash
$ sqlplus / as sysdba
SQL> EXEC DBMS_WORKLOAD_REPOSITORY.CREATE_SNAPSHOT;
SQL> @?/rdbms/admin/awrrpt.sql
```

### <a name="key-metrics"></a>Viktiga mått

Följande är de mått som du kan hämta från AWR rapporten:

- Totalt antal kärnor
- CPU-klockfrekvens
- Totalt minne i GB
- Processoranvändning
- Högsta överföringshastighet för data
- Frekvensen för i/o-ändringar (läsa/skriva)
- Gör om log hastighet (Mbit/s)
- Nätverkets dataflöde
- Nätverket svarstid hastighet (låg/hög)
- Databasens storlek i GB
- Byte som tagits emot via SQL * Net från/till klienten

### <a name="virtual-machine-size"></a>Virtuell datorstorlek

#### <a name="1-estimate-vm-size-based-on-cpu-memory-and-io-usage-from-the-awr-report"></a>1. Uppskattningen VM-storlek baserat på processor, minne och i/o-användning i rapporten AWR

En sak som du kan titta på är de översta fem tidsinställda förgrunden händelser som anger var flaskhalsarna system finns.

I följande diagram är exempelvis log-filsynkronisering högst upp. Den anger hur många väntar som krävs innan LGWR skriver log-buffert i loggfilen för gör om. Dessa resultat indikerar att snabbare lagring eller diskar är nödvändiga. Diagrammet visar dessutom också antalet CPU (kärnor) och mängden minne.

![Skärmbild av sidan AWR](./media/oracle-design/cpu_memory_info.png)

Följande diagram visar den totala i/o för läsning och skrivning. Det fanns 59 läsa och 247.3 GB skrivs vid tidpunkten för rapporten.

![Skärmbild av sidan AWR](./media/oracle-design/io_info.png)

#### <a name="2-choose-a-vm"></a>2. Välj en virtuell dator

Utifrån den information du samlade in i rapporten AWR är nästa steg att välja en virtuell dator av en liknande storlek som uppfyller dina krav. Du hittar en lista över tillgängliga virtuella datorer i artikeln [Minnesoptimerade](../../linux/sizes-memory.md).

#### <a name="3-fine-tune-the-vm-sizing-with-a-similar-vm-series-based-on-the-acu"></a>3. Finjustera VM-storlek med liknande virtuella datorer baserat på ACU

När du har valt den virtuella datorn, uppmärksam på ACU för den virtuella datorn. Du kan välja en annan virtuell dator baserat på ACU-värdet som passar dina behov bättre. Mer information finns i [Azure compute-enhet](https://docs.microsoft.com/azure/virtual-machines/windows/acu).

![Skärmbild av sidan ACU enheter](./media/oracle-design/acu_units.png)

### <a name="network-throughput"></a>Nätverkets dataflöde

Följande diagram visar relationen mellan dataflöde och allokerad IOPS:

![Skärmbild av dataflöde](./media/oracle-design/throughput.png)

Totalt antal nätverkets genomflöde beräknas baserat på följande information:
- SQL * Net trafik
- Mbit/s x antalet servrar (utgående stream, till exempel Oracle Data Guard)
- Andra faktorer, till exempel programreplikering

![Skärmbild av SQL * Net dataflöde](./media/oracle-design/sqlnet_info.png)

Baserat på dina krav på bandbredd, finns det olika gatewaytyper av där du kan välja från. Dessa inkluderar basic, VpnGw och Azure ExpressRoute. Mer information finns i den [VPN-gateway sidan med priser](https://azure.microsoft.com/pricing/details/vpn-gateway/?v=17.23h).

**Rekommendationer**

- Svarstid för nätverk är högre jämfört med en lokal distribution. Minska nätverk tur och RETUR kan avsevärt förbättra prestanda.
- För att minska turer, konsolidera program som har hög transaktioner eller ”trafikintensiva” appar i samma virtuella dator.

### <a name="disk-types-and-configurations"></a>Disktyper och konfigurationer

- *OS-diskar som standard*: dessa disktyper erbjuder beständiga data och cachelagring. De är optimerade för åtkomst till OS vid start och inte har utformats för antingen transaktionella eller datalager (Analytiska) arbetsbelastningar.

- *Ohanterade diskar*: du ska hantera de lagringskonton som lagra filerna för virtuell hårddisk (VHD) som motsvarar dina VM-diskar med dessa disktyper. VHD-filer som lagras som sidblobar i Azure storage-konton.

- *Hanterade diskar*: hanteras av Azure storage-konton som du använder för dina VM-diskar. Anger typ av disk (premium eller standard) och storleken på den disk som du behöver. Azure skapar och hanterar disken åt dig.

- *Premium-lagringsdiskar*: dessa disktyper är bäst lämpade för arbetsbelastningar under produktion. Premium storage stöder VM-diskar som kan kopplas till specifika storlek-seriens virtuella datorer, till exempel DS, DSv2, GS och F-serien virtuella datorer. Premium-diskar med olika storlekar, och du kan välja mellan diskar som sträcker sig från 32 GB till 4 096 GB. Varje diskstorleken har sin egen prestandakrav. Du kan koppla en eller flera diskar till din virtuella dator beroende på dina programkrav.

När du skapar en ny hanterad disk från portalen kan du välja den **kontotyp** för typ av disk som du vill använda. Tänk på att inte alla tillgängliga diskar visas i den nedrullningsbara menyn. När du har valt en VM-storleken visas på menyn endast de tillgängliga premiumlagring SKU: er som är baserade på den VM-storleken.

![Skärmbild av sidan hanterad disk](./media/oracle-design/premium_disk01.png)

Mer information finns i [högpresterande Premium Storage och hanterade diskar för virtuella datorer](https://docs.microsoft.com/azure/storage/storage-premium-storage).

När du har konfigurerat din lagring på en virtuell dator kanske du vill läsa in testa diskarna innan du skapar en databas. Att känna till den i/o-hastigheten vad gäller både svarstid och dataflöde kan hjälpa dig att avgöra om de virtuella datorerna har stöd för det förväntade dataflödet med målen för svarstid.

Det finns ett antal verktyg för programmet belastningstest som Oracle Orion, Sysbench och Fio.

Kör belastningstestet igen när du har distribuerat en Oracle-databas. Starta dina regelbundet och toppar arbetsbelastningar, och resultatet visar baslinje för din miljö.

Det kan vara viktigare att storleksanpassa lagring baserat på IOPS-hastighet i stället för lagringsutrymmet. Till exempel om det krävda IOPs-värdet är 5 000, men du behöver bara 200 GB, kan du fortfarande få P30 klass premiumdisk trots att den innehåller fler än 200 GB lagringsutrymme.

IOPS-hastighet kan hämtas från AWR rapporten. Det bestäms av den gör om log, fysiska läsningar och skrivningar snabbt.

![Skärmbild av sidan AWR](./media/oracle-design/awr_report.png)

Gör om storleken är till exempel 12,200,000 byte per sekund, vilket är lika med 11.63 Mbit/s.
IOPS är 12 200 000 / 2,358 = 5,174.

När du har en tydlig bild av i/o-kraven kan välja du en kombination av enheter som är bäst för att uppfylla dessa krav.

**Rekommendationer**

- För data sprids registerutrymme, i/o-arbetsbelastningen över flera diskar med hjälp av hanterad lagring eller Oracle ASM.
- Eftersom i/o-blockstorlek ökar för läsintensiva och skrivningsintensiva, kan du lägga till flera datadiskar.
- Öka blockstorleken för stora sekventiella processer.
- Använd komprimering för att minska i/o (för både data och index).
- Separera gör om loggar, system och temps och ångra TS på separata hårddiskar.
- Placera inte några programfiler på standard OS-diskar (/ dev/sda). De här diskarna inte är optimerade för snabb VM Start gånger, och de ger inte bra prestanda för ditt program.

### <a name="disk-cache-settings"></a>Inställningar för cachelagring av disk

Det finns tre alternativ för värdcachelagring:

- *Skrivskyddad*: alla begäranden cachelagras för framtida läsningar. Alla skrivåtgärder sparas direkt till Azure Blob storage.

- *Läs-och*: det här är en ”read-ahead” algoritm. Läsningar och skrivningar cachelagras för framtida läsningar. Icke-write-through skrivningar har sparats till den lokala cachen först. För SQL Server sparas skrivningar till Azure Storage eftersom den använder write-through. Det ger också disk kortast svarstid för lätta arbetsbelastningar.

- *Ingen* (inaktiverat): med det här alternativet kan du hoppa över cacheminnet. Alla data som överförs till disk och beständiga i Azure Storage. Den här metoden ger dig den högsta i/o-hastigheten för i/o-intensiva arbetsbelastningar. Du måste också beakta ”transaktionskostnaden”.

**Rekommendationer**

Om du vill maximera dataflödet rekommenderar vi att du börjar med **ingen** för värdcachelagring. För Premium Storage, Tänk på att du måste inaktivera ”hinder” när du monterar filsystemet med den **ReadOnly** eller **ingen** alternativ. Uppdatera filen/etc/fstab med UUID till diskarna.

Mer information finns i [Premiumlagring för virtuella Linux-datorer](https://docs.microsoft.com/azure/storage/storage-premium-storage#premium-storage-for-linux-vms).

![Skärmbild av sidan hanterad disk](./media/oracle-design/premium_disk02.png)

- Använd standard för OS-diskar **Läs/Skriv** cachelagring.
- Använd för SYSTEM och TEMP ångra **ingen** för cachelagring.
- DATA, använda **ingen** för cachelagring. Men om databasen är skrivskyddad eller läsintensiva använder **skrivskyddad** cachelagring.

När dina data disk inställningen sparas, kan du inte ändra värden cache-inställningen om du inte koppla bort enheten på operativsystemsnivån och sedan montera den när du har gjort ändringen.


## <a name="security"></a>Säkerhet

När du har skapat och konfigurerat Azure-miljön, är nästa steg att skydda ditt nätverk. Här följer några rekommendationer:

- *Princip för NSG*: NSG definieras med ett undernät eller ett nätverkskort. Det är enklare att styra åtkomsten på undernätverksnivån både i för säkerhet och framtvinga routning för till exempel brandväggar för webbprogram.

- *Jumpbox*: för säkrare åtkomst administratörer bör inte ansluter direkt till programtjänsten eller databas. En jumpbox används som en media mellan administratör datorn och Azure-resurser.
![Skärmbild av sidan Jumpbox-topologi](./media/oracle-design/jumpbox.png)

    Administratören datorn bör erbjuda IP tillgång till jumpboxen endast. Jumpbox ska ha åtkomst till programmet och databasen.

- *Privat nätverk* (undernät): Vi rekommenderar att du har den programtjänsten och databasen på olika undernät så bättre kontroll kan ställas in av NSG-principen.


## <a name="additional-reading"></a>Ytterligare resurser

- [Konfigurera Oracle ASM](configure-oracle-asm.md)
- [Konfigurera Oracle Data Guard](configure-oracle-dataguard.md)
- [Konfigurera Oracle guld Gate](configure-oracle-golden-gate.md)
- [Oracle-säkerhetskopiering och återställning](oracle-backup-recovery.md)

## <a name="next-steps"></a>Nästa steg

- [Självstudie: Skapa virtuella datorer med hög tillgänglighet](../../linux/create-cli-complete.md)
- [Utforska Azure CLI-exempel för VM-distribution](../../linux/cli-samples.md)
