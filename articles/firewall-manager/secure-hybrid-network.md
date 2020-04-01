---
title: 'Självstudiekurs: Skydda det virtuella navnätverket med förhandsversionen av Azure Firewall Manager'
description: I den här självstudien får du lära dig hur du skyddar ditt virtuella nätverk med Azure Firewall Manager med Azure-portalen.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: tutorial
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: cdd416bdb833e4784334a6847d724a7375e2ef8d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77459961"
---
# <a name="tutorial-secure-your-hub-virtual-network-using-azure-firewall-manager-preview"></a>Självstudiekurs: Skydda det virtuella navnätverket med förhandsversionen av Azure Firewall Manager 

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

När du ansluter ditt lokala nätverk till ett virtuellt Azure-nätverk för att skapa ett hybridnätverk är förmågan att styra åtkomst till dina Azure-nätverksresurser en viktig del av den övergripande säkerhetsplanen.

Med hjälp av förhandsversionen av Azure Firewall Manager kan du skapa ett virtuellt navnätverk för att skydda din hybridnätverkstrafik som är avsedd för privata IP-adresser, Azure PaaS och Internet. Du kan använda Azure Firewall Manager för att styra nätverksåtkomst i ett hybridnätverk med principer som definierar tillåten och nekad nätverkstrafik.

Firewall Manager stöder också en säker virtuell hubbarkitektur. En jämförelse av de skyddade virtuella hubben och hubbarkitekturtyperna för virtuella nätverk finns i [Vilka är arkitekturalternativen för Azure Firewall Manager?](vhubs-and-vnets.md)

För den här självstudien skapar du tre virtuella nätverk:

- **VNet-Hub** – brandväggen finns i det här virtuella nätverket.
- **VNet-Spoke** – det virtuella ekernätverket representerar den arbetsbelastning som finns på Azure.
- **VNet-Onprem** – det lokala virtuella nätverket representerar ett lokalt nätverk. I en faktisk distribution kan den anslutas via antingen en VPN- eller ExpressRoute-anslutning. För enkelhetens skull använder den här självstudien en VPN-gatewayanslutning, och ett virtuellt Azure-nätverk används för att representera ett lokalt nätverk.

![Hybridnätverk](media/tutorial-hybrid-portal/hybrid-network-firewall.png)

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa en brandväggsprincip
> * Skapa de virtuella nätverken
> * Konfigurera och distribuera brandväggen
> * Skapa och ansluta VPN-gatewayer
> * Peera de virtuella hubb- och ekernätverken
> * Skapa vägarna
> * Skapa de virtuella datorerna
> * testa brandväggen.


## <a name="prerequisites"></a>Krav

Ett hybridnätverk använder arkitekturmodellen hub-and-spoke för att dirigera trafik mellan Azure-virtuella nätverk och lokala nätverk. Hub-and-spoke-arkitekturen har följande krav:

- Ange **AllowGatewayTransit** när du peering VNet-Hub till VNet-Spoke. I en hub-and-spoke nätverksarkitektur, en gateway transit tillåter eker virtuella nätverk att dela VPN-gateway i navet, i stället för att distribuera VPN-gateways i varje eker virtuellt nätverk. 

   Dessutom sprids vägar till gatewayanslutna virtuella nätverk eller lokala nätverk automatiskt till routningstabellerna för peer-virtuella nätverk med hjälp av gatewaytransiteringen. Mer information finns i [Konfigurera VPN-gatewaytransas för virtuell nätverks peering](../vpn-gateway/vpn-gateway-peering-gateway-transit.md).

