---
title: Metodtips för StorSimple Virtual Array | Microsoft Docs
description: Beskriver Metodtips för distribution och hantering av StorSimple Virtual Array.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 57ac6eeb-c47c-442d-a5f4-b360d81a76a6
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/08/2018
ms.author: alkohli
ms.openlocfilehash: b8e9f12a549f71971c2da3b9865f6a74dad58f61
ms.sourcegitcommit: d372d75558fc7be78b1a4b42b4245f40f213018c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2018
ms.locfileid: "51300922"
---
# <a name="storsimple-virtual-array-best-practices"></a>Metodtips för StorSimple Virtual Array
## <a name="overview"></a>Översikt
Microsoft Azure StorSimple Virtual Array är en integrerad lagringslösning som hanterar lagringsuppgifter mellan en lokal virtuell enhet som körs i ett hypervisor-programmet och Microsoft Azure-molnlagring. StorSimple Virtual Array är en effektiv och kostnadseffektiv alternativ till den fysiska 8000-serie-matrisen. Den virtuella matrisen kan köras på din befintliga infrastruktur för hypervisor-program, stöder både iSCSI- och SMB-protokoll och lämpar sig väl för scenarier med fjärranslutna kontor/avdelningskontor. Mer information om StorSimple-lösningarna går du till [översikt över Microsoft Azure StorSimple](https://www.microsoft.com/en-us/server-cloud/products/storsimple/overview.aspx).

Den här artikeln beskriver de rekommenderade metoder som införts under installationen, distribution och hantering av StorSimple Virtual Array. Dessa metodtips innehåller verifierade riktlinjer för installation och hantering av den virtuella matrisen. Den här artikeln är avsedd för IT-administratörer som distribuerar och hanterar virtuella matriser i sina datacenter.

Vi rekommenderar en regelbunden granskning av de bästa metoderna för att säkerställa att enheten är fortfarande i efterlevnad när ändringar görs på installations- eller Flow. Bör du får problem när du implementerar dessa metodtips på din virtuella matris [kontakta Microsoft Support](storsimple-virtual-array-log-support-ticket.md) för att få hjälp.

## <a name="configuration-best-practices"></a>Metodtips för konfiguration
Dessa metodtips täcker de riktlinjer som måste följas under den första installationen och distributionen av virtuella matriser. Dessa metodtips är de som rör etablering av den virtuella datorn, grupprincipinställningar, storlek, att konfigurera nätverket, konfigurera storage-konton och skapa filresurser och volymer för den virtuella matrisen. 

### <a name="provisioning"></a>Etablering
StorSimple Virtual Array är en virtuell dator (VM) som har etablerats på hypervisor-programmet (Hyper-V eller VMware) på värdservern. Kontrollera att värden kan dedikera tillräckligt med resurser när du etablerar den virtuella datorn. Mer information går du till [minsta resurskraven](storsimple-virtual-array-deploy2-provision-hyperv.md#step-1-ensure-that-the-host-system-meets-minimum-virtual-array-requirements) att etablera en matris.

Implementera följande metodtips när du etablerar den virtuella matrisen:

|  | Hyper-V | VMware |
| --- | --- | --- |
| **Typ av virtuell dator** |**Generation 2** virtuell dator för användning med Windows Server 2012 eller senare och en *.vhdx* bild. <br></br> **Generation 1** virtuell dator för användning med Windows Server 2008 eller senare och en *VHD* bild. |Använd VM version 8 när du använder *.vmdk* bild. |
| **Minnestyp** |Konfigurera som **statiskt minne**. <br></br> Använd inte den **dynamiskt minne** alternativet. | |
| **Datatypen för disk** |Etablera som **dynamiskt expanderande**.<br></br> **Fast storlek** tar lång tid. <br></br> Använd inte den **differentierande** alternativet. |Använd den **tunn etablering** alternativet. |
| **Data diskändring** |Expandera eller minska storleken är inte tillåtet. Ett försök att göra detta leder till förlust av alla lokala data på enheten. |Expandera eller minska storleken är inte tillåtet. Ett försök att göra detta leder till förlust av alla lokala data på enheten. |

### <a name="sizing"></a>Storleksändring
När du ändrar storlek StorSimple Virtual Array, Tänk på följande faktorer:

* Lokal reservation för volymer eller resurser. Cirka 12% av utrymmet har reserverats på den lokala nivån för varje etablerade nivåindelad volym eller resurs. 10% av utrymmet är ungefär också reserverad för en lokalt Fäst volym för filsystem.
* Overhead ögonblicksbild. Ungefär är 15% utrymme på den lokala nivån reserverad för ögonblicksbilder.
* Du behöver för återställningar. Om du gör återställningen som en ny åtgärd bör storlek hänsyn till utrymmet som krävs för återställning. Återställningen görs till en resurs eller en volym med samma storlek.
* Vissa bufferten ska allokeras för eventuella oväntade tillväxt.

Utifrån föregående faktorer kan storlekskraven visas med följande formel:

`Total usable local disk size = (Total provisioned locally pinned volume/share size including space for file system) + (Max (local reservation for a volume/share) for all tiered volumes/share) + (Local reservation for all tiered volumes/shares)`

`Data disk size = Total usable local disk size + Snapshot overhead + buffer for unexpected growth or new share or volume`

I följande exempel visas hur du kan ändra storlek på en virtuell matris baserat på dina krav.

#### <a name="example-1"></a>Exempel 1:
På din virtuella matris som du vill kunna

* etablera en 2 TB nivåindelad volym eller resurs.
* etablera en 1 TB nivåindelad volym eller resurs.
* etablera en 300 GB med lokalt fixerad volym eller resurs.

För den föregående volymer eller resurser kan vi beräkna krav på diskutrymme på den lokala nivån.

Först för varje nivåindelad volym/resurs, skulle lokal reservation vara lika med 12% av storleken på volym/resurs. För lokalt Fäst volym/resurs är lokal reservation 10% av storleken på lokalt Fäst volym/resurs (utöver storleken). I det här exemplet behöver du

* 240 GB lokal reservation (för en 2 TB nivåindelad volym/resurs)
* 120 GB lokal reservation (för en 1 TB nivåindelad volym/resurs)
* 330 GB för lokalt fixerad volym eller resurs (lägger till 10% av lokal reservation till 300 GB etablerad storlek)

Det totala utrymmet som krävs på den lokala nivån hittills är: 240 GB + 120 GB + 330 GB = 690 GB.

Dessutom måste minst lika mycket utrymme på den lokala nivån som största enda reservationen. Den extra mängden används om du vill återställa från en ögonblicksbild i molnet. I det här exemplet är den största lokala reservationen 330 GB (inklusive reservation för file system), så lägger du till att till 690 GB: 690 GB + 330 GB = 1020 GB.
Om vi utförde efterföljande ytterligare återställningar kan vi alltid Frigör utrymme på den föregående återställningsåtgärden.

Vi behöver för det tredje 15% av din totala lokalt utrymme än så länge för att lagra lokala ögonblicksbilder, så att endast 85% av den är tillgänglig. I det här exemplet som skulle vara runt 1020 GB = 0.85&ast;etablerade data disk TB. Så etablerade datadisken skulle vara (1020&ast;(1/0.85)) = 1200 GB = 1,20 TB ~ 1,25 TB (avrundning till närmaste kvartilen)

Ta hänsyn till oväntad storleksökning och nya återställningar, bör du tillhandahålla en lokal disk av runt 1.25 – 1,5 TB.

> [!NOTE]
> Vi rekommenderar också att den lokala disken är tunt etablerad. Denna rekommendation beror på området återställning krävs bara när du vill återställa data som är äldre än fem dagar. Objektnivååterställning kan du återställa data för de senaste fem dagarna utan extra utrymme för återställning.


#### <a name="example-2"></a>Exempel 2:
På din virtuella matris som du vill kunna

* etablera en nivåindelad volym 2 TB
* etablera en lokalt Fäst volym 300 GB

Baserat på 12% av reservation lokala utrymmet för nivåindelade volymer/resurser och 10% för lokalt fixerade volymer/resurser, som vi behöver

* 240 GB lokal reservation (2 TB skikt i volym/resurs)
* 330 GB för lokalt fixerad volym eller resurs (lägger till 10% av lokal reservation till området 300 GB som etablerats)

Totalt utrymme som krävs på den lokala nivån är: 240 GB + 330 GB = 570 GB

Minsta lokala utrymmet som krävs för återställning är 330 GB.

15% av total disken används för att lagra ögonblicksbilder så att endast 0.85 är tillgänglig. Så, diskens storlek är (900&ast;(1/0.85)) = 1,06 TB ~ 1,25 TB (avrundning till närmaste kvartilen)

Du kan ta hänsyn till eventuella oväntade tillväxten, för att etablera en lokal disk 1.25 – 1,5 TB.

### <a name="group-policy"></a>Grupprincip
Grupprincip är en infrastruktur som gör det möjligt att implementera specifika konfigurationer för användare och datorer. Inställningar av grupprinciper finns i grupprincipobjekt (GPO), som är länkade till följande Active Directory Domain Services (AD DS)-behållare: platser, domäner eller organisationsenheter (OU). 

Om din virtuella matris är ansluten till domänen, kan grupprincipobjekt tillämpas på den. Dessa grupprincipobjekt kan installera program, till exempel ett antivirusprogram som kan påverka driften av StorSimple Virtual Array negativt.

Därför rekommenderar vi att du:

* Kontrollera att din virtuella matris är i sin egen organisationsenhet (OU) för Active Directory.
* Se till att inga grupprincipobjekt (GPO) används för den virtuella matrisen. Du kan blockera arv för att säkerställa att den virtuella matrisen (underordnad nod) automatiskt inte ärver några GPO: er från överordnat. Mer information går du till [blockera arv](https://technet.microsoft.com/library/cc731076.aspx).

### <a name="networking"></a>Nätverk
Nätverkskonfiguration för din virtuella matris görs via det lokala webbgränssnittet. Ett virtuellt nätverksgränssnitt aktiveras via hypervisor-programmet där den virtuella matrisen har etableras. Använd den [nätverksinställningar](storsimple-virtual-array-deploy3-fs-setup.md) att konfigurera det virtuella nätverket gränssnittets IP-adress, undernät och gateway.  Du kan också konfigurera den primära och sekundära DNS-server och tidsinställningar proxyinställningar för din enhet. De flesta av nätverkskonfigurationen är konfigurationen en gång. Granska den [StorSimple nätverkskraven](storsimple-ova-system-requirements.md#networking-requirements) innan du distribuerar den virtuella matrisen.

Vi rekommenderar att du följer dessa bästa metoder när du distribuerar den virtuella matrisen:

* Kontrollera att nätverket där den virtuella matrisen distribueras alltid har kapacitet att dedikera 5 Mbit/s bandbredd för Internet (eller mer).
  
  * Behovet av bandbredd för Internet varierar beroende på arbetsbelastningens och frekvens av dataändringar.
  * Dataändring som kan hanteras står i direkt proportion till Internet-bandbredd. Till exempel när du tar en säkerhetskopia av kan en 5 Mbit/s bandbredd hantera en dataändring cirka 18 GB inom åtta timmar. Med fyra gånger mer bandbredd (20 Mbit/s), kan du hantera fyra gånger mer data ändras (72 GB).
* Kontrollera anslutningen till Internet alltid är tillgänglig. Sporadiska eller otillförlitliga Internet-anslutningar till enheterna som kan leda till förlust av åtkomst till data i molnet och kan resultera i en konfiguration som inte stöds.
* Om du planerar att distribuera en enhet som en iSCSI-server:
  
  * Vi rekommenderar att du inaktiverar den **får IP-adress automatiskt** alternativet (DHCP).
  * Konfigurera statiska IP-adresser. Du måste konfigurera en primär och en sekundär DNS-server.
  * Om definierar flera nätverksgränssnitt på din virtuella matris, bara det första nätverksgränssnittet (det här gränssnittet är som standard **Ethernet**) kan nå ut till molnet. Du kan skapa flera virtuella nätverksgränssnitt på den virtuella matrisen (konfigurerat som en iSCSI-server) och ansluta dessa gränssnitt till olika undernät för att styra vilken typ av trafik.
* Att begränsa den moln bandbredden (används av den virtuella matrisen), konfigurera begränsning på routern eller brandväggen. Om du definierar begränsning i din hypervisor-program, kommer den begränsning alla protokoll, inklusive iSCSI och SMB i stället för bara bandbredden som molnet.
* Se till att tidssynkronisering för hypervisorer är aktiverat. Om du använder Hyper-V, väljer din virtuella matris i Hyper-V Manager, går du till **inställningar &gt; Integration Services**, och se till att den **tidssynkronisering** kontrolleras.

### <a name="storage-accounts"></a>Lagringskonton
StorSimple Virtual Array kan associeras med ett enda lagringskonto. Det här lagringskontot kan vara en automatiskt genererad storage-konto, ett konto i samma prenumeration som tjänsten, eller ett lagringskonto som är relaterade till en annan prenumeration. Läs mer om hur du [hantera lagringskonton för den virtuella matrisen](storsimple-virtual-array-manage-storage-accounts.md).

Använd följande rekommendationer för lagringskonton som är associerade med din virtuella matris.

* När du länkar flera virtuella matriser med ett enda lagringskonto ta maximala kapacitet (64 TB) för en virtuell matris och den maximala storleken (500 TB) för ett lagringskonto. Detta begränsar antalet full storlek virtuella matriser som kan associeras med lagringskontot i cirka 7.
* När du skapar ett nytt lagringskonto
  
  * Vi rekommenderar att du skapar den i regionen närmast office fjärranslutna kontor/avdelningskontor där StorSimple Virtual Array distribueras för att minimera svarstider.
  * Ha i åtanke som du inte kan flytta ett lagringskonto i olika regioner. Du kan också flytta en tjänst mellan prenumerationer.
  * Använd ett lagringskonto som implementerar redundans mellan datacenter. GEO-Redundant lagring (GRS), Zonredundant lagring (ZRS) och lokalt Redundant lagring (LRS) stöds för användning med din virtuella matris. Mer information om de olika typerna av lagringskonton går du till [Azure storage-replikering](../storage/common/storage-redundancy.md).

### <a name="shares-and-volumes"></a>Filresurser och volymer
Du kan etablera resurser när den är konfigurerad som en filserver och volymer när konfigurerad som en iSCSI-server för din StorSimple Virtual Array. Metodtips för att skapa resurserna och volymerna är relaterade till storleken och typ som har konfigurerats.

#### <a name="volumeshare-size"></a>Volym/resurs storlek
Du kan etablera resurser när den är konfigurerad som en filserver och volymer när konfigurerad som en iSCSI-server på den virtuella matrisen. Metodtips för att skapa resurserna och volymerna är relaterade till storleken och typ som har konfigurerats. 

Tänk på följande metodtips när du etablerar resurser eller volymer på den virtuella enheten.

* Lagringsnivåer prestanda kan påverkas av filstorlekar i förhållande till den etablerade storleken för en nivåindelad resurs. Arbeta med stora filer kan resultera i en långsam nivå ut. När du arbetar med stora filer, rekommenderar vi att den största filen är mindre än 3 procent av resursstorleken.
* Högst 16 volymer/resurser kan skapas på den virtuella matrisen. Storleksgränser volymer/resurser lokalt fixerade och nivåindelade alltid finns i den [begränsningar för StorSimple Virtual Array](storsimple-ova-limits.md).
* När du skapar en volym, faktor i den förväntade dataförbrukning samt framtida tillväxt. Volymen kan inte utökas senare.
* När volymen har skapats, kan du minska storleken på volymen på StorSimple.
* När du skriver till en nivåindelad volym på StorSimple, när volymdata når ett visst tröskelvärde (i förhållande till det lokala utrymmet som är reserverat för volymen), begränsas I/O. Du kan fortsätta att skriva till den här volymen saktar ned I/O avsevärt. Även om du kan skriva till en nivåindelad volym utöver dess etablerad kapacitet (vi inte aktivt stoppar användaren från att skriva utöver etablerad kapacitet), visas en avisering om att du har oversubscribed. När du ser aviseringen, är det viktigt att du vidtar åtgärder, till exempel ta bort volymdata (volym expansion är för närvarande stöds inte).
* För disaster recovery-användningsfall, eftersom antalet tillåtna resurser/volymer är 16 och det maximala antalet resurser/volymer som kan bearbetas parallellt även 16, har antalet resurser/volymer inte betydelse på RPO och RTO.

#### <a name="volumeshare-type"></a>Typ av volym/resurs
StorSimple stöder två typer av volym/resurs baserat på användning: lokalt Fäst och nivåer. Lokalt fixerade volymer/resurser etableras tjockt medan nivåindelade volymer/resurser är tunt etablerad. Du kan inte konvertera en lokalt Fäst volym/resurs till nivåindelade eller *tvärtom* efter att de skapats.

Vi rekommenderar att du implementera följande metodtips när du konfigurerar StorSimple-volymer/resurser:

* Identifiera den typ av volym utifrån de arbetsbelastningar som du avser att distribuera innan du skapar en volym. Använd lokalt fixerade volymer för arbetsbelastningar som kräver lokala garantier av data (även under ett avbrott i molnet) och som kräver låg molnet svarstider. När du skapar en volym på din virtuella matris kan du inte ändra typ av volym från lokalt fixerade till nivåindelade eller *vice versa*. Skapa lokalt fixerade volymer kan t.ex, när du distribuerar SQL-arbetsbelastningar eller arbetsbelastningar som är värd för virtuella datorer (VM); Använd nivåindelade volymer för filen resurs arbetsbelastningar.


#### <a name="volume-format"></a>Volym-format
När du skapar StorSimple-volymer på iSCSI-servern, måste du initiera, montera och formatera volymerna. Den här åtgärden utförs på värden är ansluten till din StorSimple-enhet. Följande metodtips rekommenderas när montera och formatera volymer på StorSimple-värden.

* Utför en snabbformatering på alla StorSimple-volymer.
* Formaterar en StorSimple-volym och använda en storlek på allokeringsenhet (AU) på 64 KB (standardvärdet är 4 KB). 64 KB AU: er baseras på testning görs inom företaget för vanliga StorSimple arbetsbelastningar och andra arbetsbelastningar.
* När du använder StorSimple Virtual Array konfigurerad som en iSCSI-server, Använd inte volymer eller dynamiska diskar som dessa volymer eller diskar stöds inte av StorSimple.

#### <a name="share-access"></a>Åtkomst till resursen
När du skapar resurser på din virtuella matris filserver kan du följa dessa riktlinjer:

* När du skapar en resurs kan du tilldela en användargrupp som resursen administratör i stället för en enskild användare.
* När resursen har skapats genom att redigera filresurser via Windows Explorer kan du hantera NTFS-behörigheter.

#### <a name="volume-access"></a>Åtkomst till en volym
När du konfigurerar iSCSI-volymer på StorSimple Virtual Array, är det viktigt att kontrollera åtkomsten behov. För att avgöra vilka värdservrar som kan komma åt volymer, skapa och associera åtkomstkontrollposter (åtkomstkontrollposter) med StorSimple-volymer.

Använd följande metodtips när du konfigurerar åtkomstkontrollposter för StorSimple-volymer:

* Associera alltid minst en ACR med en volym.

* När du tilldelar fler än en ACR till en volym, måste du kontrollera att volymen inte exponeras på ett sätt där det kan samtidigt användas av fler än en icke-klustrad värd. Om du har tilldelat flera åtkomstkontrollposter till en volym, visas ett varningsmeddelande, som du kan granska konfigurationen.

### <a name="data-security-and-encryption"></a>Datasäkerhet och kryptering
StorSimple Virtual Array har data encryption funktioner för säkerhet och som ser till att sekretessen och integriteten av dina data. När du använder dessa funktioner, rekommenderar vi att du följer dessa metodtips: 

* Definiera en krypteringsnyckel för molnlagring för att generera AES-256-kryptering innan data skickas från din virtuella matris till molnet. Den här nyckeln är inte nödvändigt om krypteras dina data till att börja med. Nyckeln kan skapas och förblir säkra med en nyckelhanteringssystem som [Azure-nyckelvalv](../key-vault/key-vault-whatis.md).
* När du konfigurerar storage-konto via StorSimple Manager-tjänsten kan du se till att du aktiverar SSL-läge att skapa en säker kanal för nätverkskommunikation mellan din StorSimple-enhet och molnet.
* Återskapa nycklar för storage-konton (genom att gå till Azure Storage-tjänsten) med jämna mellanrum på grund av eventuella ändringar att få åtkomst till baserat på den ändrade listan över administratörer.
* Data på din virtuella matris komprimeras och dedupliceras innan den skickas till Azure. Vi rekommenderar inte använda rolltjänsten Datadeduplicering på Windows Server-värd.

## <a name="operational-best-practices"></a>Metodtips för fortlöpande
Metodtips för fortlöpande är riktlinjer som ska följas under den dagliga driften eller driften av den virtuella matrisen. De här metoderna omfattar specifika hanteringsuppgifter, till exempel att säkerhetskopiera, återställa från en säkerhetskopia, utför en redundansväxling, inaktivera och ta bort matris, övervakning av systemanvändning och hälsotillstånd och köra virus-sökningar på din virtuella matris.

### <a name="backups"></a>Säkerhetskopior
Data på din virtuella matris har säkerhetskopierats till molnet på två sätt, en standard automatisk daglig säkerhetskopiering av hela enheten startar klockan 22.30 eller via en manuell säkerhetskopiering på begäran. Som standard skapar enheten automatiskt dagliga ögonblicksbilder av molnet för alla data som finns på denna. Mer information går du till [säkerhetskopiera StorSimple Virtual Array](storsimple-virtual-array-backup.md).

Frekvensen och kvarhållning av säkerhetskopior som är associerade med säkerhetskopiorna som standard kan inte ändras, men du kan konfigurera tiden då de dagliga säkerhetskopiorna startas varje dag. När du konfigurerar starttiden för de automatiska säkerhetskopieringarna, rekommenderar vi att:

* Schemalägga säkerhetskopiorna för låg belastning. Starttid för säkerhetskopiering bör inte sammanfalla med ett stort antal i/o-värden.
* Starta en manuell säkerhetskopiering på begäran när du planerar att utföra en redundansväxling av enhet eller före underhållsperiod, att skydda data på din virtuella matris.

### <a name="restore"></a>Återställ
Du kan återställa från en säkerhetskopia på två sätt: återställa till en annan volym eller resurs eller utföra en återställning (finns endast på en virtuell matris som konfigurerats som en filserver). Objektnivååterställning kan du göra en detaljerad återställning av filer och mappar från en säkerhetskopia i molnet för alla resurser på StorSimple-enheten. Mer information går du till [återställa från en säkerhetskopia](storsimple-virtual-array-clone.md).

Tänk på följande riktlinjer när du utför en återställning:

* StorSimple Virtual Array stöder inte återställning på plats. Detta kan dock vara lätt uppnås genom en tvåstegsprocess: Frigör diskutrymme på den virtuella matrisen och Återställ sedan till en annan volym/resurs.
* Vid återställning från en lokal volym, Tänk på att återställningen en långvarig åtgärd. Även om volymen kan snabbt komma online, fortsätter data att vara hydrerat i bakgrunden.
* Typ av volym förblir densamma under återställningsprocessen. En nivåindelad volym återställs till en annan nivåindelad volym och en lokalt Fäst volym till en annan lokalt Fäst volym.
* När du försöker återställa en volym eller en resurs från en säkerhetskopia om återställningsjobbet misslyckas, ange en målvolymen eller resursen fortfarande skapas i portalen. Det är viktigt att du tar bort den här oanvända målvolymen eller dela i portalen för att minimera eventuella framtida problem som härrör från det här elementet.

### <a name="failover-and-disaster-recovery"></a>Redundans och haveriberedskap
En redundansväxling av enhet kan du migrera dina data från en *källa* enheten i datacentret till en annan *target* enheten finns i samma eller en annan geografisk plats. Redundansväxling av enhet är för hela enheten. Under redundansväxlingen ändras molndata för källenheten ägarskap till som målenheten.

För StorSimple Virtual Array, kan du bara växla över till en annan virtuell matris som hanteras av samma StorSimple Manager-tjänsten. En redundansväxling till en 8000-serien-enhet eller en matris som hanteras av en annan StorSimple Manager-tjänst (än som för källenheten) är inte tillåtet. Mer information om överväganden för växling vid fel, gå till [krav för redundansväxling av enhet](storsimple-virtual-array-failover-dr.md).

När du utför en växling vid fel över för din virtuella matris kan ha följande i åtanke:

* Det är rekommenderad praxis att ta alla volymer/resurser offline innan du utför redundans för en planerad redundans. Följ instruktionerna operativsystemspecifika första att dra volymer/resurser offline på värden och sedan vidta de offline på den virtuella enheten.
* För en fil haveriberedskap (DR) rekommenderar vi att du ansluter målenheten till samma domän som källan så att behörigheter till resursen löses automatiskt. Den här versionen stöder endast redundans till en målenhet i samma domän.
* När ar har slutförts, tas källenheten bort automatiskt. Om enheten är inte längre tillgänglig, är den virtuella datorn som du har etablerats på värdsystemet fortfarande förbruka resurser. Vi rekommenderar att du tar bort den här virtuella datorn från värdsystemet för att förhindra att belasta betalar.
* Att Observera att även om redundansen misslyckas, **data alltid är säkra i molnet**. Tänk på följande tre scenarier där redundansväxlingen inte slutfördes korrekt:
  
  * Ett fel inträffade under de första stegen för växling vid fel, till exempel när DR förhandskontroller utförs. I så fall kan är din målenheten fortfarande kan användas. Du kan göra om redundans på samma målenheten.
  * Ett fel uppstod under riktig redundansväxling. I det här fallet markeras målenheten inte kan användas. Du måste etablera och konfigurera en annan virtuell matris för mål och använda det för redundans.
  * Redundansväxlingen slutfördes varefter källenheten har tagits bort men målenheten har problem och du åtkomst till inte några data. Data är fortfarande säkert i molnet och enkelt kan hämtas genom att skapa en annan virtuell matris och använda det som en målenhet för DR.

### <a name="deactivate"></a>Inaktivera
När du inaktiverar en StorSimple Virtual Array Server anslutningen mellan enheten och motsvarande StorSimple Manager-tjänsten. Inaktiveringen är en **permanent** åtgärden och kan inte ångras. En inaktiverad enhet kan inte registreras med StorSimple Manager-tjänsten igen. Mer information går du till [inaktivera och ta bort StorSimple Virtual Array](storsimple-virtual-array-deactivate-and-delete-device.md).

Tänk på följande metodtips när du inaktiverar den virtuella matrisen:

* Ta en molnögonblicksbild av alla data innan du inaktiverar en virtuell enhet. När du inaktiverar en virtuell matris förloras alla lokala enhetsdata. Tar en ögonblicksbild i molnet kan du återställa data i ett senare skede.
* Innan du inaktiverar en StorSimple Virtual Array, se till att stoppa eller ta bort klienter och värdar som beror på den enheten.
* Ta bort en inaktiverad enhet om du inte längre använder så att den inte påföras kostnader.

### <a name="monitoring"></a>Övervakning
För att säkerställa att din StorSimple Virtual Array är en kontinuerlig felfritt, måste du övervaka matrisen och du får information från systemet, inklusive aviseringar. Implementera följande metodtips för att övervaka den övergripande hälsan för den virtuella matrisen:

* Konfigurera övervakning för att spåra diskanvändningen för din virtuella matris datadisk samt OS-disken. Om kör Hyper-V kan använda du en kombination av System Center Virtual Machine Manager (SCVMM) och System Center Operations Manager för att övervaka din virtualiseringsvärdar.
* Konfigurera e-postmeddelanden på din virtuella matris att skicka aviseringar på vissa nivåer för användning.                                                                                                                                                                                                

### <a name="index-search-and-virus-scan-applications"></a>Search index och virus skanna program
En StorSimple Virtual Array kan automatiskt delar in data från den lokala nivån till Microsoft Azure-molnet. När ett program, till exempel en indexsökning eller en virusgenomsökning används för att skanna data som lagras i StorSimple, måste du noga med att molninformationen inte komma åt och dras tillbaka till den lokala nivån.

Vi rekommenderar att du implementera följande metodtips när du konfigurerar indexsökning search eller virus på den virtuella matrisen:

* Inaktivera åtgärder automatiskt konfigurerade fullständig genomsökning.
* Konfigurera index search eller virus genomsökning programmet att utföra en inkrementell skanning för nivåindelade volymer. Detta skulle skanna endast den nya data sannolikt som finns på den lokala nivån. Data som är nivåindelade till molnet används inte under en inkrementell.
* Se till att rätt sökfilter och inställningar har konfigurerats så att de avsedda typerna av filer hämta genomsöks. Till exempel bildfiler (JPEG, GIF- och TIFF) och tekniska ritningar bör inte genomsöks under inkrementell eller fullständig index återskapas.

Om du använder Windows indexering processen, Följ dessa riktlinjer:

* Använd inte Windows-indexeraren för nivåindelade volymer eftersom det återkallar stora mängder data (TB) från molnet om indexet måste återskapas ofta. Återskapa indexet skulle hämta alla filtyper för att indexera sitt innehåll.
* Använd Windows indexering processen för lokalt fästa volymer eftersom detta skulle bara komma åt data på de lokala nivåerna och skapa index (molndata inte kommer att komma åt).

### <a name="byte-range-locking"></a>Låsning byte-intervall
Program kan låsa ett angivet antal byte i filerna. Om låsning av byte-intervallet är aktiverad på de program som skriver till StorSimple, fungerar inte sedan lagringsnivåer på den virtuella matrisen. Alla delar av filer som öppnats ska vara upplåst för lagringsnivåer för att fungera. Byte-intervallet låsning stöds inte med nivåindelade volymer på den virtuella matrisen.

Rekommenderade åtgärder för att minska låsning byte-intervallet är:

* Inaktivera byteintervall låsning i programlogiken.
* Använd lokalt fixerade volymer (i stället för med skikt) för de data som är associerade med det här programmet. Inte på datanivå lokalt fixerade volymer till molnet.
* När med hjälp av lokalt fixerade volymer med byte intervallet låsning aktiverat, är volymen online innan återställningen är klar. I så fall måste du vänta tills återställningen är klar.

## <a name="multiple-arrays"></a>Flera matriser
Flera virtuella matriser kan behöva distribueras på grund av ett växande arbetsminnet för data som kan läcker över till molnet därmed påverka prestanda för enheten. I så fall är det bäst att skala enheter när arbetsminnet växer. Detta kräver en eller flera enheter som ska läggas till i det lokala datacentralen. När du lägger till enheter, kan du:

* Dela upp den aktuella uppsättningen av data.
* Distribuera nya arbetsbelastningar till nya enheter.
* Om du distribuerar flera virtuella matriser, rekommenderar vi att från belastningsutjämning perspektiv, distribuera matrisen över olika hypervisor-värdar.
* Flera virtuella matriser (när den har konfigurerats som en filserver eller en iSCSI-server) kan distribueras i en Distributed File System Namespace. Detaljerade anvisningar finns i [Distributed File System Namespace lösning med Hybrid Cloud Storage Deployment Guide](https://www.microsoft.com/download/details.aspx?id=45507). Distribuerade Filtjänstreplikeringen rekommenderas inte för närvarande för användning med den virtuella matrisen. 

## <a name="see-also"></a>Se också
Lär dig hur du [administrera StorSimple Virtual Array](storsimple-virtual-array-manager-service-administration.md) via StorSimple Manager-tjänsten.

