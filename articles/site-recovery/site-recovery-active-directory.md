---
title: Skydda Active Directory och DNS med Azure Site Recovery | Microsoft Docs
description: "Den här artikeln beskriver hur du implementerar en lösning för katastrofåterställning för Active Directory med Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: rochakm
editor: 
ms.assetid: af1d9b26-1956-46ef-bd05-c545980b72dc
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/15/2017
ms.author: manayar
ms.openlocfilehash: 5db2d424c176428d31fb99fd8288120f18fcac7a
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2017
---
# <a name="protect-active-directory-and-dns-with-azure-site-recovery"></a>Skydda Active Directory och DNS med Azure Site Recovery
Företagsprogram som SharePoint, Dynamics AX och SAP är beroende av Active Directory och DNS-infrastrukturen ska fungera korrekt. När du skapar en lösning för katastrofåterställning för program, kommer du ofta behöver återställa Active Directory och DNS innan andra programkomponenter som ska se till att rätt programfunktionen.

Du kan använda Site Recovery för att skapa en fullständig automatiserad haveriberedskapsplan för Active Directory. När avbrott inträffar kan du påbörja en växling inom några sekunder från var som helst och få Active Directory igång på några minuter. Om du har distribuerat Active Directory för flera program, till exempel SharePoint och SAP på din primära plats och du vill redundans fullständig webbplats, kan du redundans Active Directory först använda Site Recovery och växlar sedan över de andra programmen med programspecifika återställningsplaner.

Den här artikeln förklarar hur du skapar en lösning för katastrofåterställning för Active Directory, hur du utför redundans med en återställningsplan med ett klick, konfigurationer som stöds och förutsättningar.  Du bör känna till Active Directory och Azure Site Recovery innan du börjar.

## <a name="prerequisites"></a>Krav
* Ett Recovery Services-valv i en Microsoft Azure-prenumeration.
* Om du replikerar till Azure, [förbereda](tutorial-prepare-azure.md) Azure-resurser, inklusive en Azure-prenumeration, Azure-nätverk och ett lagringskonto.
* Granska kraven för stöd för alla komponenter.

## <a name="replicating-domain-controller"></a>Replikering av domänkontrollant

