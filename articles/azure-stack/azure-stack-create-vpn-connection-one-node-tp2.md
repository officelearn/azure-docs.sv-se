---
title: "Skapa en VPN-anslutning för plats-till-plats mellan två virtuella nätverk i olika Azure Stack PoC-miljöer | Microsoft Docs"
description: "Stegvisa anvisningar som gör att en molnadministratör kan skapa en VPN-anslutning från plats till plats mellan två POC-miljöer med en nod i TP2."
services: azure-stack
documentationcenter: 
author: ScottNapolitan
manager: darmour
editor: 
ms.assetid: 3f1b4e02-dbab-46a3-8e11-a777722120ec
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/08/2017
ms.author: scottnap
translationtype: Human Translation
ms.sourcegitcommit: 5104c7996de9dc0597e65be31c28a9aaa1243a90
ms.openlocfilehash: d324290caf5b5a085a2daf67e541c295dffda732


---
# <a name="create-a-site-to-site-vpn-connection-between-two-virtual-networks-in-different-azure-stack-poc-environments"></a>Skapa en VPN-anslutning för plats-till-plats mellan två virtuella nätverk i olika Azure Stack PoC-miljöer
## <a name="overview"></a>Översikt
I den här artikeln får du information om hur du skapar en VPN-anslutning från plats till plats mellan två virtuella nätverk i två separata POC-miljöer (Proof-of-Concept) i Azure Stack. När du konfigurerar anslutningarna får du lära dig hur VPN-gatewayer i Azure Stack fungerar.

> [!NOTE]
> Det här dokumentet gäller specifikt för Azure Stack TP2 POC.
> 
> 

### <a name="connection-diagram"></a>Anslutningsdiagram
Följande diagram visar hur konfigurationen ska se ut när du är klar:

![](media/azure-stack-create-vpn-connection-one-node-tp2/image1.png)

### <a name="before-you-begin"></a>Innan du börjar
För att slutföra den här konfigurationen ska du kontrollera att du har följande:

* Två servrar som uppfyller distributionskraven för Azure Stack POC som definieras i [Distributionskrav för Azure Stack](azure-stack-deploy.md), och de andra distributionskraven som definieras i det dokumentet.
* Distributionspaketet Azure Stack Technical Preview 2.

## <a name="deploy-the-poc-environments"></a>Distribuera POC-miljöerna
Du distribuerar två Azure Stack POC-miljöer för att slutföra konfigurationen.

* För varje POC du distribuerar kan du följa anvisningarna i artikeln [Distribuera Azure Stack POC](azure-stack-run-powershell-script.md).
  Varje POC-miljö i det här dokumentet kallas *POC1* och *POC2*.

## <a name="configure-quotas-for-compute-network-and-storage"></a>Konfigurera kvoter för Compute, Network och Storage
Först måste du konfigurera *kvoter* för Compute, Network och Storage. Tjänsterna kan associeras med en *Plan*, och sedan ett *Erbjudande* som klienter kan prenumerera på.

> [!NOTE]
> Du måste utföra de här stegen för varje Azure Stack POC-miljö.
> 
> 

Skapa kvoter för tjänster som har ändrats från TP1. Anvisningarna för att skapa kvoter i TP2 finns på <http://aka.ms/mas-create-quotas>. Du kan acceptera standardinställningarna för alla kvotinställningar för övningen.

## <a name="create-a-plan-and-offer"></a>Skapa plan och erbjudande
[Planer](azure-stack-key-features.md) är grupperingar av en eller flera tjänster. Som provider kan du skapa planer som du erbjuder dina klienter. Dina klienter prenumererar i sin tur på dina erbjudanden för att använda de planer och tjänster som ingår i dem.

> [!NOTE]
> Du måste utföra de här stegen för varje Azure Stack POC-miljö.
> 
> 

1. Först ska du skapa en plan. Det kan du göra genom att följa stegen i onlineartikeln [Skapa en plan](azure-stack-create-plan.md).
2. Skapa ett erbjudande med hjälp av stegen som beskrivs i [Skapa ett erbjudande i Azure Stack](azure-stack-create-offer.md).
3. Logga in på portalen som klientadministratör och [prenumerera på erbjudandet du har skapat](azure-stack-subscribe-plan-provision-vm.md).

