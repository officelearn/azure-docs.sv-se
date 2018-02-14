---
title: "Konfigurera IP-adresser för att ansluta efter växling till en sekundär plats med Azure Site Recovery | Microsoft Docs"
description: "Beskriver hur du ställer in IP-adresser för att ansluta till virtuella datorer efter en redundansväxling till en sekundär plats med Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: prateek9us
editor: 
ms.assetid: 67d73590-185c-49b2-a097-597bf54747a9
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: pratshar
ms.openlocfilehash: 6baeda08b1c41cc024a02f51ca27be2829c46962
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2017
---
# <a name="set-up-ip-addressing-to-connect-after-failover-to-a-secondary-site"></a>Konfigurera IP-adresser för att ansluta efter växling till en sekundär plats

När du har granskat kraven för distribution av den här artikeln för att planera nätverk när replikering av Hyper-V virtuella datorer (VM) hanteras i System Center Virtual Machine Manager (VMM)-moln, till en sekundär plats med hjälp av [Azure Site Recovery](site-recovery-overview.md) i Azure-portalen. 

När du har läst den här artikeln kan du lämna kommentarer längst ned på sidan eller på [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="overview"></a>Översikt

När du planerar din replikering och redundansstrategi, är en av viktiga frågor hur du ansluter till repliken efter växling vid fel. Det finns ett par alternativ: 

- **Använd en annan IP-adress**: du kan välja för att använda en annan IP-adress för den replikerade virtuella datorn. I det här scenariot den virtuella datorn får en ny IP-adress efter växling vid fel och en DNS-uppdatering krävs.
- **Behålla samma IP-adress**: du kanske vill använda samma IP-adress för replikerade virtuella datorn. Behålla samma IP adresser förenklar återställning genom att minska nätverksrelaterade problem efter växling vid fel. 

## <a name="retaining-ip-addresses"></a>Behålla IP-adresser

Om du vill behålla IP-adresser från den primära platsen efter redundans till den sekundära platsen kan du göra en fullständig undernät växling vid fel och uppdatera vägar för att ange en ny plats för IP-adresser eller alternativa distribuera ett ambitiöst undernät mellan primärt och återställningsplatser.

### <a name="stretched-subnet"></a>Ambitiöst undernät

I ett sträckta undernät är undernätet tillgängliga samtidigt i både primär och sekundär plats. Om du flyttar en server och dess konfiguration för IP (nivå 3) till den sekundära platsen ska i nätverket vidarebefordra trafiken till den nya platsen automatiskt. 

Du måste nätverksutrustning som kan hantera ett ambitiöst VLAN ur Lager2 (data-länk lager). Dessutom genom att dra ut VLAN potentiella feldomänen sträcker sig till båda platserna, i stort sett blir en enskild felpunkt. Detta är ett troligt, kan detta hända att broadcast storm igång och kan inte vara isolerad. 


### <a name="subnet-failover"></a>Undernät växling vid fel

Du kan köra en undernät redundans för att få fördelarna med sträckta undernät utan att faktiskt sträcka ut. I den här lösningen, ett undernät blir tillgängliga i käll- eller plats, men inte på båda samtidigt. För att behålla IP-adressutrymme vid en växling vid fel, kan du programmässigt ordna router-infrastruktur att flytta undernäten från en plats till en annan. Efter redundans inträffar när flyttas undernät med associerade virtuella datorer. Den största nackdelen är att vid fel, måste du flytta hela undernätet.

### <a name="example"></a>Exempel

Här är ett exempel på fullständiga undernät växling vid fel. Den primära platsen har program som körs i undernätet 192.168.1.0/24. Alla virtuella datorer i det här undernätet vid redundans har redundansväxlats till den sekundära platsen och behålla sina IP-adresser. Vägar behöver ändras för att återspegla det faktum att alla VM virtuella datorer som hör till 192.168.1.0/24 undernät har nu flyttats till den sekundära platsen.

Följande bild visar undernäten före och efter växling vid fel:

- Innan du redundans är undernät 192.168.0.1/24 aktiv på källplatsen, aktiveras på den sekundära platsen efter redundans.
- Vägar mellan primära platsen och återställningsplatsen, tredje plats och primär plats och tredje plats och återställningsplatsen måste ändras på lämpligt sätt.

**Innan du redundans**

![Innan du redundans](./media/vmm-to-vmm-walkthrough-network/network-design2.png)

**Efter växling vid fel**

![Efter växling vid fel](./media/vmm-to-vmm-walkthrough-network/network-design3.png)

Efter växling vid fel är här vad som händer:

- Site Recovery tilldelar en IP-adress för varje nätverksgränssnitt på den virtuella datorn från statisk IP-adresspool i relevanta nätverket för varje VMM-instans.
- Om IP-adresspool på den sekundära platsen är samma som på källplatsen, allokerar Site Recovery samma IP-adress (av den Virtuella källdatorn) till replikerade virtuella datorn. IP-adressen är reserverad i VMM, men det anges inte som IP-adress för redundans i Hyper-V-värden. IP-adress för redundans i Hyper-v-värd anges innan växling vid fel.
- Om samma IP-adress inte är tillgänglig, allokerar Site Recovery en annan tillgänglig IP-adress från poolen.
- Om virtuella datorer som använder DHCP hantera Site Recovery inte IP-adresser. Du måste kontrollera att DHCP-servern på den sekundära platsen kan allokera adress från samma intervall som källplatsen.

### <a name="validate-the-ip-address"></a>Verifiera IP-adress

När du aktiverar skyddet för en virtuell dator kan du använda följande exempelskript för att verifiera den adress som tilldelats till den virtuella datorn. Samma IP-adress ska ange IP-adress för redundans och tilldelats den virtuella datorn vid tidpunkten för växling vid fel:

    ```
    $vm = Get-SCVirtualMachine -Name <VM_NAME>
    $na = $vm[0].VirtualNetworkAdapters>
    $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
    $ip.address 
    ```

## <a name="changing-ip-addresses"></a>Ändra IP-adresser

IP-adresser för virtuella datorer som växlar över i det här scenariot har ändrats. Nackdelen med den här lösningen är underhåll som krävs. Vanligtvis uppdateras DNS när Replikdatorerna har startat. DNS-poster kan behöva ändras eller fluster i thenetwork och cachelagrade poster uppdateras. Detta kan leda till driftstopp. Driftstopp kan begränsas på följande sätt:

- Använd låg TTL-värden för intranätsprogram.
- Använd följande skript i en Site Recovery återställningsplan för att uppdatera DNS-servern för att säkerställa en rimlig uppdatering. Du behöver inte skriptet om du använder dynamisk DNS-registrering.

    ```
    param(
    string]$Zone,
    [string]$name,
    [string]$IP
    )
    $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
    $newrecord = $record.clone()
    $newrecord.RecordData[0].IPv4Address  =  $IP
    Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord
    ```
    
### <a name="example"></a>Exempel 

Nu ska vi titta på ett scenario där du planerar att använda olika IP-adresser i den primära servern och återställningsplatser. I det här exemplet har vi olika IP-adresser mellan primära och sekundära platser och där, s tredje plats från vilka program som finns på den primära eller återställning platsen kan nås.

- Före redundans, appar är värdbaserade undernät 192.168.1.0/24 på den primära platsen och är konfigurerad för att vara i undernätet 172.16.1.0/24 på den sekundära platsen efter en redundansväxling.
- VPN-anslutningar/nätverksvägar har konfigurerats på rätt sätt så att alla tre platser som har åtkomst till varandra.
- Appar kommer att återställas i undernätet för återställning efter redundans. Det finns ingen anledning att växla över hela undernätet i det här scenariot och inga ändringar behövs för att konfigurera om VPN- eller nätverksvägar. Se till att program fortfarande är tillgänglig för växling vid fel och vissa DNS-uppdateringar.
- Om DNS är konfigurerad för att tillåta dynamiska uppdateringar, sedan de virtuella datorerna kommer att registrera sig med den nya IP-adressen när de startar efter växling vid fel.

**Innan du redundans**

![Olika IP - före redundans](./media/vmm-to-vmm-walkthrough-network/network-design10.png)

**Efter växling vid fel**

![Olika IP - efter växling vid fel](./media/vmm-to-vmm-walkthrough-network/network-design11.png)




