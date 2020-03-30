---
title: 'Skapa en anslutning mellan virtuella nätverk: klassisk: Azure-portal'
description: Anslut virtuella Azure-nätverk tillsammans med PowerShell och Azure-portalen.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 02/12/2020
ms.author: cherylmc
ms.openlocfilehash: 63c6329ad62289cd127902c1438073b28fc8683e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201857"
---
# <a name="configure-a-vnet-to-vnet-connection-classic"></a>Konfigurera en VNet-till-VNet-anslutning (klassisk)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

Den här artikeln hjälper dig att skapa en VPN-gateway-anslutning mellan virtuella nätverk. De virtuella nätverken kan finnas i samma eller olika regioner och i samma eller olika prenumerationer. Stegen i den här artikeln gäller den klassiska distributionsmodellen och Azure-portalen. Du kan också skapa den här konfigurationen med ett annat distributionsverktyg eller en annan distributionsmodell genom att välja ett annat alternativ i listan nedan:

> [!div class="op_single_selector"]
> * [Azure-portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Powershell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Azure CLI](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Azure Portal (klassisk)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Ansluta olika distributionsmodeller – Azure Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Ansluta olika distributionsmodeller – PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

![VNet till VNet-anslutningsdiagram](./media/vpn-gateway-howto-vnet-vnet-portal-classic/v2vclassic.png)



## <a name="about-vnet-to-vnet-connections"></a>Om VNet-till-VNet-anslutningar

Att ansluta ett virtuellt nätverk till ett annat virtuellt nätverk (VNet-till-VNet) i den klassiska distributionsmodellen med hjälp av en VPN-gateway liknar att ansluta ett virtuellt nätverk till en lokal plats. Båda typerna av anslutning använder en VPN-gateway för att få en säker tunnel med IPsec/IKE.

De virtuella nätverken du ansluter kan finnas i olika prenumerationer och olika regioner. Du kan kombinera VNet-till VNet-kommunikation med konfigurationer med flera webbplatser. Det innebär att du kan etablera nätverkstopologier som kombinerar anslutningen för flera platser med en intern virtuell nätverksanslutning.

![VNet till VNet-anslutningar](./media/vpn-gateway-howto-vnet-vnet-portal-classic/aboutconnections.png)

### <a name="why-connect-virtual-networks"></a><a name="why"></a>Varför ska man ansluta virtuella nätverk?

Du kan vilja ansluta virtuella nätverk av följande skäl:

* **Geografisk redundans i flera regioner och geografisk närvaro**

  * Du kan ange din egna geografiska replikering eller synkronisering med en säker anslutning, utan att passera några Internet-slutpunkter.
  * Med Azure Load Balancer och Microsoft eller klusterteknik från tredje part kan du konfigurera högtillgänglig arbetsbelastning med georedundans i flera Azure-regioner. Ett viktigt exempel är att konfigurera att SQL alltid är aktiverat med tillgänglighetsgrupper som är spridda över flera Azure-regioner.
* **Regionala flernivåapplikationer med stark isoleringsgräns**

  * Inom samma region kan du ställa in program på flera nivåer med flera virtuella nätverk som är kopplade tillsammans med stark isolering och säker kommunikation mellan nivåer.
* **Korsprenumeration, kommunikation mellan organisationer i Azure**

  * Om du har flera Azure-prenumerationer kan du ansluta arbetsbelastningar från olika prenumerationer tillsammans på ett säkert sätt mellan virtuella nätverk.
  * För företag eller tjänsteleverantörer kan du aktivera kommunikation över flera organisationer med säker VPN-teknik i Azure.

Mer information om VNet-till-VNet-anslutningar finns i [Att tänka på när du använder VNet-till-VNet](#faq) i slutet av den här artikeln.

### <a name="working-with-azure-powershell"></a><a name="powershell"></a>Arbeta med Azure PowerShell

Vi använder portalen för de flesta stegen, men du måste använda PowerShell för att skapa anslutningar mellan virtuella nätverk. Du kan inte skapa anslutningarna med Azure-portalen. [!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

## <a name="step-1---plan-your-ip-address-ranges"></a><a name="plan"></a>Steg 1 – Planera dina IP-adressintervall

Det är viktigt att bestämma vilka intervall som du ska använda för att konfigurera dina virtuella nätverk. För den här konfigurationen måste du se till att inget av dina virtuella nätverksintervall överlappar varandra eller med något av de lokala nätverk som de ansluter till.

I följande tabell visas ett exempel på hur du definierar dina virtuella nätverk. Använd endast intervallen som riktlinje. Skriv ner intervallen för dina virtuella nätverk. Du behöver den här informationen för senare steg.

**Exempel**

| Virtual Network | Adressutrymme | Region | Ansluter till lokal nätverksplats |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |USA, östra |VNet4Lokal<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |USA, västra |VNet1Lokal<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

## <a name="step-2---create-the-virtual-networks"></a><a name="vnetvalues"></a>Steg 2 - Skapa virtuella nätverk

Skapa två virtuella nätverk i [Azure-portalen](https://portal.azure.com). Steg för att skapa klassiska virtuella nätverk finns i [Skapa ett klassiskt virtuellt nätverk](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). 

När du använder portalen för att skapa ett klassiskt virtuellt nätverk måste du navigera till den virtuella nätverkssidan med hjälp av följande steg, annars visas inte alternativet att skapa ett klassiskt virtuellt nätverk:

1. Klicka på "+" för att öppna sidan "Nytt".
2. Skriv "Virtuellt nätverk" i fältet Sök på marknadsplatsen. Om du i stället väljer Nätverk -> virtuellt nätverk får du inte möjlighet att skapa ett klassiskt virtuellt nätverk.
3. Leta upp "Virtuellt nätverk" från den returnerade listan och klicka på den för att öppna sidan Virtuellt nätverk. 
4. På den virtuella nätverkssidan väljer du "Classic" för att skapa ett klassiskt virtuellt nätverk. 

Om du använder den här artikeln som en övning kan du använda följande exempelvärden:

**Värden för TestVNet1**

Namn: TestVNet1<br>
Adressutrymme: 10.11.0.0/16, 10.12.0.0/16 (tillval)<br>
Undernätsnamn: standard<br>
Undernätsadressintervall: 10.11.0.1/24<br>
Resursgrupp: ClassicRG<br>
Plats: USA, östra<br>
GatewaySubnet: 10.11.1.0/27

**Värden för TestVNet4**

Namn: TestVNet4<br>
Adressutrymme: 10.41.0.0/16, 10.42.0.0/16 (tillval)<br>
Undernätsnamn: standard<br>
Undernät adressintervall: 10.41.0.1/24<br>
Resursgrupp: ClassicRG<br>
Plats: USA, västra<br>
GatewaySubnet: 10.41.1.0/27

**När du skapar dina virtuella nätverk bör du tänka på följande inställningar:**

* **Antal virtuella nätverksadressutrymmen** – På sidan Adressutrymmen för virtuellt nätverk anger du det adressintervall som du vill använda för det virtuella nätverket. Det här är de dynamiska IP-adresser som ska tilldelas de virtuella datorerna och andra rollinstanser som du distribuerar till det här virtuella nätverket.<br>De adressutrymmen som du väljer kan inte överlappa med adressutrymmena för något av de andra virtuella nätverken eller lokala platser som det här virtuella nätverket ansluter till.

* **Plats** – När du skapar ett virtuellt nätverk associerar du det med en Azure-plats (region). Om du till exempel vill att de virtuella datorerna som distribueras till det virtuella nätverket ska vara fysiskt placerade i västra USA väljer du den platsen. Du kan inte ändra platsen som är associerad med det virtuella nätverket när du har skapat det.

**När du har skapat dina virtuella nätverk kan du lägga till följande inställningar:**

* **Adressutrymme** – Ytterligare adressutrymme krävs inte för den här konfigurationen, men du kan lägga till ytterligare adressutrymme när du har skapat det virtuella nätverket.

* **Undernät** – Ytterligare undernät krävs inte för den här konfigurationen, men du kanske vill ha dina virtuella datorer i ett undernät som är separat från dina andra rollinstanser.

* **DNS-servrar** – Ange DNS-serverns namn och IP-adress. Du skapar inte en DNS-server med den här inställningen. Den låter dig ange vilken DNS-server du vill använda för namnmatchning för det här virtuella nätverket.

I det här avsnittet konfigurerar du anslutningstypen, den lokala platsen och skapar gatewayen.

## <a name="step-3---configure-the-local-site"></a><a name="localsite"></a>Steg 3 - Konfigurera den lokala webbplatsen

Azure använder de inställningar som anges i varje lokal nätverksplats för att avgöra hur trafik mellan virtuella nätverk ska dirigeras mellan virtuella nätverk. Varje virtuella nätverk måste peka på respektive lokalt nätverk som du vill dirigera trafik till. Du bestämmer vilket namn du vill använda för att referera till varje lokal nätverksplats. Det är bäst att använda något beskrivande.

TestVNet1 ansluter till till exempel till en lokal nätverksplats som du skapar med namnet VNet4Local. Inställningarna för VNet4Local innehåller adressprefixen för TestVNet4.

Den lokala platsen för varje virtuella nätverk är det andra virtuella nätverket. Följande exempelvärden används för vår konfiguration:

| Virtual Network | Adressutrymme | Region | Ansluter till lokal nätverksplats |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |USA, östra |VNet4Lokal<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |USA, västra |VNet1Lokal<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

1. Leta reda på TestVNet1 i Azure-portalen. Klicka på **Gateway**i avsnittet **VPN-anslutningar** på sidan .

    ![Ingen gateway](./media/vpn-gateway-howto-vnet-vnet-portal-classic/nogateway.png)
2. På sidan **Ny VPN-anslutning** väljer du **Plats-till-plats**.
3. Klicka på **Lokal webbplats** om du vill öppna sidan Lokal webbplats och konfigurera inställningarna.
4. På sidan **Lokal webbplats** namnger du den lokala webbplatsen. I vårt exempel namnger vi den lokala webbplatsen "VNet4Local".
5. För **VPN gateway IP-adress**kan du använda vilken IP-adress du vill, så länge den är i ett giltigt format. Vanligtvis använder du den faktiska externa IP-adressen för en VPN-enhet. Men för en klassisk VNet-till-VNet-konfiguration använder du den offentliga IP-adressen som har tilldelats gatewayen för ditt virtuella nätverk. Med tanke på att du ännu inte har skapat den virtuella nätverksgatewayen anger du en giltig offentlig IP-adress som platshållare.<br>Lämna inte detta tomt - det är inte valfritt för den här konfigurationen. I ett senare steg går du tillbaka till dessa inställningar och konfigurerar dem med motsvarande IP-adresser för virtuella nätverksgatewayer när Azure genererar den.
6. För **klientadressutrymme**använder du adressutrymmet för det andra virtuella nätverket. Se planeringsexemplet. Klicka på **OK** för att spara inställningarna och återgå till sidan **Ny VPN-anslutning.**

    ![lokal webbplats](./media/vpn-gateway-howto-vnet-vnet-portal-classic/localsite.png)

## <a name="step-4---create-the-virtual-network-gateway"></a><a name="gw"></a>Steg 4 - Skapa den virtuella nätverksgatewayen

Varje virtuellt nätverk måste ha en virtuell nätverksgateway. Dirigerar och krypterar trafik.

1. Markera kryssrutan **Skapa gateway omedelbart** på sidan **Ny VPN-anslutning**.
2. Klicka på **Undernät, storlek och operationsföljdstyp**. Klicka på **Undernät**på **sidan Gateway-konfiguration** .
3. Gateway-undernätsnamnet fylls i automatiskt med det obligatoriska namnet "GatewaySubnet". **Adressintervallet** innehåller DE IP-adresser som allokeras till VPN-gatewaytjänsterna. Vissa konfigurationer tillåter ett gateway-undernät på /29, men det är bäst att använda en /28 eller /27 för att hantera framtida konfigurationer som kan kräva fler IP-adresser för gateway-tjänsterna. I våra exempelinställningar använder vi 10.11.1.0/27. Justera adressutrymmet och klicka sedan på **OK**.
4. Konfigurera **gatewaystorleken**. Den här inställningen refererar till [Gateway SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku).
5. Konfigurera **operationsföljdstypen**. Operationsföljdstypen för den här konfigurationen måste vara **dynamisk**. Du kan inte ändra operationsföljdstypen senare om du inte river gatewayen och skapar en ny.
6. Klicka på **OK**.
7. På sidan **Ny VPN-anslutning** klickar du på **OK** för att börja skapa den virtuella nätverksgatewayen. Att skapa en gateway kan ofta ta 45 minuter eller mer, beroende på vald gateway-SKU.

## <a name="step-5---configure-testvnet4-settings"></a><a name="vnet4settings"></a>Steg 5 - Konfigurera TestVNet4-inställningar

Upprepa stegen för att [skapa en lokal plats](#localsite) och skapa den virtuella [nätverksgatewayen](#gw) för att konfigurera TestVNet4 och ersätta värdena vid behov. Om du gör detta som en övning använder du [exempelvärdena](#vnetvalues).

## <a name="step-6---update-the-local-sites"></a><a name="updatelocal"></a>Steg 6 - Uppdatera de lokala webbplatserna

När dina virtuella nätverksgateways har skapats för båda virtuella nätverken måste du justera IP-adressvärdena för den lokala **platsens VPN-gateway.**

|VNet-namn|Ansluten webbplats|GATEWAY IP-adress|
|:--- |:--- |:--- |
|TestVNet1|VNet4Lokal|VPN gateway IP-adress för TestVNet4|
|TestVNet4|VNet1Lokal|VPN gateway IP-adress för TestVNet1|

### <a name="part-1---get-the-virtual-network-gateway-public-ip-address"></a>Del 1 - Hämta den offentliga IP-adressen för den virtuella nätverksgatewayen

1. Leta reda på ditt virtuella nätverk i Azure-portalen.
2. Klicka här om du vill öppna sidan **Översikt för** virtuella nätverk. På sidan, i **VPN-anslutningar,** kan du visa IP-adressen för din virtuella nätverksgateway.

   ![Offentlig IP-adress](./media/vpn-gateway-howto-vnet-vnet-portal-classic/publicIP.png)
3. Kopiera IP-adressen. Du kommer att använda den i nästa avsnitt.
4. Upprepa dessa steg för TestVNet4

### <a name="part-2---modify-the-local-sites"></a>Del 2 - Ändra de lokala platserna

1. Leta reda på ditt virtuella nätverk i Azure-portalen.
2. Klicka på den lokala webbplatsen på sidan **VNet-översikt.**

   ![Lokal webbplats skapad](./media/vpn-gateway-howto-vnet-vnet-portal-classic/local.png)
3. Klicka på namnet på den lokala webbplats som du vill ändra på sidan VPN-anslutningar på sidan **VPN-anslutningar** på plats till plats.

   ![Öppna lokal webbplats](./media/vpn-gateway-howto-vnet-vnet-portal-classic/openlocal.png)
4. Klicka på den **lokala webbplats** som du vill ändra.

   ![ändra webbplats](./media/vpn-gateway-howto-vnet-vnet-portal-classic/connections.png)
5. Uppdatera **VPN gateway IP-adressen** och klicka på **OK** för att spara inställningarna.

   ![gateway IP](./media/vpn-gateway-howto-vnet-vnet-portal-classic/gwupdate.png)
6. Stäng de andra sidorna.
7. Upprepa dessa steg för TestVNet4.

## <a name="step-7---retrieve-values-from-the-network-configuration-file"></a><a name="getvalues"></a>Steg 7 - Hämta värden från nätverkskonfigurationsfilen

När du skapar klassiska virtuella nätverk i Azure-portalen är namnet som du visar inte det fullständiga namnet som du använder för PowerShell. Ett virtuella nätverk som verkar heta **TestVNet1** i portalen kan till exempel ha ett mycket längre namn i nätverkskonfigurationsfilen. Namnet kan se ut ungefär som: **Group ClassicRG TestVNet1**. När du skapar dina anslutningar är det viktigt att du använder de värden som visas i nätverkskonfigurationsfilen.

I följande steg ansluter du till ditt Azure-konto och hämtar och visar nätverkskonfigurationsfilen för att hämta de värden som krävs för dina anslutningar.

1. Hämta och installera den senaste versionen av Azure Service Management (SM) PowerShell-cmdlets. Mer information finns i [Arbeta med Azure PowerShell](#powershell).

2. Öppna PowerShell-konsolen med förhöjda rättigheter. Använd följande exempel för att hjälpa dig att ansluta. Du måste köra dessa kommandon lokalt med hjälp av PowerShell-tjänsthanteringsmodulen. Om du vill växla till tjänsthantering använder du det här kommandot:

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
6. Exportera och visa nätverkskonfigurationsfilen. Skapa en katalog på datorn och exportera sedan nätverkskonfigurationsfilen till katalogen. I det här exemplet exporteras nätverkskonfigurationsfilen till **C:\AzureNet**.

   ```powershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
7. Öppna filen med en textredigerare och visa namnen på dina virtuella nätverk och webbplatser. Dessa namn är de namn du använder när du skapar dina anslutningar.<br>VNet-namn listas som **VirtualNetworkSite-namn =**<br>Webbplatsnamn listas som **LocalNetworkSiteRef-namn =**

## <a name="step-8---create-the-vpn-gateway-connections"></a><a name="createconnections"></a>Steg 8 - Skapa VPN-gatewayanslutningar

När alla tidigare steg har slutförts kan du ange IPsec/IKE-fördelade nycklar och skapa anslutningen. Den här uppsättningen steg använder PowerShell. VNet-till-VNet-anslutningar för den klassiska distributionsmodellen kan inte konfigureras i Azure-portalen.

I exemplen märker du att den delade nyckeln är exakt densamma. Den delade nyckeln måste alltid matchas. Var noga med att ersätta värdena i dessa exempel med de exakta namnen för dina virtuella nätverk och lokala nätverksplatser.

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
3. Vänta tills anslutningarna har initierats. När gatewayen har initierats är statusen "Lyckad".

   ```
   Error          :
   HttpStatusCode : OK
   Id             :
   Status         : Successful
   RequestId      :
   StatusCode     : OK
   ```

## <a name="vnet-to-vnet-considerations-for-classic-vnets"></a><a name="faq"></a>VNet-till-VNet-överväganden för klassiska virtuella nätverk
* De virtuella nätverken kan finnas i samma eller olika prenumerationer.
* De virtuella nätverken kan finnas i samma eller olika Azure-regioner (platser).
* En molntjänst eller en belastningsutjämningsslutpunkt kan inte sträcka sig över virtuella nätverk, även om de är anslutna tillsammans.
* Att ansluta flera virtuella nätverk tillsammans kräver inga VPN-enheter.
* VNet-till-VNet stöder anslutning av Virtuella Azure-nätverk. Det stöder inte anslutning av virtuella datorer eller molntjänster som inte distribueras till ett virtuellt nätverk.
* VNet-till-VNet kräver dynamiska routningsgateways. Azure-statiska routningsgateways stöds inte.
* Virtuell nätverksanslutning kan användas samtidigt med VPN på flera platser. Det finns högst 10 VPN-tunnlar för en VPN-gateway för virtuellt nätverk som ansluter till antingen andra virtuella nätverk eller lokala webbplatser.
* Adressutrymmen till virtuella nätverk och lokala nätverksplatser får inte överlappa varandra. Överlappande adressutrymmen gör att det går att skapa virtuella nätverk eller att netcfg-konfigurationsfiler överförs.
* Redundanta tunnlar mellan ett virtuellt nätverkspar stöds inte.
* Alla VPN-tunnlar för det virtuella nätverket, inklusive P2S VPN, delar den tillgängliga bandbredden för VPN-gatewayen och samma VPN-gateway-uptime-SLA i Azure.
* VNet-till-VNet-trafik färdas över Azure-stamnätet.

## <a name="next-steps"></a>Nästa steg
Verifiera dina anslutningar. Se [Verifiera en VPN Gateway-anslutning](vpn-gateway-verify-connection-resource-manager.md).