## <a name="create-the-network-resources-in-poc-1"></a>Skapa nätverksresurser i POC 1
Nu ska du skapa de resurser du behöver för att skapa din konfiguration. Följande steg visar vad du kommer att göra. Dessa instruktioner visar hur du skapar resurser i portalen, men du kan göra samma sak med hjälp av PowerShell.

![](media/azure-stack-create-vpn-connection-one-node-tp2/image2.png)

### <a name="log-in-as-a-tenant"></a>Logga in som en klient
En tjänstadministratör kan logga in som en klient för att testa de planer, erbjudanden och prenumerationer som deras klientorganisationer använder. Om du inte redan har ett klientkonto [skapar du ett](azure-stack-add-new-user-aad.md) innan du loggar in.

### <a name="create-the-virtual-network--vm-subnet"></a>Skapa ett undernät för det virtuella nätverket och den virtuella datorn
1. Logga in med ett klientkonto.
2. Klicka på **Nytt** i Azure Portal.
   
    ![](media/azure-stack-create-vpn-connection-one-node-tp2/image3.png)
3. Välj **Nätverk** på menyn Marketplace.
4. Klicka på **Virtuellt nätverk** på menyn.
5. Klicka på **Skapa** en bit ned på bladet med resursbeskrivningar. Ange följande värden i lämpliga fält enligt den här tabellen.
   
   | **Fält** | **Värde** |
   | --- | --- |
   | Namn |vnet-01 |
   | Adressutrymme |10.0.10.0/23 |
   | Namn på undernät |subnet-01 |
   | Adressintervall för undernätet |10.0.10.0/24 |
6. Du bör se prenumerationen du skapade tidigare i fältet **Prenumeration**.
7. För resursgruppen kan du antingen skapa en ny resursgrupp eller välja **Använd befintlig**.
8. Verifiera den förvalda platsen.
9. Klicka på **Skapa**.

### <a name="create-the-gateway-subnet"></a>Skapa gateway-undernätet
1. Öppna den virtuella nätverksresurs du nyss skapade (Vnet-01) från instrumentpanelen.
2. På bladet Inställningar väljer du **Undernät**.
3. Klicka på **Gateway Subnet** (Gateway-undernät) för att lägga till ett gateway-undernät till det virtuella nätverket.
   
    ![](media/azure-stack-create-vpn-connection-one-node-tp2/image4.png)
4. Namnet på undernätet ställs in på **GatewaySubnet** som standard.
   Gatewayundernät är speciella och måste ha det här specifika namnet för att fungera ordentligt.
5. I fältet **Adressintervall** skriver du **10.0.11.0/24**.
6. Klicka på **Skapa** för att skapa gateway-undernätet.

### <a name="create-the-virtual-network-gateway"></a>Skapa den virtuella nätverksgatewayen
1. Klicka på **Nytt** i Azure Portal.
   
2. Välj **Nätverk** på menyn Marketplace.
3. Välj **Virtuell nätverksgateway** från listan med nätverksresurser.
4. Granska beskrivningen och klicka på **Skapa**.
5. I fältet **Namn** skriver du **GW1**.
6. Klicka på **Virtuellt nätverk** för att välja ett virtuellt nätverk.
   Välj **Vnet-01** från listan.
7. Klicka på menyposten **Offentlig IP-adress**. När bladet **Välj offentlig IP-adress** öppnas klickar du på **Skapa ny**.
8. I fältet **Namn** skriver du **GW1-PiP** och klickar på **OK**.
9. **Gatewaytypen** bör ha **VPN** valt som standard. Behåll den inställningen.
10. **VPN-typen ** ska ha **Routningsbaserad** valt som standard.
    Behåll den inställningen.
11. Verifiera att **Prenumeration** och **Plats** stämmer. Om du vill kan du fästa resursen på instrumentpanelen. Klicka på **Skapa**.

### <a name="create-the-local-network-gateway"></a>Skapa den lokala nätverksgatewayen
Implementeringen av en *lokal nätverksgateway* i den här distributionen av Azure Stack-utvärderingen är lite annorlunda än i en befintlig Azure-distribution.

