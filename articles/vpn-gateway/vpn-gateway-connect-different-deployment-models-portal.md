---
title: "Ansluta klassiska virtuella nätverk till Azure Resource Manager VNets: Portal | Microsoft Docs"
description: "Lär dig hur du skapar en VPN-anslutning mellan klassiska Vnet och Resource Manager VNets med hjälp av VPN-Gateway och -portalen"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 5a90498c-4520-4bd3-a833-ad85924ecaf9
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/27/2017
ms.author: cherylmc
ms.openlocfilehash: 8fd058d74d00ecc980d295ee6bd9680ff832f891
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2017
---
# <a name="connect-virtual-networks-from-different-deployment-models-using-the-portal"></a>Ansluta virtuella nätverk från olika distributionsmodeller med hjälp av portalen

Den här artikeln visar hur du ansluter klassiska Vnet till Resource Manager VNets så att de resurser som finns i separata distributionsmodeller för att kommunicera med varandra. Stegen i den här artikeln i första hand använder Azure-portalen, men du kan också skapa den här konfigurationen med hjälp av PowerShell genom att välja artikeln från den här listan.

> [!div class="op_single_selector"]
> * [Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

Ansluta ett klassiskt virtuellt nätverk till ett VNet Resource Manager liknar ansluta ett virtuellt nätverk till en lokal plats. Båda typerna av anslutning använder en VPN-gateway för att få en säker tunnel med IPsec/IKE. Du kan skapa en anslutning mellan Vnet som finns i olika prenumerationer och i olika regioner. Du kan också ansluta Vnet som redan har anslutningar till lokalt nätverk, så länge som de har konfigurerats med gatewayen är dynamisk eller ruttbaserad. Mer information om anslutningar mellan virtuella nätverk finns i [Vanliga frågor om VNet-till-VNet](#faq) i slutet av den här artikeln. 

Om ditt Vnet i samma region, kanske du vill i stället bör överväga att ansluta dem med hjälp av VNet-Peering. Ingen VPN-gateway används för VNet-peering. Mer information finns i [VNet peering (Vnet-peering)](../virtual-network/virtual-network-peering-overview.md). 

### <a name="before"></a>Innan du börjar

* Dessa instruktioner förutsätter att båda Vnet har redan skapats. Om du använder den här artikeln som en övning och inte har Vnet finns länkar i steg som hjälper dig att skapa dem.
* Kontrollera att adressintervall för till Vnet inte överlappar varandra eller överlappar ett intervall för andra anslutningar gateway kan anslutas till.
* Installera de senaste PowerShell-cmdletarna för både Resource Manager och Service Management (klassisk). I den här artikeln använder vi både Azure-portalen och PowerShell. PowerShell krävs för att skapa anslutningen från den klassiska VNet till Resource Manager-VNet. Mer information finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/overview). 

### <a name="values"></a>Exempelinställningar

Du kan använda värdena till att skapa en testmiljö eller hänvisa till dem för att bättre förstå exemplen i den här artikeln.

**Klassiska VNet**

VNet-name = ClassicVNet <br>
Adressutrymmet = 10.0.0.0/24 <br>
Undernätnamnet = undernät 1 <br>
Adressintervall för gatewayundernät = 10.0.0.0/27 <br>
Prenumerationen = den prenumeration som du vill använda <br>
Resursgruppens namn = ClassicRG <br>
Plats = västra USA <br>
GatewaySubnet = 10.0.0.32/28 <br>
Lokal plats = RMVNetLocal <br>

**Hanteraren för virtuella nätverk**

VNet-name = RMVNet <br>
Adressutrymmet = 192.168.0.0/16 <br>
Resursgruppens namn = RG1 <br>
Plats = östra USA <br>
Undernätnamnet = undernät 1 <br>
Adressområde = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Gateway för virtuella nätverksnamnet = RMGateway <br>
Gateway-typ = VPN <br>
VPN-typ = ruttbaserad <br>
SKU = VpnGw1 <br>
Plats = östra USA <br>
Virtuellt nätverk = RMVNet <br> (associera VPN-gatewayen till detta virtuella nätverk) Den första IP-konfigurationen = rmgwpip <br> (gateway offentlig IP-adress) Lokal nätverksgateway = ClassicVNetLocal <br>
Anslutningens namn = RMtoClassic

### <a name="connectoverview"></a>Översikt över anslutning

I denna konfiguration kan skapa du en VPN-anslutning för gateway via en IPsec/IKE VPN-tunnel mellan virtuella nätverk. Kontrollera att ingen av VNet-intervall överlappar varandra eller med någon av de lokala nätverk som de ansluter till.

I följande tabell visas ett exempel på hur exempel Vnet och lokala platser definieras:

| Virtual Network | Adressutrymme | Region | Ansluter till lokal nätverksplats |
|:--- |:--- |:--- |:--- |
| ClassicVNet |(10.0.0.0/24) |Västra USA | RMVNetLocal (192.168.0.0/16) |
| RMVNet | (192.168.0.0/16) |Östra USA |ClassicVNetLocal (10.0.0.0/24) |

## <a name="classicvnet"></a>Avsnittet 1 – konfigurera klassiska VNet-inställningarna

I det här avsnittet skapar du det klassiska VNet, lokalt nätverk (lokal plats) och den virtuella nätverksgatewayen. Skärmbilderna anges som exempel. Se till att ersätta värdena med din egen eller använda den [exempel](#values) värden.

### 1. <a name="classicvnet"></a>Skapa ett klassiskt virtuellt nätverk

Om du inte har ett klassiskt virtuellt nätverk och kör de här stegen som Övning, kan du skapa ett VNet med hjälp av [i den här artikeln](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) och [exempel](#values) värden från ovan.

Om du redan har ett VNet med en VPN-gateway, måste du kontrollera att gatewayen är dynamiska. Om den är statisk, måste du först radera VPN-gateway innan du går vidare till [konfigurera den lokala platsen](#local).

1. Öppna den [Azure-portalen](https://ms.portal.azure.com) och logga in med ditt Azure-konto.
2. Klicka på **+ skapa en resurs** att öppna sidan 'New'.
3. Skriv ”virtuella nätverk” i fältet 'Search marketplace'. Om du i stället väljer nätverk -> virtuella nätverk, får du inte alternativet för att skapa ett klassiskt virtuellt nätverk.
4. Leta upp ”virtuella nätverk” returnerade listan och klicka om du vill öppna sidan virtuellt nätverk. 
5. På sidan virtuellt nätverk väljer du klassisk om du vill skapa ett klassiskt virtuellt nätverk. Om du tar standard här ska du avveckla med en Resource Manager-VNet i stället.

### 2. <a name="local"></a>Konfigurera den lokala platsen

1. Gå till **alla resurser** och leta upp den **ClassicVNet** i listan.
2. På den **översikt** sidan den **VPN-anslutningar** klickar du på **Gateway** att skapa en gateway.
  ![Konfigurera en VPN-gateway](./media/vpn-gateway-connect-different-deployment-models-portal/gatewaygraphic.png "konfigurera en VPN-gateway")
3. På den **ny VPN-anslutning** sidan för **anslutningstypen**väljer **plats-till-plats**.
4. För **lokal plats**, klickar du på **konfigurera nödvändiga inställningar**. Då öppnas den **lokal plats** sidan.
5. På den **lokal plats** sida, skapar du ett namn som refererar till Resource Manager-VNet. Till exempel 'RMVNetLocal'.
6. Om VPN-gateway för Resource Manager-VNet har redan en offentlig IP-adress, använder du värdet för den **IP-adressen för VPN-gateway** fältet. Om du utföra följande steg som Övning, eller ännu inte har en virtuell nätverksgateway för Resource Manager-VNet, kan du konfigurera en platshållare för IP-adress. Kontrollera att IP-adressen platshållare använder ett ogiltigt format. Senare kan ersätta du platshållare IP-adress med offentliga IP-adressen för den virtuella nätverksgatewayen för hanteraren för filserverresurser.
7. För **klientens adressutrymme**, använda den [värden](#connectoverview) för den virtuella IP-adressens utrymmen för Resource Manager-VNet. Den här inställningen används för att ange adressutrymmen till det virtuella nätverket Resource Manager. I det här exemplet kan använda vi 192.168.0.0/16 adressintervallet för RMVNet.
8. Klicka på **OK** att spara värdena och återgå till den **ny VPN-anslutning** sidan.

### <a name="classicgw"></a>3. Skapa den virtuella nätverksgatewayen

1. På den **ny VPN-anslutning** väljer den **skapa gateway omedelbart** kryssrutan.
2. Klicka på **Valfri gatewaykonfiguration** för att öppna sidan **Gatewaykonfiguration**.

  ![Öppna gateway konfigurationssidan](./media/vpn-gateway-connect-different-deployment-models-portal/optionalgatewayconfiguration.png "öppna gateway konfigurationssidan")
3. Klicka på **undernät - konfigurera nödvändiga inställningar** att öppna den **Lägg till undernät** sidan. Den **namn** redan har konfigurerats med det obligatoriska värdet: **GatewaySubnet**.
4. Den **adressintervall** refererar till området för gateway-undernätet. Även om du kan skapa en gateway-undernät med en /29 adressintervall (3 adresser), rekommenderar vi att skapa en gateway-undernät som innehåller flera IP-adresser. Detta kommer att underlätta framtida konfigurationer som kan kräva mer tillgängliga IP-adresser. Använd om möjligt minst/27 eller /28. Om du använder de här stegen som Övning, du kan referera till den [exempelvärden](#values). Det här exemplet använder vi '10.0.0.32/28'. Klicka på **OK** att skapa gateway-undernätet.
5. På den **gatewaykonfigurationen** sidan **storlek** refererar till gateway-SKU. Välj en gateway-SKU för VPN-gateway.
6. Kontrollera den **routning typen** är **dynamiska**, klicka på **OK** att återgå till den **ny VPN-anslutning** sidan.
7. På den **ny VPN-anslutning** klickar du på **OK** att börja skapa din VPN-gateway. Skapa en VPN-gateway kan ta upp till 45 minuter att slutföra.

### <a name="ip"></a>4. Kopiera den virtuella nätverksgatewayen offentliga IP-adress

När den virtuella nätverksgatewayen har skapats kan visa du IP-adressen för gateway. 

1. Navigera till ditt klassiska VNet och på **översikt**.
2. Klicka på **VPN-anslutningar** att öppna sidan med VPN-anslutningar. På sidan med VPN-anslutningar kan du visa den offentliga IP-adressen. Det här är den offentliga IP-adress som tilldelats till din virtuella nätverksgateway. Anteckna IP-adressen. Du använder den i senare steg när du arbetar med Resource Manager lokala nätverket gateway konfigurationsinställningar. 
3. Du kan visa statusen för gateway-anslutningar. Lägg märke till den lokala nätverksplatsen som du skapade anges som 'Ansluta'. Statusen ändras när du har skapat dina anslutningar. Du kan stänga den här sidan när du är klar med att visa status.

## <a name="rmvnet"></a>Avsnitt 2 – konfigurera Hanteraren för filserverresurser VNet-inställningarna

I det här avsnittet skapar du den virtuella nätverksgatewayen och den lokala nätverksgatewayen för Resource Manager-VNet. Skärmbilderna anges som exempel. Se till att ersätta värdena med din egen eller använda den [exempel](#values) värden.

### <a name="1-create-a-virtual-network"></a>1. Skapa ett virtuellt nätverk

**Exempelvärden:**

* VNet-name = RMVNet <br>
* Adressutrymmet = 192.168.0.0/16 <br>
* Resursgruppens namn = RG1 <br>
* Plats = östra USA <br>
* Undernätnamnet = undernät 1 <br>
* Adressområde = 192.168.1.0/24 <br>


Om du inte har ett VNet Resource Manager och kör de här stegen som Övning, kan du skapa ett VNet med hjälp av [i den här artikeln](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) och exempelvärden.

### <a name="2-create-a-gateway-subnet"></a>2. Skapa ett gateway-undernät

**Exempel:** GatewaySubnet = 192.168.0.0/26

Innan du skapar en virtuell nätverksgateway, behöver du först skapa gateway-undernätet. Skapa en gateway-undernät med CIDR-antal för /28 eller större (/ 27/26, etc.). Om du skapar detta som en del av en uppgift kan använda du exempelvärden.

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

### <a name="creategw"></a>3. Skapa en virtuell nätverksgateway

**Exempelvärden:**

* Gateway för virtuella nätverksnamnet = RMGateway <br>
* Gateway-typ = VPN <br>
* VPN-typ = ruttbaserad <br>
* SKU = VpnGw1 <br>
* Plats = östra USA <br>
* Virtuellt nätverk = RMVNet <br>
* Den första IP-konfigurationen = rmgwpip <br>

[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

### <a name="createlng"></a>4. Skapa en lokal nätverksgateway

**Exempelvärden:** lokal nätverksgateway = ClassicVNetLocal

| Virtual Network | Adressutrymme | Region | Ansluter till lokal nätverksplats |Offentliga IP-adressen för gateway|
|:--- |:--- |:--- |:--- |:--- |
| ClassicVNet |(10.0.0.0/24) |Västra USA | RMVNetLocal (192.168.0.0/16) |Offentlig IP-adress som är tilldelad till ClassicVNet-gateway|
| RMVNet | (192.168.0.0/16) |Östra USA |ClassicVNetLocal (10.0.0.0/24) |Offentliga IP-adressen som är tilldelad till RMVNet gateway.|

Lokal nätverksgateway anger adressintervallet och offentliga IP-adressen som är kopplad till ditt klassiska VNet och dess virtuella nätverks-gatewayen. Om du gör dessa steg som Övning referera till exempel värdena.

[!INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]

## <a name="modifylng"></a>Avsnitt 3 - ändra inställningarna för klassiska VNet-lokala platsen

I det här avsnittet kan du ersätta platshållaren IP-adressen som du använde när du anger inställningar för lokal plats med Resource Manager VPN gateway IP-adress. Det här avsnittet använder det klassiska (SM) PowerShell-cmdlet.

1. Navigera till det klassiska virtuella nätverket i Azure-portalen.
2. På sidan för det virtuella nätverket **översikt**.
3. I den **VPN-anslutningar** klickar du på namnet på den lokala platsen i bilden.

    ![VPN-anslutningar](./media/vpn-gateway-connect-different-deployment-models-portal/vpnconnections.png "VPN-anslutningar")
4. På den **plats-till-plats VPN-anslutningar** klickar du på namnet på platsen.

    ![Site-name](./media/vpn-gateway-connect-different-deployment-models-portal/sitetosite3.png "lokala platsnamn")
5. På sidan för den lokala platsen klickar du på namnet på den lokala platsen att öppna den **lokal plats** sidan.

    ![Öppna lokal plats](./media/vpn-gateway-connect-different-deployment-models-portal/openlocal.png "öppna lokala platsen")
6. På den **lokal plats** sidan ersätter den **IP-adressen för VPN-gateway** med IP-adressen för Resource Manager-gateway.

    ![Gateway-ip-adress](./media/vpn-gateway-connect-different-deployment-models-portal/gwipaddress.png "Gateway IP-adress")
7. Klicka på **OK** att uppdatera IP-adressen.

## <a name="RMtoclassic"></a>Avsnittet 4 – skapa Resource Manager till klassiska anslutning

I följande steg ska konfigurera du anslutningen från hanteraren för filserverresurser VNet till klassiska VNet med Azure-portalen.

1. I **alla resurser**, leta upp den lokala nätverksgatewayen. I vårt exempel är den lokala nätverksgatewayen **ClassicVNetLocal**.
2. Klicka på **Configuration** och kontrollera att värdet för IP-adress är VPN-gateway för det klassiska VNet. Uppdatera vid behov och klicka sedan på **spara**. Stänga sidan.
3. I **alla resurser**, klickar du på den lokala nätverksgatewayen.
4. Klicka på **anslutningar** att öppna sidan anslutningar.
5. På den **anslutningar** klickar du på  **+**  lägga till en anslutning.
6. På den **Lägg till anslutning** sidan, namnge anslutningen. Till exempel 'RMtoClassic'.
7. **Plats-till-plats** har redan valts på den här sidan.
8. Välj den virtuella nätverksgatewayen som du vill associera med den här platsen.
9. Skapa en **delad nyckel**. Den här nyckeln används också i anslutningen som du skapar från den klassiska VNet till Resource Manager-VNet. Du kan skapa nycklar eller utgör en. Vi använder 'abc123' i vårt exempel, men du kan (och bör) använder något mer komplicerad.
10. Klicka på **OK** att skapa anslutningen.

##<a name="classictoRM"></a>Avsnittet 5 – Skapa klassisk till Resource Manager-anslutning

I följande steg ska konfigurera du anslutningen från den klassiska VNet till Resource Manager-VNet. Dessa steg kräver PowerShell. Du kan inte skapa den här anslutningen i portalen. Kontrollera att du har hämtat och installerat både klassiska (SM) och Resource Manager (RM) PowerShell-cmdlets.

### <a name="1-connect-to-your-azure-account"></a>1. Anslut till ditt Azure-konto

Öppna PowerShell-konsol med utökade behörigheter och logga in på ditt Azure-konto. Följande cmdlet efterfrågar autentiseringsuppgifter för inloggning för ditt Azure-konto. När du loggar in laddas inställningarna för ditt konto så att de blir tillgängliga för Azure PowerShell.

```powershell
Login-AzureRmAccount
```
   
Hämta en lista över dina Azure-prenumerationer om du har mer än en prenumeration.

```powershell
Get-AzureRmSubscription
```

Ange den prenumeration som du vill använda. 

```powershell
Select-AzureRmSubscription -SubscriptionName "Name of subscription"
```

Lägg till ditt Azure-konto om du vill använda det klassiska PowerShell-cmdlet (SM). Om du vill göra det måste använda du följande kommando:

```powershell
Add-AzureAccount
```

### <a name="2-view-the-network-configuration-file-values"></a>2. Visa filen konfigurationsvärden nätverk

När du skapar ett VNet i Azure portal visas inte det fullständiga namnet som använder Azure på Azure-portalen. Ett VNet som verkar vara med namnet 'ClassicVNet' i Azure-portalen kan ha en mycket längre namn i konfigurationsfilen på nätverket. Namnet kan se ut ungefär så: 'Grupp ClassicRG ClassicVNet'. I följande steg ska hämta konfigurationsfilen nätverk och visa värden.

Skapa en katalog på datorn och exportera sedan nätverkskonfigurationsfilen till katalogen. I det här exemplet exporteras nätverkskonfigurationsfilen till C:\AzureNet.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

Öppna filen i en textredigerare och visa namnet för din klassiska VNet. Använda namnen i konfigurationsfilen på nätverket när du kör PowerShell-cmdlets.

- VNet namn visas som **VirtualNetworkSite namn =**
- Platsnamn listas som **LocalNetworkSite namn =**

### <a name="3-create-the-connection"></a>3. Skapa anslutningen

Ange den delade nyckeln och skapa anslutningen från den klassiska VNet till Resource Manager-VNet. Du kan inte ange den delade nyckeln med hjälp av portalen. Kontrollera att du kör dessa steg när du är inloggad i den klassiska versionen av PowerShell-cmdlets. Det gör du genom att använda **Add-AzureAccount**. Annars kan du kommer inte att ange den '-AzureVNetGatewayKey'.

- I det här exemplet **- VNetName** är namnet på det klassiska virtuella nätverket som hittades i konfigurationsfilen nätverk. 
- Den **- LocalNetworkSiteName** är det namn du angav för den lokala platsen som hittades i konfigurationsfilen nätverk.
- Den **- SharedKey** är ett värde som du skapar och ange. I det här exemplet används vi *abc123*, men du kan skapa något mer komplicerad. Viktigt är att värdet som du anger här måste ha samma värde som du angav när du skapar din Resource Manager till klassiska anslutning.

```powershell
Set-AzureVNetGatewayKey -VNetName "Group ClassicRG ClassicVNet" `
-LocalNetworkSiteName "172B9E16_RMVNetLocal" -SharedKey abc123
```

##<a name="verify"></a>Avsnitt 6 – Kontrollera anslutningarna

Du kan verifiera dina anslutningar med hjälp av Azure-portalen eller PowerShell. När du verifierar, du kan behöva vänta en minut eller två som anslutningen skapas. När en anslutning upprättas ändras anslutningen tillståndet från 'Ansluta' till 'Ansluten'.

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>Kontrollera anslutningen från ditt klassiska VNet till Resource Manager-VNet

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

###<a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>Kontrollera anslutningen från Resource Manager-VNet till ditt klassiska VNet

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="faq"></a>Vanliga frågor och svar om VNet-till-VNet

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]
