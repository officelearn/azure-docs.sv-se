---
title: Kör en DR-test för Hyper-V virtuella datorer till en sekundär plats med hjälp av Azure Site Recovery | Microsoft Docs
description: Lär dig hur du kör en DR-test för Hyper-V virtuella datorer i VMM-moln till ett sekundärt datacenter med hjälp av Azure Site Recovery.
services: site-recovery
author: ponatara
manager: abhemraj
ms.service: site-recovery
ms.topic: article
ms.date: 02/12/2018
ms.author: ponatara
ms.openlocfilehash: c389776f62db5fd04f67ef22822e21fd4aee368f
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
---
# <a name="run-a-dr-drill-for-hyper-v-vms-to-a-secondary-site"></a>Kör en DR-test för Hyper-V virtuella datorer till en sekundär plats


Den här artikeln beskriver hur du gör en katastrof-återställningsgranskning (DR) för Hyper-V virtuella datorer som hanteras i System Center Virtual Machine Manager V(MM) moln till en sekundär lokal plats med hjälp av [Azure Site Recovery](site-recovery-overview.md).

Du kör ett redundanstest för att verifiera din replikeringsstrategi för och utför en DR-test utan avbrott eller dataförlust. Testa redundans har inte någon effekt på pågående replikering eller i din produktionsmiljö. 

## <a name="how-do-test-failovers-work"></a>Hur testa redundans arbete?

Du kan köra ett redundanstest från den primära servern till den sekundära platsen. Om du vill kontrollera att en virtuell dator växlar kan köra du ett redundanstest utan att ställa in något på den sekundära platsen. Om du vill verifiera app redundans fungerar som förväntat, måste du ställer in nätverk och infrastruktur på den sekundära platsen.
- Du kan köra ett redundanstest på en enda virtuell dator eller på en [återställningsplanen](site-recovery-create-recovery-plans.md).
- Du kan köra ett redundanstest utan ett nätverk med ett befintligt nätverk, eller med ett automatiskt skapade nätverk. Mer information om alternativen finns i tabellen nedan.
    - Du kan köra ett redundanstest utan ett nätverk. Det här alternativet är användbart om du vill kontrollera att en virtuell dator har kunnat växla över, men du inte kan verifiera några nätverkskonfigurationen.
    - Kör redundans med ett befintligt nätverk. Vi rekommenderar att du inte använder ett produktionsnätverk.
    - Kör växling vid fel och kan Site Recovery automatiskt skapa ett testnätverk. I det här fallet Site Recovery skapar nätverket automatiskt, och rensa när redundanstestet har slutförts.
- Du måste välja en återställningspunkt för att testa redundans: 
    - **Senaste bearbetas**: det här alternativet växlar en virtuell dator till den senaste återställningspunkten som bearbetas av Site Recovery. Med det här alternativet läggs ingen tid på bearbetning av data, så den ger ett lågt mål för återställningstiden.
    - **Senaste programkonsekventa**: det här alternativet redundansväxlas en virtuell dator till den senaste programkonsekventa återställningspunkten bearbetas av Site Recovery. 
    - **Senaste**: det här alternativet först bearbetar alla data som har skickats till Site Recovery-tjänsten för att skapa en återställningspunkt för varje virtuell dator innan åtgärden misslyckas över till den. Det här alternativet ger den lägsta RPO (mål för återställningspunkt), eftersom den virtuella datorn skapas efter växling vid fel har alla data som replikeras till Site Recovery när redundans utlöstes.
    - **Senaste multi-VM bearbetas**: tillgänglig för återställningsplaner som innehåller en eller flera virtuella datorer som har aktiverats för flera Virtuella datorer. Virtuella datorer med inställningen aktiverad växlas över till den senaste vanliga multi-VM konsekventa återställningspunkten. Andra virtuella datorer som växlar över till den senaste bearbetade återställningspunkten.
    - **Senaste multi-VM programkonsekventa**: det här alternativet är tillgängligt för återställningsplaner med en eller flera virtuella datorer som har aktiverats för flera Virtuella datorer. Virtuella datorer som tillhör en replikeringsgrupp växlas över till den senaste vanliga multi-VM programkonsekventa återställningspunkten. Andra virtuella datorer som växlar över till sina senaste programkonsekventa återställningspunkten.
    - **Anpassad**: Använd det här alternativet för att växla över en specifik VM till en viss återställningspunkt.