Precis som i Azure finns konceptet med en lokal nätverksgateway. I en Azure-distribution representerar en lokal nätverksgateway emellertid en lokal (hos klienten) fysisk enhet som du använder för att ansluta till en virtuell nätverksgateway i Azure. Men i den här utvärderingsdistributionen av Azure Stack är anslutningens båda ändar virtuella nätverksgatewayer!

Ett mer allmänt sätt att tänka på det här är att den lokala nätverksgatewayresursen alltid är avsedd att representera en fjärrgateway i den andra änden av anslutningen. På grund av hur POC-miljön var utformad måste du ange IP-adressen för det externa nätverkskortet på den virtuella datorns NAT för den andra POC som offentlig IP-adress för den lokala nätverksgatewayen. Sedan skapar du NAT-mappningar på den virtuella datorns NAT för att se till att båda ändar är korrekt anslutna.


### <a name="get-the-ip-address-of-the-external-adapter-of-the-nat-vm"></a>Hämta IP-adressen för den externa adaptern för den virtuella datorns NAT
1. Logga in på den fysiska Azure Stack-datorn för POC2.
2. Tryck på [Windowsknappen] + R för att öppna menyn **Kör** och skriv **mstsc** och tryck på Retur.
3. I fältet **Dator** skriver du namnet **MAS-BGPNAT01** och klickar på  **Anslut**.
4. Klicka på Start-menyn, högerklicka på **Windows PowerShell** och välj **Kör som administratör**.
5. Skriv **ipconfig /all**.
6. Leta reda på Ethernet-adaptern som är ansluten till ditt lokala nätverk och anteckna IPv4-adressen som är bunden till den adaptern. I exempelmiljön är det **10.16.167.195**, men din kommer att bli annorlunda.
7. Anteckna den adressen. Detta är det du kommer att använda som offentlig IP-adress för den lokala nätverksgatewaysresursen du skapade i POC1.

### <a name="create-the-local-network-gateway-resource"></a>Skapa en resurs för den lokala nätverksgatewayen
1. Logga in på den fysiska Azure Stack-datorn för POC1.
2. I fältet **Dator** skriver du namnet **MAS-CON01** och klickar på **Anslut**.
3. Klicka på **Nytt** i Azure Portal.
   
4. Välj **Nätverk** på menyn Marketplace.
5. Välj **lokal nätverksgateway** från listan med resurser.
6. I fältet **Namn** skriver du **POC2-GW**.
7. Du vet inte vad den andra gatewayen har för IP-adress ännu, men det är OK eftersom du kan gå tillbaka och ändra den senare. Nu skriver du **10.16.167.195** i fältet **IP-adress**.
8. I fältet **Adressutrymme** skriver du adressutrymmet för det Vnet du kommer att skapa i POC2. Det ska vara **10.0.20.0/23**, så skriv det värdet.
9. Kontrollera att **Prenumeration**, **Resursgrupp** och **plats** stämmer och klicka på **Skapa**.

### <a name="create-the-connection"></a>Skapa anslutningen
1. Klicka på **Nytt** i Azure Portal.
   
2. Välj **Nätverk** på menyn Marketplace.
3. Välj **Anslutning** i listan över resurser.
4. På inställningsbladet **Basic** väljer du **Plats-till-plats (IPSec)** som **Anslutningstyp**.
5. Ange **Prenumeration**, **Resursgrupp** och **Plats** och klicka på **OK**.
6. På bladet **Inställningar** väljer du den **virtuella nätverksgateway** (**GW1**) du skapade tidigare.
7. Välj den **lokala nätverksgateway** (**POC2-GW**) du skapade tidigare.
8. I fältet **Anslutningsnamn** skriver du **POC1-POC2**.
9. I fältet **Shared Key (PSK)** (Delad nyckel (PSK)) skriver du **12345** och klickar på **OK**.

### <a name="create-a-vm"></a>Skapa en virtuell dator
Du behöver virtuella datorer för att skicka och ta emot data i varje POC för att validera data som överförs via VPN-anslutningen. Skapa en virtuell dator i POC1 nu och placera den på VM-undernätet i det virtuella nätverket.

1. Klicka på **Nytt** i Azure Portal.
   