- Ange **UseRemoteGateways** när du peer VNet-Spoke till VNet-Hub. Om **UseRemoteGateways** är inställt och **AllowGatewayTransit** på fjärr peering också är inställt, använder ekrarnas virtuella nätverk gateways för fjärrvirtanätverket för överföring.
- Om du vill dirigera ekerundernätstrafiken genom navbrandväggen behöver du en UDR (User Defined Route) som pekar på brandväggen med inställningen för spridning av **virtual network gateway-dirigerar** inaktiverad. Det här alternativet förhindrar flödesdistribution till ekerundernäten. Detta förhindrar att inlärda vägar står i konflikt med din UDR.
- Konfigurera en UDR på hub gateway-undernätet som pekar på brandväggens IP-adress som nästa hopp till ekernätverken. Det krävs ingen UDR i Azure Firewall-undernätet eftersom det lär sig vägarna från BGP.

Se avsnittet [Skapa rutter](#create-the-routes) i den här självstudien för att se hur dessa vägar skapas.

>[!NOTE]
>Azure-brandväggen måste ha direkt Internet-anslutning. Om din AzureFirewallSubnet lär sig en standardväg till ditt lokala nätverk via BGP måste du åsidosätta detta med en UDR 0.0.0/0 med **NextHopType-värdet** som **Internet** för att upprätthålla direkt Internet-anslutning.
>
>Azure-brandväggen kan konfigureras för att stödja påtvingad tunnel. Mer information finns i [Azure Firewall forced tunneling](../firewall/forced-tunneling.md).

>[!NOTE]
>Trafiken mellan direkt peerkopplade virtuella nätverk dirigeras direkt även om en UDR pekar på Azure Firewall som standardgateway. För att undernät till undernät-trafik ska kunna skickas till brandväggen i det här scenariot måste en UDR uttryckligen innehålla nätverksprefixet för målundernätverket på båda undernäten.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="create-a-firewall-policy"></a>Skapa en brandväggsprincip

1. Logga in på Azure-portalen på [https://portal.azure.com](https://portal.azure.com).
2. Skriv **Brandväggshanteraren** i Azure Portal och tryck på **Retur**.
3. På sidan Azure Firewall Manager väljer du **Visa Azure-brandväggsprinciper**.

   ![Brandväggsprincip](media/tutorial-hybrid-portal/firewall-manager-policy.png)

1. Välj **Skapa Azure-brandväggsprincip**.
1. Välj din prenumeration och för resursgruppen väljer du **Skapa nytt** och skapa en resursgrupp med namnet **FW-Hybrid-Test**.
2. Skriv **Pol-Net01**för principnamnet .
3. För Region väljer du **Östra USA**.
4. Välj **Nästa:Regler**.
5. Välj **Lägg till en regelsamling**.
6. För **Namn**skriver du **RCNet01**.
7. För **regelsamlingstyp**väljer du **Nätverk**.
8. För **Prioritet**skriver du **100**.
9. I fältet **Åtgärd** väljer du **Tillåt**.
10. Skriv **Rules** **AllowWeb**för **Namn**.
11. För **källadresser**skriver du **192.168.1.0/24**.
12. I fältet **Protokoll** väljer du **TCP**.
13. För **målportar**skriver du **80**.
14. För **Måltyp**väljer du **IP-adress**.
15. För **Mål**skriver du **10.6.0.0/16**.
16. På nästa regelrad anger du följande information:
 
    Namn: skriv **AllowRDP**<br>
    Källa IP-adress: typ **192.168.1.0/24**.<br>
    Protokoll, välj **TCP**<br>
    Målportar, typ **3389**<br>
    Måltyp, välj **IP-adress**<br>
    För Destination skriver du **10.6.0.0/16**

1. Välj **Lägg till**.
2. Välj **Granska + Skapa**.
3. Granska informationen och välj sedan **Skapa**.

## <a name="create-the-firewall-hub-virtual-network"></a>Skapa brandväggens virtuella hubbnätverk

> [!NOTE]
> Storleken på undernätet AzureFirewallSubnet är /26. Mer information om undernätsstorleken finns i [Vanliga frågor och svar om Azure-brandväggen](../firewall/firewall-faq.md#why-does-azure-firewall-need-a-26-subnet-size).

1. På startsidan för Azure-portalen väljer du **Skapa en resurs**.
2. Under **Nätverk**väljer du **Virtuellt nätverk**.
4. För **Namn**skriver du **VNet-hub**.
5. För **adressutrymme**skriver du **10.5.0.0/16**.
6. I fältet **Prenumeration** väljer du din prenumeration.
7. För **resursgrupp**väljer du **FW-Hybrid-Test**.
8. För **Plats**väljer du **Östra USA**.
9. Under **Undernät**, i fältet **Namn**, skriver du **AzureFirewallSubnet**. Brandväggen kommer att ligga i det här undernätet, och namnet på undernätet **måste** vara AzureFirewallSubnet.
10. För **adressintervall**skriver du **10.5.0.0/26**. 
11. Acceptera de andra standardinställningarna och välj sedan **Skapa**.

## <a name="create-the-spoke-virtual-network"></a>Skapa det virtuella ekernätverket

1. På startsidan för Azure-portalen väljer du **Skapa en resurs**.
2. Under **Nätverk**väljer du **Virtuellt nätverk**.
4. För **Namn**skriver du **VNet-Spoke**.
5. För **adressutrymme**skriver du **10.6.0.0/16**.
6. I fältet **Prenumeration** väljer du din prenumeration.
7. För **resursgrupp**väljer du **FW-Hybrid-Test**.
8. Välj samma plats som tidigare i fältet **Plats**.
9. Under **Undernät**, i fältet **Namn** anger du **SN-Workload**.
10. För **adressintervall**skriver du **10.6.0.0/24**.
11. Acceptera de andra standardinställningarna och välj sedan **Skapa**.

## <a name="create-the-on-premises-virtual-network"></a>Skapa det lokala virtuella nätverket

1. På startsidan för Azure-portalen väljer du **Skapa en resurs**.
2. Under **Nätverk**väljer du **Virtuellt nätverk**.
4. För **Namn**skriver du **VNet-OnPrem**.
5. I fältet **Adressutrymme** skriver du **192.168.0.0/16**.
6. I fältet **Prenumeration** väljer du din prenumeration.
7. För **resursgrupp**väljer du **FW-Hybrid-Test**.
8. Välj samma plats som tidigare i fältet **Plats**.
9. Under **Undernät**för **Namntyp** **SN-Corp**.
10. I fältet **Adressintervall** skriver du **192.168.1.0/24**.
11. Acceptera de andra standardinställningarna och välj sedan **Skapa**.

När det virtuella nätverket har distribuerats skapar du ett andra undernät för gatewayen.

1. På sidan **VNet-Onprem** väljer du **Undernät**.
2. Välj **+Undernät**.
3. För **Namn**skriver du **GatewaySubnet**.
4. För **adressintervall (CIDR-block)** typ **192.168.2.0/24**.
5. Välj **OK**.

### <a name="create-a-public-ip-address"></a>Skapa en offentlig IP-adress

Det här är den offentliga IP-adressen som används för den lokala gatewayen.

1. På startsidan för Azure-portalen väljer du **Skapa en resurs**.
2. Skriv **offentlig IP-adress** i söktextrutan och tryck på **Retur**.
3. Välj **Offentlig IP-adress** och välj sedan **Skapa**.
4. För namnet skriver **du VNet-Onprem-GW-pip**.
5. Skriv **FW-Hybrid-Test**för resursgruppen .
6. För **Plats**väljer du **Östra USA**.
7. Acceptera de andra standardinställningarna och välj sedan **Skapa**.

## <a name="configure-and-deploy-the-firewall"></a>Konfigurera och distribuera brandväggen

När säkerhetsprinciper är associerade med ett nav kallas det för ett *virtuellt navnätverk*.

Konvertera det virtuella **nätverket VNet-Hub** till ett *virtuellt navnätverk* och skydda det med Azure-brandväggen.

1. Skriv **Brandväggshanteraren** i Azure Portal och tryck på **Retur**.
3. Välj **Visa virtuella nätverk** **på**sidan Azure Firewall Manager på sidan Azure Firewall Manager .
4. Välj **Konvertera virtuella nätverk**.
5. Välj **VNet-hubb** och välj sedan **Nästa: Azure Firewall**.
6. För **brandväggsprincipen**väljer du **Pol-Net01**.
7. Välj **Nästa " Recension + bekräfta**
8. Granska informationen och välj sedan **Bekräfta**.


   Det tar några minuter att distribuera.
7. När distributionen är klar går du till resursgruppen **FW-Hybrid-Test** och väljer brandväggen.
9. Observera **den privata IP-adressen** för brandväggen på sidan **Översikt.** Du kommer att använda den senare när du skapar standardvägen.

## <a name="create-and-connect-the-vpn-gateways"></a>Skapa och ansluta VPN-gatewayer

De virtuella hubbnätverken och de lokala virtuella nätverken ansluts via VPN-gatewayer.

### <a name="create-a-vpn-gateway-for-the-hub-virtual-network"></a>Skapa en VPN-gateway för det virtuella hubbnätverket

Skapa nu VPN-gatewayen för det virtuella hubbnätverket. Nätverk-till-nätverk-konfigurationer kräver VpnType RouteBased. Att skapa en VPN-gateway kan ofta ta 45 minuter eller mer, beroende på vald VPN-gateway-SKU.

1. På startsidan för Azure-portalen väljer du **Skapa en resurs**.
2. Skriv **den virtuella nätverksgatewayen** i söktextrutan och tryck på **Retur**.
3. Välj **Virtuell nätverksgateway**och välj **Skapa**.
4. För **Namn**, skriv **GW-hubb**.
5. För **Region**väljer du **(USA) östra USA**.
6. För **gateway-typ**väljer du **VPN**.
7. För **VPN-typ**väljer du **Ruttbaserad**.
8. För **SKU**väljer du **Basic**.
9. För **virtuellt nätverk**väljer du **VNet-hubb**.
10. För **offentlig IP-adress**väljer du **Skapa ny**och skriver **VNet-hub-GW-pip** för namnet.
11. Acceptera de återstående standardinställningarna och välj sedan **Granska + skapa**.
12. Granska konfigurationen och välj sedan **Skapa**.

### <a name="create-a-vpn-gateway-for-the-on-premises-virtual-network"></a>Skapa en VPN-gateway för det lokala virtuella nätverket

Skapa nu VPN-gatewayen för det lokala virtuella nätverket. Nätverk-till-nätverk-konfigurationer kräver VpnType RouteBased. Att skapa en VPN-gateway kan ofta ta 45 minuter eller mer, beroende på vald VPN-gateway-SKU.

1. På startsidan för Azure-portalen väljer du **Skapa en resurs**.
2. Skriv **den virtuella nätverksgatewayen** i söktextrutan och tryck på **Retur**.
3. Välj **Virtuell nätverksgateway**och välj **Skapa**.
4. För **Namn**, skriv **GW-Onprem**.
5. För **Region**väljer du **(USA) östra USA**.
6. För **gateway-typ**väljer du **VPN**.
7. För **VPN-typ**väljer du **Ruttbaserad**.
8. För **SKU**väljer du **Basic**.
9. För **virtuellt nätverk**väljer du **VNet-Onprem**.
10. För **offentlig IP-adress**väljer du **Använd befintliga*och väljer **VNet-Onprem-GW-pip** för namnet.
11. Acceptera de återstående standardinställningarna och välj sedan **Granska + skapa**.
12. Granska konfigurationen och välj sedan **Skapa**.

### <a name="create-the-vpn-connections"></a>Skapa VPN-anslutningarna

Nu kan du skapa VPN-anslutningar mellan navet och lokala gateways.

I det här steget skapar du anslutningen från det virtuella hubbnätverket till det lokala virtuella nätverket. Du ser en delad nyckel som refereras i exemplen. Du kan använda egna värden för den delade nyckeln. Det är viktigt att den delade nyckeln matchar båda anslutningarna. Att skapa en anslutning kan ta en stund att slutföra.

1. Öppna resursgruppen **FW-Hybrid-Test** och välj **GW-hubbgatewayen.**
2. Välj **Anslutningar** i den vänstra kolumnen.
3. Välj **Lägg till**.
4. Anslutningsnamnet, skriv **Hub-to-Onprem**.
5. Välj **VNet-till-VNet** för **anslutningstyp**.
6. För den **andra virtuella nätverksgatewayen**väljer du **GW-Onprem**.
7. För **delad nyckel (PSK)** skriver du **AzureA1b2C3**.
8. Välj **OK**.

Skapa anslutningen mellan det lokala virtuella nätverket och det virtuella hubbnätverket. Det här steget liknar det föregående steget förutom att du skapar anslutningen från Vnet-Onprem till VNet-hub. Kontrollera att de delade nycklarna matchar. Anslutningen upprättas efter några minuter.

1. Öppna resursgruppen **FW-Hybrid-Test** och välj **GW-Onprem-gatewayen.**
2. Välj **Anslutningar** i den vänstra kolumnen.
3. Välj **Lägg till**.
4. Anslutningsnamnet, skriv **Onprem-to-Hub**.
5. Välj **VNet-till-VNet** för **anslutningstyp**.
6. För den **andra virtuella nätverksgatewayen**väljer du **GW-hubb**.
7. För **delad nyckel (PSK)** skriver du **AzureA1b2C3**.
8. Välj **OK**.


#### <a name="verify-the-connection"></a>Verifiera anslutningen

Efter ungefär fem minuter eller så ska statusen för båda anslutningarna vara **Ansluten**.

![Gatewayanslutningar](media/secure-hybrid-network/gateway-connections.png)

## <a name="peer-the-hub-and-spoke-virtual-networks"></a>Peera de virtuella hubb- och ekernätverken

Peera nu de virtuella hubb- och ekernätverken.

1. Öppna resursgruppen **FW-Hybrid-Test** och välj det virtuella nätverket **VNet-hubb.**
2. Välj **Peerings**i den vänstra kolumnen .
3. Välj **Lägg till**.
4. För **Namn**skriver du **HubtoSpoke**.
5. För det **virtuella nätverket**väljer du **VNet-eker**
6. Om du vill ha namnet på peering från VNetSpoke till VNet-hub, skriver **du SpoketoHub**.
7. Välj **Tillåt gatewaytransitering**.
8. Välj **OK**.

### <a name="configure-additional-settings-for-the-spoketohub-peering"></a>Konfigurera ytterligare inställningar för SpoketoHub-peering

Du måste aktivera **tillåt vidarebefordrad trafik** på SpoketoHub-peering.

1. Öppna resursgruppen **FW-Hybrid-Test** och välj det virtuella nätverket **VNet-Spoke.**
2. Välj **Peerings**i den vänstra kolumnen .
3. Välj **SpoketoHub-peering.**
4. Under **Tillåt vidarebefordrad trafik från VNet-hub till VNet-Spoke**väljer du **Aktiverad**.
5. Välj **Spara**.

## <a name="create-the-routes"></a>Skapa vägarna

Därefter skapar du några vägar:

- En väg från hubbgateway-undernätet till ekerundernätet via brandväggens IP-adress
- En standardväg från ekerundernätet via brandväggens IP-adress

1. På startsidan för Azure-portalen väljer du **Skapa en resurs**.
2. Skriv **flödestabell** i söktextrutan och tryck på **Retur**.
3. Välj **rutttabell**.
4. Välj **Skapa**.
5. Skriv **UDR-Hub-Spoke**för namnet .
6. Välj **FW-Hybrid-Test** för resursgruppen.
8. För **Plats**väljer du **(USA) östra USA).**
9. Välj **Skapa**.
10. När flödestabellen har skapats markerar du den för att öppna flödestabellsidan.
11. Välj **Rutter** i den vänstra kolumnen.
12. Välj **Lägg till**.
13. För flödesnamnet skriver du **ToSpoke**.
14. För adressprefixet skriver du **10.6.0.0/16**.
15. För nästa hopptyp väljer du **Virtuell installation**.
16. För nästa hoppadress skriver du brandväggens privata IP-adress som du noterade tidigare.
17. Välj **OK**.

Associerar nu rutten till undernätet.

1. På sidan **UDR-Hub-Spoke - Routes** väljer du **Undernät**.
2. Välj **Associera**.
4. Under **Virtuellt nätverk**väljer du **VNet-hubb**.
5. Under **Undernät**väljer du **GatewaySubnet**.
6. Välj **OK**.

Skapa nu standardvägen från ekerundernätet.

1. På startsidan för Azure-portalen väljer du **Skapa en resurs**.
2. Skriv **flödestabell** i söktextrutan och tryck på **Retur**.
3. Välj **rutttabell**.
5. Välj **Skapa**.
6. För namnet skriver du **UDR-DG**.
7. Välj **FW-Hybrid-Test** för resursgruppen.
8. För **Plats**väljer du **(USA) östra USA).**
4. För **dirigerar virtual network gateway väljer**du **Inaktiverad**.
1. Välj **Skapa**.
2. När flödestabellen har skapats markerar du den för att öppna flödestabellsidan.
3. Välj **Rutter** i den vänstra kolumnen.
4. Välj **Lägg till**.
5. Skriv **ToHub**för vägnamnet .
6. För adressprefixet skriver du **0.0.0.0/0**.
7. För nästa hopptyp väljer du **Virtuell installation**.
8. För nästa hoppadress skriver du brandväggens privata IP-adress som du noterade tidigare.
9. Välj **OK**.

Associerar nu rutten till undernätet.

1. På sidan **UDR-DG - Rutter** väljer du **Undernät**.
2. Välj **Associera**.
4. Under **Virtuellt nätverk**väljer du **VNet-eker**.
5. Välj **SN-arbetsbelastning**under **Undernät**.
6. Välj **OK**.

## <a name="create-virtual-machines"></a>Skapa virtuella datorer

Skapa nu ekerarbetsbelastningen och de lokala virtuella datorerna, och placera dem i respektive undernät.

### <a name="create-the-workload-virtual-machine"></a>Skapa den virtuella arbetsbelastningsdatorn

Skapa en virtuell dator i det virtuella ekrarna som kör IIS utan offentlig IP-adress.

1. På startsidan för Azure-portalen väljer du **Skapa en resurs**.
2. Under **Populär**väljer du **Windows Server 2016 Datacenter**.
3. Ange följande värden för den virtuella datorn:
    - **Resursgrupp** - Välj **FW-Hybrid-Test**.
    - **Namn på virtuell dator**: *VM-Spoke-01*.
    - **Region** - *(USA) östra USA)*.
    - **Användarnamn**: *azureuser*.
    - **Lösenord**: skriv ditt lösenord

