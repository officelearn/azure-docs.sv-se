---
title: Skydda Active Directory och DNS med Azure Site Recovery | Microsoft Docs
description: Den här artikeln beskriver hur du implementerar en lösning för haveriberedskap för Active Directory med hjälp av Azure Site Recovery.
services: site-recovery
documentationcenter: ''
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 07/19/2018
ms.author: manayar
ms.openlocfilehash: 28ea6c58eed110cfb57f3feaa6b828289bd20e64
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44054554"
---
# <a name="use-azure-site-recovery-to-protect-active-directory-and-dns"></a>Använda Azure Site Recovery för att skydda Active Directory och DNS

Företagsprogram som SharePoint, Dynamics AX och SAP beror på Active Directory och DNS-infrastrukturen ska fungera korrekt. När du konfigurerar haveriberedskap för program, behöver du ofta att återställa Active Directory och DNS innan du återställer andra programkomponenter, för att säkerställa att rätt programfunktionen.

Du kan använda [Site Recovery](site-recovery-overview.md) att skapa en haveriberedskapsplan för Active Directory. När avbrott uppstår kan initiera du redundans. Du kan ha Active Directory dig och som körs i ett par minuter. Om du har distribuerat Active Directory för flera program på din primära plats, till exempel kan för SharePoint och SAP, du växla över den fullständiga platsen. Du kan först växlar över Active Directory med Site Recovery. Växla över de andra programmen, med programspecifika återställningsplaner.

Den här artikeln beskriver hur du skapar en lösning för haveriberedskap för Active Directory. Den innehåller förutsättningar och instruktioner för redundans. Du bör känna till Active Directory och Site Recovery innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

* Om du replikerar till Azure, [förbereda Azure-resurser](tutorial-prepare-azure.md), inklusive en prenumeration, Azure Virtual Network, ett lagringskonto och ett Recovery Services-valv.
* Granska [kraven för stöd](site-recovery-support-matrix-to-azure.md) för alla komponenter.

## <a name="replicate-the-domain-controller"></a>Replikera domänkontrollanten

