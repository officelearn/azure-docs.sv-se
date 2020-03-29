---
title: Installera SAP HANA på SAP HANA på Azure (stora instanser) | Microsoft-dokument
description: Så här installerar du SAP HANA på en SAP HANA på Azure (stora instanser).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/16/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ca59305b22fcf1e81ef518612910731cb6edea5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617097"
---
# <a name="how-to-install-and-configure-sap-hana-large-instances-on-azure"></a>Installera och konfigurera SAP HANA (stora instanser) på Azure

Innan du läser den här artikeln, bekanta dig med [HANA Stora instanser vanliga termer](hana-know-terms.md) och [HANA Stora instanser SKU :](hana-available-skus.md)

Installationen av SAP HANA är ditt ansvar. Du kan börja installera en ny SAP HANA på Azure -servern (Stora instanser) när du har upprättat anslutningen mellan dina virtuella Azure-nätverk och HANA-enheter för stora instanser. 

> [!Note]
> Enligt SAP-principen måste installationen av SAP HANA utföras av en person som har klarat examen Certified SAP Technology Associate, SAP HANA Installation certification exam eller som är en SAP-certifierad systemintegratör (SI).

När du planerar att installera HANA 2.0 läser du [SAP-supportanteckning #2235581 - SAP HANA: Operativsystem som stöds](https://launchpad.support.sap.com/#/notes/2235581/E) för att se till att operativsystemet stöds med SAP HANA-versionen som du installerar. Det operativsystem som stöds för HANA 2.0 är mer restriktivt än det operativsystem som stöds för HANA 1.0. Du måste också kontrollera om os-versionen du är intresserad av visas som stöds för den specifika HLI-enheten på den här publicerade [listan](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Klicka på enheten för att få hela informationen med den os-lista som stöds för den enheten. 

Verifiera följande innan du påbörjar HANA-installationen:
- [HLI-enheter](#validate-the-hana-large-instance-units)
- [Konfiguration av operativsystem](#operating-system)
- [Nätverkskonfiguration](#networking)
- [Storage-konfiguration](#storage)


## <a name="validate-the-hana-large-instance-units"></a>Validera hana-enheter för stora instanser

När du har fått hana-enheten för stora instans från Microsoft validerar du följande inställningar och justerar vid behov.

Det **första steget** efter att du har fått DEN STORA HANA-instansen och upprätta åtkomst och anslutning till instanserna är att kontrollera i Azure-portalen om instanserna visas med rätt SKU:er och operativsystemet. Läs [Azure HANA Large Instances-kontrollen via Azure-portalen](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal) för de steg som krävs för att utföra kontrollerna.

Det **andra steget** efter att du har fått DEN STORA HANA-instansen och upprätta åtkomst och anslutning till instanserna är att registrera operativsystemet för instansen hos OS-leverantören. Det här steget inkluderar att registrera ditt SUSE Linux OS i en instans av SUSE SMT som distribueras i en virtuell dator i Azure. 

Hana-enheten för stor instans kan ansluta till den här SMT-instansen. (Mer information finns i [Så här konfigurerar du SMT-server för SUSE Linux](hana-setup-smt.md)). Alternativt måste ditt Red Hat OS registreras hos Red Hat Subscription Manager som du behöver ansluta till. Mer information finns i kommentarerna i [Vad är SAP HANA på Azure (Stora instanser)?](https://docs.microsoft.com/azure/virtual-machines/linux/sap-hana-overview-architecture?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Detta steg är nödvändigt för att lappa OS, vilket är kundens ansvar. För SUSE hittar du dokumentationen för att installera och konfigurera SMT på den här sidan om [SMT-installation](https://www.suse.com/documentation/sles-12/book_smt/data/smt_installation.html).

Det **tredje steget** är att söka efter nya patchar och korrigeringar av den specifika OS-versionen/ versionen. Kontrollera att korrigeringsnivån för DEN STORA HANA-instansen är i det senaste tillståndet. Det kan finnas fall där de senaste patcharna inte ingår. När du har tagit över en HANA Large Instance-enhet är det obligatoriskt att kontrollera om patchar behöver tillämpas.

Det **fjärde steget** är att kolla in relevanta SAP-anteckningar för att installera och konfigurera SAP HANA på den specifika OS-versionen/ versionen. På grund av ändrade rekommendationer eller ändringar i SAP-anteckningar eller konfigurationer som är beroende av enskilda installationsscenarier kan Microsoft inte alltid konfigurera en HANA-enhet för stor instans perfekt. 

Därför är det obligatoriskt för dig som kund att läsa SAP-anteckningarna relaterade till SAP HANA för din exakta Linux-utgåva. Kontrollera även konfigurationerna för OS-versionen och tillämpa konfigurationsinställningarna om du inte redan har gjort det.

Kontrollera särskilt följande parametrar och anpassa dig så småningom till:

- net.core.rmem_max = 16777216
- net.core.wmem_max = 16777216
- net.core.rmem_default = 16777216
- net.core.wmem_default = 16777216
- net.core.optmem_max = 16777216
- net.ipv4.tcp_rmem = 65536 16777216 16777216
- net.ipv4.tcp_wmem = 65536 16777216 16777216

Från och med SLES12 SP1 och RHEL 7.2 måste dessa parametrar anges i en konfigurationsfil i katalogen /etc/sysctl.d. Till exempel måste en konfigurationsfil med namnet 91-NetApp-HANA.conf skapas. För äldre SLES- och RHEL-versioner måste dessa parametrar ställas in/etc/sysctl.conf.

För alla RHEL-versioner som börjar med RHEL 6.3 bör du tänka på: 
- Parametern sunrpc.tcp_slot_table_entries = 128 måste ställas in/etc/modprobe.d/sunrpc-local.conf. Om filen inte finns måste du skapa den först genom att lägga till posten: 
    - alternativ sunrpc tcp_max_slot_table_entries = 128

Det **femte steget** är att kontrollera systemtiden för din HANA Large Instance-enhet. Instanserna distribueras med en systemtidszon. Den här tidszonen representerar platsen för Azure-regionen där stämpeln HANA Large Instance finns. Du kan ändra systemtid eller tidszon för de instanser du äger. 

Om du beställer fler instanser till din klient måste du anpassa tidszonen för de nyligen levererade instanserna. Microsoft har ingen inblick i den systemtidszon som du har konfigurerat med instanserna efter överlämnandet. Därför kan nyligen distribuerade instanser inte anges i samma tidszon som den du ändrade till. Det är ditt ansvar som kund att anpassa tidszonen för de instanser som överlämnades, om det behövs. 

Det **sjätte steget** är att kontrollera etc / värdar. När bladen överlämnas har de olika IP-adresser som tilldelas för olika ändamål. Kontrollera etc / värdar filen. När enheter läggs till i en befintlig klient, förvänta dig inte att ha etc / värdar för de nyligen distribuerade systemen underhålls korrekt med IP-adresserna för system som levererades tidigare. Det är ditt ansvar som kund att se till att en nyligen distribuerad instans kan interagera och matcha namnen på de enheter som du distribuerade tidigare i din klientorganisation. 


## <a name="operating-system"></a>Operativsystem

Växlingsutrymmet för den levererade OS-avbildningen är inställt på 2 GB enligt [SAP-supportmeddelandet #1999997 - FAQ: SAP HANA-minne](https://launchpad.support.sap.com/#/notes/1999997/E). Som kund måste du ställa in den själv om du vill ha en annan inställning.

[SUSE Linux Enterprise Server 12 SP1 för SAP-program](https://www.suse.com/products/sles-for-sap/download/) är distributionen av Linux som är installerad för SAP HANA på Azure (Stora instanser). Den här distributionen ger SAP-specifika funktioner "out of the box" (inklusive förinställda parametrar för att köra SAP på SLES effektivt).

Se [Resursbibliotek/faktablad](https://www.suse.com/products/sles-for-sap/resource-library#white-papers) på SUSE-webbplatsen och [SAP på SUSE i](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE) SAP Community Network (SCN) för flera användbara resurser relaterade till distribution av SAP HANA på SLES (inklusive upplägg av hög tillgänglighet, säkerhetshärdning som är specifik för SAP-åtgärder med mera).

Följande är ytterligare och användbar SAP på SUSE-relaterade länkar:

- [SAP HANA på SUSE Linux webbplats](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)
- [Metodtips för SAP: Enqueue replication – SAP NetWeaver på SUSE Linux Enterprise 12](https://www.suse.com/docrepcontent/container.jsp?containerId=9113)
- [ClamSAP – SLES-virusskydd för SAP](https://scn.sap.com/community/linux/blog/2014/04/14/clamsap--suse-linux-enterprise-server-integrates-virus-protection-for-sap) (inklusive SLES 12 för SAP-applikationer)

Följande är SAP-supportanteckningar som är tillämpliga på implementering av SAP HANA på SLES 12:

- [SAP-supportanteckning #1944799 – SAP HANA-riktlinjer för installation av SLES-operativsystem](http://service.sap.com/sap/support/notes/1944799)
- [SAP-supportanteckning #2205917 – SAP HANA DB rekommenderade OS-inställningar för SLES 12 för SAP-program](https://launchpad.support.sap.com/#/notes/2205917/E)
- [SAP-supportanteckning #1984787 – SUSE Linux Enterprise Server 12: installationsanteckningar](https://launchpad.support.sap.com/#/notes/1984787)
- [SAP-supportanteckning #171356 – SAP-programvara på Linux: Allmän information](https://launchpad.support.sap.com/#/notes/1984787)
- [SAP-supportanteckning #1391070 – Linux UUID-lösningar](https://launchpad.support.sap.com/#/notes/1391070)

[Red Hat Enterprise Linux för SAP HANA](https://www.redhat.com/en/resources/red-hat-enterprise-linux-sap-hana) är ett annat erbjudande för att köra SAP HANA på HANA Stora instanser. Versioner av RHEL 7.2 och 7.3 är tillgängliga och stöds. 

Följande är ytterligare användbara SAP på Red Hat relaterade länkar:
- [SAP HANA på Red Hat Linux webbplats](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+Red+Hat).

Följande är SAP-supportanteckningar som är tillämpliga på implementering av SAP HANA på Red Hat:

- [SAP-supportanteckning #2009879 - SAP HANA riktlinjer för Red Hat Enterprise Linux (RHEL) operativsystem](https://launchpad.support.sap.com/#/notes/2009879/E)
- [SAP-supportanteckning #2292690 - SAP HANA DB: Rekommenderade OS-inställningar för RHEL 7](https://launchpad.support.sap.com/#/notes/2292690)
- [SAP-supportanteckning #1391070 – Linux UUID-lösningar](https://launchpad.support.sap.com/#/notes/1391070)
- [SAP-stödanteckning #2228351 - Linux: SAP HANA Database SPS 11 revision 110 (eller högre) på RHEL 6 eller SLES 11](https://launchpad.support.sap.com/#/notes/2228351)
- [SAP-supportanteckning #2397039 - Vanliga frågor och svar: SAP på RHEL](https://launchpad.support.sap.com/#/notes/2397039)
- [SAP-supportanteckning #2002167 - Red Hat Enterprise Linux 7.x: Installation och uppgradering](https://launchpad.support.sap.com/#/notes/2002167)

### <a name="time-synchronization"></a>Tidssynkronisering

SAP-program som bygger på SAP NetWeaver-arkitekturen är känsliga för tidsskillnader för de olika komponenterna som utgör SAP-systemet. SAP ABAP korta dumpar med fel\_titeln\_\_ZDATE LARGE TIME DIFF är förmodligen bekant. Det beror på att dessa korta dumpar visas när systemtiden för olika servrar eller virtuella datorer driver för långt ifrån varandra.

För SAP HANA på Azure (stora instanser) gäller inte tidssynkronisering som görs i Azure för beräkningsenheterna i stora instansstämplar. Den här synkroniseringen gäller inte för att köra SAP-program i inbyggda virtuella Azure-datorer, eftersom Azure säkerställer att ett systems tid är korrekt synkroniserad. 

Därför måste du ställa in en separat tidsserver som kan användas av SAP-programservrar som körs på virtuella Azure-datorer och av SAP HANA-databasinstanserna som körs på STORA HANA-instanser. Lagringsinfrastrukturen i stora instansstämplar är tidssynkronisering med NTP-servrar.


## <a name="networking"></a>Nätverk
Vi förutsätter att du följde rekommendationerna när du utformar dina virtuella Azure-nätverk och när du ansluter dessa virtuella nätverk till DE STORA HANA-instanserna, enligt beskrivningen i följande dokument:

- [SAP HANA (Stor instans) översikt och arkitektur på Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [SAP HANA-infrastruktur (Stora instanser) och anslutning på Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Det finns några detaljer värda att nämna om nätverk av de enskilda enheterna. Varje HANA-enhet med stor instans levereras med två eller tre IP-adresser som tilldelas två eller tre nätverkskortportar. Tre IP-adresser används i HANA-utskalningskonfigurationer och SCENARIOT FÖR HANA-systemreplikering. En av de IP-adresser som har tilldelats enhetens nätverkskort är utanför serverns IP-pool som beskrivs i [ÖVERSIKT ÖVER OCH ARKITEKTUR FÖR SAP HANA (Stora instanser) på Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).

Mer information om Ethernet-information för din arkitektur finns i scenarierna för [HLI-stöd.](hana-supported-scenario.md)

## <a name="storage"></a>Lagring

Lagringslayouten för SAP HANA på Azure (stora instanser) `service management` konfigureras av SAP HANA på Azure via rekommenderade SAP-riktlinjer. Dessa riktlinjer dokumenteras i [SAP HANA lagringskrav](https://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) vitbok. 

De grova storlekarna för de olika volymerna med de olika SKU:erna för stora instanser för DEA-förekomsterna dokumenteras i [ÖVERSIKT ÖVER OCH ARKITEKTUR FÖR SAP HANA (Stora instanser) på Azure](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Namngivningskonventionerna för lagringsvolymerna visas i följande tabell:

| Användning av lagring | Mount namn | Volymnamn | 
| --- | --- | ---|
| HANA-data | /hana/data/SID/mnt0000\<m> | Lagrings-IP:/hana_data_SID_mnt00001_tenant_vol |
| HANA-logg | /hana/log/SID/mnt000\<m> | Lagring IP:/hana_log_SID_mnt00001_tenant_vol |
| Hana-loggsäkerhet | /hana/log/backups /backups /hana/log/backups /log/backups /han | Lagrings-IP:/hana_log_backups_SID_mnt00001_tenant_vol |
| HANA har delat | /hana/shared/SID | Lagrings-IP:/hana_shared_SID_mnt00001_tenant_vol/delad |
| usr/sap | /usr/sap/SID | Lagring IP:/hana_shared_SID_mnt00001_tenant_vol/usr_sap |

*SID* är HANA-instansens system-ID. 

*Klienten* är en intern uppräkning av åtgärder när du distribuerar en klient.

HANA usr/sap har samma volym. Monteringspunkternas nomenklatur omfattar system-ID för HANA-instanserna samt monteringsnumret. I uppskalningsdistributioner finns det bara ett fäste, till exempel mnt00001. I utskalningsdistributioner ser du däremot lika många fästen som du har arbets- och huvudnoder. 

För utskalningsmiljöer delas och logga säkerhetskopieringsvolymer till varje nod i skalningskonfigurationen. För konfigurationer som är flera SAP-instanser skapas en annan uppsättning volymer och kopplas till HANA Large Instance-enheten. Information om lagringslayout för ditt scenario finns i [scenarier som stöds av HLI](hana-supported-scenario.md).

När du tittar på en HANA Large Instance-enhet inser du att enheterna levereras med generös diskvolym för HANA/data och att det finns en volym HANA/log/backup. Anledningen till att vi gjorde HANA/data så stora är att de ögonblicksbilder av lagring som vi erbjuder dig som kund använder samma diskvolym. Ju fler ögonblicksbilder av lagringsutrymme du utför, desto mer utrymme förbrukas av ögonblicksbilder i dina tilldelade lagringsvolymer. 

Hana/log/backup-volymen ska inte vara volymen för säkerhetskopiering av databaser. Det är dimensionerat som ska användas som säkerhetskopieringsvolym för HANA-transaktionsloggens säkerhetskopior. Mer information finns i [SAP HANA (Stora instanser) hög tillgänglighet och haveriberedskap på Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Förutom den lagring som tillhandahålls kan du köpa ytterligare lagringskapacitet i steg om 1 TB. Den här ytterligare lagringen kan läggas till som nya volymer i en stor HANA-instans.

Under introduktion med SAP HANA `service management`på Azure anger kunden ett användar-ID (UID) och grupp-ID (GID) för gruppen sidadm-användare och sapsys (till exempel: 1000 500). Under installationen av SAP HANA-systemet måste du använda samma värden. Eftersom du vill distribuera flera HANA-instanser på en enhet får du flera uppsättningar volymer (en uppsättning för varje instans). Därför måste du vid distributionen definiera:

- SIDen av de olika HANA-ank fallen (sidadm härleds från den).
- Minnesstorlekarna för de olika HANA-instanserna. Minnesstorleken per instans definierar storleken på volymerna i varje enskild volymuppsättning.

Baserat på rekommendationer för lagringsprovidern konfigureras följande monteringsalternativ för alla monterade volymer (exklusive start-LUN):

- nfs rw, vers=4, hård, timeo=600, rsize=1048576, wsize=1048576, intr, noatime, lock 0 0

Dessa monteringspunkter är konfigurerade i /etc/fstab som visas i följande grafik:

![fstab av monterade volymer i HANA Large Instance enhet](./media/hana-installation/image1_fstab.PNG)

Utdata för kommandot df -h på en S72m HANA Large Instance-enhet ser ut så här:

![fstab av monterade volymer i HANA Large Instance enhet](./media/hana-installation/image2_df_output.PNG)


Lagringsstyrenheten och noderna i stora instansstämplarna synkroniseras med NTP-servrar. När du synkroniserar SAP HANA på Azure-enheter (stora instanser) och virtuella Azure-datorer mot en NTP-server, bör det inte finnas någon betydande tidsdrift mellan infrastrukturen och beräkningsenheterna i Azure- eller Large Instance-stämplarna.

Om du vill optimera SAP HANA till det lagringsutrymme som används under ställer du in följande SAP HANA-konfigurationsparametrar:

- max_parallel_io_requests 128
- async_read_submit på
- async_write_submit_active på
- async_write_submit_blocks alla
 
För SAP HANA 1.0-versioner upp till SPS12 kan dessa parametrar ställas in under installationen av SAP HANA-databasen, enligt beskrivningen i [SAP note #2267798 - Konfiguration av SAP HANA-databasen](https://launchpad.support.sap.com/#/notes/2267798).

Du kan också konfigurera parametrarna efter SAP HANA-databasinstallationen med hjälp av hdbparam-ramverket. 

Lagringen som används i STORA HANA-instanser har en filstorleksbegränsning. [Storleksbegränsningen är 16 TB](https://docs.netapp.com/ontap-9/index.jsp?topic=%2Fcom.netapp.doc.dot-cm-vsmg%2FGUID-AA1419CF-50AB-41FF-A73C-C401741C847C.html) per fil. Till skillnad från i filstorleksbegränsningar i FILSYSTEMEN EXT3 är HANA inte underförstått medveten om den lagringsbegränsning som tillämpas av HANA Large Instances-lagringen. Det innebär att HANA inte automatiskt skapar en ny datafil när filstorleksgränsen på 16 TB har uppnåtts. När HANA försöker utöka filen efter 16 TB kommer HANA att rapportera fel och indexservern kraschar i slutet.

> [!IMPORTANT]
> För att förhindra att HANA försöker utöka datafiler utöver 16 TB-filstorleksgränsen för HANA Large Instance-lagring måste du ange följande parametrar i konfigurationsfilen FÖR SAP HANA global.ini
> 
> - datavolume_striping=sant
> - datavolume_striping_size_gb = 15000
> - Se även SAP note [#2400005](https://launchpad.support.sap.com/#/notes/2400005)
> - Var medveten om [SAP-#2631285](https://launchpad.support.sap.com/#/notes/2631285)


Med SAP HANA 2.0 har hdbparam-ramen blivit inaktuell. Därför måste parametrarna ställas in med hjälp av SQL-kommandon. Mer information finns i [SAP note #2399079: Eliminering av hdbparam i HANA 2](https://launchpad.support.sap.com/#/notes/2399079).

Mer information om lagringslayouten för din arkitektur finns i [HLI-scenarier som stöds.](hana-supported-scenario.md)


**Nästa steg**

- Se [HANA Installation på HLI](hana-example-installation.md)










































 







 




