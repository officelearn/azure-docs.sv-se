---
title: Konfigurera IP-adressering efter redundans till en sekundär plats med Azure Site Recovery
description: Beskriver hur du konfigurerar IP-adressering för anslutning till virtuella datorer på en sekundär lokal plats efter haveriberedskap och redundans med Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: a61f7ff69e648262eb721eb61a98b09dbbee924c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73961427"
---
# <a name="set-up-ip-addressing-to-connect-to-a-secondary-on-premises-site-after-failover"></a>Konfigurera IP-adressering för att ansluta till en sekundär lokal plats efter redundans

När du har växlat över virtuella hyper-virtuella datorer i VMM-moln (System Center Virtual Machine Manager) till en sekundär plats måste du kunna ansluta till replik-virtuella datorer. Den här artikeln hjälper dig att göra detta. 

## <a name="connection-options"></a>Anslutningsalternativ

Efter redundans finns det ett par sätt att hantera IP-adressering för virtuella repliker: 

- **Behåll samma IP-adress efter redundans:** I det här scenariot har den replikerade virtuella datorn samma IP-adress som den primära virtuella datorn. Detta förenklar nätverksrelaterade problem efter redundans, men kräver en del infrastrukturarbete.
- **Använd en annan IP-adress efter redundans:** I det här fallet får den virtuella datorn en ny IP-adress efter redundans. 
 

## <a name="retain-the-ip-address"></a>Behåll IP-adressen

Om du vill behålla IP-adresserna från den primära platsen kan du efter redundans till den sekundära platsen:

- Distribuera ett utsträckt undernät mellan de primära och sekundära platserna.
- Utför en fullständig undernätsväxling från den primära till sekundära platsen. Du måste uppdatera vägar för att ange den nya platsen för IP-adresserna.


### <a name="deploy-a-stretched-subnet"></a>Distribuera ett utsträckt undernät

I en utsträckt konfiguration är undernätet tillgängligt samtidigt på både primära och sekundära platser. I ett utsträckt undernät dirigerar nätverket automatiskt trafiken till den nya platsen när du flyttar en dator och dess IP-adresskonfiguration (Layer 3) till den sekundära platsen. 

- Från ett layer 2-perspektiv (datalänklager) behöver du nätverksutrustning som kan hantera en utsträckt VLAN.
- Genom att sträcka ut VLAN sträcker sig den potentiella feldomänen till båda platserna. Detta blir en enda felpunkt. Även om det är osannolikt, i ett sådant scenario kanske du inte kan isolera en incident som en sändning storm. 


### <a name="fail-over-a-subnet"></a>Växla över ett undernät

Du kan växla över hela undernätet för att få fördelarna med det utsträckta undernätet, utan att sträcka ut det. I den här lösningen är ett undernät tillgängligt på käll- eller målplatsen, men inte i båda samtidigt.

- Om du vill underhålla IP-adressutrymmet i händelse av en redundans kan du programmässigt ordna så att routerinfrastrukturen flyttar undernät från en plats till en annan.
- När en redundans inträffar flyttas undernät med tillhörande virtuella datorer.
- Den största nackdelen med detta tillvägagångssätt är att i händelse av ett fel måste du flytta hela undernätet.

#### <a name="example"></a>Exempel

Här är ett exempel på fullständig undernätsväxling. 

- Före redundans har den primära platsen program som körs i undernät 192.168.1.0/24.
- Under redundans misslyckas alla virtuella datorer i det här undernätet över till den sekundära platsen och behåller sina IP-adresser. 
- Vägar mellan alla platser måste ändras för att återspegla det faktum att alla virtuella datorer i undernätet 192.168.1.0/24 nu har flyttats till den sekundära platsen.

Följande bilder illustrerar undernäten före och efter redundansen.


**Före redundans**

![Före redundans](./media/hyper-v-vmm-networking/network-design2.png)

**Efter redundans**

![Efter redundans](./media/hyper-v-vmm-networking/network-design3.png)

Efter redundans allokerar Site Recovery en IP-adress för varje nätverksgränssnitt på den virtuella datorn. Adressen allokeras från den statiska IP-adresspoolen i det relevanta nätverket för varje VM-instans.

- Om IP-adresspoolen på den sekundära platsen är densamma som på källplatsen allokerar Site Recovery samma IP-adress (för käll-VM)) till den virtuella repliken. IP-adressen är reserverad i VMM, men den anges inte som redundans-IP-adressen på Hyper-V-värden. Redundans-IP-adressen på en Hyper-v-värd anges strax före redundansen.
- Om samma IP-adress inte är tillgänglig allokerar Site Recovery en annan tillgänglig IP-adress från poolen.
- Om virtuella datorer använder DHCP hanterar inte site recovery IP-adresserna. Du måste kontrollera att DHCP-servern på den sekundära platsen kan allokera adresser från samma intervall som källplatsen.

### <a name="validate-the-ip-address"></a>Verifiera IP-adressen

När du har aktiverat skydd för en virtuell dator kan du använda följande exempelskript för att verifiera adressen som tilldelats den virtuella datorn. Den här IP-adressen anges som IP-adress för redundans och tilldelas den virtuella datorn vid tidpunkten för redundansen:

    ```
    $vm = Get-SCVirtualMachine -Name <VM_NAME>
    $na = $vm[0].VirtualNetworkAdapters>
    $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
    $ip.address 
    ```

## <a name="use-a-different-ip-address"></a>Använda en annan IP-adress

I det här fallet ändras IP-adresserna för virtuella datorer som växlar över. Nackdelen med denna lösning är det underhåll som krävs.  DNS- och cacheposter kan behöva uppdateras. Detta kan resultera i driftstopp, vilket kan mildras på följande sätt:

- Använd låga TTL-värden för intranätprogram.
- Använd följande skript i en återställningsplan för återställning av webbplatsen för en snabb uppdatering av DNS-servern. Du behöver inte skriptet om du använder dynamisk DNS-registrering.

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

I det här exemplet har vi olika IP-adresser över primära och sekundära platser, och det finns en tredje plats från vilken program som finns på den primära platsen eller återställningsplatsen kan nås.

- Före redundans finns appar undernät 192.168.1.0/24 på den primära platsen.
- Efter redundans konfigureras appar i undernät 172.16.1.0/24 på den sekundära platsen.
- Alla tre webbplatser kan komma åt varandra.
- Efter redundans återställs appar i återställningsundernätet.
- I det här scenariot finns det ingen anledning att växla över hela undernätet, och inga ändringar behövs för att konfigurera om VPN eller nätverksvägar. Redundansen och vissa DNS-uppdateringar säkerställer att programmen förblir tillgängliga.
- Om DNS är konfigurerat för att tillåta dynamiska uppdateringar registrerar sig de virtuella datorerna med den nya IP-adressen när de startar efter redundans.

**Före redundans**

![Annan IP-adress - före redundans](./media/hyper-v-vmm-networking/network-design10.png)

**Efter redundans**

![Annan IP-adress - efter redundans](./media/hyper-v-vmm-networking/network-design11.png)


## <a name="next-steps"></a>Nästa steg

[Köra en redundansväxling](hyper-v-vmm-failover-failback.md)

