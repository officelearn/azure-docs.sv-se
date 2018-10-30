---
title: Konfigurera IP-adresser för att ansluta till en sekundär lokal plats efter en redundansväxling med Azure Site Recovery | Microsoft Docs
description: Beskriver hur du ställer in IP-adresser för att ansluta till virtuella datorer på en sekundär lokal plats efter haveriberedskap och redundans med Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/28/2018
ms.author: rayne
ms.openlocfilehash: 1a9a89138dc71f7a0255b8ab4084182848d6e994
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2018
ms.locfileid: "50211748"
---
# <a name="set-up-ip-addressing-to-connect-to-a-secondary-on-premises-site-after-failover"></a>Konfigurera IP-adresser för att ansluta till en sekundär lokal plats efter redundans

När du växlar över Hyper-V-datorer i System Center Virtual Machine Manager (VMM)-moln till en sekundär plats måste du kunna ansluta till repliken virtuella datorer. Den här artikeln hjälper dig att göra detta. 

## <a name="connection-options"></a>Anslutningsalternativ

Det finns ett par olika sätt att hantera IP-adresser för virtuella replikdatorerna efter redundansväxlingen: 

- **Behålla samma IP-adress efter redundansväxling**: I det här scenariot den replikerade virtuella datorn har samma IP-adress som den primära virtuella datorn. Detta förenklar nätverket utfärdar efter växling vid fel, men det krävs vissa infrastrukturarbete.
- **Använd en annan IP-adress efter redundansväxling**: I det här scenariot hämtar den virtuella datorn en ny IP-adress efter redundansväxling. 
 

## <a name="retain-the-ip-address"></a>Behålla IP-adressen

Om du vill behålla IP-adresser från den primära platsen efter redundans till den sekundära platsen kan du:

- Distribuera ett sträckt undernät mellan primärt och sekundära platser.
- Utför en fullständig undernät redundansväxling från primär till sekundär plats. Du behöver uppdatera vägar för att ange den nya platsen för IP-adresser.


### <a name="deploy-a-stretched-subnet"></a>Distribuera ett sträckt undernät

I en konfiguration för sträckt är undernätet tillgängligt samtidigt i både de primära och sekundära platserna. I ett sträckt undernät när du flyttar en virtuell dator och dess IP-(Layer 3)-adresskonfiguration till den sekundära platsen dirigerar i nätverket automatiskt trafik till den nya platsen. 

- Ur en nivå 2 (data-länk lager) måste du nätverksutrustning som kan hantera ett sträckt VLAN.
- Genom att utvidga VLAN, utökar potentiella feldomänen till båda platserna. Detta blir en enskild felpunkt. Men det är osannolikt, i ett sådant scenario kanske du inte att kunna isolera en incident, till exempel en broadcast storm. 


### <a name="fail-over-a-subnet"></a>Växla över ett undernät

Du kan växla över hela undernätet för att få fördelarna med det sträckta undernätet utan att faktiskt utvidga den. I den här lösningen, ett undernät är tillgängliga på käll- eller plats, men inte i båda samtidigt.

- Om du vill behålla IP-adressutrymmet i händelse av redundans kan du programmässigt ordna router-infrastruktur för att flytta undernät från en plats till en annan.
- När det uppstår redundans, flytta undernät med deras associerade virtuella datorer.
- Den huvudsakliga Nackdelen med den här metoden är att om ett fel inträffar, måste du flytta hela undernätet.

#### <a name="example"></a>Exempel

Här är ett exempel på fullständiga undernät redundans. 

- Före redundansväxlingen har den primära platsen program som körs i undernätet 192.168.1.0/24.
- Under redundansväxlingen, alla de virtuella datorerna i det här undernätet växlas över till den sekundära platsen och behålla sina IP-adresser. 
- Vägar mellan alla platser måste ändras för att återspegla det faktum att alla virtuella datorer i undernätet 192.168.1.0/24 har nu flyttats till den sekundära platsen.

På följande bild illustreras undernäten före och efter redundans.


**Före redundans**

![Före redundans](./media/hyper-v-vmm-networking/network-design2.png)

**Efter en redundansväxling**

![Efter en redundansväxling](./media/hyper-v-vmm-networking/network-design3.png)

Efter en redundansväxling tilldelar Webbplatsåterställning en IP-adress för varje nätverksgränssnitt på den virtuella datorn. Adressen allokeras från statisk IP-adresspool i det relevanta nätverket för varje virtuell datorinstans.

- Om IP-adresspool på den sekundära platsen är samma som på källplatsen, allokerar Site Recovery samma IP-adress (av den Virtuella källdatorn), till den Virtuella replikdatorn. IP-adressen är reserverad i VMM, men ändra inte IP-adress för växling vid fel på Hyper-V-värden. IP-adress för redundans på en Hyper-v-värden anges precis före redundansväxlingen.
- Om samma IP-adress inte är tillgängligt, tilldelar Webbplatsåterställning en annan tillgänglig IP-adress från poolen.
- Site Recovery hanterar inte IP-adresser om virtuella datorer använder DHCP. Du måste kontrollera att DHCP-servern på den sekundära platsen kan allokera adresser från samma intervall som källplatsen.

### <a name="validate-the-ip-address"></a>Verifiera IP-adress

När du har aktiverat skydd för en virtuell dator, kan du använda följande exempelskript för att verifiera den adress som tilldelats den virtuella datorn. Den här IP-adressen är värdet som IP-adress för redundans och tilldelas den virtuella datorn vid redundansväxlingen:

    ```
    $vm = Get-SCVirtualMachine -Name <VM_NAME>
    $na = $vm[0].VirtualNetworkAdapters>
    $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
    $ip.address 
    ```

## <a name="use-a-different-ip-address"></a>Använd en annan IP-adress

IP-adresserna för virtuella datorer som redundansväxlar i det här scenariot har ändrats. Nackdel med den här lösningen är underhållet krävs.  DNS- och cache poster kan behöva uppdateras. Detta kan leda till driftstopp och kan undvikas på följande sätt:

- Använd låg TTL-värden för intranätprogram.
- Använd följande skript i en återställningsplan för Site Recovery för en rimlig uppdatering av DNS-servern. Du behöver inte skriptet om du använder dynamisk DNS-registrering.

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

I det här exemplet har vi olika IP-adresser mellan primära och sekundära platser och det finns en tredje plats från vilka program som körs på den primära eller recovery webbplatsen är tillgänglig.

- Appar är före redundans, värdbaserade undernät 192.168.1.0/24 på den primära platsen.
- Appar är konfigurerade i undernätet 172.16.1.0/24 på den sekundära platsen efter redundansväxlingen.
- Alla tre platser har åtkomst till varandra.
- Appar kommer att återställas i undernätet för återställning efter redundansväxlingen.
- Det finns inget behov att växla över hela undernätet i det här scenariot och inga ändringar behövs för att konfigurera om VPN- eller nätverksvägar. Se till att programmen är tillgängliga för växling vid fel och vissa uppdateringar av DNS.
- Om DNS är konfigurerad för att tillåta dynamiska uppdateringar, ska sedan de virtuella datorerna registrera sig med den nya IP-adressen när de startar efter en redundansväxling.

**Före redundans**

![Olika IP-adress – före redundans](./media/hyper-v-vmm-networking/network-design10.png)

**Efter en redundansväxling**

![Annan IP-adress - efter redundans](./media/hyper-v-vmm-networking/network-design11.png)


## <a name="next-steps"></a>Nästa steg

[Köra en redundans](hyper-v-vmm-failover-failback.md)