- Du måste konfigurera [Site Recovery-replikering](#enable-protection-using-site-recovery), på minst en virtuell dator som är värd för en domänkontrollant eller DNS.
- Om du har [flera domänkontrollanter](#environment-with-multiple-domain-controllers) i din miljö kan du också ställa in en [ytterligare en domänkontrollant](#protect-active-directory-with-active-directory-replication) på målplatsen. Ytterligare en domänkontrollant kan vara i Azure eller i ett sekundärt lokalt datacenter.
- Om du har bara ett fåtal program och en domänkontrollant, kan du redundansväxla hela platsen. I det här fallet bör du använda Site Recovery replikera domänkontrollanten till målplatsen (antingen i Azure eller i ett sekundärt lokalt datacenter). Du kan använda samma replikerad domänkontrollant eller DNS-dator för [redundanstest](#test-failover-considerations).
- - Om du har många program och mer än en domänkontrollant i din miljö eller om du planerar att växla över några program samtidigt, förutom att replikera den domain controller virtuella datorn med Site Recovery, rekommenderar vi att du ställer in en [ytterligare en domänkontrollant](#protect-active-directory-with-active-directory-replication) på målplatsen (antingen i Azure eller i ett sekundärt lokalt datacenter). För [redundanstest](#test-failover-considerations), du kan använda en domänkontrollant som replikeras av Site Recovery. Du kan använda ytterligare en domänkontrollant på målplatsen för redundans.

## <a name="enable-protection-with-site-recovery"></a>Aktivera skydd med Site Recovery

Du kan använda Site Recovery för att skydda den virtuella dator som är värd för en domänkontrollant eller DNS.

### <a name="protect-the-vm"></a>Skydda den virtuella datorn
Den domänkontrollant som replikeras med hjälp av Site Recovery används för [redundanstest](#test-failover-considerations). Kontrollera att den uppfyller följande krav:

1. Domänkontrollanten är en global katalogserver.
2. Domänkontrollanten ska vara FSMO-rollägare för roller som krävs under ett redundanstest. I annat fall dessa roller måste vara [tagit](http://aka.ms/ad_seize_fsmo) efter redundansen.

### <a name="configure-vm-network-settings"></a>Konfigurera nätverksinställningar för virtuella datorer
För den virtuella datorn som är värd för domänkontrollanten eller DNS i Site Recovery, konfigurerar du nätverksinställningar under den **beräkning och nätverk** inställningarna för den replikerade virtuella datorn. Detta säkerställer att den virtuella datorn är ansluten till rätt nätverk efter redundansväxling.

## <a name="protect-active-directory"></a>Skydda Active Directory

### <a name="site-to-site-protection"></a>Plats-till-plats-skydd
Skapa en domänkontrollant på den sekundära platsen. När du befordrar servern till en domänkontrollant, ange namnet på samma domän som används på den primära platsen. Du kan använda den **Active Directory-platser och tjänster** snapin-modulen kan konfigurera inställningar på länken platsobjektet som platserna ska läggas till. Du kan styra när replikeringen sker mellan två eller flera platser och hur ofta det inträffar genom att konfigurera inställningar på en plats. Mer information finns i [schemalägga replikering mellan platser](https://technet.microsoft.com/library/cc731862.aspx).

### <a name="site-to-azure-protection"></a>Plats-till-Azure-skydd
Skapa först en domänkontrollant i en Azure-nätverk. När du befordrar servern till en domänkontrollant, kan du ange samma domännamn som används på den primära platsen.

Konfigurera sedan om DNS-server för det virtuella nätverket använder DNS-server i Azure.

![Azure-nätverk](./media/site-recovery-active-directory/azure-network.png)

### <a name="azure-to-azure-protection"></a>Azure till Azure-skydd
Skapa först en domänkontrollant i en Azure-nätverk. När du befordrar servern till en domänkontrollant, kan du ange samma domännamn som används på den primära platsen.

Konfigurera sedan om DNS-server för det virtuella nätverket använder DNS-server i Azure.

## <a name="test-failover-considerations"></a>Testa tänka på vid
För att undvika påverkan på produktionsarbetsbelastningar kan inträffar testa redundans i ett nätverk som är isolerat från produktionsnätverket.

De flesta programmen kräver förekomsten av en domänkontrollant eller en DNS-server. Innan programmet växlar måste skapa du därför en domänkontrollant i det isolerade nätverket som ska användas för att testa redundans. Det enklaste sättet att göra detta är att använda Site Recovery för att replikera en virtuell dator som är värd för en domänkontrollant eller DNS. Kör ett redundanstest för den virtuella domain controller datorn innan du kör ett redundanstest av återställningsplanen för programmet. Här är hur du gör det:

1. Använd Site Recovery till [replikera](vmware-azure-tutorial.md) den virtuella dator som är värd för en domänkontrollant eller DNS.
2. Skapa ett isolerat nätverk. Alla virtuella nätverk som du skapar i Azure är isolerade från andra nätverk som standard. Vi rekommenderar att du använder samma IP-adressintervallet för det här nätverket som du använder i företagets nätverk. Aktivera inte plats-till-plats-anslutningen i det här nätverket.
3. Ange en DNS-IP-adress i det isolerade nätverket. Använd IP-adressen som du förväntar dig DNS-virtuella datorn för att hämta. Om du replikerar till Azure, kan du ange IP-adressen för den virtuella datorn som används vid redundans. Ange IP-adressen i den replikerade virtuella datorn i den **beräkning och nätverk** inställningar, Välj den **mål-IP** inställningar.

    ![Testning i Azure-nätverk](./media/site-recovery-active-directory/azure-test-network.png)

    > [!TIP]
    > Site Recovery försöker skapa virtuella datorer för testning i ett undernät med samma namn och genom att använda samma IP-adress som har angetts i den **beräkning och nätverk** inställningarna för den virtuella datorn. Om ett undernät med samma namn inte är tillgängligt i Azure-nätverk som har angetts för redundanstestning, skapas den virtuella testdatorn i alfabetisk ordning första undernätet.
    >
    > Om mål-IP är en del av det valda undernätet, försöker Site Recovery skapa virtuella testdatorn redundans med hjälp av IP-måladressen. Om mål-IP inte är en del av det valda undernätet, skapas virtuella testdatorn redundans med hjälp av nästa tillgängliga IP-Adressen i det valda undernätet.
    >
    >

### <a name="test-failover-to-a-secondary-site"></a>Testa redundans till en sekundär plats

1. Om du replikerar till en annan lokal plats och du använder DHCP, [konfigurera DNS och DHCP för att testa redundans](hyper-v-vmm-test-failover.md#prepare-dhcp).
2. Göra en redundanstestning av domain controller virtuell dator som körs i det isolerade nätverket. Använd den senaste tillgängliga *programkonsekvent* återställningspunkt av domain controller virtuell dator till gör redundanstestningen.
3. Kör ett redundanstest för den återställningsplan som innehåller virtuella datorer som programmet körs på.
4. När testet är klart, *Rensa redundanstestet* på domain controller virtuella datorn. Det här steget tar bort den domänkontrollant som har skapats för att testa redundans.


### <a name="remove-references-to-other-domain-controllers"></a>Ta bort referenser till andra domänkontrollanter
När du initierar ett redundanstest innehåller inte alla domänkontrollanter i testnätverket. Om du vill ta bort referenser till andra domänkontrollanter som finns i produktionsmiljön, du kan behöva [överta FSMO Active Directory-roller](http://aka.ms/ad_seize_fsmo) och gör [metadataborttagning](https://technet.microsoft.com/library/cc816907.aspx) för domänkontrollanter som saknas .


### <a name="issues-caused-by-virtualization-safeguards"></a>Problem som orsakas av skyddsmekanismerna för virtualisering

> [!IMPORTANT]
> Vissa av de konfigurationer som beskrivs i det här avsnittet är inte standard- eller standard domain controller konfigurationer. Om du inte vill göra dessa ändringar till en domänkontrollant för produktion, kan du skapa en domänkontrollant som är dedikerad för Site Recovery ska använda för att testa redundans. Dessa bara göra ändringar i domänkontrollanten.  
>
>

Från och med Windows Server 2012 [ytterligare säkerhetsåtgärder som är inbyggda i Active Directory Domain Services (AD DS)](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100). Dessa skydd hjälper till att skydda virtualiserade domänkontrollanter mot USN återställningar om den underliggande hypervisor-plattformen stöder **VM-Generationsid**. Azure stöder **VM-Generationsid**. Därför har domänkontrollanter som kör Windows Server 2012 eller senare på Azure-datorer dessa ytterligare säkerhetsåtgärder.


När **VM-Generationsid** återställs, den **InvocationID** återställs även värdet för AD DS-databasen. Dessutom RID-poolen förkastas och SYSVOL markeras som icke-auktoritativ. Mer information finns i [introduktion till virtualisering av Active Directory Domain Services](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100) och [på ett säkert sätt virtualisering av DFSR](https://blogs.technet.microsoft.com/filecab/2013/04/05/safely-virtualizing-dfsr/).

Redundansväxel till Azure kan orsaka **VM-Generationsid** att återställa. När du återställer **VM-Generationsid** utlöser ytterligare säkerhetsåtgärder när domain controller virtuella datorn startar i Azure. Detta kan resultera i en *betydande fördröjning* ska kunna logga in på domain controller virtuella datorn.

Eftersom den här domänkontrollanten används endast i ett redundanstest virtualiseringsskydd inte är nödvändiga. Att se till att den **VM-Generationsid** ändras inte värdet för domain controller virtuell dator kan du ändra värdet för följande DWORD till **4** i den lokala domänkontrollanten:


`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\gencounter\Start`


#### <a name="symptoms-of-virtualization-safeguards"></a>Symtomen för virtualiseringsskydd

Om efter ett redundanstest utlöses skyddsmekanismerna för virtualisering, visas en eller flera av följande:  

* Den **GenerationID** värdet ändras.

    ![Ändring av generations-ID](./media/site-recovery-active-directory/Event2170.png)

* Den **InvocationID** värdet ändras.

    ![Ändring av anrops-ID](./media/site-recovery-active-directory/Event1109.png)

* SYSVOL och NETLOGON-resurser är inte tillgängliga.

    ![SYSVOL-resursen](./media/site-recovery-active-directory/sysvolshare.png)

    ![NtFrs SYSVOL](./media/site-recovery-active-directory/Event13565.png)

* DFSR-databaser tas bort.

    ![DFSR-databaser har tagits bort](./media/site-recovery-active-directory/Event2208.png)


### <a name="troubleshoot-domain-controller-issues-during-test-failover"></a>Felsöka domänkontrollantproblem under redundanstest

> [!IMPORTANT]
> Några av de konfigurationer som beskrivs i det här avsnittet är inte standard- eller standard domain controller konfigurationer. Om du inte vill göra dessa ändringar till en domänkontrollant för produktion, kan du skapa en domänkontrollant som är dedikerad för redundanstest för Site Recovery. Ändra bara till den dedikerade domänkontrollanten.  
>
>

1. Kör följande kommando för att kontrollera om SYSVOL och NETLOGON-mappar som delas i Kommandotolken:

    `NET SHARE`

2. Kör följande kommando för att se till att domänkontrollanten fungerar korrekt i Kommandotolken:

    `dcdiag /v > dcdiag.txt`

3. Leta efter följande text i utdataloggen. Texten bekräftar att domänkontrollanten fungerar korrekt.

    * ”lyckades test anslutning”
    * ”lyckades test annonserar”
    * ”lyckades test MachineAccount”

Om föregående villkor är uppfyllda är det troligt att domänkontrollanten fungerar korrekt. Om den inte gör du följande:

1. Göra en auktoritativ återställning på domänkontrollanten. Tänk på följande information:
    * Även om vi inte rekommenderar [FRS replikering](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs/), om du använder FRS-replikering, följer du stegen för en auktoritativ återställning. Processen beskrivs i [använda registernyckeln BurFlags för att starta om tjänsten File Replication](https://support.microsoft.com/kb/290762).

        Mer information om BurFlags finns i bloggposten [D2 och D4: Vad är det för?](https://blogs.technet.microsoft.com/janelewis/2006/09/18/d2-and-d4-what-is-it-for/).
    * Om du använder DFSR replikering, följer du instruktionerna för en auktoritativ återställning. Processen beskrivs i [tvinga fram en auktoritär och icke-auktoritativ synkronisering för DFSR-replikerad SYSVOL (som ”D4/D2” för FRS)](https://support.microsoft.com/kb/2218556).

        Du kan också använda PowerShell-funktioner. Mer information finns i [DFSR SYSVOL auktoritativa/icke-auktoritativ återställning PowerShell funktioner](https://blogs.technet.microsoft.com/thbouche/2013/08/28/dfsr-sysvol-authoritative-non-authoritative-restore-powershell-functions/).

2. Kringgå kravet på den inledande synkroniseringen genom att ange följande registernyckel till **0** i den lokala domänkontrollanten. Om det DWORD-värdet inte finns, kan du skapa den under den **parametrar** noden.

    `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Repl Perform Initial Synchronizations`

    Mer information finns i [felsöka DNS-händelse-ID 4013: DNS-servern kunde inte läsa in AD-integrerade DNS-zoner](https://support.microsoft.com/kb/2001093).

3. Inaktivera kravet på att en global katalogserver är tillgängliga för att verifiera användarinloggning. Om du vill göra detta, i den lokala domänkontrollanten, anger du följande registernyckel **1**. Om det DWORD-värdet inte finns, kan du skapa den under den **Lsa** noden.

    `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\IgnoreGCFailures`

    Mer information finns i [inaktivera kravet på att en global katalogserver är tillgängliga för att validera användarinloggningar](http://support.microsoft.com/kb/241789).

### <a name="dns-and-domain-controller-on-different-machines"></a>DNS- och domänkontrollant på olika datorer

Om du kör en domänkontrollant och DNs på samma virtuella dator, kan du hoppa över den här proceduren.


Om DNS inte är på samma virtuella dator som domänkontrollant, måste du skapa en DNS VM för att testa redundans. Du kan använda en ny DNS-server och skapa alla nödvändiga zoner. Till exempel kan Active Directory-domänen är contoso.com, du skapa en DNS-zon med namnet contoso.com. Poster som motsvarar Active Directory måste uppdateras i DNS på följande sätt:

1. Se till att de här inställningarna är uppfyllda innan du börjar någon annan virtuell dator i återställningsplanen:
   * Zonen måste ha namnet efter skogsrotens namn.
   * Zonen måste vara filbaserat.
   * Zonen måste vara aktiverat för säkra och osäkra uppdateringar.
   * Konfliktlösare för den virtuella datorn som är värd för domänkontrollanten måste peka på IP-adressen för den virtuella datorn i DNS.

2. Kör följande kommando på den virtuella datorn som är värd för domänkontrollanten:

    `nltest /dsregdns`

3. Kör följande kommandon för att lägga till en zon på DNS-servern, tillåta ej säkra uppdateringar och lägga till en post för zonen till DNS:

    `dnscmd /zoneadd contoso.com  /Primary`

    `dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1`

    `dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM>`

    `dnscmd /config contoso.com /allowupdate 1`

## <a name="next-steps"></a>Nästa steg
Läs mer om [skyddar arbetsbelastningar med Azure Site Recovery](site-recovery-workload.md).
