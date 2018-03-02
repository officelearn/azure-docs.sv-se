---
title: Skydda Active Directory och DNS med Azure Site Recovery | Microsoft Docs
description: "Den här artikeln beskriver hur du implementerar en lösning för katastrofåterställning för Active Directory med hjälp av Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/27/2018
ms.author: manayar
ms.openlocfilehash: e07b868883b0154ad38ba2f7f51dd2db663525a0
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2018
---
# <a name="use-azure-site-recovery-to-protect-active-directory-and-dns"></a>Använda Azure Site Recovery för att skydda Active Directory och DNS

Företagsprogram som SharePoint, Dynamics AX och SAP är beroende av Active Directory och DNS-infrastrukturen ska fungera korrekt. När du ställer in katastrofåterställning för program kan behöver du ofta återställa Active Directory och DNS innan du återställer andra programkomponenter, för att säkerställa rätt programfunktionen.

Du kan använda [Site Recovery](site-recovery-overview.md) att skapa en plan för katastrofåterställning för Active Directory. När ett avbrott inträffar kan du påbörja en växling. Du kan ha Active Directory in och köra några minuter. Om du har distribuerat Active Directory för flera program på din primära plats, till exempel kanske SharePoint och SAP, du vill växla över fullständig webbplats. Först kan du växla över Active Directory med objekt återställning. Växla sedan över de andra programmen som med programspecifika återställningsplaner.

Den här artikeln förklaras hur du skapar en lösning för katastrofåterställning för Active Directory. Den omfattar krav och anvisningar för växling vid fel. Du bör känna till Active Directory och Site Recovery innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

* Om du replikerar till Azure, [förbereda Azure-resurser](tutorial-prepare-azure.md), inklusive en prenumeration, ett virtuellt Azure-nätverk, ett lagringskonto och Recovery Services-valvet.
* Granska [kraven för stöd](site-recovery-support-matrix-to-azure.md) för alla komponenter.

## <a name="replicate-the-domain-controller"></a>Replikera domänkontrollanten

