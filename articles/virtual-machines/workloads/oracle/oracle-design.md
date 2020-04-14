---
title: Designa och implementera en Oracle-databas på Azure | Microsoft-dokument
description: Designa och implementera en Oracle-databas i din Azure-miljö.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: mimckitt
ms.openlocfilehash: 41e1720dfeaa98a9d0bc2227c58083ce769b06e0
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263411"
---
# <a name="design-and-implement-an-oracle-database-in-azure"></a>Designa och implementera en Oracle-databas i Azure

## <a name="assumptions"></a>Antaganden

- Du planerar att migrera en Oracle-databas från lokalt till Azure.
- Du har [diagnostikpaketet](https://docs.oracle.com/cd/E11857_01/license.111/e11987/database_management.htm) för Oracle-databasen som du vill migrera
- Du har en förståelse för de olika måtten i Oracle AWR-rapporter.
- Du har en grundläggande förståelse för programprestanda och plattformsutnyttjande.

## <a name="goals"></a>Mål

- Förstå hur du optimerar din Oracle-distribution i Azure.
- Utforska alternativ för prestandajustering för en Oracle-databas i en Azure-miljö.

## <a name="the-differences-between-an-on-premises-and-azure-implementation"></a>Skillnaderna mellan en lokal och Azure-implementering 

Följande är några viktiga saker att tänka på när du migrerar lokala program till Azure. 

En viktig skillnad är att i en Azure-implementering delas resurser som virtuella datorer, diskar och virtuella nätverk mellan andra klienter. Dessutom kan resurser begränsas baserat på kraven. Istället för att fokusera på att undvika misslyckas (MTBF), azure är mer inriktad på att överleva felet (MTTR).

I följande tabell visas några av skillnaderna mellan en lokal implementering och en Azure-implementering av en Oracle-databas.

> 
> |  | **Lokal implementering** | **Azure-implementering** |
> | --- | --- | --- |
> | **Nätverk** |LAN/WAN  |SDN (programvarudefinierat nätverk)|
> | **Säkerhetsgrupp** |Begränsningsverktyg för IP/port |[Nätverkssäkerhetsgrupp (NSG)](https://azure.microsoft.com/blog/network-security-groups) |
> | **Motståndskraft** |MTBF (medeltid mellan fel) |MTTR (genomsnittlig tid till återhämtning)|
> | **Planerat underhåll** |Korrigering/uppgraderingar|[Tillgänglighetsuppsättningar](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines) (korrigering/uppgraderingar som hanteras av Azure) |
> | **Resurs** |Dedikerad  |Delas med andra klienter|
> | **Regioner** |Datacenter |[Regionpar](https://docs.microsoft.com/azure/virtual-machines/windows/regions#region-pairs)|
> | **Storage** |SAN/fysiska diskar |[Azure-hanterad lagring](https://azure.microsoft.com/pricing/details/managed-disks/?v=17.23h)|
> | **Skala** |Vertikal skala |Horisontell skalning|


### <a name="requirements"></a>Krav

- Bestäm databasens storlek och tillväxttakt.
- Bestäm IOPS-kraven, som du kan uppskatta baserat på OracleS AWR-rapporter eller andra verktyg för nätverksövervakning.

## <a name="configuration-options"></a>Konfigurationsalternativ

Det finns fyra potentiella områden som du kan ställa in för att förbättra prestanda i en Azure-miljö:

- Storlek för virtuell dator
- Nätverksdataflöde
- Disktyper och diskkonfigurationer
- Inställningar för diskcachen

### <a name="generate-an-awr-report"></a>Generera en AWR-rapport

Om du har en befintlig Oracle-databas och planerar att migrera till Azure har du flera alternativ. Om du har [diagnostikpaketet](https://www.oracle.com/technetwork/oem/pdf/511880.pdf) för Oracle-instanserna kan du köra Oracle AWR-rapporten för att hämta måtten (IOPS, Mbps, GiBs och så vidare). Välj sedan den virtuella datorn baserat på de mått som du har samlat in. Eller så kan du kontakta infrastrukturteamet för att få liknande information.

Du kan överväga att köra din AWR-rapport under både vanliga och högsta arbetsbelastningar, så att du kan jämföra. Baserat på dessa rapporter kan du ändra storlek på de virtuella datorerna baserat på antingen den genomsnittliga arbetsbelastningen eller den maximala arbetsbelastningen.

Nedan följer ett exempel på hur du skapar en AWR-rapport (Generera dina AWR-rapporter med din Oracle Enterprise Manager, om din aktuella installation har en):

```bash
$ sqlplus / as sysdba
SQL> EXEC DBMS_WORKLOAD_REPOSITORY.CREATE_SNAPSHOT;
SQL> @?/rdbms/admin/awrrpt.sql
```

### <a name="key-metrics"></a>Viktiga mått

Följande är de mått som du kan få från AWR-rapporten:

- Totalt antal kärnor
- CPU-klockhastighet
- Totalt minne i GB
- CPU-användning
- Högsta dataöverföringshastighet
- I/O-ändringar (läs/skriv)
- Gör om logghastighet (MBPs)
- Nätverksdataflöde
- Nätverksfördröjning (låg/hög)
- Databasstorlek i GB
- Byte som tas emot via SQL*Net från/till klient

### <a name="virtual-machine-size"></a>Storlek för virtuell dator

#### <a name="1-estimate-vm-size-based-on-cpu-memory-and-io-usage-from-the-awr-report"></a>1. Uppskatta VM-storlek baserat på CPU-, minnes- och I/O-användning från AWR-rapporten

En sak du kan titta på är de fem tidsinställda förgrundshändelser som visar var systemet flaskhalsar är.

I följande diagram finns till exempel loggfilssynkroniseringen överst. Det anger antalet väntetider som krävs innan LGWR skriver loggbufferten till omvärdet loggfilen. Dessa resultat visar att bättre prestanda lagring eller diskar krävs. Dessutom visar diagrammet också antalet PROCESSORER (kärnor) och mängden minne.

![Skärmbild av rapportsidan för AWR](./media/oracle-design/cpu_memory_info.png)

Följande diagram visar den totala I/O för läsning och skrivning. Det fanns 59 GB läsa och 247,3 GB skrivs under tidpunkten för rapporten.

![Skärmbild av rapportsidan för AWR](./media/oracle-design/io_info.png)

#### <a name="2-choose-a-vm"></a>2. Välj en virtuell dator

Baserat på den information som du samlat in från AWR-rapporten är nästa steg att välja en virtuell dator av liknande storlek som uppfyller dina krav. Du hittar en lista över tillgängliga virtuella datorer i artikeln [Minnesoptimerad](../../linux/sizes-memory.md).

#### <a name="3-fine-tune-the-vm-sizing-with-a-similar-vm-series-based-on-the-acu"></a>3. Finjustera vm-storleksändringen med en liknande VM-serie baserad på ACU

När du har valt den virtuella datorn bör du vara uppmärksam på ACU för den virtuella datorn. Du kan välja en annan virtuell dator baserat på det ACU-värde som passar dina behov bättre. Mer information finns i [Azure compute unit](https://docs.microsoft.com/azure/virtual-machines/windows/acu).

![Skärmbild av sidan ACU-enheter](./media/oracle-design/acu_units.png)

### <a name="network-throughput"></a>Nätverksdataflöde

Följande diagram visar förhållandet mellan dataflöde och IOPS:

![Skärmbild av dataflöde](./media/oracle-design/throughput.png)

Det totala nätverksflödet beräknas utifrån följande information:
- SQL*Nettotrafik
- MBps x antal servrar (utgående ström som Oracle Data Guard)
- Andra faktorer, till exempel applikationsreplikering

![Skärmbild av SQL*Net-dataflödet](./media/oracle-design/sqlnet_info.png)

Baserat på kraven på nätverksbandbredd finns det olika gatewaytyper som du kan välja mellan. Dessa inkluderar grundläggande, VpnGw och Azure ExpressRoute. Mer information finns på [prissidan för VPN-gatewayen](https://azure.microsoft.com/pricing/details/vpn-gateway/?v=17.23h).

**Rekommendationer**

- Nätverksfördröjningen är högre jämfört med en lokal distribution. Att minska nätverksrundningsresor kan avsevärt förbättra prestanda.
- För att minska tur och retur genom att konsolidera program som har höga transaktioner eller "pratsamma" appar på samma virtuella dator.
- Använd virtuella datorer med [accelererade nätverk](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) för bättre nätverksprestanda.
- För vissa Linux-avstrumationer kan du överväga att aktivera [TRIM/UNMAP-stöd](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm#trimunmap-support).
- Installera [Oracle Enterprise Manager](https://www.oracle.com/technetwork/oem/enterprise-manager/overview/index.html) på en separat virtuell dator.
- Enorma sidor är inte aktiverade på Linux som standard. Överväg att aktivera `use_large_pages = ONLY` stora sidor och ställ in på Oracle DB. Detta kan bidra till att öka prestanda. Mer information finns [här](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/USE_LARGE_PAGES.html#GUID-1B0F4D27-8222-439E-A01D-E50758C88390).

### <a name="disk-types-and-configurations"></a>Disktyper och diskkonfigurationer

- *Standard-OS-diskar:* Dessa disktyper erbjuder beständiga data och cachelagring. De är optimerade för OS-åtkomst vid start och är inte utformade för antingen transaktions- eller informationslagerarbetsbelastningar (analytiska) arbetsbelastningar.

- *Ohanterat diskar:* Med dessa disktyper hanterar du lagringskonton som lagrar VHD-filerna (Virtual Hard Disk) som motsvarar dina VM-diskar. VHD-filer lagras som sidblobar i Azure-lagringskonton.

- *Hanterade diskar*: Azure hanterar de lagringskonton som du använder för dina VM-diskar. Du anger disktypen (premium eller standard) och storleken på den disk som du behöver. Azure skapar och hanterar disken åt dig.

- *Premium-lagringsdiskar*: Dessa disktyper är bäst lämpade för produktionsarbetsbelastningar. Premiumlagring stöder VM-diskar som kan kopplas till virtuella datorer i storleksserien, till exempel virtuella datorer i DS, DSv2, GS och F-serien. Premiumdisken levereras med olika storlekar, och du kan välja mellan diskar från 32 GB till 4 096 GB. Varje diskstorlek har sina egna prestandaspecifikationer. Beroende på dina programkrav kan du koppla en eller flera diskar till den virtuella datorn.

När du skapar en ny hanterad disk från portalen kan du välja **kontotyp** för den typ av disk som du vill använda. Tänk på att inte alla tillgängliga diskar visas på den nedrullningsvänliga menyn. När du har valt en viss vm-storlek visar menyn endast tillgängliga premiumlagringssskuser som baseras på den virtuella datorns storlek.

![Skärmbild av den hanterade disksidan](./media/oracle-design/premium_disk01.png)

När du har konfigurerat lagringen på en virtuell dator kanske du vill läsa in testa diskarna innan du skapar en databas. Att känna till I/O-hastigheten både när det gäller svarstid och dataflöde kan hjälpa dig att avgöra om de virtuella datorerna stöder det förväntade dataflödet med svarstidsmål.

Det finns ett antal verktyg för testning av programbelastning, till exempel Oracle Orion, Sysbench och Fio.

Kör belastningstestet igen när du har distribuerat en Oracle-databas. Starta dina vanliga och högsta arbetsbelastningar, och resultaten visar dig baslinjen för din miljö.

Det kan vara viktigare att ändra storlek på lagringen baserat på IOPS-hastigheten i stället för lagringsstorleken. Om den nödvändiga IOPS är 5 000, men du bara behöver 200 GB, kan du fortfarande få premiumdisken i P30-klassen även om den levereras med mer än 200 GB lagringsutrymme.

IOPS-kursen kan erhållas från rapporten AWR. Det bestäms av omrubbningsloggen, fysiska läsningar och skrivfrekvens.

![Skärmbild av rapportsidan för AWR](./media/oracle-design/awr_report.png)

Omringsstorleken är till exempel 12 200 000 byte per sekund, vilket är lika med 11,63 MBPs.
IOPS är 12.200.000 / 2.358 = 5.174.

När du har en tydlig bild av I/O-kraven kan du välja en kombination av enheter som är bäst lämpade för att uppfylla dessa krav.

**Rekommendationer**

- För datatabellutrymme sprider du I/O-arbetsbelastningen över ett antal diskar med hjälp av hanterad lagring eller Oracle ASM.
- När I/O-blockstorleken ökar för läsintensiva och skrivintensiva åtgärder lägger du till fler datadiskar.
- Öka blockstorleken för stora sekventiella processer.
- Använd datakomprimering för att minska I/O (för både data och index).
- Separata gör om loggar, system och temps och ångraR TS på separata datadiskar.
- Placera inga programfiler på standard-OS-diskar (/dev/sda). Dessa diskar är inte optimerade för snabba starttider för virtuella datorer, och de kanske inte ger bra prestanda för ditt program.
- När du använder virtuella datorer i M-serien på Premium-lagring aktiverar du [Write Accelerator](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator) på omredares disk.

### <a name="disk-cache-settings"></a>Inställningar för diskcachen

Det finns tre alternativ för värdcachelagring:

- *ReadOnly*: Alla begäranden cachelagras för framtida läsningar. Alla skrivningar sparas direkt till Azure Blob-lagring.

- *ReadWrite*: Detta är en "read-ahead" algoritm. Läser och skriver cachelagras för framtida läsningar. Icke-skrivningar sparas först i den lokala cachen. Det ger också den lägsta diskfördröjningen för ljusarbetsbelastningar. Om du använder ReadWrite-cache med ett program som inte hanterar beständiga data som krävs kan data gå förlorade om den virtuella datorn kraschar.

- *Ingen* (inaktiverad): Genom att använda det här alternativet kan du kringgå cachen. Alla data överförs till disken och sparas till Azure Storage. Den här metoden ger dig den högsta I/O-hastigheten för I/O-intensiva arbetsbelastningar. Du måste också ta hänsyn till "transaktionskostnader".

**Rekommendationer**

För att maximera dataflödet rekommenderar vi att du börjar med **Ingen** för värdcachelagring. För Premium Storage bör du tänka på att du måste inaktivera "hinder" när du monterar filsystemet med **ReadOnly-** eller **None-alternativen.** Uppdatera filen /etc/fstab med UUID till diskarna.

![Skärmbild av den hanterade disksidan](./media/oracle-design/premium_disk02.png)

- För OS-diskar använder du **standardcache-cachelagring av läs-/skriv.**
- För SYSTEM, TEMP och ÅNGRA används **Ingen** för cachelagring.
- För DATA använder du **Ingen** för cachelagring. Men om databasen är skrivskyddad eller läsintensiv använder du **Skrivskyddad** cachelagring.

När datadiskinställningen har sparats kan du inte ändra inställningen för värdcachen om du inte avmonterar enheten på OS-nivå och sedan monterar om den när du har gjort ändringen.

## <a name="security"></a>Säkerhet

När du har konfigurerat och konfigurerat din Azure-miljö är nästa steg att skydda nätverket. Här är några rekommendationer:

- *NSG-princip*: NSG kan definieras av ett undernät eller nätverkskort. Det är enklare att styra åtkomsten på undernätsnivå, både för säkerhet och tvångsroutning för saker som programbrandvägger.

- *Jumpbox*: För säkrare åtkomst bör administratörer inte ansluta direkt till programtjänsten eller databasen. En jumpbox används som ett medium mellan administratörsmaskinen och Azure-resurser.
![Skärmbild av sidan Hoppboxtopologi](./media/oracle-design/jumpbox.png)

    Administratörsdatorn bör endast erbjuda IP-begränsad åtkomst till jumpboxen. Jumpboxen ska ha tillgång till programmet och databasen.

- *Privat nätverk* (undernät): Vi rekommenderar att du har programtjänsten och databasen på separata undernät, så bättre kontroll kan ställas in av NSG-principen.


## <a name="additional-reading"></a>Ytterligare läsning

- [Konfigurera Oracle ASM](configure-oracle-asm.md)
- [Konfigurera Oracle Data Guard](configure-oracle-dataguard.md)
- [Konfigurera Oracle Golden Gate](configure-oracle-golden-gate.md)
- [Oracle säkerhetskopiering och återställning](oracle-backup-recovery.md)

## <a name="next-steps"></a>Nästa steg

- [Självstudiekurs: Skapa virtuella datorer med hög tillgång](../../linux/create-cli-complete.md)
- [Utforska Azure CLI-exempel för VM-distribution](../../linux/cli-samples.md)