2. Välj **virtuella datorer** på Marketplace-menyn.
3. I listan över avbildningar av virtuella datorer väljer du **Windows Server 2012 R2 Datacenter**.
4. På bladet **Grundläggande inställningar** skriver du **VM01** i fältet **Namn**.
5. Skriv ett giltigt användarnamn och lösenord. Du kommer att använda det här kontot för att logga in på den virtuella datorn när den har skapats.
6. Ange **Prenumeration**, **Resursgrupp** och **Plats** och klicka sedan på **OK**.
7. På bladet **Storlek** väljer du en storlek för den virtuella datorn för den här instansen och sedan på **Välj**.
8. På bladet **Inställningar** kan du godkänna standardvärdena. Se bara till att det valda virtuella nätverket är **VNET-01** och att undernätet är inställt på **10.0.10.0/24**. Klicka på **OK**.
9. Granska inställningarna på bladet **Summary** (Sammanfattning) och klicka på **OK**.

## <a name="create-the-network-resources-in-poc-2"></a>Skapa nätverksresurserna i POC 2
### <a name="log-in-as-a-tenant"></a>Logga in som en klient
En tjänstadministratör kan logga in som en klient för att testa de planer, erbjudanden och prenumerationer som deras klientorganisationer använder. Om du inte redan har ett klientkonto [skapar du ett](azure-stack-add-new-user-aad.md) innan du loggar in.

### <a name="create-the-virtual-network-and-vm-subnet"></a>Skapa ett undernät för det virtuella nätverket och den virtuella datorn
1. Logga in med ett klientkonto.
2. Klicka på **Nytt** i Azure Portal.
   
3. Välj **Nätverk** på menyn Marketplace.
4. Klicka på **Virtuellt nätverk** på menyn.
5. Klicka på **Skapa** en bit ned på bladet med resursbeskrivningar. Skriv följande värden för aktuella fält som anges i tabellen nedan.
   
   | **Fält** | **Värde** |
   | --- | --- |
   | Namn |vnet-02 |
   | Adressutrymme |10.0.20.0/23 |
   | Namn på undernät |subnet-02 |
   | Adressintervall för undernätet |10.0.20.0/24 |
6. Du bör se prenumerationen du skapade tidigare i fältet **Prenumeration**.
7. För resursgruppen kan du antingen skapa en ny resursgrupp eller välja **Använd befintlig** om du redan har en.
8. Verifiera den förvalda **platsen**. Om du vill kan du fästa det virtuella nätverket på instrumentpanelen för enkel åtkomst.
9. Klicka på **Skapa**.

### <a name="create-the-gateway-subnet"></a>Skapa gateway-undernätet
1. Öppna den virtuella nätverksresurs du skapade (**Vnet-02**) från instrumentpanelen.
2. På bladet **Inställningar** väljer du **Undernät**.
3. Klicka på **Gateway Subnet** (Gateway-undernät) för att lägga till ett gateway-undernät till det virtuella nätverket.
   
4. Namnet på undernätet ställs in på **GatewaySubnet** som standard.
   Gatewayundernät är speciella och måste ha det här specifika namnet för att fungera ordentligt.
5. I fältet **Adressintervall** skriver du **10.0.20.0/24**.
6. Klicka på **Skapa** för att skapa gateway-undernätet.

### <a name="create-the-virtual-network-gateway"></a>Skapa den virtuella nätverksgatewayen
1. Klicka på **Nytt** i Azure Portal.
   
2. Välj **Nätverk** på menyn Marketplace.
3. Välj **Virtuell nätverksgateway** från listan med nätverksresurser.
4. Granska beskrivningen och klicka på **Skapa**.
5. I fältet **Namn** skriver du **GW2**.
6. Klicka på **Virtuellt nätverk** för att välja ett virtuellt nätverk.
   Välj **Vnet-02** från listan.
7. Klicka på **Offentlig IP-adress**. När bladet **Välj offentlig IP-adress** öppnas klickar du på **Skapa ny**.
8. I fältet **Namn** skriver du **GW2-PiP** och klickar på **OK**.
9. **Gatewaytypen** bör ha **VPN** valt som standard. Behåll den inställningen.
10. **VPN-typen ** ska ha **Routningsbaserad** valt som standard.
    Behåll den inställningen.
