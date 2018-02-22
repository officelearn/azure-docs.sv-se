---
title: "Anslut Azure Stack till Azure med hjälp av ExpressRoute"
description: "Hur du ansluter virtuella nätverk i Azure-stacken till virtuella nätverk i Azure med hjälp av ExpressRoute."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: 8ec733c01b19f01951d0b53265daaef32c3afe73
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="connect-azure-stack-to-azure-using-expressroute"></a>Anslut Azure Stack till Azure med hjälp av ExpressRoute

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Det finns två sätt att ansluta virtuella nätverk i Azure-stacken till virtuella nätverk i Azure:
   * **Plats-till-plats**

     En VPN-anslutning via IPsec (IKE v1 och v2 för IKE). Den här typen av anslutning kräver en VPN-enhet eller RRAS. Mer information finns i [ansluta Azure-stacken till Azure med hjälp av VPN](azure-stack-connect-vpn.md).
   * **ExpressRoute**

     En direkt anslutning till Azure från Azure Stack-distribution. ExpressRoute är **inte** en VPN-anslutning via det offentliga Internet. Läs mer om Azure ExpressRoute [ExpressRoute översikt](../expressroute/expressroute-introduction.md).

Den här artikeln visar ett exempel som ansluter Azure Stack till Azure via ExpressRoute.
## <a name="requirements"></a>Krav
Följande är kraven för att ansluta Azure-stacken och Azure med hjälp av ExpressRoute:
* En Azure-prenumeration vill skapa en ExpressRoute-krets och Vnet i Azure.
* En etablerad ExpressRoute-krets via en [anslutning providern](../expressroute/expressroute-locations.md).
* En router som har ExpressRoute-kretsen anslutna till WAN-portar.
* LAN-sidan för routern är kopplad till Azure-stacken Multitenant Gateway.
* Routern måste ha stöd för plats-till-plats VPN-anslutningar mellan sina LAN-gränssnitt och Azure-stacken Multitenant Gateway.
* Om mer än en klient har lagts till i distributionen av Azure-stacken, måste routern kunna skapa flera VRFs (virtuella Routning och vidarebefordran).

Följande diagram visar en översikt för nätverk när du har slutfört konfigurationen:

![Konceptuellt diagram](media/azure-stack-connect-expressroute/Conceptual.png)

**Diagram 1**

Följande Arkitekturdiagram visar hur flera innehavare ansluta från Azure Stack-infrastrukturen via ExpressRoute-router till Azure i Microsoft edge:

![Arkitekturdiagram](media/azure-stack-connect-expressroute/Architecture.png)

**Diagram 2**

Exemplet som visas i den här artikeln använder samma arkitektur för att ansluta till Azure via privata ExpressRoute-peering. Detta görs med hjälp av en plats-till-plats VPN-anslutning från gateway för virtuellt nätverk i Azure-stacken till en ExpressRoute-router. Följande steg i den här artikeln visar hur du skapar en anslutning från slutpunkt till slutpunkt mellan två Vnet från två olika klienter i Azure-stacken till sina respektive Vnet i Azure. Du kan välja att lägga till många Vnet-klient och replikera stegen för varje klient eller använda det här exemplet för att distribuera bara en enskild klient VNet.

## <a name="configure-azure-stack"></a>Konfigurera Azure Stack
Nu kan du skapa resurser måste du ställa in din Azure Stack-miljö som en klient. Följande steg visar vad du behöver göra. Dessa instruktioner visar hur du skapar resurser med hjälp av Azure Stack-portalen, men du kan också använda PowerShell.

![Nätverket resurs steg](media/azure-stack-connect-expressroute/image2.png)
### <a name="before-you-begin"></a>Innan du börjar
Innan du börjar konfigurationen måste du:
* En Azure-Stack-distribution.

   Information om hur du distribuerar Azure Stack Development Kit finns [Azure Stack Development Kit distribution quickstart](azure-stack-deploy-overview.md).
