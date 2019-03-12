---
title: Ansluta Azure Stack till Azure med ExpressRoute
description: Lär dig hur du ansluter virtuella nätverk i Azure Stack till virtuella nätverk i Azure med ExpressRoute.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 10/22/2018
ms.openlocfilehash: 3f23f62554ce7f4b90b4116fdd6085027e71650d
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2019
ms.locfileid: "57770177"
---
# <a name="connect-azure-stack-to-azure-using-azure-expressroute"></a>Ansluta Azure Stack till Azure med Azure ExpressRoute

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

Den här artikeln visar hur du ansluter ett virtuellt nätverk för Azure Stack till ett Azure-nätverk med hjälp av en [Microsoft Azure ExpressRoute](/azure/expressroute/) direkt anslutning.

Du kan använda den här artikeln som en vägledning och använda exemplen för att ställa in samma testmiljön. Eller du kan använda i artikeln som en genomgång som hjälper dig att konfigurera din egen ExpressRoute-miljö.

## <a name="overview-assumptions-and-prerequisites"></a>Översikt över, förutsättningar och krav

Azure ExpressRoute kan du utöka ditt lokala nätverk till Microsoft-molnet över en privat anslutning som tillhandahålls av en anslutningsprovider. ExpressRoute är inte en VPN-anslutning via det offentliga internet.

Läs mer om Azure ExpressRoute, den [översikt över ExpressRoute](../expressroute/expressroute-introduction.md).

### <a name="assumptions"></a>Antaganden

Den här artikeln förutsätter att:

* Du har kunskaper om Azure.
* Du har grundläggande kunskaper om Azure Stack.
* Du har en grundläggande förståelse för nätverk.

### <a name="prerequisites"></a>Förutsättningar

För att ansluta Azure Stack och Azure med ExpressRoute, måste du uppfylla följande krav:

* En etablerad [ExpressRoute-krets](../expressroute/expressroute-circuit-peerings.md) via en [anslutningsleverantör](../expressroute/expressroute-locations.md).
* En Azure-prenumeration att skapa en ExpressRoute-krets och virtuella nätverk i Azure.
* En router som måste:
  * Stöd för plats-till-plats-VPN-anslutningar mellan LAN-gränssnitt och Azure Stack Multitenant Gateway.
  * Stöd för att skapa flera VRFs (virtuella Routning och vidarebefordran) om det finns fler än en klient i Azure Stack-distributioner.
* En router som har:
  * En WAN-port som är anslutna till ExpressRoute-kretsen.
  * En LAN-port som är anslutna till Azure Stack Multitenant Gateway.

### <a name="expressroute-network-architecture"></a>ExpressRoute nätverksarkitektur

I följande diagram visas de Azure Stack och Azure-miljöer när du har slutfört konfigurationen av ExpressRoute med exempel i den här artikeln:

*Bild 1. ExpressRoute-nätverket*

![ExpressRoute-nätverket](media/azure-stack-connect-expressroute/Conceptual.png)

Följande diagram visar hur flera innehavare ansluta från Azure Stack-infrastruktur via ExpressRoute-router till Azure på Microsoft edge:

*Figur 2. Anslutningar för flera innehavare*

![Flera innehavare anslutningar med ExpressRoute](media/azure-stack-connect-expressroute/Architecture.png)

I exemplet i den här artikeln använder samma arkitektur för flera innehavare som visas i figur 2 för att ansluta Azure Stack till Azure med ExpressRoute privat peering. Anslutningen görs med hjälp av en plats-till-plats VPN-anslutning från den virtuella nätverksgatewayen i Azure Stack till en ExpressRoute-router.

Stegen i den här artikeln visar hur du skapar en slutpunkt till slutpunkt-anslutning mellan två virtuella nätverk från två olika klienter i Azure Stack allt till motsvarande virtuella nätverk i Azure. Ställa in två klienter är valfritt. Du kan också använda de här stegen för en enda klient.

## <a name="configure-azure-stack"></a>Konfigurera Azure Stack

Om du vill konfigurera Azure Stack-miljön för den första klienten använder du stegen i följande diagram som vägledning. Om du konfigurerar mer än en klient, Upprepa dessa steg:

>[!NOTE]
>De här stegen visar hur du skapar resurser med hjälp av Azure Stack-portalen, men du kan också använda PowerShell.

![Konfigurationen av Azure Stack-nätverk](media/azure-stack-connect-expressroute/image2.png)

### <a name="before-you-begin"></a>Innan du börjar

Innan du börjar konfigurera Azure Stack, behöver du:

* En distribution för integrerade Azure Stack-system eller en distribution för Azure Stack Development Kit (ASDK). Information om hur du distribuerar ASDK finns i den [Snabbstart för utveckling med Azure Stack Development Kit](azure-stack-deploy-overview.md).
* Ett erbjudande i Azure Stack som dina användare kan prenumerera. Mer information finns i [planer, erbjudanden och prenumerationer](azure-stack-plan-offer-quota-overview.md).

### <a name="create-network-resources-in-azure-stack"></a>Skapa nätverksresurser i Azure Stack

Använd följande procedurer för att skapa de nödvändiga nätverksresurserna i Azure Stack för en klient.

#### <a name="create-the-virtual-network-and-vm-subnet"></a>Skapa ett undernät för det virtuella nätverket och den virtuella datorn

1. Logga in på användarportalen med ett användarkonto (klient).

2. I portalen, väljer **+ skapa en resurs**.

3. Under **Azure Marketplace**väljer **nätverk**.

4. Under **aktuell**väljer **virtuellt nätverk**.

5. Under **skapa virtuellt nätverk**, ange de värden som visas i följande tabell i lämpliga fält:

   |Fält  |Värde  |
   |---------|---------|
   |Namn     |Tenant1VNet1         |
   |Adressutrymme     |10.1.0.0/16|
   |Namn på undernät     |Tenant1-Sub1|
   |Adressintervall för undernätet     |10.1.1.0/24|

6. Du bör se prenumerationen du skapade tidigare i den **prenumeration** fält. För återstående fält:

    * Under **resursgrupp**väljer **Skapa nytt** att skapa en ny resursgrupp eller om du redan har en, Välj **Använd befintlig**.
    * Verifiera förvalet **plats**.
    * Klicka på **Skapa**.
    * (Valfritt) Klicka på **fäst på instrumentpanelen**.

#### <a name="create-the-gateway-subnet"></a>Skapa gateway-undernätet

1. Under **virtuellt nätverk**väljer **Tenant1VNet1**.
1. Under **INSTÄLLNINGAR** väljer du **Undernät**.
1. Välj **+ Gateway-undernät** att lägga till ett gateway-undernät till det virtuella nätverket.
1. Namnet på undernätet ställs in på **GatewaySubnet** som standard. Gateway-undernät är ett specialfall och måste använda det här namnet ska fungera korrekt.
1. Kontrollera att den **adressintervall** är **10.1.0.0/24**.
1. Klicka på **OK** att skapa gateway-undernätet.

#### <a name="create-the-virtual-network-gateway"></a>Skapa den virtuella nätverksgatewayen

1. I Azure Stack-portalen för användaren klickar du på **+ skapa en resurs**.
1. Under **Azure Marketplace**väljer **nätverk**.
1. Välj **Virtuell nätverksgateway** från listan med nätverksresurser.
1. I den **namn** anger **GW1**.
1. Välj **virtuellt nätverk**.
1. Välj **Tenant1VNet1** från den nedrullningsbara listan.
1. Välj **offentliga IP-adressen**, sedan **Välj offentlig IP-adress**, och klicka sedan på **Skapa ny**.
1. I den **namn** skriver **GW1-PiP**, och klicka sedan på **OK**.
1. **VPN-typen**  ska ha **Routningsbaserad** valt som standard. Behåll den inställningen.
1. Verifiera att **Prenumeration** och **Plats** stämmer. Klicka på **Skapa**.

#### <a name="create-the-local-network-gateway"></a>Skapa den lokala nätverksgatewayen

