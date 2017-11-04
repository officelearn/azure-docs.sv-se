---
title: 'Skapa en anslutning mellan Vnet: klassiska: Azure-portalen | Microsoft Docs'
description: "Så här ansluter du tillsammans med PowerShell och den klassiska Azure-portalen virtuella Azure-nätverk."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/02/2017
ms.author: cherylmc
ms.openlocfilehash: 77097d59077cd8e199acdb5dc0d8427369565eea
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="configure-a-vnet-to-vnet-connection-classic"></a>Konfigurera VNet-till-VNet-anslutning (klassisk)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

Den här artikeln visar hur du skapar en VPN-gatewayanslutning mellan virtuella nätverk. De virtuella nätverken kan finnas i samma eller olika regioner och i samma eller olika prenumerationer. Stegen i den här artikeln gäller för den klassiska distributionsmodellen och Azure portal. Du kan också skapa den här konfigurationen med ett annat distributionsverktyg eller en annan distributionsmodell genom att välja ett annat alternativ i listan nedan:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Azure CLI](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Azure Portal (klassisk)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Ansluta olika distributionsmodeller – Azure Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Ansluta olika distributionsmodeller – PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

![VNet till VNet anslutning Diagram](./media/vpn-gateway-howto-vnet-vnet-portal-classic/v2vclassic.png)

## <a name="about-vnet-to-vnet-connections"></a>Om VNet-till-VNet-anslutningar

Ansluta ett virtuellt nätverk till ett annat virtuellt nätverk (VNet-till-VNet) i den klassiska distributionsmodellen med hjälp av en VPN-gateway liknar ansluta ett virtuellt nätverk till en lokal plats. Båda typerna av anslutning använder en VPN-gateway för att få en säker tunnel med IPsec/IKE.

Vnet som du ansluter kan finnas i olika prenumerationer och olika regioner. Du kan kombinera VNet till VNet-kommunikation med flera platser konfigurationer. Det innebär att du kan etablera nätverkstopologier som kombinerar anslutningen för flera platser med en intern virtuell nätverksanslutning.

![VNet till VNet-anslutningar](./media/vpn-gateway-howto-vnet-vnet-portal-classic/aboutconnections.png)

### <a name="why"></a>Varför ska man ansluta virtuella nätverk?

Du kan vilja ansluta virtuella nätverk av följande skäl:

* **Geografisk redundans i flera regioner och geografisk närvaro**

  * Du kan ange din egna geografiska replikering eller synkronisering med en säker anslutning, utan att passera några Internet-slutpunkter.
  * Med Azure belastningsutjämnare och Microsoft eller tredje parts klustring teknik, kan du ställa in högtillgänglig arbetsbelastning med geo-redundans över flera Azure-regioner. Ett viktigt exempel är att konfigurera att SQL alltid är aktiverat med tillgänglighetsgrupper som är spridda över flera Azure-regioner.
* **Regional program på flera nivåer med stark isoleringsgräns**

  * I samma region, kan du konfigurera flera nivåer program med flera VNets anslutna tillsammans med stark isolering och kommunikationen mellan nivå.
* **Mellan kommunikation mellan organisation i Azure-prenumeration**

  * Om du har flera Azure-prenumerationer, kan du ansluta arbetsbelastningar från olika prenumerationer tillsammans på ett säkert sätt mellan virtuella nätverk.
  * Du kan aktivera kommunikation mellan organisation med säkra VPN-teknik i Azure för företag och leverantörer.

Mer information om VNet-till-VNet-anslutningar finns i [Att tänka på när du använder VNet-till-VNet](#faq) i slutet av den här artikeln.

### <a name="before-you-begin"></a>Innan du börjar

Innan du påbörjar den här övningen, hämta och installera den senaste versionen av Azure Service Management (SM) PowerShell-cmdlets. Mer information finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/overview). Vi använder portalen för de flesta av steg, men du måste skapa anslutningar mellan till Vnet med hjälp av PowerShell. Du kan inte skapa anslutningar med Azure-portalen.

## <a name="plan"></a>Steg 1 – Planera dina IP-adressintervall

Det är viktigt att bestämma intervall som du använder för att konfigurera de virtuella nätverken. För den här konfigurationen måste du se till att ingen av VNet-intervall överlappar varandra eller med någon av de lokala nätverk som de ansluter till.

Följande tabell visar ett exempel på hur du definierar ditt Vnet. Använder du områdena som en riktlinje endast. Skriv ned intervallen för dina virtuella nätverk. Du behöver den här informationen för senare steg.

**Exempel**

| Virtual Network | Adressutrymmet | Region | Ansluter till lokal nätverksplats |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |Östra USA |VNet4Local<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |Västra USA |VNet1Local<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

## <a name="vnetvalues"></a>Steg 2 – Skapa virtuella nätverk

Skapa två virtuella nätverk i den [Azure-portalen](https://portal.azure.com). Anvisningar om hur du skapar klassiska virtuella nätverk finns [skapa ett klassiskt virtuellt nätverk](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). 

När du använder portalen för att skapa ett klassiskt virtuellt nätverk, ska du gå till bladet för virtuella nätverk med hjälp av följande steg, annars visas inte alternativet för att skapa ett klassiskt virtuellt nätverk:

1. Klicka på ”+” att öppna 'New-bladet.
2. Skriv ”virtuella nätverk” i fältet 'Search marketplace'. Om du i stället väljer nätverk -> virtuella nätverk, får du inte alternativet för att skapa ett klassiskt virtuellt nätverk.
3. Leta ”virtuella nätverk” i returnerade listan och klicka på den för att öppna bladet virtuellt nätverk. 
4. Välj klassisk om du vill skapa ett klassiskt virtuellt nätverk på virtuellt nätverk-bladet. 

Om du använder den här artikeln som Övning använder du följande exempelvärden:

**Värden för TestVNet1**

Namn: TestVNet1<br>
Adressutrymmet: 10.11.0.0/16, 10.12.0.0/16 (valfritt)<br>
Undernätnamnet: standard<br>
Adressintervallet i undernätet: 10.11.0.1/24<br>
Resursgrupp: ClassicRG<br>
Plats: Östra USA<br>
GatewaySubnet: 10.11.1.0/27

**Värden för TestVNet4**

Namn: TestVNet4<br>
Adressutrymmet: 10.41.0.0/16, 10.42.0.0/16 (valfritt)<br>
Undernätnamnet: standard<br>
Adressintervallet i undernätet: 10.41.0.1/24<br>
Resursgrupp: ClassicRG<br>
Plats: Västra USA<br>
GatewaySubnet: 10.41.1.0/27

**När du skapar ditt Vnet, Tänk på följande inställningar:**

* **Virtuella nätverk adressutrymmen** – på sidan virtuella nätverk adressutrymmen ange adressintervallet som du vill använda för det virtuella nätverket. Detta är dynamiska IP-adresser som ska tilldelas virtuella datorer och andra rollinstanser som du distribuerar till det här virtuella nätverket.<br>Adressutrymmen som du väljer kan inte överlappa adressutrymmen för alla andra Vnet eller lokala platser som detta virtuella nätverk ska ansluta till.

* **Plats** – när du skapar ett virtuellt nätverk, kopplar du det till en Azure-plats (region). Om du vill att dina virtuella datorer som distribueras till det virtuella nätverket ska placeras fysiskt i USA, västra markerar du den platsen. Du kan inte ändra plats som associeras med det virtuella nätverket när du har skapat.

**När du har skapat ditt Vnet, kan du lägga till följande inställningar:**

* **Adressutrymmet** – ytterligare adressutrymme krävs inte för den här konfigurationen, men du kan lägga till ytterligare adressutrymme när du har skapat ditt VNet.

* **Undernät** – undernät krävs inte för den här konfigurationen, men du kanske vill ha dina virtuella datorer i ett undernät som skiljer sig från andra rollinstanser.

* **DNS-servrar** – ange DNS-servernamn och IP-adress. Du skapar inte en DNS-server med den här inställningen. Den låter dig ange vilken DNS-server du vill använda för namnmatchning för det här virtuella nätverket.

I det här avsnittet Konfigurera anslutningstypen den lokala platsen och skapa gatewayen.

## <a name="localsite"></a>Steg 3 – Konfigurera den lokala platsen

Azure använder inställningarna i varje lokal nätverksplats för att bestämma hur ska vidarebefordra trafik mellan till Vnet. Varje virtuellt nätverk måste peka på respektive lokala nätverket som du vill dirigera trafik till. Du ange ett namn som du vill använda för att referera till varje lokal nätverksplats. Det är bäst att använda en beskrivande text.

Till exempel ansluter TestVNet1 till en lokal nätverksplats som du skapar med namnet 'VNet4Local'. Inställningarna för VNet4Local innehålla adressprefixen för TestVNet4.

Den lokala platsen för varje virtuellt nätverk är det andra VNet. Följande Exempelvärden används för vår konfiguration:

| Virtual Network | Adressutrymmet | Region | Ansluter till lokal nätverksplats |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |Östra USA |VNet4Local<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |Västra USA |VNet1Local<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

1. Leta upp TestVNet1 i Azure-portalen. I den **VPN-anslutningar** avsnitt i bladet, klickar du på **Gateway**.

    ![Ingen gateway](./media/vpn-gateway-howto-vnet-vnet-portal-classic/nogateway.png)
2. På den **ny VPN-anslutning** väljer **plats-till-plats**.
3. Klicka på **lokal plats** att öppna sidan lokal plats och konfigurera inställningarna.
4. På den **lokal plats** sidan, namnge den lokala platsen. I vårt exempel namn vi för den lokala platsen 'VNet4Local'.
5. För **IP-adressen för VPN-gateway**, du kan använda alla IP-adresser som du vill använda, så länge som den är i ett giltigt format. Normalt använder du den faktiska externa IP-adressen för en VPN-enhet. Men för en klassiska VNet-till-VNet-konfiguration kan du använda offentliga IP-adressen som är tilldelad till gateway för din VNet. Med tanke på att du har ännu inte har skapat den virtuella nätverksgatewayen, kan du ange en giltig offentlig IP-adress som platshållare.<br>Lämna inte detta tomt - det är inte valfria för den här konfigurationen. I ett senare steg, gå tillbaka till de här inställningarna och konfigurera dem med motsvarande virtuella nätverkets gateway IP-adresser när Azure genererar den.
6. För **klientens adressutrymme**, använda adressutrymmet för det andra VNet. Referera till ditt planering exempel. Klicka på **OK** att spara dina inställningar och gå tillbaka till den **ny VPN-anslutning** bladet.

    ![lokala platsen](./media/vpn-gateway-howto-vnet-vnet-portal-classic/localsite.png)

## <a name="gw"></a>Steg 4 – skapa den virtuella nätverksgatewayen

Varje virtuellt nätverk måste ha en virtuell nätverksgateway. Den virtuella nätverksgatewayen vägar och krypterar trafik.

1. Markera kryssrutan **Skapa gateway omedelbart** på bladet **Ny VPN-anslutning**.
2. Klicka på **undernät, storlek och routning**. På den **gatewaykonfigurationen** bladet, klickar du på **undernät**.
3. Namnet på gateway-undernätet fylls i automatiskt med det obligatoriska namnet 'GatewaySubnet'. Den **adressintervall** innehåller IP-adresser som är allokerade till VPN-gateway-tjänster. Vissa konfigurationer tillåta /29 ett gateway-undernät, men det är bäst att använda en /28 eller minst/27 för framtida konfigurationer som kan kräva flera IP-adresser för gateway-tjänster. Vi använder 10.11.1.0/27 i vårt exempel inställningarna. Justera adressutrymmet och klicka sedan på **OK**.
4. Konfigurera den **Gateway storlek**. Den här inställningen refererar till den [Gateway-SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku).
5. Konfigurera den **Routningstyp**. Routning skriver för den här konfigurationen måste vara **dynamiska**. Du kan inte ändra typen routning senare om du inte går sönder ned gatewayen och skapa en ny.
6. Klicka på **OK**.
7. På den **ny VPN-anslutning** bladet, klickar du på **OK** du vill skapa den virtuella nätverksgatewayen. Att skapa en gateway kan ofta ta 45 minuter eller mer, beroende på vald gateway-SKU.

## <a name="vnet4settings"></a>Steg 5 – konfigurera TestVNet4 inställningar

Upprepa stegen för att [skapa en lokal plats](#localsite) och [skapa den virtuella nätverksgatewayen](#gw) att konfigurera TestVNet4, ersätter värden vid behov. Om du gör detta som en övning, använder du den [exempelvärden](#vnetvalues).

## <a name="updatelocal"></a>Steg 6 – uppdatera lokala platser

När virtuella nätverks-gateway har skapats för båda Vnet, måste du justera de lokala webbplatserna **IP-adressen för VPN-gateway** värden.

|Namn på virtuella nätverk|Anslutna platsen|IP-adressen för gateway|
|:--- |:--- |:--- |
|TestVNet1|VNet4Local|VPN-gateway IP-adress för TestVNet4|
|TestVNet4|VNet1Local|VPN-gateway IP-adress för TestVNet1|

### <a name="part-1---get-the-virtual-network-gateway-public-ip-address"></a>Del 1 - Get virtuella nätverkets gateway offentliga IP-adress

1. Leta upp ditt virtuella nätverk i Azure-portalen.
2. Klicka för att öppna VNet **översikt** bladet. På bladet för i **VPN-anslutningar**, du kan visa IP-adressen för din virtuella nätverksgateway.

  ![Offentlig IP-adress](./media/vpn-gateway-howto-vnet-vnet-portal-classic/publicIP.png)
3. Kopiera den IP-adressen. Du kan använda den i nästa avsnitt.
4. Upprepa dessa steg för TestVNet4

### <a name="part-2---modify-the-local-sites"></a>Del 2 – ändra lokala platser

1. Leta upp ditt virtuella nätverk i Azure-portalen.
2. På VNet **översikt** bladet, klickar du på den lokala platsen.

  ![Lokal plats som har skapats](./media/vpn-gateway-howto-vnet-vnet-portal-classic/local.png)
3. På den **plats-till-plats VPN-anslutningar** bladet, klickar du på namnet på den lokala platsen som du vill ändra.

  ![Öppna lokal plats](./media/vpn-gateway-howto-vnet-vnet-portal-classic/openlocal.png)
4. Klicka på den **lokal plats** som du vill ändra.

  ![Ändra plats](./media/vpn-gateway-howto-vnet-vnet-portal-classic/connections.png)
5. Uppdatering av **IP-adressen för VPN-gateway** och på **OK** spara inställningarna.

  ![gateway-IP](./media/vpn-gateway-howto-vnet-vnet-portal-classic/gwupdate.png)
6. Stäng de andra blad.
7. Upprepa dessa steg för TestVNet4.

## <a name="getvalues"></a>Steg 7 – hämta värden från konfigurationsfilen nätverk

När du skapar klassiska Vnet i Azure-portalen är inte det namn som du vill visa det fullständiga namnet som du använder för PowerShell. Till exempel ett VNet som verkar ha namnet **TestVNet1** i portalen kan ha ett mycket längre namn i konfigurationsfilen på nätverket. Namnet kan se ut ungefär så: **grupp ClassicRG TestVNet1**. När du skapar dina anslutningar, är det viktigt att använda de värden som du ser i konfigurationsfilen på nätverket.

I följande steg ska du ansluta till ditt Azure-konto och hämta och visa konfigurationsfilen nätverket för att hämta värden som krävs för anslutningar.

1. Hämta och installera den senaste versionen av Azure Service Management (SM) PowerShell-cmdlets. Mer information finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/overview).

2. Öppna PowerShell-konsolen med utökade rättigheter och anslut till ditt konto. Använd följande exempel för att ansluta:

  ```powershell
  Login-AzureRmAccount
  ```

  Kontrollera prenumerationerna för kontot.

  ```powershell
  Get-AzureRmSubscription
  ```

  Om du har mer än en prenumeration väljer du den du vill använda.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
  ```

  Därefter kan du använda följande cmdlet för att lägga till din Azure-prenumeration i PowerShell för den klassiska distributionsmodellen.

  ```powershell
  Add-AzureAccount
  ```
3. Exportera och visa konfigurationsfilen nätverk. Skapa en katalog på datorn och exportera sedan nätverkskonfigurationsfilen till katalogen. I det här exemplet är nätverket konfigurationsfilen exporteras till **C:\AzureNet**.

  ```powershell
  Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
  ```
4. Öppna filen i en textredigerare och visa namn för din Vnet och platser. Dessa ska vara det namn som du använder när du skapar dina anslutningar.<br>VNet namn visas som **VirtualNetworkSite namn =**<br>Platsnamn listas som **LocalNetworkSiteRef namn =**

## <a name="createconnections"></a>Steg 8 – skapa VPN-gateway-anslutningar

Du kan ange IPsec/IKE i förväg delade nycklar och skapa anslutningen när alla föregående steg har slutförts. Den här uppsättningen anvisningar använder PowerShell. VNet-till-VNet-anslutningar för den klassiska distributionsmodellen kan inte konfigureras i Azure-portalen.

Observera att den delade nyckeln är exakt samma i exempel. Den delade nyckeln måste alltid matcha. Se till att ersätta värdena i de här exemplen med de exakta namn för din Vnet och lokala nätverksplatser.

1. Skapa TestVNet1-till-TestVNet4-anslutningen.

  ```powershell
  Set-AzureVNetGatewayKey -VNetName 'Group ClassicRG TestVNet1' `
  -LocalNetworkSiteName '17BE5E2C_VNet4Local' -SharedKey A1b2C3D4
  ```
2. Skapa TestVNet4-till-TestVNet1-anslutningen.

  ```powershell
  Set-AzureVNetGatewayKey -VNetName 'Group ClassicRG TestVNet4' `
  -LocalNetworkSiteName 'F7F7BFC7_VNet1Local' -SharedKey A1b2C3D4
  ```
3. Vänta tills anslutningar initieras. När gatewayen har initierats är Status ”lyckades”.

  ```
  Error          :
  HttpStatusCode : OK
  Id             :
  Status         : Successful
  RequestId      :
  StatusCode     : OK
  ```

## <a name="faq"></a>VNet-till-VNet-överväganden för klassiska Vnet
* Virtuella nätverk kan finnas i samma eller olika prenumerationer.
* De virtuella nätverken kan finnas i samma eller olika Azure-regioner (platser).
* En molntjänst eller en slutpunkt för belastningsutjämning kan inte omfatta flera virtuella nätverk, även om de är sammankopplade.
* Ansluta flera virtuella nätverk till varandra kräver inte någon VPN-enheter.
* VNet-till-VNet stöder anslutande virtuella Azure-nätverk. Det stöder inte ansluta virtuella datorer eller molntjänster som inte har distribuerats till ett virtuellt nätverk.
* VNet-till-VNet kräver dynamiska routnings-gatewayer. Azure statisk routning gateways stöds inte.
* Virtuell nätverksanslutning kan användas samtidigt med VPN på flera platser. Det finns högst 10 VPN-tunnlar för en VPN-gateway för virtuellt nätverk som ansluter till andra virtuella nätverk eller lokala platser.
* Adressutrymmen till virtuella nätverk och lokala nätverksplatser får inte överlappa varandra. Överlappande adressutrymmen kommer att skapa virtuella nätverk eller överför netcfg konfigurationsfiler som misslyckas.
* Redundanta tunnlar mellan ett virtuellt nätverkspar stöds inte.
* VPN-tunnlar för VNet, inklusive P2S VPN kan dela den tillgängliga bandbredden för VPN-gateway och samma VPN gateway drifttid SLA i Azure.
* VNet-till-VNet-trafik skickas via Azure basen.

## <a name="next-steps"></a>Nästa steg
Verifiera dina anslutningar. Se [verifiera en anslutning för VPN-Gateway](vpn-gateway-verify-connection-resource-manager.md).
