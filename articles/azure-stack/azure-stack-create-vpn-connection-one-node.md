---
title: Skapa en plats-till-plats VPN-anslutning mellan två virtuella nätverk i olika Azure Stack Development Kit-miljöer | Microsoft Docs
description: Stegvisa anvisningar som molnadministratör använder för att skapa en plats-till-plats VPN-anslutning mellan två nod Azure Stack Development Kit miljöer.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 3f1b4e02-dbab-46a3-8e11-a777722120ec
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/12/2018
ms.author: sethm
ms.reviewer: scottnap
ms.lastreviewed: 09/12/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 65bf3d7eb84f4a4e6fe6d74bd08c41ba4d9dd637
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55247229"
---
# <a name="create-a-site-to-site-vpn-connection-between-two-virtual-networks-in-different-azure-stack-development-kit-environments"></a>Skapa en plats-till-plats VPN-anslutning mellan två virtuella nätverk i olika miljöer i Azure Stack Development Kit
## <a name="overview"></a>Översikt
Den här artikeln visar hur du skapar en plats-till-plats VPN-anslutning mellan två virtuella nätverk i två separata miljöer i Azure Stack Development Kit. När du konfigurerar anslutningarna får du lära dig hur VPN-gatewayer i Azure Stack fungerar.

### <a name="connection-diagram"></a>Anslutningsdiagram
Följande diagram visar hur konfigurationen ska se ut när du är klar.

![Konfiguration för plats-till-plats VPN-anslutning](media/azure-stack-create-vpn-connection-one-node-tp2/OneNodeS2SVPN.png)

### <a name="before-you-begin"></a>Innan du börjar
Slutför konfigurationen genom att se till att du har följande innan du börjar:

* Två servrar och andra krav som uppfyller maskinvarukraven för Azure Stack Development Kit enligt beskrivningen i [snabbstarten: Utvärdera Azure Stack Development Kit](azure-stack-deploy-overview.md). 
* Den [Azure Stack Development Kit](https://azure.microsoft.com/overview/azure-stack/try/) distributionspaketet.

## <a name="deploy-the-azure-stack-development-kit-environments"></a>Distribuera Azure Stack Development Kit-miljöer
För att slutföra konfigurationen, måste du distribuera två Azure Stack Development Kit-miljöer.
> [!NOTE] 
> För varje Azure Stack Development Kit som du distribuerar, följer du de [distributionsanvisningarna](azure-stack-run-powershell-script.md). I den här artikeln, Azure Stack Development Kit-miljöer kallas *POC1* och *POC2*.


## <a name="prepare-an-offer-on-poc1-and-poc2"></a>Förbereda ett erbjudande på POC1 och POC2
Förbered ett erbjudande på både POC1 och POC2 så att en användare kan prenumerera på erbjudandet och distribuera virtuella datorer. Information om hur du skapar ett erbjudande finns i [tillgängliggör virtuella datorer för Azure Stack-användare](azure-stack-tutorial-tenant-vm.md).

## <a name="review-and-complete-the-network-configuration-table"></a>Granska och fylla i tabellen för konfiguration av nätverk
I följande tabell sammanfattas nätverkskonfigurationen för både Azure Stack Development Kit-miljöer. Stegen som visas efter tabellen att lägga till den externa BGPNAT-adressen som är specifik för ditt nätverk.

**Tabell för konfiguration av nätverk**
|   |POC1|POC2|
|---------|---------|---------|
|Namn på virtuellt nätverk     |VNET-01|VNET-02 |
|Virtuella nätverkets adressutrymme |10.0.10.0/23|10.0.20.0/23|
|Namn på undernät     |Undernät-01|Undernät-02|
|Adressintervall för undernätet|10.0.10.0/24 |10.0.20.0/24 |
|Gateway-undernät      |10.0.11.0/24|10.0.21.0/24|
|Extern BGPNAT adress     |         |         |

> [!NOTE]
> Externa BGPNAT IP-adresser i exempelmiljön är 10.16.167.195 för POC1 och 10.16.169.131 för POC2. Använd följande procedur för att bestämma externa BGPNAT IP-adresser för Azure Stack Development Kit-värdar och lägga till dem i tabellen ovan network configuration.


### <a name="get-the-ip-address-of-the-external-adapter-of-the-nat-vm"></a>Hämta IP-adressen för den externa adaptern för den virtuella datorns NAT
1. Logga in på den fysiska Azure Stack-datorn för POC1.
2. Redigera följande Powershell-kod för att ersätta administratörslösenordet och kör koden på POC-värden:

   ```powershell
   cd \AzureStack-Tools-master\connect
   Import-Module .\AzureStack.Connect.psm1
   $Password = ConvertTo-SecureString "<your administrator password>" `
    -AsPlainText `
    -Force
   Get-AzureStackNatServerAddress `
    -HostComputer "AzS-bgpnat01" `
    -Password $Password
   ```
3. Lägg till IP-adress i nätverket configuration tabellen som visas i föregående avsnitt.

4. Upprepa den här proceduren på POC2.

## <a name="create-the-network-resources-in-poc1"></a>Skapa nätverksresurser i POC1
Nu kan du skapa POC1 nätverksresurser som du behöver för att skapa din gateway. Följande instruktioner visar hur du skapar resurser med hjälp av användarportalen. Du kan också använda PowerShell-kod för att skapa resurser.

![Arbetsflöde som används för att skapa resurser](media/azure-stack-create-vpn-connection-one-node-tp2/image2.png)

### <a name="sign-in-as-a-tenant"></a>Logga in som en klient
En tjänstadministratör kan logga in som en klient att testa de planer, erbjudanden och prenumerationer som deras klientorganisationer använder. Om du inte redan har en, [skapa ett klientkonto](azure-stack-add-new-user-aad.md) innan du loggar in.

### <a name="create-the-virtual-network-and-vm-subnet"></a>Skapa ett undernät för det virtuella nätverket och den virtuella datorn
1. Använda ett klientkonto för att logga in på användarportalen.
2. Välj i användarportalen, **+ skapa en resurs**.
3. Gå till **Marketplace**, och välj sedan **nätverk**.
4. Välj **virtuellt nätverk**.
5. För **namn**, **adressutrymme**, **undernätsnamn**, och **undernätsadressintervall**, använder de värden som visas tidigare i nätverket konfigurationstabell med en.
6. I **prenumeration**, den prenumeration som du skapade tidigare visas.
7. För **resursgrupp**, du kan skapa en resursgrupp eller om du redan har en, Välj **Använd befintlig**.
8. Verifiera den förvalda platsen.
9. Välj **fäst till instrumentpanelen**.
10. Välj **Skapa**.

### <a name="create-the-gateway-subnet"></a>Skapa gateway-undernätet
1. Öppna resurs för VNET-01-virtuella nätverk som du skapade tidigare på instrumentpanelen.
2. På bladet **Inställningar** väljer du **Undernät**.
3. Om du vill lägga till ett gateway-undernät till det virtuella nätverket, Välj **Gatewayundernätet**.
   
    ![Lägg till gateway-undernät](media/azure-stack-create-vpn-connection-one-node-tp2/image4.png)

4. Som standard anges namnet på undernätet till **GatewaySubnet**.
   Gatewayundernät är speciella. För att fungera korrekt måste de använda den *GatewaySubnet* namn.
5. I **adressintervall**, kontrollera att adressen är **10.0.11.0/24**.
6. Välj **OK** att skapa gateway-undernätet.

### <a name="create-the-virtual-network-gateway"></a>Skapa den virtuella nätverksgatewayen
1. I Azure-portalen väljer du **+ skapa en resurs**. 
2. Gå till **Marketplace**, och välj sedan **nätverk**.
3. Listan med nätverksresurser, väljer **virtuell nätverksgateway**.
4. I **namn**, ange **GW1**.
5. Välj den **virtuellt nätverk** för att välja ett virtuellt nätverk.
   Välj **VNET-01** i listan.
6. Välj den **offentliga IP-adressen** menyalternativ. När den **Välj offentlig IP-adress** konfigurationsbladet öppnas, Välj **Skapa ny**.
7. I **namn**, ange **GW1-PiP**, och välj sedan **OK**.
8.  Som standard för **VPN-typ**, **routningsbaserad** har valts.
    Behåll den **routningsbaserad** VPN-typ.
9. Verifiera att **Prenumeration** och **Plats** stämmer. Du kan fästa resursen på instrumentpanelen. Välj **Skapa**.

### <a name="create-the-local-network-gateway"></a>Skapa den lokala nätverksgatewayen
Implementeringen av en *lokal nätverksgateway* i den här distributionen av Azure Stack-utvärderingen är lite annorlunda än i en befintlig Azure-distribution.

I Azure-distribution representerar en lokal nätverksgateway en fysisk enhet från en lokal (hos klienten) som används för att ansluta till en virtuell nätverksgateway i Azure. I den här utvärderingsdistributionen Azure Stack är båda ändar av anslutningen virtuella nätverksgatewayer!

Ett sätt att tänka detta mer allmänt är att den lokala gateway-nätverksresursen alltid anger en fjärrgateway i den andra änden av anslutningen. På grund av det sätt som har utformats för Azure Stack Development Kit kan måste du ange IP-adressen för det externa nätverkskortet på nätverksadresser (NAT) virtuell dator av andra Azure Stack Development Kit som den offentliga IP-adressen för den lokala nätverksgatewayen. Du kan sedan skapa NAT-mappningar på NAT-VM för att se till att båda ändar är korrekt anslutna.


### <a name="create-the-local-network-gateway-resource"></a>Skapa gateway-resursen lokalt nätverk
1. Logga in på den fysiska Azure Stack-datorn för POC1.
2. Välj i användarportalen, **+ skapa en resurs**.
3. Gå till **Marketplace**, och välj sedan **nätverk**.
4. Välj i listan över resurser **lokal nätverksgateway**.
5. I **namn**, ange **POC2-GW**.
6. I **IP-adress**, ange den externa BGPNAT-adressen för POC2. Den här adressen visas tidigare i tabellen network configuration.
7. I **adressutrymme**, adressutrymmet för det POC2 virtuella nätverk som du skapar senare, ange **10.0.20.0/23**.
8. Kontrollera att din **prenumeration**, **resursgrupp**, och **plats** är korrekta och välj sedan **skapa**.

### <a name="create-the-connection"></a>Skapa anslutningen
1. Välj i användarportalen, **+ skapa en resurs**.
2. Gå till **Marketplace**, och välj sedan **nätverk**.
3. Välj i listan över resurser **anslutning**.
4. På den **grunderna** inställningsbladet för den **anslutningstypen**väljer **plats-till-plats (IPSec)**.
5. Välj den **prenumeration**, **resursgrupp**, och **plats**, och välj sedan **OK**.
6. På den **inställningar** bladet väljer **virtuell nätverksgateway**, och välj sedan **GW1**.
7. Välj **lokal nätverksgateway**, och välj sedan **POC2-GW**.
8. I **anslutningsnamn**, ange **POC1-POC2**.
9. I **delad nyckel (PSK)**, ange **12345**, och välj sedan **OK**.
10. På den **sammanfattning** bladet väljer **OK**.

### <a name="create-a-vm"></a>Skapa en virtuell dator
För att validera data som överförs via VPN-anslutningen, måste de virtuella datorerna att skicka och ta emot data i varje Azure Stack Development Kit. Skapa en virtuell dator i POC1 nu och sedan placera den i VM-undernätet i det virtuella nätverket.

1. I Azure-portalen väljer du **+ skapa en resurs**.
2. Gå till **Marketplace**, och välj sedan **Compute**.
3. I listan över avbildningar av virtuella datorer, väljer du den **Windows Server 2016 Datacenter utvärdering** bild.
4. På den **grunderna** bladet i **namn**, ange **VM01**.
5. Ange ett giltigt användarnamn och lösenord. Du kan använda det här kontot för att logga in på den virtuella datorn när den har skapats.
6. Ange en **prenumeration**, **resursgrupp**, och **plats**, och välj sedan **OK**.
7. På den **storlek** bladet för den här instansen Välj en storlek på virtuell dator och därefter **Välj**.
8. På den **inställningar** bladet godkänna standardvärdena. Se till att den **VNET-01** virtuellt nätverk har valts. Kontrollera att undernätet är inställt på **10.0.10.0/24**. Välj sedan **OK**.
9. På den **sammanfattning** bladet granskar du inställningarna och välj sedan **OK**.



## <a name="create-the-network-resources-in-poc2"></a>Skapa nätverksresurser i POC2

Nästa steg är att skapa nätverksresurser för POC2. Följande instruktioner visar hur du skapar resurser med hjälp av användarportalen.

### <a name="sign-in-as-a-tenant"></a>Logga in som en klient
En tjänstadministratör kan logga in som en klient att testa de planer, erbjudanden och prenumerationer som deras klientorganisationer använder. Om du inte redan har en, [skapa ett klientkonto](azure-stack-add-new-user-aad.md) innan du loggar in.

### <a name="create-the-virtual-network-and-vm-subnet"></a>Skapa ett undernät för det virtuella nätverket och den virtuella datorn

1. Logga in med ett klientkonto.
2. Välj i användarportalen, **+ skapa en resurs**.
3. Gå till **Marketplace**, och välj sedan **nätverk**.
4. Välj **virtuellt nätverk**.
5. Använd informationen som visas tidigare i tabellen network configuration för att identifiera värdena för POC2 **namn**, **adressutrymme**, **undernätsnamn**, och **Undernätsadressintervall**.
6. I **prenumeration**, den prenumeration som du skapade tidigare visas.
7. För **resursgrupp**, skapa en ny resursgrupp eller, om du redan har en väljer **Använd befintlig**.
8. Verifiera förvalet **plats**.
9. Välj **fäst till instrumentpanelen**.
10. Välj **Skapa**.

### <a name="create-the-gateway-subnet"></a>Skapa gateway-undernätet
1. Öppna den virtuella nätverksresurs du skapade (**VNET-02**) från instrumentpanelen.
2. På bladet **Inställningar** väljer du **Undernät**.
3. Välj **gatewayundernätet** att lägga till ett gateway-undernät till det virtuella nätverket.
4. Namnet på undernätet ställs in på **GatewaySubnet** som standard.
   Gatewayundernät är speciella och måste ha det här specifika namnet för att fungera ordentligt.
5. I den **adressintervall** fältet, verifiera att adressen är **10.0.21.0/24**.
6. Välj **OK** att skapa gateway-undernätet.

### <a name="create-the-virtual-network-gateway"></a>Skapa den virtuella nätverksgatewayen
1. I Azure-portalen väljer du **+ skapa en resurs**.  
2. Gå till **Marketplace**, och välj sedan **nätverk**.
3. Listan med nätverksresurser, väljer **virtuell nätverksgateway**.
4. I **namn**, ange **GW2**.
5. Om du vill välja ett virtuellt nätverk, Välj **virtuellt nätverk**. Välj sedan **VNET-02** i listan.
6. Välj **Offentlig IP-adress**. När den **Välj offentlig IP-adress** konfigurationsbladet öppnas, Välj **Skapa ny**.
7. I **namn**, ange **GW2-PiP**, och välj sedan **OK**.
8. Som standard för **VPN-typ**, **routningsbaserad** har valts.
    Behåll den **routningsbaserad** VPN-typ.
9. Verifiera att **Prenumeration** och **Plats** stämmer. Du kan fästa resursen på instrumentpanelen. Välj **Skapa**.

### <a name="create-the-local-network-gateway-resource"></a>Skapa gateway-resursen lokalt nätverk

1. Välj i användarportalen POC2 **+ skapa en resurs**. 
4. Gå till **Marketplace**, och välj sedan **nätverk**.
5. Välj i listan över resurser **lokal nätverksgateway**.
6. I **namn**, ange **POC1-GW**.
7. I **IP-adress**, ange den externa BGPNAT-adressen för POC1 som anges tidigare i tabellen network configuration.
8. I **adressutrymme**, från POC1, ange den **10.0.10.0/23** adressutrymmet för **VNET-01**.
9. Kontrollera att din **prenumeration**, **resursgrupp**, och **plats** är korrekta och välj sedan **skapa**.

## <a name="create-the-connection"></a>Skapa anslutningen
1. Välj i användarportalen, **+ skapa en resurs**. 
2. Gå till **Marketplace**, och välj sedan **nätverk**.
3. Välj i listan över resurser **anslutning**.
4. På den **grundläggande** inställningsbladet för den **anslutningstypen**, Välj **plats-till-plats (IPSec)**.
5. Välj den **prenumeration**, **resursgrupp**, och **plats**, och välj sedan **OK**.
6. På den **inställningar** bladet väljer **virtuell nätverksgateway**, och välj sedan **GW2**.
7. Välj **lokal nätverksgateway**, och välj sedan **POC1-GW**.
8. I **anslutningsnamn**, ange **POC2-POC1**.
9. I **delad nyckel (PSK)**, ange **12345**. Om du väljer ett annat värde, kan du komma ihåg att den *måste* matchar värdet för den delade nyckeln som du skapade på POC1. Välj **OK**.
10. Granska den **sammanfattning** bladet och välj sedan **OK**.

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator
Skapa en virtuell dator i POC2 nu och placera den på VM-undernätet i det virtuella nätverket.

1. I Azure-portalen väljer du **+ skapa en resurs**.
2. Gå till **Marketplace**, och välj sedan **Compute**.
3. I listan över avbildningar av virtuella datorer, väljer du den **Windows Server 2016 Datacenter utvärdering** bild.
4. På den **grunderna** bladet för **namn**, ange **VM02**.
5. Ange ett giltigt användarnamn och lösenord. Du kan använda det här kontot för att logga in på den virtuella datorn när den har skapats.
6. Ange en **prenumeration**, **resursgrupp**, och **plats**, och välj sedan **OK**.
7. På den **storlek** bladet och välja en virtuell dator storlek för den här instansen och välj sedan **Välj**.
8. På den **inställningar** bladet du godkänna standardvärdena. Se till att den **VNET-02** virtuellt nätverk har valts och kontrollera att undernätet är inställt på **10.0.20.0/24**. Välj **OK**.
9. Granska inställningarna på den **sammanfattning** bladet och välj sedan **OK**.

## <a name="configure-the-nat-virtual-machine-on-each-azure-stack-development-kit-for-gateway-traversal"></a>Konfigurera NAT-VM på varje Azure Stack Development Kit för gatewayöverträdelse
Eftersom Azure Stack Development Kit är fristående och isolerad från nätverket där den fysiska värden har distribuerats i *externa* VIP-nätverket som gatewayerna är anslutna till inte är faktiskt extern. I stället är VIP-nätverket dold bakom en router som utför nätverksadresser. 

Routern är en Windows Server-dator som kallas *AzS-bgpnat01*, som kör rollen Routing och Remote Access Services (RRAS) i Azure Stack Development Kit-infrastruktur. Du måste konfigurera NAT på den virtuella datorn i AzS-bgpnat01 så att VPN-anslutningen för plats-till-plats att ansluta i båda ändar. 

Om du vill konfigurera VPN-anslutningen måste du skapa en statisk karta väg för NAT som mappar det externa gränssnittet på den virtuella datorn BGPNAT till VIP för Edge Gateway Pool. En statisk väg för NAT-mappning måste anges för varje port i en VPN-anslutning.

> [!NOTE]
> Den här konfigurationen krävs för Azure Stack Development Kit-miljöer.
> 
> 

### <a name="configure-the-nat"></a>Konfigurera NAT
> [!IMPORTANT]
> Du måste slutföra den här proceduren för *både* Azure Stack Development Kit miljöer.

1. Fastställa den **interna IP-adress** ska användas i följande PowerShell-skript. Öppna den virtuella nätverksgatewayen (GW1 och GW2), och klicka sedan på den **översikt** bladet spara värdet för den **offentliga IP-adressen** för senare användning.
![Intern IP-adress](media/azure-stack-create-vpn-connection-one-node-tp2/InternalIP.PNG)
2. Logga in på den fysiska Azure Stack-datorn för POC1.
3. Kopiera och redigera följande PowerShell-skript. Om du vill konfigurera NAT i varje Azure Stack Development Kit kan du köra skriptet i en upphöjd Windows PowerShell ISE. I skriptet, Lägg till värden i den *externa BGPNAT adress* och *interna IP-adress* platshållare:

   ```powershell
   # Designate the external NAT address for the ports that use the IKE authentication.
   Invoke-Command `
    -ComputerName AzS-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress <External BGPNAT address> `
      -PortStart 499 `
      -PortEnd 501}
   Invoke-Command `
    -ComputerName AzS-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress <External BGPNAT address> `
      -PortStart 4499 `
      -PortEnd 4501}
   # create a static NAT mapping to map the external address to the Gateway
   # Public IP Address to map the ISAKMP port 500 for PHASE 1 of the IPSEC tunnel
   Invoke-Command `
    -ComputerName AzS-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress <External BGPNAT address> `
      -InternalIPAddress <Internal IP address> `
      -ExternalPort 500 `
      -InternalPort 500}
   # Finally, configure NAT traversal which uses port 4500 to
   # successfully establish the complete IPSEC tunnel over NAT devices
   Invoke-Command `
    -ComputerName AzS-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress <External BGPNAT address> `
      -InternalIPAddress <Internal IP address> `
      -ExternalPort 4500 `
      -InternalPort 4500}
   ```

