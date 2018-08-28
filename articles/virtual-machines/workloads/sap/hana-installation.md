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
ms.date: 08/27/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1d335e135551b7b6faed8ee566acb14b46fd6c81
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2018
ms.locfileid: "43107519"
---
# <a name="how-to-install-and-configure-sap-hana-large-instances-on-azure"></a>Installera och konfigurera SAP HANA (stora instanser) på Azure

Här följer några viktiga definitioner som du behöver veta innan du läser guiden. I [SAP HANA (stora instanser) översikt och arkitektur på Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) introducerade vi två olika typer av enheter för stora HANA-instansen med:

- S72, S72m, S144, S144m, S192, S192m och S192xm som vi kallar det ”Type jag klassen” SKU: er.
- S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm och S960m som vi kallar ”typ II class-SKU: er.

Klass-specificerare ska användas i hela dokumentationen stora HANA-instansen att så småningom referera till olika funktioner och krav baserat på HANA stora instans SKU: er.

Vi använder ofta de övriga definitionerna är:
- **Stor instans stämpel:** en infrastruktur-stack med maskinvara som är SAP HANA TDI certifierade och dedikerade för att köra SAP HANA-instanser i Azure.
- **SAP HANA på Azure (stora instanser):** officiellt namn för erbjudandet i Azure att köra HANA-instanser i på SAP HANA TDI-certifierad maskinvara som är distribuerad i stor instans tidsstämplar i olika Azure-regioner. Relaterade termen **stora HANA-instansen** är kort för SAP HANA på Azure (stora instanser) och är mycket används den här tekniska distributionsguiden.


Installationen av SAP HANA är ditt ansvar och du kan starta aktiviteten efter handoff av en ny SAP HANA på Azure (stora instanser)-servern. Och när anslutningen mellan din virtuella Azure-nätverket och stora HANA-instansen enhet(er) har upprättats. 

> [!Note]
> Installationen av SAP HANA måste utföras av en person som är certifierade för SAP HANA-installationer per SAP-princip. En person som har klarat certifierade SAP teknik associera examen, certifieringsprov för SAP HANA-Installation, eller genom ett SAP-certifierade systemintegrerare (SI).

