---
title: Kör en NHyper-V-haveriberedskapsövning till en sekundär plats med Azure Site Recovery
description: Lär dig hur du kör en DR-borr för virtuella hyper-V-datorer i VMM-moln till ett sekundärt lokalt datacenter med Azure Site Recovery.
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: 0363911574a076b13cb72591fb2564364e096c76
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257971"
---
# <a name="run-a-dr-drill-for-hyper-v-vms-to-a-secondary-site"></a>Köra en DR-borr för virtuella hyper-v-datorer till en sekundär plats


I den här artikeln beskrivs hur du utför en DR-övning (Disaster Recovery) för virtuella hyper-virtuella datorer som hanteras i V(MM-moln för System Center Virtual Machine Manager) till en sekundär lokal plats med hjälp av [Azure Site Recovery](site-recovery-overview.md).

Du kör en testväxling för att validera replikeringsstrategin och utföra en DR-övning utan dataförlust eller driftstopp. En testundanställning påverkar inte den pågående replikeringen eller på produktionsmiljön. 

## <a name="how-do-test-failovers-work"></a>Hur fungerar testrundans?

Du kör en testundanhandundanställning från den primära till den sekundära platsen. Om du bara vill kontrollera att en virtuell dator växlar över, kan du köra en test redundans utan att ställa in något på den sekundära platsen. Om du vill verifiera att appväxling fungerar som förväntat måste du konfigurera nätverk och infrastruktur på den sekundära platsen.
- Du kan köra en testväxling på en enda virtuell dator eller på en [återställningsplan](site-recovery-create-recovery-plans.md).
- Du kan köra en testväxling utan ett nätverk, med ett befintligt nätverk eller med ett automatiskt skapat nätverk. Mer information om dessa alternativ finns i tabellen nedan.
    - Du kan köra en testväxling utan nätverk. Det här alternativet är användbart om du bara vill kontrollera att en virtuell dator kunde växla över, men du kan inte verifiera någon nätverkskonfiguration.
    - Kör redundansen med ett befintligt nätverk. Vi rekommenderar att du inte använder ett produktionsnätverk.
    - Kör redundansen och låt Site Recovery automatiskt skapa ett testnätverk. I det här fallet skapas nätverket automatiskt och rensar det när testundansen är klar.
- Du måste välja en återställningspunkt för test redundansen: 
    - **Senast bearbetad:** Det här alternativet misslyckas med en virtuell dator över till den senaste återställningspunkten som bearbetas av Site Recovery. Med det här alternativet läggs ingen tid på bearbetning av data, så den ger ett lågt mål för återställningstiden.
    - **Senaste app-konsekvent:** Det här alternativet växlar över en virtuell dator till den senaste programkonsekventa återställningspunkten som bearbetas av Site Recovery. 
    - **Senaste:** Det här alternativet bearbetar först alla data som har skickats till site recovery-tjänsten, för att skapa en återställningspunkt för varje virtuell dator innan den inte över till den. Det här alternativet ger den lägsta RPO (Recovery Point Objective), eftersom den virtuella datorn som skapats efter redundans kommer att ha alla data replikeras till Site Recovery när redundansen utlöstes.
    - **Senaste flera virtuella datorer som bearbetas:** Tillgänglig för återställningsplaner som innehåller en eller flera virtuella datorer som har konsekvens för flera virtuella datorer aktiverat. Virtuella datorer med inställningen aktiverad växla över till den senaste gemensamma konsekventa återställningspunkten för flera virtuella datorer. Andra virtuella datorer växlar över till den senaste bearbetade återställningspunkten.
    - **Senaste multi-VM app-konsekvent:** Det här alternativet är tillgängligt för återställningsplaner med en eller flera virtuella datorer som har multi-VM konsekvens aktiverat. Virtuella datorer som ingår i en replikeringsgrupp växlar över till den senaste vanliga återställningspunkten för flera virtuella datorer. Andra virtuella datorer växlar över till sin senaste programkonsekventa återställningspunkt.
    - **Anpassad:** Använd det här alternativet om du vill växla över en viss virtuell dator till en viss återställningspunkt.



## <a name="prepare-networking"></a>Förbereda nätverk

När du kör en testväxling uppmanas du att välja nätverksinställningar för testreplikdatorer, enligt summeringen i tabellen.