4. Upprepa den här proceduren på POC2.

## <a name="test-the-connection"></a>Testa anslutningen
Nu när plats-till-plats-anslutningen har upprättats bör du kontrollera att du kan hämta trafik som passerar genom den. Du kan kontrollera genom att logga in på en av de virtuella datorerna som du skapade i antingen Azure Stack Development Kit-miljö. Sedan kan pinga den virtuella datorn som du skapade i den andra miljön. 

För att säkerställa att du ska skicka trafik via plats-till-plats-anslutning, kontrollerar du att du pinga den Direct IP (DIP)-adressen för den virtuella datorn på fjärrundernätet, inte VIP. Gör detta genom att hitta den DIP-adressen på den andra änden av anslutningen. Spara adressen för senare användning.

### <a name="sign-in-to-the-tenant-vm-in-poc1"></a>Logga in på virtuella Datornätverket i POC1
1. Logga in på den fysiska Azure Stack-datorn för POC1 och sedan använda ett klientkonto för att logga in på användarportalen.
2. I det vänstra navigeringsfältet väljer **Compute**.
3. I listan över virtuella datorer, hitta **VM01** som du skapade tidigare och välj den sedan.
4. Bladet för den virtuella datorn och klicka på **Connect**, och sedan öppna filen VM01.rdp.
   
     ![Knappen Anslut](media/azure-stack-create-vpn-connection-one-node-tp2/image17.png)