Du måste ställa in [Site Recovery replikering](#enable-protection-using-site-recovery), på minst en virtuell dator som är värd för en domänkontrollant eller DNS. Om du har [flera domänkontrollanter](#environment-with-multiple-domain-controllers) i din miljö måste du ställa en [ytterligare en domänkontrollant](#protect-active-directory-with-active-directory-replication) på målplatsen. Ytterligare en domänkontrollant kan vara i Azure eller i ett sekundärt lokalt datacenter.

### <a name="single-domain-controller"></a>En domänkontrollant
Om du har bara några program och en domänkontrollant, kanske du vill växla över hela webbplatsen tillsammans. I det här fallet bör du använda Site Recovery replikera domänkontrollanten till målplatsen (antingen i Azure eller i ett sekundärt lokalt datacenter). Du kan använda samma replikerade domänkontrollant eller DNS-virtuell dator för [redundanstestningen](#test-failover-considerations).

### <a name="multiple-domain-controllers"></a>Flera domänkontrollanter
Om du har många program och mer än en domänkontrollant i din miljö eller om du planerar att växla över några program samtidigt, förutom att replikera domain controller virtuell dator med Site Recovery rekommenderar vi att du ställer in en [ytterligare en domänkontrollant](#protect-active-directory-with-active-directory-replication) på målplatsen (antingen i Azure eller i ett sekundärt lokalt datacenter). För [redundanstestningen](#test-failover-considerations), du kan använda en domänkontrollant som replikeras av Site Recovery. Du kan använda ytterligare en domänkontrollant på målplatsen för redundans.

## <a name="enable-protection-with-site-recovery"></a>Aktivera skydd med Site Recovery

Du kan använda Site Recovery för att skydda den virtuella datorn som är värd för domänkontrollanten eller DNS.

### <a name="protect-the-vm"></a>Skydda den virtuella datorn
Den domänkontrollant som replikeras med hjälp av Site Recovery används för [redundanstestningen](#test-failover-considerations). Kontrollera att det uppfyller följande krav:

1. Domänkontrollanten är en global katalogserver.
2. Domänkontrollanten ska vara FSMO-rollägare för roller som krävs under ett redundanstest. Annars dessa roller måste vara [beslag](http://aka.ms/ad_seize_fsmo) efter växling vid fel.

### <a name="configure-vm-network-settings"></a>Konfigurera nätverksinställningar för Virtuella datorer
Konfigurera nätverksinställningar enligt för den virtuella datorn som är värd för domänkontrollanten eller DNS i Site Recovery på **beräknings- och nätverksinställningar** inställningarna för den replikerade virtuella datorn. Detta säkerställer att den virtuella datorn är ansluten till rätt nätverk efter redundansväxling.

## <a name="protect-active-directory"></a>Skydda Active Directory

### <a name="site-to-site-protection"></a>Plats-till-plats-skydd
Skapa en domänkontrollant på den sekundära platsen. När du befordrar servern till en domänkontrollant, ange namnet på samma domän som används på den primära platsen. Du kan använda den **Active Directory-platser och tjänster** snapin-modulen att konfigurera inställningar på platslänkobjekt som platserna ska läggas till. Du kan styra när replikeringen sker mellan två eller flera platser och hur ofta den inträffar genom att konfigurera inställningar på en länk. Mer information finns i [schemalägga replikering mellan platser](https://technet.microsoft.com/library/cc731862.aspx).

### <a name="site-to-azure-protection"></a>Plats till Azure-skydd
Första, [skapa en domänkontrollant i en Azure-nätverket](../active-directory/active-directory-install-replica-active-directory-domain-controller.md). När du befordrar servern till en domänkontrollant, kan du ange samma domännamn som används på den primära platsen.

Sedan [konfigurera DNS-servern för det virtuella nätverket](../active-directory/active-directory-install-replica-active-directory-domain-controller.md#reconfigure-dns-server-for-the-virtual-network) du använder DNS-servern i Azure.

![Azure-nätverk](./media/site-recovery-active-directory/azure-network.png)

### <a name="azure-to-azure-protection"></a>Azure-Azure-skydd
Första, [skapa en domänkontrollant i en Azure-nätverket](../active-directory/active-directory-install-replica-active-directory-domain-controller.md). När du befordrar servern till en domänkontrollant, kan du ange samma domännamn som används på den primära platsen.

Sedan [konfigurera DNS-servern för det virtuella nätverket](../active-directory/active-directory-install-replica-active-directory-domain-controller.md#reconfigure-dns-server-for-the-virtual-network) du använder DNS-servern i Azure.

## <a name="test-failover-considerations"></a>Redundanstestning
För att undvika påverkan på produktionsarbetsbelastningar, inträffar testa redundans i ett nätverk som är isolerat från produktionsnätverket.

De flesta program kräver förekomsten av en domänkontrollant eller en DNS-server. Därför innan programmet växlar, måste du skapa en domänkontrollant i isolerat nätverk som ska användas för att testa redundans. Det enklaste sättet att göra detta är att använda Site Recovery replikera en virtuell dator som är värd för en domänkontrollant eller DNS. Sedan kör ett redundanstest för den domain controller virtuella datorn innan du kör ett redundanstest av återställningsplan för programmet. Här visas hur du gör det:

1. Använd Site Recovery [replikera](site-recovery-replicate-vmware-to-azure.md) den virtuella datorn som är värd för domänkontrollanten eller DNS.
2. Skapa ett isolerat nätverk. Alla virtuella nätverk som du skapar i Azure är isolerad från andra nätverk som standard. Vi rekommenderar att du använder samma IP-adressintervallet för det här nätverket som du använder i företagets nätverk. Aktivera inte plats-till-plats-anslutning i nätverket.
3. Ange ett DNS-IP-adress i isolerat nätverk. Använd IP-adressen som du förväntar dig att den virtuella datorn i DNS-få. Om du replikerar till Azure kan du ange IP-adressen för den virtuella datorn som används vid redundans. Ange IP-adressen i den replikerade virtuella datorn i den **beräknings- och nätverksinställningar** inställningar, väljer den **mål-IP** inställningar.

    ![Azure testnätverket](./media/site-recovery-active-directory/azure-test-network.png)

    > [!TIP]
    > Site Recovery försöker skapa virtuella testdatorer i ett undernät med samma namn och genom att använda samma IP-adress som har angetts i den **beräknings- och nätverksinställningar** inställningarna för den virtuella datorn. Om ett undernät med samma namn är inte tillgänglig i virtuella Azure-nätverket som har angetts för att testa redundans, skapas den virtuella testdatorn i alfabetisk ordning första undernätet.
    >
    > Om mål-IP-adressen är en del av det valda undernätet, försöker Site Recovery skapa testa redundans virtuell dator med hjälp av IP-måladressen. Om mål-IP inte är en del av det valda undernätet, har testa redundans virtuell dator skapats med hjälp av nästa tillgängliga IP-Adressen i det valda undernätet.
    >
    >

### <a name="test-failover-to-a-secondary-site"></a>Testa redundans till en sekundär plats

1. Om du replikerar till en annan lokal plats och du använder DHCP, [konfigurera DNS och DHCP för att testa redundans](hyper-v-vmm-test-failover.md#prepare-dhcp).
2. Göra ett redundanstest av domain controller virtuell dator som körs i isolerat nätverk. Använd den senaste tillgängliga *programmet konsekvent* återställningspunkt domain controller virtuell dator om du vill testa redundans.
3. Köra ett redundanstest för återställningsplanen innehåller virtuella datorer som programmet körs på.
4. När testet är klart, *Rensa redundanstestet* på domain controller virtuella datorn. Det här steget tar bort den domänkontrollant som skapades för redundanstestningen.


### <a name="remove-references-to-other-domain-controllers"></a>Ta bort referenser till andra domänkontrollanter
När du initierar ett redundanstest inte innehåller alla domänkontrollanter i testnätverket. Om du vill ta bort referenser till andra domänkontrollanter som finns i produktionsmiljön, du kan behöva [överta FSMO Active Directory-roller](http://aka.ms/ad_seize_fsmo) och gör [rensning av metadata](https://technet.microsoft.com/library/cc816907.aspx) för domänkontrollanter som saknas .


### <a name="issues-caused-by-virtualization-safeguards"></a>Problem som orsakas av virtualiseringsskydd

> [!IMPORTANT]
> Vissa av konfigurationerna som beskrivs i det här avsnittet är inte standard- eller standard domain controller konfigurationer. Om du inte vill att göra dessa ändringar till en domänkontrollant för produktion, kan du skapa en domänkontrollant som är dedikerad för Site Recovery ska användas för att testa redundans. Göra dessa ändringar endast för domänkontrollanten.  
>
>

Från och med Windows Server 2012 [ytterligare skydd är inbyggda i Active Directory Domain Services (AD DS)](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100). Dessa skydd skydda virtualiserade domänkontrollanter mot USN-återställning om den underliggande hypervisor-plattformen stöder **VM-GenerationID**. Azure stöder **VM-GenerationID**. Därför har domänkontrollanter som kör Windows Server 2012 eller senare på Azure virtual machines dessa ytterligare skydd.


När **VM-GenerationID** återställs, den **InvocationID** värdet för AD DS-databasen återställs också. Dessutom RID-poolen förkastas och SYSVOL är markerad som icke-auktoritativa. Mer information finns i [introduktion till virtualisering av Active Directory Domain Services](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100) och [säker virtualisering av DFSR](https://blogs.technet.microsoft.com/filecab/2013/04/05/safely-virtualizing-dfsr/).

Misslyckande till Azure kan orsaka **VM-GenerationID** att återställa. Återställer **VM-GenerationID** utlöser ytterligare skydd när domain controller virtuella datorn startar i Azure. Detta kan resultera i en *betydande fördröjning* att de kan logga in på domänen controller virtuell dator.

Eftersom den här domänkontrollanten används endast i ett redundanstest virtualiseringsskydd inte är nödvändiga. Att se till att den **VM-GenerationID** ändras inte värdet för domain controller virtuell dator kan du ändra värdet för följande DWORD till **4** i den lokala domänkontrollanten:


`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\gencounter\Start`


#### <a name="symptoms-of-virtualization-safeguards"></a>Symptom på virtualiseringsskydd

Om efter ett redundanstest utlöses skyddsmekanismerna för virtualisering, kan du se en eller flera av följande problem:  

* Den **GenerationID** värdet ändras.

    ![Ändring av generations-ID](./media/site-recovery-active-directory/Event2170.png)

* Den **InvocationID** värdet ändras.

    ![Ändring av anrops-ID](./media/site-recovery-active-directory/Event1109.png)

* SYSVOL och NETLOGON resurser är inte tillgängliga.

    ![SYSVOL-resursen](./media/site-recovery-active-directory/sysvolshare.png)

    ![NtFrs SYSVOL](./media/site-recovery-active-directory/Event13565.png)

* DFSR-databaser tas bort.

    ![DFSR-databaser har tagits bort](./media/site-recovery-active-directory/Event2208.png)


### <a name="troubleshoot-domain-controller-issues-during-test-failover"></a>Felsökning av problem med domain controller under redundanstestningen

> [!IMPORTANT]
> Några av de konfigurationer som beskrivs i det här avsnittet är inte standard- eller standard domain controller konfigurationer. Om du inte vill att göra dessa ändringar till en domänkontrollant för produktion, kan du skapa en domänkontrollant som är dedikerad för Site Recovery testning av redundans. Ändra bara till den dedikerade domänkontrollanten.  
>
>

1. Kör följande kommando för att kontrollera om SYSVOL och NETLOGON mappar delas i Kommandotolken:

    `NET SHARE`

2. I Kommandotolken, kör du följande kommando för att kontrollera att domänkontrollanten fungerar korrekt:

    `dcdiag /v > dcdiag.txt`

3. Leta efter följande text i utdataloggen. Texten som bekräftar att domänkontrollanten fungerar korrekt.

    * ”Anslutningen skickade test”
    * ”Annonserar skickade test”
    * ”MachineAccount skickade test”

Om föregående villkoren uppfylls är det troligt att domänkontrollanten fungerar korrekt. Om den inte gör du följande:

1. Göra en auktoritativ återställning på domänkontrollanten. Tänk på följande information:
    * Även om vi inte rekommenderar [FRS replikering](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs/), om du använder FRS-replikering följer du stegen för en auktoritativ återställning. Den här processen beskrivs i [med registernyckeln BurFlags för att initiera tjänsten File Replication](https://support.microsoft.com/kb/290762).

        Mer information om BurFlags finns i bloggposten [D2 och D4: Vad är det för?](https://blogs.technet.microsoft.com/janelewis/2006/09/18/d2-and-d4-what-is-it-for/).
    * Om du använder DFSR replikering följer du instruktionerna för en auktoritativ återställning. Den här processen beskrivs i [tvingar fram en auktoritär och icke-auktoritär synkronisering för DFSR-replikerad SYSVOL (som ”D4/D2” för FRS)](https://support.microsoft.com/kb/2218556).

        Du kan också använda PowerShell-funktioner. Mer information finns i [DFSR SYSVOL auktoritära/icke-auktoritativ återställning PowerShell funktioner](https://blogs.technet.microsoft.com/thbouche/2013/08/28/dfsr-sysvol-authoritative-non-authoritative-restore-powershell-functions/).

2. Kringgå kravet på inledande synkroniseringen genom att ange följande registernyckel **0** i den lokala domänkontrollanten. Om DWORD-värdet inte finns, kan du skapa det under den **parametrar** nod.

    `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Repl Perform Initial Synchronizations`

    Mer information finns i [felsökning av DNS-händelse-ID 4013: DNS-servern kunde inte läsa in AD-integrerade DNS-zoner](https://support.microsoft.com/kb/2001093).

3. Inaktivera kravet på att en global katalogserver är tillgängliga för att verifiera användarinloggning. Om du vill göra detta, i den lokala domänkontrollanten, ange följande registernyckel till **1**. Om DWORD-värdet inte finns, kan du skapa det under den **Lsa** nod.

    `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\IgnoreGCFailures`

    Mer information finns i [inaktivera kravet på att en global katalogserver är tillgängliga för att validera användarinloggningar](http://support.microsoft.com/kb/241789).

### <a name="dns-and-domain-controller-on-different-machines"></a>DNS- och domänkontrollant på olika datorer
Om DNS inte på samma virtuella dator som domänkontrollant, måste du skapa en virtuell dator i DNS för att testa redundans. Om DNS- och domänkontrollanten inte finns på samma virtuella dator, kan du hoppa över det här avsnittet.

Du kan använda en ny DNS-server och skapa zonerna som krävs. Om Active Directory-domänen är contoso.com kan skapa du en DNS-zon med namnet contoso.com. Poster som motsvarar Active Directory måste uppdateras i DNS på följande sätt:

1. Se till att dessa inställningar är uppfyllda innan du börjar någon annan virtuell dator i återställningsplanen:
   * Zonen måste ha namnet efter skogsrotens namn.
   * Zonen måste vara filbaserat.
   * Zonen måste vara aktiverat för säkra och osäkra uppdateringar.
   * Matcharen för den virtuella datorn som är värd för domänkontrollanten måste peka på IP-adressen för den virtuella datorn i DNS.

2. Kör följande kommando på den virtuella datorn som är värd för domänkontrollanten:

    `nltest /dsregdns`

3. Kör följande kommandon för att lägga till en zon på DNS-servern, tillåter osäkra uppdateringar och lägga till en post för zonen DNS:

    `dnscmd /zoneadd contoso.com  /Primary`

    `dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1`

    `dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM>`

    `dnscmd /config contoso.com /allowupdate 1`

## <a name="next-steps"></a>Nästa steg
Lär dig mer om [skydda arbetsbelastningar på företag med Azure Site Recovery](site-recovery-workload.md).