11. Verifiera att **Prenumeration** och **Plats** stämmer. Om du vill kan du fästa resursen på instrumentpanelen. Klicka på **Skapa**.

### <a name="create-the-local-network-gateway"></a>Skapa den lokala nätverksgatewayen
#### <a name="get-the-ip-address-of-the-external-adapter-of-the-nat-vm"></a>Hämta IP-adressen för den externa adaptern för den virtuella datorns NAT
1. Logga in på den fysiska Azure Stack-datorn för POC1.
2. Tryck på och håll in [Windowsknappen] + R för att öppna menyn **Kör** och skriv **mstsc** och tryck på Retur.
3. I fältet **Dator** skriver du namnet **MAS-BGPNAT01** och klickar på  **Anslut**.
4. Klicka på Start-menyn, högerklicka på  **Windows PowerShell** och välj **Kör som administratör**.
5. Skriv **ipconfig /all**.
6. Leta reda på Ethernet-adaptern som är ansluten till ditt lokala nätverk och anteckna IPv4-adressen som är bunden till den adaptern. I exempelmiljön är det **10.16.169.131**, men din kommer att bli annorlunda.
7. Anteckna den adressen. Detta är det du kommer att använda senare som offentlig IP-adress för den lokala nätverksgatewaysresursen du skapade i POC1.

#### <a name="create-the-local-network-gateway-resource"></a>Skapa en resurs för den lokala nätverksgatewayen
1. Logga in på den fysiska Azure Stack-datorn för POC2.
2. I fältet **Dator** skriver du namnet **MAS-CON01** och klickar på **Anslut**.
3. Klicka på **Nytt** i Azure Portal.
   
4. Välj **Nätverk** på menyn Marketplace.
5. Välj **lokal nätverksgateway** från listan med resurser.
6. I fältet **Namn** skriver du **POC1-GW**.
7. Nu behöver du den offentliga IP-adress du registrerade för den virtuella nätverksgatewayen i POC1. Skriv **10.16.169.131** i fältet **IP-adress**.
8. I fältet **Adressutrymme** skriver du adressutrymmet för **Vnet-01** från POC1 - **10.0.0.0/16**.
9. Kontrollera att **Prenumeration**, **Resursgrupp** och **plats** stämmer och klicka på **Skapa**.

## <a name="create-the-connection"></a>Skapa anslutningen
1. Klicka på **Nytt** i Azure Portal.
   
2. Välj **Nätverk** på menyn Marketplace.
3. Välj **Anslutning** i listan över resurser.
4. På inställningsbladet **Basic** väljer du **Plats-till-plats (IPSec)** som **Anslutningstyp**.
5. Ange **Prenumeration**, **Resursgrupp** och **Plats** och klicka på **OK**.
6. På bladet **Inställningar** väljer du den **virtuella nätverksgateway** (**GW1**) du skapade tidigare.
7. Välj den **lokala nätverksgateway** (**POC1-GW**) du skapade tidigare.
8. I fältet **Anslutningsnamn** skriver du **POC2-POC1**.
9. I fältet **Shared Key (PSK)** (Delad nyckel (PSK)) skriver du **12345**. Om du väljer ett annat värde ska du komma ihåg att det MÅSTE matcha värdet för den delade nyckel du skapade på POC1. Klicka på **OK**.

## <a name="create-a-vm"></a>Skapa en virtuell dator
Skapa en virtuell dator i POC1 nu och placera den på VM-undernätet i det virtuella nätverket.

1. Klicka på **Nytt** i Azure Portal.
   
2. Välj **virtuella datorer** på Marketplace-menyn.
3. I listan över avbildningar av virtuella datorer väljer du **Windows Server 2012 R2 Datacenter**.
4. På bladet **Grundläggande inställningar** skriver du **VM02** i fältet **Namn**.
5. Skriv ett giltigt användarnamn och lösenord. Du kommer att använda det här kontot för att logga in på den virtuella datorn när den har skapats.
6. Ange **Prenumeration**, **Resursgrupp** och **Plats** och klicka sedan på **OK**.
7. På bladet **Storlek** väljer du en storlek för den virtuella datorn för den här instansen och sedan på **Välj**.
8. På bladet Inställningar kan du godkänna standardvärdena. Se bara till att det valda virtuella nätverket är **VNET-02** och att undernätet är inställt på **20.0.0.0/24**. Klicka på **OK**.
9. Granska inställningarna på bladet **Summary** (Sammanfattning) och klicka på **OK**.