| **Alternativet** | **Detaljer** | |
| --- | --- | --- |
| **Inget** | Test-VM skapas på den värd där den virtuella repliken finns. Det läggs inte till i molnet och är inte anslutet till något nätverk.<br/><br/> Du kan ansluta datorn till ett VM-nätverk när den har skapats.| |
| **Använd befintliga** | Test-VM skapas på den värd där den virtuella repliken finns. Det läggs inte till i molnet.<br/><br/>Skapa ett VM-nätverk som är isolerat från produktionsnätverket.<br/><br/>Om du använder ett VLAN-baserat nätverk rekommenderar vi att du skapar ett separat logiskt nätverk (som inte används i produktion) i VMM för detta ändamål. Det här logiska nätverket används för att skapa VM-nätverk för test redundans.<br/><br/>Det logiska nätverket bör associeras med minst ett av nätverkskorten för alla Hyper-V-servrar som är värdar för virtuella datorer.<br/><br/>För virtuella VLAN-nätverk bör de nätverksplatser som du lägger till i det logiska nätverket isoleras.<br/><br/>Om du använder ett Windows Network Virtualization-baserat logiskt nätverk, azure site recovery automatiskt skapar isolerade VM-nätverk. | |
| **Skapa ett nätverk** | Ett tillfälligt testnätverk skapas automatiskt baserat på den inställning som du anger i **Logiskt nätverk** och dess relaterade nätverksplatser.<br/><br/> Redundanskontroller om att virtuella datorer skapas.<br/><br/> Du bör använda det här alternativet om en återställningsplan använder mer än ett vm-nätverk.<br/><br/> Om du använder Windows Network Virtualiseringsnätverk kan det här alternativet automatiskt skapa VM-nätverk med samma inställningar (undernät och IP-adresspooler) i nätverket för den virtuella datorn för repliken. Dessa VM-nätverk rensas automatiskt när testundansen är klar.<br/><br/> Test-VM skapas på den värd där den virtuella datorn för replik finns. Det läggs inte till i molnet.|

### <a name="best-practices"></a>Bästa praxis

- Om du testar ett produktionsnätverk kan du få driftstopp för produktionsarbetsbelastningar. Be användarna att inte använda relaterade appar när haveriberedskapsövningen pågår.

- Testnätverket behöver inte matcha den logiska nätverkstypen VMM som används för test redundans. Men vissa kombinationer fungerar inte:

     - Om repliken använder DHCP- och VLAN-baserad isolering behöver vm-nätverket för repliken inte en statisk IP-adresspool. Så att använda Windows Network Virtualization för test redundans kommer inte att fungera eftersom inga adresspooler är tillgängliga. 
        
     - Test failover fungerar inte om repliknätverket inte använder någon isolering och testnätverket använder Windows Network Virtualisering. Detta beror på att nätverket utan isolering inte har de undernät som krävs för att skapa ett Windows Network Virtualization-nätverk.
        
- Vi rekommenderar att du inte använder det nätverk som du har valt för nätverksmappning för test redundans.

- Hur virtuella replikdatorer är anslutna till mappade VM-nätverk efter redundans beror på hur VM-nätverket är konfigurerat i VMM-konsolen.


### <a name="vm-network-configured-with-no-isolation-or-vlan-isolation"></a>VM-nätverk konfigurerat utan isolering eller VLAN-isolering

Om ett VM-nätverk har konfigurerats i VMM utan isolering eller VLAN-isolering bör du tänka på följande:

- Om DHCP har definierats för vm-nätverket är den virtuella replikdatorn ansluten till VLAN-ID via de inställningar som anges för nätverksplatsen i det associerade logiska nätverket. Den virtuella datorn tar emot sin IP-adress från den tillgängliga DHCP-servern.
- Du behöver inte definiera en statisk IP-adresspool för måldassnätverket. Om en statisk IP-adresspool används för VM-nätverket är den virtuella replikdatorn ansluten till VLAN-ID via de inställningar som anges för nätverksplatsen i det associerade logiska nätverket.
- Den virtuella datorn tar emot sin IP-adress från poolen som har definierats för VM-nätverket. Om en statisk IP-adresspool inte har definierats i mål-VM-nätverket misslyckas IP-adressallokeringen. Skapa IP-adresspoolen på både käll- och mål-VMM-servrar som du ska använda för skydd och återställning.

### <a name="vm-network-with-windows-network-virtualization"></a>VM-nätverk med Windows Network Virtualisering

Om ett VM-nätverk har konfigurerats i VMM med Windows Network Virtualisering, observera följande:

- Du bör definiera en statisk pool för mål-VM-nätverket, oavsett om käll-VM-nätverket är konfigurerat för att använda DHCP eller en statisk IP-adresspool. 
- Om du definierar DHCP fungerar mål-VMM-servern som en DHCP-server och tillhandahåller en IP-adress från poolen som har definierats för mål-VM-nätverket.
- Om användning av en statisk IP-adresspool har definierats för källservern allokerar mål-VMM-servern en IP-adress från poolen. I båda fallen misslyckas IP-adressallokering om en statisk IP-adresspool inte har definierats.