5. Logga in med det konto som du konfigurerade när du har skapat den virtuella datorn.
6. Öppna en förhöjd behörighet **Windows PowerShell** fönster.
7. Ange **ipconfig/all**.
8. Utdata och hitta den **IPv4-adress**, och spara adressen för senare användning. Det här är den adress som du kommer att pinga från POC2. I exempelmiljön är adressen **10.0.10.4**, men i din miljö kan det bli annorlunda. Det bör ligga inom det **10.0.10.0/24** undernät som du skapade tidigare.
9. Om du vill skapa en brandväggsregel som tillåter den virtuella datorn svarar på ping, kör du följande PowerShell-kommando:

   ```powershell
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

### <a name="sign-in-to-the-tenant-vm-in-poc2"></a>Logga in på virtuella Datornätverket i POC2
1. Logga in på den fysiska Azure Stack-datorn för POC2 och sedan använda ett klientkonto för att logga in på användarportalen.
2. I det vänstra navigeringsfältet, klickar du på **Compute**.
3. Från listan över virtuella datorer, hitta **VM02** som du skapade tidigare och välj den sedan.
4. Klicka på **Anslut** i bladet för den virtuella datorn.
5. Logga in med det konto som du konfigurerade när du har skapat den virtuella datorn.
6. Öppna en förhöjd behörighet **Windows PowerShell** fönster.
7. Ange **ipconfig/all**.
8. Du bör se en IPv4-adress som ligger inom **10.0.20.0/24**. I exempelmiljön är adressen är **10.0.20.4**, men din postadress vara annorlunda.
9. Om du vill skapa en brandväggsregel som tillåter den virtuella datorn svarar på ping, kör du följande PowerShell-kommando:

   ```powershell
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

