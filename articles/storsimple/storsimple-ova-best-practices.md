---
title: "Bästa praxis för virtuell StorSimple-matrisen | Microsoft Docs"
description: "Rekommenderade säkerhetsmetoder för att distribuera och hantera den virtuella StorSimple-matrisen."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 57ac6eeb-c47c-442d-a5f4-b360d81a76a6
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/16/2018
ms.author: alkohli
ms.openlocfilehash: 46fd818d8ca15515c91bb6e65e99b0a3bc1f1fa4
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/17/2018
---
# <a name="storsimple-virtual-array-best-practices"></a>Metodtips för virtuell StorSimple-matris
## <a name="overview"></a>Översikt
Microsoft Azure StorSimple virtuell matrisen är en integrerad lagringslösning som hanterar lagringsuppgifter mellan en lokal virtuell enhet körs i ett hypervisor-programmet och Microsoft Azure-molnlagring. Virtuella StorSimple-matrisen är ett effektivt och mer kostnadseffektivt alternativ till fysiska matrisen 8000-serien. Virtuella matrisen kan köras på din befintliga infrastruktur för hypervisor-programmet stöder både iSCSI- och SMB-protokoll och lämpar sig väl för scenarier med fjärråtkomst office/avdelningskontor. Mer information om StorSimple-lösningar, gå till [översikt över Microsoft Azure StorSimple](https://www.microsoft.com/en-us/server-cloud/products/storsimple/overview.aspx).

Den här artikeln beskrivs bästa praxis implementeras under installationen, distribution och hantering av den virtuella StorSimple-matrisen. Följande rekommendationer innehåller validerade riktlinjer för installation och hantering av virtuella matrisen. Den här artikeln är avsedd för IT-administratörer som distribuerar och hanterar virtuella matriser i sina datacenter.

Vi rekommenderar en regelbunden granskning av bästa praxis för att säkerställa att din enhet är fortfarande i kompatibilitet när ändringar görs i installations- eller flödet. Ska det uppstår problem vid implementera tipsen på din virtuella matrisen [kontaktar Microsoft Support](storsimple-virtual-array-log-support-ticket.md) om du behöver hjälp.

## <a name="configuration-best-practices"></a>Metodtips för konfiguration
Följande rekommendationer täcker de riktlinjer som behöver följas under den första installationen och distributionen av virtuella matriser. Dessa bästa praxis är att de rör etableringen av den virtuella datorn, grupprincipinställningar, storlek, konfigurera nätverket, konfigurera storage-konton och skapa filresurser och volymer för den virtuella matrisen. 

### <a name="provisioning"></a>Etablering
StorSimple virtuell matrisen är en virtuell dator (VM) som har etablerats på hypervisor-programmet (Hyper-V eller VMware) på värdservern. Vid etablering av den virtuella datorn, se till att värden kan dedikera tillräckligt med resurser. Mer information finns på [minsta resurskraven](storsimple-virtual-array-deploy2-provision-hyperv.md#step-1-ensure-that-the-host-system-meets-minimum-virtual-array-requirements) att etablera en matris.

Implementera följande säkerhetsmetoder vid etablering av virtuella matrisen:

|  | Hyper-V | VMware |
| --- | --- | --- |
| **Typ av virtuell dator** |**Generation 2** virtuell dator för användning med Windows Server 2012 eller senare och en *.vhdx* bild. <br></br> **Generation 1** virtuell dator för användning med Windows Server 2008 eller senare och en *VHD* bild. |Använd virtuella version 8 när du använder *.vmdk* bild. |
| **Typ av minne** |Konfigurera som **statiskt minne**. <br></br> Använd inte den **dynamiskt minne** alternativet. | |
| **Datatypen för disk** |Tillhandahålla som **dynamiskt expanderande**.<br></br> **En fast storlek** tar lång tid. <br></br> Använd inte den **differentierande** alternativet. |Använd den **tunn etablering** alternativet. |
| **Disk dataändring** |Utökningen eller krympa tillåts inte. Ett försök att göra detta resulterar i förlust av alla lokala data på enheten. |Utökningen eller krympa tillåts inte. Ett försök att göra detta resulterar i förlust av alla lokala data på enheten. |

### <a name="sizing"></a>Storlek
Tänk på följande när du ändrar storlek på din virtuella StorSimple-matris:

* Lokala reservation för volymer eller resurser. Cirka 12% utrymme har reserverats på den lokala nivån för varje etablerade nivåindelad volym eller resurs. 10% av området är ungefär också reserverat för en lokalt Fäst volym för filsystem.
* Snapshot-omkostnader. Ungefär är 15% utrymmet på den lokala nivån reserverat för ögonblicksbilder.
* Behovet av återställningar. Om utför återställning som en ny operation bör storlek hänsyn till det utrymme som krävs för återställning. Återställning görs för en resurs eller en volym av samma storlek.
* Vissa buffert bör tilldelas för eventuella oväntade tillväxt.

Baserat på de föregående faktorerna kan sizing kraven representeras med följande formel:

`Total usable local disk size = (Total provisioned locally pinned volume/share size including space for file system) + (Max (local reservation for a volume/share) for all tiered volumes/share) + (Local reservation for all tiered volumes/shares)`

`Data disk size = Total usable local disk size + Snapshot overhead + buffer for unexpected growth or new share or volume`

I följande exempel visas hur du kan ändra storlek på en virtuell matris baserat på dina krav.

#### <a name="example-1"></a>Exempel 1:
På din virtuella matrisen som du vill kunna

* etablera en 2 TB nivåindelad volym eller resurs.
* etablera en 1 TB nivåindelad volym eller resurs.
* etablera en lokalt Fäst volym 300 GB eller filresurs.

Låt oss beräkna kraven på diskutrymme på den lokala nivån för för föregående volymer eller resurser.

Först för varje nivåindelad volym/resurs, skulle lokala reservationen vara lika med 12% volym/dela-storlek. För lokalt Fäst volym/dela är lokala reservationen 10% av lokalt Fäst volym/dela-storlek (utöver etablerade storlek). I det här exemplet behöver du

* 240 GB lokala reservation (2 TB skikt i volym/dela)
* 120 GB lokala reservation (skikt volym/dela för 1 TB)
* 330 GB för lokalt Fäst volym eller resurs (lägga till 10% av lokala reservation 300 GB etablerats storlek)

Det totala utrymmet som krävs på den lokala nivån hittills är: 240 GB + 120 GB + 330 GB = 690 GB.

Dessutom måste minst lika mycket utrymme på den lokala nivån som största enda reservationen. Den här extra belopp används om du behöver återställa från en ögonblicksbild i molnet. I det här exemplet är den största lokala reservationen 330 GB (inklusive reservation för file system), så lägger du till att den 690 GB: 690 GB + 330 GB = 1020 GB.
Om vi har genomfört efterföljande ytterligare återställningar kan vi alltid frigöra utrymme från föregående återställningsåtgärd.

Vi behöver tredje, 15% av din lokala totalt utrymme så långt för att lagra lokala ögonblicksbilder så att endast 85% av den är tillgänglig. I det här exemplet som skulle vara runt 1020 GB = 0.85&ast;etablerade data disk TB. Därför etablerade datadisken skulle vara (1020&ast;(1/0.85)) = 1200 GB = 1,20 TB ~ 1,25 TB (runda av till närmaste KVARTIL)

Factoring i oväntat tillväxt och nya återställningar, bör du etablerar en lokal disk av runt 1,25-1,5 TB.

> [!NOTE]
> Vi rekommenderar också att den lokala disken är tunt etablerad. Denna rekommendation beror området återställning behövs bara om du vill återställa data som är äldre än fem dagar. Objektnivååterställning kan du återställa data för de senaste fem dagarna utan extra utrymme för återställning.


#### <a name="example-2"></a>Exempel 2:
På din virtuella matrisen som du vill kunna

* etablera en 2 TB nivåer volym
* etablera en 300 GB lokalt Fäst volym

Baserat på 12% lokalt utrymme reservation för nivåindelade volymer-resurser och 10% för lokalt fästa volymer-resurser, behöver vi

* 240 GB lokala reservation (2 TB skikt i volym/dela)
* 330 GB för lokalt Fäst volym eller resurs (lägga till 10% av lokala reservation 300 GB allokerade utrymme)

Totalt utrymme som krävs på den lokala nivån är: 240 GB + 330 GB = 570 GB

Minsta lokalt utrymme som behövs för återställning är 330 GB.

15% av totalt disken används för att lagra ögonblicksbilderna så att endast 0.85 är tillgänglig. Diskens storlek är därför, (900&ast;(1/0.85)) = 1,06 TB ~ 1,25 TB (runda av till närmaste KVARTIL)

Du kan etablera en lokal disk 1,25-1,5 TB factoring i eventuella oväntade tillväxt.

### <a name="group-policy"></a>Grupprincip
Grupprincip är en infrastruktur som hjälper dig att implementera specifika konfigurationer för användare och datorer. Grupprincipinställningar finns i grupprincipobjekt (GPO) som är länkade till följande Active Directory Domain Services (AD DS)-behållare: platser, domäner eller organisationsenheter (OU). 

Om din virtuella matris är ansluten till domänen, tillämpas grupprincipobjekt på den. Dessa grupprincipobjekt kan installera program, till exempel ett antivirusprogram som kan påverka driften av den virtuella StorSimple-matrisen negativt.

Därför rekommenderar vi att du:

* Se till att virtuella matrisen i sin egen organisationsenhet (OU) för Active Directory.
* Se till att inga grupprincipobjekt (GPO) används för din virtuella matris. Du kan blockera arv för att säkerställa att den virtuella matrisen (underordnad nod) automatiskt inte ärver grupprincipobjekt från överordnat. Mer information finns på [blockera arv](https://technet.microsoft.com/library/cc731076.aspx).

### <a name="networking"></a>Nätverk
Nätverkskonfiguration för din virtuella matris görs via det lokala webbgränssnittet. Ett virtuellt nätverksgränssnitt aktiveras via hypervisor-programmet där virtuella matrisen har etablerats. Använd den [nätverksinställningar](storsimple-virtual-array-deploy3-fs-setup.md) att konfigurera virtuellt IP-gränssnitt, nätmask och gateway.  Du kan också konfigurera den primära och sekundära DNS-server och tidsinställningar proxyinställningar för din enhet. De flesta av nätverkskonfigurationen är en gång. Granska de [StorSimple nätverkskrav](storsimple-ova-system-requirements.md#networking-requirements) innan du distribuerar virtuella matrisen.

Vi rekommenderar att du följa dessa rekommendationer när du distribuerar virtuella matrisen:

* Kontrollera att nätverket där virtuella matrisen har distribuerats alltid har kapacitet att dedikera 5 Mbit/s Internet-bandbredd (eller mer).
  
  * Behovet av Internet-bandbredd varierar beroende på din arbetsbelastning egenskaper och mängden data ändras.
  * Den ändring av data som kan hanteras är proportionell mot Internet-bandbredd. Exempelvis när du tar en säkerhetskopia av kan en 5 Mbit/s bandbredd hantera cirka 18 GB data ändras i 8 timmar. Med fyra gånger så mycket bandbredd (20 Mbit/s), kan du hantera ändring av fyra gånger mer data (72 GB).
* Kontrollera anslutningen till Internet alltid är tillgängligt. Sporadiska eller otillförlitliga Internet-anslutningar till enheter som kan resultera i förlust av åtkomst till data i molnet och kan resultera i en konfiguration som inte stöds.
* Om du planerar att distribuera din enhet som en iSCSI-server:
  
  * Vi rekommenderar att du inaktiverar den **hämta IP-adress automatiskt** alternativet (DHCP).
  * Konfigurera statiska IP-adresser. Du måste konfigurera en primär och en sekundär DNS-server.
  * Om definiera flera nätverksgränssnitt för din virtuella matrisen bara det första nätverksgränssnittet (det här gränssnittet är som standard **Ethernet**) kan nå molnet. Du kan skapa flera virtuella nätverksgränssnitt på din virtuella matriskonfiguration (som en iSCSI-server) och ansluta dessa gränssnitt till olika undernät för att styra vilken typ av trafik.
* Att begränsa den moln bandbredden (används av virtuella matrisen), konfigurera begränsning på routern eller brandväggen. Om du definierar begränsning i din hypervisor-program, kommer den begränsning alla protokoll inklusive iSCSI och SMB i stället för bara molnet bandbredd.
* Se till att tidssynkronisering för hypervisorer är aktiverat. Om du använder Hyper-V väljer virtuella matrisen i Hyper-V Manager, gå till **inställningar &gt; Integration Services**, och kontrollera att den **tidssynkronisering** är markerad.

### <a name="storage-accounts"></a>Lagringskonton
StorSimple virtuell matris kan associeras med ett enda storage-konto. Det här lagringskontot kan vara ett automatiskt genererade storage-konto, ett konto med samma prenumeration som för tjänsten, eller ett lagringskonto som är relaterade till en annan prenumeration. Mer information finns i så här [hantera storage-konton för din virtuella matrisen](storsimple-virtual-array-manage-storage-accounts.md).

Använd följande rekommendationer för lagringskonton som är kopplade till virtuella matrisen.

* När du länkar flera virtuella matriser med ett enda lagringskonto hänsyn till den maximala kapaciteten (64 TB) för en virtuell matris och maximal storlek (500 TB) för ett lagringskonto. Detta begränsar antalet fullskaligt virtuella lagringsmatriser som kan associeras med detta lagringskonto till ungefär 7.
* När du skapar ett nytt lagringskonto
  
  * Vi rekommenderar att du skapar den i region närmast den office/filialkontor där din virtuella StorSimple-matris distribueras för att minimera svarstider.
  * Tänk dessutom på att du inte kan flytta ett lagringskonto över olika regioner. Du kan också flytta en tjänst för alla prenumerationer.
  * Använd ett lagringskonto som implementerar redundans mellan datacenter. GEO-Redundant lagring (GRS), zonen Redundant lagring (ZRS) och lokalt Redundant lagring (LRS) stöds för användning med virtuella matrisen. Mer information om de olika typerna av lagringskonton går du till [Azure storage-replikering](../storage/common/storage-redundancy.md).

### <a name="shares-and-volumes"></a>Filresurser och volymer
Du kan etablera resurser när den är konfigurerad som en filserver och volymer när konfigurerad som en iSCSI-server för din virtuella StorSimple-matris. Metodtips för att skapa filresurser och volymer är relaterade till storleken och den typen av konfigurerad.

#### <a name="volumeshare-size"></a>Volym/dela storlek
Du kan etablera resurser när den är konfigurerad som en filserver och volymer när konfigurerad som en iSCSI-server på din virtuella matrisen. Metodtips för att skapa filresurser och volymer relaterar till storleken och den typen av konfigurerad. 

Tänk på följande metodtips vid etablering av resurser eller volymer på din virtuella enhet.

* Filstorlekarna i förhållande till etablerade storleken på en skiktad resurs kan påverka lagringsnivåer prestanda. Arbeta med stora filer kan resultera i en långsam nivån. När du arbetar med stora filer, rekommenderar vi att den största filen är mindre än 3% av storleken på filresursen.
* Högst 16 volymer-resurser kan skapas på virtuella matrisen. Storleksgränser för volymer/resurser lokalt Fäst och nivåindelade alltid finns i den [virtuella StorSimple-matris begränsar](storsimple-ova-limits.md).
* När du skapar en volym, faktor för förbrukning av förväntade data samt framtida tillväxt. Volymen kan inte expanderas senare.
* När volymen har skapats, kan du minska storleken på volymen på StorSimple.
* Vid skrivning till en nivåindelad volym på StorSimple, när volymens data når ett visst tröskelvärde (i förhållande till den lokala utrymme som är reserverat för volymen), begränsas IO. Fortsätter att skriva till den här volymen långsammare IO avsevärt. Trots att du kan skriva till en nivåindelad volym utöver dess etablerad kapacitet (vi inte aktivt stoppar användaren från att skriva etablerad kapacitet) kan du se att du har övertecknat aviseringsmeddelanden. När du ser aviseringen, är det viktigt att du vidtar åtgärder, till exempel ta bort volymens data (volym expansion är för närvarande stöds inte).
* För disaster recovery användningsfall, eftersom antalet tillåtna resurser-volymer är 16 och det maximala antalet resurser/volymer som kan bearbetas parallellt även 16, har antalet resurser/volymer inte betydelse i din Återställningspunktmål och RTOs.

#### <a name="volumeshare-type"></a>Volym/dela-typ
StorSimple stöder två volym/dela-typer som baseras på användningen: lokalt Fäst och nivåer. Lokalt fästa volymer-resurser etableras tjockt, vilket medan nivåindelade volymer/resurser är tunt allokerad. Du kan inte konvertera en lokalt Fäst volym/dela till nivåindelade eller *tvärtom* skapas en gång.

Vi rekommenderar att du implementera följande metodtips när du konfigurerar StorSimple-volymer/resurser:

* Identifiera volymtyp baserat på de arbetsbelastningar som du tänker distribuera innan du skapar en volym. Använd lokalt fästa volymer för arbetsbelastningar som kräver lokala garantier data (även under ett avbrott i molnet) och som kräver molnet låg latens. När du skapar en volym på din virtuella matrisen du inte ändra volymtypen från lokalt Fäst till nivåindelade eller *vice versa*. Skapa lokalt fästa volymer som exempel, när du distribuerar SQL arbetsbelastningar eller arbetsbelastningar som är värd för virtuella datorer (VM) Använd nivåindelade volymer för filen resursen arbetsbelastningar.
* Markera alternativet för mindre vanliga arkiveringsdata när du hanterar mycket stora filer. En större segmentstorlek för deduplicering på 512 kB används när detta alternativ är aktiverat för att påskynda dataöverföring till molnet.

#### <a name="volume-format"></a>Volymen format
När du skapar StorSimple-volymer på iSCSI-servern måste du initiera, montera och formatera volymerna. Den här åtgärden utförs på den värden är ansluten till din StorSimple-enhet. Följande metodtips rekommenderas när montera och formatera volymer på StorSimple-värden.

* Utför en snabbformatering på alla StorSimple-volymer.
* Formaterar en StorSimple-volym och använda en storlek på allokeringsenhet (Australien) på 64 KB (standard är 4 KB). 64 KB Australien baseras på Testa görs inom företaget för vanliga arbetsbelastningar på StorSimple och andra arbetsbelastningar.
* När du använder den virtuella StorSimple-matrisen konfigurerad som en iSCSI-server kan använda inte volymer eller dynamiska diskar som dessa volymer eller diskar stöds inte av StorSimple.

#### <a name="share-access"></a>Åtkomst till resursen
När du skapar resurser på filservern virtuella matris Följ dessa riktlinjer:

* När du skapar en resurs kan du tilldela en användargrupp som administratör av en resurs i stället för en enskild användare.
* Du kan hantera NTFS-behörigheter när resursen har skapats genom att redigera resurser via Utforskaren.

#### <a name="volume-access"></a>Åtkomst till en volym
Det är viktigt att styra åtkomsten behov när du konfigurerar iSCSI-volymer på din virtuella StorSimple-matrisen. För att avgöra vilka värdservrar för fjärrskrivbordssessioner kan komma åt volymer, skapar och associerar access control-poster (ACRs) med StorSimple-volymer.

Använd följande metodtips när du konfigurerar ACRs för StorSimple-volymer:

* Koppla alltid minst en ACR till en volym.

* Se till att volymen inte visas på ett sätt där den kan samtidigt användas av fler än en icke-klustrad värd när du tilldelar en volym mer än en ACR. Om du har tilldelat en volym flera ACRs visas ett varningsmeddelande som du kan granska din konfiguration.

### <a name="data-security-and-encryption"></a>Datasäkerhet och kryptering
Din virtuella StorSimple-matris har data säkerhet och kryptering funktioner som garantera sekretess och integritet för dina data. När du använder dessa funktioner, bör du följa dessa rekommendationer: 

* Definiera en krypteringsnyckel för molnlagring att generera AES 256-kryptering innan data skickas från din virtuella matris till molnet. Den här nyckeln är inte nödvändigt om dina data krypteras börja med. Nyckeln kan skapas och förblir säkra med en nyckelhanteringssystem [Azure key vault](../key-vault/key-vault-whatis.md).
* När du konfigurerar lagringskonto via StorSimple Manager-tjänsten måste du kontrollera att du aktiverar SSL-läge för att skapa en säker kanal för nätverkskommunikation mellan din StorSimple-enhet och molnet.
* Återskapa nycklarna för storage-konton (genom att öppna Azure Storage-tjänst) med jämna mellanrum på grund av ändringar att få åtkomst till baserat på den ändrade listan över administratörer.
* Data på virtuella matrisen komprimeras och deduplicerade innan den skickas till Azure. Vi rekommenderar inte använda rolltjänsten Datadeduplicering på Windows Server-värd.

## <a name="operational-best-practices"></a>Metodtips för fortlöpande
Metodtips för fortlöpande finns riktlinjer som ska följas under den dagliga driften eller i virtuella matrisen. Praxis täcka specifika hanteringsuppgifter, till exempel säkerhetskopiering, återställning från en säkerhetskopia, utför en växling vid fel, inaktivera och ta bort matrisen övervakning systemanvändning och hälsa och kör virus-sökningar på din virtuella matris.

### <a name="backups"></a>Säkerhetskopior
Data på virtuella matrisen har säkerhetskopierats till moln på två sätt, standard automatiserad daglig säkerhetskopiering av hela enheten startar klockan 22.30 eller via en manuell säkerhetskopiering på begäran. Som standard skapar enheten automatiskt dagliga molnögonblicksbilder för alla data som finns på den. Mer information finns på [säkerhetskopiera din virtuella StorSimple-matris](storsimple-virtual-array-backup.md).

Frekvens och kvarhållning som är associerade med standard-säkerhetskopieringar kan inte ändras, men du kan konfigurera den tid då dagliga säkerhetskopior initieras varje dag. När du konfigurerar starttiden för automatisk säkerhetskopiering, rekommenderar vi att:

* Schemalägga säkerhetskopiorna för låg belastning. Starttid för säkerhetskopiering bör inte sammanfaller med flera i/o-värden.
* Starta en manuell säkerhetskopiering på begäran när du planerar att utföra en enhet med växling vid fel eller före underhållsperiod, att skydda data på virtuella matrisen.

### <a name="restore"></a>Återställ
Du kan återställa från en säkerhetskopia på två sätt: återställa till en annan volym eller resurs eller utföra en återställning (endast tillgängligt på en virtuell matris som konfigurerats som en filserver). Objektnivååterställning kan du göra en detaljerad återställning av filer och mappar från en säkerhetskopiering i molnet för alla resurser på StorSimple-enhet. Mer information finns på [återställa från en säkerhetskopia](storsimple-virtual-array-clone.md).

Tänk på följande riktlinjer när du utför en återställning:

* Din virtuella StorSimple-matrisen har inte stöd för återställning på plats. Detta kan dock vara lätt uppnås genom en tvåstegsprocess: Frigör diskutrymme på den virtuella matrisen och sedan återställa till en annan volym-resurs.
* Vid återställning från en lokal volym, Tänk på att återställningen långvarig åtgärd. Om volymen kan snabbt anslutits fortfarande data ur i bakgrunden.
* Volymtypen förblir detsamma vid återställningen. En nivåindelad volym återställs till en annan nivåindelad volym och en lokalt Fäst volym till en annan lokalt Fäst volym.
* När du försöker återställa en volym eller en resurs från en säkerhetskopia om återställningsjobbet misslyckas, ange en målvolym eller resursen kan fortfarande skapas i portalen. Det är viktigt att du tar bort den här oanvända målvolymen eller dela i portalen för att minimera eventuella framtida problem som härrör från det här elementet.

### <a name="failover-and-disaster-recovery"></a>Redundans och katastrofåterställning
En enhet redundans kan du migrera data från en *källa* enheten i datacentret till en annan *mål* enhet finns i samma eller en annan geografisk plats. Enhet för växling vid fel är för hela enheten. Under växling vid fel ändras molndata för källan ägarskap till som målenheten.

För din virtuella StorSimple-matris kan du bara växla över till en annan virtuell matrisen hanteras av samma StorSimple Manager-tjänsten. En växling till en serieenhet som 8000-eller en matris som hanteras av en annan StorSimple Manager-tjänst (än den för källan) är inte tillåtet. Mer information om överväganden för växling vid fel, gå till [krav för redundans enheten](storsimple-virtual-array-failover-dr.md).

När du utför en misslyckas över för din virtuella matrisen bör du tänka på följande:

* Det är rekommenderad praxis att vidta alla volymer/resurser offline innan du utför redundans för en planerad redundans. Följ instruktionerna operativsystemspecifika volymer/resurser offline på värden först och sedan vidta de offline på din virtuella enhet.
* För en fil server katastrofåterställning (DR) rekommenderar vi du Anslut målenheten till samma domän som källa så att behörigheter till resursen löses automatiskt. Endast redundans till en målenhet i samma domän som stöds i den här versionen.
* När ar har slutförts tas på källenheten bort automatiskt. Om enheten är inte längre tillgänglig, förbrukar den virtuella datorn som du har etablerats på värdsystemet fortfarande resurser. Vi rekommenderar att du tar bort den här virtuella datorn från din värdsystemet för att förhindra att belasta uppstår.
* Att Observera att även om växling vid fel misslyckas, **data alltid är säkert i molnet**. Tänk på följande tre scenarier där växling vid fel inte slutfördes korrekt:
  
  * Ett fel uppstod i inledande av växling vid fel, till exempel när de inledande kontrollerna i DR utförs. I det här fallet kan målenheten fortfarande användas. Du kan försöka redundans på samma målenhet.
  * Ett fel uppstod under faktiska failover-processen. I det här fallet markeras målenheten inte kan användas. Du måste etablera och konfigurera en annan virtuell Målmatrisen och Använd som för redundans.
  * Växling vid fel slutfördes och därefter på källenheten har tagits bort men målenheten har problem och du kan inte komma åt alla data. Data är fortfarande säkert i molnet och enkelt kan hämtas genom att skapa en annan virtuell matris och använda det som en målenhet för DR.

### <a name="deactivate"></a>Inaktivera
När du inaktiverar en virtuell StorSimple-matris sever anslutningen mellan enheten och motsvarande StorSimple Manager-tjänsten. Inaktiveringen en **permanent** åtgärden och kan inte ångras. En inaktiverad enhet kan inte registreras med StorSimple Manager-tjänsten igen. Mer information finns på [inaktivera och ta bort din virtuella StorSimple-matris](storsimple-virtual-array-deactivate-and-delete-device.md).

Kom ihåg följande metodtips när du inaktiverar virtuella matrisen:

* Ta en ögonblicksbild i molnet för alla data innan du inaktiverar en virtuell enhet. När du inaktiverar en virtuell matris alla lokala enhet-data går förlorade. Tar en ögonblicksbild i molnet kan du återställa data i ett senare skede.
* Se till att stoppa eller ta bort klienter och värdar som är beroende av enheten innan du inaktiverar en virtuell StorSimple-matris.
* Ta bort en inaktiverad enhet om du inte längre använder så att den inte påförs kostnader.

### <a name="monitoring"></a>Övervakning
Se till att din virtuella StorSimple-matris i kontinuerlig felfritt tillstånd, måste du övervaka matrisen och se till att du får information från systemet, inklusive aviseringar. Implementera följande säkerhetsmetoder för att övervaka den övergripande hälsan för den virtuella matrisen:

* Konfigurera övervakning för att spåra diskanvändning hårddisken virtuella matris data samt OS-disk. Du kan använda en kombination av System Center Virtual Machine Manager (SCVMM) och System Center Operations Manager (SCOM) för att övervaka din virtualiseringsvärdar om du kör Hyper-V.
* Konfigurera e-postaviseringar på din virtuella matrisen att skicka aviseringar på vissa nivåer för användning.                                                                                                                                                                                                

### <a name="index-search-and-virus-scan-applications"></a>Indexsökning och virus söka program
En virtuell StorSimple-matris kan automatiskt datanivå data från den lokala nivån till Microsoft Azure-molnet. När ett program, till exempel en indexsökning eller en viruskontroll används för att söka igenom de data som lagras på StorSimple, måste du ta hand att molninformationen inte komma åt och dras tillbaka till den lokala nivån.

Vi rekommenderar att du implementera följande metodtips när du konfigurerar indexsökning sökning eller virus på din virtuella matrisen:

* Inaktivera alla operationer som automatiskt konfigurerade fullständig genomsökning.
* Konfigurera index sökning eller virus genomsökning programmet att utföra en inkrementell skanning för nivåindelade volymer. Detta skulle skanna bara den nya data förmodligen som finns på den lokala nivån. De data som är nivåer till molnet inte kan kommas åt under en stegvis åtgärd.
* Se till att rätt sökfilter och inställningar som har konfigurerats så att de avsedda typerna av filer hämta genomsöks. Till exempel bildfiler (JPEG, GIF- och TIFF) och tekniska ritningar bör inte genomsökas under inkrementell eller fullständig index återskapas.

Om du använder Windows indexering process, Följ dessa riktlinjer:

* Använd inte Windows indexeraren för nivåindelade volymer som återkallar stora mängder data (TBs) från molnet om indexet måste återskapas ofta. Återskapa indexet skulle hämta alla filtyper att indexera deras innehåll.
* Använd Windows indexering process för lokalt fästa volymer eftersom detta skulle endast åtkomst till data på de lokala nivåerna för att skapa index (molninformationen inte kommer att komma åt).

### <a name="byte-range-locking"></a>Låsa byte-intervall
Program kan låsa ett angivet antal byte i filerna. Om låsning byte-intervallet är aktiverad på de program som skriver till din StorSimple, fungerar inte sedan skiktning på din virtuella matrisen. Alla delar av filer som öppnas bör vara öppet för skiktning för att fungera. Låsa byte-intervallet stöds inte med nivåindelade volymer på din virtuella matrisen.

Rekommenderade åtgärder för att minska låsa byte-intervallet är:

* Inaktivera låsning i applogiken byte-intervallet.
* Använd lokalt fästa volymer (i stället för med skikt) för data som är associerade med det här programmet. Inte på datanivå lokalt fästa volymer i molnet.
* När du använder lokalt Fäst volymer med byte intervallet låsning aktiverad, kan volymen anslutas innan återställningen är slutförd. I dessa fall måste du vänta tills återställningen fullständig.

## <a name="multiple-arrays"></a>Flera matriser
Flera virtuella matriser kan behöva distribueras på grund av en växande arbetsminnet för data som kan läcker över till molnet därmed påverka prestanda för enheten. I dessa fall är det bäst att skala enheter när arbetsminnet växer. Detta kräver en eller flera enheter som ska läggas till i den lokala datacentralen. När du lägger till enheterna kan du:

* Dela den aktuella mängden data.
* Distribuera nya arbetsbelastningar till nya enheter.
* Om du distribuerar flera virtuella matriser, rekommenderar vi att från belastningsutjämning perspektiv, distribuera matrisen mellan olika hypervisor-värdar.
* Flera virtuella matriser (om konfigurerad som en filserver eller en iSCSI-server) kan distribueras i en Distributed File System Namespace. Detaljerade anvisningar finns i [Distributed File System Namespace lösning med Hybrid Cloud Storage Deployment Guide](https://www.microsoft.com/download/details.aspx?id=45507). Distributed File System Replication rekommenderas för närvarande inte för användning med virtuella matrisen. 

## <a name="see-also"></a>Se också
Lär dig hur du [administrera din virtuella StorSimple-matris](storsimple-virtual-array-manager-service-administration.md) via StorSimple Manager-tjänsten.

