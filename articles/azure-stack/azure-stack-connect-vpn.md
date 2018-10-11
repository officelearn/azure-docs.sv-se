---
title: Ansluta Azure Stack till Azure med VPN
description: Hur du ansluter virtuella nätverk i Azure Stack till virtuella nätverk i Azure med hjälp av VPN.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/12/2018
ms.author: sethm
ms.reviewer: scottnap
ms.openlocfilehash: dcbe222d8dd3d3c658e5778fdc4bc1cc01b5c12d
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2018
ms.locfileid: "49078893"
---
# <a name="connect-azure-stack-to-azure-using-vpn"></a>Ansluta Azure Stack till Azure med VPN

*Gäller för: integrerade Azure Stack-system*

Den här artikeln visar hur du skapar en plats-till-plats-VPN för att ansluta ett virtuellt nätverk i Azure Stack till ett virtuellt nätverk i Azure.

## <a name="before-you-begin"></a>Innan du börjar

Slutför konfigurationen genom att kontrollera att du har följande innan du börjar:

* Ett Azure Stack-integrerat system (med flera noder)-distribution som är direkt ansluten till Internet. Ditt externa offentliga IP-adressintervall måste nås direkt från det offentliga Internet.
* En giltig Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa en [kostnadsfritt Azure-konto här](https://azure.microsoft.com/free/?b=17.06).

### <a name="vpn-connection-diagram"></a>Anslutningsdiagram för VPN

Följande diagram visar hur konfigurationen ska se ut när du är klar:

![Konfiguration för plats-till-plats VPN-anslutning](media/azure-stack-connect-vpn/image2.png)

### <a name="network-configuration-example-values"></a>Exempel konfigurationsvärden för nätverk

Network configuration exempel tabell visar de värden som används för exemplen i den här artikeln. Du kan använda dessa värden eller du kan referera till dem för att bättre förstå exemplen i den här artikeln.

**Exempel på konfiguration av nätverk**

|   |Azure Stack|Azure|
|---------|---------|---------|
|Namn på virtuellt nätverk     |Azs-VNet|AzureVNet |
|Virtuella nätverkets adressutrymme |10.1.0.0/16|10.100.0.0/16|
|Namn på undernät     |FrontEnd|FrontEnd|
|Adressintervall för undernätet|10.1.0.0/24 |10.100.0.0/24 |
|Gateway-undernät      |10.1.1.0/24|10.100.1.0/24|

## <a name="create-the-network-resources-in-azure"></a>Skapa nätverksresurser i Azure

Först skapar du till nätverksresurser för Azure. Följande instruktioner visar hur du skapar resurser med hjälp av den [Azure-portalen](http://portal.azure.com/).

### <a name="create-the-virtual-network-and-virtual-machine-vm-subnet"></a>Skapa virtuellt nätverk och undernät för virtuell dator (VM)

1. Logga in på den [Azure-portalen](http://portal.azure.com/) med din Azure-konto.
2. Välj i användarportalen, **+ skapa en resurs**.
3. Gå till **Marketplace**, och välj sedan **nätverk**.
4. Välj **virtuellt nätverk**.
5. Använd informationen från tabellen network configuration för att identifiera värdena för Azure **namn**, **adressutrymme**, **undernätsnamn**, och **Undernätadress intervallet**.
6. För **resursgrupp**, skapa en ny resursgrupp eller, om du redan har en väljer **Använd befintlig**.
7. Välj den **plats** för ditt VNet.  Om du använder exempelvärdena väljer **USA, östra** eller Använd en annan plats om du föredrar.
8. Välj **fäst till instrumentpanelen**.
9. Välj **Skapa**.

### <a name="create-the-gateway-subnet"></a>Skapa gateway-undernätet

1. Öppna den virtuella nätverksresurs du skapade (**AzureVNet**) från instrumentpanelen.
2. På den **inställningar** väljer **undernät**.
3. Välj **gatewayundernätet** att lägga till ett gateway-undernät till det virtuella nätverket.
4. Namnet på undernätet ställs in på **GatewaySubnet** som standard.

   >[!IMPORTANT]
   >Gatewayundernät är speciella och **måste** har det här specifika namnet ska fungera korrekt.

5. I den **adressintervall** fältet, verifiera att adressen är **10.100.1.0/24**.
6. Välj **OK** att skapa gateway-undernätet.

### <a name="create-the-virtual-network-gateway"></a>Skapa den virtuella nätverksgatewayen

1. I Azure-portalen väljer du **+ skapa en resurs**.  
2. Gå till **Marketplace**, och välj sedan **nätverk**.
3. Listan med nätverksresurser, väljer **virtuell nätverksgateway**.
4. I **namn**, typ **Azure-GW**.
5. Om du vill välja ett virtuellt nätverk, Välj **virtuellt nätverk**. Välj sedan **AzureVnet** i listan.
6. Välj **offentliga IP-adressen**. När den **Välj offentlig IP-adress** avsnittet öppnas, Välj **Skapa ny**.
7. I **namn**, typ **Azure-GW-PiP**, och välj sedan **OK**.
8. Som standard för **VPN-typ**, **routningsbaserad** har valts. Behåll den **routningsbaserad** VPN-typ.
9. Verifiera att **Prenumeration** och **Plats** stämmer. Du kan fästa resursen på instrumentpanelen. Välj **Skapa**.

### <a name="create-the-local-network-gateway-resource"></a>Skapa gateway-resursen lokalt nätverk

1. I Azure-portalen väljer du **+ skapa en resurs**.
2. Gå till **Marketplace**, och välj sedan **nätverk**.
3. Välj i listan över resurser **lokal nätverksgateway**.
4. I **namn**, typ **Azs-GW**.
5. I **IP-adress**, skriver du den offentliga IP-adressen för din virtuella nätverksgateway för Azure-Stack som anges tidigare i tabellen network configuration.
6. I **adressutrymme**, på Azure Stack, och Skriv den **10.1.0.0/24** och **10.1.1.0/24** adressutrymme för **AzureVNet**.
7. Kontrollera att din **prenumeration**, **resursgrupp**, och **plats** är korrekta och välj sedan **skapa**.

## <a name="create-the-connection"></a>Skapa anslutningen

1. Välj i användarportalen, **+ skapa en resurs**.
2. Gå till **Marketplace**, och välj sedan **nätverk**.
3. Välj i listan över resurser **anslutning**.
4. På den **grundläggande** inställningar för den **anslutningstypen**, Välj **plats-till-plats (IPSec)**.
5. Välj den **prenumeration**, **resursgrupp**, och **plats**, och välj sedan **OK**.
6. På den **inställningar** väljer **virtuell nätverksgateway**, och välj sedan **Azure-GW**.
7. Välj **lokal nätverksgateway**, och välj sedan **Azs-GW**.
8. I **anslutningsnamn**, typ **Azure Azs**.
9. I **delad nyckel (PSK)**, typ **12345**. Välj **OK**.

   >[!NOTE]
   >Om du använder ett annat värde för den delade nyckeln kan du komma ihåg att den *måste* matchar värdet för den delade nyckeln som du skapar på den andra änden av anslutningen.

10. Granska den **sammanfattning** avsnittet och välj sedan **OK**.

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Skapa en virtuell dator i Azure nu och placera den i VM-undernätet i det virtuella nätverket.

1. I Azure-portalen väljer du **+ skapa en resurs**.
2. Gå till **Marketplace**, och välj sedan **Compute**.
3. I listan över avbildningar av virtuella datorer, väljer du den **Windows Server 2016 Datacenter Oval** bild.
4. På den **grunderna** avsnittet för **namn**, typ **AzureVM**.
5. Ange ett giltigt användarnamn och lösenord. Du kan använda det här kontot för att logga in på den virtuella datorn när den har skapats.
6. Ange en **prenumeration**, **resursgrupp**, och **plats**, och välj sedan **OK**.
7. På den **storlek** avsnittet, välja en VM-storlek för den här instansen och välj sedan **Välj**.
8. På den **inställningar** avsnittet, du kan använda standardinställningarna. Innan du väljer OK, bekräftar du att:

   * Den **AzureVnet** virtuellt nätverk har valts.
   * Att undernätet är inställt på **10.100.0.0/24**.

   Välj **OK**.

9. Granska inställningarna på den **sammanfattning** avsnittet och välj sedan **OK**.

## <a name="create-the-network-resources-in-azure-stack"></a>Skapa nätverksresurser i Azure Stack

Nu kan du skapa nätverksresurser i Azure Stack.

### <a name="sign-in-as-a-user"></a>Logga in som en användare

En tjänstadministratör kan logga in som en användare att testa de planer, erbjudanden och prenumerationer som användarna kan använda. Om du inte redan har en, [skapa ett användarkonto](azure-stack-add-new-user-aad.md) innan du loggar in.

### <a name="create-the-virtual-network-and-a-vm-subnet"></a>Skapa det virtuella nätverket och ett Virtuellt datorundernät

1. Använda ett användarkonto för att logga in på användarportalen.
2. Välj i användarportalen, **+ skapa en resurs**.

    ![Skapa ett nytt virtuellt nätverk](media/azure-stack-create-vpn-connection-one-node-tp2/image3.png)

3. Gå till **Marketplace**, och välj sedan **nätverk**.
4. Välj **virtuellt nätverk**.
5. För **namn**, **adressutrymme**, **undernätsnamn**, och **undernätsadressintervall**, använder du värden från tabellen network configuration.
6. I **prenumeration**, den prenumeration som du skapade tidigare visas.
7. För **resursgrupp**, du kan skapa en resursgrupp eller om du redan har en, Välj **Använd befintlig**.
8. Verifiera den förvalda platsen.
9. Välj **fäst till instrumentpanelen**.
10. Välj **Skapa**.

### <a name="create-the-gateway-subnet"></a>Skapa gateway-undernätet

1. Öppna Azs-VNet-vnet-resurs du skapade på instrumentpanelen.
2. På den **inställningar** väljer **undernät**.
3. Om du vill lägga till ett gateway-undernät till det virtuella nätverket, Välj **Gatewayundernätet**.

    ![Lägg till gateway-undernät](media/azure-stack-create-vpn-connection-one-node-tp2/image4.png)

4. Som standard anges namnet på undernätet till **GatewaySubnet**. Gatewayundernät är speciella. För att fungera korrekt måste de använda den *GatewaySubnet* namn.
5. I **adressintervall**, kontrollera att adressen är **10.1.1.0/24**.
6. Välj **OK** att skapa gateway-undernätet.

### <a name="create-the-virtual-network-gateway"></a>Skapa den virtuella nätverksgatewayen

1. I Azure Stack-portalen väljer **+ skapa en resurs**.
2. Gå till **Marketplace**, och välj sedan **nätverk**.
3. Listan med nätverksresurser, väljer **virtuell nätverksgateway**.
4. I **namn**, typ **Azs-GW**.
5. Välj den **virtuellt nätverk** för att välja ett virtuellt nätverk. Välj **Azs-VNet** i listan.
6. Välj den **offentliga IP-adressen** menyalternativ. När den **Välj offentlig IP-adress** avsnittet öppnas, Välj **Skapa ny**.
7. I **namn**, typ **Azs-GW-PiP**, och välj sedan **OK**.
8. Som standard **routningsbaserad** har valts för **VPN-typ**. Behåll den **routningsbaserad** VPN-typ.
9. Verifiera att **Prenumeration** och **Plats** stämmer. Du kan fästa resursen på instrumentpanelen. Välj **Skapa**.

### <a name="create-the-local-network-gateway"></a>Skapa den lokala nätverksgatewayen

Konceptet med en *lokal nätverksgateway* i Azure Stack är lite annorlunda än i en Azure-distribution.

I Azure-distribution representerar en lokal nätverksgateway en fysisk enhet som är lokalt (på användarens plats) som du ansluter till en virtuell nätverksgateway i Azure. Men i Azure Stack som, båda ändar av anslutningen är virtuella nätverksgatewayer!

Ett mer allmänt sätt att tänka på detta är att den lokala gateway-nätverksresursen alltid anger en fjärrgateway i den andra änden av anslutningen.

### <a name="create-the-local-network-gateway-resource"></a>Skapa gateway-resursen lokalt nätverk

1. Logga in på Azure Stack-portalen.
2. Välj i användarportalen, **+ skapa en resurs**.
3. Gå till **Marketplace**, och välj sedan **nätverk**.
4. Välj i listan över resurser **lokal nätverksgateway**.
5. I **namn**, typ **Azure-GW**.
6. I **IP-adress**, ange offentliga IP-adressen för den virtuella nätverksgatewayen i Azure **Azure-GW-PiP**. Den här adressen visas tidigare i tabellen network configuration.
7. I **adressutrymme**, adressutrymmet för det virtuella Azure-nätverket som du skapade, Skriv **10.100.0.0/24** och **10.100.1.0/24**.
8. Kontrollera att din **prenumeration**, **resursgrupp**, och **plats** är korrekta och välj sedan **skapa**.

### <a name="create-the-connection"></a>Skapa anslutningen

1. Välj i användarportalen, **+ skapa en resurs**.
2. Gå till **Marketplace**, och välj sedan **nätverk**.
3. Välj i listan över resurser **anslutning**.
4. På den **grunderna** inställningar för den **anslutningstypen**väljer **plats-till-plats (IPSec)**.
5. Välj den **prenumeration**, **resursgrupp**, och **plats**, och välj sedan **OK**.
6. På den **inställningar** väljer **virtuell nätverksgateway**, och välj sedan **Azs-GW**.
7. Välj **lokal nätverksgateway**, och välj sedan **Azure-GW**.
8. I **anslutningsnamn**, typ **Azs Azure**.
9. I **delad nyckel (PSK)**, typ **12345**, och välj sedan **OK**.
10. På den **sammanfattning** väljer **OK**.

### <a name="create-a-virtual-machine-vm"></a>Skapa en virtuell dator (VM)

Om du vill kontrollera VPN-anslutningen måste du skapa två virtuella datorer, en i Azure och en i Azure Stack. När du har skapat dessa virtuella datorer kan använda du dem för att skicka och ta emot data via VPN-tunnel.

1. I Azure-portalen väljer du **+ skapa en resurs**.
2. Gå till **Marketplace**, och välj sedan **Compute**.
3. I listan över avbildningar av virtuella datorer, väljer du den **Windows Server 2016 Datacenter Oval** bild.
4. På den **grunderna** i avsnittet **namn**, typ **Azs-VM**.
5. Ange ett giltigt användarnamn och lösenord. Du kan använda det här kontot för att logga in på den virtuella datorn när den har skapats.
6. Ange en **prenumeration**, **resursgrupp**, och **plats**, och välj sedan **OK**.
7. På den **storlek** , för den här instansen, Välj en VM-storlek, och välj sedan **Välj**.
8. På den **inställningar** avsnittet, acceptera standardinställningarna. Se till att den **Azs-VNet** virtuellt nätverk har valts. Kontrollera att undernätet är inställt på **10.1.0.0/24**. Välj sedan **OK**.
9. På den **sammanfattning** avsnittet, granskar du inställningarna och välj sedan **OK**.

## <a name="test-the-connection"></a>Testa anslutningen

När plats-till-plats-anslutning har upprättats, bör du kontrollera att du kan få data som flödar i båda riktningarna. Det är det enklaste sättet att testa anslutningen genom att göra ett Pingtest:

* Logga in på den virtuella datorn som du skapade i Azure Stack och pinga den virtuella datorn i Azure.
* Logga in på den virtuella datorn som du skapade i Azure och pinga den virtuella datorn i Azure Stack.

>[!NOTE]
>För att säkerställa att du skickar trafik via plats-till-plats-anslutning, pinga Direct IP (DIP)-adressen för den virtuella datorn på fjärrundernätet, inte VIP.

### <a name="sign-in-to-the-user-vm-in-azure-stack"></a>Logga in på användar-VM i Azure Stack

1. Logga in på Azure Stack-portalen.
2. I det vänstra navigeringsfältet väljer **virtuella datorer**.
3. I listan över virtuella datorer, hitta **Azs-VM** som du skapade tidigare och välj den sedan.
4. I avsnittet för den virtuella datorn, Välj **Connect**, och sedan öppna filen Azs-VM.rdp.

     ![Knappen Anslut](media/azure-stack-create-vpn-connection-one-node-tp2/image17.png)

5. Logga in med det konto som du konfigurerade när du har skapat den virtuella datorn.
6. Öppna en förhöjd behörighet **Windows PowerShell** fönster.
7. Skriv **ipconfig /all**.
8. Utdata och hitta den **IPv4-adress**, och spara adressen för senare användning. Det här är den adress som du kommer att pinga från Azure. I exempelmiljön är adressen är **10.1.0.4**, men i din miljö kan det bli annorlunda. Det bör ligga inom det **10.1.0.0/24** undernät som du skapade tidigare.
9. Om du vill skapa en brandväggsregel som tillåter den virtuella datorn svarar på ping, kör du följande PowerShell-kommando:

   ```powershell
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

### <a name="sign-in-to-the-tenant-vm-in-azure"></a>Logga in på virtuella Datornätverket i Azure

1. Logga in på Azure Portal.
2. I det vänstra navigeringsfältet väljer **virtuella datorer**.
3. Från listan över virtuella datorer, hitta **Azure-VM** som du skapade tidigare och välj den sedan.
4. I avsnittet för den virtuella datorn, Välj **Connect**.
5. Logga in med det konto som du konfigurerade när du har skapat den virtuella datorn.
6. Öppna en förhöjd behörighet **Windows PowerShell** fönster.
7. Skriv **ipconfig /all**.
8. Du bör se en IPv4-adress som ligger inom **10.100.0.0/24**. I exempelmiljön är adressen är **10.100.0.4**, men din postadress vara annorlunda.
9. Om du vill skapa en brandväggsregel som tillåter den virtuella datorn svarar på ping, kör du följande PowerShell-kommando:

   ```powershell
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

10. Pinga den virtuella datorn i Azure Stack via tunneln från den virtuella datorn i Azure. Detta gör att pinga den DIP du antecknade från Azs-VM. I exempelmiljön är **10.1.0.4**, men se till att pinga adressen som du antecknade i labbet. Du bör se ett resultat som ser ut som följande skärmbild:

    ![Lyckad ping](media/azure-stack-create-vpn-connection-one-node-tp2/image19b.png)

11. Ett svar från fjärrdatorn virtuella anger testet lyckats! Du kan stänga fönstret för den virtuella dator.

Du bör också göra mer rigorösa dataöverföring testning. Till exempel stora kopiera olika filer i båda riktningarna.

### <a name="viewing-data-transfer-statistics-through-the-gateway-connection"></a>Visa statistik via gatewayanslutningen för dataöverföring

Om du vill veta hur mycket data passerar genom plats-till-plats-anslutning kan den här informationen är tillgänglig i den **anslutning** avsnittet. Det här testet är också ett annat sätt att kontrollera att den ping du precis skickade faktiskt passerade genom VPN-anslutningen.

1. När du har loggat in till den virtuella datorn för användaren i Azure Stack, Använd användarkontot för att logga in på användarportalen.
2. Gå till **alla resurser**, och välj sedan den **Azs Azure** anslutning. **Anslutningar** visas.
3. På den **anslutning** avsnittet statistik för **Data i** och **Data ut** visas. I följande skärmdump är i stort tillskrivas ytterligare filöverföringen. Du bör se några nollvärden det.

    ![Data in och ut](media/azure-stack-connect-vpn/Connection.png)

## <a name="next-steps"></a>Nästa steg

[Distribuera appar till Azure och Azure Stack](azure-stack-solution-pipeline.md)
