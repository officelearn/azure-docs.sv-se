---
title: Installera SAP HANA på SAP HANA i Azure (stora instanser) | Microsoft Docs
description: Så här installerar du SAP HANA på en SAP HANA i Azure (stora instans).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/27/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ecef13f0ce97c7cec5a6583479911a08a99b0877
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37110736"
---
# <a name="how-to-install-and-configure-sap-hana-large-instances-on-azure"></a>Installera och konfigurera SAP HANA (stora instanser) på Azure

Följande är några viktiga definitioner du behöver veta innan du läser guiden. I [SAP HANA (stora instanser) översikt och arkitektur för Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) vi har fört två olika klasser av HANA stora instans enheter med:

- S72, S72m, S144, S144m, S192, S192m och S192xm som vi kallar ”typen I klassen' av SKU: er.
- S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm och S960m som vi kallar ”typ II class-av SKU: er.

Klass-specificerare kommer att användas i hela dokumentationen HANA stora instans att så småningom referera till olika funktioner och krav utifrån HANA stora instans SKU: er.

Övriga definitioner som vi använder ofta är:
- **Stora instans stämpel:** maskinvara infrastruktur-stacken, som är SAP HANA TDI certifierad och avsett för att köra SAP HANA-instanser i Azure.
- **SAP HANA i Azure (stora instanser):** officiellt namn för erbjudandet i Azure att köra HANA instanser i på SAP HANA TDI certifierad maskinvara som distribueras i stora instans tidsstämplar i olika Azure-regioner. Relaterade termen **HANA stora instans** kort för SAP HANA i Azure (stora instanser) och är ofta används den här tekniska distributionsguiden.


Installationen av SAP HANA är ditt ansvar och du kan starta aktiviteten efter leverans av en ny SAP HANA på Azure (stora instanser)-server. Och när anslutningen mellan din Azure-VNet(s) och HANA stora instans enhet(er) har upprättats. 

> [!Note]
> Installationen av SAP HANA måste utföras av en person som är certifierade för att utföra SAP HANA installationer per SAP-princip. En person som har klarat certifierade SAP-teknik associera examen, SAP HANA-Installation-certifiering, eller genom att en SAP-certifierade integrator (SI).