* Ett erbjudande på Azure-stacken som dina användare kan prenumerera på.

  Instruktioner finns i [tillgängliggöra virtuella datorer till Azure-stacken användarna](azure-stack-tutorial-tenant-vm.md).

### <a name="create-network-resources-in-azure-stack"></a>Skapa nätverksresurser i Azure-stacken

Använd följande procedurer för att skapa de nödvändiga nätverksresurserna i Azure-stacken för varje klient:

#### <a name="create-the-virtual-network-and-vm-subnet"></a>Skapa ett undernät för det virtuella nätverket och den virtuella datorn
1. Logga in på användarportalen med ett användarkonto (klient).

2. I portalen klickar du på **ny**.

   ![](media/azure-stack-connect-expressroute/MAS-new.png)

3. Välj **Nätverk** på menyn Marketplace.

4. Klicka på **Virtuellt nätverk** på menyn.

5. Skriv värden till relevanta fält med hjälp av följande tabell:

   |Fält  |Värde  |
   |---------|---------|
   |Namn     |Tenant1VNet1         |
   |Adressutrymme     |10.1.0.0/16|
   |Namn på undernät     |Tenant1 Ab1|
   |Adressintervall för undernätet     |10.1.1.0/24|

6. Du bör se prenumerationen du skapade tidigare i fältet **Prenumeration**.

    a. För resursgrupp, kan du antingen skapa en resursgrupp eller om du redan har en, Välj **använda befintliga**.

    b. Verifiera den förvalda platsen.

    c. Klicka på **fäst på instrumentpanelen**.

    d. Klicka på **Skapa**.



#### <a name="create-the-gateway-subnet"></a>Skapa gateway-undernätet
1. Öppna resursen virtuella nätverk som du skapade (Tenant1VNet1) från instrumentpanelen.
2. Välj på avsnittet inställningar **undernät**.
3. Klicka på **Gateway Subnet** (Gateway-undernät) för att lägga till ett gateway-undernät till det virtuella nätverket.
   
    ![](media/azure-stack-connect-expressroute/gatewaysubnet.png)
4. Namnet på undernätet ställs in på **GatewaySubnet** som standard.
   Gatewayundernät är speciella och måste ha det här specifika namnet för att fungera ordentligt.
5. I den **adressintervall** fältet, kontrollera att adressen är **10.1.0.0/24**.
6. Klicka på **OK** att skapa gateway-undernätet.

#### <a name="create-the-virtual-network-gateway"></a>Skapa den virtuella nätverksgatewayen
1. Klicka på användarportalen Azure Stack **ny**.
   
2. Välj **Nätverk** på menyn Marketplace.
3. Välj **Virtuell nätverksgateway** från listan med nätverksresurser.
4. I fältet **Namn** skriver du **GW1**.
5. Klicka på **Virtuellt nätverk** för att välja ett virtuellt nätverk.
   Välj **Tenant1VNet1** från listan.
6. Klicka på menyposten **Offentlig IP-adress**. När den **Välj offentlig IP-adress** avsnittet öppnas klickar du på **Skapa nytt**.
7. I fältet **Namn** skriver du **GW1-PiP** och klickar på **OK**.
8. **VPN-typen**  ska ha **Routningsbaserad** valt som standard.
    Behåll den inställningen.
9. Verifiera att **Prenumeration** och **Plats** stämmer. Du kan fästa på instrumentpanelen till resursen om du vill. Klicka på **Skapa**.

#### <a name="create-the-local-network-gateway"></a>Skapa den lokala nätverksgatewayen

Syftet med den lokala gateway nätverksresursen är att ange fjärrgatewayen i slutet av VPN-anslutningen. I det här exemplet är fjärrparten LAN undergränssnittet för ExpressRoute-routern. Klient 1 i det här exemplet är fjärradressen 10.60.3.255 som visas i Diagram 2.