Du behöver installationsprogrammet [Site Recovery replikering](#enable-protection-using-site-recovery) på minst en virtuell dator som värd för en domänkontrollant och DNS. Om du har [flera domänkontrollanter](#environment-with-multiple-domain-controllers) i din miljö, förutom replikerar domain controller virtuell dator med Site Recovery du måste också konfigurera en [ytterligare en domänkontrollant](#protect-active-directory-with-active-directory-replication)på målplatsen (Azure eller ett sekundärt lokalt datacenter).

### <a name="single-domain-controller-environment"></a>Enkel domänkontrollantmiljö
Om du har några program och en enda domänkontrollant och du vill växla över hela webbplatsen tillsammans, sedan bör du använda Site Recovery replikera domänkontrollanten till målplatsen (Azure eller ett sekundärt lokalt datacenter). Samma replikerade domain controller/DNS-virtuella datorn kan användas för [redundanstestningen](#test-failover-considerations) samt.

### <a name="environment-with-multiple-domain-controllers"></a>Miljö med flera domänkontrollanter
Om du har många program och det finns fler än en domänkontrollant i miljön eller om du planerar att växla över några program samtidigt, rekommenderar vi att förutom replikerar domain controller virtuell dator med Site Recovery, bör du också Konfigurera en [ytterligare en domänkontrollant](#protect-active-directory-with-active-directory-replication) på målplatsen (Azure eller ett sekundärt lokalt datacenter). För [redundanstestningen](#test-failover-considerations), du kan använda domänkontrollant replikeras av Site Recovery och växling vid fel, ytterligare en domänkontrollant på målplatsen.

## <a name="enable-protection-using-site-recovery"></a>Aktivera skydd med Site Recovery
### <a name="protect-the-virtual-machine"></a>Skydda den virtuella datorn
Aktivera skyddet för virtuella domänen domänkontrollantens/DNS i Site Recovery. Domänkontrollanten replikeras med Site Recovery används för [redundanstestningen](#test-failover-considerations). Kontrollera att den uppfyller följande krav:

1. Domänkontrollanten är en global katalogserver
2. Domänkontrollanten bör vara FSMO-rollägare för roller som krävs under ett redundanstest (annars dessa roller måste vara [beslag](http://aka.ms/ad_seize_fsmo) efter växling vid fel)

### <a name="configure-virtual-machine-network-settings"></a>Konfigurera inställningar för virtuell dator
Konfigurera nätverksinställningar enligt inställningarna för beräknings- och nätverksinställningarna för den replikerade virtuella datorn i Site Recovery för domain controller/DNS-virtuella datorn. Detta är att säkerställa att den virtuella datorn ska anslutas till rätt nätverk efter redundansväxling.

## <a name="protect-active-directory-with-active-directory-replication"></a>Skydda Active Directory med Active Directory-replikering
### <a name="site-to-site-protection"></a>Plats-till-plats-skydd
Skapa en domänkontrollant på den sekundära platsen. När du befordrar servern till en domänkontrollant, ange namnet på samma domän som används på den primära platsen. Du kan använda den **Active Directory-platser och tjänster** snapin-modulen att konfigurera inställningar på platslänkobjekt som platserna ska läggas till. Genom att konfigurera inställningar på en platslänk, du kan styra när replikeringen sker mellan två eller flera platser och hur ofta. Mer information finns i [schemalägga replikering mellan platser](https://technet.microsoft.com/library/cc731862.aspx).

### <a name="site-to-azure-protection"></a>Plats till Azure-skydd
Följ instruktionerna för att [skapa en domänkontrollant i en Azure-nätverket](../active-directory/active-directory-install-replica-active-directory-domain-controller.md). När du befordrar servern till en domänkontrollant, kan du ange samma domännamn som används på den primära platsen.

Sedan [konfigurera DNS-servern för det virtuella nätverket](../active-directory/active-directory-install-replica-active-directory-domain-controller.md#reconfigure-dns-server-for-the-virtual-network), för att använda DNS-server i Azure.

![Azure-nätverk](./media/site-recovery-active-directory/azure-network.png)

### <a name="azure-to-azure-protection"></a>Azure-Azure-skydd
Följ instruktionerna för att [skapa en domänkontrollant i en Azure-nätverket](../active-directory/active-directory-install-replica-active-directory-domain-controller.md). När du befordrar servern till en domänkontrollant, kan du ange samma domännamn som används på den primära platsen.

Sedan [konfigurera DNS-servern för det virtuella nätverket](../active-directory/active-directory-install-replica-active-directory-domain-controller.md#reconfigure-dns-server-for-the-virtual-network), för att använda DNS-server i Azure.

## <a name="test-failover-considerations"></a>Redundanstestning
Testa redundans inträffar i ett nätverk som är isolerad från produktionsnätverket att undvika påverkan på produktionsarbetsbelastningar.

De flesta program kräver också förekomsten av en domänkontrollant och en DNS-server ska fungera. Därför innan programmet har redundansväxlats måste en domänkontrollant skapas i isolerat nätverk som ska användas för att testa redundans. Det enklaste sättet att göra detta är att replikera en domän domänkontrollantens/DNS virtuell dator med Site Recovery. Kör ett redundanstest för den domain controller virtuella datorn innan du kör ett redundanstest av återställningsplan för programmet. Här visas hur du gör det:

1. [Replikera](site-recovery-replicate-vmware-to-azure.md) domain controller/DNS-virtuell dator med Site Recovery.
2. Skapa ett isolerat nätverk. Alla virtuella nätverk som skapats i Azure som standard är isolerad från andra nätverk. Vi rekommenderar att IP-adressintervall för det här nätverket är samma som produktionsnätverket. Aktivera inte plats-till-plats-anslutning i nätverket.
3. Ange en DNS-IP-adress i nätverket som skapats som IP-adressen som du förväntar dig att den virtuella datorn i DNS-få. Om du replikerar till Azure, ange IP-adressen för den virtuella datorn som används på redundans i den **mål-IP** i **beräknings- och nätverksinställningar** inställningarna för den replikerade virtuella datorn.

    ![Azure Testnätverket](./media/site-recovery-active-directory/azure-test-network.png)

> [!TIP]
> Site Recovery försöker skapa virtuella testdatorer i ett undernät med samma namn och använder samma IP-Adressen som angetts i **beräknings- och nätverksinställningar** inställningarna för den virtuella datorn. Om ett undernät med samma namn inte finns i virtuella Azure-nätverket för testning av redundans, sedan skapas den virtuella testdatorn i det första undernätet i alfabetisk ordning. Om mål-IP är en del av det valda undernätet, försöker Site Recovery skapa testa redundans virtuell dator med hjälp av mål-IP. Om mål-IP inte är en del av det valda undernätet skapas testa redundans virtuell dator med nästa tillgängliga IP-Adressen i det valda undernätet.
>
>


1. Om du replikerar till en annan lokal plats och du använder DHCP, följ instruktionerna för att [konfigurera DNS och DHCP för att testa redundans](site-recovery-test-failover-vmm-to-vmm.md#prepare-dhcp)
2. Göra ett redundanstest för domain controller virtuell dator körs i isolerat nätverk. Använd senaste tillgängliga **programmet konsekvent** återställningspunkt domain controller virtuell dator om du vill testa redundans.
3. Köra ett redundanstest för återställningsplanen innehåller virtuella datorer av programmet.
4. När testningen är klar, **Rensa redundanstestet** på domain controller virtuella datorn. Det här steget tar bort den domänkontrollant som skapades för redundanstestningen.


### <a name="removing-reference-to-other-domain-controllers"></a>Ta bort referensen till andra domänkontrollanter
När du gör ett redundanstest sätta inte du alla domänkontrollanter i testnätverket. Om du vill ta bort referensen till andra domänkontrollanter som finns i produktionsmiljön, du kan behöva [överta FSMO Active Directory-roller](http://aka.ms/ad_seize_fsmo) och gör [rensning av metadata](https://technet.microsoft.com/library/cc816907.aspx) för saknar domän domänkontrollanter.



> [!IMPORTANT]
> Vissa av konfigurationerna som beskrivs i följande avsnitt är inte standard/domain controller standardkonfigurationerna. Om du inte vill att göra dessa ändringar till en domänkontrollant för produktion, kan du skapa en domänkontrollant dedikerade som ska användas för att testa redundans för Site Recovery och göra dessa ändringar som.  
>
>

### <a name="issues-because-of-virtualization-safeguards"></a>Problem på grund av virtualiseringsskydd

Från och med Windows Server 2012 [ytterligare skydd har skapats i Active Directory Domain Services](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100). Dessa skydd skydda virtualiserade domänkontrollanter mot USN-återställning, så länge den underliggande hypervisor-plattformen stöder VM-Generationsid. Azure stöder VM-Generationsid, vilket innebär att domänkontrollanter som kör Windows Server 2012 eller senare på Azure virtuella datorer har fler skyddsmekanismer.


När VM-GenerationID återställs invocationID för AD DS-databasen återställs även, RID-poolen förkastas och SYSVOL är markerad som icke-auktoritativ. Mer information finns i [introduktion till Active Directory DS-virtualisering](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100) och [säker virtualisering av DFSR](https://blogs.technet.microsoft.com/filecab/2013/04/05/safely-virtualizing-dfsr/)

Misslyckande till Azure kan orsaka återställning av VM-GenerationID och som aktiveras ytterligare skydd när domain controller virtuella datorn startar i Azure. Det kan resultera i en **betydande fördröjning** i användare som kan logga in på domänen controller virtuell dator. Eftersom den här domänkontrollanten kan endast användas i ett redundanstest, behövs inte skyddsmekanismerna för virtualisering. För att säkerställa att VM-GenerationID för domain controller virtuella datorn inte ändrar kan du ändra värdet för följande DWORD till 4 i den lokala domänkontrollanten.


        HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\gencounter\Start


#### <a name="symptoms-of-virtualization-safeguards"></a>Symptom på virtualiseringsskydd

Om virtualiseringsskydd har dessutom efter ett redundanstest, kan du se en eller flera av följande problem:  

Ändring av generations-ID

![Ändring av generations-ID](./media/site-recovery-active-directory/Event2170.png)

Ändring av anrops-ID

![Ändring av anrops-ID](./media/site-recovery-active-directory/Event1109.png)

SYSVOL och Netlogon resurser är inte tillgängliga

![SYSVOL-resursen](./media/site-recovery-active-directory/sysvolshare.png)

![NTFRS Sysvol](./media/site-recovery-active-directory/Event13565.png)

DFSR-databaser har tagits bort

![Ta bort DFSR-DB](./media/site-recovery-active-directory/Event2208.png)


> [!IMPORTANT]
> Vissa av konfigurationerna som beskrivs i följande avsnitt är inte standard/domain controller standardkonfigurationerna. Om du inte vill att göra dessa ändringar till en domänkontrollant för produktion, kan du skapa en domänkontrollant dedikerade som ska användas för att testa redundans för Site Recovery och göra dessa ändringar som.  
>
>


### <a name="troubleshooting-domain-controller-issues-during-test-failover"></a>Domain controller felsökningsproblem under redundanstestningen


Kör följande kommando för att kontrollera om SYSVOL och NETLOGON mappar delas på en kommandotolk:

    NET SHARE

Kör följande kommando för att kontrollera att domänkontrollanten fungerar korrekt på kommandoraden.

    dcdiag /v > dcdiag.txt

Leta efter följande text för att bekräfta att domänkontrollanten fungerar bra i utdataloggen.

* ”Anslutningen skickade test”
* ”Annonserar skickade test”
* ”MachineAccount skickade test”

Om föregående villkoren uppfylls är det troligt att domänkontrollanten fungerar väl. Annars kan du prova följande steg.


* Göra en auktoritativ återställning på domänkontrollanten.
    * Även om det är [bör inte använda FRS replikering](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs/), men om du använder fortfarande den och följer stegen [här](https://support.microsoft.com/kb/290762) att göra en auktoritativ återställning. Du kan läsa mer om Burflags diskuterats i föregående länk [här](https://blogs.technet.microsoft.com/janelewis/2006/09/18/d2-and-d4-what-is-it-for/).
    * Om du använder DFSR replication, och följ sedan anvisningarna tillgängliga [här](https://support.microsoft.com/kb/2218556) att göra en auktoritativ återställning. Du kan också använda Powershell-funktioner som är tillgängliga på den här [länken](https://blogs.technet.microsoft.com/thbouche/2013/08/28/dfsr-sysvol-authoritative-non-authoritative-restore-powershell-functions/) för detta ändamål.

* Kringgå synkroniseringskrav genom att ange följande registernyckel till 0 på den lokala domänkontrollanten. Om den här DWORD inte finns, kan du skapa det under noden-parametrar'. Du kan läsa mer om den [här](https://support.microsoft.com/kb/2001093)

        HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Repl Perform Initial Synchronizations

* Inaktivera kravet på att en global katalogserver är tillgänglig för att verifiera användaren loggar in genom att ange följande registernyckel till 1 i den lokala domänkontrollanten. Om den här DWORD inte finns kan du skapa det under noden 'Lsa. Du kan läsa mer om den [här](http://support.microsoft.com/kb/241789)

        HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\IgnoreGCFailures



### <a name="dns-and-domain-controller-on-different-machines"></a>DNS- och domänkontrollant på olika datorer
Om DNS inte på samma virtuella dator som domänkontrollant, måste du skapa en DNS VM för att testa redundans. Om de finns på samma virtuella dator kan du hoppa över det här avsnittet.

Du kan använda en ny DNS-server och skapa zonerna som krävs. Om Active Directory-domänen är contoso.com kan skapa du en DNS-zon med namnet contoso.com. Poster som motsvarar Active Directory måste uppdateras i DNS, enligt följande:

1. Kontrollera de här inställningarna är uppfyllda innan någon annan virtuell dator i återställningsplanen kommer:

   * Zonen måste ha namnet efter skogsrotens namn.
   * Zonen måste vara filbaserat.
   * Zonen måste vara aktiverat för säker och icke-säker uppdateringar.
   * Matcharen för domain controller virtuell dator ska peka på IP-adressen för den virtuella datorn i DNS.
2. Kör följande kommando på domain controller virtuell dator:

    `nltest /dsregdns`
3. Lägga till en zon på DNS-servern, tillåter inte är säkra uppdateringar och lägga till en post för DNS:

        dnscmd /zoneadd contoso.com  /Primary
        dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1
        dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM>
        dnscmd /config contoso.com /allowupdate 1

## <a name="next-steps"></a>Nästa steg
[Lär dig mer](site-recovery-workload.md) om hur du skyddar arbetsbelastningar på företag med Azure Site Recovery.