Kontrollera igen, särskilt när du planerar att installera HANA 2.0 [SAP stöd Obs #2235581 - SAP HANA: operativsystem som stöds](https://launchpad.support.sap.com/#/notes/2235581/E) för att se till att Operativsystemet stöds med SAP HANA släpper du valt att installera. Du upptäcker att stöds Operativsystemet för HANA 2.0 är mer begränsad operativsystem som stöds för HANA 1.0. 

## <a name="first-steps-after-receiving-the-hana-large-instance-units"></a>Första stegen när du har fått HANA stora instans enhet(er)

**Första steget** när du tar emot stora HANA-instansen och har etablerat åtkomst och anslutning till instanser, är att registrera OS på instansen med leverantören av OS. Det här steget inkluderar registrera ditt SUSE Linux-operativsystem i en instans av SUSE SMT som du behöver har distribuerats på en virtuell dator i Azure. HANA stora instans-enhet kan ansluta till den här SMT-instansen (se senare i den här dokumentationen). Eller Operativsystemet Red Hat måste vara registrerad med Red Hat prenumeration Manager måste du ansluta till. Se även anmärkningar i det här [dokument](https://docs.microsoft.com/azure/virtual-machines/linux/sap-hana-overview-architecture?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Det här steget är nödvändigt för att kunna uppdatera Operativsystemet. En aktivitet som är ansvarig för kunden. SUSE, Sök i dokumentationen för att installera och konfigurera SMT [här](https://www.suse.com/documentation/sles-12/book_smt/data/smt_installation.html).

**Andra steg** är att söka efter nya korrigeringsfiler och korrigeringar av specifika OS/versionen. Kontrollera om korrigeringsnivå av stora HANA-instansen på det aktuella tillståndet. Baserat på tidsinställningen för OS-korrigering/versioner och ändringar till Microsoft kan distribuera avbildningen, kan det finnas fall där de senaste korrigeringarna ingår inte kanske. Därför är det ett obligatoriskt steg när du har tagit över en HANA stora instans-enhet, kontrollera om publicerades korrigeringsprogram som är relevanta för säkerhet, funktioner, tillgänglighet och prestanda under tiden av viss Linux-leverantör och måste tillämpas.

**Tredje steget** är att checka ut den relevanta SAP-informationen för att installera och konfigurera SAP HANA på specifika OS/versionen. På grund av att ändra rekommendationer eller ändringar för SAP anteckningar eller konfigurationer som är beroende av enskilda installationsscenarier Microsoft kommer inte alltid kan ha en HANA stora instans-enhet som har konfigurerats helt. Därför är det obligatoriska du som kund att läsa SAP-information relaterad till SAP HANA på din exakt Linux-versionen. Även kontrollera konfigurationer OS/slutversionen nödvändigt och tillämpa konfigurationsinställningarna där det inte redan har gjort.

Kontrollera följande parametrar i specifika, och slutligen justeras till:

- NET.Core.rmem_max = 16777216
- NET.Core.wmem_max = 16777216
- NET.Core.rmem_default = 16777216
- NET.Core.wmem_default = 16777216
- NET.Core.optmem_max = 16777216
- net.ipv4.tcp_rmem = 65536 16777216 16777216
- net.ipv4.tcp_wmem = 65536 16777216 16777216

Från och med SLES12 SP1 och RHEL 7.2 kan måste dessa parametrar anges i en fil i katalogen /etc/sysctl.d. Till exempel måste en fil med namnet 91-NetApp-HANA.conf skapas. Parametrarna måste vara set in/etc/sysctl.conf för äldre SLES och RHEL versioner.

Alla RHEL släpper och från och med SLES12, den 
- sunrpc.tcp_slot_table_entries = 128

Parametern måste anges in/etc/modprobe.d/sunrpc-local.conf. Om filen inte finns, måste det först skapas genom att lägga till följande post: 
- alternativ sunrpc tcp_max_slot_table_entries = 128

**Fjärde steget** är att kontrollera din HANA stora instans enhet systemtiden. Instanserna distribueras med en tidszon för system som representerar platsen för Azure-regionen HANA stora instans stämpeln finns i. Du kan välja att ändra systemklockan eller tidszonen för de instanser som du äger. Gör detta och beställa fler instanser i din klient vara beredd att du behöver anpassa tidszonen för de nyligen levererat instanserna. Microsoft har ingen insikter om datorns tidszon konfigurera med instanser efter övergång. Nyligen distribuerade instanser kan därför inte anges i samma tidszon som du har ändrat till. Därför är det ditt ansvar som kund för att kontrollera och anpassa tidszonen för de instanser som överlämnas om det behövs. 

**Femte steget** är att kontrollera etc/hosts. Eftersom bladen hämta överlämnas har de olika IP-adresser som är tilldelade för olika ändamål (se nästa avsnitt). Kontrollera etc/hosts-filen. I fall där enheter läggs till i en befintlig klient inte förväntar dig att ha etc/hosts nyligen distribuerade system hanteras korrekt med IP-adresser för tidigare levererat system. Därför det är på du som kund för att kontrollera de korrekta inställningarna genom att en instans som nyligen distribuerade kan interagera och matcha namnen på tidigare distribuerade enheter i din klient. 

## <a name="networking"></a>Nätverk
Vi förutsätter att du följt rekommendationerna i utforma Azure-Vnet och ansluter dessa Vnet till HANA stora instanser som beskrivs i dessa dokument:

- [Översikt över SAP HANA (stora instans) och arkitektur på Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Infrastruktur för SAP HANA (stora instanser) och anslutningar på Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Det finns vissa information värt att nämna om nätverk enda enheter. Varje enhet HANA stora instans levereras med två eller tre IP-adresser som är tilldelade två eller tre NIC-portar på enheten. Tre IP-adresser används i HANA skalbar konfigurationer och HANA System Replication-scenario. En av IP-adresser som tilldelats nätverkskortet på enheten ligger utanför servern IP-adresspool som beskrivs i den [SAP HANA (stora instans) översikt och arkitektur för Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).

Se [HLI stöds scenarier](hana-supported-scenario.md) Läs Ethernet-information för din arkitektur.

## <a name="storage"></a>Storage

Lagringslayout för SAP HANA i Azure (stora instanser) konfigureras av SAP HANA på Azure Service Management via SAP rekommenderade riktlinjer enligt beskrivningen i [lagringskraven för SAP HANA](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) vitboken. Ungefärlig storlek på olika volymer med de olika HANA stora instanser SKU: er fick dokumenterade i [SAP HANA (stora instans) översikt och arkitektur för Azure](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Namnkonventionerna för lagringsvolymer finns i följande tabell:

| Lagringsanvändning | Monteringspunkt | Volymnamn | 
| --- | --- | ---|
| HANA data | /hana/data/SID/mnt0000<m> | Storage IP:/hana_data_SID_mnt00001_tenant_vol |
| HANA logg | /Hana/log/SID/mnt0000<m> | Lagring IP: / hana_log_SID_mnt00001_tenant_vol |
| HANA loggsäkerhetskopiering | /Hana/log/Backups | Lagring IP: / hana_log_backups_SID_mnt00001_tenant_vol |
| HANA delade | /Hana/Shared/SID | Lagring IP: / hana_shared_SID_mnt00001_tenant_vol/delade |
| usr/sap | /usr/SAP/SID | Lagring IP: / hana_shared_SID_mnt00001_tenant_vol/usr_sap |

Där SID = HANA instans System-ID 

Och = en intern uppräkning av åtgärder när du distribuerar en klient.

Som du ser delar HANA delade usr/sap samma volym. Nomenklaturen för monteringspunkter innehåller System-ID för HANA instanser samt mount-numret. Skala upp distributioner finns det bara en monteringspunkt som mnt00001. Medan i skalbar distribution visas så många monteringar som måste ha worker och master noder. För skalbar miljö, data, log, delad och kopplade till varje nod i konfigurationen för skalbara loggen Säkerhetskopiera volymer. För konfigurationer med flera instanser av SAP, en annan uppsättning volymer skapas och är kopplade till enheten HAN stora instans. Se [HLI stöds scenarier](hana-supported-scenario.md) lagring layout information för ditt scenario.

När du läser dokumentet och se ut en HANA stora instans-enhet upptäcker du att enheterna ingår i stället generösa diskvolymen för HANA-data och att vi har en volym HANA/loggsäkerhetskopiering. Orsaken till varför vi storlek HANA/data så stor är att vi erbjuder du som kund lagring ögonblicksbilderna använder den samma volymen. Det innebär mer lagringsutrymme ögonblicksbilder som du utför, mer utrymme som förbrukas av ögonblicksbilder i din tilldelade lagringsvolymer. HANA/loggsäkerhetskopiering volymen kan inte antas vara volymen för att placera säkerhetskopiorna av databasen i. Den storlek som ska användas som säkerhetskopieringsvolymen för säkerhetskopieringarna av transaktionsloggen HANA. I framtida snapshot versioner av lagring self service, vi gäller specifika volymen om du vill ha mer frekventa ögonblicksbilder. Och med mer frekventa replikeringar till disaster recovery plats om du vill att alternativet i för disaster recovery funktioner som tillhandahålls av HANA stora instans-infrastruktur. Mer information finns i [SAP HANA (stora instanser) med hög tillgänglighet och katastrofåterställning i Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 

Du kan köpa ytterligare lagringsutrymme kapacitet i steg om 1 TB utöver det lagringsutrymme som tillhandahålls. Den här ytterligare lagringsutrymme kan läggas till som nya volymer till en stor HANA-instanser.

Under onboarding med SAP HANA på Azure-tjänsthantering kunden som anger ett användar-ID (UID) och grupp-ID (GID) för gruppen sidadm användar- och sapsys (ex: 1000,500) är det nödvändigt att dessa samma värden som används under installationen av SAP HANA-system. Som du vill distribuera flera HANA instanser på en enhet kan hämta du flera uppsättningar av volymer (en uppsättning för varje instans). Därför vid tidpunkten för distribution måste du definiera:

- SID för olika HANA instanser (sidadm härleds ur den).
- Minne storlekar på olika HANA instanser. Eftersom minnesstorlek per instanser definierar storleken på volymerna i varje enskild volym-uppsättning.

Baserat på providern lagringsrekommendationer alternativen montera har konfigurerats för alla monterade volymer (utesluter Start LUN):

- NFS-rw drivrutiner = 4, hårt, timeo = 600, rsize = 1048576 wsize = 1048576, Intro noatime, låsa 0 0

Dessa montera punkter konfigureras i/etc/fstab som visas i följande bild:

![fstab av monterade volymer i HANA stora instans enhet](./media/hana-installation/image1_fstab.PNG)

Utdata från den kommandot df -h på en enhet S72m HANA stora instans ser ut som:

![fstab av monterade volymer i HANA stora instans enhet](./media/hana-installation/image2_df_output.PNG)


Lagringsstyrenhet och noderna i stora instans stämplar synkroniseras till NTP-servrar. Med du synkronisera SAP HANA på Azure (stora instanser)-enheter och virtuella Azure-datorer mot en NTP-server, bör det finnas några betydande tid drift sker mellan infrastruktur- och beräknings-enheter i Azure eller stora instans stämplar.

För att optimera SAP HANA till lagring som används under, bör du också ange följande parametrar för SAP HANA-konfiguration:

- max_parallel_io_requests 128
- async_read_submit på
- async_write_submit_active på
- alla async_write_submit_blocks
 
För SAP HANA 1.0 versioner upp till SPS12, dessa parametrar kan anges under installationen av SAP HANA-databas, enligt beskrivningen i [SAP Obs #2267798 - konfigurationen för SAP HANA-databas](https://launchpad.support.sap.com/#/notes/2267798)

Du kan också konfigurera parametrarna efter installationen för SAP HANA-databas med hjälp av hdbparam framework. 

Hdbparam framework har ersatts med SAP HANA 2.0. Därför måste parametrar anges med hjälp av SQL-kommandon. Mer information finns i [SAP Obs #2399079: eliminering av hdbparam i HANA 2](https://launchpad.support.sap.com/#/notes/2399079).

Se [HLI stöds scenarier](hana-supported-scenario.md) Läs lagringslayout för din arkitektur.

## <a name="operating-system"></a>Operativsystem

Växlingsutrymme bildens levererat OS är inställd på 2 GB enligt den [SAP stöd Obs #1999997 – vanliga frågor och svar: SAP HANA minne](https://launchpad.support.sap.com/#/notes/1999997/E). Någon annan inställning önskade måste ställas in av du som kund.

[SUSE Linux Enterprise Server 12 SP1 för SAP-program](https://www.suse.com/products/sles-for-sap/hana) fördelning av Linux som installerats för SAP HANA i Azure (stora instanser). Viss distributionen ger funktioner för SAP-specifika &quot;direkt&quot; (inklusive förinställda parametrar för att köra SAP på SLES effektivt).

Se [resurs bibliotek/faktablad](https://www.suse.com/products/sles-for-sap/resource-library#white-papers) på webbplatsen SUSE och [SAP på SUSE](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE) på SAP Community nätverk (SCN) för flera användbara resurser som rör distribution av SAP HANA på SLES (inklusive inställning hög Tillgänglighet, säkerhet härdning som är specifika för SAP-åtgärder med mera).

Ytterligare och användbara SAP på SUSE-relaterade länkar:

- [SAP HANA på platsen för SUSE Linux](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)
- [Bästa praxis för SAP: sätta replikering – SAP NetWeaver på SUSE Linux Enterprise 12](https://www.suse.com/docrepcontent/container.jsp?containerId=9113).
- [ClamSAP – SLES virusskydd för SAP](http://scn.sap.com/community/linux/blog/2014/04/14/clamsap--suse-linux-enterprise-server-integrates-virus-protection-for-sap) (inklusive SLES 12 för SAP-program).

SAP stöd Anmärkningar till implementera SAP HANA på SLES 12:

- [Stöd för SAP-kommentar #1944799 – SAP HANA riktlinjer för Installation av operativsystemet SLES](http://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html).
- [Stöd för SAP-kommentar #2205917 – SAP HANA DB rekommenderade OS-inställningar för SLES 12 för SAP-program](https://launchpad.support.sap.com/#/notes/2205917/E).
- [SAP stöd Obs! #1984787 – SUSE Linux Enterprise Server 12 installationsinformation](https://launchpad.support.sap.com/#/notes/1984787).
- [Stöd för SAP-kommentar #171356 – SAP-program på Linux: allmän Information](https://launchpad.support.sap.com/#/notes/1984787).
- [Stöd för SAP-kommentar #1391070 – Linux UUID lösningar](https://launchpad.support.sap.com/#/notes/1391070).

[Red Hat Enterprise Linux för SAP HANA](https://www.redhat.com/en/resources/red-hat-enterprise-linux-sap-hana) är ett annat erbjudande för att köra SAP HANA på HANA stora instanser. Versioner av RHEL 6.7 och 7.2 är tillgängliga. Kontrollera anteckningen i motsatt interna virtuella Azure-datorer där endast RHEL 7.2 och senare versioner stöds, HANA stora instanser stöder RHEL 6.7 samt. Men vi rekommenderar att du använder en RHEL 7.x version.

Ytterligare och användbara SAP på Red Hat relaterade länkar:
- [SAP HANA på Red Hat Linux plats](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+Red+Hat).

SAP stöd Anmärkningar till implementera SAP HANA på Red Hat:

- [Stöd för SAP-kommentar #2009879 - SAP HANA riktlinjer för Red Hat Enterprise Linux (RHEL) operativsystem](https://launchpad.support.sap.com/#/notes/2009879/E).
- [SAP stöd Obs! #2292690 - SAP HANA DB rekommenderas OS-inställningar för RHEL 7](https://launchpad.support.sap.com/#/notes/2292690).
- [SAP stöd Obs! #2247020 - SAP HANA DB rekommenderas OS-inställningar för RHEL 6.7](https://launchpad.support.sap.com/#/notes/2247020).
- [Stöd för SAP-kommentar #1391070 – Linux UUID lösningar](https://launchpad.support.sap.com/#/notes/1391070).
- [SAP stöd Obs! #2228351 - Linux SAP HANA-databas Service Pack 11 revision 110 (eller högre) på RHEL 6 eller SLES 11](https://launchpad.support.sap.com/#/notes/2228351).
- [SAP stöd Obs! #2397039 – vanliga frågor och svar SAP-på RHEL](https://launchpad.support.sap.com/#/notes/2397039).
- [Stöd för SAP-kommentar #1496410 - Red Hat Enterprise Linux 6.x: Installation och uppgradering](https://launchpad.support.sap.com/#/notes/1496410).
- [Stöd för SAP-kommentar #2002167 - Red Hat Enterprise Linux 7.x: Installation och uppgradering](https://launchpad.support.sap.com/#/notes/2002167).

## <a name="time-synchronization"></a>Tidssynkronisering

SAP-program som bygger på SAP NetWeaver arkitektur är känsliga på tidsskillnaderna för de olika komponenterna som utgör SAP-system. SAP ABAP kort Dumpar med fel rubriken ZDATE\_stor\_tid\_DIFF är troligen bekant, dessa kort Dumpar ut när systemklockan på olika servrar eller virtuella datorer går för långt ifrån varandra.

För SAP HANA i Azure (stora instanser) tidssynkronisering i Azure&#39;t gäller för enheter för beräkning i stora instans stämplar. Synkroniseringen kan inte användas för SAP-program som körs i enhetligt virtuella Azure-datorer som Azure garanterar ett system&#39;s tid är korrekt synkroniserad. Därför en gång till gång server måste ställas in som kan användas av SAP programservrar som kör på virtuella Azure-datorer och SAP HANA-databas instanser som körs på HANA stora instanser. Lagringsinfrastruktur i stora instans stämplar är tidsinställningen synkroniserad med NTP-servrar.

## <a name="setting-up-smt-server-for-suse-linux"></a>Konfigurera SMT server för SUSE Linux
SAP HANA stora instanser har inte direkt anslutning till Internet. Det är därför inte enkelt att registrera sådan enhet med OS-providern att ladda ned och tillämpa korrigeringar. En lösning kan vara att ställa in en SMT-server i en Azure VM för SUSE Linux. Medan Azure VM måste finnas i ett Azure-VNet som är anslutet till stora HANA-instansen. Med sådana en SMT-server kan HANA stora instans-enhet registrera och ladda ned korrigeringar. 

SUSE innehåller en större handbok om deras [prenumeration hanteringsverktyg för SLES 12 SP2](https://www.suse.com/documentation/sles-12/pdfdoc/book_smt/book_smt.pdf). 

Som villkor för installation av en SMT-server som uppfyller uppgiften för HANA stora instansen behöver du:

- Ett virtuellt Azure-nätverk som är ansluten till HANA stora instans ER kretsen.
- Ett konto som är associerad med en organisation, SUSE. Medan organisationen måste ha en giltig prenumeration SUSE.

### <a name="installation-of-smt-server-on-azure-vm"></a>Installation av SMT server på Azure VM

I det här steget kan installera du SMT-server i en Azure VM. Den första åtgärden är att logga in på den [SUSE Customer Center](https://scc.suse.com/)

När du är inloggad, gå till organisation--> inloggningsuppgifter för organisationen. I detta avsnitt bör du hitta de autentiseringsuppgifter som krävs för att konfigurera SMT-servern.

Det tredje steget är att installera en SUSE Linux virtuell dator i Azure VNet. Ta en bild med SLES 12 SP2 galleriet Azure för att distribuera den virtuella datorn. I distributionsprocessen, inte definierar ett DNS-namn och inte använder statiska IP-adresser som visas i den här skärmbilden

![distribution av virtuella datorer för SMT server](./media/hana-installation/image3_vm_deployment.png)

Den distribuerade virtuella datorn har en mindre VM och tog emot interna IP-adress i Azure VNet för 10.34.1.4. Smtserver var namnet på den virtuella datorn. Efter installationen kan kontrollerades anslutningen till HANA stora instans enhet(er). Beroende på hur du ordnade namnmatchning du kan behöva konfigurera lösning av HANA stora instans-enheter i etc/hosts på Azure VM. Lägga till ytterligare en disk i den virtuella datorn som ska användas för att rymma uppdateringsfilerna. Startdisken själva kan vara för liten. I det fall påvisa, fick disken monteras /srv/www/htdocs som visas i följande skärmbild. En disk på 100 GB bör vara tillräckligt.

![distribution av virtuella datorer för SMT server](./media/hana-installation/image4_additional_disk_on_smtserver.PNG)

Logga in på HANA stora instans-enheter, underhålla/etc/hosts och kontrollera om du kan nå den virtuella Azure-datorn som ska köra SMT-server i nätverket.

När den här kontrollen görs har, måste du logga in på den virtuella Azure-datorn som ska köras SMT-servern. Om du använder putty för att logga in på den virtuella datorn måste du köra den här kommandosekvens i bash-fönstret:

```
cd ~
echo "export NCURSES_NO_UTF8_ACS=1" >> .bashrc
```

Starta om din bash för att aktivera inställningarna när du kör kommandona. Starta YAST.

Gå till programvaruunderhåll och Sök efter smt i YAST. Välj smt som växlar automatiskt till yast2 smt som visas nedan

![SMT i yast](./media/hana-installation/image5_smt_in_yast.PNG)


Acceptera valet för installation på smtserver. När du har installerat kan du gå till serverkonfigurationen SMT och ange organisations autentiseringsuppgifter från SUSE kunden Center som hämtats tidigare. Även ange det Virtuella Azure-värdnamnet som SMT-Serveradress. I den här demonstrationen var https://smtserver som visas i nästa graphics.

![Konfiguration av SMT server](./media/hana-installation/image6_configuration_of_smtserver1.png)

Du behöver testa om anslutningen till SUSE Customer Center fungerar som nästa steg. Som du ser i följande grafik, om demonstration fungerade.

![Testa ansluta till SUSE kunden Center](./media/hana-installation/image7_test_connect.png)

En gång SMT installationen startar, måste du ange ett lösenord för databasen. Eftersom det är en ny installation måste du ange lösenordet som visas i nästa grafik.

![Ange lösenord för databas](./media/hana-installation/image8_define_db_passwd.PNG)

Nästa interaktion som du har är när ett certifikat skapas. Gå igenom dialogrutan enligt nästa och steget ska fortsätta.

![Skapa ett certifikat för SMT server](./media/hana-installation/image9_certificate_creation.PNG)

Det kan finnas vissa antal minuter som används i steget ”Kör kontroll av synkronisering' i slutet av konfigurationen. Efter installation och konfiguration av SMT-server, bör du hitta directory lagringsplatsen under montera punkt /srv/www/htdocs/plus vissa underkataloger under lagringsplatsen. 

Starta om servern SMT och dess relaterade tjänster med dessa kommandon.

```
rcsmt restart
systemctl restart smt.service
systemctl restart apache2
```

### <a name="download-of-packages-onto-smt-server"></a>Hämtning av paket till SMT server

Välj paket i SMT-hantering med Yast när alla tjänster har startats om. Paketet markeringen beror på OS-avbildningen av HANA stora instans-server och inte SLES utgåva eller version av den virtuella datorn kör SMT-server. Ett exempel på urvalssidan visas nedan.

![Välj paket](./media/hana-installation/image10_select_packages.PNG)

När du är klar med valet i paketet, måste du starta den ursprungliga kopian väljer paket till SMT-server som du konfigurerar. Den här kopian utlöses i gränssnittet med hjälp av kommandot smt-speglingen som visas nedan


![Hämta paket till SMT-server](./media/hana-installation/image11_download_packages.PNG)

Som du ser ovan ska paket kopieras till kataloger som skapas under montera punkt /srv/www/htdocs. Den här processen kan ta en stund. Beroende på hur många paket som du väljer, det kan ta upp till en timme eller mer.
Du måste flytta till klientinstallationen SMT som den här processen är klar. 

### <a name="set-up-the-smt-client-on-hana-large-instance-units"></a>Ställ in SMT-klienten på HANA stora instans enheter

På klienter är i det här fallet HANA stora instans-enheter. Installationen av SMT kopieras skriptet clientSetup4SMT.sh till Azure-VM. Kopiera som skriptet över till HANA stora instans-enhet du vill ansluta till servern SMT. Starta skriptet med alternativet -h och ge den som parametern namnet på SMT-servern. I det här exemplet smtserver.

![Konfigurera SMT-klienten](./media/hana-installation/image12_configure_client.PNG)

Det kan finnas ett scenario där inläsningen av certifikat från servern av klienten lyckades, men registreringen misslyckades enligt nedan.

![Registrering av klienten misslyckas](./media/hana-installation/image13_registration_failed.PNG)

Om registreringen misslyckas kan läsa det här [SUSE stöder dokumentet](https://www.suse.com/de-de/support/kb/doc/?id=7006024) och utföra stegen som beskrivs i det.

> [!IMPORTANT] 
> Du måste ange namnet på den virtuella datorn i det här fallet smtserver, utan det fullständigt kvalificerade domännamnet som servernamn. Bara det Virtuella namnet fungerar. 

När dessa steg har utförts, måste du köra följande kommando på HANA stora instans-enhet

```
SUSEConnect –cleanup
```

> [!Note] 
> I våra tester vi alltid var tvungen att vänta några minuter efter att steget. Körs omedelbart-clientSetup4SMT.sh efter korrigerande åtgärder som beskrivs i artikel SUSE avslutades med meddelanden att certifikatet inte skulle vara giltigt ännu. Väntar på vanligtvis 5 – 10 minuter och köra clientSetup4SMT.sh avslutades i en lyckad klientkonfiguration.

Om du råkade ut för det problem som du behöver åtgärda baserat på stegen i artikeln SUSE måste du starta om clientSetup4SMT.sh på HANA stora instans enheten igen. Nu ska den slutföras enligt nedan.

![Klienten har registrerats](./media/hana-installation/image14_finish_client_config.PNG)

Du har konfigurerat SMT klienten av HANA stora instans enheten att ansluta mot SMT-server som du har installerat på den virtuella Azure-datorn med det här steget. Nu kan du göra 'zypper upp' eller 'zypper i ”installera operativsystemskorrigeringar till HANA stora instanser eller installera ytterligare paket. Det är att förstå att du bara kan få korrigeringsprogram som du hämtade innan på SMT-servern.


## <a name="example-of-an-sap-hana-installation-on-hana-large-instances"></a>Exempel på en SAP HANA-installation på HANA stora instanser
Det här avsnittet visar hur man installerar SAP HANA på en enhet med stora HANA-instans. Det ser ut som starttillstånd som vi har:

- Du har angett Microsoft alla data för att distribuera en SAP HANA stora instans.
- Du har fått SAP HANA stora instans från Microsoft.
- Du har skapat ett virtuellt Azure-nätverk som är anslutet till det lokala nätverket.
- Du ansluten ExpressRotue kretsen för HANA stora instanser till samma Azure VNet.
- Du har installerat en Azure VM som du använder som en hopp för HANA stora instanser.
- Du kontrollerat att du kan ansluta från rutan hopp till HANA stora instans-enhet och vice versa.
- Du har markerat om de nödvändiga paketen och korrigeringsprogram som är installerade.
- Du läst SAP anteckningar och dokumentation om HANA installation i Operativsystemet som du använder och kontrollerat att HANA-versionen av choice stöds i Operativsystemet versionen.

Vad som visas i nästa sekvenser är hämtningen av HANA installationspaket till rutan hopp VM, i det här fallet körs på en Windows-Operativsystemet, kopiering av paket till HANA stora instans-enhet och i vilken ordning inställningarna.

### <a name="download-of-the-sap-hana-installation-bits"></a>Hämtningen av bits för SAP HANA-installation
Eftersom enheterna HANA stora instansen inte har direkt anslutning till internet, kan inte direkt hämta installationspaket från SAP för HANA stora instans VM. Om du vill lösa saknas direkt Internetanslutning, behöver du rutan hopp. Du kan hämta paketen till rutan hopp VM.

För att kunna hämta HANA Installationspaketen, behöver du en SAP-S-användare eller andra användare som får du åtkomst till SAP-Marketplace. Gå igenom den här sekvensen skärmar när du loggar in:

Gå till [SAP Service Marketplace](https://support.sap.com/en/index.html) > Klicka på ladda ned programvara > installationer och uppgradering > efter alfabetisk Index > Under H – SAP HANA-plattformen Edition > SAP HANA-plattformen version 2.0 > Installation > hämta den följande filer

![Hämta HANA installation](./media/hana-installation/image16_download_hana.PNG)

I fallet demonstration hämtade vi installationspaket för SAP HANA 2.0. I rutan Azure hopp VM Expandera du självextraherande arkiv till katalogen som visas nedan.

![Extrahera HANA installation](./media/hana-installation/image17_extract_hana.PNG)

Kopiera den katalog som skapats av extrahering HANA stora instans enhet till /hana/shared volym i en katalog som du skapade i fallet ovan 51052030, som arkiven extraheras.

> [!Important]
> Kopiera inte installationspaket till rot- eller LUN-Start eftersom utrymme är begränsat och måste användas av andra processer samt.


### <a name="install-sap-hana-on-the-hana-large-instance-unit"></a>Installera SAP HANA på HANA stora instans-enhet
Du måste logga in som användarrot för att kunna installera SAP HANA. Endast rot har tillräcklig behörighet för att installera SAP HANA.
Det första du behöver göra är att ange behörighet för den katalog som du kopierade via i/hana/delade. Behörigheterna måste du ange t.ex.

```
chmod –R 744 <Installation bits folder>
```

Om du vill installera SAP HANA använder grafisk installation måste gtk2 paketet vara installerad på HANA stora instanser. Kontrollera om det är installerat med kommandot

```
rpm –qa | grep gtk2
```

Vi visar SAP HANA-installationen med det grafiska användargränssnittet i ytterligare steg. Gå till installationskatalogen och gå till katalogen sub HDB_LCM_LINUX_X86_64 som nästa steg. Start

```
./hdblcmgui 
```
out-of-katalogen. Nu komma vägleds du genom en sekvens med skärmar där du måste ange data för installationen. I det fall påvisa, installerar vi SAP HANA-databasservern och SAP HANA-klientkomponenter. Vår markeringen är därför SAP HANA-databas som visas nedan

![Välj HANA i installationen](./media/hana-installation/image18_hana_selection.PNG)

På nästa skärm väljer du alternativet Installera nya System

![Välj HANA nyinstallation](./media/hana-installation/image19_select_new.PNG)

Du måste välja mellan flera ytterligare komponenter som kan installeras dessutom till SAP HANA-databasservern efter det här steget.

![Välj ytterligare HANA komponenter](./media/hana-installation/image20_select_components.PNG)

Vi valde SAP HANA-klienten och SAP HANA Studio för den här dokumentationen. Vi kan också installera en instans av skala upp. därför på nästa skärm måste du välja en enda värd-systemet 

![Välj skala upp installation](./media/hana-installation/image21_single_host.PNG)

På nästa skärm måste du ange vissa data

![Ange SAP HANA-SID](./media/hana-installation/image22_provide_sid.PNG)

> [!Important]
> Som HANA System-ID (SID), måste du ange samma SID som du tillhandahöll Microsoft när du sorterade HANA stora instans-distributionen. Om du väljer en annan SID gör installationen misslyckas på grund av problem med åtkomst till behörighet i olika volymer

Katalogen du använder/hana/delade katalogen som installationen. I nästa steg måste du ange platser för HANA datafiler och HANA loggfiler


![Ange HANA loggplatsen](./media/hana-installation/image23_provide_log.PNG)

> [!Note]
> Du måste definiera som data och loggfiler de volymer som redan medföljde monteringspunkter som innehåller SID som du valde i skärmen markeringen innan den här skärmen. SID matchningsfel med det namn du angav i inloggningsskärmen före gå tillbaka och justera SID till det värde som du har på monteringspunkterna.

Granska värdnamnet i nästa steg, och slutligen korrigera den. 

![Granska värdnamn](./media/hana-installation/image24_review_host_name.PNG)

I nästa steg behöver du också hämta data som du gav till Microsoft när du sorterade HANA stora instans-distributionen. 

![Ge systemanvändare UID och GID](./media/hana-installation/image25_provide_guid.PNG)

> [!Important]
> Du måste ange samma System användar-ID och ID användargrupp som du tillhandahöll Microsoft som order enhet distributionen. Om du inte vill ge samma ID: N, misslyckas installationen av SAP HANA på HANA stora instans-enheten.

På två skärmar, vilket vi inte visas i den här dokumentationen du behöver ange lösenordet för systemanvändaren för SAP HANA-databasen och sapadm användares lösenord som används för värden SAP-Agent som installeras som en del av SAP HANA-datab ase-instans.

När du har definierat lösenordet en bekräftelseskärm visas. Kontrollera att alla data i listan och fortsätta med installationen. Du når ett förlopp som dokument under installationen, som den nedan

![Kontrollera installationens förlopp](./media/hana-installation/image27_show_progress.PNG)

När installationen är klar bör du en bild som det nedanstående

![Installationen är klar](./media/hana-installation/image28_install_finished.PNG)

SAP HANA-instansen bör nu vara aktiv och körs och redo för användning. Du ska kunna ansluta till den från SAP HANA Studio. Kontrollera också att du söka efter de senaste korrigeringarna för SAP HANA och tillämpa dessa korrigeringar.
























































 







 