1. Logga in på den fysiska Azure Stack-datorn.
2. Logga in på användarportalen med ditt användarkonto och klicka på **ny**.
3. Välj **Nätverk** på menyn Marketplace.
4. Välj **lokal nätverksgateway** från listan med resurser.
5. I den **namn** fälttyp **ER-Router-GW**.
6. För den **IP-adress** fältet finns för Diagram 2. IP-adressen för routern ExpressRoute LAN subinterface för klient-1 är 10.60.3.255. Ange IP-adressen för routern motsvarande gränssnitt för din egen miljö.
7. I den **adressutrymme** anger adressutrymmet för Vnet som du vill ansluta till i Azure. Det här exemplet finns Diagram 2. För klient-1, Lägg märke till att de nödvändiga undernäten **192.168.2.0/24** (detta är Hub Vnet i Azure) och **10.100.0.0/16** (detta är pinnar VNet i Azure). Ange motsvarande undernät för din egen miljö.
   > [!IMPORTANT]
   > Det här exemplet förutsätter att du använder statiska vägar för plats-till-plats VPN-anslutning mellan Azure Stack-gateway och ExpressRoute-router.

8. Kontrollera att din **prenumeration**, **resursgruppen**, och **plats** är korrekta och klicka på **skapa**.

#### <a name="create-the-connection"></a>Skapa anslutningen
1. Klicka på användarportalen Azure Stack **ny**.
2. Välj **Nätverk** på menyn Marketplace.
3. Välj **Anslutning** i listan över resurser.
4. I den **grunderna** inställningar, väljer **plats-till-plats (IPSec)** som den **anslutningstypen**.
5. Välj den **prenumeration**, **resursgruppen**, och **plats** och på **OK**.
6. I den **inställningar** klickar du på **virtuell nätverksgateway** klickar du på **GW1**.
7. Klicka på **lokal nätverksgateway**, och klicka på **ER Router GW**.
8. I den **anslutningsnamn** anger **ConnectToAzure**.
9. I den **delad nyckel (PSK)** anger **abc123** och på **OK**.
10. På den **sammanfattning** klickar du på **OK**.

    När anslutningen har skapats kan se du den offentliga IP-adressen som används av den virtuella nätverksgatewayen. Om du vill hitta adressen i Azure Stack-portalen, bläddra till din virtuella nätverksgateway. I **översikt**, hitta den **offentliga IP-adressen**. Observera att den här adressen; Du kommer att använda den som den *interna IP-adressen* i nästa avsnitt (om tillämpligt för din distribution).

    ![](media/azure-stack-connect-expressroute/GWPublicIP.png)

#### <a name="create-a-virtual-machine"></a>Skapa en virtuell dator
För att validera data som överförs via VPN-anslutningen måste virtuella datorer för att skicka och ta emot data i Azure-stacken virtuella nätverk. Skapa en virtuell dator nu och placera den i ditt Virtuella undernät i det virtuella nätverket.

1. Klicka på användarportalen Azure Stack **ny**.
2. Välj **virtuella datorer** på Marketplace-menyn.
3. I listan över virtuella datoravbildningar, väljer du den **Windows Server 2016 Datacenter Eval** bild och klicka på **skapa**.
4. På den **grunderna** avsnitt i den **namn** fälttyp **VM01**.
5. Skriv ett giltigt användarnamn och lösenord. Du kommer att använda det här kontot för att logga in på den virtuella datorn när den har skapats.
6. Ange en **prenumeration**, **resursgruppen**, och **plats** och klicka sedan på **OK**.
7. På den **storlek** , på en virtuell datorstorlek för den här instansen och klicka sedan på **Välj**.
8. På den **inställningar** avsnitt, du kan acceptera standardinställningarna. Men se till att det virtuella nätverket som valts är **Tenant1VNet1** och undernätet är inställd på **10.1.1.0/24**. Klicka på **OK**.
9. Granska inställningarna på den **sammanfattning** avsnittet och klicka på **OK**.

Upprepa föregående steg från för varje klient VNet som du vill ansluta **skapa virtuella nätverk och Virtuellt datornät** via **skapa en virtuell dator** avsnitt.

