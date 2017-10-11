---
title: Testa redundans (VMM till VMM) i Azure Site Recovery | Microsoft Docs
description: "Azure Site Recovery samordnar replikering, redundans och återställning av virtuella datorer och fysiska servrar. Läs mer om redundans till Azure eller ett sekundärt datacenter."
services: site-recovery
documentationcenter: 
author: prateek9us
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/05/2017
ms.author: pratshar
ms.openlocfilehash: afc4790d5714ce7145c8f4291a05acc2e9882a9b
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="test-failover-vmm-to-vmm-in-site-recovery"></a>Testa redundans (VMM till VMM) i Site Recovery


Den här artikeln innehåller information och instruktioner för att göra ett redundanstest eller visa disaster recovery (DR) av virtuella datorer (VM) och fysiska servrar som skyddas med Azure Site Recovery. Du ska använda en System Center Virtual Machine Manager VMM-hanterade lokal plats som återställningsplatsen.

Du kan köra ett redundanstest för att verifiera din replikeringsstrategi för eller utföra en DR-test utan avbrott eller dataförlust. Testa redundans har inte någon effekt på pågående replikering eller i din produktionsmiljö. Du kan köra den på en virtuell dator eller en [återställningsplanen](site-recovery-create-recovery-plans.md). När du utlöst ett redundanstest måste du ange det nätverk som de virtuella testdatorerna ska ansluta till. Du kan spåra förloppet för redundanstestningen på den **jobb** sidan.  

