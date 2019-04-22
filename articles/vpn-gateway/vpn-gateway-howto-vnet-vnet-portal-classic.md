---
title: 'Skapa en anslutning mellan virtuella nätverk: Klassisk: Azure-portalen | Microsoft Docs'
description: Ansluta Azure-nätverk tillsammans med PowerShell och Azure-portalen.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/14/2018
ms.author: cherylmc
ms.openlocfilehash: e323a8d71bbffd1d29ad793dff7b5b4a072b6979
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59046130"
---
# <a name="configure-a-vnet-to-vnet-connection-classic"></a>Konfigurera en VNet-till-VNet-anslutning (klassisk)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

Den här artikeln hjälper dig att skapa en VPN-gatewayanslutning mellan virtuella nätverk. De virtuella nätverken kan finnas i samma eller olika regioner och i samma eller olika prenumerationer. Stegen i den här artikeln gäller den klassiska distributionsmodellen och Azure-portalen. Du kan också skapa den här konfigurationen med ett annat distributionsverktyg eller en annan distributionsmodell genom att välja ett annat alternativ i listan nedan:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Azure CLI](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Azure Portal (klassisk)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Ansluta olika distributionsmodeller – Azure Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Ansluta olika distributionsmodeller – PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

![VNet-till-VNet-anslutning Diagram](./media/vpn-gateway-howto-vnet-vnet-portal-classic/v2vclassic.png)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="about-vnet-to-vnet-connections"></a>Om VNet-till-VNet-anslutningar

Ansluta ett virtuellt nätverk till ett annat virtuellt nätverk (VNet till VNet) i den klassiska distributionsmodellen med hjälp av en VPN-gateway liknar ansluter ett virtuellt nätverk till en lokal plats. Båda typerna av anslutning använder en VPN-gateway för att få en säker tunnel med IPsec/IKE.

De VNets du ansluter kan finnas i olika prenumerationer och olika regioner. Du kan kombinera VNet till VNet-kommunikation med konfigurationer för flera platser. Det innebär att du kan etablera nätverkstopologier som kombinerar anslutningen för flera platser med en intern virtuell nätverksanslutning.

![VNet-till-VNet-anslutningar](./media/vpn-gateway-howto-vnet-vnet-portal-classic/aboutconnections.png)

### <a name="why"></a>Varför ska man ansluta virtuella nätverk?

Du kan vilja ansluta virtuella nätverk av följande skäl:

* **Geografisk redundans i flera regioner och geografisk närvaro**

  * Du kan ange din egna geografiska replikering eller synkronisering med en säker anslutning, utan att passera några Internet-slutpunkter.
  * Med Azure Load Balancer och Microsoft eller tredje parts klusterteknik kan konfigurera du arbetsbelastning med hög tillgänglighet med geo-redundans i flera Azure-regioner. Ett viktigt exempel är att konfigurera SQL så att det alltid är aktiverat med tillgänglighetsgrupper som är spridda över flera Azure-regioner.
* **Regionala flernivåprogram med stark isoleringsgräns**

  * Inom samma region kan konfigurera du flernivåprogram med flera virtuella nätverk som är anslutna tillsammans med stark isolering och säker kommunikation mellan nivån kommunikation.
* **Cross-prenumeration, kommunikation mellan organisation i Azure**

  * Om du har flera Azure-prenumerationer kan ansluta du arbetsbelastningar från olika prenumerationer tillsammans på ett säkert sätt mellan virtuella nätverk.
  * Du kan aktivera kommunikation mellan olika organisationer med säkra VPN-teknik i Azure för företag eller tjänstleverantörer.

Mer information om VNet-till-VNet-anslutningar finns i [Att tänka på när du använder VNet-till-VNet](#faq) i slutet av den här artikeln.

### <a name="before-you-begin"></a>Innan du börjar

Innan du påbörjar den här övningen, hämta och installera den senaste versionen av Azure Service Management (SM) PowerShell-cmdletar. Mer information finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/overview). Vi använder portalen för de flesta av stegen, men du måste använda PowerShell för att skapa anslutningar mellan virtuella nätverk. Du kan inte skapa anslutningar som använder Azure-portalen.

## <a name="plan"></a>Steg 1 – Planera dina IP-adressintervall

Det är viktigt att bestämma vilka intervall som du använder för att konfigurera dina virtuella nätverk. Den här konfigurationen måste du se till att ingen av dina VNet-adressintervall överlappar varandra eller med någon av de lokala nätverk som de ansluter till.

I följande tabell visar ett exempel på hur du definierar dina virtuella nätverk. Använder du områdena som endast en riktlinje. Skriv ned intervall för dina virtuella nätverk. Du behöver den här informationen för senare steg.

**Exempel**

| Virtual Network | Adressutrymme | Region | Ansluter till lokal nätverksplats |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |Östra USA |VNet4Local<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |Västra USA |VNet1Local<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

## <a name="vnetvalues"></a>Steg 2 – Skapa virtuella nätverk

Skapa två virtuella nätverk i den [Azure-portalen](https://portal.azure.com). Anvisningar om hur du skapar klassiska virtuella nätverk finns i [skapa ett klassiskt virtuellt nätverk](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). 

När du använder portalen för att skapa ett klassiskt virtuellt nätverk, ska du gå till sidan virtuellt nätverk med hjälp av följande steg, annars visas inte alternativet för att skapa ett klassiskt virtuellt nätverk:

1. Klicka på ”+” att öppna sidan ”New”.
2. Skriv ”virtuell nätverksgateway” i fältet ”Sök på marketplace”. Om du i stället välja nätverk -> virtuella nätverk, får du inte alternativet för att skapa ett klassiskt virtuellt nätverk.
3. Leta upp ”virtuell nätverksgateway” från den returnerade listan och klicka på den för att öppna sidan virtuellt nätverk. 
4. Välj ”klassisk” för att skapa ett klassiskt virtuellt nätverk på sidan virtuellt nätverk. 

Om du använder den här artikeln som en övning kan använda du följande exempelvärden:

**Värden för TestVNet1**

Namn: TestVNet1<br>
Adressutrymme: 10.11.0.0/16 10.12.0.0/16 (valfritt)<br>
Undernätsnamn: standard<br>
Adressintervall för undernätet: 10.11.0.1/24<br>
Resursgrupp: ClassicRG<br>
Plats: Östra USA<br>
GatewaySubnet: 10.11.1.0/27

**Värden för TestVNet4**

Namn: TestVNet4<br>
Adressutrymme: 10.41.0.0/16 10.42.0.0/16 (valfritt)<br>
Undernätsnamn: standard<br>
Adressintervall för undernätet: 10.41.0.1/24<br>
Resursgrupp: ClassicRG<br>
Plats: Västra USA<br>
GatewaySubnet: 10.41.1.0/27

**När du skapar dina virtuella nätverk, Tänk på följande inställningar:**

* **Adressutrymmen för virtuella nätverk** – på sidan adressutrymmen för virtuella nätverk anger du det adressintervall som du vill använda för det virtuella nätverket. Dessa är de dynamiska IP-adresser som ska tilldelas de virtuella datorerna och andra rollinstanser som du distribuerar till det här virtuella nätverket.<br>De adressutrymmen som du väljer kan inte överlappa adressutrymmen för någon av de andra virtuella nätverk eller lokala platser som det här virtuella nätverket ska ansluta till.

* **Plats** – när du skapar ett virtuellt nätverk kan du koppla den till en Azure-plats (region). Om du vill att dina virtuella datorer som distribueras till det virtuella nätverket ska finnas fysiskt i västra USA, väljer du den platsen. Du kan inte ändra plats som associeras med det virtuella nätverket när du har skapat.

**När du har skapat ditt virtuella nätverk, kan du lägga till följande inställningar:**

* **Adressutrymme** – ytterligare adressutrymme krävs inte för den här konfigurationen, men du kan lägga till ytterligare adressutrymme när du har skapat det virtuella nätverket.

* **Undernät** – fler undernät krävs inte för den här konfigurationen, men du kanske vill ha dina virtuella datorer i ett undernät som är avgränsat från dina andra rollinstanser.

* **DNS-servrar** – ange DNS-servernamnet och IP-adress. Du skapar inte en DNS-server med den här inställningen. Den låter dig ange vilken DNS-server du vill använda för namnmatchning för det här virtuella nätverket.

I det här avsnittet ska du konfigurerar typen av anslutning kan den lokala platsen och skapa gatewayen.

## <a name="localsite"></a>Steg 3 – Konfigurera den lokala platsen

Azure använder de inställningar som anges i varje lokal nätverksplats för att avgöra hur du dirigerar trafik mellan virtuella nätverk. Varje virtuellt nätverk måste peka till respektive lokala nätverk som du vill dirigera trafik till. Du kan fastställa det namn som du vill använda för att referera till varje lokal nätverksplats. Det är bäst att använda något beskrivande.

Till exempel ansluter TestVNet1 till en lokal nätverksplats som du skapar med namnet ”VNet4Local”. Inställningarna för VNet4Local innehåller IP-adressprefixen för TestVNet4.

Den lokala platsen för varje virtuellt nätverk är det andra virtuella nätverket. Följande Exempelvärden används för vår konfiguration:

| Virtual Network | Adressutrymme | Region | Ansluter till lokal nätverksplats |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |Östra USA |VNet4Local<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |Västra USA |VNet1Local<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

1. Leta upp TestVNet1 i Azure-portalen. I den **VPN-anslutningar** avsnitt på sidan, klickar du på **Gateway**.

    ![Ingen gateway](./media/vpn-gateway-howto-vnet-vnet-portal-classic/nogateway.png)
2. På den **ny VPN-anslutning** väljer **plats-till-plats**.
3. Klicka på **lokal plats** att öppna sidan lokal plats och konfigurera inställningarna.
4. På den **lokal plats** sidan, namnge den lokala platsen. I vårt exempel namn vi för den lokala platsen 'VNet4Local'.
5. För **IP-adress för VPN-gateway**, du kan använda alla IP-adresser som du vill, så länge det är ett giltigt format. Normalt använder du den faktiska externa IP-adressen för en VPN-enhet. Men för en klassisk VNet-till-VNet-konfigurationen måste du använda offentliga IP-adress som är tilldelad till gatewayen för ditt virtuella nätverk. Med tanke på att du har ännu inte har skapat den virtuella nätverksgatewayen kan ange du någon giltig offentlig IP-adress som platshållare.<br>Lämna inte det tomt – det är inte valfritt för den här konfigurationen. I ett senare steg, gå tillbaka till de här inställningarna och konfigurera dem med motsvarande virtuella nätverkets gateway IP-adresser när Azure genererar den.
6. För **Klientadressutrymme**, använda adressutrymmet för det andra virtuella nätverket. Referera till ditt planering exempel. Klicka på **OK** att spara dina inställningar och gå tillbaka till den **ny VPN-anslutning** sidan.

    ![lokal plats](./media/vpn-gateway-howto-vnet-vnet-portal-classic/localsite.png)

## <a name="gw"></a>Steg 4 – Skapa virtuell nätverksgateway

Varje virtuellt nätverk måste ha en virtuell nätverksgateway. Den virtuella nätverksgatewayen dirigerar och krypterar trafik.

1. Markera kryssrutan **Skapa gateway omedelbart** på sidan **Ny VPN-anslutning**.
2. Klicka på **undernät, storlek och routningstyp**. På den **gatewaykonfiguration** klickar du på **undernät**.
3. Namnet på gateway-undernätet fylls i automatiskt med det obligatoriska namnet ”GatewaySubnet”. Den **adressintervall** innehåller IP-adresser som allokerats till VPN-gatewaytjänsterna. Tillåt ett gateway-undernät/29 för vissa konfigurationer, men det är bäst att använda en/28 eller/27 för att hantera framtida konfigurationer som kan kräva flera IP-adresser för gateway-tjänsterna. I vårt exempelinställningar använder vi 10.11.1.0/27. Justera adressutrymmet och klicka sedan på **OK**.
4. Konfigurera den **Gatewaystorlek**. Den här inställningen refererar till den [Gateway-SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku).
5. Konfigurera den **Routningstyp**. Routning skriver för den här konfigurationen måste vara **dynamisk**. Du kan inte ändra routningstyp som senare om du inte plocka ner gatewayen och skapa en ny.
6. Klicka på **OK**.
7. På den **ny VPN-anslutning** klickar du på **OK** och börjar skapa den virtuella nätverksgatewayen. Att skapa en gateway kan ofta ta 45 minuter eller mer, beroende på vald gateway-SKU.

## <a name="vnet4settings"></a>Steg 5 – konfigurera TestVNet4-inställningar

Upprepa stegen för att [skapa en lokal plats](#localsite) och [skapa den virtuella nätverksgatewayen](#gw) konfigurera TestVNet4, ersätter värden vid behov. Om du gör detta som en övning, använder du den [exempelvärden](#vnetvalues).

## <a name="updatelocal"></a>Steg 6 – uppdatera lokala platser

När du har skapat din virtuella nätverksgatewayer för båda de virtuella nätverken måste du justera de lokala platserna **IP-adress för VPN-gateway** värden.

|Namn på virtuellt nätverk|Anslutna platsen|Gatewayens IP-adress|
|:--- |:--- |:--- |
|TestVNet1|VNet4Local|VPN-gatewayens IP-adress för TestVNet4|
|TestVNet4|VNet1Local|VPN-gatewayens IP-adress för TestVNet1|

### <a name="part-1---get-the-virtual-network-gateway-public-ip-address"></a>Del 1 – hämta virtuellt nätverk gatewayens offentliga IP-adress

1. Leta upp ditt virtuella nätverk i Azure-portalen.
2. Klicka för att öppna det virtuella nätverket **översikt** sidan. På sidan i **VPN-anslutningar**, du kan visa IP-adressen för din virtuella nätverksgateway.

   ![Offentlig IP-adress](./media/vpn-gateway-howto-vnet-vnet-portal-classic/publicIP.png)
3. Kopiera IP-adress. Du kan använda den i nästa avsnitt.
4. Upprepa dessa steg för TestVNet4

### <a name="part-2---modify-the-local-sites"></a>Del 2 – ändra de lokala platserna

1. Leta upp ditt virtuella nätverk i Azure-portalen.
2. I det virtuella nätverket **översikt** klickar du på den lokala platsen.

   ![Lokal plats som har skapats](./media/vpn-gateway-howto-vnet-vnet-portal-classic/local.png)
3. På den **plats-till-plats VPN-anslutningar** klickar du på namnet på den lokala platsen som du vill ändra.

   ![Öppna lokal plats](./media/vpn-gateway-howto-vnet-vnet-portal-classic/openlocal.png)
4. Klicka på den **lokal plats** som du vill ändra.

   ![Ändra plats](./media/vpn-gateway-howto-vnet-vnet-portal-classic/connections.png)
5. Uppdatera den **IP-adress för VPN-gateway** och klicka på **OK** att spara inställningarna.

   ![Gateway IP](./media/vpn-gateway-howto-vnet-vnet-portal-classic/gwupdate.png)
6. Stäng de övriga sidorna.
7. Upprepa dessa steg för TestVNet4.

## <a name="getvalues"></a>Steg 7 – hämta värden från nätverkskonfigurationsfilen

När du skapar klassiska virtuella nätverk i Azure portal är det namn som du vill visa inte det fullständiga namnet som du använder för PowerShell. Till exempel ett VNet som verkar ha namnet **TestVNet1** i portalen kan ha en mycket längre namn i nätverkskonfigurationsfilen. Namnet kan se ut ungefär som: **Gruppera ClassicRG TestVNet1**. När du skapar dina anslutningar, är det viktigt att använda värdena som visas i nätverkskonfigurationsfilen.

I följande steg ska du ansluta till ditt Azure-konto och ladda ned och visa nätverkskonfigurationsfilen så att du får de värden som krävs för dina anslutningar.

1. Hämta och installera den senaste versionen av Azure Service Management (SM) PowerShell-cmdletar. Mer information finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/overview).

2. Öppna PowerShell-konsolen med utökade rättigheter och anslut till ditt konto. Använd följande exempel för att ansluta:

   ```powershell
   Connect-AzAccount
   ```

   Kontrollera prenumerationerna för kontot.

   ```powershell
   Get-AzSubscription
   ```

   Om du har mer än en prenumeration väljer du den du vill använda.

   ```powershell
   Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
   ```

   Använd sedan följande cmdlet för att lägga till din Azure-prenumeration i PowerShell för den klassiska distributionsmodellen.

   ```powershell
   Add-AzureAccount
   ```
3. Exportera och visa nätverkskonfigurationsfilen. Skapa en katalog på datorn och exportera sedan nätverkskonfigurationsfilen till katalogen. I det här exemplet exporteras nätverkskonfigurationsfilen till **C:\AzureNet**.

   ```powershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
4. Öppna filen med en textredigerare och visa namnen för dina virtuella nätverk och platser. Dessa ska vara det namn som du använder när du skapar dina anslutningar.<br>VNet-namnen visas som **VirtualNetworkSite name =**<br>Platsnamn anges som **LocalNetworkSiteRef namn =**

## <a name="createconnections"></a>Steg 8 – skapa VPN-gatewayanslutningar

När de föregående stegen har slutförts, kan du ställa in de i förväg delade IPsec/IKE-nycklarna och skapa anslutningen. Den här uppsättningen med steg används PowerShell. VNet-till-VNet-anslutningar för den klassiska distributionsmodellen kan inte konfigureras i Azure-portalen.

Observera att den delade nyckeln är exakt samma i exemplen är. Den delade nyckeln måste alltid matcha. Glöm inte att ersätta värdena i de här exemplen med exakt namn för ditt virtuella nätverk och lokala nätverksplatser.

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
3. Vänta tills anslutningar kan initieras. När gatewayen har initierats är statusen ”klar”.

   ```
   Error          :
   HttpStatusCode : OK
   Id             :
   Status         : Successful
   RequestId      :
   StatusCode     : OK
   ```

## <a name="faq"></a>VNet-till-VNet-överväganden för klassiska virtuella nätverk
* De virtuella nätverken kan finnas i samma eller olika prenumerationer.
* De virtuella nätverken kan finnas i samma eller olika Azure-regioner (platser).
* En molntjänst eller en slutpunkt för belastningsutjämning kan inte omfatta flera virtuella nätverk, även om de är sammankopplade.
* Ansluta flera virtuella nätverk tillsammans kräver inte någon VPN-enheter.
* VNet-till-VNet stöder anslutning av Azure virtuella nätverk. Det stöder inte anslutning av virtuella datorer eller molntjänster som inte har distribuerats till ett virtuellt nätverk.
* VNet-till-VNet kräver dynamiska routnings-gatewayer. Azure gatewayer med statisk routning stöds inte.
* Virtuell nätverksanslutning kan användas samtidigt med VPN på flera platser. Det finns högst 10 VPN-tunnlar för en VPN-gateway för virtuellt nätverk som ansluter till andra virtuella nätverk eller till lokala platser.
* Adressutrymmen till virtuella nätverk och lokala nätverksplatser får inte överlappa varandra. Överlappande adressutrymmen innebär att skapa virtuella nätverk eller överför netcfg-konfigurationsfiler misslyckas.
* Redundanta tunnlar mellan ett virtuellt nätverkspar stöds inte.
* Alla VPN-tunnlar för det virtuella nätverket, inklusive P2S VPN, delar den tillgängliga bandbredden för VPN-gateway och den samma VPN-gateways upptids-SLA i Azure.
* VNet-till-VNet-trafik skickas via Azures stamnätverk.

## <a name="next-steps"></a>Nästa steg
Verifiera dina anslutningar. Se [verifiera en VPN-gatewayanslutning](vpn-gateway-verify-connection-resource-manager.md).
