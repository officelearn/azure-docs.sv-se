---
title: "Skapa en plats-till-plats VPN-anslutning mellan två virtuella nätverk i olika miljöer i Azure-stacken Development Kit | Microsoft Docs"
description: "Stegvisa anvisningar som molnadministratören använder för att skapa en plats-till-plats VPN-anslutning mellan två nod Azure Stack Development Kit miljöer."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 3f1b4e02-dbab-46a3-8e11-a777722120ec
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 7/10/2017
ms.author: brenduns
ms.reviewer: scottnap
ms.openlocfilehash: 886d56169c5500c9175b7ddc43edfc29c5142fbb
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="create-a-site-to-site-vpn-connection-between-two-virtual-networks-in-different-azure-stack-development-kit-environments"></a>Skapa en plats-till-plats VPN-anslutning mellan två virtuella nätverk i olika Azure-stacken Development Kit miljöer
## <a name="overview"></a>Översikt
Den här artikeln visar hur du skapar en plats-till-plats VPN-anslutning mellan två virtuella nätverk i två separata Azure Stack Development Kit miljöer. När du konfigurerar anslutningar kan du lära dig hur VPN-gatewayer i Azure-stacken fungerar.

### <a name="connection-diagram"></a>Anslutningsdiagram
Följande diagram visar hur konfigurationen ska se ut när du är klar.

![Konfiguration för plats-till-plats VPN-anslutning](media/azure-stack-create-vpn-connection-one-node-tp2/OneNodeS2SVPN.png)

### <a name="before-you-begin"></a>Innan du börjar
Slutför konfigurationen genom att se till att du har följande innan du börjar:

* Två servrar som uppfyller maskinvarukraven Azure Stack Development Kit, som definieras av den [kraven för distribution av Azure-stacken](azure-stack-deploy.md). Se till att de förutsättningar som visas i den [artikel](azure-stack-deploy.md) uppfylls för.
* Den [Azure Stack Development Kit](https://azure.microsoft.com/en-us/overview/azure-stack/try/) distributionspaketet.

## <a name="deploy-the-azure-stack-development-kit-environments"></a>Distribuera Azure Stack Development Kit-miljöer
För att slutföra konfigurationen måste du distribuera två Azure Stack Development Kit miljöer.
> [!NOTE] 
> För varje Azure-stacken Development Kit som du distribuerar, följer du de [distributionsanvisningarna](azure-stack-run-powershell-script.md). I den här artikeln Azure Stack Development Kit miljöer kallas *POC1* och *POC2*.


## <a name="prepare-an-offer-on-poc1-and-poc2"></a>Förbereda ett erbjudande på POC1 och POC2
Förbered ett erbjudande på både POC1 och POC2 så att en användare kan prenumerera på erbjudandet och distribuera virtuella datorer. Information om hur du skapar ett erbjudande finns [tillgängliggöra virtuella datorer till Azure-stacken användarna](azure-stack-tutorial-tenant-vm.md).

## <a name="review-and-complete-the-network-configuration-table"></a>Granska och fylla i tabellen för konfiguration av nätverk
I följande tabell sammanfattas nätverkskonfigurationen för både Azure-stacken Development Kit-miljöer. Använd proceduren som visas efter tabellen om du vill lägga till externa BGPNAT adressen som är specifik för ditt nätverk.

**Tabell för konfiguration av nätverk**
|   |POC1|POC2|
|---------|---------|---------|
|Namn för virtuellt nätverk     |VNET-01|VNET-02 |
|Virtuellt adressutrymme |10.0.10.0/23|10.0.20.0/23|
|Namn på undernät     |Subnet-01|Subnet-02|
|Adressintervall för undernätet|10.0.10.0/24 |10.0.20.0/24 |
|Gateway-undernät      |10.0.11.0/24|10.0.21.0/24|
|Extern BGPNAT adress     |         |         |

> [!NOTE]
> De externa BGPNAT IP-adresserna i exempel-miljö är 10.16.167.195 för POC1 och 10.16.169.131 för POC2. Använd följande procedur för att fastställa de externa BGPNAT IP-adresserna för dina Azure-stacken Development Kit värdar och lägga till dem i tabellen för föregående konfiguration nätverk.


### <a name="get-the-ip-address-of-the-external-adapter-of-the-nat-vm"></a>Hämta IP-adressen för den externa adaptern för den virtuella datorns NAT
1. Logga in på den fysiska datorn i Azure-Stack för POC1.
2. Redigera följande Powershell-koden för att ersätta lösenord för administratörskontot och sedan köra koden på POC värden:

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
3. Lägga till IP-adressen i tabellen nätverket konfiguration som visas i föregående avsnitt.

4. Upprepa den här proceduren på POC2.

## <a name="create-the-network-resources-in-poc1"></a>Skapa nätverksresurser i POC1
Nu kan du skapa POC1 nätverksresurser som måste du ställa in din gateway. I följande anvisningar visar hur du skapar resurser med hjälp av användarportalen. Du kan också använda PowerShell-koden för att skapa resurser.

![Arbetsflöde som används för att skapa resurser](media/azure-stack-create-vpn-connection-one-node-tp2/image2.png)

### <a name="sign-in-as-a-tenant"></a>Logga in som en klient
En tjänstadministratör kan logga in som en klient att testa planer, erbjudanden och prenumerationer som kan använda för sina klienter. Om du inte redan har en, [skapa ett klient-konto](azure-stack-add-new-user-aad.md) innan du loggar in.

### <a name="create-the-virtual-network-and-vm-subnet"></a>Skapa ett undernät för det virtuella nätverket och den virtuella datorn
1. Använda ett klient-konto för att logga in på användarportalen.
2. Välj i användarportalen, **ny**.

    ![Skapa ett nytt virtuellt nätverk](media/azure-stack-create-vpn-connection-one-node-tp2/image3.png)

3. Gå till **Marketplace**, och välj sedan **nätverk**.
4. Välj **för virtuella nätverk**.
5. För **namn**, **adressutrymmet**, **undernätsnamn**, och **adressintervall för Gatewayundernät**, använder de värden som visas tidigare i nätverket av konfigurationstabellen.
6. I **prenumeration**, visas den prenumeration som du skapade tidigare.
7. För **resursgruppen**, kan du antingen skapa en resursgrupp eller om du redan har en, Välj **använda befintliga**.
8. Verifiera den förvalda platsen.
9. Välj **fäst till instrumentpanelen**.
10. Välj **Skapa**.

### <a name="create-the-gateway-subnet"></a>Skapa gateway-undernätet
1. Öppna resursen VNET-01 virtuella nätverk som du skapade tidigare på instrumentpanelen.
2. På bladet **Inställningar** väljer du **Undernät**.
3. Om du vill lägga till ett gateway-undernät i det virtuella nätverket, Välj **Gatewayundernät**.
   
    ![Lägg till gateway-undernät](media/azure-stack-create-vpn-connection-one-node-tp2/image4.png)

4. Undernätnamnet är som standard **GatewaySubnet**.
   Gateway-undernät är särskilda. För att fungera korrekt måste de använda den *GatewaySubnet* namn.
5. I **adressintervall**, kontrollera att adressen är **10.0.11.0/24**.
6. Välj **OK** att skapa gateway-undernätet.

### <a name="create-the-virtual-network-gateway"></a>Skapa den virtuella nätverksgatewayen
1. Välj i Azure-portalen **ny**. 
2. Gå till **Marketplace**, och välj sedan **nätverk**.
3. Välj i listan över nätverksresurser **virtuell nätverksgateway**.
4. I **namn**, ange **GW1**.
5. Välj den **för virtuella nätverk** objektet om du vill välja ett virtuellt nätverk.
   Välj **VNET-01** från listan.
6. Välj den **offentliga IP-adressen** menyalternativet. När den **Välj offentlig IP-adress** blad öppnas väljer **Skapa nytt**.
7. I **namn**, ange **GW1 PiP**, och välj sedan **OK**.
8.  Som standard för **VPN-typ**, **ruttbaserad** är markerad.
    Behåll den **ruttbaserad** VPN-typ.
9. Verifiera att **Prenumeration** och **Plats** stämmer. Du kan fästa på instrumentpanelen till resursen. Välj **Skapa**.

### <a name="create-the-local-network-gateway"></a>Skapa den lokala nätverksgatewayen
Implementeringen av en *lokal nätverksgateway* i den här distributionen av Azure Stack-utvärderingen är lite annorlunda än i en befintlig Azure-distribution.

En lokal nätverksgateway representerar en fysisk enhet lokalt (på klient), som används för att ansluta till en virtuell nätverksgateway i Azure i Azure-distribution. I den här Azure-stacken utvärdering distributionen är båda ändar av anslutningen virtuella nätverksgatewayerna!

Ett sätt att tänka på detta mer allmänna är att den lokala gateway nätverksresursen alltid anger fjärrgatewayen i slutet av anslutningen. På grund av hur Azure-stacken Development Kit har utformats, måste du ange IP-adressen för det externa nätverkskortet på network address translation (NAT) VM i andra Azure-stacken Development Kit som den offentliga IP-adressen för den lokala gatewayen. Du kan sedan skapa NAT-mappningar på NAT-VM för att se till att båda ändarna är ordentligt anslutna.


### <a name="create-the-local-network-gateway-resource"></a>Skapa lokal gateway nätverksresursen
1. Logga in på den fysiska datorn i Azure-Stack för POC1.
2. Välj i användarportalen, **ny**.
3. Gå till **Marketplace**, och välj sedan **nätverk**.
4. Välj i listan över resurser **lokal nätverksgateway**.
5. I **namn**, ange **POC2 GW**.
6. I **IP-adress**, ange den externa BGPNAT-adressen för POC2. Den här adressen visas tidigare i tabellen nätverket konfiguration.
7. I **adressutrymme**, adressutrymmet för det POC2 VNET som du skapar senare, ange **10.0.20.0/23**.
8. Kontrollera att din **prenumeration**, **resursgruppen**, och **plats** är korrekta och välj sedan **skapa**.

### <a name="create-the-connection"></a>Skapa anslutningen
1. Välj i användarportalen, **ny**.
2. Gå till **Marketplace**, och välj sedan **nätverk**.
3. Välj i listan över resurser **anslutning**.
4. På den **grunderna** inställningar-bladet för den **anslutningstypen**väljer **plats-till-plats (IPSec)**.
5. Välj den **prenumeration**, **resursgruppen**, och **plats**, och välj sedan **OK**.
6. På den **inställningar** bladet väljer **virtuell nätverksgateway**, och välj sedan **GW1**.
7. Välj **lokal nätverksgateway**, och välj sedan **POC2 GW**.
8. I **anslutningsnamn**, ange **POC1 POC2**.
9. I **delad nyckel (PSK)**, ange **12345**, och välj sedan **OK**.
10. På den **sammanfattning** bladet väljer **OK**.

### <a name="create-a-vm"></a>Skapa en virtuell dator
För att validera data som överförs via VPN-anslutningen, måste de virtuella datorerna för att skicka och ta emot data i varje Azure-stacken Development Kit. Skapa en virtuell dator i POC1 nu och sedan placera den på VM-undernät i det virtuella nätverket.

1. Välj i Azure-portalen **ny**.
2. Gå till **Marketplace**, och välj sedan **Compute**.
3. I listan över virtuella datoravbildningar, väljer du den **Windows Server 2016 Datacenter Eval** bild.
4. På den **grunderna** bladet i **namn**, ange **VM01**.
5. Ange ett giltigt användarnamn och lösenord. Du kan använda det här kontot för att logga in på den virtuella datorn när den har skapats.
6. Ange en **prenumeration**, **resursgruppen**, och **plats**, och välj sedan **OK**.
7. På den **storlek** bladet för den här instansen, välja en storlek för virtuell dator och välj sedan **Välj**.
8. På den **inställningar** bladet standardvärdena. Se till att den **VNET-01** virtuellt nätverk har valts. Kontrollera att undernätet är **10.0.10.0/24**. Välj sedan **OK**.
9. På den **sammanfattning** bladet granskar du inställningarna och välj sedan **OK**.



## <a name="create-the-network-resources-in-poc2"></a>Skapa nätverksresurser i POC2

Nästa steg är att skapa nätverksresurser för POC2. I följande anvisningar visar hur du skapar resurser med hjälp av användarportalen.

### <a name="sign-in-as-a-tenant"></a>Logga in som en klient
En tjänstadministratör kan logga in som en klient att testa planer, erbjudanden och prenumerationer som kan använda för sina klienter. Om du inte redan har en, [skapa ett klient-konto](azure-stack-add-new-user-aad.md) innan du loggar in.

### <a name="create-the-virtual-network-and-vm-subnet"></a>Skapa ett undernät för det virtuella nätverket och den virtuella datorn

1. Logga in med ett klient-konto.
2. Välj i användarportalen, **ny**.
3. Gå till **Marketplace**, och välj sedan **nätverk**.
4. Välj **för virtuella nätverk**.
5. Använd informationen som visas tidigare i tabellen nätverket konfiguration för att identifiera värdena för POC2 **namn**, **adressutrymmet**, **undernätsnamn**, och **Adressintervall för gatewayundernät**.
6. I **prenumeration**, visas den prenumeration som du skapade tidigare.
7. För **resursgruppen**, skapa en ny resursgrupp eller om du redan har en **använda befintliga**.
8. Kontrollera standard **plats**.
9. Välj **fäst till instrumentpanelen**.
10. Välj **Skapa**.

### <a name="create-the-gateway-subnet"></a>Skapa gateway-undernätet
1. Öppna virtuella nätverksresurs som du skapade (**VNET-02**) från instrumentpanelen.
2. På bladet **Inställningar** väljer du **Undernät**.
3. Välj **gatewayundernät** att lägga till ett gateway-undernät i det virtuella nätverket.
4. Namnet på undernätet ställs in på **GatewaySubnet** som standard.
   Gatewayundernät är speciella och måste ha det här specifika namnet för att fungera ordentligt.
5. I den **adressintervall** fältet, kontrollera att adressen är **10.0.21.0/24**.
6. Välj **OK** att skapa gateway-undernätet.

### <a name="create-the-virtual-network-gateway"></a>Skapa den virtuella nätverksgatewayen
1. Välj i Azure-portalen **ny**.  
2. Gå till **Marketplace**, och välj sedan **nätverk**.
3. Välj i listan över nätverksresurser **virtuell nätverksgateway**.
4. I **namn**, ange **GW2**.
5. Om du vill välja ett virtuellt nätverk väljer **för virtuella nätverk**. Välj sedan **VNET-02** från listan.
6. Välj **offentliga IP-adressen**. När den **Välj offentlig IP-adress** blad öppnas väljer **Skapa nytt**.
7. I **namn**, ange **GW2 PiP**, och välj sedan **OK**.
8. Som standard för **VPN-typ**, **ruttbaserad** är markerad.
    Behåll den **ruttbaserad** VPN-typ.
9. Verifiera att **Prenumeration** och **Plats** stämmer. Du kan fästa på instrumentpanelen till resursen. Välj **Skapa**.

### <a name="create-the-local-network-gateway-resource"></a>Skapa lokal gateway nätverksresursen

1. Välj i användarportalen POC2 **ny**. 
4. Gå till **Marketplace**, och välj sedan **nätverk**.
5. Välj i listan över resurser **lokal nätverksgateway**.
6. I **namn**, ange **POC1 GW**.
7. I **IP-adress**, ange adressen externa BGPNAT för POC1 som anges tidigare i tabellen nätverket konfiguration.
8. I **adressutrymme**, från POC1, ange den **10.0.10.0/23** adressutrymmet för **VNET-01**.
9. Kontrollera att din **prenumeration**, **resursgruppen**, och **plats** är korrekta och välj sedan **skapa**.

## <a name="create-the-connection"></a>Skapa anslutningen
1. Välj i användarportalen, **ny**. 
2. Gå till **Marketplace**, och välj sedan **nätverk**.
3. Välj i listan över resurser **anslutning**.
4. På den **grundläggande** inställningar-bladet för den **anslutningstypen**, Välj **plats-till-plats (IPSec)**.
5. Välj den **prenumeration**, **resursgruppen**, och **plats**, och välj sedan **OK**.
6. På den **inställningar** bladet väljer **virtuell nätverksgateway**, och välj sedan **GW2**.
7. Välj **lokal nätverksgateway**, och välj sedan **POC1 GW**.
8. I **anslutningsnamn**, ange **POC2 POC1**.
9. I **delad nyckel (PSK)**, ange **12345**. Om du väljer ett annat värde, Kom ihåg att den *måste* matcha värdet för den delade nyckeln som du skapade på POC1. Välj **OK**.
10. Granska de **sammanfattning** bladet och väljer sedan **OK**.

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator
Skapa en virtuell dator i POC2 nu och placera den på VM-undernät i det virtuella nätverket.

1. Välj i Azure-portalen **ny**.
2. Gå till **Marketplace**, och välj sedan **Compute**.
3. I listan över virtuella datoravbildningar, väljer du den **Windows Server 2016 Datacenter Eval** bild.
4. På den **grunderna** bladet för **namn**, ange **VM02**.
5. Ange ett giltigt användarnamn och lösenord. Du kan använda det här kontot för att logga in på den virtuella datorn när den har skapats.
6. Ange en **prenumeration**, **resursgruppen**, och **plats**, och välj sedan **OK**.
7. På den **storlek** bladet Välj en virtuell dator storleken för den här instansen och välj sedan **Välj**.
8. På den **inställningar** bladet du kan acceptera standardinställningarna. Se till att den **VNET-02** virtuellt nätverk är markerad och kontrollera att undernätet är **10.0.20.0/24**. Välj **OK**.
9. Granska inställningarna på den **sammanfattning** bladet och väljer sedan **OK**.

## <a name="configure-the-nat-virtual-machine-on-each-azure-stack-development-kit-for-gateway-traversal"></a>Konfigurera virtuella NAT på varje Azure-stacken Development Kit för gateway-traversal
Eftersom Azure Stack Development Kit är fristående och isolerade från ett nätverk som den fysiska värden har distribuerats, den *externa* VIP-nätverk som gateway är ansluten till inte är faktiskt extern. I stället döljs nätverkets VIP bakom en router som utför nätverksadresser. 

Routern är en Windows Server-dator som kallas *AzS bgpnat01*, som kör rollen Routning och fjärråtkomsttjänster (RRAS) i Azure-stacken Development Kit-infrastruktur. Du måste konfigurera NAT på den virtuella datorn i AzS bgpnat01 att plats-till-plats VPN-anslutningen att ansluta på båda ändarna. 

Om du vill konfigurera VPN-anslutningen måste du skapa en statisk NAT kartan väg som matchar det externa gränssnittet på den virtuella datorn BGPNAT med VIP-Adressen för Gateway-poolen kant. En statisk väg för NAT-mappning måste anges för varje port i en VPN-anslutning.

> [!NOTE]
> Den här konfigurationen krävs för Azure-stacken Development Kit-miljöer.
> 
> 

### <a name="configure-the-nat"></a>Konfigurera NAT
> [!IMPORTANT]
> Du måste slutföra den här proceduren för *både* Azure Stack Development Kit miljöer.

1. Ta reda på **interna IP-adress** ska användas i följande PowerShell-skript. Öppna den virtuella nätverksgatewayen (GW1 och GW2), och klicka sedan på den **översikt** bladet spara värdet för den **offentliga IP-adressen** för senare användning.
![Intern IP-adress](media/azure-stack-create-vpn-connection-one-node-tp2/InternalIP.PNG)
2. Logga in på den fysiska datorn i Azure-Stack för POC1.
3. Kopiera och redigera följande PowerShell-skript. För att konfigurera NAT på varje Azure-stacken Development Kit, kör du skriptet i en upphöjd Windows PowerShell ISE. Lägga till värden till skriptet i *externa BGPNAT adress* och *interna IP-adress* platshållare:

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
Nu när plats-till-plats-anslutning har upprättats bör du verifiera att du kan få trafik som passerar genom den. För att validera, logga in på någon av de virtuella datorerna som du skapade i antingen Azure Stack Development Kit-miljö. Pinga den virtuella datorn som du skapade i den andra miljön. 

Se till att du pinga direkt IP-Adressen (DIP)-adressen för den virtuella datorn på det fjärranslutna undernätet inte VIP för att säkerställa att skicka trafik via plats-till-plats-anslutning. Gör detta genom att hitta den DIP-adressen på den andra änden av anslutningen. Spara den för senare användning.

### <a name="sign-in-to-the-tenant-vm-in-poc1"></a>Logga in på klienten VM i POC1
1. Logga in på den fysiska datorn i Azure-Stack för POC1 och sedan använda ett klient-kontot för att logga in på användarportalen.
2. I det vänstra navigeringsfältet väljer **Compute**.
3. I listan över virtuella datorer, hitta **VM01** som du skapade tidigare och markerar den.
4. På bladet för den virtuella datorn klickar du på **Anslut**, och sedan öppna filen VM01.rdp.
   
     ![Ansluta knappen](media/azure-stack-create-vpn-connection-one-node-tp2/image17.png)
5. Logga in med det konto som du konfigurerade när du har skapat den virtuella datorn.
6. Öppna en förhöjd behörighet **Windows PowerShell** fönster.
7. Ange **ipconfig/all**.
8. I resultatet är att hitta den **IPv4-adress**, och sedan spara adress för senare användning. Det här är den adress som du kan pinga från POC2. I exempelmiljön är adressen **10.0.10.4**, men i din miljö kan det bli annorlunda. Det bör omfattas av **10.0.10.0/24** undernät som du skapade tidigare.
9. Om du vill skapa en brandväggsregel som tillåter den virtuella datorn som svar på pingmeddelanden, kör du följande PowerShell-kommando:

   ```powershell
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

### <a name="sign-in-to-the-tenant-vm-in-poc2"></a>Logga in på klienten VM i POC2
1. Logga in på den fysiska datorn i Azure-Stack för POC2 och sedan använda ett klient-kontot för att logga in på användarportalen.
2. I det vänstra navigeringsfältet klickar du på **Compute**.
3. Listan över virtuella datorer och hitta **VM02** som du skapade tidigare och markerar den.
4. Klicka på **Anslut** i bladet för den virtuella datorn.
5. Logga in med det konto som du konfigurerade när du har skapat den virtuella datorn.
6. Öppna en förhöjd behörighet **Windows PowerShell** fönster.
7. Ange **ipconfig/all**.
8. Du bör se en IPv4-adress som ligger inom **10.0.20.0/24**. I exempel-miljö adressen är **10.0.20.4**, men din adress vara annorlunda.
9. Om du vill skapa en brandväggsregel som tillåter den virtuella datorn som svar på pingmeddelanden, kör du följande PowerShell-kommando:

   ```powershell
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

10. Pinga den virtuella datorn på POC1, via tunneln från den virtuella datorn på POC2. Om du vill göra detta pinga DIP-ADRESSEN som du antecknade från VM01.
   I exempel-miljö är **10.0.10.4**, men se till att pinga den adress som du antecknade i labbet. Du bör se ett resultat som ser ut som följande:
   
    ![Lyckad ping](media/azure-stack-create-vpn-connection-one-node-tp2/image19b.png)
11. Ett svar från fjärrdatorn virtuella anger en lyckad testa! Du kan stänga fönstret för den virtuella dator. Om du vill testa anslutningen kan du andra typer av överföring av data som en kopia av filen.

### <a name="viewing-data-transfer-statistics-through-the-gateway-connection"></a>Visa statistik via gatewayanslutningen för dataöverföring
Om du vill veta hur mycket data som skickas via plats-till-plats-anslutning till den här informationen är tillgänglig på den **anslutning** bladet. Det här testet är också ett annat sätt att kontrollera att ping som skickas bara faktiskt har gått igenom VPN-anslutningen.

1. När du är inloggad på den virtuella datorn för klienten i POC2 kan du använda klientkontot för att logga in på användarportalen.
2. Gå till **alla resurser**, och välj sedan den **POC2 POC1** anslutning. **Anslutningar** visas.
4. På den **anslutning** bladet, statistik för **Data i** och **ut Data** visas. I följande skärmbild hänföras stora tal till ytterligare filöverföring. Du bör se vissa inte är noll värden.
   
    ![Data in och ut](media/azure-stack-create-vpn-connection-one-node-tp2/image20.png)
