---
title: Ansluta till azure-virtuella datorer lokal redundans med Azure Site Recovery
description: Beskriver hur du ansluter till virtuella Azure-datorer efter redundans från lokalt till Azure med hjälp av Azure Site Recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/13/2019
ms.author: mayg
ms.openlocfilehash: f222cdd315b79503b1bdea032f495c71df4682b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281995"
---
# <a name="connect-to-azure-vms-after-failover-from-on-premises"></a>Ansluta till virtuella Azure-datorer efter redundans från lokala 


I den här artikeln beskrivs hur du konfigurerar anslutning så att du kan ansluta till virtuella Azure-datorer efter redundans.

När du ställer in haveriberedskap av lokala virtuella datorer (VMs) och fysiska servrar till Azure, börjar [Azure Site Recovery](site-recovery-overview.md) replikera datorer till Azure. När avbrott inträffar kan du växla över till Azure från din lokala webbplats. När redundans inträffar skapar Site Recovery virtuella Azure-datorer med hjälp av replikerade lokala data. Som en del av planeringen för haveriberedskap måste du ta reda på hur du ansluter till appar som körs på dessa virtuella Azure-datorer efter redundans.

I den här artikeln lär du dig hur du:

> [!div class="checklist"]
> * Förbered lokala datorer före redundans.
> * Förbered virtuella Azure-datorer efter redundans. 
> * Behåll IP-adresser på virtuella Azure-datorer efter redundans.
> * Tilldela nya IP-adresser till virtuella Azure-datorer efter redundans.

## <a name="prepare-on-premises-machines"></a>Förbered lokala maskiner

Förbered lokala datorer innan redundans för att säkerställa anslutning till virtuella Azure-datorer.

### <a name="prepare-windows-machines"></a>Förbereda Windows-maskiner

Gör följande på lokala Windows-datorer:

1. Konfigurera Windows-inställningar. Dessa inkluderar att ta bort statiska beständiga vägar eller WinHTTP-proxy och att ange disk-SAN-principen till **OnlineAll**. [Följ](../virtual-machines/windows/prepare-for-upload-vhd-image.md#set-windows-configurations-for-azure) dessa instruktioner.

2. Kontrollera att [dessa tjänster](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services) körs.

3. Aktivera fjärrskrivbord (RDP) för att tillåta fjärranslutningar till den lokala datorn. [Lär dig hur du](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings) aktiverar RDP med PowerShell.

4. Om du vill komma åt en virtuell Azure-dator via internet efter redundans tillåter du TCP och UDP i den offentliga profilen i Windows-brandväggen i Windows-brandväggen på den lokala datorn och anger RDP som en tillåten app för alla profiler.

5. Om du vill komma åt en Virtuell Azure-dator via en plats-till-plats-VPN efter redundans, i Windows-brandväggen på den lokala datorn, tillåt RDP för domänen och privata profiler. [Läs om](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules) hur du tillåter RDP-trafik.
6. Kontrollera att det inte finns några Windows-uppdateringar som väntar på den lokala virtuella datorn när du utlöser en redundans. Om det finns, uppdateringar kan börja installera på Azure VM efter redundans, och du kommer inte att kunna logga in på den virtuella datorn tills uppdateringarna är klara.

### <a name="prepare-linux-machines"></a>Förbered Linux-maskiner

Gör följande på lokala Linux-datorer:

1. Kontrollera att tjänsten Secure Shell är inställd på att starta automatiskt vid systemstart.
2. Kontrollera att brandväggsreglerna tillåter en SSH-anslutning.


## <a name="configure-azure-vms-after-failover"></a>Konfigurera virtuella Azure-datorer efter redundans

Efter redundans gör du följande på de virtuella Azure-datorerna som skapas.

1. Om du vill ansluta till den virtuella datorn via internet tilldelar du en offentlig IP-adress till den virtuella datorn. Du kan inte använda samma offentliga IP-adress för den virtuella Azure-datorn som du använde för din lokala dator. [Läs mer](../virtual-network/virtual-network-public-ip-address.md)
2. Kontrollera att NSG-regler (Network Security Group) på den virtuella datorn tillåter inkommande anslutningar till RDP- eller SSH-porten.
3. Kontrollera [startdiagnostik](../virtual-machines/troubleshooting/boot-diagnostics.md#enable-boot-diagnostics-on-existing-virtual-machine) för att visa den virtuella datorn.


> [!NOTE]
> Azure Bastion-tjänsten erbjuder privat RDP- och SSH-åtkomst till virtuella Azure-datorer. [Läs mer](../bastion/bastion-overview.md) om den här tjänsten.

## <a name="set-a-public-ip-address"></a>Ange en offentlig IP-adress

Som ett alternativ till att tilldela en offentlig IP-adress manuellt till en Virtuell Azure-dator kan du tilldela adressen under redundans med hjälp av en skript- eller Azure automation-runbook i en [återställningsplan](site-recovery-create-recovery-plans.md)för webbplatsåterställning eller så kan du konfigurera ROUTning på DNS-nivå med Azure Traffic Manager. [Läs mer](concepts-public-ip-address-with-site-recovery.md) om hur du konfigurerar en offentlig adress.


## <a name="assign-an-internal-address"></a>Tilldela en intern adress

Om du vill ange den interna IP-adressen för en virtuell Azure-dator efter redundans har du ett par alternativ:

- **Behåll samma IP-adress:** Du kan använda samma IP-adress på den virtuella Azure-datorn som den som allokeras till den lokala datorn.
- **Använd olika IP-adress:** Du kan använda en annan IP-adress för Den virtuella Azure-datorn.


## <a name="retain-ip-addresses"></a>Behåll IP-adresser

Med Site Recovery kan du behålla samma IP-adresser när du inte över till Azure. Om du behåller samma IP-adress undviks potentiella nätverksproblem efter redundans, men det innebär en viss komplexitet.

- Om målet Azure VM använder samma IP-adress /undernät som din lokala plats, kan du inte ansluta mellan dem med hjälp av en plats-till-plats VPN-anslutning eller ExpressRoute, på grund av adressöverlappningen. Undernäten måste vara unika.
- Du behöver en anslutning från lokalt till Azure efter redundans, så att appar är tillgängliga på virtuella Azure-datorer. Azure stöder inte utsträckta VPN, så om du vill behålla IP-adresser måste du ta IP-utrymmet över till Azure genom att misslyckas över hela undernätet, förutom den lokala datorn.
- Undernätsväxling säkerställer att ett visst undernät inte är tillgängligt samtidigt lokalt och i Azure.

Att behålla IP-adresser kräver följande steg:

- I egenskaperna för beräkning & nätverksegenskaper för det replikerade objektet anger du nätverks- och IP-adressering för målet Azure VM för att spegla den lokala inställningen.
- Undernät måste hanteras som en del av haveriberedskapsprocessen. Du behöver ett Azure-nätverk för att matcha det lokala nätverket, och efter redundansnätverksvägar måste ändras för att återspegla att undernätet har flyttats till Azure och nya IP-adressplatser.  

### <a name="failover-example"></a>Exempel på redundans

Nu ska vi titta på ett exempel.

- Det fiktiva företaget Woodgrove Bank är värd för sina affärsappar lokalt De är värd för sina mobilappar i Azure.
- De ansluter från lokalt till Azure via plats-till-plats-VPN. 
- Woodgrove använder Site Recovery för att replikera lokala datorer till Azure.
- Deras lokala appar använder hårdkodade IP-adresser, så de vill behålla samma IP-adresser i Azure.
- Lokala datorer som kör apparna körs i tre undernät:
    - 192.168.1.0/24.
    - 192.168.2.0/24
    - 192.168.3.0/24
- Deras appar som körs i Azure finns i Azure VNet **Azure Network** i två undernät:
- 172.16.1.0/24
- 172.16.2.0/24.

För att behålla adresserna, här är vad de gör.

1. När de aktiverar replikering anger de att datorer ska replikeras till **Azure Network**.
2. De skapar **Återställningsnätverk** i Azure. Detta virtuella nätverk speglar undernätet 192.168.1.0/24 i deras lokala nätverk.
3. Woodgrove upprättar en [VNet-till-VNet-anslutning](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) mellan de två nätverken. 

    > [!NOTE]
    > Beroende på programkrav kan en VNet-till-VNet-anslutning konfigureras före redundans, som en manuell steg/skriptsteg/Azure-automatiseringskörningskörning i en [återställningsplan](site-recovery-create-recovery-plans.md)för platsen eller efter att redundansen har slutförts.

4. Före redundans, på datorns egenskaper i Site Recovery, anger de mål-IP-adressen till adressen till den lokala datorn, enligt beskrivningen i nästa procedur.
5. Efter redundans skapas virtuella Azure-datorer med samma IP-adress. Woodgrove ansluter från **Azure Network** till **Återställningsnätverk** VNet med hjälp av VNet-peering (med transitanslutning aktiverad).
6. På plats måste Woodgrove göra nätverksändringar, inklusive att ändra vägar så att de återspeglar att 192.168.1.0/24 har flyttats till Azure.  

**Infrastruktur före redundans**

![Före felupp redundans för undernät](./media/site-recovery-network-design/network-design7.png)


**Infrastruktur efter redundans**

![Efter felning i undernätet](./media/site-recovery-network-design/network-design9.png)


### <a name="set-target-network-settings"></a>Ange inställningar för målnätverk

Innan redundans anger du nätverksinställningar och IP-adress för målet Azure VM.

1.  Välj den lokala datorn i valvet för återställningstjänster - > **replikerade objekt.**
2. På sidan **Beräkna och nätverk** för datorn klickar du på **Redigera**för att konfigurera nätverks- och kortinställningar för målet Azure VM.
3. I **Nätverksegenskaper**väljer du det målnätverk där Den virtuella Azure-datorn ska placeras när den skapas efter redundans.
4. Konfigurera nätverkskorten i målnätverket i **Nätverksgränssnitt.** Som standard visar Site Recovery alla identifierade nätverkskort på den lokala datorn.
    - I **målnätverksgränssnittstyp** kan du ange varje nätverkskort som **primärt,** **sekundärt**eller **skapa inte** om du inte behöver det specifika nätverkskortet i målnätverket. Ett nätverkskort måste anges som primärt för redundans. Observera att ändring av målnätverket påverkar alla nätverkskort för Den virtuella Azure-datorn.
    - Klicka på nätverkskortets namn för att ange det undernät där Azure VM ska distribueras.
    - Skriv över **Dynamisk** med den privata IP-adress som du vill tilldela för att rikta Azure VM. Om en IP-adress inte anges Site Recovery kommer att tilldela nästa tillgängliga IP-adress i undernätet till nätverkskortet vid redundans.
    - [Läs mer](site-recovery-manage-network-interfaces-on-premises-to-azure.md) om hur du hanterar nätverkskort för lokal redundans till Azure.


## <a name="get-new-ip-addresses"></a>Skaffa nya IP-adresser

I det här fallet får Azure VM en ny IP-adress efter redundans. En DNS-uppdatering för att uppdatera poster för misslyckade datorer för att peka på IP-adressen för Den virtuella Azure-datorn.



## <a name="next-steps"></a>Nästa steg
[Lär dig mer om](site-recovery-active-directory.md) att replikera lokala Active Directory och DNS till Azure.


