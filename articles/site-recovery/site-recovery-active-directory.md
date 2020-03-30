---
title: Konfigurera Active Directory/DNS-haveriberedskap med Azure Site Recovery
description: I den här artikeln beskrivs hur du implementerar en lösning för haveriberedskap för Active Directory och DNS med Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/9/2019
ms.author: mayg
ms.openlocfilehash: 8c1f85217db12b60cdcd8ea0bdb65792b8d02648
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257815"
---
# <a name="set-up-disaster-recovery-for-active-directory-and-dns"></a>Konfigurera haveriberedskap för Active Directory och DNS

Företagsprogram som SharePoint, Dynamics AX och SAP är beroende av Active Directory och en DNS-infrastruktur för att fungera korrekt. När du konfigurerar haveriberedskap för program måste du ofta återställa Active Directory och DNS innan du återställer andra programkomponenter för att säkerställa korrekt programfunktionalitet.

Du kan använda [Site Recovery](site-recovery-overview.md) för att skapa en haveriberedskapsplan för Active Directory. När ett avbrott inträffar kan du initiera en redundansväxling. Active Directory kan vara igång på några minuter. Om du har distribuerat Active Directory för flera program på din primära plats, till exempel för SharePoint och SAP, kanske du vill växla över hela webbplatsen. Du kan först växla över Active Directory med hjälp av Site Recovery. Växla sedan över de andra programmen med hjälp av programspecifika återställningsplaner.

I den här artikeln beskrivs hur du skapar en lösning för haveriberedskap för Active Directory. Den innehåller förutsättningar och redundansinstruktioner. Du bör känna till Active Directory och Site Recovery innan du börjar.

## <a name="prerequisites"></a>Krav

* Om du replikerar till Azure [förbereder du Azure-resurser](tutorial-prepare-azure.md), inklusive en prenumeration, ett Virtuellt Azure-nätverk, ett lagringskonto och ett Recovery Services-valv.
* Granska [kraven för stöd](site-recovery-support-matrix-to-azure.md) för alla komponenter.

## <a name="replicate-the-domain-controller"></a>Replikera domänkontrollanten