Kontrollera igen, särskilt när du planerar att installera HANA 2.0 [SAP Support Obs! #2235581 – SAP HANA: Supported Operating Systems](https://launchpad.support.sap.com/#/notes/2235581/E) för att se till att Operativsystemet stöds med SAP HANA släpper du valt att installera. Du upptäcker att Operativsystemet som stöds för HANA 2.0 är mer begränsad än OS som stöds för HANA 1.0. 

> [!IMPORTANT] 
> Versionen stöds nu för Type II enheter endast SLES 12 SP2 OS. 

## <a name="first-steps-after-receiving-the-hana-large-instance-units"></a>Första stegen när du har fått den HANA stora instans enhet(er)

**Första steget** när du tar emot stora HANA-instansen och har etablerade åtkomst och anslutningen till instanserna, är att registrera Operativsystemet på instansen med din OS-provider. Det här steget omfattar registrering av din SUSE Linux-operativsystem i en instans av SUSE SMT som du måste ha distribuerats i en virtuell dator i Azure. Den stora HANA-instansen enheten kan ansluta till den här SMT-instansen (se senare i den här dokumentationen). Eller ditt Red Hat-operativsystem måste vara registrerad med Red Hat prenumeration Manager måste du ansluta till. Se anmärkning också i det här [dokumentet](https://docs.microsoft.com/azure/virtual-machines/linux/sap-hana-overview-architecture?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Det här steget är nödvändigt för att kunna uppdatera Operativsystemet. En aktivitet som är ansvarig för kunden. Hitta dokumentation för att installera och konfigurera SMT för SUSE, [här](https://www.suse.com/documentation/sles-12/book_smt/data/smt_installation.html).

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

När du läser dokumentet och se en enhet för stora HANA-instansen upptäcker du att enheterna som medföljer i stället generösa diskvolymen för HANA/data och att vi har en volym HANA/loggsäkerhetskopiering. Anledningen till varför vi storlek HANA/data så stor är att storage-ögonblicksbilder som vi erbjuder för dig som kund som använder den samma volymen. Det innebär mer lagringsutrymme ögonblicksbilder som du utför, desto mer utrymme förbrukas av ögonblicksbilder i dina tilldelade lagringsvolymer. HANA/loggsäkerhetskopiering volymen kan inte antas vara volymen för att placera databassäkerhetskopior i. Det är storlek för att användas som säkerhetskopieringsvolymen för säkerhetskopieringar av transaktionsloggen i HANA. I framtida ögonblicksbilder versioner av lagring self service, vi rikta in dig på den här specifika volymen om du vill ha mer frekventa ögonblicksbilder. Och med den mer frekventa replikeringar till katastrofåterställningsplatsen om du hellre vill att alternativet in för disaster recovery-funktionen tillhandahålls av infrastruktur för stora HANA-instansen. Mer information finns i [SAP HANA (stora instanser), hög tillgänglighet och katastrofåterställning i Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 

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

## <a name="time-synchronization"></a>Tidssynkronisering

SAP-program som bygger på SAP NetWeaver-arkitekturen är känsliga på tidsskillnaderna för de olika komponenterna som utgör SAP-system. SAP ABAP kort minnesdumpar med fel rubriken ZDATE\_stor\_tid\_DIFF är sannolikt bekant, eftersom dessa kort Dumpar visas när systemklockan på olika servrar eller virtuella datorer för långt ifrån varandra går.

För SAP HANA på Azure (stora instanser) tidssynkronisering i Azure&#39;t gäller för compute-enheter i stämplarna som stor instans. Synkroniseringen kan inte användas för att köra SAP-program i interna virtuella Azure-datorer som Azure ser till ett system&#39;s tid är korrekt synkroniserad. Därför kan en separat tid servern måste konfigureras som kan användas av SAP-programservrar som körs på virtuella datorer i Azure och SAP HANA-databas instanser som körs på stora HANA-instanser. Lagringsinfrastruktur i stor instans stämplar är tidsinställningen synkroniserad högst med NTP-servrar.

## <a name="setting-up-smt-server-for-suse-linux"></a>Ställa in SMT server för SUSE Linux
Stora SAP HANA-instanser har inte direkt anslutning till Internet. Därför är det inte enkelt att registrera till exempel enheter med OS-providern och för att ladda ned och tillämpa korrigeringar. När det gäller SUSE Linux vara en lösning att ställa in en SMT-server i en Azure VM. Medan den virtuella Azure-datorn måste finnas i ett virtuellt Azure nätverk, som är ansluten till den stora HANA-instansen. Med sådana en SMT-server kan den stora HANA-instansen enheten registrera och ladda ned korrigeringar. 

SUSE innehåller en större vägledning om deras [prenumeration hanteringsverktyg för SLES 12 SP2](https://www.suse.com/documentation/sles-12/pdfdoc/book_smt/book_smt.pdf). 

Som villkor för installation av SMT-server som uppfyller uppgiften för stora HANA-instansen behöver du:

- Ett virtuellt Azure nätverk som är ansluten till HANA stora instans ER-kretsen.
- Ett SUSE-konto som är associerad med en organisation. Medan organisationen behöver ha en giltig SUSE-prenumeration.

### <a name="installation-of-smt-server-on-azure-vm"></a>Installation av SMT-server på Azure VM

I det här steget ska installera du SMT-server i en Azure VM. Det första måttet är att logga in på den [SUSE Customer Center](https://scc.suse.com/)

När du är inloggad, gå till organisation--> organisationens autentiseringsuppgifter. I avsnittet bör du hitta de autentiseringsuppgifter som krävs för att konfigurera SMT-servern.

Det tredje steget är att installera en SUSE Linux-dator i Azure VNet. Ta en bild för SLES 12 SP2 galleriet i Azure för att distribuera den virtuella datorn. I distributionsprocessen, inte definierar ett DNS-namn och inte använder statiska IP-adresser som visas i den här skärmbilden

![distribution av virtuella datorer för SMT-server](./media/hana-installation/image3_vm_deployment.png)

Den distribuerade virtuella datorn har en mindre VM och fick den interna IP-adressen i Azure VNet för 10.34.1.4. Namnet på den virtuella datorn var smtserver. Efter installationen kan kontrollerades anslutningen till stora HANA-instans enhet(er). Beroende på hur du organiserat namnmatchning du kan behöva konfigurera lösning av enheter för stora HANA-instansen i etc/värdar för den virtuella Azure-datorn. Lägg till ytterligare en disk till den virtuella datorn som ska användas för att lagra uppdateringarna. Startdisken själva kan vara för litet. I det fallet visas fick disken monteras /srv/www/htdocs enligt följande skärmbild. En disk på 100 GB bör vara tillräckligt.

![distribution av virtuella datorer för SMT-server](./media/hana-installation/image4_additional_disk_on_smtserver.PNG)

Logga in på stora HANA-instansen enhet(er), underhålla/etc/hosts och kontrollera om du kan nå den virtuella Azure-datorer som ska köra SMT-server i nätverket.

När den här kontrollen är klar har behöver du logga in på Azure VM som ska köras SMT-servern. Om du använder putty för att logga in på den virtuella datorn som du behöver köra den här sekvensen av kommandon i bash-fönstret:

```
cd ~
echo "export NCURSES_NO_UTF8_ACS=1" >> .bashrc
```

Starta om din bash för att aktivera inställningarna när du kör dessa kommandon. Starta sedan YAST.

Gå till programvaruunderhåll och Sök efter smt i YAST. Välj smt växla automatiskt till yast2 smt enligt nedan

![SMT i yast](./media/hana-installation/image5_smt_in_yast.PNG)


Acceptera valet för installation på smtserver. När du har installerat kan du gå till SMT-serverkonfigurationen och ange organisationens autentiseringsuppgifter från SUSE kunden Center som hämtades tidigare. Ange också Azure VM-värdnamn som SMT-Serveradress. I det här exemplet var https://smtserver som visas i nästa bilderna.

![Konfigurationen av SMT-server](./media/hana-installation/image6_configuration_of_smtserver1.png)

Du måste testa om anslutningen till SUSE Customer Center fungerar som nästa steg. Den fungerar som du ser i följande bilderna i fallet demonstration.

![Testa ansluta till SUSE Customer Center](./media/hana-installation/image7_test_connect.png)

När SMT installationen startar, måste du ange ett lösenord för databasen. Eftersom det är en ny installation som du behöver definiera lösenordet som du ser i nästa bilderna.

![Ange lösenordet för databas](./media/hana-installation/image8_define_db_passwd.PNG)

I nästa interaktion som du har är när ett certifikat skapas. Gå igenom dialogrutan som visas nästa och steget bör fortsätta.

![Skapa certifikat för SMT-server](./media/hana-installation/image9_certificate_creation.PNG)

Det kan finnas vissa antal minuter som används i steg i ”Kör kontroll av synkronisering” i slutet av konfigurationen. Efter installationen och konfigurationen av SMT-server, bör du hitta directory lagringsplatsen under montera punkt /srv/www/htdocs/plus några undermappar under lagringsplatsen. 

Starta om servern SMT och relaterade tjänster med följande kommandon.

```
rcsmt restart
systemctl restart smt.service
systemctl restart apache2
```

### <a name="download-of-packages-onto-smt-server"></a>Hämtningen av paketen på SMT-servern

Välj paket i SMT-hantering med Yast när alla tjänster har startats om. Paketet valet beror på OS-avbildning av stora HANA-instans-server och inte på SLES-version eller -versionen av SMT-servern. Ett exempel på val av skärmen visas nedan.

![Välj paket](./media/hana-installation/image10_select_packages.PNG)

När du är klar med det paket valet av måste du starta den inledande kopian av SMT-server som du har konfigurerat väljer paketen. Den här kopian har utlösts i gränssnittet med hjälp av kommandot smt-speglingen enligt nedan


![Ladda ned paket till SMT-servern](./media/hana-installation/image11_download_packages.PNG)

Som du ser ovan, bör paket som ska kopieras till de kataloger som skapats under montera punkt /srv/www/htdocs. Den här processen kan ta en stund. Beroende på hur många paket som du väljer, det kan ta upp till en timme eller mer.
Du måste flytta till SMT-klientkonfiguration som den här processen är klar. 

### <a name="set-up-the-smt-client-on-hana-large-instance-units"></a>Konfigurera SMT-klienten på stora HANA-instansen enheter

Klienterna är i det här fallet de stora HANA-instansen. Serverinstallation SMT kopierade skriptet clientSetup4SMT.sh på den virtuella Azure-datorn. Kopia som skriptet över till stora HANA-instansen-enhet du vill ansluta till servern SMT. Starta skriptet med alternativet -h och ge den som parameter namnet på din SMT-server. I det här exemplet smtserver.

![Konfigurera SMT-klienten](./media/hana-installation/image12_configure_client.PNG)

Det kan finnas ett scenario där belastningen på certifikatet från servern och klienten lyckades men registreringen misslyckades enligt nedan.

![Registrering av klienten misslyckas](./media/hana-installation/image13_registration_failed.PNG)

Om registreringen misslyckas kan du läsa följande [SUSE stöd för dokument](https://www.suse.com/de-de/support/kb/doc/?id=7006024) och kör stegen som beskrivs det.

> [!IMPORTANT] 
> Du måste ange namnet på den virtuella datorn i det här fallet smtserver, utan att det fullständigt kvalificerade domännamnet som servernamn. Bara den virtuella datorn namnet fungerar. 

När dessa steg har utförts, måste du köra följande kommando på den stora HANA-instansen enheten

```
SUSEConnect –cleanup
```

> [!Note] 
> I våra tester hade vi alltid Vänta några minuter efter det steget. Körs omedelbart-clientSetup4SMT.sh efter de korrigerande åtgärderna som beskrivs i artikel SUSE avslutades med meddelanden att certifikatet inte giltigt ännu. Väntar på vanligtvis 5 – 10 minuter och kör clientSetup4SMT.sh avslutades i en lyckad klientkonfiguration.

Om du råkade ut för problem som du behöver åtgärda baserat på stegen i artikeln SUSE, måste du starta om clientSetup4SMT.sh på stora HANA-instansen enheten igen. Nu bör den slutföras utan problem som visas nedan.

![Klienten har registrerats](./media/hana-installation/image14_finish_client_config.PNG)

Det här steget ska du har konfigurerat SMT-klienten av den stora HANA-instansen enheten att ansluta mot den SMT-server som du installerade i Azure-VM. Du kan nu dra 'zypper upp' eller 'zypper i ”om du installerar korrigeringsfiler för operativsystem till stora HANA-instanser eller installera ytterligare paket. Det är underförstått att du bara kan få korrigeringsprogram som du laddade ner innan på SMT-servern.


## <a name="example-of-an-sap-hana-installation-on-hana-large-instances"></a>Exempel på en installation av SAP HANA på stora HANA-instanser
Det här avsnittet visar hur du installerar SAP HANA på en enhet för stora HANA-instansen. Starttillstånd har vi ut:

- Du har angett Microsoft alla data för att distribuera du en SAP HANA stor instans.
- Du har fått SAP HANA för stora-instansen från Microsoft.
- Du har skapat ett virtuellt Azure nätverk som är ansluten till ditt lokala nätverk.
- Du har anslutit ExpressRotue kretsen för stora HANA-instanser till samma Azure VNet.
- Du har installerat en Azure-dator som du använder som en jumpbox för stora HANA-instanser.
- Du har gjort till att du kan ansluta från jumpboxen till stora HANA-instansen-enhet och vice versa.
- Du har markerat om alla nödvändiga paket och korrigeringsprogram som är installerade.
- Du läser SAP notes och dokumentation om HANA-installation på operativsystem du använder och kontrollerat att HANA-versionen av val stöds på Operativsystemets version.

Vad som visas i nästa sekvenser är hämtningen av HANA-paket till jumpboxen virtuell dator, i det här fallet körs på en Windows-OS, kopia av paket till stora HANA-instansen-enhet och sekvens med installationen.

### <a name="download-of-the-sap-hana-installation-bits"></a>Hämta SAP HANA-installation-bitar
Eftersom enheter för stora HANA-instansen inte har direkt anslutning till internet, kan du direkt hämta installationspaket från SAP för HANA stora instans VM. Om du vill lösa saknas direkt Internetanslutning, behöver du jumpbox. Du kan hämta paketen till jumpboxen VM.

För att kunna hämta Installationspaketen HANA, behöver du en SAP-S-användare eller andra användare, där du kan komma åt Marketplace SAP. Gå igenom den här sekvensen av skärmar när du loggar in:

Gå till [SAP Service Marketplace](https://support.sap.com/en/index.html) > Klicka på ladda ned programvara > installationer och uppgradering > efter alfabetisk Index > Under H – SAP HANA-plattformen Edition > SAP HANA-plattformen Edition 2.0 > installationen > ladda ned den följande filer

![Ladda ned HANA-installation](./media/hana-installation/image16_download_hana.PNG)

I fallet demonstration hämtade vi installationspaket för SAP HANA 2.0. På Azure jumpbox VM Expandera du självextraherande arkiv till katalogen som visas nedan.

![Extrahera HANA-installation](./media/hana-installation/image17_extract_hana.PNG)

Kopiera den katalog som skapats av extrahering till stora HANA-instans-enhet till /hana/shared volym i en katalog som du skapade i fallet ovan 51052030, eftersom arkiven har extraherats.

> [!Important]
> Kopiera inte installationspaket till rot- eller LUN-Start eftersom utrymme är begränsad och måste användas av andra processer samt.


### <a name="install-sap-hana-on-the-hana-large-instance-unit"></a>Installera SAP HANA på stora HANA-instansen-enhet
Du måste logga in som användarrot för att kunna installera SAP HANA. Endast rot har tillräcklig behörighet för att installera SAP HANA.
Det första du behöver göra är att ange behörigheter för den katalog som du kopierade över till/hana/delas. Behörigheterna måste du tycker

```
chmod –R 744 <Installation bits folder>
```

Om du vill installera SAP HANA med den grafiska installationen måste gtk2 paketet vara installerad på stora HANA-instanser. Kontrollera om programmet har installerats med kommandot

```
rpm –qa | grep gtk2
```

Vi har bevisat SAP HANA-installation med det grafiska användargränssnittet i ytterligare steg. Gå till installationskatalogen och navigera till katalogen sub HDB_LCM_LINUX_X86_64 som nästa steg. Start

```
./hdblcmgui 
```
utanför den katalogen. Nu komma vägleds du genom en sekvens av skärmar där du måste ange data för installationen. I det fallet visas installerar vi databasservern för SAP HANA och SAP HANA-klientkomponenter. Vårt val är därför SAP HANA-databas som visas nedan

![Välj HANA i installationen](./media/hana-installation/image18_hana_selection.PNG)

På nästa skärm väljer du alternativet Installera nya System

![Välj ny HANA-installation](./media/hana-installation/image19_select_new.PNG)

När det här steget måste du välja mellan flera ytterligare komponenter som kan installeras dessutom till SAP HANA-databasservern.

![Välj ytterligare HANA-komponenter](./media/hana-installation/image20_select_components.PNG)

I den här dokumentationen valde vi SAP HANA-klienten och SAP HANA-Studio. Vi också har installerats en upp-instans. därför på nästa skärm måste du välja ”enda värd systemet” 

![Välj skala upp installationen](./media/hana-installation/image21_single_host.PNG)

På nästa skärm måste du ange vissa data

![Ange SAP HANA-SID](./media/hana-installation/image22_provide_sid.PNG)

> [!Important]
> Som HANA System-ID (SID) som du måste du ange samma SID, som du angav Microsoft när du har beställt distributionen stora HANA-instansen. Välja ett annat SID kan installationen misslyckas på grund av problem med åtkomst till behörigheten i olika volymer

Som installationen delade katalogen som du använder/hana/directory. I nästa steg måste du ange platserna för HANA datafiler och HANA-loggfiler


![Ange HANA loggplatsen](./media/hana-installation/image23_provide_log.PNG)

> [!Note]
> Du bör definiera som data och loggfiler volymerna som redan medföljde monteringspunkter som innehåller SID som du valde i skärmen val innan den här skärmen. På skärmen innan du går du tillbaka och anpassa SID till det värde som du har på monteringspunkterna om SID matchningsfel med det namn du angav i.

Granska värdnamnet och så småningom rätta till det i nästa steg. 

![Granska värdnamn](./media/hana-installation/image24_review_host_name.PNG)

I nästa steg måste du också hämta data som du gav till Microsoft när du har beställt distributionen stora HANA-instansen. 

![Ange systemanvändare UID och GID](./media/hana-installation/image25_provide_guid.PNG)

> [!Important]
> Du måste ange samma System användar-ID och ID användargrupp som du angav Microsoft som din beställning enhet distributionen. Om du inte ge samma ID: N, misslyckas installationen av SAP HANA på stora HANA-instansen-enhet.

I följande två skärmar, som vi inte visas i denna dokumentation måste du ange lösenordet för systemanvändare av SAP HANA-databas och lösenordet för användaren sapadm som används för SAP-Värdagenten som installeras som en del av SAP HANA-datab ase-instans.

När du har definierat lösenordet visas en bekräftelseskärm. Kontrollera alla data i listan och fortsätta med installationen. Du når en förlopp som dokumenterar under installationen, som den nedan

![Kontrollera installationens förlopp](./media/hana-installation/image27_show_progress.PNG)

När installationen är klar bör du en bild som det nedanstående

![Installationen är klar](./media/hana-installation/image28_install_finished.PNG)

SAP HANA-instans ska nu vara igång och körs och är tillgänglig för användning. Du ska kunna ansluta till den från SAP HANA-Studio. Kontrollera också att du söka efter de senaste uppdateringarna av SAP HANA och installerar dessa korrigeringar.
























































 







 