4. Välj **Nästa:Diskar**.
5. Acceptera standardinställningarna och välj **Nästa: Nätverk .**
6. Välj **VNet-Spoke** för det virtuella nätverket och undernätet är **SN-arbetsbelastning**.
7. För **Offentlig IP**väljer du **Ingen**.
8. För **offentliga inkommande portar**väljer du **Tillåt valda portar**och väljer sedan **HTTP (80)** och **RDP (3389)**
9. Välj **Nästa:Hantering**.
10. För **startdiagnostik**väljer du **Av**.
11. Välj **Granska+Skapa**, granska inställningarna på sammanfattningssidan och välj sedan **Skapa**.

### <a name="install-iis"></a>Installera IIS

1. Öppna Cloud Shell från Azure-portalen och se till att det är inställt på **PowerShell**.
2. Kör följande kommando för att installera IIS på den virtuella datorn och ändra platsen om det behövs:

   ```azurepowershell-interactive
   Set-AzVMExtension `
           -ResourceGroupName FW-Hybrid-Test `
           -ExtensionName IIS `
           -VMName VM-Spoke-01 `
           -Publisher Microsoft.Compute `
           -ExtensionType CustomScriptExtension `
           -TypeHandlerVersion 1.4 `
           -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell      Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
           -Location EastUS
   ```

### <a name="create-the-on-premises-virtual-machine"></a>Skapa den lokala virtuella datorn

Det här är en virtuell dator som du använder för att ansluta med fjärrskrivbord till den offentliga IP-adressen. Därifrån kan du sedan ansluta till den lokala servern via brandväggen.

1. På startsidan för Azure-portalen väljer du **Skapa en resurs**.
2. Under **Populär**väljer du **Windows Server 2016 Datacenter**.
3. Ange följande värden för den virtuella datorn:
    - **Resursgrupp** - Välj befintlig och välj sedan **FW-Hybrid-Test**.
    - **Namn på** - virtuell dator*VM-Onprem*.
    - **Region** - *(USA) östra USA)*.
    - **Användarnamn**: *azureuser*.
    - **Lösenord**: skriv ditt lösenord.

4. Välj **Nästa:Diskar**.
5. Acceptera standardinställningarna och välj **Nästa:Nätverk .**
6. Välj **VNet-Onprem** för virtuellt nätverk och kontrollera att undernätet är **SN-Corp**.
7. För **offentliga inkommande portar**väljer du **Tillåt valda portar**och väljer sedan **RDP (3389)**
8. Välj **Nästa:Hantering**.
9. För **Startdiagnostik**väljer du **Av**.
10. Välj **Granska+Skapa**, granska inställningarna på sammanfattningssidan och välj sedan **Skapa**.

## <a name="test-the-firewall"></a>testa brandväggen.

1. Observera först den privata IP-adressen för virtuell dator för VM-Spoke-01 på sidan VM-Spoke-01 Overview.

2. Från Azure-portalen ansluter du till den virtuella datorn **VM-Onprem**.
<!---2. Open a Windows PowerShell command prompt on **VM-Onprem**, and ping the private IP for **VM-spoke-01**.

   You should get a reply.--->
3. Öppna en webbläsare på **VM-Onprem** och gå till http://\<privat IP-adress för VM-spoke-01\>.

   Du bör se **VM-spoke-01** ![webbsida: VM-Spoke-01 webbsida](media/secure-hybrid-network/vm-spoke-01-web.png)

4. Öppna ett fjärrskrivbord till **VM-spoke-01** på den privata IP-adressen från den virtuella datorn **VM-Onprem.**

   Anslutningen ska lyckas och du bör kunna logga in.

Så nu har du verifierat att brandväggsreglerna fungerar:

<!---- You can ping the server on the spoke VNet.--->
- Du kan bläddra i webbservern på det virtuella ekernätverket.
- Du kan ansluta till servern på det virtuella ekernätverket med hjälp av RDP.

Ändra sedan brandväggsnätverksregelns insamlingsåtgärd till **Neka** för att verifiera att brandväggsreglerna fungerar som förväntat.

1. Öppna resursgruppen **FW-Hybrid-Test** och välj brandväggsprincipen **Pol-Net01.**
2. Under **Inställningar**väljer du **Regler**.
3. Under **Nätverksregler**väljer du **RCNet01-regelsamlingen,** väljer ellipserna (...) och väljer **Redigera**.
4. För **åtgärd för regelsamling**väljer du **Neka**.
5. Välj **Spara**.

Stäng alla befintliga fjärrskrivbord och webbläsare på **VM-Onprem** innan du testar de ändrade reglerna. När regelsamlingsuppdateringen har slutförts kör du testerna igen. De bör alla misslyckas den här gången.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan behålla dina brandväggsresurser för nästa självstudie eller, om de inte längre behövs, så tar du bort resursgruppen **FW-Hybrid-Test** för att ta bort alla brandväggsrelaterade resurser.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudiekurs: Säkra ditt virtuella WAN med förhandsversionen av Azure Firewall Manager](secure-cloud-network.md)