## <a name="prepare-the-infrastructure"></a>Förbered infrastrukturen

Om du bara vill kontrollera att en virtuell dator kan växla över kan du köra en test redundans utan en infrastruktur. Om du vill göra en fullständig DR-övning för att testa app redundans måste du förbereda infrastrukturen på den sekundära platsen:

- Om du kör en testväxling med ett befintligt nätverk förbereder du Active Directory, DHCP och DNS i nätverket.
- Om du kör en testväxling med alternativet att skapa ett VM-nätverk automatiskt måste du lägga till infrastrukturresurser i det automatiskt skapade nätverket innan du kör testundanundansen. I en återställningsplan kan du underlätta detta genom att lägga till ett manuellt steg före Grupp-1 i återställningsplanen som du ska använda för testundansen. Lägg sedan till infrastrukturresurserna i det automatiskt skapade nätverket innan du kör testundansen.


### <a name="prepare-dhcp"></a>Förbereda DHCP
Om de virtuella datorer som ingår i test failover använder DHCP skapar du en test-DHCP-server i det isolerade nätverket för test redundans.


### <a name="prepare-active-directory"></a>Förbereda Active Directory
Om du vill köra en testväxling för programtestning behöver du en kopia av active directory-produktionsmiljön i testmiljön. Mer information finns i [övervägandena om testundanundanfall för Active Directory](site-recovery-active-directory.md#test-failover-considerations).

### <a name="prepare-dns"></a>Förbered DNS
Förbered en DNS-server för test redundans enligt följande:

* **DHCP**: Om virtuella datorer använder DHCP bör IP-adressen för test-DNS uppdateras på testet DHCP-servern. Om du använder en nätverkstyp för Windows Network Virtualisering fungerar VMM-servern som DHCP-server. Därför bör DNS-adressen uppdateras i test failover-nätverket. I det här fallet registrerar sig de virtuella datorerna till den relevanta DNS-servern.
* **Statisk adress**: Om virtuella datorer använder en statisk IP-adress bör IP-adressen för test-DNS-servern uppdateras i test failover-nätverket. Du kan behöva uppdatera DNS med IP-adressen för test virtuella datorer. Du kan använda följande exempelskript för detta ändamål:

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

Den här proceduren beskriver hur du kör en testväxling för en återställningsplan. Du kan också köra redundansen för en enskild virtuell dator på fliken **Virtuella datorer.**

1. Välj **återställningsplaner** > *recoveryplan_name*. Klicka på > **Redundanstest redundans**. **Failover**
2. På bladet **Testa redundans** anger du hur virtuella repliker ska anslutas till nätverk efter test redundansen.
3. Spåra redundansstatus på fliken **Jobb.**
4. När redundansen är klar kontrollerar du att de virtuella datorerna startar.
5. När du är klar klickar du på **Rensa testundans** på återställningsplanen. I **Kommentarer** skriver du och sparar eventuella observationer från redundanstestningen. Det här steget tar bort alla virtuella datorer och nätverk som har skapats av Site Recovery under test redundans. 

![Redundanstest](./media/hyper-v-vmm-test-failover/TestFailover.png)
 


> [!TIP]
> IP-adressen som ges till en virtuell dator under test redundans är samma IP-adress som den virtuella datorn skulle få för en planerad eller oplanerad redundans (förutsätter att IP-adressen är tillgänglig i test redundansnätverket). Om samma IP-adress inte är tillgänglig i test failover-nätverket får den virtuella datorn en annan IP-adress som är tillgänglig i test failover-nätverket.



### <a name="run-a-test-failover-to-a-production-network"></a>Köra en testväxling till ett produktionsnätverk

Vi rekommenderar att du inte kör en test redundans till nätverket för produktionsåterställning som du angav under nätverksmappningen. Men om du behöver validera heltäckande nätverksanslutning i en felad virtuell dator bör du tänka på följande:

* Kontrollera att den primära virtuella datorn stängs av när du gör test redundansen. Om du inte gör det körs två virtuella datorer med samma identitet i samma nätverk samtidigt. Denna situation kan leda till oönskade konsekvenser.
* Alla ändringar som du gör i virtuella testundans-datorer går förlorade när du rensar upp virtuella test redundansdatorer. Dessa ändringar replikeras inte tillbaka till de primära virtuella datorerna.
* Testning som detta leder till driftstopp för ditt produktionsprogram. Be användare av programmet att inte använda programmet när DR-borren pågår.  


## <a name="next-steps"></a>Nästa steg
När du har kört en DR-borr kan du [köra en fullständig redundans](site-recovery-failover.md).



