---
title: Installera SAP HANA på SAP HANA på Azure (stora instanser) | Microsoft Docs
description: Hur du installerar SAP HANA på en SAP HANA på Azure (stora instanser).
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
ms.openlocfilehash: fc63eb792e58d960ae67138b5e58e6b705945030
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/20/2019
ms.locfileid: "56446400"
---
# <a name="how-to-install-and-configure-sap-hana-large-instances-on-azure"></a>Installera och konfigurera SAP HANA (stora instanser) på Azure

Innan du läser den här artikeln, bekanta dig med [vanliga termer som HANA stora instanser](hana-know-terms.md) och [HANA stora instanser SKU: er](hana-available-skus.md).

Installationen av SAP HANA är ditt ansvar. Du kan börja installera en ny SAP HANA på Azure (stora instanser) server när du upprättar anslutningen mellan din Azure-nätverk och enhet(er) för stora HANA-instansen. 

> [!Note]
> Installationen av SAP HANA måste utföras av en person som har klarat certifierade SAP teknik associera examen, certifieringsprov för SAP HANA-Installation eller som är en SAP-certifierade systemintegrerare (SI) per SAP-princip.

När du planerar att installera HANA 2.0, se [SAP support-kommentar #2235581 – SAP HANA: Operativsystem som stöds](https://launchpad.support.sap.com/#/notes/2235581/E) för att se till att Operativsystemet stöds med SAP HANA versionen som du installerar. Operativsystemet som stöds för HANA 2.0 är mer restriktiva än Operativsystemet som stöds för HANA 1.0. 

> [!IMPORTANT] 
> Typ II för enheter för för närvarande endast SLES 12 SP2 OS-versionen stöds. 

Innan du påbörjar installationen av HANA, måste du verifiera följande:
- [HLI enhet(er)](#validate-the-hana-large-instance-units)
- [Konfiguration av operativsystem](#operating-system)
- [Nätverkskonfiguration](#networking)
- [Storage-konfiguration](#storage)


## <a name="validate-the-hana-large-instance-units"></a>Verifiera stora HANA-instansen enhet(er)

När du har fått den stora HANA-instansen enheten från Microsoft, verifiera följande inställningar och justera efter behov.

Den **första steget** när du får den stora HANA-instansen och upprätta åtkomst och anslutningen till instanserna är att registrera Operativsystemet på instansen med din OS-provider. Det här steget innehåller registrering av din SUSE Linux-operativsystem i en instans av SUSE SMT som distribueras i en virtuell dator i Azure. 

Den stora HANA-instansen enheten kan ansluta till den här SMT-instansen. (Mer information finns i [hur du ställer in SMT server för SUSE Linux](hana-setup-smt.md)). Du kan också måste ditt Red Hat-operativsystem vara registrerad med Red Hat prenumeration Manager som du behöver för att ansluta till. Mer information finns i anmärkningar i [vad är SAP HANA på Azure (stora instanser)?](https://docs.microsoft.com/azure/virtual-machines/linux/sap-hana-overview-architecture?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Det här steget är också krävs för uppdatering av Operativsystemet, som ansvarar för kunden. För SUSE, hittar du i dokumentationen för att installera och konfigurera SMT på den här sidan om [installation av SMT](https://www.suse.com/documentation/sles-12/book_smt/data/smt_installation.html).

Den **andra steget** är att söka efter nya uppdateringar och korrigeringar av specifika OS/slutversionen. Kontrollera att korrigeringsnivån för den stora HANA-instansen är i det senaste tillståndet. Det kan finnas fall där de senaste korrigeringarna inkluderas inte. När du tar över en enhet för stora HANA-instansen, är det obligatoriskt att kontrollera om korrigeringar ska användas.

Den **tredje steget** är att Kolla in den relevanta SAP-informationen för att installera och konfigurera SAP HANA på specifika OS/slutversionen. På grund av ändrar rekommendationer eller ändringar för att SAP anteckningar eller konfigurationer som är beroende av enskilda installationsscenarier kan att inte Microsoft alltid kunna konfigurera en enhet för stora HANA-instansen perfekt. 

Därför är det obligatoriskt för dig som en kund att läsa SAP notes relaterat till SAP HANA för dina exakta Linux-versionen. Även kontrollera konfigurationerna för OS/slutversionen och tillämpa konfigurationsinställningarna om du inte redan gjort.

Mer specifikt kontrollerar följande parametrar och justera så småningom att:

- net.core.rmem_max = 16777216
- net.core.wmem_max = 16777216
- net.core.rmem_default = 16777216
- net.core.wmem_default = 16777216
- net.core.optmem_max = 16777216
- net.ipv4.tcp_rmem = 65536 16777216 16777216
- net.ipv4.tcp_wmem = 65536 16777216 16777216

Från och med SLES12 SP1 och RHEL 7.2, måste dessa parametrar anges i en konfigurationsfil i katalogen /etc/sysctl.d. Till exempel måste en fil med namnet 91-NetApp-HANA.conf skapas. Dessa parametrar måste vara set in/etc/sysctl.conf för äldre SLES och RHEL versioner.

För alla RHEL-versioner från och med RHEL 6.3, Tänk på följande: 
- Sunrpc.tcp_slot_table_entries = 128 parametern måste anges in/etc/modprobe.d/sunrpc-local.conf. Om filen inte finns, måste du först skapa det genom att lägga till följande post: 
    - alternativ sunrpc tcp_max_slot_table_entries = 128

Den **fjärde steget** är att kontrollera att systemklockan på din enhet för stora HANA-instansen. Instanserna distribueras med en tidszon som system. Den här tidszonen representerar platsen för Azure-regionen där stämpeln stora HANA-instansen finns. Du kan ändra systemtiden eller tidszonen för de instanser som du äger. 

Om du beställer fler instanser i din klient, måste du anpassa tidszonen för de nyligen levererade instanserna. Microsoft har ingen insikt i tidszonen konfigurera med förekomsterna efter övergång. Nyligen distribuerade instanser kan därför inte anges i samma tidszon som du har ändrat till. Den har är ditt ansvar som kund att anpassa tidszonen för instanser som har överlämnas, om det behövs. 

Den **femte steget** är att kontrollera etc/hosts. Eftersom bladen hämta överlämnas har de olika IP-adresser som tilldelas för olika syften. Kontrollera etc/hosts-filen. När enheter läggs till i en befintlig klient, inte tror att du har nyligen distribuerade system som hanteras korrekt med IP-adresserna för system som har levererats tidigare etc /-värdar. Det är ditt ansvar som kunden ser till att en nyligen distribuerade instans kan interagera och matcha namnen på de enheter som du har distribuerat tidigare i din klient. 

## <a name="operating-system"></a>Operativsystem

> [!IMPORTANT] 
> För Type II-enheter stöds för närvarande endast SLES 12 SP2 OS-version. 

Växlingsutrymme av levererade OS-avbildningen är inställd på 2 GB enligt den [SAP support-kommentar #1999997 – vanliga frågor och svar: SAP HANA minne](https://launchpad.support.sap.com/#/notes/1999997/E). Som kund, om du vill att en annan inställning måste du ange den själv.

[SUSE Linux Enterprise Server 12 SP1 för SAP-program](https://www.suse.com/products/sles-for-sap/download/) är distribution av Linux som har installerats för SAP HANA på Azure (stora instanser). Den här specifika distributionen tillhandahåller funktioner för SAP-specifika ”out of box” (inklusive förinställda parametrar för att köra SAP på SLES effektivt).

Se [Resource bibliotek/white paper-faktablad](https://www.suse.com/products/sles-for-sap/resource-library#white-papers) på webbplatsen SUSE och [SAP på SUSE](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE) på SAP Community nätverk (SCN) för flera användbara resurser som rör distribution av SAP HANA på SLES (inklusive konfiguration av hög tillgänglighet, säkerhet appstyrning som är specifik för SAP-åtgärder med mera).

Nedan följer ytterligare och användbart SAP på SUSE-relaterade länkar:

- [SAP HANA på SUSE Linux-plats](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)
- [Metodtips för SAP: Placera replikeringen – SAP NetWeaver på SUSE Linux Enterprise 12](https://www.suse.com/docrepcontent/container.jsp?containerId=9113)
- [ClamSAP – SLES virusskydd för SAP](http://scn.sap.com/community/linux/blog/2014/04/14/clamsap--suse-linux-enterprise-server-integrates-virus-protection-for-sap) (inklusive SLES 12 för SAP-program)

Följande är SAP support anteckningar som kan användas för att implementera SAP HANA på SLES 12:

- [SAP support Obs! #1944799 – SAP HANA-riktlinjer för installation av operativsystemet SLES](http://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html)
- [SAP support-kommentar #2205917 – SAP HANA-Databasobjekt rekommenderade OS-inställningar för SLES 12 för SAP-program](https://launchpad.support.sap.com/#/notes/2205917/E)
- [SAP support-kommentar #1984787 – SUSE Linux Enterprise Server 12: installationsinformation](https://launchpad.support.sap.com/#/notes/1984787)
- [SAP support Obs! #171356 – SAP-program på Linux:  Allmän information](https://launchpad.support.sap.com/#/notes/1984787)
- [SAP support-kommentar 1391070 # – Linux UUID lösningar](https://launchpad.support.sap.com/#/notes/1391070)

[Red Hat Enterprise Linux for SAP HANA](https://www.redhat.com/en/resources/red-hat-enterprise-linux-sap-hana) är ett annat erbjudande för att köra SAP HANA på stora HANA-instanser. Versionerna av RHEL 6.7 och 7.2 är tillgängliga. Observera att i motsats till interna virtuella Azure-datorer där endast RHEL 7.2 och senare versioner stöds stora HANA-instanser har stöd för RHEL 6.7 samt. Dock bör du använda en 7.x RHEL-version.

Följande är ytterligare användbar SAP på Red Hat länkarna:
- [SAP HANA på Red Hat Linux plats](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+Red+Hat).

Följande är SAP support anteckningar som kan användas för att implementera SAP HANA på Red Hat:

- [SAP support Obs! #2009879 – SAP HANA-riktlinjer för operativsystemet Red Hat Enterprise Linux (RHEL)](https://launchpad.support.sap.com/#/notes/2009879/E)
- [SAP support note #2292690 - SAP HANA DB: Rekommenderade OS-inställningar för RHEL 7](https://launchpad.support.sap.com/#/notes/2292690)
- [SAP Support Obs #2247020 – SAP HANA-Databasobjekt Rekommenderade OS-inställningar för RHEL 6.7](https://launchpad.support.sap.com/#/notes/2247020)
- [SAP support-kommentar 1391070 # – Linux UUID lösningar](https://launchpad.support.sap.com/#/notes/1391070)
- [SAP support Obs 2228351 # – Linux SAP HANA Database Service Pack 11 revision 110 (eller högre) på RHEL 6 eller SLES 11](https://launchpad.support.sap.com/#/notes/2228351)
- [SAP support Obs #2397039 – vanliga frågor och svar SAP på RHEL](https://launchpad.support.sap.com/#/notes/2397039)
- [SAP support-kommentar #1496410 - Red Hat Enterprise Linux 6.x: Installation och uppgradering](https://launchpad.support.sap.com/#/notes/1496410)
- [SAP support-kommentar #2002167 - Red Hat Enterprise Linux 7.x: Installation och uppgradering](https://launchpad.support.sap.com/#/notes/2002167)

### <a name="time-synchronization"></a>Tidssynkronisering

SAP-program som bygger på SAP NetWeaver-arkitekturen är känsliga för tidsskillnaderna för de olika komponenterna som utgör SAP-system. SAP ABAP kort minnesdumpar med fel rubriken ZDATE\_stor\_tid\_DIFF är förmodligen redan bekant. Det beror på att dessa kort Dumpar visas när systemklockan på olika servrar eller virtuella datorer för långt ifrån varandra går.

För SAP HANA på Azure (stora instanser) gäller inte tidssynkronisering som utförts i Azure i compute-enheter i stämplarna som stor instans. Synkroniseringen kan inte användas för att köra SAP-program i interna virtuella Azure-datorer, eftersom Azure ser till att en systemtiden är korrekt synkroniserad. 

Därför måste du konfigurera en separat tidsserver som kan användas av SAP-programservrar som körs på virtuella Azure-datorer och av SAP HANA database-instanser som körs på stora HANA-instanser. Lagringsinfrastruktur i stor instans stämplar är synkroniserad tid med NTP-servrar.


## <a name="networking"></a>Nätverk
Vi förutsätter att du följt rekommendationerna designa din Azure-nätverk och ansluta de virtuella nätverk till HANA stora instanser, enligt beskrivningen i följande dokument:

- [SAP HANA (stor instans) översikt och arkitektur på Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [SAP HANA (stora instanser) infrastruktur och anslutningsmöjlighet på Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Det finns vissa detaljer värt att nämna om nätverk av de enda enheterna. Varje enhet för stora HANA-instansen levereras med två eller tre IP-adresser som är kopplade till två eller tre NIC-portar. Tre IP-adresser används i HANA skala ut konfigurationer och HANA system replication scenario. En av IP-adresser som har tilldelats till nätverkskortet på enheten ligger utanför servern IP-adresspool som beskrivs i [SAP HANA (stora instanser) översikt och arkitektur på Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).

Mer information om Ethernet-information om arkitekturen finns i den [HLI stöds scenarier](hana-supported-scenario.md).

## <a name="storage"></a>Storage

Lagringslayout för SAP HANA på Azure (stora instanser) har konfigurerats av SAP HANA på Azure service-hantering via SAP rekommenderade riktlinjer. Dessa riktlinjer finns dokumenterade i den [lagringskrav för SAP HANA](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) white paper om. 

Ungefärlig storlek på olika volymer med olika HANA stora instanser SKU: erna dokumenteras i [SAP HANA (stora instanser) översikt och arkitektur på Azure](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Namnkonventionerna för lagringsvolymer visas i följande tabell:

| Lagringsanvändning | Monteringspunkt | Volymnamn | 
| --- | --- | ---|
| HANA-data | /hana/data/SID/mnt0000<m> | Storage IP:/hana_data_SID_mnt00001_tenant_vol |
| HANA-logg | /hana/log/SID/mnt0000<m> | Storage IP:/hana_log_SID_mnt00001_tenant_vol |
| Loggsäkerhetskopiering för HANA | /Hana/log/Backups | Storage IP: / hana_log_backups_SID_mnt00001_tenant_vol |
| HANA delas | /Hana/Shared/SID | Storage IP:/hana_shared_SID_mnt00001_tenant_vol/shared |
| usr/sap | /usr/sap/SID | Storage IP:/hana_shared_SID_mnt00001_tenant_vol/usr_sap |

*SID* är HANA-instans System-ID. 

*Klient* är en intern uppräkning av åtgärder när du distribuerar en klient.

HANA usr/sap dela på samma volym. Nomenklaturen för monteringspunkter innehåller system-ID och HANA-instanser som mount-nummer. I stora distributioner finns bara en monteringspunkt, till exempel mnt00001. I skalbara distributioner, å andra sidan, ser du så många monteringar som du har worker och master-noder. 

För skalbar miljöer, data, log och log delat och kopplade till varje nod i konfigurationen av skalbara säkerhetskopierade volymer. För konfigurationer som är flera SAP-instanser är en annan uppsättning volymer skapas och ansluts till stora HANA-instansen-enhet. Lagringsinformation layout för ditt scenario, se [HLI stöds scenarier](hana-supported-scenario.md).

När du tittar på en enhet för stora HANA-instansen upptäcker du att enheterna som levereras med generösa diskvolymen för HANA/data och att det finns en volym HANA/loggsäkerhetskopiering. Orsaken till att vi har gjort HANA/data så stor är att storage-ögonblicksbilder som vi erbjuder du som kund som använder samma diskvolymen. Fler storage-ögonblicksbilder som du utför, desto mer utrymme förbrukas av ögonblicksbilder i dina tilldelade lagringsvolymer. 

HANA/loggsäkerhetskopiering volymen är inte avsedd för att vara volymen för säkerhetskopiering av databaser. Det är storlek för att användas som den säkerhetskopiera volymen för de HANA säkerhetskopieringarna av transaktionsloggen. Mer information finns i [SAP HANA (stora instanser) hög tillgänglighet och katastrofåterställning recovery på Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Utöver det lagringsutrymme som tillhandahålls kan du köpa ytterligare kapacitet i steg om 1 TB. Den här ytterligare lagringsutrymme kan läggas till som nya volymer till stora HANA-instansen.

Under integrering med SAP HANA på Azure service management kunden anger en användare ID (UID) och grupp-ID (GID) för gruppen sidadm användar- och sapsys (till exempel: 1000,500). Under installationen av SAP HANA-system, måste du använda dessa samma värden. Eftersom du vill distribuera flera HANA-instanser på en enhet får du flera uppsättningar av volymer (en uppsättning för varje instans). Därför vid tidpunkten för distribution måste du definiera följande:

- SID för olika HANA-instanser (sidadm härleds från den).
- Minnesstorlekar på olika HANA-instanser. Minnesstorlek per instans definierar storleken på volymerna i varje enskild volym-uppsättning.

Baserat på rekommendationer för providern, konfigureras följande alternativ för montering för alla monterade volymer (omfattar ej Start LUN):

- nfs    rw, vers=4, hard, timeo=600, rsize=1048576, wsize=1048576, intr, noatime, lock 0 0

Dessa montera punkter som har konfigurerats i/etc/fstab enligt följande bild:

![fstab av monterade volymer i stora HANA-instansen enhet](./media/hana-installation/image1_fstab.PNG)

Utdata från den kommandot df -h på en enhet för S72m HANA stora instanser ut som:

![fstab av monterade volymer i stora HANA-instansen enhet](./media/hana-installation/image2_df_output.PNG)


Lagringsstyrenhet och noder i stor instans stämplar synkroniseras till NTP-servrar. När du synkroniserar SAP HANA på Azure (stora instanser) enheter och virtuella Azure-datorer mot en NTP-server, bör det finnas utan betydande tid drift mellan infrastrukturen och compute-enheter i Azure eller stor instans stämplar.

Ange följande konfigurationsparametrar för SAP HANA för att optimera SAP HANA till lagring som används under:

- max_parallel_io_requests 128
- async_read_submit on
- async_write_submit_active on
- async_write_submit_blocks all
 
För SAP HANA 1.0 versioner upp till SPS12 kan dessa parametrar kan anges under installationen av SAP HANA-databas, enligt beskrivningen i [SAP-kommentar #2267798 - konfigurationen av SAP HANA-databas](https://launchpad.support.sap.com/#/notes/2267798).

Du kan också konfigurera parametrarna efter installationen för SAP HANA-databas med hjälp av hdbparam framework. 

Med SAP HANA 2.0 är hdbparam framework inaktuell. Därför måste parametrarna anges med hjälp av SQL-kommandon. Mer information finns i [SAP-kommentar #2399079: Eliminering av hdbparam i HANA 2](https://launchpad.support.sap.com/#/notes/2399079).

Referera till [HLI stöds scenarier](hana-supported-scenario.md) mer information om lagringslayout för din arkitektur.


**Nästa steg**

- Referera till [HANA-Installation på HLI](hana-example-installation.md)










































 







 