## <a name="configure-the-nat-vm-in-each-poc-for-gateway-traversal"></a>Konfigurera den virtuella datorns NAT i varje POC för gatewayöverträdelse
Eftersom POC utformades för att vara fristående och isolerad från nätverket där den fysiska värden är distribuerad är det ”externa” VIP-nätverket som gatewayerna är anslutna till egentligen inte externt, utan döljs bakom en router som utför NAT (Network Address Translation). Routern är egentligen en Windows Server VM (**MAS-BGPNAT01**) som kör RRAS-rollen (tjänster för routning och fjärråtkomst) i POC-infrastrukturen. Du måste konfigurera NAT på den virtuella datorn MAS-BGPNAT01 för att VPN-anslutningar för plats-till-plats ska kunna ansluta i båda ändar. För att göra det måste du skapa en statisk NAT-mappning som mappar det externa gränssnittet på den virtuella datorn BGPNAT till VIP för Edge Gateway Pool för de portar som krävs för en VPN-anslutning.

> [!NOTE]
> Den här konfigurationen krävs enbart för POC-miljöer.
> 
> 

### <a name="configure-nat"></a>Konfigurera NAT
Du måste följa de här anvisningarna i BÅDA POC-miljöerna.

1. Logga in på den fysiska Azure Stack-datorn för POC1.
2. Tryck på och håll in [Windowsknappen] + R för att öppna menyn **Kör** och skriv **mstsc** och tryck på **Retur**.
3. I fältet **Dator** skriver du namnet **MAS-BGPNAT01** och klickar på **Anslut**.
4. Klicka på Start-menyn, högerklicka på **Windows PowerShell** och välj **Kör som administratör**.
5. Skriv **ipconfig /all**.
6. Leta reda på Ethernet-adaptern som är ansluten till ditt lokala nätverk och anteckna IPv4-adressen som är bunden till den adaptern. I exempelmiljön är det **10.16.169.131** (inringat i rött nedan), men din kommer att bli annorlunda.
   
    ![](media/azure-stack-create-vpn-connection-one-node-tp2/image16.png)
7. Skriv följande PowerShell-kommando för att ange den externa NAT-adressen för portarna för IKE-autentisering. Kom ihåg att ändra IP-adressen till den som passar din miljö.
   
       Add-NetNatExternalAddress -NatName BGPNAT -IPAddress 10.16.169.131 -PortStart 499 -PortEnd 501
8. Sedan kan du skapa en statisk NAT-mappning för att mappa den externa adressen till gatewayens offentliga IP-adress för att  mappa ISAKMP-port 500  för FAS 1 av IPSEC-tunneln.
   
        Add-NetNatStaticMapping -NatName BGPNAT -Protocol UDP -ExternalIPAddress 10.16.169.131 -InternalIPAddress 192.168.102.1 -ExternalPort 500 -InternalPort 500
> [!NOTE] 
> Parametern `-InternalAddress` här är den offentliga IP-adressen för den virtuella gateway du skapade tidigare.  För att hitta IP-adressen kan du titta på egenskaperna på bladet Virtuell nätverks-gateway och leta rätt på värdet för den offentliga IP-adressen.       

9. Slutligen måste du konfigurera NAT-överträdelse som använder port 4500 för att skapa en fullständig IPEC-tunnel över NAT-enheter.
   
        Add-NetNatStaticMapping -NatName BGPNAT -Protocol UDP -ExternalIPAddress 10.16.169.131 -InternalIPAddress 192.168.102.1 -ExternalPort 4500 -InternalPort 4500
> [!NOTE] 
> Parametern `-InternalAddress` här är den offentliga IP-adressen för den virtuella gateway du skapade tidigare.  För att hitta IP-adressen kan du titta på egenskaperna på bladet Virtuell nätverks-gateway och leta rätt på värdet för den offentliga IP-adressen.       

