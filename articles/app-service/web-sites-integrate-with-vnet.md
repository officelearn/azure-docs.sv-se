---
title: Integrera app med Azure Virtual Network
description: Integrera app i Azure App Service med virtuella Azure-nätverk.
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 04/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 4866397af244ffb3c6aa9c7547b0a9413b10ccfd
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604865"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integrera din app med ett virtuellt Azure-nätverk

I den här artikeln beskrivs funktionen för VNet-integrering av Azure App Service och hur du konfigurerar den med appar i [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714). Med [Virtuella Nätverk (Azure Virtual Network)][VNETOverview] kan du placera många av dina Azure-resurser i ett nätverk som inte är internetrdigerbart.

Azure App Service har två varianter:

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

## <a name="enable-vnet-integration"></a>Aktivera VNet-integrering

1. Gå till **nätverksgränssnittet** i App Service-portalen. Under **VNet-integrering**väljer du **Klicka här för att konfigurera**.

1. Välj **Lägg till VNet**.

   ![Välj VNet-integrering][1]

1. Listrutan innehåller alla virtuella Azure Resource Manager-nätverk i din prenumeration i samma region. Under det finns en lista över resource manager virtuella nätverk i alla andra regioner. Välj det virtuella nätverk som du vill integrera med.

   ![Välj det virtuella nätverket][2]

   * Om det virtuella nätverket finns i samma region skapar du antingen ett nytt undernät eller väljer ett tomt befintligt undernät.
   * Om du vill välja ett virtuella nätverk i en annan region måste du ha etablerat en VNet-gateway med punkt till plats aktiverat.
   * Om du vill integrera med ett klassiskt virtuellt nätverk väljer du Klicka här för **att ansluta till ett klassiskt virtuellt nätverk**i stället för att välja listrutan **Virtuellt nätverk.** Välj önskat klassiskt virtuellt nätverk. Mål-virtuella nätverk måste redan ha en virtuell nätverksgateway etablerad med point-to-site aktiverat.

    ![Välj klassiskt virtuella nätverk][3]

Under integrationen startas appen om. När integrationen är klar ser du information om det virtuella nätverk som du är integrerad med.

## <a name="regional-vnet-integration"></a>Regional VNet-integrering

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

### <a name="how-regional-vnet-integration-works"></a>Så här fungerar regional VNet-integration

Appar i App Service finns på arbetarroller. Grundläggande och högre prisplaner är dedikerade värdplaner där det inte finns några andra kunders arbetsbelastningar som körs på samma arbetare. Regional VNet-integrering fungerar genom att montera virtuella gränssnitt med adresser i det delegerade undernätet. Eftersom från-adressen finns i ditt virtuella nätverk kan den komma åt det mesta i eller via ditt virtuella nätverk som en virtuell dator i ditt virtuella nätverk. Nätverksimplementeringen skiljer sig från att köra en virtuell dator i ditt virtuella nätverk. Det är därför vissa nätverksfunktioner ännu inte är tillgängliga för den här funktionen.

![Så här fungerar regional VNet-integration][5]

När regional VNet-integrering är aktiverad ringer appen utgående samtal till internet via samma kanaler som vanligt. De utgående adresser som visas i appegenskapernas portal är de adresser som fortfarande används av appen. Vilka ändringar för din app är anrop till tjänstslutpunktssäkra tjänster, eller RFC 1918-adresser går in i ditt virtuella nätverk. Om WEBSITE_VNET_ROUTE_ALL är inställt på 1 kan all utgående trafik skickas till ditt virtuella nätverk.

Funktionen stöder endast ett virtuellt gränssnitt per anställd. Ett virtuellt gränssnitt per anställd innebär en regional VNet-integration per apptjänstplan. Alla appar i samma App Service-plan kan använda samma VNet-integrering. Om du behöver en app för att ansluta till ytterligare ett virtuella nätverk måste du skapa en annan apptjänstplan. Det virtuella gränssnittet som används är inte en resurs som kunderna har direkt åtkomst till.

På grund av hur den här tekniken fungerar visas inte den trafik som används med VNet-integrering i Azure Network Watcher eller NSG-flödesloggar.