### <a name="configure-the-nat-virtual-machine-for-gateway-traversal"></a>Konfigurera NAT virtuell dator för gateway-traversal
> [!IMPORTANT]
> Det här avsnittet avser endast Azure-stacken Development Kit-distributioner. NAT behövs inte för distributioner med flera noder.

Azure-stacken Development Kit är fristående och isolerade från ett nätverk som är distribuerad på den fysiska värden. Är inte extern så ”externa” VIP-nätverk som gateway är ansluten till, men i stället döljs bakom en router som gör NAT (Network Address Translation).
 
Routern är en virtuell dator med Windows Server (**AzS BGPNAT01**) kör rollen Routning och fjärråtkomsttjänster (RRAS) i Azure-stacken Development Kit-infrastruktur. Du måste konfigurera NAT på den virtuella datorn i AzS BGPNAT01 att plats-till-plats VPN-anslutningen att ansluta på båda ändarna.

#### <a name="configure-the-nat"></a>Konfigurera NAT

1. Logga in på den fysiska datorn med Azure-stacken med ditt administratörskonto.
2. Kopiera och redigera följande PowerShell-skript och köra i en upphöjd Windows PowerShell ISE. Ersätt ditt administratörslösenord. Adressen som returnerades är din *externa BGPNAT adress*.

   ```
   cd \AzureStack-Tools-master\connect
   Import-Module .\AzureStack.Connect.psm1
   $Password = ConvertTo-SecureString "<your administrator password>" `
    -AsPlainText `
    -Force
   Get-AzureStackNatServerAddress `
    -HostComputer "azs-bgpnat01" `
    -Password $Password
   ```
4. För att konfigurera NAT, kopiera och redigera följande PowerShell-skript och köra i en upphöjd Windows PowerShell ISE. Redigera skriptet för att ersätta den *externa BGPNAT adress* och *interna IP-adress* (som du antecknade tidigare i den **skapa anslutningen** avsnitt).

   Exempel-diagram i *externa BGPNAT adress* är 10.10.0.62 och *interna IP-adress* är 192.168.102.1.

   ```
   $ExtBgpNat = '<External BGPNAT address>'
   $IntBgpNat = '<Internal IP address>'

   # Designate the external NAT address for the ports that use the IKE authentication.
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress $Using:ExtBgpNat `
      -PortStart 499 `
      -PortEnd 501}
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress $Using:ExtBgpNat `
      -PortStart 4499 `
      -PortEnd 4501}
   # create a static NAT mapping to map the external address to the Gateway
   # Public IP Address to map the ISAKMP port 500 for PHASE 1 of the IPSEC tunnel
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress $Using:ExtBgpNat `
      -InternalIPAddress $Using:IntBgpNat `
      -ExternalPort 500 `
      -InternalPort 500}
   # Finally, configure NAT traversal which uses port 4500 to
   # successfully establish the complete IPSEC tunnel over NAT devices
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress $Using:ExtBgpNat `
      -InternalIPAddress $Using:IntBgpNat `
      -ExternalPort 4500 `
      -InternalPort 4500}
   ```

## <a name="configure-azure"></a>Konfigurera Azure
Nu när du har slutfört konfigurationen av Azure-stacken, kan du distribuera en Azure-resurser. Följande diagram visar en exempel-klient virtuellt nätverk i Azure. Du kan använda alla namn och adresseringsschema för din VNet i Azure. Adressintervall Vnet i Azure och Azure-stacken måste dock vara unika och inte överlappa varandra.

![Azure Vnet](media/azure-stack-connect-expressroute/AzureArchitecture.png)

**Diagram 3**

De resurser som du distribuerar i Azure liknar de resurser som du distribuerade i Azure-stacken. På liknande sätt kan distribuera du:
* Virtuella nätverk och undernät
* Ett gateway-undernät
* En virtuell nätverksgateway
* En anslutning
* En ExpressRoute-krets

