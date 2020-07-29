---
title: 'Skapa en anslutning mellan virtuella nätverk: klassisk: Azure Portal'
description: Anslut virtuella Azure-nätverk tillsammans med PowerShell och Azure Portal.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/12/2020
ms.author: cherylmc
ms.openlocfilehash: bdd27645045195016b7a563787470bf6f2187115
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84985461"
---
# <a name="configure-a-vnet-to-vnet-connection-classic"></a>Konfigurera en VNet-till-VNet-anslutning (klassisk)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

Den här artikeln hjälper dig att skapa en VPN gateway-anslutning mellan virtuella nätverk. De virtuella nätverken kan finnas i samma eller olika regioner och i samma eller olika prenumerationer. Stegen i den här artikeln gäller den klassiska distributions modellen och Azure Portal. Du kan också skapa den här konfigurationen med ett annat distributionsverktyg eller en annan distributionsmodell genom att välja ett annat alternativ i listan nedan:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Azure CLI](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Azure Portal (klassisk)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Ansluta olika distributionsmodeller – Azure Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Ansluta olika distributionsmodeller – PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

![Diagram över VNet till VNet-anslutning](./media/vpn-gateway-howto-vnet-vnet-portal-classic/v2vclassic.png)



## <a name="about-vnet-to-vnet-connections"></a>Om VNet-till-VNet-anslutningar

Att ansluta ett virtuellt nätverk till ett annat virtuellt nätverk (VNet-till-VNet) i den klassiska distributions modellen med hjälp av en VPN-gateway liknar att ansluta ett virtuellt nätverk till en lokal plats. Båda typerna av anslutning använder en VPN-gateway för att få en säker tunnel med IPsec/IKE.

Virtuella nätverk du ansluter kan finnas i olika prenumerationer och i olika regioner. Du kan kombinera VNet till VNet-kommunikation med konfigurationer för flera platser. Det innebär att du kan etablera nätverkstopologier som kombinerar anslutningen för flera platser med en intern virtuell nätverksanslutning.

![VNet till VNet-anslutningar](./media/vpn-gateway-howto-vnet-vnet-portal-classic/aboutconnections.png)

### <a name="why-connect-virtual-networks"></a><a name="why"></a>Varför ska man ansluta virtuella nätverk?

Du kan vilja ansluta virtuella nätverk av följande skäl:

* **Geografisk redundans i flera regioner och geografisk närvaro**

  * Du kan ange din egna geografiska replikering eller synkronisering med en säker anslutning, utan att passera några Internet-slutpunkter.
  * Med Azure Load Balancer-och Microsoft-teknik och kluster teknik från tredje part kan du konfigurera arbets belastning med hög tillgänglighet med GEO-redundans i flera Azure-regioner. Ett viktigt exempel är att konfigurera att SQL alltid är aktiverat med tillgänglighetsgrupper som är spridda över flera Azure-regioner.
* **Regional program med flera nivåer med stark isolerings gränser**

  * Inom samma region kan du konfigurera flera nivåer med flera virtuella nätverk som är anslutna till varandra med stark isolering och säker kommunikation mellan olika nivåer.
* **Kors prenumeration, kommunikation mellan organisationer i Azure**

  * Om du har flera Azure-prenumerationer kan du ansluta arbets belastningar från olika prenumerationer på ett säkert sätt mellan virtuella nätverk.
  * För företag eller tjänste leverantörer kan du aktivera kommunikation mellan organisationer med säker VPN-teknik i Azure.

Mer information om VNet-till-VNet-anslutningar finns i [Att tänka på när du använder VNet-till-VNet](#faq) i slutet av den här artikeln.

### <a name="working-with-azure-powershell"></a><a name="powershell"></a>Arbeta med Azure PowerShell

Vi använder portalen för de flesta av stegen, men du måste använda PowerShell för att skapa anslutningarna mellan virtuella nätverk. Du kan inte skapa anslutningar med hjälp av Azure Portal. [!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

## <a name="step-1---plan-your-ip-address-ranges"></a><a name="plan"></a>Steg 1 – Planera dina IP-adressintervall

Det är viktigt att bestämma vilka intervall som ska användas för att konfigurera dina virtuella nätverk. För den här konfigurationen måste du se till att inga av dina VNet-intervall överlappar varandra, eller med något av de lokala nätverk som de ansluter till.

I följande tabell visas ett exempel på hur du definierar din virtuella nätverk. Använd intervallen som en rikt linje. Skriv ned intervallen för dina virtuella nätverk. Du behöver den här informationen för senare steg.

**Exempel**

| Virtual Network | Adressutrymme | Region | Ansluter till den lokala nätverks platsen |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |USA, östra |VNet4Local<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |USA, västra |VNet1Local<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

## <a name="step-2---create-the-virtual-networks"></a><a name="vnetvalues"></a>Steg 2 – skapa de virtuella nätverken

Skapa två virtuella nätverk i [Azure Portal](https://portal.azure.com). Anvisningar för hur du skapar klassiska virtuella nätverk finns i [skapa ett klassiskt virtuellt nätverk](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). 

När du använder portalen för att skapa ett klassiskt virtuellt nätverk måste du gå till sidan virtuellt nätverk med hjälp av följande steg, annars är alternativet för att skapa ett klassiskt virtuellt nätverk inte:

1. Klicka på "+" för att öppna sidan "nytt".
2. I fältet Sök i Marketplace skriver du Virtual Network. Om du i stället väljer nätverk-> Virtual Network, får du inte alternativet att skapa ett klassiskt VNet.
3. Leta upp Virtual Network från den returnerade listan och klicka på den för att öppna sidan Virtual Network. 
4. På sidan virtuellt nätverk väljer du klassisk för att skapa ett klassiskt VNet. 

Om du använder den här artikeln som en övning kan du använda följande exempel värden:

**Värden för TestVNet1**

Namn: TestVNet1<br>
Adress utrymme: 10.11.0.0/16, 10.12.0.0/16 (valfritt)<br>
Under näts namn: standard<br>
Adress intervall för under nätet: 10.11.0.1/24<br>
Resurs grupp: ClassicRG<br>
Plats: USA, östra<br>
GatewaySubnet: 10.11.1.0/27

**Värden för TestVNet4**

Namn: TestVNet4<br>
Adress utrymme: 10.41.0.0/16, 10.42.0.0/16 (valfritt)<br>
Under näts namn: standard<br>
Adress intervall för under nätet: 10.41.0.1/24<br>
Resurs grupp: ClassicRG<br>
Plats: USA, västra<br>
GatewaySubnet: 10.41.1.0/27

**Tänk på följande inställningar när du skapar din virtuella nätverk:**

* **Virtual Network adress utrymmen** – på sidan Virtual Network adress utrymmen anger du det adress intervall som du vill använda för det virtuella nätverket. Detta är de dynamiska IP-adresser som ska tilldelas de virtuella datorerna och andra roll instanser som du distribuerar till det här virtuella nätverket.<br>De adress utrymmen du väljer får inte överlappa adress utrymmena för någon av de andra virtuella nätverk eller lokala platser som det virtuella nätverket ska ansluta till.

* **Plats** – när du skapar ett virtuellt nätverk kopplar du det till en Azure-plats (region). Om du till exempel vill att de virtuella datorerna som distribueras till ditt virtuella nätverk ska vara fysiskt placerade i västra USA väljer du den platsen. Du kan inte ändra platsen som är kopplad till det virtuella nätverket när du har skapat det.

**När du har skapat din virtuella nätverk kan du lägga till följande inställningar:**

* **Adress utrymme** – ytterligare adress utrymme krävs inte för den här konfigurationen, men du kan lägga till ytterligare adress utrymme när du har skapat VNet.

* **Undernät** – ytterligare undernät krävs inte för den här konfigurationen, men du kanske vill ha dina virtuella datorer i ett undernät som är skilt från dina andra roll instanser.

* **DNS-servrar** – ange DNS-serverns namn och IP-adress. Du skapar inte en DNS-server med den här inställningen. Den låter dig ange vilken DNS-server du vill använda för namnmatchning för det här virtuella nätverket.

I det här avsnittet konfigurerar du anslutnings typen, den lokala platsen och skapar gatewayen.

## <a name="step-3---configure-the-local-site"></a><a name="localsite"></a>Steg 3 – Konfigurera den lokala platsen

Azure använder inställningarna som anges i varje lokal nätverks plats för att fastställa hur trafik ska dirigeras mellan virtuella nätverk. Varje VNet måste peka på respektive lokalt nätverk som du vill dirigera trafiken till. Du bestämmer det namn som du vill använda för att referera till varje lokal nätverks plats. Det är bäst att använda något beskrivande.

Till exempel ansluter TestVNet1 till en lokal nätverks plats som du skapar med namnet "VNet4Local". Inställningarna för VNet4Local innehåller adressprefix för TestVNet4.

Den lokala platsen för varje VNet är det andra virtuella nätverket. Följande exempel värden används för vår konfiguration:

| Virtual Network | Adressutrymme | Region | Ansluter till den lokala nätverks platsen |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |USA, östra |VNet4Local<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |USA, västra |VNet1Local<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

1. Hitta TestVNet1 i Azure Portal. I avsnittet **VPN-anslutningar** på sidan klickar du på **Gateway**.

    ![Ingen gateway](./media/vpn-gateway-howto-vnet-vnet-portal-classic/nogateway.png)
2. På sidan **ny VPN-anslutning** väljer du **plats-till-plats**.
3. Klicka på **lokal plats** för att öppna sidan lokal plats och konfigurera inställningarna.
4. På sidan **lokal plats** namnger du din lokala plats. I vårt exempel namnger vi den lokala platsen "VNet4Local".
5. Du kan använda valfri IP-adress för **VPN-gatewayens IP-adress**så länge formatet är giltigt. Normalt använder du den faktiska externa IP-adressen för en VPN-enhet. Men för en klassisk VNet-till-VNet-konfiguration använder du den offentliga IP-adress som är tilldelad gatewayen för ditt VNet. Eftersom du ännu inte har skapat den virtuella Nätverksgatewayen anger du en giltig offentlig IP-adress som plats hållare.<br>Lämna inte detta tomt – det är inte valfritt för den här konfigurationen. I ett senare steg går du tillbaka till de här inställningarna och konfigurerar dem med motsvarande virtuella nätverksgateways IP-adresser när Azure genererar den.
6. Använd adress utrymmet för det andra virtuella nätverket för **klient adress utrymme**. Se planerings exemplet. Spara inställningarna och gå tillbaka till sidan **ny VPN-anslutning** genom att klicka på **OK** .

    ![lokal plats](./media/vpn-gateway-howto-vnet-vnet-portal-classic/localsite.png)

## <a name="step-4---create-the-virtual-network-gateway"></a><a name="gw"></a>Steg 4 – skapa den virtuella Nätverksgatewayen

Varje virtuellt nätverk måste ha en virtuell nätverksgateway. Den virtuella Nätverksgatewayen dirigerar och krypterar trafiken.

1. Markera kryssrutan **Skapa gateway omedelbart** på sidan **Ny VPN-anslutning**.
2. Klicka på **undernät, storlek och typ av routning**. På sidan **gateway-konfiguration** klickar du på **undernät**.
3. Gateway-undernätets namn fylls i automatiskt med det obligatoriska namnet "GatewaySubnet". **Adress intervallet** innehåller de IP-adresser som allokeras till VPN gateway-tjänsterna. Vissa konfigurationer tillåter ett Gateway-undernät på/29, men det är bäst att använda en/28 eller/27 för att hantera framtida konfigurationer som kan kräva fler IP-adresser för gateway-tjänsterna. I våra exempel inställningar använder vi 10.11.1.0/27. Justera adress utrymmet och klicka sedan på **OK**.
4. Konfigurera **Gateway-storleken**. Den här inställningen refererar till [Gateway-SKU: n](vpn-gateway-about-vpn-gateway-settings.md#gwsku).
5. Konfigurera **Dirigerings typen**. Dirigerings typen för den här konfigurationen måste vara **dynamisk**. Du kan inte ändra cirkulations typen senare om du inte avrundar gatewayen och skapar en ny.
6. Klicka på **OK**.
7. På sidan **ny VPN-anslutning** klickar du på **OK** för att börja skapa den virtuella Nätverksgatewayen. Att skapa en gateway kan ofta ta 45 minuter eller mer, beroende på vald gateway-SKU.

## <a name="step-5---configure-testvnet4-settings"></a><a name="vnet4settings"></a>Steg 5 – Konfigurera TestVNet4-inställningar

Upprepa stegen för att [skapa en lokal plats](#localsite) och [skapa den virtuella Nätverksgatewayen](#gw) för att konfigurera TestVNet4, och ersätt värdena vid behov. Om du gör detta i övningen använder du [exempel värden](#vnetvalues).

## <a name="step-6---update-the-local-sites"></a><a name="updatelocal"></a>Steg 6 – uppdatera de lokala platserna

När dina virtuella nätverks-gatewayer har skapats för båda virtuella nätverk måste du justera **IP-adresserna** för de lokala platsernas VPN-gateway.

|VNet-namn|Ansluten webbplats|IP-adress för gateway|
|:--- |:--- |:--- |
|TestVNet1|VNet4Local|IP-adress för VPN-gateway för TestVNet4|
|TestVNet4|VNet1Local|IP-adress för VPN-gateway för TestVNet1|

### <a name="part-1---get-the-virtual-network-gateway-public-ip-address"></a>Del 1 – hämta den virtuella nätverks-gatewayens offentliga IP-adress

1. Leta upp det virtuella nätverket i Azure Portal.
2. Klicka för att öppna sidan VNet- **Översikt** . På sidan kan du Visa IP-adressen för din virtuella nätverksgateway i **VPN-anslutningar**.

   ![Offentlig IP-adress](./media/vpn-gateway-howto-vnet-vnet-portal-classic/publicIP.png)
3. Kopiera IP-adressen. Du kommer att använda den i nästa avsnitt.
4. Upprepa de här stegen för TestVNet4

### <a name="part-2---modify-the-local-sites"></a>Del 2 – ändra de lokala platserna

1. Leta upp det virtuella nätverket i Azure Portal.
2. På sidan VNet- **Översikt** klickar du på den lokala platsen.

   ![En lokal plats har skapats](./media/vpn-gateway-howto-vnet-vnet-portal-classic/local.png)
3. På sidan **plats-till-plats-VPN-anslutningar** klickar du på namnet på den lokala plats som du vill ändra.

   ![Öppna lokal plats](./media/vpn-gateway-howto-vnet-vnet-portal-classic/openlocal.png)
4. Klicka på den **lokala plats** som du vill ändra.

   ![Ändra plats](./media/vpn-gateway-howto-vnet-vnet-portal-classic/connections.png)
5. Uppdatera **IP-adressen för VPN-gatewayen** och spara inställningarna genom att klicka på **OK** .

   ![Gateway-IP](./media/vpn-gateway-howto-vnet-vnet-portal-classic/gwupdate.png)
6. Stäng de andra sidorna.
7. Upprepa de här stegen för TestVNet4.

## <a name="step-7---retrieve-values-from-the-network-configuration-file"></a><a name="getvalues"></a>Steg 7 – hämta värden från nätverks konfigurations filen

När du skapar en klassisk virtuella nätverk i Azure Portal, är det namn som du visar inte det fullständiga namnet som du använder för PowerShell. Ett VNet som verkar ha namnet **TestVNet1** i portalen kan till exempel ha ett mycket längre namn i nätverks konfigurations filen. Namnet kan se ut ungefär så här: **Group ClassicRG TestVNet1**. När du skapar dina anslutningar är det viktigt att använda de värden som visas i nätverks konfigurations filen.

I följande steg ska du ansluta till ditt Azure-konto och hämta och Visa nätverks konfigurations filen för att hämta de värden som krävs för dina anslutningar.

1. Hämta och installera den senaste versionen av PowerShell-cmdletarna för Azure Service Management (SM). Mer information finns i [arbeta med Azure PowerShell](#powershell).

2. Öppna PowerShell-konsolen med utökade rättigheter. Använd följande exempel för att ansluta. Du måste köra dessa kommandon lokalt med PowerShell Service Management-modulen. Använd följande kommando för att växla till Service Management:

   ```powershell
   azure config mode asm
   ```
3. Anslut till ditt konto. Använd följande exempel för att ansluta:

   ```powershell
   Add-AzureAccount
   ```
4. Kontrollera prenumerationerna för kontot.

   ```powershell
   Get-AzureSubscription
   ```
5. Om du har mer än en prenumeration väljer du den du vill använda.

   ```powershell
   Select-AzureSubscription -SubscriptionId "Replace_with_your_subscription_ID"
   ```
6. Exportera och Visa nätverks konfigurations filen. Skapa en katalog på datorn och exportera sedan nätverkskonfigurationsfilen till katalogen. I det här exemplet exporteras nätverks konfigurations filen till **C:\AzureNet**.

   ```powershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
7. Öppna filen med en text redigerare och visa namnen på dina virtuella nätverk och platser. Namnen är de namn som du använder när du skapar dina anslutningar.<br>VNet-namn visas som **VirtualNetworkSite namn =**<br>Plats namn anges som **LocalNetworkSiteRef namn =**

## <a name="step-8---create-the-vpn-gateway-connections"></a><a name="createconnections"></a>Steg 8 – skapa VPN gateway-anslutningar

När alla föregående steg har slutförts, kan du ställa in i förväg delade IPsec/IKE-nycklar och skapa anslutningen. Den här uppsättningen steg använder PowerShell. VNet-till-VNet-anslutningar för den klassiska distributions modellen kan inte konfigureras i Azure Portal.

I exemplen ser du att den delade nyckeln är exakt samma. Den delade nyckeln måste alltid matcha. Se till att ersätta värdena i de här exemplen med de exakta namnen för dina virtuella nätverk-och lokala nätverks platser.

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
3. Vänta tills anslutningarna har initierats. När gatewayen har initierats är statusen "lyckades".

   ```
   Error          :
   HttpStatusCode : OK
   Id             :
   Status         : Successful
   RequestId      :
   StatusCode     : OK
   ```

## <a name="vnet-to-vnet-considerations-for-classic-vnets"></a><a name="faq"></a>Aspekter av VNet-till-VNet-överväganden för klassiska virtuella nätverk
* De virtuella nätverken kan finnas i samma eller olika prenumerationer.
* De virtuella nätverken kan finnas i samma eller olika Azure-regioner (platser).
* En moln tjänst eller en slut punkt för belastnings utjämning kan inte omfatta flera virtuella nätverk, även om de är anslutna till varandra.
* Att ansluta flera virtuella nätverk tillsammans kräver inte några VPN-enheter.
* VNet-till-VNet stöder anslutning av virtuella Azure-nätverk. Den stöder inte anslutning av virtuella datorer eller moln tjänster som inte har distribuerats till ett virtuellt nätverk.
* VNet-till-VNet kräver dynamiska routnings-gatewayer. Azures statiska RRAS-gatewayer stöds inte.
* Virtuell nätverksanslutning kan användas samtidigt med VPN på flera platser. Det finns högst 10 VPN-tunnlar för en VPN-gateway för virtuellt nätverk som ansluter till antingen andra virtuella nätverk eller lokala platser.
* Adressutrymmen till virtuella nätverk och lokala nätverksplatser får inte överlappa varandra. Överlappande adress utrymmen innebär att virtuella nätverk skapas eller att konfiguration av netcfg-filer inte kan köras.
* Redundanta tunnlar mellan ett virtuellt nätverkspar stöds inte.
* Alla VPN-tunnlar för VNet, inklusive P2S VPN, delar den tillgängliga bandbredden för VPN-gatewayen och samma VPN-gateway drift tid för service avtal i Azure.
* VNet-till-VNet-trafik överförs över Azure-stamnätet.

## <a name="next-steps"></a>Nästa steg
Verifiera dina anslutningar. Se [Verifiera en VPN gateway anslutning](vpn-gateway-verify-connection-resource-manager.md).