## <a name="prepare-networking"></a>Förbereda nätverk

När du kör ett redundanstest uppmanas du att välja nätverksinställningar för test replikdatorer som sammanfattas i tabellen.

**Alternativet** | **Detaljer** 
--- | --- 
**Ingen** | Den Virtuella testdatorn har skapats på den värd som replikerade virtuella datorn finns. Det är inte lägga till i molnet och är inte ansluten till något nätverk.<br/><br/> Du kan ansluta datorn till ett Virtuellt datornätverk när den har skapats.
**Använd befintlig** | Den Virtuella testdatorn har skapats på den värd som replikerade virtuella datorn finns. Det är inte lägga till i molnet.<br/><br/>Skapa ett Virtuellt datornätverk som är isolerat från produktionsnätverket.<br/><br/>Om du använder ett VLAN-baserade nätverk, rekommenderar vi att du skapar ett separat logiskt nätverk (används inte i produktion) i VMM för detta ändamål. Det här logiska nätverket används för att skapa Virtuella datornätverk för redundanstestning.<br/><br/>Det logiska nätverket ska kopplas till minst ett nätverkskort för alla Hyper-V-servrar som är värd för virtuella datorer.<br/><br/>För VLAN logiska nätverk, ska nätverksplatser som du lägger till det logiska nätverket isoleras.<br/><br/>Om du använder ett Windows-Nätverksvirtualisering – logiska nätverk med skapar Azure Site Recovery automatiskt isolerade Virtuella nätverk. 
**Skapa ett nätverk** | En tillfällig testnätverket skapas automatiskt utifrån den inställning som du anger i **logiskt nätverk** och dess relaterade nätverksplatser.<br/><br/> Växling vid fel kontrollerar du att virtuella datorer skapas. |Du bör använda det här alternativet om en återställningsplan använder mer än ett Virtuellt datornätverk.<br/><br/> Om du använder Windows nätverksvirtualiseringsnätverk kan det här alternativet automatiskt skapa Virtuella datornätverk med samma inställningar (undernät och IP-adresspooler) i nätverket för den virtuella replikdatorn. Dessa Virtuella datornätverk rensas automatiskt när du testar redundansen är klar.<br/><br/> Testet virtuell dator skapas på värden där den replikerade virtuella datorn finns. Det är inte lägga till i molnet.

### <a name="best-practices"></a>Bästa praxis

- Testa ett produktionsnätverk orsakar driftstopp för produktionsarbetsbelastningar. Be användarna inte att använda relaterade appar när disaster recovery ökad pågår.

- Testnätverket behöver inte matcha typ av VMM logiskt nätverk som används för att testa redundans. Men vissa kombinationer fungerar inte:

     - Om replikeringen använder DHCP- och VLAN-baserad isolering, behöver det Virtuella datornätverket för repliken inte en statisk IP-adresspool. Det med hjälp av Windows-Nätverksvirtualisering för att testa redundans fungerar inte eftersom ingen adresspool är tillgängliga. 
        
     - Testa redundans fungerar inte om repliken nätverket använder ingen isolering och testnätverket använder Windows-Nätverksvirtualisering. Det beror på att ingen isolering nätverket saknar undernät som krävs för att skapa ett nätverk för Windows-Nätverksvirtualisering.
        
- Vi rekommenderar att du inte använder det nätverk som du har valt för nätverksmappning för att testa redundans.

- Hur replikerade virtuella datorerna är anslutna till mappade VM-nätverk efter växling vid fel som beror på hur det Virtuella datornätverket har konfigurerats i VMM-konsolen.


### <a name="vm-network-configured-with-no-isolation-or-vlan-isolation"></a>Virtuellt datornätverk utan isolering eller VLAN-isolering

Om ett VM-nätverk har konfigurerats i VMM utan isolering eller VLAN-isolering, Tänk på följande:

- Om DHCP har definierats för det Virtuella datornätverket, är den replikerade virtuella datorn ansluten till VLAN-ID via inställningarna som har angetts för nätverksplatsen i det associerade logiska nätverket. Den virtuella datorn får sin IP-adress från DHCP-servern finns.
- Du behöver inte definiera en statisk IP-adresspool för det Virtuella datornätverket mål. Om du använder en statisk IP-adresspool för det Virtuella datornätverket, är den replikerade virtuella datorn ansluten till VLAN-ID via inställningarna som har angetts för nätverksplatsen i det associerade logiska nätverket.
- Den virtuella datorn får sin IP-adress från den pool som har definierats för det Virtuella datornätverket. Om en statisk IP-adresspool har inte definierats i VM målnätverket, misslyckas IP-adressallokering. Skapa IP-adresspool på både käll- och VMM-servrar som du ska använda för skydd och återställning.

