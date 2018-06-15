---
title: Konfigurera IP-adresser för att ansluta till en sekundär lokal plats efter växling vid fel med Azure Site Recovery | Microsoft Docs
description: Beskriver hur du ställer in IP-adresser för att ansluta till virtuella datorer på en sekundär lokal plats efter redundans Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 02/12/2018
ms.author: rayne
ms.openlocfilehash: 531705bc704b3366c1c670ecf07c809ade67bc55
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
ms.locfileid: "29378892"
---
# <a name="set-up-ip-addressing-to-connect-to-a-secondary-on-premises-site-after-failover"></a>Konfigurera IP-adresser för att ansluta till en sekundär lokal plats efter växling vid fel

När du växlar över Hyper-V virtuella datorer i System Center Virtual Machine Manager (VMM)-moln till en sekundär plats måste du kunna ansluta till virtuella datorer för replikering. Den här artikeln hjälper dig att göra detta. 

## <a name="connection-options"></a>Anslutningsalternativ

Det finns ett par olika sätt att hantera IP-adressering för replikerade virtuella efter redundans: 

- **Behålla samma IP-adress efter redundans**: I det här scenariot den replikerade virtuella datorn har samma IP-adress som den primära virtuella datorn. Detta förenklar nätverket relaterade problem efter växling vid fel, men krävs vissa infrastrukturarbete.
- **Använd en annan IP-adress efter växling vid fel**: I det här scenariot den virtuella datorn får en ny IP-adress efter växling vid fel. 
 

## <a name="retain-the-ip-address"></a>Behålls IP-adressen

Om du vill behålla IP-adresser från den primära platsen efter redundans till den sekundära platsen kan du:

- Distribuera ett ambitiöst undernät mellan primärt och sekundära platser.
- Utför en fullständig undernät redundansväxling från primär till sekundär plats. Du måste uppdatera vägar för att ange en ny plats för IP-adresser.


### <a name="deploy-a-stretched-subnet"></a>Distribuera ett ambitiöst undernät

I en konfiguration för sträckta är undernätet tillgängliga samtidigt i båda primära och sekundära platser. I ett sträckta undernät, när du flyttar en dator och dess konfigurering av IP-(nivå 3) till den sekundära platsen dirigerar nätverket automatiskt trafik till den nya platsen. 

- Du måste nätverksutrustning som kan hantera ett ambitiöst VLAN ur Lager2 (data-länk lager).
- Genom att dra ut VLAN utökar potentiella feldomänen till båda platserna. Detta blir en enskild felpunkt. Medan osannolik, i ett sådant scenario kanske du inte att kunna isolera en incident, till exempel en broadcast storm. 


### <a name="fail-over-a-subnet"></a>Växla över ett undernät

Du kan växla över hela subnätet för att få fördelarna med sträckta undernät utan att faktiskt sträcka ut. Ett undernät är tillgängliga i den här lösningen på käll- eller plats, men inte i båda samtidigt.

- För att behålla IP-adressutrymme vid en växling vid fel, kan du programmässigt ordna router-infrastruktur att flytta undernät från en plats till en annan.
- När det uppstår redundans, flytta undernät med deras associerade virtuella datorer.
- Den huvudsakliga Nackdelen med den här metoden är att vid fel, måste du flytta hela undernätet.

#### <a name="example"></a>Exempel

Här är ett exempel på fullständiga undernät växling vid fel. 

- Före redundans har den primära platsen program som körs i undernätet 192.168.1.0/24.
- Alla de virtuella datorerna i det här undernätet har redundansväxlats till den sekundära platsen under växling vid fel och behåller sina IP-adresser. 
- Vägar mellan alla platser som behöver ändras för att återspegla det faktum att alla virtuella datorer i undernätet 192.168.1.0/24 har nu flyttats till den sekundära platsen.

Följande bild illustreras undernäten före och efter växling vid fel.


**Innan du redundans**

![Innan du redundans](./media/hyper-v-vmm-networking/network-design2.png)

**Efter växling vid fel**

![Efter växling vid fel](./media/hyper-v-vmm-networking/network-design3.png)

Efter växling vid fel allokerar Site Recovery en IP-adress för varje nätverksgränssnitt på den virtuella datorn. Adressen som har allokerats från den statiska IP-adresspoolen i det aktuella nätverket för varje VM-instans.

- Om IP-adresspool på den sekundära platsen är samma som på källplatsen, allokerar Site Recovery samma IP-adress (av den Virtuella källdatorn), till replikerade virtuella datorn. IP-adressen är reserverad i VMM, men det anges inte som IP-adress för redundans i Hyper-V-värden. IP-adress för redundans i Hyper-v-värd anges innan växling vid fel.
- Om samma IP-adress inte är tillgänglig, allokerar Site Recovery en annan tillgänglig IP-adress från poolen.
- Om virtuella datorer som använder DHCP hantera Site Recovery inte IP-adresser. Du måste kontrollera att DHCP-servern på den sekundära platsen kan allokera adresser från samma intervall som källplatsen.

### <a name="validate-the-ip-address"></a>Verifiera IP-adress

När du aktiverar skyddet för en virtuell dator kan du använda följande exempelskript för att verifiera den adress som tilldelats till den virtuella datorn. Denna IP-adress som IP-adress för redundans och tilldelats den virtuella datorn vid tidpunkten för växling vid fel:

    ```
    $vm = Get-SCVirtualMachine -Name <VM_NAME>
    $na = $vm[0].VirtualNetworkAdapters>
    $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
    $ip.address 
    ```

## <a name="use-a-different-ip-address"></a>Använd en annan IP-adress

IP-adresser för virtuella datorer som växlar över i det här scenariot har ändrats. Nackdelen med den här lösningen är underhåll som krävs.  DNS- och cache-poster kan behöva uppdateras. Detta kan leda till driftstopp, som kan begränsas på följande sätt:

- Använd låg TTL-värden för intranätsprogram.
- Använd följande skript i ett Site Recovery återställningsplan för en rimlig uppdatering av DNS-servern. Du behöver inte skriptet om du använder dynamisk DNS-registrering.

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

I det här exemplet har vi olika IP-adresser mellan primära och sekundära platser och det finns en tredje plats från vilka program som finns på den primära eller återställning webbplatsen är tillgänglig.

- Appar är värdbaserade undernät 192.168.1.0/24 på den primära platsen före redundans.
- Appar har konfigurerats i undernätet 172.16.1.0/24 på den sekundära platsen efter redundans.
- Alla tre platser har åtkomst till varandra.
- Appar kommer att återställas i undernätet för återställning efter redundans.
- Det finns ingen anledning att växla över hela undernätet i det här scenariot och inga ändringar behövs för att konfigurera om VPN- eller nätverksvägar. Se till att program fortfarande är tillgänglig för växling vid fel och vissa DNS-uppdateringar.
- Om DNS är konfigurerad för att tillåta dynamiska uppdateringar, sedan de virtuella datorerna kommer att registrera sig med den nya IP-adressen när de startar efter växling vid fel.

**Innan du redundans**

![Olika IP-adress - före redundans](./media/hyper-v-vmm-networking/network-design10.png)

**Efter växling vid fel**

![Olika IP-adress - efter växling vid fel](./media/hyper-v-vmm-networking/network-design11.png)


## <a name="next-steps"></a>Nästa steg

[Kör en redundansväxling](hyper-v-vmm-failover-failback.md)