- Du måste ställa in Site Recovery replication, på minst en virtuell dator som är värd för en domänkontrollant eller DNS.
- Om du har flera domänkontrollanter i din miljö måste du också konfigurera ytterligare en domänkontrollant på målplatsen. Den ytterligare domänkontrollanten kan finnas i Azure eller i ett sekundärt lokalt datacenter.
- Om du bara har ett fåtal program och en domänkontrollant kanske du vill växla över hela webbplatsen tillsammans. I det här fallet rekommenderar vi att du använder Site Recovery för att replikera domänkontrollanten till målplatsen (antingen i Azure eller i ett sekundärt lokalt datacenter). Du kan använda samma replikerade domänkontrollant eller virtuella DNS-dator för [test redundans](#test-failover-considerations).
- - Om du har många program och fler än en domänkontrollant i din miljö, eller om du planerar att växla över några program åt gången, förutom att replikera domänkontrollantens virtuella dator med Site Recovery, rekommenderar vi att du konfigurerar en ytterligare domänkontrollant på målplatsen (antingen i Azure eller i ett sekundärt lokalt datacenter). För [test redundans](#test-failover-considerations)kan du använda domänkontrollant som replikeras av Site Recovery. För redundans kan du använda ytterligare domänkontrollant på målplatsen.

## <a name="enable-protection-with-site-recovery"></a>Aktivera skydd med platsåterställning

Du kan använda Site Recovery för att skydda den virtuella datorn som är värd för domänkontrollanten eller DNS.

### <a name="protect-the-vm"></a>Skydda den virtuella datorn
Domänkontrollanten som replikeras med hjälp av Site Recovery används för [test redundans](#test-failover-considerations). Se till att den uppfyller följande krav:

1. Domänkontrollanten är en global katalogserver.
2. Domänkontrollanten bör vara FSMO-rollägare för roller som behövs under en testväxling. Annars måste dessa roller [beslagtas](https://aka.ms/ad_seize_fsmo) efter redundansen.

### <a name="configure-vm-network-settings"></a>Konfigurera vm-nätverksinställningar
Konfigurera nätverksinställningar under **beräknings- och nätverksinställningarna** för den replikerade virtuella datorn i Site Recovery för den virtuella datorn. Detta säkerställer att den virtuella datorn är ansluten till rätt nätverk efter redundans.

## <a name="protect-active-directory"></a>Skydda Active Directory

### <a name="site-to-site-protection"></a>Skydd från plats till plats
Skapa en domänkontrollant på den sekundära platsen. När du befordrar servern till en domänkontrollantroll anger du namnet på samma domän som används på den primära platsen. Du kan använda snapin-modulen **Platser och tjänster** i Active Directory för att konfigurera inställningar på platslänkobjektet som platserna läggs till i. Genom att konfigurera inställningar på en platslänk kan du styra när replikering sker mellan två eller flera platser och hur ofta den inträffar. Mer information finns i [Schemalägga replikering mellan platser](https://technet.microsoft.com/library/cc731862.aspx).

### <a name="site-to-azure-protection"></a>Skydd från plats till Azure
Skapa först en domänkontrollant i ett virtuellt Azure-nätverk. När du befordrar servern till en domänkontrollantroll anger du samma domännamn som används på den primära platsen.

Konfigurera sedan om DNS-servern för det virtuella nätverket så att DNS-servern i Azure används.

![Azure-nätverk](./media/site-recovery-active-directory/azure-network.png)

### <a name="azure-to-azure-protection"></a>Azure-till-Azure-skydd
Skapa först en domänkontrollant i ett virtuellt Azure-nätverk. När du befordrar servern till en domänkontrollantroll anger du samma domännamn som används på den primära platsen.

Konfigurera sedan om DNS-servern för det virtuella nätverket så att DNS-servern i Azure används.

## <a name="test-failover-considerations"></a>Testa redundansöverväganden
För att undvika påverkan på produktionsarbetsbelastningar sker test redundans i ett nätverk som är isolerat från produktionsnätverket.

De flesta program kräver att det finns en domänkontrollant eller en DNS-server. Innan programmet växlar över måste du därför skapa en domänkontrollant i det isolerade nätverket som ska användas för test redundans. Det enklaste sättet att göra detta är att använda Site Recovery för att replikera en virtuell dator som är värd för en domänkontrollant eller DNS. Kör sedan en test redundans för den virtuella datorn för domänkontrollanten innan du kör en testundanställning av återställningsplanen för programmet. Så här gör du:

1. Använd Site Recovery för att [replikera](vmware-azure-tutorial.md) den virtuella datorn som är värd för domänkontrollanten eller DNS.
2. Skapa ett isolerat nätverk. Alla virtuella nätverk som du skapar i Azure isoleras från andra nätverk som standard. Vi rekommenderar att du använder samma IP-adressintervall för det här nätverket som du använder i produktionsnätverket. Aktivera inte anslutning mellan plats och plats i det här nätverket.
3. Ange en DNS-IP-adress i det isolerade nätverket. Använd den IP-adress som du förväntar dig att den virtuella DNS-datorn ska hämtas. Om du replikerar till Azure anger du IP-adressen för den virtuella datorn som används vid redundans. Om du vill ange IP-adressen väljer du inställningarna för **mål-IP** i den replikerade virtuella datorn i inställningarna **för beräkning och nätverk.**

    ![Azure-testnätverk](./media/site-recovery-active-directory/azure-test-network.png)

    > [!TIP]
    > Site Recovery försöker skapa test virtuella datorer i ett undernät med samma namn och med samma IP-adress som finns i **beräknings- och nätverksinställningarna** för den virtuella datorn. Om ett undernät med samma namn inte är tillgängligt i det virtuella Azure-nätverk som tillhandahålls för test redundans, skapas den virtuella testdatorn i det alfabetiskt första undernätet.
    >
    > Om mål-IP-adressen är en del av det valda undernätet försöker Site Recovery skapa den virtuella testundanhandboken med hjälp av mål-IP-adressen. Om mål-IP inte är en del av det valda undernätet skapas den virtuella testundandatorn med hjälp av nästa tillgängliga IP i det valda undernätet.
    >
    >

### <a name="test-failover-to-a-secondary-site"></a>Testa redundans till en sekundär plats

1. Om du replikerar till en annan lokal plats och använder DHCP [konfigurerar du DNS och DHCP för testmissundanläggning](hyper-v-vmm-test-failover.md#prepare-dhcp).
2. Gör en testväxling av den virtuella datorn för domänkontrollanten som körs i det isolerade nätverket. Använd den senaste tillgängliga *programkonsekventa* återställningspunkten för den virtuella datorn för domänkontrollanten för att utföra test redundansen.
3. Kör en testväxling för återställningsplanen som innehåller virtuella datorer som programmet körs på.
4. När testningen är klar *rensar du testundansen* på den virtuella datorn för domänkontrollanten. Det här steget tar bort domänkontrollanten som skapades för test redundans.


### <a name="remove-references-to-other-domain-controllers"></a>Ta bort referenser till andra domänkontrollanter
När du startar en testväxling ska du inte inkludera alla domänkontrollanter i testnätverket. Om du vill ta bort referenser till andra domänkontrollanter som finns i produktionsmiljön kan du behöva [ta tag i FSMO Active Directory-roller](https://aka.ms/ad_seize_fsmo) och göra [metadatarensning](https://technet.microsoft.com/library/cc816907.aspx) för saknade domänkontrollanter.


### <a name="issues-caused-by-virtualization-safeguards"></a>Problem som orsakas av virtualiseringsgarantier

> [!IMPORTANT]
> Vissa av de konfigurationer som beskrivs i det här avsnittet är inte standardkonfigurationer eller standardkonfigurationer för domänkontrollanter. Om du inte vill göra dessa ändringar i en produktionsdomänkontrollant kan du skapa en domänkontrollant som är dedikerad för platsåterställning som ska användas för testundanläggning. Gör dessa ändringar endast i domänkontrollanten.  
>
>

Från och med Windows Server 2012 [är ytterligare skyddsåtgärder inbyggda i AD DS (Active Directory Domain Services).](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100) Dessa skyddsåtgärder hjälper till att skydda virtualiserade domänkontrollanter mot USN-återställningar om den underliggande hypervisorplattformen stöder **VM-GenerationID**. Azure stöder **VM-GenerationID**. På grund av detta har domänkontrollanter som kör Windows Server 2012 eller senare på virtuella Azure-datorer dessa ytterligare skyddsåtgärder.


När **VM-GenerationID** återställs återställs också AD DS-databasens **invocationID-värde.** Dessutom ignoreras RID-poolen och sysvol-mappen markeras som icke-auktoritär. Mer information finns i [Introduktion till Active Directory Domain Services virtualisering](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100) och säkert [virtuellt DFSR](https://blogs.technet.microsoft.com/filecab/2013/04/05/safely-virtualizing-dfsr/).

Om du inte går över till Azure kan **VM-GenerationID** återställas. Om du återställer **VM-GenerationID** utlöses ytterligare skyddsåtgärder när den virtuella domänkontrollanten startar i Azure. Detta kan leda till en *betydande fördröjning* i att kunna logga in på domänkontrollantens virtuella dator.

Eftersom den här domänkontrollanten endast används i en testväxling är virtualiseringsgarantier inte nödvändiga. Om du vill vara medveten om att **VM-GenerationID-värdet** för den virtuella datorn för domänkontrollanten inte ändras kan du ändra värdet för följande DWORD till **4** i den lokala domänkontrollanten:


`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\gencounter\Start`


#### <a name="symptoms-of-virtualization-safeguards"></a>Symtom på virtualiseringsskydd

Om virtualiseringsgarantier utlöses efter en testväxling kan ett eller flera av följande symptom visas:  

* **GenerationID-värdet** ändras.

    ![Ändring av genererings-ID](./media/site-recovery-active-directory/Event2170.png)

* Värdet **InvocationID** ändras.

    ![Ändring av anrops-ID](./media/site-recovery-active-directory/Event1109.png)

* Sysvol-mappen och NETLOGON-resurserna är inte tillgängliga.

    ![Sysvol mappresurs](./media/site-recovery-active-directory/sysvolshare.png)

    ![NtFrs sysvol-mapp](./media/site-recovery-active-directory/Event13565.png)

* DFSR-databaser tas bort.

    ![DFSR-databaser tas bort](./media/site-recovery-active-directory/Event2208.png)


### <a name="troubleshoot-domain-controller-issues-during-test-failover"></a>Felsöka problem med domänkontrollanten under test redundans

> [!IMPORTANT]
> Vissa av de konfigurationer som beskrivs i det här avsnittet är inte standardkonfigurationer eller standardkonfigurationer för domänkontrollanter. Om du inte vill göra dessa ändringar i en produktionsdomänkontrollant kan du skapa en domänkontrollant som är dedikerad för redundans för platsåterställning. Gör ändringarna endast till den dedikerade domänkontrollanten.  
>
>

1. Kör följande kommando i kommandotolken för att kontrollera om sysvol-mappen och NETLOGON-mappen är delade:

    `NET SHARE`

2. Kör följande kommando i kommandotolken för att se till att domänkontrollanten fungerar korrekt:

    `dcdiag /v > dcdiag.txt`

3. Leta efter följande text i utdataloggen. Texten bekräftar att domänkontrollanten fungerar korrekt.

    * "godkänd testanslutning"
    * "godkänd test Reklam"
    * "godkänt test MachineAccount"

Om föregående villkor är uppfyllda är det troligt att domänkontrollanten fungerar korrekt. Om så inte är fallet gör du följande:

1. Gör en auktoritär återställning av domänkontrollanten. Tänk på följande:
    * Även om vi inte rekommenderar [FRS-replikering,](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs/)om du använder FRS-replikering, följer du stegen för en auktoritär återställning. Processen beskrivs i [Använda registernyckeln BurFlags för att initiera om filreplikeringstjänsten](https://support.microsoft.com/kb/290762).

        För mer information om BurFlags, se [blogginlägget D2 och D4: Vad är det för?](https://blogs.technet.microsoft.com/janelewis/2006/09/18/d2-and-d4-what-is-it-for/).
    * Om du använder DFSR-replikering slutför du stegen för en auktoritär återställning. Processen beskrivs i [Force en auktoritär och icke-auktoritär synkronisering för DFSR-replikerad sysvol mapp (som "D4/D2" för FRS)](https://support.microsoft.com/kb/2218556).

        Du kan också använda PowerShell-funktionerna. Mer information finns i [DFSR-SYSVOL-auktoritära/icke-auktoritära återställningsfunktioner](https://blogs.technet.microsoft.com/thbouche/2013/08/28/dfsr-sysvol-authoritative-non-authoritative-restore-powershell-functions/)för PowerShell .

2. Kringgå det första synkroniseringskravet genom att ange följande registernyckel till **0** i den lokala domänkontrollanten. Om DWORD inte finns kan du skapa det under **noden Parametrar.**

    `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Repl Perform Initial Synchronizations`

    Mer information finns i [Felsöka DNS-händelse-ID 4013: DNS-servern kunde inte läsa in AD-integrerade DNS-zoner](https://support.microsoft.com/kb/2001093).

3. Inaktivera kravet på att en global katalogserver ska vara tillgänglig för att validera användarinloggningen. Det gör du i den lokala domänkontrollanten som anger följande registernyckel till **1**. Om DWORD inte finns kan du skapa det under **Lsa-noden.**

    `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\IgnoreGCFailures`

    Mer information finns i [Inaktivera kravet på att en global katalogserver ska vara tillgänglig för att validera användarinloggning](https://support.microsoft.com/kb/241789).

### <a name="dns-and-domain-controller-on-different-machines"></a>DNS och domänkontrollant på olika datorer

Om du kör domänkontrollanten och DN:erna på samma virtuella dator kan du hoppa över den här proceduren.


Om DNS inte finns på samma virtuella dator som domänkontrollanten måste du skapa en VIRTUELL DNS-dator för testundansen. Du kan använda en ny DNS-server och skapa alla nödvändiga zoner. Om Active Directory-domänen till exempel är contoso.com kan du skapa en DNS-zon med namnet contoso.com. De poster som motsvarar Active Directory måste uppdateras i DNS enligt följande:

1. Kontrollera att dessa inställningar finns på plats innan någon annan virtuell dator i återställningsplanen startar:
   * Zonen måste namnges efter skogens rotnamn.
   * Zonen måste vara filstödd.
   * Zonen måste vara aktiverad för säkra uppdateringar och osäkra uppdateringar.
   * Matcharen för den virtuella datorn som är värd för domänkontrollanten bör peka på IP-adressen för den virtuella DNS-datorn.

2. Kör följande kommando på den virtuella datorn som är värd för domänkontrollanten:

    `nltest /dsregdns`

3. Kör följande kommandon för att lägga till en zon på DNS-servern, tillåta osäkra uppdateringar och lägga till en post för zonen i DNS:

    `dnscmd /zoneadd contoso.com  /Primary`

    `dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1`

    `dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM>`

    `dnscmd /config contoso.com /allowupdate 1`

## <a name="next-steps"></a>Nästa steg
Läs mer om [hur du skyddar företagsarbetsbelastningar med Azure Site Recovery](site-recovery-workload.md).