### <a name="vm-network-with-windows-network-virtualization"></a>Virtuellt datornätverk med Nätverksvirtualisering i Windows

Om ett VM-nätverk har konfigurerats i VMM med Windows-Nätverksvirtualisering, Tänk på följande:

- Du bör ange en statisk adresspool för VM målnätverket, oavsett om VM-källnätverket är konfigurerad för att använda DHCP eller en statisk IP-adresspool. 
- Om du definierar DHCP, VMM-målservern fungerar som en DHCP-server och tillhandahåller en IP-adress från den pool som har definierats för det Virtuella datornätverket mål.
- Om användning av en statisk IP-adresspool har definierats för källservern, allokerar en IP-adress i VMM-målservern från poolen. I båda fallen misslyckas IP-adressallokering om en statisk IP-adresspool inte har definierats.



## <a name="prepare-the-infrastructure"></a>Förbered infrastrukturen

Om du vill kontrollera att en virtuell dator kan växla över kan köra du ett redundanstest utan någon infrastruktur. Om du vill göra en fullständig DR-test för att testa appen växling vid fel, måste du förbereda infrastrukturen på den sekundära platsen:

- Om du kör ett redundanstest med ett befintligt nätverk, förbereda Active Directory, DHCP och DNS i nätverket.
- Om du kör ett redundanstest med alternativet för att automatiskt skapa ett Virtuellt datornätverk som du behöver lägga till infrastrukturresurser till nätverkets automatiskt skapade innan du kör redundanstestet. Du kan underlätta detta genom att lägga till en manuell åtgärd innan grupp 1 i återställningsplanen som du ska använda för att testa redundans i en återställningsplan. Sedan lägger du till infrastrukturresurser automatiskt skapade nätverket innan du kör redundanstestet.


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

1. Välj **Återställningsplaner** > *recoveryplan_name*. Klicka på **redundans** > **Redundanstestningen**.
2. På den **Redundanstestning** bladet anger hur replikering VMs ska vara anslutna till nätverk efter redundanstestet.
3. Följa redundansförloppet på den **jobb** fliken.
4. När redundansväxlingen är klar kontrollerar du att de virtuella datorerna startas.
5. När du är klar klickar du på **Rensa redundanstestet** på återställningsplanen. I **Kommentarer** skriver du och sparar eventuella observationer från redundanstestningen. Det här steget tar bort alla virtuella datorer och nätverk som har skapats av Site Recovery under redundanstestningen. 

![Testa redundans](./media/hyper-v-vmm-test-failover/TestFailover.png)
 


> [!TIP]
> IP-adressen som angavs till en virtuell dator under testning av redundans är samma IP-adress som den virtuella datorn kommer att ta emot en planerad eller oplanerad växling vid fel (förutsatt att IP-adress är tillgänglig i nätverket). Om samma IP-adress är inte tillgänglig i nätverket får den virtuella datorn en annan IP-adress som är tillgänglig i nätverket.



### <a name="run-a-test-failover-to-a-production-network"></a>Köra ett redundanstest för ett produktionsnätverk

Vi rekommenderar att du inte vill köra ett redundanstest till nätverket produktion recovery plats som du angav under nätverksmappning. Men om du behöver verifiera nätverksanslutning från slutpunkt till slutpunkt på en virtuell dator misslyckades över Observera följande:

* Kontrollera att den primära virtuella datorn är avstängd när du utför redundanstestet. Om du inte kör två virtuella datorer med samma identitet i samma nätverk på samma gång. Denna situation kan leda till oönskade konsekvenser.
* Alla ändringar du gör att du testar redundansen VMs försvinner när du rensa virtuella testdatorer växling vid fel. Dessa ändringar replikeras inte till de primära virtuella datorerna.
* Så här testar leder till avbrott för ditt produktionsprogram. Be användare av programmet inte att använda programmet när DR-test pågår.  


## <a name="next-steps"></a>Nästa steg
När du har kört en DR-test, kan du [kör en fullständig redundans](site-recovery-failover.md).



