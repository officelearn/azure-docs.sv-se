---
title: Installera SAP HANA på SAP HANA på Azure (stora instanser) | Microsoft Docs
description: Hur du installerar SAP HANA på en SAP HANA på Azure (stor instans).
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
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: dad088138fea2dd4fadc0cc9eed71245c32a8e0b
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44162687"
---
# <a name="how-to-install-and-configure-sap-hana-large-instances-on-azure"></a>Installera och konfigurera SAP HANA (stora instanser) på Azure

Innan du läser den här artikeln, bekanta dig med [vanliga termer som HANA stora instanser](hana-know-terms.md) och [HANA stora instanser SKU: er](hana-available-skus.md).

Installationen av SAP HANA är ditt ansvar och du kan starta aktiviteten efter handoff av en ny SAP HANA på Azure (stora instanser)-servern. Och när anslutningen mellan din virtuella Azure-nätverket och stora HANA-instansen enhet(er) har upprättats. 

> [!Note]
> Installationen av SAP HANA måste utföras av en person som är certifierade för SAP HANA-installationer per SAP-princip. En person som har klarat certifierade SAP teknik associera examen, certifieringsprov för SAP HANA-Installation, eller genom ett SAP-certifierade systemintegrerare (SI).

Kontrollera igen, särskilt när du planerar att installera HANA 2.0 [SAP Support Obs! #2235581 – SAP HANA: Supported Operating Systems](https://launchpad.support.sap.com/#/notes/2235581/E) för att se till att Operativsystemet stöds med SAP HANA släpper du valt att installera. Du upptäcker att Operativsystemet som stöds för HANA 2.0 är mer begränsad än OS som stöds för HANA 1.0. 

> [!IMPORTANT] 
> Versionen stöds nu för Type II enheter endast SLES 12 SP2 OS. 

Innan du påbörjar installationen av HANA, måste du verifiera följande:
- [Verifiera HLI enhet(er)](#validate-the-hana-large-instance-units)
- [Konfiguration av operativsystem](#operating-system)
- [Nätverkskonfiguration](#networking)
- [Storage-konfiguration](#storage)


## <a name="validate-the-hana-large-instance-units"></a>Verifiera HANA stora instanser enhet(er)

När du har fått den stora HANA-instansen enheten från Microsoft verifiera följande inställningar och justera efter behov.

**Första steget** när du tar emot stora HANA-instansen och har etablerade åtkomst och anslutningen till instanserna, är att registrera Operativsystemet på instansen med din OS-provider. Det här steget omfattar registrering av din SUSE Linux-operativsystem i en instans av SUSE SMT som du måste ha distribuerats i en virtuell dator i Azure. Den stora HANA-instansen enheten kan ansluta till den här SMT-instansen (se [så installationsprogrammet SMT server för SUSE Linux](hana-setup-smt.md)). Eller ditt Red Hat-operativsystem måste vara registrerad med Red Hat prenumeration Manager måste du ansluta till. Se anmärkning också i det här [dokumentet](https://docs.microsoft.com/azure/virtual-machines/linux/sap-hana-overview-architecture?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Det här steget är nödvändigt för att kunna uppdatera Operativsystemet. En aktivitet som är ansvarig för kunden. Hitta dokumentation för att installera och konfigurera SMT för SUSE, [här](https://www.suse.com/documentation/sles-12/book_smt/data/smt_installation.html).

**Andra steg** är att söka efter nya uppdateringar och korrigeringar av specifika OS/slutversionen. Kontrollera om korrigeringsnivån för den stora HANA-instansen på det senaste tillståndet. Baserat på tidsinställningen för patch/versioner och ändringar i avbildningen som Microsoft kan distribuera, kan det finnas fall där de senaste korrigeringarna inte kan ingå. Det är därför ett obligatoriskt steg när du tar över en enhet på stora HANA-instansen, att kontrollera om korrigeringsprogram som är relevanta för säkerhet, funktioner, tillgänglighet och prestanda har getts ut under tiden av den specifika Linux-försäljare och som ska tillämpas.

**Tredje steget** är att Kolla in den relevanta SAP-informationen för att installera och konfigurera SAP HANA på specifika OS/slutversionen. På grund av ändring av rekommendationer eller ändringar i SAP Notes eller konfigurationer som är beroende av den enskilda installationsscenarier, Microsoft kommer inte alltid kan ha en enhet för stora HANA-instansen som konfigurerats perfekt. Därför är det obligatoriskt för dig som kund att läsa SAP Notes relaterade till SAP HANA på din exakta Linux-version. Även kontrollera konfigurationerna för OS/slutversionen behövs och tillämpa konfigurationsinställningar där det inte redan gjort.

Kontrollera följande parametrar i viss, och så småningom justeras till:

- NET.Core.rmem_max = 16777216
- NET.Core.wmem_max = 16777216
- NET.Core.rmem_default = 16777216
- NET.Core.wmem_default = 16777216
- NET.Core.optmem_max = 16777216
- net.ipv4.tcp_rmem = 65536 16777216 16777216
- net.ipv4.tcp_wmem = 65536 16777216 16777216

Från och med SLES12 SP1 och RHEL 7.2, måste dessa parametrar anges i en konfigurationsfil i katalogen /etc/sysctl.d. Till exempel måste en fil med namnet 91-NetApp-HANA.conf skapas. Dessa parametrar måste vara set in/etc/sysctl.conf för äldre SLES och RHEL versioner.

Alla RHEL släpper och börjar med SLES12, den 
- sunrpc.tcp_slot_table_entries = 128

Parametern måste anges in/etc/modprobe.d/sunrpc-local.conf. Om filen inte finns, måste den först skapas genom att lägga till följande post: 
- alternativ sunrpc tcp_max_slot_table_entries = 128

**Fjärde steget** är att kontrollera systemklockan på den HANA stora instans enheten. Instanserna distribueras med en system-tidszonen som representerar platsen för Azure-regionen HANA stora instans stämpeln finns i. Du kan ändra systemtiden eller tidszonen för de instanser som du äger. Gör det och beställa fler instanser i din klient, var beredd på att du måste anpassa tidszonen för de nyligen levererade instanserna. Microsoft har ingen insikter om datorns tidszon konfigurera med förekomsterna efter övergång. Nyligen distribuerade instanser kan därför inte anges i samma tidszon som du har ändrat till. Därför är det ditt ansvar som kund att kontrollera och vid behov anpassas instanser överlämnas tidszon. 

**Femte steget** är att kontrollera etc/hosts. Eftersom bladen hämta överlämnas har de olika IP-adresser som tilldelats för olika syften (se nästa avsnitt). Kontrollera etc/hosts-filen. I fall där enheter läggs till i en befintlig klient inte tror att du har etc/värdar för de nyligen distribuerade system som hanteras korrekt med IP-adresserna för tidigare levererat system. Därför är det på att du som kund att kontrollera de korrekta inställningarna, så att en nyligen distribuerade instans kan interagera och matcha namnen på tidigare distribuerade enheter i din klient. 

## <a name="operating-system"></a>Operativsystem

> [!IMPORTANT] 
> Versionen stöds nu för Type II enheter endast SLES 12 SP2 OS. 

Växlingsutrymme i procent av den levererade operativsystemavbildningen är inställd på 2 GB enligt den [SAP Support Obs! #1999997 – vanliga frågor och svar: SAP HANA minne](https://launchpad.support.sap.com/#/notes/1999997/E). Alla olika inställningar som önskade måste anges av dig som en kund.

[SUSE Linux Enterprise Server 12 SP1 för SAP-program](https://www.suse.com/products/sles-for-sap/hana) är distribution av Linux som installerats för SAP HANA på Azure (stora instanser). Den här specifika distributionen tillhandahåller funktioner för SAP-specifika &quot;direkt ur lådan&quot; (inklusive förinställda parametrar för att köra SAP på SLES effektivt).

Se [Resource bibliotek/White Paper-faktablad](https://www.suse.com/products/sles-for-sap/resource-library#white-papers) på webbplatsen SUSE och [SAP på SUSE](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE) på SAP Community nätverk (SCN) för flera användbara resurser som rör distribution av SAP HANA på SLES (inklusive konfiguration av hög Tillgänglighet, säkerhet härdning som är specifika för SAP-åtgärder med mera).

Ytterligare och användbart SAP på SUSE-relaterade länkar:

- [SAP HANA på SUSE Linux plats](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)
- [Bästa praxis för SAP: placera replikeringen – SAP NetWeaver på SUSE Linux Enterprise 12](https://www.suse.com/docrepcontent/container.jsp?containerId=9113).
- [ClamSAP – SLES virusskydd för SAP](http://scn.sap.com/community/linux/blog/2014/04/14/clamsap--suse-linux-enterprise-server-integrates-virus-protection-for-sap) (inklusive SLES 12 för SAP-program).

SAP Support Anmärkningar till implementera SAP HANA på SLES 12:

- [SAP Support-kommentar #1944799 – SAP HANA riktlinjer för Installation av operativsystemet SLES](http://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html).
- [SAP Support-kommentar #2205917 – SAP HANA-Databasobjekt rekommenderade OS-inställningar för SLES 12 för SAP-program](https://launchpad.support.sap.com/#/notes/2205917/E).
- [SAP Support-kommentar #1984787 – SUSE Linux Enterprise Server 12: Installationsinformation](https://launchpad.support.sap.com/#/notes/1984787).
- [SAP Support-kommentar #171356 – SAP-program på Linux: allmän Information](https://launchpad.support.sap.com/#/notes/1984787).
- [SAP Support-kommentar #1391070 – Linux UUID lösningar](https://launchpad.support.sap.com/#/notes/1391070).

[Red Hat Enterprise Linux for SAP HANA](https://www.redhat.com/en/resources/red-hat-enterprise-linux-sap-hana) är ett annat erbjudande för att köra SAP HANA på stora HANA-instanser. Versionerna av RHEL 6.7 och 7.2 är tillgängliga. . Obs i motsatt interna virtuella Azure-datorer där endast RHEL 7.2 och senare versioner stöds, stora HANA-instanser stöder RHEL 6.7 samt. Men vi rekommenderar att du använder en 7.x RHEL-version.

Ytterligare och användbart SAP på Red Hat länkarna:
- [SAP HANA på Red Hat Linux plats](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+Red+Hat).

SAP Support Anmärkningar till implementera SAP HANA på Red Hat:

- [SAP Support-kommentar #2009879 – SAP HANA riktlinjer för Red Hat Enterprise Linux (RHEL)-operativsystem](https://launchpad.support.sap.com/#/notes/2009879/E).
- [SAP Support-kommentar #2292690 – SAP HANA-Databasobjekt: Rekommenderas OS-inställningar för RHEL 7](https://launchpad.support.sap.com/#/notes/2292690).
- [SAP Support-kommentar #2247020 – SAP HANA-Databasobjekt: Rekommenderas OS-inställningar för RHEL 6.7](https://launchpad.support.sap.com/#/notes/2247020).
- [SAP Support-kommentar #1391070 – Linux UUID lösningar](https://launchpad.support.sap.com/#/notes/1391070).
- [SAP Support-kommentar #2228351 - Linux: SAP HANA Database Service Pack 11 revision 110 (eller högre) på RHEL 6 eller SLES 11](https://launchpad.support.sap.com/#/notes/2228351).
- [SAP Support-kommentar #2397039 – vanliga frågor och svar: SAP på RHEL](https://launchpad.support.sap.com/#/notes/2397039).
- [SAP Support-kommentar #1496410 - Red Hat Enterprise Linux 6.x: Installation och uppgradering](https://launchpad.support.sap.com/#/notes/1496410).
- [SAP Support-kommentar #2002167 - Red Hat Enterprise Linux 7.x: Installation och uppgradering](https://launchpad.support.sap.com/#/notes/2002167).

### <a name="time-synchronization"></a>Tidssynkronisering

SAP-program som bygger på SAP NetWeaver-arkitekturen är känsliga på tidsskillnaderna för de olika komponenterna som utgör SAP-system. SAP ABAP kort minnesdumpar med fel rubriken ZDATE\_stor\_tid\_DIFF är sannolikt bekant, eftersom dessa kort Dumpar visas när systemklockan på olika servrar eller virtuella datorer för långt ifrån varandra går.

För SAP HANA på Azure (stora instanser) tidssynkronisering i Azure&#39;t gäller för compute-enheter i stämplarna som stor instans. Synkroniseringen kan inte användas för att köra SAP-program i interna virtuella Azure-datorer som Azure ser till ett system&#39;s tid är korrekt synkroniserad. Därför kan en separat tid servern måste konfigureras som kan användas av SAP-programservrar som körs på virtuella datorer i Azure och SAP HANA-databas instanser som körs på stora HANA-instanser. Lagringsinfrastruktur i stor instans stämplar är tidsinställningen synkroniserad högst med NTP-servrar.


## <a name="networking"></a>Nätverk
Vi förutsätter att du följt rekommendationerna i utforma dina virtuella Azure-nätverk och ansluta de virtuella nätverk till stora HANA-instanser som beskrivs i dessa dokument:

- [SAP HANA (stor instans) översikt och arkitektur på Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [SAP HANA (stora instanser) infrastruktur och anslutningsmöjlighet på Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Det finns vissa detaljer värt att nämna om nätverk av de enda enheterna. Varje enhet för stora HANA-instansen levereras med två eller tre IP-adresser som är kopplade till två eller tre NIC-portar på enheten. Tre IP-adresser används i HANA skala ut konfigurationer och HANA System Replication-scenario. En av IP-adresserna som tilldelats till nätverkskortet på enheten är från Server-IP-poolen som beskrevs i den [SAP HANA (stor instans) översikt och arkitektur på Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).

Se [HLI stöds scenarier](hana-supported-scenario.md) mer ethernet-information för din arkitektur.

## <a name="storage"></a>Storage

Lagringslayout för SAP HANA på Azure (stora instanser) har konfigurerats av SAP HANA på Azure Service Management via SAP rekommenderade riktlinjer som beskrivs i [lagringskrav för SAP HANA](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) white paper om. Ungefärlig storlek på olika volymer med olika HANA stora instanser SKU: er har dokumenterade i [SAP HANA (stor instans) översikt och arkitektur på Azure](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Namnkonventionerna för lagringsvolymer visas i följande tabell:

| Lagringsanvändning | Monteringspunkt | Volymnamn | 
| --- | --- | ---|
| HANA-data | /hana/data/SID/mnt0000<m> | Storage IP:/hana_data_SID_mnt00001_tenant_vol |
| HANA-logg | /Hana/log/SID/mnt0000<m> | Storage IP: / hana_log_SID_mnt00001_tenant_vol |
| Loggsäkerhetskopiering för HANA | /Hana/log/Backups | Storage IP: / hana_log_backups_SID_mnt00001_tenant_vol |
| HANA delas | /Hana/Shared/SID | Storage IP: / hana_shared_SID_mnt00001_tenant_vol/delade |
| usr/sap | /usr/SAP/SID | Storage IP: / hana_shared_SID_mnt00001_tenant_vol/usr_sap |

Där SID = HANA-instans System-ID 

- Och klienttrafik = en intern uppräkning av åtgärder när du distribuerar en klient.

Som du ser delar HANA delas och usr/sap samma volym. Nomenklaturen för monteringspunkter innehåller System-ID och HANA-instanser som mount-nummer. I stora distributioner finns det bara en monteringspunkt som mnt00001. Medan i utskalningsdistribution ser du så många monteringar som, finns det worker och master-noder. För skalbar miljö, data, log, delat och kopplade till varje nod i konfigurationen av skalbara säkerhetskopiera loggvolymerna. För konfigurationer med flera SAP-instanser körs en annan uppsättning volymer skapas och är kopplade till enheten HAN stor instans. Se [HLI stöds scenarier](hana-supported-scenario.md) för layout av lagringsinformation för ditt scenario.

När du läser dokumentet och se en enhet för stora HANA-instansen upptäcker du att enheterna som medföljer i stället generösa diskvolymen för HANA/data och att vi har en volym HANA/loggsäkerhetskopiering. Anledningen till varför vi storlek HANA/data så stor är att storage-ögonblicksbilder som vi erbjuder för dig som kund som använder den samma volymen. Det innebär mer lagringsutrymme ögonblicksbilder som du utför, desto mer utrymme förbrukas av ögonblicksbilder i dina tilldelade lagringsvolymer. HANA/loggsäkerhetskopiering volymen kan inte antas vara volymen för att placera databassäkerhetskopior i. Det är storlek för att användas som säkerhetskopieringsvolymen för säkerhetskopieringar av transaktionsloggen i HANA. Mer information finns i [SAP HANA (stora instanser), hög tillgänglighet och katastrofåterställning i Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 

Utöver det lagringsutrymme som tillhandahålls kan du köpa ytterligare kapacitet i steg om 1 TB. Den här ytterligare lagringsutrymme kan läggas till som nya volymer till stora HANA-instanser.

Under integrering med SAP HANA på Azure Service Management kunden som anger ett användar-ID (UID) och grupp-ID (GID) för gruppen sidadm användar- och sapsys (ex: 1000,500) är det nödvändigt att dessa samma värden som används under installationen av SAP HANA-system. Som du vill distribuera flera HANA-instanser på en enhet får du flera uppsättningar av volymer (en uppsättning för varje instans). Därför vid tidpunkten för distribution måste du definiera:

- SID för olika HANA-instanser (sidadm härleds från den).
- Minnesstorlekar på olika HANA-instanser. Eftersom minnesstorlek per instanser definierar storleken på volymerna i varje enskild volym-uppsättning.

Baserat på rekommendationer för providern följande alternativ för montering har konfigurerats för alla monterade volymer (omfattar ej Start LUN):

- NFS-rw drivrutiner = 4, hård, timeo = 600, rsize = 1048576 wsize = 1048576, Intro, noatime, låsa 0 0

Dessa montera punkter är konfigurerade i/etc/fstab som visas i följande bild:

![fstab av monterade volymer i stora HANA-instansen enhet](./media/hana-installation/image1_fstab.PNG)

Utdata från den kommandot df -h på en enhet för S72m HANA stora instanser ser ut som:

![fstab av monterade volymer i stora HANA-instansen enhet](./media/hana-installation/image2_df_output.PNG)


Lagringsstyrenhet och noder i stor instans stämplar synkroniseras till NTP-servrar. Med dig synkroniseringen SAP HANA på Azure (stora instanser) enheter och virtuella Azure-datorer mot en NTP-server, bör det finnas utan betydande tid drift händer mellan infrastrukturen och compute-enheter i Azure eller stor instans stämplar.

För att optimera SAP HANA till lagring som används under, bör du också ange följande konfigurationsparametrar för SAP HANA:

- max_parallel_io_requests 128
- async_read_submit på
- async_write_submit_active på
- async_write_submit_blocks alla
 
För SAP HANA 1.0 versioner upp till SPS12 kan dessa parametrar kan anges under installationen av SAP HANA-databas, enligt beskrivningen i [SAP Obs! #2267798 - konfigurationen av SAP HANA-databas](https://launchpad.support.sap.com/#/notes/2267798)

Du kan också konfigurera parametrarna efter installationen för SAP HANA-databas med hjälp av hdbparam framework. 

Med SAP HANA 2.0 är hdbparam framework inaktuell. Därmed måste parametrarna anges med hjälp av SQL-kommandon. Mer information finns i [SAP Obs! #2399079: eliminering av hdbparam i HANA 2](https://launchpad.support.sap.com/#/notes/2399079).

Se [HLI stöds scenarier](hana-supported-scenario.md) mer lagringslayout för din arkitektur.


**Nästa steg**

- Se [HANA-Installation på HLI](hana-example-installation.md)










































 







 