10. Pinga den virtuella datorn på POC1, genom tunneln från den virtuella datorn i POC2. Om du vill göra detta måste pinga du den DIP du antecknade från VM01.
   I exempelmiljön är **10.0.10.4**, men se till att pinga adressen som du antecknade i labbet. Du bör se ett resultat som ser ut som följande:
   
    ![Lyckad ping](media/azure-stack-create-vpn-connection-one-node-tp2/image19b.png)
11. Ett svar från fjärrdatorn virtuella anger testet lyckats! Du kan stänga fönstret för den virtuella dator. Du kan prova andra typer av dataöverföringar som en filkopia om du vill testa anslutningen.

### <a name="viewing-data-transfer-statistics-through-the-gateway-connection"></a>Visa statistik via gatewayanslutningen för dataöverföring
Om du vill veta hur mycket data passerar genom plats-till-plats-anslutning kan den här informationen är tillgänglig i den **anslutning** bladet. Det här testet är också ett annat sätt att kontrollera att den ping du precis skickade faktiskt passerade genom VPN-anslutningen.

1. När du är inloggad på den virtuella klientdatorn i POC2, kan du använda klientkontot för att logga in på användarportalen.
2. Gå till **alla resurser**, och välj sedan den **POC2-POC1** anslutning. **Anslutningar** visas.
4. På den **anslutning** bladet, statistik för **Data i** och **Data ut** visas. I följande skärmbild är det stora antalet tillskrivas ytterligare filöverföringen. Du bör se några nollvärden det.
   
    ![Data in och ut](media/azure-stack-create-vpn-connection-one-node-tp2/image20.png)
