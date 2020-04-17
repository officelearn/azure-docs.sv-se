---
title: Metodtips för StorSimple Virtual Array | Microsoft-dokument
description: Beskriver de bästa metoderna för distribution och hantering av StorSimple Virtual Array.
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
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: bdf69a9ff7b3260b47042f296a47826e3c52387b
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460655"
---
# <a name="storsimple-virtual-array-best-practices"></a>Metodtips för StorSimple Virtual Array

## <a name="overview"></a>Översikt

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Microsoft Azure StorSimple Virtual Array är en integrerad lagringslösning som hanterar lagringsuppgifter mellan en lokal virtuell enhet som körs i en hypervisor- och Microsoft Azure-molnlagring. StorSimple Virtual Array är ett effektivt och kostnadseffektivt alternativ till den fysiska matrisen i 8000-serien. Den virtuella matrisen kan köras på din befintliga hypervisor-infrastruktur, stöder både iSCSI- och SMB-protokollen och är väl lämpad för scenarier för fjärrkontor/filialkontor. Mer information om StorSimple-lösningarna finns i [Översikt över Microsoft Azure StorSimple](https://www.microsoft.com/en-us/server-cloud/products/storsimple/overview.aspx).

Den här artikeln beskriver de metodtips som implementerades under den första installationen, distributionen och hanteringen av StorSimple Virtual Array. De här metodtipsen innehåller validerade riktlinjer för installation och hantering av den virtuella matrisen. Den här artikeln riktar sig till IT-administratörer som distribuerar och hanterar de virtuella matriserna i sina datacenter.

Vi rekommenderar en regelbunden genomgång av de bästa metoderna för att säkerställa att enheten fortfarande är i överensstämmelse när ändringar görs i installations- eller driftflödet. Om du stöter på några problem när du implementerar de här metodtipsen på den virtuella matrisen [kontaktar du Microsoft Support](storsimple-virtual-array-log-support-ticket.md) för att få hjälp.

## <a name="configuration-best-practices"></a>Metodtips för konfiguration
Dessa metodtips täcker de riktlinjer som måste följas under den första installationen och distributionen av de virtuella matriserna. Dessa metodtips omfattar de som är relaterade till etablering av den virtuella datorn, grupprincipinställningar, storlek, inställning av nätverk, konfigurera lagringskonton och skapa resurser och volymer för den virtuella matrisen. 

### <a name="provisioning"></a>Etablering
StorSimple Virtual Array är en virtuell dator (VM) som etablerats på hypervisor (Hyper-V eller VMware) på värdservern. När du etablerar den virtuella datorn ska du se till att värden kan avsätta tillräckliga resurser. Mer information finns i [lägsta resurskrav](storsimple-virtual-array-deploy2-provision-hyperv.md#step-1-ensure-that-the-host-system-meets-minimum-virtual-array-requirements) för att etablera en matris.

Implementera följande metodtips vid etablering av den virtuella matrisen:

|  | Hyper-V | VMware |
| --- | --- | --- |
| **Typ av virtuell dator** |**Generation 2** Virtuell dator för användning med Windows Server 2012 eller senare och en *VHDX-avbildning.* <br></br> **Generation 1** Virtuell dator för användning med en Windows Server 2008 eller senare och en *VHD-avbildning.* |Använd version 8 för virtuella datorer när du använder *VMDK-avbildning.* |
| **Minnestyp** |Konfigurera som **statiskt minne**. <br></br> Använd inte alternativet **dynamiskt minne.** | |
| **Datadisktyp** |Etablera som **dynamiskt expanderande**.<br></br> **Fast storlek** tar lång tid. <br></br> Använd inte **alternativet differencing.** |Använd alternativet **för tunn etablering.** |
| **Ändring av datadisk** |Expansion eller krympning är inte tillåtet. Ett försök att göra det resulterar i förlust av alla lokala data på enheten. |Expansion eller krympning är inte tillåtet. Ett försök att göra det resulterar i förlust av alla lokala data på enheten. |

### <a name="sizing"></a>Storlekar
När du ändrar storlek på den virtuella storsimple-matrisen bör du tänka på följande:

* Lokal reservation för volymer eller resurser. Cirka 12 % av utrymmet är reserverat på den lokala nivån för varje etablerad nivåindelad volym eller resurs. Ungefär 10 % av utrymmet är också reserverat för en lokalt fäst volym för filsystemet.
* Ögonblicksbild overhead. Ungefär 15 % utrymme på den lokala nivån är reserverat för ögonblicksbilder.
* Behov av återställningar. Om du gör återställning som en ny åtgärd bör storleksändringen ta hänsyn till det utrymme som behövs för återställning. Återställning görs till en resurs eller volym av samma storlek.
* En viss buffert bör fördelas för en oväntad tillväxt.

Baserat på föregående faktorer kan storlekskraven representeras av följande ekvation:

`Total usable local disk size = (Total provisioned locally pinned volume/share size including space for file system) + (Max (local reservation for a volume/share) for all tiered volumes/share) + (Local reservation for all tiered volumes/shares)`

`Data disk size = Total usable local disk size + Snapshot overhead + buffer for unexpected growth or new share or volume`

Följande exempel illustrerar hur du kan ändra storlek på en virtuell matris baserat på dina behov.

#### <a name="example-1"></a>Exempel 1:
På den virtuella matrisen vill du kunna

* etablera en 2 TB nivåindelad volym eller andel.
* etablera en 1 TB nivåindelad volym eller andel.
* etablera en 300 GB lokalt fäst volym eller resurs.

För föregående volymer eller resurser, låt oss beräkna utrymmeskraven på den lokala nivån.

För det första, för varje nivåindelad volym/aktie, skulle den lokala reservationen vara lika med 12 % av volymen/resursstorleken. För den lokalt fästa volymen/resursen är den lokala reservationen 10 % av den lokalt fästa volymen/resursstorleken (utöver den etablerade storleken). I det här exemplet behöver du

* 240 GB lokal reservation (för en 2 TB nivåindelad volym/resurs)
* 120 GB lokal reservation (för en 1 TB nivåindelad volym/resurs)
* 330 GB för lokalt fäst volym eller resurs (lägga till 10 % av den lokala reservationen till den etablerade storleken på 300 GB)

Det totala utrymmet som krävs på den lokala nivån hittills är: 240 GB + 120 GB + 330 GB = 690 GB.

För det andra behöver vi minst lika mycket utrymme på den lokala nivån som den största enskilda reservationen. Det här extra beloppet används om du behöver återställa från en ögonblicksbild i molnet. I det här exemplet är den största lokala reservationen 330 GB (inklusive reservation för filsystem), så du lägger till den i 690 GB: 690 GB + 330 GB = 1020 GB.
Om vi utförde efterföljande ytterligare återställningar kan vi alltid frigöra utrymme från den tidigare återställningen.

För det tredje behöver vi 15 % av ditt totala lokala utrymme hittills för att lagra lokala ögonblicksbilder, så att endast 85% av det är tillgängligt. I det här exemplet skulle det vara cirka 1020&ast;GB = 0,85 etablerade datadisk TB. Så skulle den etablerade datadisken vara (1020&ast;(1/0.85))= 1200 GB = 1,20 TB ~ 1,25 TB (avrundning till närmaste kvartilen)

Factoring i oväntad tillväxt och nya återställningar, bör du etablera en lokal disk på cirka 1,25 - 1,5 TB.

> [!NOTE]
> Vi rekommenderar också att den lokala disken är tunt etablerad. Den här rekommendationen beror på att återställningsutrymmet bara behövs när du vill återställa data som är äldre än fem dagar. Med återställning på objektnivå kan du återställa data under de senaste fem dagarna utan att behöva det extra utrymmet för återställning.


#### <a name="example-2"></a>Exempel 2:
På den virtuella matrisen vill du kunna

* etablera en 2 TB nivåindelad volym
* etablera en 300 GB lokalt fäst volym

Baserat på 12 % av den lokala utrymmesreservationen för nivåindelad volym/andelar och 10 % för lokalt fästa volymer/aktier behöver vi

* 240 GB lokal reservation (för 2 TB nivåindelningsvolym/resurs)
* 330 GB för lokalt fäst volym eller resurs (lägga till 10 % av den lokala reservationen i det 300 GB-etablerade utrymmet)

Totalt utrymme som krävs på den lokala nivån är: 240 GB + 330 GB = 570 GB

Det minsta lokala utrymme som behövs för återställning är 330 GB.

15 % av den totala disken används för att lagra ögonblicksbilder så att endast 0,85 är tillgängligt. Så är diskstorleken (900&ast;(1/0.85)) = 1,06 TB ~ 1,25 TB (avrundning till närmaste kvartilen)

Med en faktor i en oväntad tillväxt kan du etablera en lokal disk på 1,25–1,5 TB.

### <a name="group-policy"></a>Grupprincip
Grupprincip är en infrastruktur som gör att du kan implementera specifika konfigurationer för användare och datorer. Grupprincipinställningar finns i Grupprincipobjekt som är länkade till följande AD DS-behållare (Active Directory Domain Services): platser, domäner eller organisationsenheter. 

Om den virtuella matrisen är domänansluten kan grupprincipobjekt tillämpas på den. Dessa grupprincipobjekt kan installera program som ett antivirusprogram som kan påverka driften av StorSimple Virtual Array negativt.

Därför rekommenderar vi att du:

* Kontrollera att den virtuella matrisen finns i en egen organisationsenhet (OU) för Active Directory.
* Kontrollera att inga grupprincipobjekt (GRUPPRINCIPOBJEKT) tillämpas på den virtuella matrisen. Du kan blockera arv för att säkerställa att den virtuella matrisen (underordnad nod) inte automatiskt ärver några grupprincipobjekt från den överordnade. Mer information finns i [blockera arv](https://technet.microsoft.com/library/cc731076.aspx).

### <a name="networking"></a>Nätverk
Nätverkskonfigurationen för den virtuella matrisen sker via det lokala webbgränssnittet. Ett virtuellt nätverksgränssnitt aktiveras via hypervisorn där den virtuella matrisen etableras. Använd sidan [Nätverksinställningar](storsimple-virtual-array-deploy3-fs-setup.md) för att konfigurera IP-adressen för det virtuella nätverksgränssnittet, undernätet och gatewayen.  Du kan också konfigurera den primära och sekundära DNS-servern, tidsinställningarna och valfria proxyinställningar för enheten. De flesta av nätverkskonfigurationen är en engångskonfiguration. Granska [StorSimple-nätverkskraven](storsimple-ova-system-requirements.md#networking-requirements) innan du distribuerar den virtuella matrisen.

När du distribuerar den virtuella matrisen rekommenderar vi att du följer de här metodtipsen:

* Kontrollera att nätverket där den virtuella matrisen distribueras alltid har kapacitet att avsätta 5 Mbit/s Internet-bandbredd (eller mer).
  
  * Internet bandbredd behov varierar beroende på din arbetsbelastning egenskaper och graden av dataförändring.
  * Den dataändring som kan hanteras är direkt proportionell mot internetbandbredden. Som ett exempel när du tar en säkerhetskopia kan en bandbredd på 5 Mbit/s hantera en dataändring på cirka 18 GB på 8 timmar. Med fyra gånger mer bandbredd (20 Mbit/s) kan du hantera fyra gånger mer dataändring (72 GB).
* Se till att anslutningen till Internet alltid är tillgänglig. Sporadiska eller opålitliga Internetanslutningar till enheterna kan resultera i förlust av åtkomst till data i molnet och kan resultera i en konfiguration som inte stöds.
* Om du planerar att distribuera enheten som en iSCSI-server:
  
  * Vi rekommenderar att du inaktiverar alternativet **Hämta IP-adress automatiskt** (DHCP).
  * Konfigurera statiska IP-adresser. Du måste konfigurera en primär och en sekundär DNS-server.
  * Om du definierar flera nätverksgränssnitt på den virtuella matrisen kan endast det första nätverksgränssnittet (som standard det här gränssnittet **nå**molnet). Om du vill styra typen av trafik kan du skapa flera virtuella nätverksgränssnitt på den virtuella matrisen (konfigurerade som en iSCSI-server) och ansluta dessa gränssnitt till olika undernät.
* Om du bara vill begränsa molnbandbredden (som används av den virtuella matrisen) konfigurerar du begränsning på routern eller brandväggen. Om du definierar begränsning i hypervisorn begränsar det alla protokoll, inklusive iSCSI och SMB i stället för bara molnbandbredden.
* Kontrollera att tidssynkronisering för hypervisor är aktiverat. Om du använder Hyper-V väljer du den virtuella matrisen i Hyper-V-hanteraren, går till **Inställningar &gt; Integration Services**och kontrollerar att **tidssynkroniseringen** är markerad.

### <a name="storage-accounts"></a>Lagringskonton
StorSimple Virtual Array kan associeras med ett enda lagringskonto. Det här lagringskontot kan vara ett automatiskt genererat lagringskonto, ett konto i samma prenumeration som tjänsten eller ett lagringskonto som är relaterat till en annan prenumeration. Mer information finns i hur du [hanterar lagringskonton för den virtuella matrisen](storsimple-virtual-array-manage-storage-accounts.md).

Använd följande rekommendationer för lagringskonton som är associerade med den virtuella matrisen.

* När du länkar flera virtuella matriser med ett enda lagringskonto, faktor i den maximala kapaciteten (64 TB) för en virtuell matris och den maximala storleken (500 TB) för ett lagringskonto. Detta begränsar antalet virtuella matriser i full storlek som kan associeras med det lagringskontot till cirka 7.
* När du skapar ett nytt lagringskonto
  
  * Vi rekommenderar att du skapar den i den region som ligger närmast fjärrkontoret/filialkontoret där den virtuella storsimple-matrisen distribueras för att minimera fördröjningar.
  * Tänk på att du inte kan flytta ett lagringskonto mellan olika regioner. Du kan inte heller flytta en tjänst mellan prenumerationer.
  * Använd ett lagringskonto som implementerar redundans mellan datacenter. Geo-Redundant Storage (GRS), Zone Redundant Storage (ZRS) och Lokalt redundant lagring (LRS) stöds alla för användning med den virtuella matrisen. Mer information om de olika typerna av lagringskonton finns i [Azure storage replication](../storage/common/storage-redundancy.md).

### <a name="shares-and-volumes"></a>Aktier och volymer
För den virtuella storsimple-matrisen kan du etablera resurser när den konfigureras som en filserver och volymer när den konfigureras som en iSCSI-server. De bästa metoderna för att skapa resurser och volymer är relaterade till storleken och typen som konfigurerats.

#### <a name="volumeshare-size"></a>Volym/Delningsstorlek
På den virtuella matrisen kan du etablera resurser när den konfigureras som en filserver och volymer när den konfigureras som en iSCSI-server. De bästa metoderna för att skapa resurser och volymer avser storleken och typen som konfigurerats. 

Tänk på följande metodtips när du etablerar resurser eller volymer på den virtuella enheten.

* Filstorlekarna i förhållande till den etablerade storleken på en nivåindelad resurs kan påverka nivådelningsprestandan. Om du arbetar med stora filer kan det leda till en långsam nivå ut. När du arbetar med stora filer rekommenderar vi att den största filen är mindre än 3 % av resursstorleken.
* Högst 16 volymer/resurser kan skapas på den virtuella matrisen. För storleksgränserna för lokalt fästa och nivåindelade volymer/resurser, se alltid [begränsningarna för StorSimple Virtual Array](storsimple-ova-limits.md).
* När du skapar en volym, faktor i den förväntade dataförbrukningen samt framtida tillväxt. Volymen eller resursen kan inte expanderas senare.
* När volymen/resursen har skapats kan du inte krympa storleken på volymen/resursen på StorSimple.
* När du skriver till en nivåindelad volym på StorSimple, när volymdata når ett visst tröskelvärde (i förhållande till det lokala utrymme som reserverats för volymen), begränsas IO. Fortsätter att skriva till denna volym saktar ner IO betydligt. Även om du kan skriva till en nivåindelad volym utöver dess etablerade kapacitet (vi hindrar inte aktivt användaren från att skriva utöver den etablerade kapaciteten), ser du ett varningsmeddelande om att du har övertecknat. När du ser aviseringen är det absolut nödvändigt att du vidtar åtgärder som att ta bort volymdata (volymexpansion stöds för närvarande inte).
* När antalet tillåtna resurser/volymer är 16 och det maximala antalet aktier/volymer som kan bearbetas parallellt är också 16, har antalet aktier/volymer inte någon betydelse för dina RPO- och RTOs.

#### <a name="volumeshare-type"></a>Typ av volym/delning
StorSimple stöder två volym-/resurstyper baserat på användningen: lokalt fäst och nivåinställd. Lokalt fästa volymer/resurser etableras tjockt medan de nivåindelade volymerna/resurserna är tunt etablerade. Du kan inte konvertera en lokalt fäst volym/resurs till nivåindelad eller *vice versa* när den har skapats.

Vi rekommenderar att du implementerar följande metodtips när du konfigurerar StorSimple-volymer/-resurser:

* Identifiera volymtypen baserat på de arbetsbelastningar som du tänker distribuera innan du skapar en volym. Använd lokalt fästa volymer för arbetsbelastningar som kräver lokala garantier för data (även under ett moln avbrott) och som kräver låga moln svarstider. När du har skapat en volym på den virtuella matrisen kan du inte ändra volymtypen från lokalt fäst till nivåindelad eller *vice versa*. Skapa till exempel lokalt fästa volymer när du distribuerar SQL-arbetsbelastningar eller arbetsbelastningar som är värdar för virtuella datorer. använda nivåindelada volymer för filresursarbetsbelastningar.


#### <a name="volume-format"></a>Volymformat
När du har skapat StorSimple-volymer på iSCSI-servern måste du initiera, montera och formatera volymerna. Den här åtgärden utförs på värden som är ansluten till StorSimple-enheten. Följande metodtips rekommenderas vid montering och formatering av volymer på StorSimple-värden.

* Utför ett snabbformat på alla StorSimple-volymer.
* När du formaterar en StorSimple-volym använder du en allokeringsenhetsstorlek (AUS) på 64 KB (standard är 4 KB). 64 KB AUS baseras på tester som görs internt för vanliga StorSimple-arbetsbelastningar och andra arbetsbelastningar.
* När du använder Den virtuella storsimple-matrisen som konfigurerats som en iSCSI-server ska du inte använda spännvidd volymer eller dynamiska diskar eftersom dessa volymer eller diskar inte stöds av StorSimple.

#### <a name="share-access"></a>Dela åtkomst
När du skapar resurser på den virtuella matrisfilservern följer du dessa riktlinjer:

* När du skapar en resurs tilldelar du en användargrupp som resursadministratör i stället för en enskild användare.
* Du kan hantera NTFS-behörigheterna när resursen har skapats genom att redigera resurserna via Utforskaren.

#### <a name="volume-access"></a>Tillgång till volym
När du konfigurerar iSCSI-volymerna på den virtuella storsimple-matrisen är det viktigt att du kontrollerar åtkomsten där det behövs. För att avgöra vilka värdservrar som kan komma åt volymer, skapa och associera åtkomstkontrollposter (ACRs) med StorSimple-volymer.

Använd följande metodtips när du konfigurerar AKOL för StorSimple-volymer:

* Associera alltid minst en ACR med en volym.

* När du tilldelar mer än en ACR till en volym, se till att volymen inte exponeras på ett sätt där den kan nås samtidigt av mer än en icke-klustrade värd. Om du har tilldelat flera åtkomstkontrollistor till en volym visas ett varningsmeddelande där du kan granska konfigurationen.

### <a name="data-security-and-encryption"></a>Datasäkerhet och kryptering
Din StorSimple Virtual Array har datasäkerhets- och krypteringsfunktioner som säkerställer dina datas konfidentialitet och integritet. När du använder dessa funktioner rekommenderar vi att du följer dessa metodtips: 

* Definiera en molnlagringskrypteringsnyckel för att generera AES-256-kryptering innan data skickas från din virtuella matris till molnet. Den här nyckeln krävs inte om dina data är krypterade till att börja med. Nyckeln kan genereras och hållas säker med hjälp av ett nyckelhanteringssystem som [Azure key vault](../key-vault/general/overview.md).
* När du konfigurerar lagringskontot via StorSimple Manager-tjänsten ska du se till att du aktiverar TLS-läget för att skapa en säker kanal för nätverkskommunikation mellan StorSimple-enheten och molnet.
* Återskapa nycklarna för dina lagringskonton (genom att använda Azure Storage-tjänsten) regelbundet för att ta hänsyn till eventuella ändringar för åtkomst baserat på den ändrade listan över administratörer.
* Data på den virtuella matrisen komprimeras och dedupliceras innan de skickas till Azure. Vi rekommenderar inte att du använder rolltjänsten Datadeduplicering på din Windows Server-värd.

## <a name="operational-best-practices"></a>Metodtips för drift
De bästa metoderna för drift är riktlinjer som bör följas under den dagliga hanteringen eller driften av den virtuella matrisen. Dessa metoder omfattar specifika hanteringsuppgifter som att ta säkerhetskopior, återställa från en säkerhetskopia, utföra en redundans, inaktivera och ta bort matrisen, övervaka systemanvändning och hälsa och köra virussökningar på din virtuella matris.

### <a name="backups"></a>Säkerhetskopior
Data på den virtuella matrisen säkerhetskopieras till molnet på två sätt, en automatisk standardsäkerhetskopiering av hela enheten som börjar klockan 22:30 eller via en manuell säkerhetskopiering på begäran. Som standard skapar enheten automatiskt dagliga ögonblicksbilder av molnet av alla data som finns på den. Mer information finns i [säkerhetskopiera den virtuella storsimple-matrisen](storsimple-virtual-array-backup.md).

Frekvensen och kvarhållningen som är associerade med standardsäkerhetskopiorna kan inte ändras, men du kan konfigurera den tid då de dagliga säkerhetskopieringarna initieras varje dag. När du konfigurerar starttiden för de automatiska säkerhetskopieringarna rekommenderar vi att:

* Schemalägg dina säkerhetskopior för lågtrafik. Starttiden för säkerhetskopiering bör inte sammanfalla med många värd-IO.
* Starta en manuell säkerhetskopiering på begäran när du planerar att utföra en enhets redundans eller före underhållsfönstret, för att skydda data på den virtuella matrisen.

### <a name="restore"></a>Återställ
Du kan återställa från en säkerhetskopia på två sätt: återställa till en annan volym eller dela eller utföra en återställning på objektnivå (endast tillgängligt på en virtuell matris som konfigurerats som en filserver). Med återställning på objektnivå kan du göra en detaljerad återställning av filer och mappar från en molnsäkerhetskopiering av alla resurser på StorSimple-enheten. Mer information finns i [återställa från en säkerhetskopia](storsimple-virtual-array-clone.md).

När du utför en återställning bör du tänka på följande:

* Din StorSimple Virtual Array stöder inte återställning på plats. Detta kan dock lätt uppnås genom en tvåstegsprocess: skapa utrymme på den virtuella matrisen och sedan återställa till en annan volym/resurs.
* När du återställer från en lokal volym, tänk på att återställningen kommer att vara en tidskrävande åtgärd. Även om volymen snabbt kan komma online, fortsätter data att vara hydrerad i bakgrunden.
* Volymtypen förblir densamma under återställningsprocessen. En nivåindelad volym återställs till en annan nivåindelad volym och en lokalt fäst volym till en annan lokalt fäst volym.
* När du försöker återställa en volym eller en resurs från en säkerhetskopia kan en målvolym eller resurs fortfarande skapas i portalen om återställningsjobbet misslyckas. Det är viktigt att du tar bort den oanvända målvolymen eller resursen i portalen för att minimera eventuella framtida problem som uppstår från det här elementet.

### <a name="failover-and-disaster-recovery"></a>Redundans och haveriberedskap
Med en enhetsväxling kan du migrera data från en *källenhet* i datacentret till en annan *målenhet* som finns på samma eller en annan geografisk plats. Enheten redundans är för hela enheten. Under redundans ändrar molndata för källenheten ägarskapet till målenhetens.

För den virtuella storsimple-matrisen kan du bara växla över till en annan virtuell matris som hanteras av samma StorSimple Manager-tjänst. En redundans till en enhet i 8000-serien eller en matris som hanteras av en annan StorSimple Manager-tjänst (än den för källenheten) är inte tillåten. Mer information om redundansöverväganden finns [i förutsättningarna för enhetens redundans](storsimple-virtual-array-failover-dr.md).

När du utför en återställning för din virtuella matris bör du tänka på följande:

* För en planerad redundans är det en rekommenderad metod för att koppla från alla volymer/resurser innan redundansen initieras. Följ de operativsystemspecifika instruktionerna för att ta volymerna/resurserna offline på värden först och sedan koppla från dem på den virtuella enheten.
* För en filserverkatastrofåterställning (DR) rekommenderar vi att du ansluter målenheten till samma domän som källan så att resursbehörigheterna löses automatiskt. Endast redundansen till en målenhet i samma domän stöds i den här versionen.
* När DR har slutförts tas källenheten bort automatiskt. Även om enheten inte längre är tillgänglig förbrukar den virtuella datorn som du etablerade på värdsystemet fortfarande resurser. Vi rekommenderar att du tar bort den här virtuella datorn från värdsystemet för att förhindra att eventuella avgifter tillkommer.
* Observera att även om redundansen misslyckas **är data alltid säkra i molnet**. Tänk på följande tre scenarier där redundansen inte slutfördes:
  
  * Ett fel uppstod i de inledande stadierna av redundansen, till exempel när DR-förkontrollerna utförs. I det här fallet är din målenhet fortfarande användbar. Du kan försöka med redundansen på samma målenhet igen.
  * Ett fel uppstod under den faktiska redundansprocessen. I det här fallet markeras målenheten oanvändbar. Du måste etablera och konfigurera en annan virtuell målmatris och använda den för redundans.
  * Redundansen slutfördes efter vilken källenheten togs bort men målenheten har problem och du kan inte komma åt några data. Data är fortfarande säkra i molnet och kan enkelt hämtas genom att skapa en annan virtuell matris och sedan använda den som en målenhet för DR.

### <a name="deactivate"></a>Inaktivera
När du inaktiverar en StorSimple Virtual Array avbryter du anslutningen mellan enheten och motsvarande StorSimple Manager-tjänst. Inaktivering är en **permanent** åtgärd och kan inte ångras. Det går inte att registrera en inaktiverad enhet med Tjänsten StorSimple Manager igen. Mer information finns i inaktivera [och ta bort den virtuella storsimple-matrisen](storsimple-virtual-array-deactivate-and-delete-device.md).

Tänk på följande när du inaktiverar den virtuella matrisen:

* Ta en ögonblicksbild av alla data innan du inaktiverar en virtuell enhet. När du inaktiverar en virtuell matris går alla lokala enhetsdata förlorade. Om du tar en ögonblicksbild i molnet kan du återställa data i ett senare skede.
* Innan du inaktiverar en StorSimple Virtual Array ska du se till att stoppa eller ta bort klienter och värdar som är beroende av den enheten.
* Ta bort en inaktiverad enhet om du inte längre använder den så att den inte samlar på sig avgifter.

### <a name="monitoring"></a>Övervakning
För att säkerställa att din Virtuella StorSimple-matris är i ett kontinuerligt felfritt tillstånd måste du övervaka matrisen och se till att du får information från systemet, inklusive aviseringar. Så här övervakar du den virtuella matrisens allmänna hälsotillstånd:

* Konfigurera övervakning för att spåra diskanvändningen på den virtuella matrisdatadisken och OS-disken. Om du kör Hyper-V kan du använda en kombination av System Center Virtual Machine Manager (SCVMM) och System Center Operations Manager för att övervaka virtualiseringsvärdarna.
* Konfigurera e-postmeddelanden på den virtuella matrisen för att skicka aviseringar på vissa användningsnivåer.                                                                                                                                                                                                

### <a name="index-search-and-virus-scan-applications"></a>Indexsöknings- och virussökningsprogram
En StorSimple Virtual Array kan automatiskt nivådata från den lokala nivån till Microsoft Azure-molnet. När ett program som en indexsökning eller en virussökning används för att skanna data som lagras på StorSimple måste du se till att molndata inte får åtkomst och dras tillbaka till den lokala nivån.

Vi rekommenderar att du implementerar följande metodtips när du konfigurerar indexsökningen eller virussökningen på den virtuella matrisen:

* Inaktivera alla automatiskt konfigurerade fullständiga genomsökningsåtgärder.
* För nivåindelade volymer konfigurerar du indexsöknings- eller virussökningsprogrammet för att utföra en inkrementell genomsökning. Detta skulle söka igenom endast de nya data som sannolikt finns på den lokala nivån. Data som är nivåindelade till molnet nås inte under en inkrementell åtgärd.
* Kontrollera att rätt sökfilter och inställningar är konfigurerade så att endast de avsedda typerna av filer genomsöks. Bildfiler (JPEG, GIF och TIFF) och tekniska ritningar bör till exempel inte skannas under den inkrementella eller fullständiga indexombyggnaden.

Om du använder Windows-indexeringsprocessen följer du dessa riktlinjer:

* Använd inte Windows Indexer för nivåindelade volymer eftersom den återkallar stora mängder data (TBs) från molnet om indexet behöver återskapas ofta. Om du återskapar indexet skulle alla filtyper hämtas för att indexera innehållet.
* Använd Windows-indexeringsprocessen för lokalt fästa volymer eftersom detta bara skulle komma åt data på de lokala nivåerna för att skapa indexet (molndata kommer inte att nås).

### <a name="byte-range-locking"></a>Låsning av byteintervall
Program kan låsa ett angivet byteintervall i filerna. Om byteintervalllåsning är aktiverat på de program som skriver till Din StorSimple, fungerar nivåindelning inte på den virtuella matrisen. För att nivåindelningen ska fungera bör alla områden i de filer som används låsas upp. Byte intervall låsning stöds inte med nivåindelad volymer på din virtuella matris.

Rekommenderade åtgärder för att lindra byte intervall låsning inkluderar:

* Inaktivera låsning av byteintervall i programlogiken.
* Använd lokalt fästa volymer (i stället för nivåindelade) för data som är associerade med det här programmet. Lokalt fästa volymer inte nivå i molnet.
* När du använder lokalt fästa volymer med byteintervalllåsning aktiverad kan volymen anslutas innan återställningen är klar. I dessa fall måste du vänta tills återställningen är klar.

## <a name="multiple-arrays"></a>Flera matriser
Flera virtuella matriser kan behöva distribueras för att ta hänsyn till en växande arbetsuppsättning data som kan spillas ut på molnet vilket påverkar enhetens prestanda. I dessa fall är det bäst att skala enheter när arbetsuppsättningen växer. Detta kräver att en eller flera enheter läggs till i det lokala datacentret. När du lägger till enheterna kan du:

* Dela den aktuella uppsättningen data.
* Distribuera nya arbetsbelastningar till nya enheter.
* Om du distribuerar flera virtuella matriser rekommenderar vi att distribuera matrisen över olika hypervisorvärdar från belastningsutjämningsperspektiv.
* Flera virtuella matriser (när de konfigureras som en filserver eller en iSCSI-server) kan distribueras i ett namnområde för distribuerat filsystem. Detaljerade steg finns i Lösningen för [distribuerat filsystem med distributionsguide för hybridmolnlagring](https://www.microsoft.com/download/details.aspx?id=45507). Distributed File System Replication rekommenderas för närvarande inte för användning med den virtuella matrisen. 

## <a name="see-also"></a>Se även
Läs om hur du [administrerar din Virtuella StorSimple-matris](storsimple-virtual-array-manager-service-administration.md) via StorSimple Manager-tjänsten.

