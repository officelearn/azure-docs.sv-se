---
title: 'Ansluta klassiska virtuella nätverk till Azure Resource Manager-VNets: Portal | Microsoft Docs'
description: Steg för att ansluta klassiska virtuella nätverk till Resource Manager-VNets med VPN-Gateway och portal
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 10/17/2018
ms.author: cherylmc
ms.openlocfilehash: 4d2edeaf7423d3a46becf386294d2dd8c46e9ab7
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55508342"
---
# <a name="connect-virtual-networks-from-different-deployment-models-using-the-portal"></a>Anslut virtuella nätverk från olika distributionsmodeller med hjälp av portalen

Den här artikeln visar hur du ansluter klassiska virtuella nätverk till Resource Manager-VNets så att resurserna som finns i separata distributionsmodeller ska kunna kommunicera med varandra. Stegen i den här artikeln i första hand använder Azure-portalen, men du kan också skapa den här konfigurationen med hjälp av PowerShell genom att välja artikeln från den här listan.

> [!div class="op_single_selector"]
> * [Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

Ansluta ett klassiskt virtuellt nätverk till ett Resource Manager-VNet liknar ansluta ett virtuellt nätverk till en lokal plats. Båda typerna av anslutning använder en VPN-gateway för att få en säker tunnel med IPsec/IKE. Du kan skapa en anslutning mellan virtuella nätverk som finns i olika prenumerationer och i olika regioner. Du kan också ansluta virtuella nätverk som redan har anslutningar till lokala nätverk, så länge som de har konfigurerats med gatewayen är dynamisk eller routningsbaserad. Mer information om anslutningar mellan virtuella nätverk finns i [Vanliga frågor om VNet-till-VNet](#faq) i slutet av den här artikeln. 

Om du inte redan har en virtuell nätverksgateway och inte vill skapa ett du istället du överväga att ansluta dina virtuella nätverk med VNet-Peering. Ingen VPN-gateway används för VNet-peering. Mer information finns i [VNET-peering](../virtual-network/virtual-network-peering-overview.md).

### <a name="before"></a>Innan du börjar

* Dessa instruktioner förutsätter att båda de virtuella nätverken har redan skapats. Om du använder den här artikeln som en övning och inte har virtuella nätverk finns länkar i steg för att skapa dem.
* Kontrollera att adressintervallen för den virtuella nätverken inte överlappar varandra eller överlappar med något av intervallen för andra anslutningar som gatewayer kan anslutas till.
* Installera det senaste PowerShell-cmdlet för både Resource Manager och Service Management (klassisk). I den här artikeln använder vi både Azure-portalen och PowerShell. PowerShell krävs för att skapa anslutningen från det klassiska virtuella nätverket till Resource Manager-VNet. Mer information finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/overview). 

### <a name="values"></a>Exempelinställningar

Du kan använda värdena till att skapa en testmiljö eller hänvisa till dem för att bättre förstå exemplen i den här artikeln.

**Klassiskt virtuellt nätverk**

Namn på virtuellt nätverk = ClassicVNet <br>
Adressutrymme = 10.0.0.0/24 <br>
Namn på undernät = Subnet-1 <br>
Adressintervall för undernätet = 10.0.0.0/27 <br>
Prenumeration = den prenumeration som du vill använda <br>
Resursgrupp = ClassicRG <br>
Plats = USA, västra <br>
GatewaySubnet = 10.0.0.32/28 <br>
Lokal plats = RMVNetLocal <br>

**Resource Manager-nätverk**

Namn på virtuellt nätverk = RMVNet <br>
Adressutrymme = 192.168.0.0/16 <br>
Resursgrupp = RG1 <br>
Plats = USA, östra <br>
Namn på undernät = Subnet-1 <br>
Adressintervall = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Det virtuella nätverkets gateway name = RMGateway <br>
Gateway-typ = VPN <br>
VPN-typ = routningsbaserad <br>
SKU: N = VpnGw1 <br>
Plats = USA, östra <br>
Virtuellt nätverk = RMVNet <br> (associera VPN-gatewayen till det här virtuella nätverket) Första IP-konfiguration = rmgwpip <br> (gateway offentlig IP-adress) Lokal nätverksgateway = ClassicVNetLocal <br>
Anslutningsnamn = RMtoClassic

### <a name="connectoverview"></a>Översikt över anslutning

För den här konfigurationen måste skapa du en VPN-gateway-anslutning via en IPsec/IKE VPN-tunnel mellan virtuella nätverk. Kontrollera att ingen av dina VNet-adressintervall överlappar varandra eller med någon av de lokala nätverk som de ansluter till.

I följande tabell visas ett exempel på hur exempel virtuella nätverk och lokala platser definieras:

| Virtual Network | Adressutrymme | Region | Ansluter till lokal nätverksplats |
|:--- |:--- |:--- |:--- |
| ClassicVNet |(10.0.0.0/24) |Västra USA | RMVNetLocal (192.168.0.0/16) |
| RMVNet | (192.168.0.0/16) |Östra USA |ClassicVNetLocal (10.0.0.0/24) |

## <a name="classicvnet"></a>Avsnitt 1 – konfigurera klassiska VNet-inställningarna

I det här avsnittet skapar du det klassiska virtuella nätverket, lokalt nätverk (lokal plats) och den virtuella nätverksgatewayen. Skärmbilderna anges som exempel. Se till att ersätta värdena med dina egna eller Använd den [exempel](#values) värden.

### 1. <a name="classicvnet"></a>Skapa ett klassiskt virtuellt nätverk

Om du inte har ett klassiskt virtuellt nätverk och kör de här stegen som en övning kan du skapa ett virtuellt nätverk med hjälp av [i den här artikeln](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) och [exempel](#values) inställningsvärden ovan.

Om du redan har ett virtuellt nätverk med en VPN-gateway kan du kontrollera att gatewayen är dynamiska. Om den är statisk, måste du först radera VPN-gatewayen innan du går vidare till [konfigurera den lokala platsen](#local).

1. Öppna [Azure Portal](https://ms.portal.azure.com) och logga in med ditt Azure-konto.
2. Klicka på **+ skapa en resurs** att öppna sidan ”New”.
3. Skriv ”virtuell nätverksgateway” i fältet ”Sök på marketplace”. Om du i stället välja nätverk -> virtuella nätverk, får du inte alternativet för att skapa ett klassiskt virtuellt nätverk.
4. Leta upp ”virtuell nätverksgateway” från den returnerade listan och klicka på den för att öppna sidan virtuellt nätverk. 
5. Välj ”klassisk” för att skapa ett klassiskt virtuellt nätverk på sidan virtuellt nätverk. Om du skapar standard här kan kommer du behöva skapa en Resource Manager-VNet i stället.

### 2. <a name="local"></a>Konfigurera den lokala platsen

1. Gå till **alla resurser** och leta upp den **ClassicVNet** i listan.
2. På den **översikt** sidan den **VPN-anslutningar** klickar du på **Gateway** att skapa en gateway.
  ![Konfigurera en VPN-gateway](./media/vpn-gateway-connect-different-deployment-models-portal/gatewaygraphic.png "konfigurera en VPN-gateway")
3. På den **ny VPN-anslutning** sidan för **anslutningstypen**väljer **plats-till-plats**.
4. För **lokal plats**, klickar du på **konfigurera nödvändiga inställningar**. Då öppnas det **lokal plats** sidan.
5. På den **lokal plats** sida, skapar du ett namn som refererar till Resource Manager-VNet. Till exempel ”RMVNetLocal”.
6. Om VPN-gatewayen för Resource Manager-VNet som redan har en offentlig IP-adress, använder du värdet för den **IP-adress för VPN-gateway** fält. Om du genomför de här stegen som en övning, eller ännu inte har en virtuell nätverksgateway för ditt VNet i Resource Manager, kan du göra en platshållare IP-adress. Se till att PLATSHÅLLAR-IP-adressen använder ett giltigt format. Senare kan ersätta du PLATSHÅLLAR-IP-adressen med offentliga IP-adressen för den virtuella nätverksgatewayen för Resource Manager.
7. För **Klientadressutrymme**, använda den [värden](#connectoverview) för den virtuella IP-adressens utrymmen för Resource Manager-VNet. Den här inställningen används för att ange adressutrymmen för att dirigera till Resource Manager-nätverk. I det här exemplet använder vi 192.168.0.0/16 adressintervallet för RMVNet.
8. Klicka på **OK** att spara värdena och återgå till den **ny VPN-anslutning** sidan.

### <a name="classicgw"></a>3. Skapa den virtuella nätverksgatewayen

1. På den **ny VPN-anslutning** väljer den **skapa gateway omedelbart** kryssrutan.
2. Klicka på **Valfri gatewaykonfiguration** för att öppna sidan **Gatewaykonfiguration**.

  ![Öppna gateway konfigurationssidan](./media/vpn-gateway-connect-different-deployment-models-portal/optionalgatewayconfiguration.png "öppna gateway konfigurationssidan")
3. Klicka på **undernät - Konfigurera obligatoriska inställningar** att öppna den **Lägg till undernät** sidan. Den **namn** har redan konfigurerats med det obligatoriska värdet: **GatewaySubnet**.
4. Den **adressintervall** refererar till området för gateway-undernätet. Men du kan skapa ett gateway-undernät med ett/29-adressintervall (3 adresser), rekommenderar vi att skapa ett gateway-undernät som innehåller fler IP-adresser. Detta kommer att hantera framtida konfigurationer som kan kräva mer tillgängliga IP-adresser. Använd om möjligt /27 eller/28. Om du använder de här stegen som en övning, ser du den [exempelvärden](#values). Det här exemplet använder vi '10.0.0.32/28'. Klicka på **OK** att skapa gateway-undernätet.
5. På den **gatewaykonfiguration** sidan **storlek** refererar till gateway-SKU. Välj SKU-gatewayen för din VPN-gateway.
6. Kontrollera den **Routningstyp** är **dynamisk**, klicka sedan på **OK** att återgå till den **ny VPN-anslutning** sidan.
7. På den **ny VPN-anslutning** klickar du på **OK** att börja skapa din VPN-gateway. Det kan ta upp till 45 minuter att skapa en VPN-gateway.

### <a name="ip"></a>4. Kopiera den virtuella nätverksgatewayen offentlig IP-adress

När den virtuella nätverksgatewayen har skapats, kan du visa gatewayens IP-adress. 

1. Navigera till ditt klassiska virtuella nätverk och klicka på **översikt**.
2. Klicka på **VPN-anslutningar** att öppna sidan VPN-anslutningar. Du kan visa den offentliga IP-adressen på sidan VPN-anslutningar. Det här är den offentliga IP-adress som tilldelats din virtuella nätverksgateway. Anteckna IP-adressen. Du använder den i senare steg när du arbetar med dina konfigurationsinställningar för Resource Manager lokala gateway. 
3. Du kan visa statusen för din gateway-anslutningar. Lägg märke till den lokala nätverksplatsen som du har skapat visas som ”anslutning”. Status ändras när du har skapat dina anslutningar. Du kan stänga den här sidan när du är klar med att visa status.

## <a name="rmvnet"></a>Avsnitt 2 – konfigurera Resource Manager-VNet-inställningarna

I det här avsnittet skapar du den virtuella nätverksgatewayen och den lokala nätverksgatewayen för Resource Manager-VNet. Skärmbilderna anges som exempel. Se till att ersätta värdena med dina egna eller Använd den [exempel](#values) värden.

### <a name="1-create-a-virtual-network"></a>1. Skapa ett virtuellt nätverk

**Exempelvärden:**

* Namn på virtuellt nätverk = RMVNet <br>
* Adressutrymme = 192.168.0.0/16 <br>
* Resursgrupp = RG1 <br>
* Plats = USA, östra <br>
* Namn på undernät = Subnet-1 <br>
* Adressintervall = 192.168.1.0/24 <br>


Om du inte har ett Resource Manager-VNet och kör de här stegen som en övning kan du skapa ett virtuellt nätverk med stegen i [skapa ett virtuellt nätverk](../virtual-network/quick-create-portal.md), med exempelvärdena som.

### <a name="2-create-a-gateway-subnet"></a>2. Skapa ett gateway-undernät

**Exempelvärde:** GatewaySubnet = 192.168.0.0/26

Innan du skapar en virtuell nätverksgateway måste du först skapa gateway-undernätet. Skapa ett gateway-undernät med CIDR-antal på/28 eller större (/ 27, / 26, etc.). Om du skapar detta som en del av en övning, kan du använda exempelvärdena.

[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="creategw"></a>3. Skapa en virtuell nätverksgateway

**Exempelvärden:**

* Det virtuella nätverkets gateway name = RMGateway <br>
* Gateway-typ = VPN <br>
* VPN-typ = routningsbaserad <br>
* SKU: N = VpnGw1 <br>
* Plats = USA, östra <br>
* Virtuellt nätverk = RMVNet <br>
* Första IP-konfiguration = rmgwpip <br>

[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

### <a name="createlng"></a>4. Skapa en lokal nätverksgateway

**Exempelvärden:** Lokal nätverksgateway = ClassicVNetLocal

| Virtual Network | Adressutrymme | Region | Ansluter till lokal nätverksplats |Gatewayens offentliga IP-adress|
|:--- |:--- |:--- |:--- |:--- |
| ClassicVNet |(10.0.0.0/24) |Västra USA | RMVNetLocal (192.168.0.0/16) |Offentliga IP-adressen som tilldelas till ClassicVNet-gateway|
| RMVNet | (192.168.0.0/16) |Östra USA |ClassicVNetLocal (10.0.0.0/24) |Offentliga IP-adressen som tilldelas RMVNet gatewayen.|

Den lokala nätverksgatewayen anger adressintervallet och offentlig IP-adress som är associerade med ditt klassiska virtuella nätverk och dess virtuella nätverksgateway. Om du genomför de här stegen som en övning, refererar du till exempelvärdena.

[!INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]

## <a name="modifylng"></a>Avsnitt 3 – ändra inställningar för lokal plats av klassiska virtuella nätverk

I det här avsnittet ska ersätta du platshållaren IP-adressen som du använde när du anger inställningar för lokal plats, med Resource Manager VPN-gatewayens IP-adress. Det här avsnittet använder det klassiska (SM) PowerShell-cmdlet.

1. Gå till det klassiska virtuella nätverket i Azure-portalen.
2. På sidan för ditt virtuella nätverk, **översikt**.
3. I den **VPN-anslutningar** klickar du på namnet på den lokala platsen i bilden.

  ![VPN-anslutningar](./media/vpn-gateway-connect-different-deployment-models-portal/vpnconnections.png "VPN-anslutningar")
4. På den **plats-till-plats-VPN-anslutningar** klickar du på namnet på platsen.

  ![Site-name](./media/vpn-gateway-connect-different-deployment-models-portal/sitetosite3.png "namn på lokal plats")
5. På sidan för den lokala platsen klickar du på namnet på den lokala platsen att öppna den **lokal plats** sidan.

  ![Öppna lokal plats](./media/vpn-gateway-connect-different-deployment-models-portal/openlocal.png "öppna lokal plats")
6. På den **lokal plats** sidan och Ersätt den **IP-adress för VPN-gateway** med IP-adressen för Resource Manager-gateway.

  ![Gateway-ip-adress](./media/vpn-gateway-connect-different-deployment-models-portal/gwipaddress.png "Gateway-IP-adress")
7. Klicka på **OK** att uppdatera IP-adressen.

## <a name="RMtoclassic"></a>Avsnitt 4 – skapa Resource Manager till klassiska anslutning

I följande steg ska konfigurera du anslutningen från Resource Manager-VNet till det klassiska virtuella nätverket med Azure portal.

1. I **alla resurser**, leta upp den lokala nätverksgatewayen. I vårt exempel är den lokala nätverksgatewayen **ClassicVNetLocal**.
2. Klicka på **Configuration** och kontrollera att IP-adressvärde är VPN-gateway för det klassiska virtuella nätverket. Uppdatera om det behövs och klicka sedan på **spara**. Stäng sidan.
3. I **alla resurser**, klickar du på den lokala nätverksgatewayen.
4. Klicka på **anslutningar** att öppna sidan anslutningar.
5. På den **anslutningar** klickar du på **+** att lägga till en anslutning.
6. På den **Lägg till anslutning** sidan, namnge anslutningen. Till exempel ”RMtoClassic”.
7. **Plats-till-plats** har redan valts på den här sidan.
8. Välj den virtuella nätverksgatewayen som du vill associera med den här platsen.
9. Skapa en **delad nyckel**. Den här nyckeln används också i den anslutning som du skapar från det klassiska virtuella nätverket till Resource Manager-VNet. Du kan generera nyckeln eller utgör en. I vårt exempel använder vi 'abc123', men du kan (och bör) använda ett mer komplext.
10. Klicka på **OK** att skapa anslutningen.

## <a name="classictoRM"></a>Avsnitt 5 – Skapa klassisk till Resource Manager-anslutning

I följande steg ska konfigurera du anslutningen från det klassiska virtuella nätverket till Resource Manager-VNet. De här stegen kräver PowerShell. Du kan inte skapa den här anslutningen i portalen. Kontrollera att du har hämtat och installerat både klassiska (SM) och Resource Manager (RM) PowerShell-cmdletar.

### <a name="1-connect-to-your-azure-account"></a>1. Anslut till ditt Azure-konto

Öppna PowerShell-konsolen med förhöjd behörighet och logga in på kontot. När du har loggat in hämtas dina kontoinställningar så att de är tillgängliga för Azure PowerShell. Följande cmdlet uppmanar dig inloggningsuppgifterna för ditt Azure-konto för distributionsmodellen i Resource Manager:

```powershell
Connect-AzureRmAccount
```

Hämta en lista över dina Azure-prenumerationer.

```powershell
Get-AzureRmSubscription
```

Om du har mer än en prenumeration kan du ange den prenumeration som du vill använda.

```powershell
Select-AzureRmSubscription -SubscriptionName "Name of subscription"
```

Logga sedan in kan använda det klassiska PowerShell-cmdlet (Service Management). Använd följande kommando för att lägga till ditt Azure-konto för den klassiska distributionsmodellen:

```powershell
Add-AzureAccount
```

Hämta en lista över dina prenumerationer. Det här steget kan vara nödvändigt när du lägger till Service Management-cmdletar, beroende på din Azure-modulen installera.

```powershell
Get-AzureSubscription
```

Om du har mer än en prenumeration kan du ange den prenumeration som du vill använda.

```powershell
Select-AzureSubscription -SubscriptionName "Name of subscription"
```

### <a name="2-view-the-network-configuration-file-values"></a>2. Visa filen konfigurationsvärden nätverk

När du skapar ett virtuellt nätverk i Azure-portalen, visas inte det fullständiga namnet som använder Azure i Azure-portalen. Ett virtuellt nätverk som verkar ha namnet ”ClassicVNet” i Azure-portalen kan till exempel ha en mycket längre namn i nätverkskonfigurationsfilen. Namnet kan se ut ungefär som: 'Group ClassicRG ClassicVNet'. I följande steg ska du hämta nätverkskonfigurationsfilen och visa värdena.

Skapa en katalog på datorn och exportera sedan nätverkskonfigurationsfilen till katalogen. I det här exemplet exporteras nätverkskonfigurationsfilen till C:\AzureNet.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

Öppna filen med en textredigerare och visa namnet på ditt klassiska virtuella nätverk. Använda namnen i nätverkskonfigurationsfilen när du kör PowerShell-cmdletar.

- VNet-namnen visas som **VirtualNetworkSite name =**
- Platsnamn anges som **LocalNetworkSite name =**

### <a name="3-create-the-connection"></a>3. Skapa anslutningen

Ange den delade nyckeln och skapa anslutningen från det klassiska virtuella nätverket till Resource Manager-VNet. Du kan inte ange den delade nyckeln med hjälp av portalen. Kontrollera att du kör dessa steg när du är inloggad i med hjälp av den klassiska versionen av PowerShell-cmdletar. Du gör detta genom att använda **Add-AzureAccount**. I annat fall du kommer inte att kunna ställa in den ”-AzureVNetGatewayKey”.

- I det här exemplet **- VNetName** är namnet på den klassiska VNet som hittades i nätverkskonfigurationsfilen. 
- Den **- LocalNetworkSiteName** är det namn du angav för den lokala platsen som hittades i nätverkskonfigurationsfilen.
- Den **- SharedKey** är ett värde som du genererar och anger. I det här exemplet vi använde *abc123*, men du kan generera ett mer komplext. Viktigt är att värdet som du anger här måste vara samma värde som du angav när du skapar dina Resource Manager till klassiska anslutning.

```powershell
Set-AzureVNetGatewayKey -VNetName "Group ClassicRG ClassicVNet" `
-LocalNetworkSiteName "172B9E16_RMVNetLocal" -SharedKey abc123
```

## <a name="verify"></a>Avsnitt 6 – verifiera dina anslutningar

Du kan verifiera dina anslutningar med hjälp av Azure portal eller PowerShell. Vid verifiering, kan du behöva vänta en minut eller två som anslutningen håller på att skapas. När en anslutning lyckas ändras anslutningen status från ”anslutning” till ”ansluten”.

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>Verifiera anslutningen från klassiska VNet till ditt VNet i Resource Manager

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

### <a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>Verifiera anslutningen från Resource Manager-VNet till ditt klassiska virtuella nätverk

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="faq"></a>Vanliga frågor och svar om VNet-till-VNet

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]