Lokala gateway-resurs identifierar en fjärrgateway i den andra änden av VPN-anslutningen. I det här exemplet är fjärransluten änden av anslutningen LAN-undergränssnittet för ExpressRoute-routern. För klient-1, visas i bild 2, är fjärradressen 10.60.3.255.

1. Logga in på användarportalen för Azure Stack med ditt konto och välj **+ skapa en resurs**.
1. Under **Azure Marketplace**väljer **nätverk**.
1. Välj **lokal nätverksgateway** från listan med resurser.
1. I den **namn** skriver **ER-Router-GW**.
1. För den **IP-adress** fältet, se figur 2. IP-adressen för LAN-undergränssnittet ExpressRoute router för klient 1 är 10.60.3.255. Ange IP-adressen för motsvarande din router-gränssnitt för din egen miljö.
1. I den **adressutrymme** fältet, anger du adressutrymmet för de virtuella nätverken som du vill ansluta till i Azure. Undernät för klient 1 i *bild 2* är följande:

   * 192.168.2.0/24 är det virtuella hubbnätverket i Azure.
   * 10.100.0.0/16 är eker virtuellt nätverk i Azure.

   > [!IMPORTANT]
   > Det här exemplet förutsätts att du använder statiska vägar för plats-till-plats VPN-anslutning mellan Azure Stack-gateway och ExpressRoute-router.

1. Kontrollera att din **prenumeration**, **resursgrupp**, och **plats** är korrekta. Klicka sedan på **Skapa**.

#### <a name="create-the-connection"></a>Skapa anslutningen

1. Välj i användarportalen för Azure Stack **+ skapa en resurs**.
1. Under **Azure Marketplace**väljer **nätverk**.
1. Välj **Anslutning** i listan över resurser.
1. Under **grunderna**, Välj **plats-till-plats (IPSec)** som den **anslutningstypen**.
1. Välj den **prenumeration**, **resursgrupp**, och **plats**. Klicka på **OK**.
1. Under **inställningar**väljer **virtuell nätverksgateway**, och välj sedan **GW1**.
1. Välj **lokal nätverksgateway**, och välj sedan **ER Router GW**.
1. I den **anslutningsnamn** anger **ConnectToAzure**.
1. I den **delad nyckel (PSK)** anger **abc123** och välj sedan **OK**.
1. Under **sammanfattning**väljer **OK**.

#### <a name="get-the-virtual-network-gateway-public-ip-address"></a>Hämta virtuella nätverk gatewayens offentliga IP-adress

När du har skapat den virtuella nätverksgatewayen som du kan hämta gatewayens offentliga IP-adressen. Anteckna den här adressen om du behöver den senare för din distribution. Beroende på din distribution, den här adressen används som den **interna IP-adress**.

1. Välj i användarportalen för Azure Stack **alla resurser**.
1. Under **alla resurser**, Välj den virtuella nätverksgatewayen, vilket är **GW1** i det här exemplet.
1. Under **virtuell nätverksgateway**väljer **översikt** från listan över resurser. Du kan även välja **egenskaper**.
1. IP-adressen som du vill Observera anges under **offentliga IP-adressen**. Den här adressen är 192.68.102.1 för exempel-konfigurationen.

#### <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Testa trafik via VPN-anslutningen, behöver du virtuella datorer för att skicka och ta emot data i Azure Stack virtuella nätverk. Skapa en virtuell dator och distribuera den till VM-undernät för det virtuella nätverket.

1. Välj i användarportalen för Azure Stack **+ skapa en resurs**.
1. Under **Azure Marketplace**väljer **Compute**.
1. I listan över avbildningar av virtuella datorer, väljer du den **Windows Server 2016 Datacenter utvärdering** bild.

   >[!NOTE]
   >Om den avbildning som användes i den här artikeln inte är tillgänglig, kan du be Azure Stack-operatör att ange en annan Windows Server-avbildning.