10. Upprepa steg 1–9 i POC2.

## <a name="test-the-connection"></a>Testa anslutningen
Nu när plats-till-plats-anslutningen har upprättats bör du kontrollera att du kan hämta trafik som passerar genom den. Den här uppgiften är enkel. Du ska bara logga in på någon av de virtuella datorer du skapade i POC-miljön och pinga den virtuella dator du skapade i den andra miljön. Om du vill kontrollera att du leder trafiken genom plats-till-plats-anslutningen måste du se till att pinga den virtuella datorns DIP-adress (Direct IP) på fjärrundernätet, inte VIP. För att göra det måste du hitta och anteckna adressen för den andra änden av anslutningen.

### <a name="log-in-to-the-tenant-vm-in-poc1"></a>Logga in på det virtuella datornätverket i POC1
1. Logga in på den fysiska Azure Stack-datorn för POC1 och logga in på portalen med ett klientkonto.
2. Klicka på **Virtuella datorer** i det vänstra navigeringsfältet.
3. Leta rätt på **VM01** som du skapade tidigare i listan över virtuella datorer och klicka på den.
4. Klicka på **Anslut** i bladet för den virtuella datorn.
   
     ![](media/azure-stack-create-vpn-connection-one-node-tp2/image17.png)
5. Öppna en kommandotolk inifrån den virtuella datorn och skriv **ipconfig /all**.
6. Leta rätt på **IPv4-adressen** i utdata och anteckna den. Det är denna adress du kommer att pinga från POC2. I exempelmiljön är adressen **10.0.10.4**, men i din miljö kan det bli annorlunda. Den bör emellertid hamna inom undernätet **10.0.10.0/24** som skapades tidigare.

### <a name="log-in-to-the-tenant-vm-in-poc2"></a>Logga in på det virtuella datornätverket i POC2
1. Logga in på den fysiska Azure Stack-datorn för POC2 och logga in på portalen med ett klientkonto.
2. Klicka på **Virtuella datorer** i det vänstra navigeringsfältet.
3. Leta rätt på **VM02** som du skapade tidigare i listan över virtuella datorer och klicka på den.
4. Klicka på **Anslut** i bladet för den virtuella datorn.
   
5. Öppna en kommandotolk inifrån den virtuella datorn och skriv **ipconfig /all**.
6. Du bör se en IPv4-adress som hamnar inom 10.0.20.0/24. I exempelmiljön är adressen 10.0.20.4, men i din miljö kan det bli annorlunda.
7. Från den virtuella datorn i POC2 ska du nu pinga den virtuella datorn i POC1, genom tunneln. Du ska pinga den DIP du antecknade från VM01.
   I exempelmiljön är det 10.0.10.4, men se till att pinga adressen som du antecknade i labbet. Du bör se ett resultat som ser ut så här:
   
    ![](media/azure-stack-create-vpn-connection-one-node-tp2/image19b.png)
8. Om du får ett svar från den virtuella fjärrdatorn har testet lyckats! Nu kan du stänga fönstret för VM-anslutning. Eller så kan du göra några andra dataöverföringar som en kopia av filen för att testa anslutningen.

### <a name="viewing-data-transfer-statistics-through-the-gateway-connection"></a>Visa statistik via gatewayanslutningen för dataöverföring
Om du vill veta hur mycket trafik som passerar genom plats-till-plats-anslutningen finns det information om det på bladet Anslutning. Det här testet är också ett annat bra sätt att kontrollera att den ping du precis skickade faktiskt passerade genom VPN-anslutningen.

1. När du fortfarande är inloggad till den virtuella klientdatorn i POC2 loggar du in på **Microsoft Azure Stack POC Portal** med klientkontot.
2. Klicka på menyalternativet **Browse** (Bläddra) och välj **Anslutningar**.
3. Klicka på anslutningen **POC2-POC1** i listan.
4. På bladet Anslutningar kan du se statistik för **data in** och **data ut**. I följande skärmbild ser du större tal än vad bara en ping kan skapa. Det beror på några ytterligare filöverföringar. Du bör se några nollvärden där.
   
    ![](media/azure-stack-create-vpn-connection-one-node-tp2/image20.png)




<!--HONumber=Feb17_HO2-->