Exemplet Azure nätverksinfrastrukturen konfigureras på följande sätt:
* En standard nav (192.168.2.0/24) och ekrar (10.100.0.0./16) VNet modellen används.
* Arbetsbelastningarna som har distribuerats i ekrar Vnet och ExpressRoute-kretsen är ansluten till hubben virtuella nätverk.
* Två Vnet länkas med hjälp av funktionen VNet-peering.

### <a name="configure-vnets"></a>Konfigurera Vnet
1. Logga in på Azure-portalen med dina autentiseringsuppgifter för Azure.
2. Skapa hubben VNet med 192.168.2.0/24-adressutrymme. Skapa ett undernät med 192.168.2.0/25 adressintervallet och Lägg till en gateway-undernätet med 192.168.2.128/27 adressintervallet.
3. Skapa ekrar VNet och undernät med hjälp av 10.100.0.0/16-adressintervall.


Mer information om hur du skapar virtuella nätverk i Azure finns [skapa ett virtuellt nätverk med flera undernät](../virtual-network/virtual-networks-create-vnet-arm-pportal.md).

### <a name="configure-an-expressroute-circuit"></a>Konfigurera en ExpressRoute-krets

1. Granska ExpressRoute-krav i [ExpressRoute krav & checklista](../expressroute/expressroute-prerequisites.md).
2. Följ stegen i [skapa och ändra en ExpressRoute-krets](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) att skapa en ExpressRoute-krets med din Azure-prenumeration.
3. Dela nyckeln för tjänsten från föregående steg med hoster-leverantören att etablera ExpressRoute-krets sina slutet.
4. Följ stegen i [skapa och ändra peering för en ExpressRoute-krets](../expressroute/expressroute-howto-routing-portal-resource-manager.md) Konfigurera privat peering i ExpressRoute-kretsen.

### <a name="create-the-virtual-network-gateway"></a>Skapa den virtuella nätverksgatewayen

* Följ stegen i [konfigurera en virtuell nätverksgateway för ExpressRoute med hjälp av PowerShell](../expressroute/expressroute-howto-add-gateway-resource-manager.md) att skapa en virtuell nätverksgateway expressroute i hubben virtuella nätverk.

### <a name="create-the-connection"></a>Skapa anslutningen

* Om du vill länka ExpressRoute-kretsen till hubben VNet, följer du stegen i [ansluta ett virtuellt nätverk till en ExpressRoute-krets](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md).

### <a name="peer-the-vnets"></a>Peer-till Vnet

* Peer-NAV- och pinnar Vnet med hjälp av stegen i [skapa ett virtuellt nätverk peering med Azure-portalen](../virtual-network/virtual-networks-create-vnetpeering-arm-portal.md). När du konfigurerar VNet-peering, se till att du väljer du följande alternativ:
   * Från NAV och ekrar: **Tillåt gateway överföring**
   * Från ekrar till hubben: **Använd fjärr-gateway**

### <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

* Distribuera din arbetsbelastning virtuella datorer i ekrar virtuella nätverk.

Upprepa dessa steg för varje ytterligare klient Vnet som du vill ansluta i Azure via sina respektive ExpressRoute-kretsar.

## <a name="configure-the-router"></a>Konfigurera routern

Du kan använda följande infrastrukturdiagram för slutpunkt till slutpunkt-för att styra konfigurationen av ExpressRoute-Router. Det här diagrammet visar två klienter (klient 1 och 2 för klient) med deras respektive Expressroute-kretsar. Varje är kopplad till sina egna VRF (virtuella Routning och vidarebefordran) i ExpressRoute-router för att säkra isolering av slutpunkt till slutpunkt mellan två klienter LAN och WAN sida. Observera de IP-adresser som används i router-gränssnitt som du följer exempelkonfiguration.

![Slut till slut diagram](media/azure-stack-connect-expressroute/EndToEnd.png)

**Diagram över 4**

Du kan använda en router som har stöd för IKEv2 VPN och BGP för att avsluta plats-till-plats VPN-anslutning från Azure-stacken. Samma router används för att ansluta till Azure med hjälp av en ExpressRoute-krets. 