## <a name="gateway-required-vnet-integration"></a>Gateway-obligatorisk VNet-integrering

VNet-integrering som krävs för gateway stöder anslutning till ett virtuellt nätverk i en annan region eller till ett klassiskt virtuellt nätverk. Gateway-obligatorisk VNet-integrering:

* Gör det möjligt för en app att bara ansluta till ett virtuella nätverk åt gången.
* Gör att upp till fem virtuella nätverk kan integreras i en App Service-plan.
* Tillåter att samma virtuella nätverk används av flera appar i en App Service-plan utan att det totala antalet som kan användas av en App Service-plan. Om du har sex appar som använder samma virtuella nätverk i samma App Service-plan räknas det som ett virtuella nätverk som används.
* Stöder ett serviceavtal på 99,9 % på grund av serviceavtalet på gatewayen.
* Gör det möjligt för dina appar att använda den DNS som det virtuella nätverket är konfigurerat med.
* Kräver en ruttbaserad gateway för virtuellt nätverk som konfigurerats med en SSTP-punkt-till-plats-VPN innan den kan anslutas till en app.

Du kan inte använda gateway-obligatorisk VNet-integrering:

* Med Linux-appar.
* Med ett VNet kopplat till Azure ExpressRoute.
* Så här kommer du åt skyddade tjänstslutpunktsresurser.
* Med en onlineexistens gateway som stöder både ExpressRoute och point-to-site eller site-to-site VPN.

### <a name="set-up-a-gateway-in-your-azure-virtual-network"></a>Konfigurera en gateway i ditt virtuella Azure-nätverk ###

Så här skapar du en gateway:

1. [Skapa ett gateway-undernät][creategatewaysubnet] i ditt virtuella nätverk.  

1. [Skapa VPN-gatewayen][creategateway]. Välj en ruttbaserad VPN-typ.

1. [Ange adresserna från punkt till plats][setp2saddresses]. Om gatewayen inte finns i den grundläggande SKU:n måste IKEV2 inaktiveras i point-to-site-konfigurationen och SSTP måste väljas. Adressutrymmet för punkt till plats måste finnas i RFC 1918-adressblocken 10.0.0.0/8, 172.16.0.0/12 och 192.168.0.0/16.

Om du skapar gatewayen för användning med App Service VNet Integration behöver du inte ladda upp ett certifikat. Det kan ta 30 minuter att skapa gatewayen. Du kan inte integrera din app med ditt virtuella nätverk förrän gatewayen har etablerats.

### <a name="how-gateway-required-vnet-integration-works"></a>Så här fungerar gateway-obligatorisk VNet-integrering

Gateway-obligatorisk VNet-integrering bygger på vpn-teknik från punkt till plats. Vpn-nätverk på punkt till plats begränsar nätverksåtkomsten till den virtuella datorn som är värd för appen. Appar är begränsade för att skicka trafik ut till Internet endast via Hybrid-anslutningar eller via VNet-integrering. När appen konfigureras med portalen för att använda gateway-obligatorisk VNet-integrering hanteras en komplex förhandling för din räkning för att skapa och tilldela certifikat på gateway- och programsidan. Resultatet är att de arbetare som används för att vara värdar för dina appar kan ansluta direkt till den virtuella nätverksgatewayen i det valda virtuella nätverket.

![Så här fungerar gateway-obligatorisk VNet-integrering][6]

### <a name="access-on-premises-resources"></a>Åtkomst till lokala resurser

Appar kan komma åt lokala resurser genom att integrera med virtuella nätverk som har anslutningar från plats till plats. Om du använder gateway-obligatorisk VNet-integrering uppdaterar du dina lokala VPN-gatewayvägar med dina punkt-till-plats-adressblock. När plats-till-plats-VPN först konfigureras bör skripten som används för att konfigurera det ställa in vägar korrekt. Om du lägger till point-to-site-adresserna när du har skapat vpn från plats till plats måste du uppdatera rutterna manuellt. Information om hur du gör som varierar per gateway och beskrivs inte här. Du kan inte ha BGP konfigurerat med en VPN-anslutning från plats till plats.

