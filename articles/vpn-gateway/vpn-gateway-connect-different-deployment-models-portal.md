---
title: 'Ansluta klassiska virtuella nätverk till Virtuella Azure Resource Manager-nätverk: Portal | Microsoft-dokument'
description: Steg för att ansluta klassiska virtuella nätverk till resurshanteraren virtuella nätverk med VPN Gateway och portalen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: cherylmc
ms.openlocfilehash: 5e64cb2db2bd16a881334779a1c6f1ef19296da2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152031"
---
# <a name="connect-virtual-networks-from-different-deployment-models-using-the-portal"></a>Ansluta virtuella nätverk från olika distributionsmodeller med hjälp av portalen

Den här artikeln visar hur du ansluter klassiska virtuella nätverk till virtuella nätverk för Resurshanteraren så att resurserna i de separata distributionsmodellerna kan kommunicera med varandra. Stegen i den här artikeln använder främst Azure-portalen, men du kan också skapa den här konfigurationen med PowerShell genom att välja artikeln från den här listan.

> [!div class="op_single_selector"]
> * [Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Powershell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

Att ansluta ett klassiskt virtuella nätverk till ett resurshanteraren-nätverk påminner om att ansluta ett virtuella nätverk till en lokal plats. Båda typerna av anslutning använder en VPN-gateway för att få en säker tunnel med IPsec/IKE. Du kan skapa en anslutning mellan virtuella nätverk som finns i olika prenumerationer och i olika regioner. Du kan också ansluta virtuella nätverk som redan har anslutningar till lokala nätverk, så länge gatewayen som de har konfigurerats med är dynamisk eller flödesbaserad. Mer information om anslutningar mellan virtuella nätverk finns i [Vanliga frågor om VNet-till-VNet](#faq) i slutet av den här artikeln. 

Om du inte redan har en virtuell nätverksgateway och inte vill skapa en, kanske du i stället vill överväga att ansluta dina virtuella nätverk med hjälp av VNet Peering. Ingen VPN-gateway används för VNet-peering. Mer information finns i [VNet peering (Vnet-peering)](../virtual-network/virtual-network-peering-overview.md).

### <a name="before-you-begin"></a><a name="before"></a>Innan du börjar



* Dessa steg förutsätter att båda virtuella nätverken redan har skapats. Om du använder den här artikeln som en övning och inte har virtuella nätverk finns det länkar i stegen som hjälper dig att skapa dem.
* Kontrollera att adressintervallen för virtuella nätverk inte överlappar varandra eller överlappar något av intervallen för andra anslutningar som gatewayerna kan vara anslutna till.
* Installera de senaste PowerShell-cmdlets för både Resource Manager och Service Management (klassisk). I den här artikeln använder vi både Azure-portalen och PowerShell. PowerShell krävs för att skapa anslutningen från det klassiska virtuella nätverket till resurshanterarens virtuella nätverk. Mer information finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/overview). 

### <a name="example-settings"></a><a name="values"></a>Exempelinställningar

Du kan använda värdena till att skapa en testmiljö eller hänvisa till dem för att bättre förstå exemplen i den här artikeln.

**Klassiska virtuella nätverk**

VNet-namn = ClassicVNet <br>
Adressutrymme = 10.0.0.0/24 <br>
Undernätsnamn = Undernät-1 <br>
Undernätsadressintervall = 10.0.0.0/27 <br>
Prenumeration = den prenumeration du vill använda <br>
Resursgrupp = ClassicRG <br>
Plats = Västra USA <br>
GatewaySubnet = 10.0.0.32/28 <br>
Lokal plats = RMVNetLocal <br>

**ResurshanterarenS virtuella nätverk**

VNet-namn = RMVNet <br>
Adressutrymme = 192.168.0.0/16 <br>
Resursgrupp = RG1 <br>
Plats = Östra USA <br>
Undernätsnamn = Undernät-1 <br>
Adressintervall = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Namn på virtuell nätverksgateway = RMGateway <br>
Gateway-typ = VPN <br>
VPN-typ = Ruttbaserad <br>
SKU = VpnGw1 <br>
Plats = Östra USA <br>
Virtuellt nätverk = RMVNet <br> (associera VPN-gatewayen till det här virtuella nätverket) Första IP-konfiguration = rmgwpip <br> (gateway offentlig IP-adress) Lokal nätverksgateway = ClassicVNetLokal <br>
Anslutningsnamn = RMtoClassic

### <a name="connection-overview"></a><a name="connectoverview"></a>Översikt över anslutning

För den här konfigurationen skapar du en VPN-gatewayanslutning via en IPsec/IKE VPN-tunnel mellan de virtuella nätverken. Kontrollera att inget av dina virtuella nätverksintervall överlappar varandra eller med något av de lokala nätverk som de ansluter till.

I följande tabell visas ett exempel på hur exempelv-nätverk och lokala webbplatser definieras:

| Virtual Network | Adressutrymme | Region | Ansluter till lokal nätverksplats |
|:--- |:--- |:--- |:--- |
| ClassicVNet |(10.0.0.0/24) |USA, västra | RMVNetLokal (192.168.0.0/16) |
| RMVNet (på andra sätt) | (192.168.0.0/16) |USA, östra |ClassicVNetLocal (10.0.0.0/24) |

## <a name="section-1---configure-the-classic-vnet-settings"></a><a name="classicvnet"></a>Avsnitt 1 - Konfigurera de klassiska VNet-inställningarna

I det här avsnittet skapar du det klassiska virtuella nätverket, det lokala nätverket (lokal plats) och den virtuella nätverksgatewayen. Skärmbilderna anges som exempel. Var noga med att ersätta värdena [Example](#values) med dina egna eller använd exempelvärdena.

### <a name="1-create-a-classic-vnet"></a>1. <a name="classicvnet"> </a>Skapa ett klassiskt virtuella nätverk

Om du inte har ett klassiskt virtuella nätverk och kör dessa steg som en övning kan du skapa ett virtuella nätverk med hjälp av [den](#values) [här artikeln](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) och exempelinställningarna ovanifrån.

Om du redan har ett VNet med en VPN-gateway kontrollerar du att gatewayen är dynamisk. Om det är statiskt måste du först ta bort VPN-gatewayen innan du fortsätter att [konfigurera den lokala platsen](#local).

1. Öppna [Azure Portal](https://ms.portal.azure.com) och logga in med ditt Azure-konto.
2. Klicka på **+ Skapa en resurs** för att öppna sidan "Nytt".
3. Skriv "Virtuellt nätverk" i fältet Sök på marknadsplatsen. Om du i stället väljer Nätverk -> virtuellt nätverk får du inte möjlighet att skapa ett klassiskt virtuellt nätverk.
4. Leta upp "Virtuellt nätverk" från den returnerade listan och klicka på den för att öppna sidan Virtuellt nätverk. 
5. På den virtuella nätverkssidan väljer du "Classic" för att skapa ett klassiskt virtuellt nätverk. Om du tar standard här, kommer du att avsluta med en Resource Manager VNet istället.

### <a name="2-configure-the-local-site"></a>2. <a name="local"> </a>Konfigurera den lokala webbplatsen

1. Navigera till **alla resurser** och leta reda på **ClassicVNet** i listan.
2. Klicka på **Gateway** i avsnittet **Inställningar** på menyn och klicka sedan på banderollen för att skapa en gateway.
  ![Konfigurera en VPN gateway](./media/vpn-gateway-connect-different-deployment-models-portal/gatewaygraphic.png "Konfigurera en VPN gateway")
3. Välj **Plats-till-plats**för **anslutningstyp**på sidan **Ny VPN-anslutning** .
4. För **Lokal webbplats**klickar du på Konfigurera obligatoriska **inställningar**. Då öppnas sidan **Lokal webbplats.**
5. På sidan **Lokal webbplats** skapar du ett namn som refererar till resurshanterarens virtuella nätverk. Till exempel "RMVNetLocal".
6. Om VPN-gatewayen för Resource Manager-nätverket redan har en offentlig IP-adress använder du värdet för **IP-adressfältet för VPN-gatewayen.** Om du gör de här stegen som en övning eller ännu inte har en virtuell nätverksgateway för ditt Resource Manager-nätverk kan du skapa en platshållar-IP-adress. Kontrollera att platshållarens IP-adress använder ett giltigt format. Senare ersätter du platshållarens IP-adress med den offentliga IP-adressen för Resource Manager-gatewayen för virtuella nätverk.
7. För **klientadressutrymme**använder du [värdena](#connectoverview) för det virtuella nätverkets IP-adressutrymmen för resurshanterarens virtuella nätverk. Den här inställningen används för att ange adressutrymmen som ska dirigeras till det virtuella nätverket Resource Manager. I exemplet använder vi 192.168.0.0/16, adressintervallet för RMVNet.
8. Klicka på **OK** om du vill spara värdena och gå tillbaka till sidan **Ny VPN-anslutning.**

### <a name="3-create-the-virtual-network-gateway"></a><a name="classicgw"></a>3. Skapa den virtuella nätverksgatewayen

1. Markera kryssrutan **Skapa gateway direkt** på sidan Ny **VPN-anslutning.**
2. Klicka på **Valfri gatewaykonfiguration** för att öppna sidan **Gatewaykonfiguration**.

   ![Öppna konfigurationssida för gateway](./media/vpn-gateway-connect-different-deployment-models-portal/optionalgatewayconfiguration.png "Öppna konfigurationssida för gateway")
3. Klicka på **Undernät - Konfigurera nödvändiga inställningar** för att öppna sidan Lägg till **undernät.** **Namnet** är redan konfigurerat med önskat värde: **GatewaySubnet**.
4. **Adressintervallet** refererar till intervallet för gateway-undernätet. Även om du kan skapa ett gateway-undernät med ett /29-adressintervall (3 adresser), rekommenderar vi att du skapar ett gateway-undernät som innehåller fler IP-adresser. Detta kommer att rymma framtida konfigurationer som kan kräva mer tillgängliga IP-adresser. Använd om möjligt /27 eller /28. Om du använder dessa steg som en övning kan du referera till [exempelvärdena](#values). I det här exemplet använder vi '10.0.0.32/28'. Klicka på **OK** för att skapa gateway-undernätet.
5. På **sidan Gateway-konfiguration** refererar **Storlek** till gateway-SKU. Välj gateway-SKU för VPN-gatewayen.
6. Kontrollera **att routningstypen** är **dynamisk**och klicka sedan på **OK** för att återgå till sidan **Ny VPN-anslutning.**
7. På sidan **Ny VPN-anslutning** klickar du på **OK** för att börja skapa din VPN-gateway. Det kan ta upp till 45 minuter att skapa en VPN-gateway.

### <a name="4-copy-the-virtual-network-gateway-public-ip-address"></a><a name="ip"></a>4. Kopiera den offentliga IP-adressen för den virtuella nätverksgatewayen

När den virtuella nätverksgatewayen har skapats kan du visa gateway-IP-adressen. 

1. Navigera till ditt klassiska virtuella nätverk och klicka på **Översikt**.
2. Klicka på **VPN-anslutningar** för att öppna sidan VPN-anslutningar. På sidan VPN-anslutningar kan du visa den offentliga IP-adressen. Det här är den offentliga IP-adress som tilldelats din virtuella nätverksgateway. Anteckna IP-adressen. Du använder den i senare steg när du arbetar med konfigurationsinställningarna för resource manager-konfigurationen för lokala nätverksgatewayer. 
3. Du kan visa status för gatewayanslutningarna. Observera att den lokala nätverkswebbplats som du skapade visas som "Ansluta". Statusen ändras när du har skapat dina anslutningar. Du kan stänga den här sidan när du är klar med statusen.

## <a name="section-2---configure-the-resource-manager-vnet-settings"></a><a name="rmvnet"></a>Avsnitt 2 - Konfigurera inställningarna för Resurshanteraren

I det här avsnittet skapar du den virtuella nätverksgatewayen och den lokala nätverksgatewayen för ditt Resource Manager-VNet. Skärmbilderna anges som exempel. Var noga med att ersätta värdena [Example](#values) med dina egna eller använd exempelvärdena.

### <a name="1-create-a-virtual-network"></a>1. Skapa ett virtuellt nätverk

**Exempelvärden:**

* VNet-namn = RMVNet <br>
* Adressutrymme = 192.168.0.0/16 <br>
* Resursgrupp = RG1 <br>
* Plats = Östra USA <br>
* Undernätsnamn = Undernät-1 <br>
* Adressintervall = 192.168.1.0/24 <br>

Om du inte har ett virtuellt resurshanteraren och kör dessa steg som en övning skapar du ett virtuellt nätverk med stegen i [Skapa ett virtuellt nätverk](../virtual-network/quick-create-portal.md)med hjälp av exempelvärdena.

### <a name="2-create-a-virtual-network-gateway"></a><a name="creategw"></a>2. Skapa en virtuell nätverksgateway

I det här steget ska du skapa den virtuella nätverksgatewayen för ditt virtuella nätverk. Att skapa en gateway kan ofta ta 45 minuter eller mer, beroende på vald gateway-SKU.

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

**Exempelvärden:**

* Namn på virtuell nätverksgateway = RMGateway <br>
* Gateway-typ = VPN <br>
* VPN-typ = Ruttbaserad <br>
* SKU = VpnGw1 <br>
* Plats = Östra USA <br>
* Virtuellt nätverk = RMVNet <br>
* GatewaySubnet = 192.168.0.0/26 <br>
* Första IP-konfiguration = rmgwpip <br>

[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="3-create-a-local-network-gateway"></a><a name="createlng"></a>3. Skapa en lokal nätverksgateway

**Exempelvärden:** Lokal nätverksgateway = ClassicVNetLokal

| Virtual Network | Adressutrymme | Region | Ansluter till lokal nätverksplats |Offentlig IP-adress för gateway|
|:--- |:--- |:--- |:--- |:--- |
| ClassicVNet |(10.0.0.0/24) |USA, västra | RMVNetLokal (192.168.0.0/16) |Den offentliga IP-adressen som har tilldelats ClassicVNet-gatewayen|
| RMVNet (på andra sätt) | (192.168.0.0/16) |USA, östra |ClassicVNetLocal (10.0.0.0/24) |Den offentliga IP-adressen som har tilldelats RMVNet-gatewayen.|

Den lokala nätverksgatewayen anger adressintervallet och den offentliga IP-adress som är associerad med det klassiska virtuella nätverket och dess virtuella nätverksgateway. Om du gör dessa steg som en övning läser du exempelvärdena.

[!INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]

## <a name="section-3---modify-the-classic-vnet-local-site-settings"></a><a name="modifylng"></a>Avsnitt 3 - Ändra de klassiska inställningarna för virtuella nätverk på lokal webbplats

I det här avsnittet ersätter du platshållarens IP-adress som du använde när du angav de lokala platsinställningarna med IP-adressen för Resource Manager VPN-gatewayen. I det här avsnittet används de klassiska PowerShell-cmdletsna (SM).

1. Navigera till det klassiska virtuella nätverket i Azure-portalen.
2. Klicka på **Översikt**på sidan för det virtuella nätverket .
3. Klicka på namnet på den lokala webbplatsen i bilden i avsnittet **VPN-anslutningar.**

   ![VPN-anslutningar](./media/vpn-gateway-connect-different-deployment-models-portal/vpnconnections.png "VPN-anslutningar")
4. Klicka på namnet på webbplatsen på sidan VPN-anslutningar på sidan **VPN-anslutningar** på plats till plats.

   ![Webbplatsnamn](./media/vpn-gateway-connect-different-deployment-models-portal/sitetosite3.png "Namn på lokal webbplats")
5. På anslutningssidan för den lokala webbplatsen klickar du på namnet på den lokala webbplatsen för att öppna sidan **Lokal webbplats.**

   ![Öppen lokal webbplats](./media/vpn-gateway-connect-different-deployment-models-portal/openlocal.png "Öppna lokal webbplats")
6. På sidan **Lokal webbplats** ersätter du IP-adressen för **VPN-gatewayen** med IP-adressen för Resource Manager-gatewayen.

   ![Gateway-ip-adress](./media/vpn-gateway-connect-different-deployment-models-portal/gwipaddress.png "GATEWAY IP-adress")
7. Klicka på **OK** för att uppdatera IP-adressen.

## <a name="section-4---create-resource-manager-to-classic-connection"></a><a name="RMtoclassic"></a>Avsnitt 4 - Skapa Resurshanteraren till klassisk anslutning

I de här stegen konfigurerar du anslutningen från Resource Manager VNet till det klassiska virtuella nätverket med hjälp av Azure-portalen.

1. Leta reda på den lokala nätverksgatewayen i **Alla resurser.** I vårt exempel är den lokala nätverksgatewayen **ClassicVNetLocal**.
2. Klicka på **Konfiguration** och kontrollera att IP-adressvärdet är VPN-gatewayen för det klassiska virtuella nätverket. Uppdatera om det behövs och klicka sedan på **Spara**. Stäng sidan.
3. Klicka på den lokala nätverksgatewayen i **Alla resurser.**
4. Klicka på **Anslutningar** för att öppna sidan Anslutningar.
5. Klicka på länken **+** på sidan **Anslutningar.**
6. Namnge anslutningen på sidan **Lägg till anslutning.** Till exempel "RMtoClassic".
7. **Plats-till-plats** är redan markerat på den här sidan.
8. Välj den virtuella nätverksgateway som du vill associera med den här platsen.
9. Skapa en **delad nyckel**. Den här nyckeln används också i anslutningen som du skapar från det klassiska virtuella nätverket till resurshanterarens virtuella nätverk. Du kan generera nyckeln eller göra en upp. I vårt exempel använder vi "abc123", men du kan (och bör) använda något mer komplext.
10. Klicka på **OK** för att skapa anslutningen.

## <a name="section-5---create-classic-to-resource-manager-connection"></a><a name="classictoRM"></a>Avsnitt 5 - Skapa klassisk till Resource Manager-anslutning

I de här stegen konfigurerar du anslutningen från det klassiska virtuella nätverket till resurshanterarens virtuella nätverk. Dessa steg kräver PowerShell. Du kan inte skapa den här anslutningen i portalen. Kontrollera att du har hämtat och installerat både de klassiska (SM) och Resurshanteraren (RM) PowerShell-cmdlets.

### <a name="1-connect-to-your-azure-account"></a>1. Anslut till ditt Azure-konto

Öppna PowerShell-konsolen med förhöjda rättigheter och logga in på ditt Azure-konto. När du har loggat in hämtas dina kontoinställningar så att de är tillgängliga för Azure PowerShell. Följande cmdlet uppmanar dig att ange inloggningsuppgifter för ditt Azure-konto för Resurshanterarens distributionsmodell:

```powershell
Connect-AzAccount
```

Hämta en lista över dina Azure-prenumerationer.

```powershell
Get-AzSubscription
```

Om du har mer än en prenumeration anger du den prenumeration som du vill använda.

```powershell
Select-AzSubscription -SubscriptionName "Name of subscription"
```

Logga sedan in för att använda de klassiska PowerShell-cmdlets (Service Management). Använd följande kommando för att lägga till ditt Azure-konto för den klassiska distributionsmodellen:

```powershell
Add-AzureAccount
```

Få en lista över dina prenumerationer. Det här steget kan vara nödvändigt när du lägger till servicehanterings-cmdlets, beroende på din Azure-modulinstallation.

```powershell
Get-AzureSubscription
```

Om du har mer än en prenumeration anger du den prenumeration som du vill använda.

```powershell
Select-AzureSubscription -SubscriptionName "Name of subscription"
```

### <a name="2-view-the-network-configuration-file-values"></a>2. Visa nätverkskonfigurationsfilvärdena

När du skapar ett virtuella nätverk i Azure-portalen visas inte det fullständiga namnet som Azure använder i Azure-portalen. Ett virtuella nätverk som verkar heta "ClassicVNet" i Azure-portalen kan till exempel ha ett mycket längre namn i nätverkskonfigurationsfilen. Namnet kan se ut ungefär som: "Group ClassicRG ClassicVNet". I de här stegen hämtar du nätverkskonfigurationsfilen och visar värdena.

Skapa en katalog på datorn och exportera sedan nätverkskonfigurationsfilen till katalogen. I det här exemplet exporteras nätverkskonfigurationsfilen till C:\AzureNet.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

Öppna filen med en textredigerare och visa namnet på ditt klassiska virtuella nätverk. Använd namnen i nätverkskonfigurationsfilen när du kör dina PowerShell-cmdlets.

- VNet-namn listas som **VirtualNetworkSite-namn =**
- Webbplatsnamn listas som **LocalNetworkSite namn=**

### <a name="3-create-the-connection"></a>3. Skapa anslutningen

Ange den delade nyckeln och skapa anslutningen från det klassiska virtuella nätverket till resurshanterarens virtuella nätverk. Du kan inte ange den delade nyckeln med hjälp av portalen. Kontrollera att du kör de här stegen när du är inloggad med den klassiska versionen av PowerShell-cmdlets. Det gör du genom att använda **Add-AzureAccount**. Annars kan du inte ange "-AzureVNetGatewayKey".

- I det här exemplet är **-VNetName** namnet på det klassiska virtuella nätverket som finns i nätverkskonfigurationsfilen. 
- **-LocalNetworkSiteName** är det namn som du har angett för den lokala platsen, enligt namnet i nätverkskonfigurationsfilen.
- **-SharedKey** är ett värde som du genererar och anger. I det här exemplet använde vi *abc123*, men du kan generera något mer komplext. Det viktiga är att värdet du anger här måste vara samma värde som du angav när du skapade Resurshanteraren till klassisk anslutning.

```powershell
Set-AzureVNetGatewayKey -VNetName "Group ClassicRG ClassicVNet" `
-LocalNetworkSiteName "172B9E16_RMVNetLocal" -SharedKey abc123
```

## <a name="section-6---verify-your-connections"></a><a name="verify"></a>Avsnitt 6 - Verifiera dina anslutningar

Du kan verifiera dina anslutningar med hjälp av Azure-portalen eller PowerShell. När du verifierar kan du behöva vänta en minut eller två när anslutningen skapas. När en anslutning lyckas ändras anslutningstillståndet från "Ansluta" till Ansluten.

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>Så här verifierar du anslutningen från det klassiska virtuella nätverket till ditt resurshanterarens virtuella nätverk

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

### <a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>Så här verifierar du anslutningen från resource manager-nätverket till ditt klassiska virtuella nätverk

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="vnet-to-vnet-faq"></a><a name="faq"></a>Vanliga frågor och svar om VNet-till-VNet

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]