Här är ett exempel på konfiguration från en Cisco ASR 1000 som stöder infrastruktur för nätverk som visas i Diagram 4:

```
ip vrf Tenant 1
 description Routing Domain for PRIVATE peering to Azure for Tenant 1
 rd 1:1
!
ip vrf Tenant 2
 description Routing Domain for PRIVATE peering to Azure for Tenant 2
 rd 1:5
!
crypto ikev2 proposal V2-PROPOSAL2 
description IKEv2 proposal for Tenant 1 
encryption aes-cbc-256
 integrity sha256
 group 2
crypto ikev2 proposal V4-PROPOSAL2 
description IKEv2 proposal for Tenant 2 
encryption aes-cbc-256
 integrity sha256
 group 2
!
crypto ikev2 policy V2-POLICY2 
description IKEv2 Policy for Tenant 1 
match fvrf Tenant 1
 match address local 10.60.3.255
 proposal V2-PROPOSAL2
description IKEv2 Policy for Tenant 2
crypto ikev2 policy V4-POLICY2 
 match fvrf Tenant 2
 match address local 10.60.3.251
 proposal V4-PROPOSAL2
!
crypto ikev2 profile V2-PROFILE
description IKEv2 profile for Tenant 1 
match fvrf Tenant 1
 match address local 10.60.3.255
 match identity remote any
 authentication remote pre-share key abc123
 authentication local pre-share key abc123
 ivrf Tenant 1
!
crypto ikev2 profile V4-PROFILE
description IKEv2 profile for Tenant 2
 match fvrf Tenant 2
 match address local 10.60.3.251
 match identity remote any
 authentication remote pre-share key abc123
 authentication local pre-share key abc123
 ivrf Tenant 2
!
crypto ipsec transform-set V2-TRANSFORM2 esp-gcm 256 
 mode tunnel
crypto ipsec transform-set V4-TRANSFORM2 esp-gcm 256 
 mode tunnel
!
crypto ipsec profile V2-PROFILE
 set transform-set V2-TRANSFORM2 
 set ikev2-profile V2-PROFILE
!
crypto ipsec profile V4-PROFILE
 set transform-set V4-TRANSFORM2 
 set ikev2-profile V4-PROFILE
!
interface Tunnel10
description S2S VPN Tunnel for Tenant 1
 ip vrf forwarding Tenant 1
 ip address 11.0.0.2 255.255.255.252
 ip tcp adjust-mss 1350
 tunnel source TenGigabitEthernet0/1/0.211
 tunnel mode ipsec ipv4
 tunnel destination 10.10.0.62
 tunnel vrf Tenant 1
 tunnel protection ipsec profile V2-PROFILE
!
interface Tunnel20
description S2S VPN Tunnel for Tenant 2
 ip vrf forwarding Tenant 2
 ip address 11.0.0.2 255.255.255.252
 ip tcp adjust-mss 1350
 tunnel source TenGigabitEthernet0/1/0.213
 tunnel mode ipsec ipv4
 tunnel destination 10.10.0.62
 tunnel vrf VNET3
 tunnel protection ipsec profile V4-PROFILE
!
interface GigabitEthernet0/0/1
 description PRIMARY Express Route Link to AZURE over Equinix
 no ip address
 negotiation auto
!
interface GigabitEthernet0/0/1.100
description Primary WAN interface of Tenant 1
 description PRIMARY ER link supporting Tenant 1 to Azure
 encapsulation dot1Q 101
 ip vrf forwarding Tenant 1
 ip address 192.168.1.1 255.255.255.252
!
interface GigabitEthernet0/0/1.102
description Primary WAN interface of Tenant 2
 description PRIMARY ER link supporting Tenant 2 to Azure
 encapsulation dot1Q 102
 ip vrf forwarding Tenant 2
 ip address 192.168.1.17 255.255.255.252
!
interface GigabitEthernet0/0/2
 description BACKUP Express Route Link to AZURE over Equinix
 no ip address
 negotiation auto
!
interface GigabitEthernet0/0/2.100
description Secondary WAN interface of Tenant 1
 description BACKUP ER link supporting Tenant 1 to Azure
 encapsulation dot1Q 101
 ip vrf forwarding Tenant 1
 ip address 192.168.1.5 255.255.255.252
!
interface GigabitEthernet0/0/2.102
description Secondary WAN interface of Tenant 2 
description BACKUP ER link supporting Tenant 2 to Azure
 encapsulation dot1Q 102
 ip vrf forwarding Tenant 2
 ip address 192.168.1.21 255.255.255.252
!
interface TenGigabitEthernet0/1/0
 description Downlink to ---Port 1/47
 no ip address
!
interface TenGigabitEthernet0/1/0.211
 description LAN interface of Tenant 1
description Downlink to --- Port 1/47.211
 encapsulation dot1Q 211
 ip vrf forwarding Tenant 1
 ip address 10.60.3.255 255.255.255.254
!
interface TenGigabitEthernet0/1/0.213
description LAN interface of Tenant 2
 description Downlink to --- Port 1/47.213
 encapsulation dot1Q 213
 ip vrf forwarding Tenant 2
 ip address 10.60.3.251 255.255.255.254
!
router bgp 65530
 bgp router-id <removed>
 bgp log-neighbor-changes
 description BGP neighbor config and route advertisement for Tenant 1 VRF 
 address-family ipv4 vrf Tenant 1
  network 10.1.0.0 mask 255.255.0.0
  network 10.60.3.254 mask 255.255.255.254
  network 192.168.1.0 mask 255.255.255.252
  network 192.168.1.4 mask 255.255.255.252
  neighbor 10.10.0.62 remote-as 65100
  neighbor 10.10.0.62 description VPN-BGP-PEER-for-Tenant 1
  neighbor 10.10.0.62 ebgp-multihop 5
  neighbor 10.10.0.62 activate
  neighbor 10.60.3.254 remote-as 4232570301
  neighbor 10.60.3.254 description LAN peer for CPEC:INET:2112 VRF
  neighbor 10.60.3.254 activate
  neighbor 10.60.3.254 route-map BLOCK-ALL out
  neighbor 192.168.1.2 remote-as 12076
  neighbor 192.168.1.2 description PRIMARY ER peer for Tenant 1 to Azure
  neighbor 192.168.1.2 ebgp-multihop 5
  neighbor 192.168.1.2 activate
  neighbor 192.168.1.2 soft-reconfiguration inbound
  neighbor 192.168.1.2 route-map Tenant 1-ONLY out
  neighbor 192.168.1.6 remote-as 12076
  neighbor 192.168.1.6 description BACKUP ER peer for Tenant 1 to Azure
  neighbor 192.168.1.6 ebgp-multihop 5
  neighbor 192.168.1.6 activate
  neighbor 192.168.1.6 soft-reconfiguration inbound
  neighbor 192.168.1.6 route-map Tenant 1-ONLY out
  maximum-paths 8
 exit-address-family
 !
description BGP neighbor config and route advertisement for Tenant 2 VRF 
address-family ipv4 vrf Tenant 2
  network 10.1.0.0 mask 255.255.0.0
  network 10.60.3.250 mask 255.255.255.254
  network 192.168.1.16 mask 255.255.255.252
  network 192.168.1.20 mask 255.255.255.252
  neighbor 10.10.0.62 remote-as 65300
  neighbor 10.10.0.62 description VPN-BGP-PEER-for-Tenant 2
  neighbor 10.10.0.62 ebgp-multihop 5
  neighbor 10.10.0.62 activate
  neighbor 10.60.3.250 remote-as 4232570301
  neighbor 10.60.3.250 description LAN peer for CPEC:INET:2112 VRF
  neighbor 10.60.3.250 activate
  neighbor 10.60.3.250 route-map BLOCK-ALL out
  neighbor 192.168.1.18 remote-as 12076
  neighbor 192.168.1.18 description PRIMARY ER peer for Tenant 2 to Azure
  neighbor 192.168.1.18 ebgp-multihop 5
  neighbor 192.168.1.18 activate
  neighbor 192.168.1.18 soft-reconfiguration inbound
  neighbor 192.168.1.18 route-map VNET-ONLY out
  neighbor 192.168.1.22 remote-as 12076
  neighbor 192.168.1.22 description BACKUP ER peer for Tenant 2 to Azure
  neighbor 192.168.1.22 ebgp-multihop 5
  neighbor 192.168.1.22 activate
  neighbor 192.168.1.22 soft-reconfiguration inbound
  neighbor 192.168.1.22 route-map VNET-ONLY out
  maximum-paths 8
 exit-address-family
!
ip forward-protocol nd
!
ip as-path access-list 1 permit ^$
ip route vrf Tenant 1 10.1.0.0 255.255.0.0 Tunnel10
ip route vrf Tenant 2 10.1.0.0 255.255.0.0 Tunnel20
!
ip prefix-list BLOCK-ALL seq 5 deny 0.0.0.0/0 le 32
!
route-map BLOCK-ALL permit 10
 match ip address prefix-list BLOCK-ALL
!
route-map VNET-ONLY permit 10
 match as-path 1
!
```