Ingen ytterligare konfiguration krävs för att den regionala VNet-integrationsfunktionen ska nå via ditt virtuella nätverk till lokala resurser. Du behöver bara ansluta ditt virtuella nätverk till lokala resurser med hjälp av ExpressRoute eller en plats-till-plats-VPN.

> [!NOTE]
> Funktionen för gateway-obligatorisk VNet-integrering integrerar inte en app med ett virtuella nätverk som har en ExpressRoute-gateway. Även om ExpressRoute-gatewayen är konfigurerad i [samexistensläge][VPNERCoex]fungerar inte VNet-integreringen. Om du behöver komma åt resurser via en ExpressRoute-anslutning använder du den regionala VNet-integrationsfunktionen eller en [App Service-miljö][ASE]som körs i ditt virtuella nätverk.
> 
> 

### <a name="peering"></a>Peering

Om du använder peering med den regionala VNet-integreringen behöver du inte göra någon ytterligare konfiguration.

Om du använder gateway-obligatorisk VNet-integrering med peering måste du konfigurera några ytterligare objekt. Så här konfigurerar du peering så att den fungerar med appen:

1. Lägg till en peering-anslutning på det virtuella nätverk som appen ansluter till. När du lägger till peering-anslutningen aktiverar du **Tillåt virtuell nätverksåtkomst** och väljer **Tillåt vidarebefordrad trafik** och Tillåt **gatewaytransitering**.
1. Lägg till en peering-anslutning på det virtuella nätverket som peeras till det virtuella nätverk som du är ansluten till. När du lägger till peering-anslutningen på mål-VNet aktiverar du **Tillåt virtuell nätverksåtkomst** och väljer **Tillåt vidarebefordrad trafik** och Tillåt **fjärrgateways**.
1. Gå till **apptjänstens nätverksgränssnitt** > för**nätverk** **i** > portalen. Välj det virtuella nätverk som appen ansluter till. Under routningsavsnittet lägger du till adressintervallet för det virtuella nätverk som är peered med det virtuella nätverk som appen är ansluten till.

## <a name="manage-vnet-integration"></a>Hantera VNet-integrering

Att ansluta och koppla från med ett virtuella nätverk är på appnivå. Åtgärder som kan påverka VNet-integrering över flera appar finns på apptjänstplansnivå. Från app > > **Nätverks-VNet Integration** portal, kan du få information om ditt virtuella nätverk. **Networking** Du kan se liknande information på apptjänstplansnivå i **apptjänsttjänstens** > **nätverksnätverksportal.** **Networking** > 

Den enda åtgärd du kan ta i appvyn för din VNet-integreringsinstans är att koppla från appen från det virtuella nätverk som den är ansluten till. Om du vill koppla från appen från ett virtuella nätverk väljer du **Koppla från**. Appen startas om när du kopplar från ett virtuella nätverk. Om du kopplar från det virtuella nätverket ändras inte. Undernätet eller gatewayen tas inte bort. Om du sedan vill ta bort ditt virtuella nätverk kopplar du först från appen från det virtuella nätverket och tar bort resurserna i det, till exempel gateways.

Användargränssnittet för App Service-tjänst VNet-integrering visar alla VNet-integreringar som används av apparna i apptjänstplanen. Om du vill se information på varje virtuella nätverk väljer du det virtuella nätverk du är intresserad av. Det finns två åtgärder som du kan utföra här för gateway-obligatorisk VNet-integrering:

* **Synkroniseringsnätverk:** Synkroniseringsnätverksåtgärden används endast för den gatewayberoende VNet-integrationsfunktionen. Genom att utföra en synkroniseringsnätverksåtgärd säkerställer du att certifikaten och nätverksinformationen är synkroniserade. Om du lägger till eller ändrar DNS för ditt virtuella nätverk utför du en synkroniseringsnätverksåtgärd. Den här åtgärden startar om alla appar som använder det här virtuella nätverket.
* **Lägg till rutter:** Lägga till rutter driver utgående trafik till ditt virtuella nätverk.

