---
title: Kör en DR-test av Hyper-V-datorer till en sekundär plats med Azure Site Recovery | Microsoft Docs
description: Lär dig hur du kör en DR-test för Hyper-V-datorer i VMM-moln till ett sekundärt datacenter med Azure Site Recovery.
services: site-recovery
author: ponatara
manager: abhemraj
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: ponatara
ms.openlocfilehash: 03533af27ac6fd406b4639c31c3add0015a76f45
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2018
ms.locfileid: "37918806"
---
# <a name="run-a-dr-drill-for-hyper-v-vms-to-a-secondary-site"></a>Köra en DR-test för Hyper-V-datorer till en sekundär plats


Den här artikeln beskriver hur du gör ett programåterställningstest (DR) för Hyper-V-datorer som hanteras i System Center Virtual Machine Manager V(MM)-moln till en sekundär lokal plats med hjälp av [Azure Site Recovery](site-recovery-overview.md).

Du kör ett redundanstest för att verifiera din replikeringsstrategi och utföra en DR-test utan någon dataförlust eller driftstopp. Ett redundanstest har inte någon inverkan på den pågående replikeringen eller i din produktionsmiljö. 

## <a name="how-do-test-failovers-work"></a>Hur testa redundans fungerar?

Du kan köra ett redundanstest från primärt till den sekundära platsen. Om du vill kontrollera att en virtuell dator växlar över kan köra du ett redundanstest utan att ställa in allt på den sekundära platsen. Om du vill kontrollera app redundans fungerar som förväntat, kommer du behöva konfigurera nätverk och infrastruktur på den sekundära platsen.
- Du kan köra ett redundanstest på en enskild virtuell dator eller på en [återställningsplanen](site-recovery-create-recovery-plans.md).
- Du kan köra ett redundanstest utan ett nätverk med ett befintligt nätverk eller med ett virtuellt nätverk skapas automatiskt. Mer information om alternativen finns i tabellen nedan.
    - Du kan köra ett redundanstest utan ett nätverk. Det här alternativet är användbart om du bara vill kontrollera att en virtuell dator har kunnat växla över, men du kan inte verifiera någon nätverkskonfiguration behövs.
    - Kör redundans med ett befintligt nätverk. Vi rekommenderar att du inte använder ett produktionsnätverk.
    - Köra redundans och låt Site Recovery automatiskt skapa ett testnätverk. I det här fallet kommer Site Recovery skapa nätverket automatiskt och rensa när testningen är klar.
- Du måste välja en återställningspunkt för att testa redundans: 
    - **Senaste bearbetade**: det här alternativet redundansväxlar en virtuell dator till den senaste återställningspunkten som bearbetats av Site Recovery. Med det här alternativet läggs ingen tid på bearbetning av data, så den ger ett lågt mål för återställningstiden.
    - **Senaste appkonsekventa**: det här alternativet redundansväxlas en virtuell dator till den senaste programkonsekventa återställningspunkten som bearbetats av Site Recovery. 
    - **Senaste**: det här alternativet bearbetar först alla data som har skickats till Site Recovery-tjänsten för att skapa en återställningspunkt för varje virtuell dator innan växling till den. Det här alternativet ger den lägsta RPO (mål för återställningspunkt), eftersom den virtuella datorn skapas efter redundans har alla data som replikeras till Site Recovery när redundansen utlöstes.
    - **Senaste multi-VM bearbetas**: tillgänglig för återställningsplaner som innehåller en eller flera virtuella datorer som har konsekvens aktiverat. Virtuella datorer med inställningen aktiverad växlar över till den senaste vanliga Konsekvens programkonsekvent återställningspunkten. Andra virtuella datorer som växlar över till den senaste bearbetade återställningspunkten.
    - **Senaste multi-VM appkonsekvent**: det här alternativet är tillgängligt för återställningsplaner med en eller flera virtuella datorer som har konsekvens aktiverat. Virtuella datorer som ingår i en replikeringsgrupp växlar över till den senaste vanliga Konsekvens programkonsekventa återställningspunkten. Andra virtuella datorer som växlar över till sina senaste programkonsekventa återställningspunkten.
    - **Anpassad**: Använd det här alternativet för att växla över en specifik virtuell dator till en specifik återställningspunkt.



## <a name="prepare-networking"></a>Förbereda nätverk

När du kör ett redundanstest, är du uppmanas att välja nätverksinställningar för test replikerade datorer som sammanfattas i tabellen.

