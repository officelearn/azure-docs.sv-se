---
title: "Anslut Azure Stack till Azure med hjälp av VPN"
description: "Hur du ansluter virtuella nätverk i Azure-stacken till virtuella nätverk i Azure med hjälp av VPN."
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
ms.reviewer: scottnap
ms.openlocfilehash: 16cc1962eb72ac219adc8483f38cecf41a4296c1
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="connect-azure-stack-to-azure-using-vpn"></a>Anslut Azure Stack till Azure med hjälp av VPN

*Gäller för: Azure Stack integrerat system*

Den här artikeln visar hur du skapar en plats-till-plats-VPN för att ansluta ett virtuellt nätverk i Azure-stacken till ett virtuellt nätverk i Azure.

### <a name="connection-diagram"></a>Anslutningsdiagram
Följande diagram visar hur konfigurationen ska se ut när du är klar:

![Konfiguration för plats-till-plats VPN-anslutning](media/azure-stack-connect-vpn/image2.png)

### <a name="before-you-begin"></a>Innan du börjar
Slutför konfigurationen genom att kontrollera att du har följande innan du börjar:

* En Azure-stacken integrerat system (med flera noder) distribution som är direkt ansluten till Internet. Detta innebär att din externa offentliga IP-adressintervall kunna nås direkt från Internet.
* Ett giltigt Azure-prenumeration.  Om du inte har en Azure-prenumeration kan du skapa en [kostnadsfritt Azure-konto här](https://azure.microsoft.com/free/?b=17.06).

## <a name="network-example-values-table"></a>Nätverket exempeltabell värden
Nätverket visas exempel värden exempelvärden som används i den här artikeln. Du kan använda dessa värden eller du kan använda dem för att bättre förstå exemplen i den här artikeln.

Nätverket exempeltabell värden
|   |Azure Stack|Azure|
|---------|---------|---------|
|Namn för virtuellt nätverk     |Azs-VNet|AzureVNet |
|Virtuellt adressutrymme |10.1.0.0/16|10.100.0.0/16|
|Namn på undernät     |FrontEnd|FrontEnd|
|Adressintervall för undernätet|10.1.0.0/24 |10.100.0.0/24 |
|Gateway-undernät      |10.1.1.0/24|10.100.1.0/24|

## <a name="create-the-network-resources-in-azure"></a>Skapa nätverksresurser i Azure

Först skapar du nätverksresurser för Azure. I följande anvisningar visar hur du skapar resurser med hjälp av den [Azure-portalen](http://portal.azure.com/).

### <a name="create-the-virtual-network-and-vm-subnet"></a>Skapa ett undernät för det virtuella nätverket och den virtuella datorn

1. Logga in på den [Azure-portalen](http://portal.azure.com/) med ditt Azure-konto.
2. Välj i användarportalen, **ny**.
3. Gå till **Marketplace**, och välj sedan **nätverk**.
4. Välj **för virtuella nätverk**.
5. Använd informationen från tabellen nätverket konfiguration för att identifiera värdena för Azure **namn**, **adressutrymmet**, **undernätsnamn**, och **undernätsadress intervallet**.
6. För **resursgruppen**, skapa en ny resursgrupp eller om du redan har en **använda befintliga**.
7. Välj den **plats** för ditt VNet.  Om du använder exempelvärden väljer **östra USA** eller Använd en annan plats om du föredrar.
8. Välj **fäst till instrumentpanelen**.
9. Välj **Skapa**.

### <a name="create-the-gateway-subnet"></a>Skapa gateway-undernätet
1. Öppna virtuella nätverksresurs som du skapade (**AzureVNet**) från instrumentpanelen.
2. På den **inställningar** väljer **undernät**.
3. Välj **gatewayundernät** att lägga till ett gateway-undernät i det virtuella nätverket.
4. Namnet på undernätet ställs in på **GatewaySubnet** som standard.
   Gatewayundernät är speciella och måste ha det här specifika namnet för att fungera ordentligt.
5. I den **adressintervall** fältet, kontrollera att adressen är **10.100.1.0/24**.
6. Välj **OK** att skapa gateway-undernätet.

### <a name="create-the-virtual-network-gateway"></a>Skapa den virtuella nätverksgatewayen
1. Välj i Azure-portalen **ny**.  
2. Gå till **Marketplace**, och välj sedan **nätverk**.
3. Välj i listan över nätverksresurser **virtuell nätverksgateway**.
4. I **namn**, typen **Azure-GW**.
5. Om du vill välja ett virtuellt nätverk väljer **för virtuella nätverk**. Välj sedan **AzureVnet** från listan.
6. Välj **offentliga IP-adressen**. När den **Välj offentlig IP-adress** avsnittet öppnas väljer **Skapa nytt**.
7. I **namn**, typen **Azure-GW-PiP**, och välj sedan **OK**.
8. Som standard för **VPN-typ**, **ruttbaserad** är markerad.
    Behåll den **ruttbaserad** VPN-typ.
9. Verifiera att **Prenumeration** och **Plats** stämmer. Du kan fästa på instrumentpanelen till resursen. Välj **Skapa**.

### <a name="create-the-local-network-gateway-resource"></a>Skapa lokal gateway nätverksresursen

1. Välj i Azure-portalen **ny**. 
4. Gå till **Marketplace**, och välj sedan **nätverk**.
5. Välj i listan över resurser **lokal nätverksgateway**.
6. I **namn**, typen **Azs GW**.
7. I **IP-adress**, skriver du den offentliga IP-adressen för din virtuella nätverksgateway för Azure-stacken som anges tidigare i tabellen nätverket konfiguration.
8. I **adressutrymme**, från Azure-stacken, skriver du den **10.1.0.0/24** och **10.1.1.0/24** adressutrymmet för **AzureVNet**.
9. Kontrollera att din **prenumeration**, **resursgruppen**, och **plats** är korrekta och välj sedan **skapa**.

## <a name="create-the-connection"></a>Skapa anslutningen
1. Välj i användarportalen, **ny**. 
2. Gå till **Marketplace**, och välj sedan **nätverk**.
3. Välj i listan över resurser **anslutning**.
4. På den **grundläggande** inställningar för den **anslutningstypen**, Välj **plats-till-plats (IPSec)**.
5. Välj den **prenumeration**, **resursgruppen**, och **plats**, och välj sedan **OK**.
6. På den **inställningar** väljer **virtuell nätverksgateway**, och välj sedan **Azure-GW**.
7. Välj **lokal nätverksgateway**, och välj sedan **Azs GW**.
8. I **anslutningsnamn**, typen **Azure Azs**.
9. I **delad nyckel (PSK)**, typen **12345**. Om du väljer ett annat värde, Kom ihåg att den *måste* matcha värdet för den delade nyckeln som du skapar på den andra änden av anslutningen. Välj **OK**.
10. Granska de **sammanfattning** avsnittet och väljer sedan **OK**.

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator
Skapa en virtuell dator i Azure nu och placera den på VM-undernät i det virtuella nätverket.

1. Välj i Azure-portalen **ny**.
2. Gå till **Marketplace**, och välj sedan **Compute**.
3. I listan över virtuella datoravbildningar, väljer du den **Windows Server 2016 Datacenter Eval** bild.
4. På den **grunderna** avsnittet för **namn**, typen **AzureVM**.
5. Ange ett giltigt användarnamn och lösenord. Du kan använda det här kontot för att logga in på den virtuella datorn när den har skapats.
6. Ange en **prenumeration**, **resursgruppen**, och **plats**, och välj sedan **OK**.
7. På den **storlek** markerar du en storlek på virtuell dator för den här instansen, och välj sedan **Välj**.
8. På den **inställningar** avsnitt, du kan acceptera standardinställningarna. Se till att den **AzureVnet** virtuellt nätverk är markerad och kontrollera att undernätet är **10.100.0.0/24**. Välj **OK**.
9. Granska inställningarna på den **sammanfattning** avsnittet och väljer sedan **OK**.

## <a name="create-the-network-resources-in-azure-stack"></a>Skapa nätverksresurser i Azure-stacken
Nu kan du skapa nätverksresurser i Azure-stacken.

### <a name="sign-in-as-a-user"></a>Logga in som en användare
En tjänstadministratör kan logga in som en användare att testa planer, erbjudanden och prenumerationer som användarna kan använda. Om du inte redan har en, [skapa ett användarkonto](azure-stack-add-new-user-aad.md) innan du loggar in.

### <a name="create-the-virtual-network-and-vm-subnet"></a>Skapa ett undernät för det virtuella nätverket och den virtuella datorn
1. Använd ett konto för att logga in på användarportalen.
2. Välj i användarportalen, **ny**.

    ![Skapa ett nytt virtuellt nätverk](media/azure-stack-create-vpn-connection-one-node-tp2/image3.png)

3. Gå till **Marketplace**, och välj sedan **nätverk**.
4. Välj **för virtuella nätverk**.
5. För **namn**, **adressutrymmet**, **undernätsnamn**, och **adressintervall för Gatewayundernät**, använda värden från tabellen nätverket konfiguration.
6. I **prenumeration**, visas den prenumeration som du skapade tidigare.
7. För **resursgruppen**, kan du antingen skapa en resursgrupp eller om du redan har en, Välj **använda befintliga**.
8. Verifiera den förvalda platsen.
9. Välj **fäst till instrumentpanelen**.
10. Välj **Skapa**.

### <a name="create-the-gateway-subnet"></a>Skapa gateway-undernätet
1. Öppna Azs VNet virtuellt nätverksresursen som du skapade på instrumentpanelen.
2. På den **inställningar** väljer **undernät**.
3. Om du vill lägga till ett gateway-undernät i det virtuella nätverket, Välj **Gatewayundernät**.
   
    ![Lägg till gateway-undernät](media/azure-stack-create-vpn-connection-one-node-tp2/image4.png)

4. Undernätnamnet är som standard **GatewaySubnet**.
   Gateway-undernät är särskilda. För att fungera korrekt måste de använda den *GatewaySubnet* namn.
5. I **adressintervall**, kontrollera att adressen är **10.1.1.0/24**.
6. Välj **OK** att skapa gateway-undernätet.

### <a name="create-the-virtual-network-gateway"></a>Skapa den virtuella nätverksgatewayen
1. Markera i Azure Stack-portal **ny**. 
2. Gå till **Marketplace**, och välj sedan **nätverk**.
3. Välj i listan över nätverksresurser **virtuell nätverksgateway**.
4. I **namn**, typen **Azs GW**.
5. Välj den **för virtuella nätverk** objektet om du vill välja ett virtuellt nätverk.
   Välj **Azs VNet** från listan.
6. Välj den **offentliga IP-adressen** menyalternativet. När den **Välj offentlig IP-adress** avsnittet öppnas väljer **Skapa nytt**.
7. I **namn**, typen **Azs-GW-PiP**, och välj sedan **OK**.
8.  Som standard för **VPN-typ**, **ruttbaserad** är markerad.
    Behåll den **ruttbaserad** VPN-typ.
9. Verifiera att **Prenumeration** och **Plats** stämmer. Du kan fästa på instrumentpanelen till resursen. Välj **Skapa**.

### <a name="create-the-local-network-gateway"></a>Skapa den lokala nätverksgatewayen
Begreppet en *lokal nätverksgateway* i Azure-stacken är något annat än i Azure-distribution.

En lokal nätverksgateway representerar en fysisk enhet lokalt (på Användarplats), som används för att ansluta till en virtuell nätverksgateway i Azure i Azure-distribution. I Azure-stacken är båda ändar av anslutningen virtuella nätverksgatewayerna!

Ett sätt att tänka på detta mer allmänna är att den lokala gateway nätverksresursen alltid anger fjärrgatewayen i slutet av anslutningen. 

### <a name="create-the-local-network-gateway-resource"></a>Skapa lokal gateway nätverksresursen
1. Logga in på Azure Stack-portalen.
2. Välj i användarportalen, **ny**.
3. Gå till **Marketplace**, och välj sedan **nätverk**.
4. Välj i listan över resurser **lokal nätverksgateway**.
5. I **namn**, typen **Azure-GW**.
6. I **IP-adress**, skriver du den offentliga IP-adressen för den virtuella nätverksgatewayen i Azure **Azure-GW-PiP**. Den här adressen visas tidigare i tabellen nätverket konfiguration.
7. I **adressutrymme**, adressutrymmet för det Azure-VNET som du skapade, Skriv **10.100.0.0/24** och **10.100.1.0/24**.
8. Kontrollera att din **prenumeration**, **resursgruppen**, och **plats** är korrekta och välj sedan **skapa**.

### <a name="create-the-connection"></a>Skapa anslutningen
1. Välj i användarportalen, **ny**.
2. Gå till **Marketplace**, och välj sedan **nätverk**.
3. Välj i listan över resurser **anslutning**.
4. På den **grunderna** inställningar för den **anslutningstypen**väljer **plats-till-plats (IPSec)**.
5. Välj den **prenumeration**, **resursgruppen**, och **plats**, och välj sedan **OK**.
6. På den **inställningar** väljer **virtuell nätverksgateway**, och välj sedan **Azs GW**.
7. Välj **lokal nätverksgateway**, och välj sedan **Azure-GW**.
8. I **anslutningsnamn**, typen **Azs Azure**.
9. I **delad nyckel (PSK)**, typen **12345**, och välj sedan **OK**.
10. På den **sammanfattning** väljer **OK**.

### <a name="create-a-vm"></a>Skapa en virtuell dator
För att validera data som överförs via VPN-anslutningen måste du skapa virtuella datorer på varje slutet för att skicka och ta emot data via VPN-tunnel. 

1. Välj i Azure-portalen **ny**.
2. Gå till **Marketplace**, och välj sedan **Compute**.
3. I listan över virtuella datoravbildningar, väljer du den **Windows Server 2016 Datacenter Eval** bild.
4. På den **grunderna** i avsnittet **namn**, typen **Azs VM**.
5. Ange ett giltigt användarnamn och lösenord. Du kan använda det här kontot för att logga in på den virtuella datorn när den har skapats.
6. Ange en **prenumeration**, **resursgruppen**, och **plats**, och välj sedan **OK**.
7. På den **storlek** avsnittet för den här instansen, väljer en storlek för virtuell dator och väljer sedan **Välj**.
8. På den **inställningar** avsnittet, Godkänn standardinställningarna. Se till att den **Azs VNet** virtuellt nätverk har valts. Kontrollera att undernätet är **10.1.0.0/24**. Välj sedan **OK**.
9. På den **sammanfattning** avsnittet, granskar du inställningarna och väljer sedan **OK**.


## <a name="test-the-connection"></a>Testa anslutningen
Nu när plats-till-plats-anslutning har upprättats bör du verifiera att du kan få trafik som passerar genom den. Om du vill validera, logga in på någon av de virtuella datorerna som du skapade i Azure-stacken. Pinga den virtuella datorn som du skapade i Azure. 

Se till att skicka trafik via plats-till-plats-anslutning genom att pinga direkt IP-Adressen (DIP)-adressen för den virtuella datorn på det fjärranslutna undernätet inte VIP. Gör detta genom att hitta den DIP-adressen på den andra änden av anslutningen. Spara den för senare användning.

### <a name="sign-in-to-the-user-vm-in-azure-stack"></a>Logga in användaren VM i Azure-stacken
1. Logga in på Azure Stack-portalen.
2. I det vänstra navigeringsfältet väljer **virtuella datorer**.
3. I listan över virtuella datorer, hitta **Azs VM** som du skapade tidigare och markerar den.
4. Klicka på avsnittet för den virtuella datorn **Anslut**, och sedan öppna filen Azs VM.rdp.
   
     ![Ansluta knappen](media/azure-stack-create-vpn-connection-one-node-tp2/image17.png)
5. Logga in med det konto som du konfigurerade när du har skapat den virtuella datorn.
6. Öppna en förhöjd behörighet **Windows PowerShell** fönster.
7. Skriv **ipconfig /all**.
8. I resultatet är att hitta den **IPv4-adress**, och sedan spara adress för senare användning. Det här är den adress som du kan pinga från Azure. I exempel-miljö adressen är **10.1.0.4**, men i din miljö kan det vara olika. Det bör omfattas av **10.1.0.0/24** undernät som du skapade tidigare.
9. Om du vill skapa en brandväggsregel som tillåter den virtuella datorn som svar på pingmeddelanden, kör du följande PowerShell-kommando:

   ```powershell
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

### <a name="sign-in-to-the-tenant-vm-in-azure"></a>Logga in på klienten VM i Azure
1. Logga in på Azure Portal.
2. I det vänstra navigeringsfältet klickar du på **virtuella datorer**.
3. Listan över virtuella datorer och hitta **Azure VM** som du skapade tidigare och markerar den.
4. Klicka på avsnittet för den virtuella datorn **Anslut**.
5. Logga in med det konto som du konfigurerade när du har skapat den virtuella datorn.
6. Öppna en förhöjd behörighet **Windows PowerShell** fönster.
7. Skriv **ipconfig /all**.
8. Du bör se en IPv4-adress som ligger inom **10.100.0.0/24**. I exempel-miljö adressen är **10.100.0.4**, men din adress vara annorlunda.
9. Om du vill skapa en brandväggsregel som tillåter den virtuella datorn som svar på pingmeddelanden, kör du följande PowerShell-kommando:

   ```powershell
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

10. Pinga den virtuella datorn i Azure-stacken genom tunneln från den virtuella datorn i Azure. Om du vill göra detta pinga DIP-ADRESSEN som du antecknade från Azs-VM.
   I exempel-miljö är **10.1.0.4**, men se till att pinga den adress som du antecknade i labbet. Du bör se ett resultat som ser ut som följande skärmbild:
   
    ![Lyckad ping](media/azure-stack-create-vpn-connection-one-node-tp2/image19b.png)
11. Ett svar från fjärrdatorn virtuella anger en lyckad testa! Du kan stänga fönstret för den virtuella dator. Om du vill testa anslutningen kan du andra typer av överföring av data som en kopia av filen.

### <a name="viewing-data-transfer-statistics-through-the-gateway-connection"></a>Visa statistik via gatewayanslutningen för dataöverföring
Om du vill veta hur mycket data som skickas via plats-till-plats-anslutning till den här informationen är tillgänglig på den **anslutning** avsnitt. Det här testet är också ett annat sätt att kontrollera att ping som skickas bara faktiskt har gått igenom VPN-anslutningen.

1. Medan du är inloggad på den virtuella datorn för användare i Azure-stacken, kan du använda ditt användarkonto för att logga in på användarportalen.
2. Gå till **alla resurser**, och välj sedan den **Azs Azure** anslutning. **Anslutningar** visas.
4. På den **anslutning** avsnittet statistik för **Data i** och **ut Data** visas. I följande skärmbild hänföras stora tal till ytterligare filöverföring. Du bör se vissa inte är noll värden.
   
    ![Data in och ut](media/azure-stack-connect-vpn/Connection.png)

## <a name="next-steps"></a>Nästa steg

[Distribuera appar till Azure och Azure Stack](azure-stack-solution-pipeline.md)