### <a name="gateway-required-vnet-integration-routing"></a>Gateway-obligatorisk VNet-integreringsroutning
De vägar som definieras i ditt virtuella nätverk används för att dirigera trafik till ditt virtuella nätverk från din app. Om du vill skicka ytterligare utgående trafik till det virtuella nätverket lägger du till dessa adressblock här. Den här funktionen fungerar bara med gateway-obligatorisk VNet-integrering. Flödestabeller påverkar inte apptrafiken när du använder gateway-obligatorisk VNet-integrering på det sätt som de gör med regional VNet-integrering.

### <a name="gateway-required-vnet-integration-certificates"></a>VNet-integrationscertifikat som krävs
När gateway-obligatorisk VNet-integrering är aktiverat krävs ett obligatoriskt utbyte av certifikat för att säkerställa anslutningens säkerhet. Tillsammans med certifikaten är DNS-konfiguration, vägar och andra liknande saker som beskriver nätverket.

Om certifikat eller nätverksinformation ändras väljer du **Synkronisera nätverk**. När du väljer **Synkronisera nätverk**orsakar du ett kort avbrott i anslutningen mellan appen och ditt virtuella nätverk. Appen startas inte om, men förlusten av anslutning kan leda till att webbplatsen inte fungerar som den ska.

## <a name="pricing-details"></a>Prisinformation
Den regionala VNet-integrationsfunktionen har ingen extra kostnad för användning utöver prisnivåavgifterna för App Service-planen.

Tre avgifter är relaterade till användningen av den gateway-obligatoriska VNet Integration-funktionen:

* **Priser på apptjänstabonnemang**: Dina appar måste finnas i en standard-, Premium- eller PremiumV2-apptjänstplan. Mer information om dessa kostnader finns i [Priser för App Service][ASPricing].
* **Kostnader för dataöverföring**: Det finns en avgift för datasegress, även om det virtuella nätverket finns i samma datacenter. Dessa avgifter beskrivs i [uppgifter om överföring av data][DataPricing].
* **VPN-gateway kostnader:** Det finns en kostnad för den virtuella nätverksgatewayen som krävs för punkt-till-plats-VPN. Mer information finns i [VPN-gatewayprissättning][VNETPricing].

## <a name="troubleshooting"></a>Felsökning

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="automation"></a>Automation

CLI-stöd är tillgängligt för regional VNet Integration. Installera [Azure CLI][installCLI]för att komma åt följande kommandon .

        az webapp vnet-integration --help

        Group
            az webapp vnet-integration : Methods that list, add, and remove virtual network integrations
            from a webapp.
                This command group is in preview. It may be changed/removed in a future release.
        Commands:
            add    : Add a regional virtual network integration to a webapp.
            list   : List the virtual network integrations on a webapp.
            remove : Remove a regional virtual network integration from webapp.

        az appservice vnet-integration --help

        Group
            az appservice vnet-integration : A method that lists the virtual network integrations used in an
            appservice plan.
                This command group is in preview. It may be changed/removed in a future release.
        Commands:
            list : List the virtual network integrations used in an appservice plan.

För gateway-obligatorisk VNet-integrering kan du integrera App Service med ett virtuellt Azure-nätverk med hjälp av PowerShell. Ett färdigt skript finns [i Ansluta en app i Azure App Service till ett virtuellt Azure-nätverk](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3).


<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-app.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-addvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-classic.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-regionalworks.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-gwworks.png


<!--Links-->
[VNETOverview]: https://azure.microsoft.com/documentation/articles/virtual-networks-overview/ 
[AzurePortal]: https://portal.azure.com/
[ASPricing]: https://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: https://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: https://azure.microsoft.com/documentation/articles/vpn-gateway-howto-point-to-site-rm-ps/
[ILBASE]: environment/create-ilb-ase.md
[V2VNETPortal]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md
[VPNERCoex]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[ASE]: environment/intro.md
[creategatewaysubnet]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw
[creategateway]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#creategw
[setp2saddresses]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#addresspool
[VNETRouteTables]: https://docs.microsoft.com/azure/virtual-network/manage-route-table/
[installCLI]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest/
[privateendpoints]: networking/private-endpoint.md