Om du har några kommentarer eller frågor kan du publicera dem längst ned i den här artikeln eller på den [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="prepare-the-infrastructure-for-test-failover"></a>Förbereda infrastrukturen för att testa redundans
Om du vill köra ett redundanstest genom att använda ett befintligt nätverk förbereda Active Directory, DHCP och DNS i nätverket.

Om du vill köra ett redundanstest med hjälp av alternativet för att skapa Virtuella nätverk automatiskt kan du lägga till en manuell åtgärd innan grupp 1 i återställningsplanen som du ska använda för redundanstestning. Sedan lägger du till infrastrukturresurser automatiskt skapade nätverket innan du kör redundanstestet.

### <a name="things-to-note"></a>Saker att Observera
När du replikerar till en sekundär plats, vilken typ av nätverk som använder den replikerade datorn behöver inte matchar typ av logiska nätverk som används för att testa redundans, men vissa kombinationer kanske inte fungerar. Om replikeringen använder DHCP- och VLAN-baserad isolering, behöver det Virtuella datornätverket för repliken inte en statisk IP-adresspool. Det med hjälp av Windows-Nätverksvirtualisering för att testa redundans fungerar inte eftersom ingen adresspool är tillgängliga. 

Dessutom fungerar redundanstestningen inte om repliken nätverket använder ingen isolering och testnätverket använder Windows-Nätverksvirtualisering. Det beror på att ingen isolering nätverket saknar undernät som krävs för att skapa ett nätverk för Windows-Nätverksvirtualisering.

Hur replikerade virtuella datorerna är anslutna till mappade VM-nätverk efter växling vid fel som beror på hur det Virtuella datornätverket har konfigurerats i VMM-konsolen.

#### <a name="vm-network-configured-with-no-isolation-or-vlan-isolation"></a>Virtuellt datornätverk utan isolering eller VLAN-isolering
Om DHCP har definierats för det Virtuella datornätverket, är den replikerade virtuella datorn ansluten till VLAN-ID via inställningarna som har angetts för nätverksplatsen i det associerade logiska nätverket. Den virtuella datorn får sin IP-adress från DHCP-servern finns. 

Du behöver inte definiera en statisk IP-adresspool för det Virtuella datornätverket mål. Om du använder en statisk IP-adresspool för det Virtuella datornätverket, är den replikerade virtuella datorn ansluten till VLAN-ID via inställningarna som har angetts för nätverksplatsen i det associerade logiska nätverket.

Den virtuella datorn får sin IP-adress från den pool som har definierats för det Virtuella datornätverket. Om en statisk IP-adresspool har inte definierats i VM målnätverket, misslyckas IP-adressallokering. Skapa IP-adresspool på både käll- och VMM-servrar som du ska använda för skydd och återställning.

#### <a name="vm-network-with-windows-network-virtualization"></a>Virtuellt datornätverk med Nätverksvirtualisering i Windows
Om ett Virtuellt datornätverk är konfigurerad med Windows-Nätverksvirtualisering, bör du definiera en statisk adresspool för VM målnätverket, oavsett om VM-källnätverket är konfigurerad för att använda DHCP eller en statisk IP-adresspool. 

Om du definierar DHCP, VMM-målservern fungerar som en DHCP-server och tillhandahåller en IP-adress från den pool som har definierats för det Virtuella datornätverket mål. Om användning av en statisk IP-adresspool har definierats för källservern, allokerar en IP-adress i VMM-målservern från poolen. I båda fallen misslyckas IP-adressallokering om en statisk IP-adresspool inte har definierats.


### <a name="prepare-dhcp"></a>Förbereda DHCP
Om de virtuella datorerna som är involverad i testa redundans använda DHCP, skapa en test DHCP-server i det isolerade nätverket för att testa redundans.

### <a name="prepare-active-directory"></a>Förbereda Active Directory
Om du vill köra ett redundanstest för program, testa, måste en kopia av Active Directory produktionsmiljön i din testmiljö. Mer information finns i [redundanstestning för Active Directory](site-recovery-active-directory.md#test-failover-considerations).

### <a name="prepare-dns"></a>Förbereda DNS
Förbered en DNS-server för att testa redundans på följande sätt:

* **DHCP**: om virtuella datorer använder DHCP IP-adressen för testet DNS ska uppdateras på test DHCP-servern. Om du använder en nätverkstyp för Windows-Nätverksvirtualisering, fungerar VMM-servern som DHCP-servern. IP-adressen för DNS ska vara uppdaterade i nätverket. I det här fallet registrera de virtuella datorerna sig själva till relevanta DNS-servern.
* **Statisk adress**: om virtuella datorer använder en statisk IP-adress, IP-adressen för DNS-testserver ska uppdateras i nätverket. Du kan behöva uppdatera DNS med IP-adressen för de virtuella testdatorerna. Du kan använda följande exempelskript för detta ändamål:

        Param(
        [string]$Zone,
        [string]$name,
        [string]$IP
        )
        $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
        $newrecord = $record.clone()
        $newrecord.RecordData[0].IPv4Address  =  $IP
        Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord



## <a name="run-a-test-failover"></a>Köra ett redundanstest
Den här proceduren beskriver hur du kör ett redundanstest för en återställningsplan. Du kan också köra redundans för en enskild virtuell dator den **virtuella datorer** fliken.

![Testa redundans-bladet](./media/site-recovery-test-failover-vmm-to-vmm/TestFailover.png)

1. Välj **Återställningsplaner** > *recoveryplan_name*. Klicka på **redundans** > **Redundanstestningen**.
1. På den **Redundanstestning** bladet ange hur virtuella datorer måste vara anslutna till nätverk efter redundanstestet. Mer information finns i [alternativ](#network-options-in-site-recovery).
1. Följa redundansförloppet på den **jobb** fliken.
1. När redundansväxlingen är klar kontrollerar du att de virtuella datorerna startas.
1. När du är klar klickar du på **Rensa redundanstestet** på återställningsplanen. I **anteckningar**, registrera och spara observationer från redundanstestningen. Det här steget tar bort de virtuella datorer och nätverk som har skapats under redundanstestningen.


## <a name="network-options-in-site-recovery"></a>Nätverksalternativ i Site Recovery

När du kör ett redundanstest, uppmanas du att välja nätverksinställningar för replik testdatorer. Du har flera alternativ.  

| **Testa redundans alternativet** | **Beskrivning** | **Kontrollera redundans** | **Detaljer** |
| --- | --- | --- | --- |
| **Växla över till en sekundär VMM-plats--utan nätverk** |Markera inte ett Virtuellt datornätverk. |Växling vid fel kontrollerar du att testdatorer skapas.<br/><br/>Den virtuella testdatorn har skapats på värden där den replikerade virtuella datorn finns. Det är inte lägga till det moln där den virtuella replikdatorn finns. |<p>Misslyckades-over-datorn är inte ansluten till något nätverk.<br/><br/>Datorn kan vara ansluten till ett Virtuellt datornätverk när den har skapats. |
| **Växla över till en sekundär VMM-plats--med nätverk** |Välj ett befintligt Virtuellt datornätverk. |Växling vid fel kontrollerar du att virtuella datorer skapas. |Den virtuella testdatorn har skapats på värden där den replikerade virtuella datorn finns. Det är inte lägga till det moln där den virtuella replikdatorn finns.<br/><br/>Skapa ett Virtuellt datornätverk som är isolerat från produktionsnätverket.<br/><br/>Om du använder ett VLAN-baserade nätverk, rekommenderar vi att du skapar ett separat logiskt nätverk (används inte i produktion) i VMM för detta ändamål. Det här logiska nätverket används för att skapa Virtuella datornätverk för redundanstestning.<br/><br/>Det logiska nätverket ska kopplas till minst ett nätverkskort för alla Hyper-V-servrar som är värd för virtuella datorer.<br/><br/>För VLAN logiska nätverk, ska nätverksplatser som du lägger till det logiska nätverket isoleras.<br/><br/>Om du använder ett Windows-Nätverksvirtualisering – logiska nätverk med skapar Azure Site Recovery automatiskt isolerade Virtuella nätverk. |
| **Växla över till en sekundär plats i VMM – skapa ett nätverk** |En tillfällig testnätverket skapas automatiskt utifrån den inställning som du anger i **logiskt nätverk** och dess relaterade nätverksplatser. |Växling vid fel kontrollerar du att virtuella datorer skapas. |Använd det här alternativet om återställningsplanen använder mer än ett Virtuellt datornätverk. Om du använder Windows nätverksvirtualiseringsnätverk kan det här alternativet automatiskt skapa Virtuella datornätverk med samma inställningar (undernät och IP-adresspooler) i nätverket för den virtuella replikdatorn. Dessa Virtuella datornätverk rensas automatiskt när du testar redundansen är klar.</p><p>Den virtuella testdatorn har skapats på värden där den replikerade virtuella datorn finns. Det är inte lägga till det moln där den virtuella replikdatorn finns. |

> [!TIP]
> IP-adressen som angavs till en virtuell dator under testning av redundans är samma IP-adress som den virtuella datorn kommer att ta emot en planerad eller oplanerad växling vid fel (förutsatt att IP-adress är tillgänglig i nätverket). Om samma IP-adress är inte tillgänglig i nätverket får den virtuella datorn en annan IP-adress som är tillgänglig i nätverket.
>
>


## <a name="test-failover-to-a-production-network-on-a-recovery-site"></a>Testa redundans för ett produktionsnätverk på en återställningsplats
Vi rekommenderar att du väljer ett nätverk som skiljer sig från nätverket produktion recovery plats som du angav i när du utför ett redundanstest [nätverksmappning](https://docs.microsoft.com/azure/site-recovery/site-recovery-network-mapping). Men om du vill validera nätverksanslutningen från slutpunkt till slutpunkt på en virtuell dator misslyckades över, notera följande:

* Kontrollera att den primära virtuella datorn är avstängd när du utför redundanstestet. Om du inte kör två virtuella datorer med samma identitet i samma nätverk på samma gång. Denna situation kan leda till oönskade konsekvenser.
* Alla ändringar som du gör i virtuella testdatorer redundans försvinner när du rensa virtuella testdatorer växling vid fel. Dessa ändringar replikeras inte till den primära virtuella datorn.
* Det här kan du göra testning leder till avbrott för ditt produktionsprogram. Be användare av programmet inte att använda programmet när DR-test pågår.  


## <a name="next-steps"></a>Nästa steg
När du har kört ett redundanstest kan du prova en [redundans](site-recovery-failover.md).