## <a name="test-the-connection"></a>Testa anslutningen

Testa anslutningen efter att plats-till-plats-anslutning och ExpressRoute-kretsen. Den här uppgiften är enkelt.  Logga in på någon av de virtuella datorerna som du skapade i ditt Azure VNet och pinga den virtuella datorn som du skapade i Azure Stack-miljö, och vice versa. 

För att säkerställa att du skickar trafik via plats-till-plats och ExpressRoute-anslutningar, måste du pinga den dedicerade IP-Adressen (DIP)-adressen för den virtuella datorn i slutet av både och inte VIP-adressen för den virtuella datorn. Du måste därför hitta och anteckna adressen på den andra änden av anslutningen.

### <a name="allow-icmp-in-through-the-firewall"></a>Tillåta ICMP i genom brandväggen
Som standard tillåter inte Windows Server 2016 ICMP-paket i genom brandväggen. Så för varje virtuell dator som du använder i testet, kör du följande cmdlet i ett upphöjt PowerShell-fönster:


   ```
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

### <a name="ping-the-azure-stack-virtual-machine"></a>Pinga virtuella Azure-stacken

1. Logga in på Azure-stacken användarportalen med ett klient-konto.
2. Klicka på **Virtuella datorer** i det vänstra navigeringsfältet.
3. Hitta den virtuella datorn som du skapade tidigare och klicka på den.
4. Klicka på avsnittet för den virtuella datorn **Anslut**.
5. Öppna en upphöjd PowerShell och skriv **ipconfig/all**.
6. Hitta IPv4-adress i utdata och notera den. Pinga denna adress från den virtuella datorn i Azure VNet. I exempel-miljö är adressen från undernätet som 10.1.1.x/24. Adressen kan vara annorlunda i din miljö. Det bör dock i undernätet som du skapade för klient-VNet-undernätet.


### <a name="view-data-transfer-statistics"></a>Visa statistik för överföring av data

Om du vill veta hur mycket trafik som passerar genom din anslutning hittar den här informationen i avsnittet anslutning i Azure-stacken användarportalen. Den här informationen är också ett annat bra sätt att kontrollera att ping som skickas bara faktiskt har gått igenom VPN- och ExpressRoute-anslutningarna.

1. Logga in på Microsoft Azure-stacken användarportalen med ditt innehavarkonto.
2. Navigera till resursgruppen där din VPN-Gateway har skapats och markera den **anslutningar** objekttypen.
3. Klicka på den **ConnectToAzure** anslutningen i listan.
4. På den **anslutning** avsnitt, kan du visa statistik för **Data i** och **ut Data**. Du bör se några nollvärden där.

   ![Data i ut Data](media/azure-stack-connect-expressroute/DataInDataOut.png)

## <a name="next-steps"></a>Nästa steg
[Distribuera appar till Azure och Azure Stack](azure-stack-solution-pipeline.md)