1. I **Skapa virtuell dator**väljer **grunderna**, Skriv **VM01** som den **namn**.
1. Ange ett giltigt användarnamn och lösenord. Det här kontot ska du använda för att logga in på den virtuella datorn när den har skapats.
1. Ange en **prenumeration**, **resursgrupp**, och en **plats**. Välj **OK**.
1. Under **väljer du en storlek**, välja en VM-storlek för den här instansen och välj sedan **Välj**.
1. Under **inställningar**, bekräftar du att:

   * Det virtuella nätverket är **Tenant1VNet1**.
   * Att undernätet är inställt på **10.1.1.0/24**.

   Använd standardinställningarna och klicka på **OK**.

1. Under **sammanfattning**granskar VM-konfigurationen och klickar sedan på **OK**.

Upprepa steg som i följande avsnitt för att lägga till fler klienter:

* [Skapa virtuellt nätverk och undernät för virtuella datorer](#create-the-virtual-network-and-vm-subnet)
* [Skapa gateway-undernätet](#create-the-gateway-subnet)
* [Skapa den virtuella nätverksgatewayen](#create-the-virtual-network-gateway)
* [Skapa lokal nätverksgateway](#create-the-local-network-gateway)
* [Skapa anslutningen](#create-the-connection)
* [Skapa en virtuell dator](#create-a-virtual-machine)

Om du använder klient 2 som ett exempel kan du komma ihåg att ändra IP-adresser för att undvika överlappningar.

### <a name="configure-the-nat-virtual-machine-for-gateway-traversal"></a>Konfigurera den virtuella datorn i NAT för gatewayöverträdelse

> [!IMPORTANT]
> Det här avsnittet avser endast Azure Stack Development Kit (ASDK)-distributioner. NAT behövs inte för distributioner med flera noder.

Azure Stack Development Kit är fristående och isolerad från nätverket där den fysiska värden har distribuerats. VIP-nätverket som gatewayerna är anslutna till är inte externt, det är dolt bakom en router som utför NAT (Network adress Translation).

Routern är en Windows Server-dator (AzS-BGPNAT01) som kör rollen Routing och Remote Access Services (RRAS). Du måste konfigurera NAT på den virtuella datorn i AzS-BGPNAT01 ska aktivera VPN-anslutningen för plats-till-plats att ansluta i båda ändar.

#### <a name="configure-the-nat"></a>Konfigurera NAT

1. Logga in på Azure Stack-värddatorn med ditt administratörskonto.
1. Kopiera och redigera följande PowerShell-skript. Ersätt `"your administrator password"` med administratörslösenordet och kör skriptet i en upphöjd PowerShell ISE. Det här skriptet returnerar din **externa BGPNAT adress**.

   ```PowerShell
   cd \AzureStack-Tools-master\connect
   Import-Module .\AzureStack.Connect.psm1
   $Password = ConvertTo-SecureString "your administrator password" `
    -AsPlainText `
    -Force
   Get-AzureStackNatServerAddress `
    -HostComputer "azs-bgpnat01" `
    -Password $Password
   ```

1. Om du vill konfigurera NAT, kopiera och redigera följande PowerShell-skript. Redigera skriptet för att ersätta den `'External BGPNAT address'` och `'Internal IP address'` med följande exempelvärden:

   * För *externa BGPNAT adress* använder 10.10.0.62
   * För *interna IP-adress* använder 192.168.102.1

   Kör följande skript från en upphöjd PowerShell ISE:

   ```PowerShell
   $ExtBgpNat = 'External BGPNAT address'
   $IntBgpNat = 'Internal IP address'

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
   # Create a static NAT mapping to map the external address to the Gateway public IP address to map the ISAKMP port 500 for PHASE 1 of the IPSEC tunnel.
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress $Using:ExtBgpNat `
      -InternalIPAddress $Using:IntBgpNat `
      -ExternalPort 500 `
      -InternalPort 500}
   # Configure NAT traversal which uses port 4500 to  establish the complete IPSEC tunnel over NAT devices.
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

När du har konfigurerat Azure Stack, kan du distribuera Azure-resurser. Följande bild visar ett exempel på ett virtuellt klientnätverk i Azure. Du kan använda alla namn och adresseringsschema för ditt virtuella nätverk i Azure. Adressintervallet för de virtuella nätverken i Azure och Azure Stack måste vara unikt och får inte överlappa varandra.

*Bild 3. Virtuella Azure-nätverk*

![Azure VNets](media/azure-stack-connect-expressroute/AzureArchitecture.png)

De resurser som du distribuerar i Azure liknar de resurser som du distribuerade i Azure Stack. Du ska distribuera följande komponenter:

* Virtuella nätverk och undernät
* Ett gateway-undernät
* En virtuell nätverksgateway
* En anslutning
* En ExpressRoute-krets

Exemplet Azure nätverksinfrastrukturen är konfigurerad på följande sätt:

* En standard hub (192.168.2.0/24) och eker (10.100.0.0./16) VNet-modellen. Mer information om en hub-spoke för nätverk finns i [implementerar en hub-spoke för nätverk i Azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke).
* Arbetsbelastningarna som distribueras i eker virtuellt nätverk och ExpressRoute-kretsen är ansluten till det virtuella hubbnätverket.
* De två virtuella nätverken är anslutna med VNet-peering.

### <a name="configure-the-azure-vnets"></a>Konfigurera den virtuella Azure-nätverken

1. Logga in på Azure-portalen med dina autentiseringsuppgifter för Azure.
1. Skapa det virtuella hubbnätverket med 192.168.2.0/24-adressintervall.
1. Skapa ett undernät med 192.168.2.0/25-adressintervall och Lägg till en gateway-undernät med 192.168.2.128/27 adressintervallet.
1. Skapa ekern VNet och undernät med hjälp av 10.100.0.0/16 adressintervall.

Mer information om hur du skapar virtuella nätverk i Azure finns i [skapa ett virtuellt nätverk](../virtual-network/manage-virtual-network.md#create-a-virtual-network).

### <a name="configure-an-expressroute-circuit"></a>Konfigurera en ExpressRoute-krets

1. Gå igenom kraven för ExpressRoute i [ExpressRoute-krav och checklista](../expressroute/expressroute-prerequisites.md).

1. Följ stegen i [skapa och ändra en ExpressRoute-krets](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) att skapa en ExpressRoute-krets med hjälp av din Azure-prenumeration.

   >[!NOTE]
   >Ge tjänstnyckeln för din krets till din tjänst så att de kan ställa in din ExpressRoute-krets sina slutet.

1. Följ stegen i [skapa och ändra peering för en ExpressRoute-krets](../expressroute/expressroute-howto-routing-portal-resource-manager.md) att konfigurera privat peering i ExpressRoute-kretsen.

### <a name="create-the-virtual-network-gateway"></a>Skapa den virtuella nätverksgatewayen

Följ stegen i [konfigurera en virtuell nätverksgateway för ExpressRoute med PowerShell](../expressroute/expressroute-howto-add-gateway-resource-manager.md) skapar en virtuell nätverksgateway för ExpressRoute i det virtuella hubbnätverket.

### <a name="create-the-connection"></a>Skapa anslutningen

Om du vill länka ExpressRoute-kretsen till det virtuella hubbnätverket, följer du stegen i [ansluta ett virtuellt nätverk till en ExpressRoute-krets](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md).

### <a name="peer-the-vnets"></a>Peerkoppla de virtuella nätverken

Peer-NAV och ekrar virtuella nätverk med stegen i [skapa vnet-peering med hjälp av Azure portal](../virtual-network/virtual-networks-create-vnetpeering-arm-portal.md). När du konfigurerar VNet-peering kan du kontrollera att du använder du följande alternativ:

* Från hubben till ekern **Tillåt gatewayöverföring**.
* Från eker till hubb, **Använd fjärrgateway**.

### <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Distribuera dina virtuella datorer för arbetsbelastningen i virtuellt ekernätverk.

Upprepa dessa steg för alla ytterligare klient virtuella nätverk som du vill ansluta i Azure via sina respektive ExpressRoute-kretsar.

## <a name="configure-the-router"></a>Konfigurera routern

Du kan använda följande konfigurationsdiagram för ExpressRoute-router som en vägledning för att konfigurera din ExpressRoute-Router. Det här diagrammet visar två klientorganisationer (klient 1 och 2 för klient) med deras respektive ExpressRoute-kretsar. Varje klient är länkad till sina egna VRF (virtuella Routning och vidarebefordran) i LAN och WAN-sida av ExpressRoute-router. Den här konfigurationen garanterar slutpunkt till slutpunkt isolering mellan de två innehavarna. Anteckna IP-adresser som används i router-gränssnitt du följer konfigurationsexemplet.

*Bild 4. Konfiguration av ExpressRoute-router*

![Konfiguration av ExpressRoute-router](media/azure-stack-connect-expressroute/EndToEnd.png)

Du kan använda en router som har stöd för IKEv2 VPN och BGP om du vill avsluta plats-till-plats VPN-anslutning från Azure Stack. Samma router används för att ansluta till Azure med en ExpressRoute-krets.

Följande Konfigurationsexempel för Cisco ASR 1000 Series aggregering tjänster Router stöder nätverksinfrastruktur som visas i den *ExpressRoute routerkonfiguration* diagram.

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
 description PRIMARY ExpressRoute Link to AZURE over Equinix
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
 description BACKUP ExpressRoute Link to AZURE over Equinix
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

Testa anslutningen när du har skapat anslutningen för plats-till-plats och ExpressRoute-kretsen.

Utför följande ping-test:

* Logga in till någon av de virtuella datorerna i ditt Azure VNet och pinga den virtuella datorn som du skapade i Azure Stack.
* Logga in på någon av de virtuella datorerna som du skapade i Azure Stack och pinga den virtuella datorn som du skapade i Azure VNet.

>[!NOTE]
>Om du vill se till att du skickar trafik över plats-till-plats och ExpressRoute-anslutningar, måste du pinga den dedikerade IP (DIP)-adressen för den virtuella datorn i både ändar och inte VIP-adressen för den virtuella datorn.

### <a name="allow-icmp-in-through-the-firewall"></a>Tillåta ICMP i genom brandväggen

Som standard tillåter Windows Server 2016 inte inkommande ICMP-paket genom brandväggen. Du måste tillåta inkommande ICMP-paket för varje virtuell dator som du använder för Pingtest. Om du vill skapa en brandväggsregel för ICMP, kör du följande cmdlet i en upphöjd PowerShell-fönster:

```PowerShell
# Create ICMP firewall rule.
New-NetFirewallRule `
  –DisplayName “Allow ICMPv4-In” `
  –Protocol ICMPv4
```

### <a name="ping-the-azure-stack-virtual-machine"></a>Pinga den virtuella datorn i Azure Stack

1. Logga in på användarportalen för Azure Stack med ett klientkonto.

1. Hitta den virtuella datorn som du skapade och välj den virtuella datorn.

1. Välj **Anslut**.

1. Från en Windows- eller PowerShell kommandotolk, ange **ipconfig/all**. Anteckna IPv4-adressen som returneras i resultatet.

1. Pinga IPv4-adress från den virtuella datorn i Azure VNet.

   I exempelmiljön är IPv4-adress från 10.1.1.x/24 undernät. Adressen kan skilja sig i din miljö, men det bör vara i det undernät du skapade för klienten VNet-undernät.

### <a name="view-data-transfer-statistics"></a>Visa statistik för överföring av data

Om du vill veta hur mycket trafik som passerar genom din anslutning hittar den här informationen på Azure Stack-användarportalen. Det här är ett bra sätt att ta reda på om huruvida din ping testdata har gått igenom VPN och ExpressRoute-anslutningar:

1. Logga in på användarportalen för Azure Stack med klientkontot och välj **alla resurser**.
1. Navigera till resursgruppen för din VPN-Gateway och markera den **anslutning** objekttyp.
1. Välj den **ConnectToAzure** anslutningen i listan.
1. Under **anslutningar**>**översikt**, du kan se statistik för **Data i** och **Data ut**. Du bör se några nollvärden.

   ![Data i och Data ut](media/azure-stack-connect-expressroute/DataInDataOut.png)

## <a name="next-steps"></a>Nästa steg

[Distribuera appar till Azure och Azure Stack](azure-stack-solution-pipeline.md)