**Alternativ** | **Detaljer** 
--- | --- 
**Ingen** | Den Virtuella testdatorn har skapats på den värd där den Virtuella replikdatorn finns. Den har lagts till inte i molnet och är inte ansluten till något nätverk.<br/><br/> Du kan ansluta datorn till ett Virtuellt datornätverk när den har skapats.
**Använd befintlig** | Den Virtuella testdatorn har skapats på den värd där den Virtuella replikdatorn finns. Inte läggs den till molnet.<br/><br/>Skapa ett Virtuellt datornätverk som är isolerat från produktionsnätverket.<br/><br/>Om du använder en VLAN-nätverk, rekommenderar vi att du skapar ett separat logiskt nätverk (används inte i produktion) i VMM för detta ändamål. Det här logiska nätverket används för att skapa VM-nätverk för redundanstestning.<br/><br/>Det logiska nätverket ska associeras med minst en av nätverkskort på alla Hyper-V-servrar som är värdar för virtuella datorer.<br/><br/>VLAN logiska nätverk, bör nätverksplatser som du lägger till det logiska nätverket vara isolerade.<br/><br/>Om du använder ett logiskt nätverk för Nätverksvirtualisering i Windows-baserade skapar Azure Site Recovery automatiskt isolerade Virtuella datornätverk. 
**Skapa ett nätverk** | Ett tillfälligt testnätverk skapas automatiskt baserat på den inställning som du anger i **logiskt nätverk** och dess relaterade nätverksplatser.<br/><br/> Redundans kontrollerar att virtuella datorer skapas. |Du bör använda det här alternativet om en återställningsplan använder mer än ett Virtuellt datornätverk.<br/><br/> Om du använder Windows nätverksvirtualiseringsnätverk kan det här alternativet automatiskt skapa Virtuella datornätverk med samma inställningar (undernät och IP-adresspooler) i nätverket för den replikerade virtuella datorn. Dessa Virtuella datornätverk rensas automatiskt när du testar redundansen är klar.<br/><br/> Testet virtuella datorn skapas på värden där den replikerade virtuella datorn finns. Inte läggs den till molnet.

### <a name="best-practices"></a>Bästa praxis

- Testa ett produktionsnätverk orsakar driftstopp för produktionsarbetsbelastningar. Be dina användare inte ska använda relaterade appar när programåterställningstest pågår.

- Testnätverket behöver inte matchar inte VMM logiskt nätverk för redundanstestning. Men fungerar inte för vissa kombinationer:

     - Om repliken använder DHCP- och VLAN-baserad isolering, behöver det Virtuella datornätverket för repliken inte en statisk IP-adresspool. Det med hjälp av Windows-Nätverksvirtualisering för att testa redundans fungerar inte eftersom ingen-adresspooler som är tillgängliga. 
        
     - Testa redundans fungerar inte om repliken nätverket använder ingen isolering och testnätverket använder Windows-Nätverksvirtualisering. Det beror på att typen no-isolation nätverket saknar undernät som krävs för att skapa ett nätverk för Windows-Nätverksvirtualisering.
        
- Vi rekommenderar att du inte använder det nätverket som du valt för nätverksmappning för att testa redundans.

- Hur replikering av virtuella datorer är anslutna till den mappade Virtuella datornätverk efter växling vid fel som beror på hur det Virtuella datornätverket har konfigurerats i VMM-konsolen.


### <a name="vm-network-configured-with-no-isolation-or-vlan-isolation"></a>Virtuellt datornätverk utan isolering eller VLAN-isolering

Om ett Virtuellt datornätverk har konfigurerats i VMM utan isolering eller VLAN-isolering, Tänk på följande:

- Om DHCP har definierats för det Virtuella datornätverket använder är den replikerade virtuella datorn ansluten till VLAN-ID via inställningarna som angetts för nätverksplatsen i det associerade logiska nätverket. Den virtuella datorn tar emot dess IP-adress från de tillgängliga DHCP-server.
- Du behöver inte definiera en statisk IP-adresspool för det Virtuella datornätverket mål. Om du använder en statisk IP-adresspool för det Virtuella datornätverket, är den replikerade virtuella datorn ansluten till VLAN-ID via inställningarna som angetts för nätverksplatsen i det associerade logiska nätverket.
- Den virtuella datorn tar emot dess IP-adress från poolen som har definierats för det Virtuella datornätverket. Om en statisk IP-adresspool inte är definierad i det Virtuella datornätverket mål, misslyckas IP-adressallokering. Skapa IP-adresspoolen på både käll- och VMM-servrar som du ska använda för skydd och återställning.

### <a name="vm-network-with-windows-network-virtualization"></a>Virtuellt datornätverk med Nätverksvirtualisering i Windows

Om ett Virtuellt datornätverk har konfigurerats i VMM med Windows-Nätverksvirtualisering, Tänk på följande:

- Du bör definiera en statisk adresspool för Målnätverk för virtuella datorer, oavsett om VM-källnätverket är konfigurerad för att använda DHCP eller en statisk IP-adresspool. 
- Om du definierar DHCP, VMM-målservern fungerar som en DHCP-server och tillhandahåller en IP-adress från poolen som har definierats för det Virtuella datornätverket mål.
- Om användning av en statisk IP-adresspool har definierats för källservern, tilldelar VMM-målservern en IP-adress från poolen. I båda fallen misslyckas IP-adressallokering om en statisk IP-adresspool inte har definierats.



## <a name="prepare-the-infrastructure"></a>Förbered infrastrukturen

Om du vill kontrollera att en virtuell dator kan växla över kan köra du ett redundanstest utan någon infrastruktur. Om du vill göra en fullständig DR-test för att testa redundans för app måste du förbereda infrastrukturen på den sekundära platsen:

- Om du kör ett redundanstest med ett befintligt nätverk kan du förbereda Active Directory, DHCP och DNS i nätverket.
- Om du kör ett redundanstest med alternativet för att skapa ett Virtuellt datornätverk automatiskt, måste du lägga till resurser i infrastrukturen till det automatiskt skapade nätverket innan du kör redundanstestet. I en återställningsplan kan du förenkla detta genom att lägga till en manuell åtgärd innan grupp 1 i återställningsplanen som du ska använda för att testa redundans. Lägg sedan till infrastrukturresurser till det automatiskt skapade nätverket innan du kör redundanstestet.


### <a name="prepare-dhcp"></a>Förbereda DHCP
Om de virtuella datorerna som ingår i redundanstest använda DHCP, skapa en DHCP-server för test i det isolerade nätverket i syfte att testa redundans.


### <a name="prepare-active-directory"></a>Förbered Active Directory
Om du vill köra ett redundanstest för program som testar behöver du en kopia av Active Directory-miljö för produktion i din testmiljö. Mer information finns i [testa tänka på vid för Active Directory](site-recovery-active-directory.md#test-failover-considerations).

### <a name="prepare-dns"></a>Förbereda DNS
Förbered en DNS-server för att testa redundans på följande sätt:

* **DHCP**: om virtuella datorer använder DHCP IP-adressen för testet DNS ska uppdateras på test DHCP-servern. Om du använder en nätverkstyp för Windows-Nätverksvirtualisering, fungerar VMM-servern som DHCP-servern. IP-adressen för DNS-bör därför uppdateras i nätverket för redundanstestet. I det här fallet registrera de virtuella datorerna sig själva till relevanta DNS-servern.
* **Statisk adress**: om virtuella datorer använder en statisk IP-adress, IP-adressen för DNS-server för test ska uppdateras i nätverket för redundanstestet. Du kan behöva uppdatera DNS med IP-adressen för virtuella datorer för testning. Du kan använda följande exempelskript för detta ändamål:

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

1. Välj **Återställningsplaner** > *recoveryplan_name*. Klicka på **redundans** > **Redundanstest**.
2. På den **Redundanstestning** bladet anger du hur replikering av VMs ska vara anslutna till nätverk efter redundanstestet.
3. Följa redundansförloppet på den **jobb** fliken.
4. När redundansväxlingen är klar kan du kontrollera att de virtuella datorerna startas.
5. När du är klar klickar du på **Rensa redundanstestning** på återställningsplanen. I **Kommentarer** skriver du och sparar eventuella observationer från redundanstestningen. Det här steget tar bort alla virtuella datorer och nätverk som har skapats av Site Recovery under redundanstestningen. 

![Testa redundans](./media/hyper-v-vmm-test-failover/TestFailover.png)
 


> [!TIP]
> IP-adressen som ges till en virtuell dator under redundanstest är samma IP-adress som den virtuella datorn ska få för en planerad eller oplanerad redundansväxling (förutsatt att den IP-adressen är tillgänglig i nätverket för redundanstestet). Om samma IP-adress inte är tillgänglig i nätverket för redundanstestet, får den virtuella datorn en annan IP-adress som är tillgänglig i nätverket för redundanstestet.



### <a name="run-a-test-failover-to-a-production-network"></a>Köra ett redundanstest till ett produktionsnätverk

Vi rekommenderar att du inte kör ett redundanstest till nätverket produktion recovery plats som du angav under nätverksmappning. Men om du behöver att verifiera nätverksanslutningar från slutpunkt till slutpunkt i en redundansväxlade virtuella datorn, Tänk på följande:

* Kontrollera att den primära virtuella datorn stängs av när du gör redundanstestningen. Om du inte köra två virtuella datorer med samma identitet i samma nätverk på samma gång. Denna situation kan leda till oönskade konsekvenser.
* Alla ändringar du gör redundanstestningen virtuella datorer går förlorade när du rensa virtuella testdatorer redundans. Dessa ändringar replikeras inte till de primära virtuella datorerna.
* Testa så här leder till driftstopp för dina produktionsprogram. Be användare av programmet inte ska använda programmet när DR-test pågår.  


## <a name="next-steps"></a>Nästa steg
När du har kört en DR-test, kan du [köra en fullständig redundans](site-recovery-failover.md).



