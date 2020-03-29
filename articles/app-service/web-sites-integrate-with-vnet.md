---
title: Integrera app med Azure Virtual Network
description: Integrera appar i Azure App Service med Virtuella Azure-nätverk.
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 02/27/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 89aa78e0d26598eacf436ca88cc6c5549f91d2fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673213"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integrera en app med Azure Virtual Network
I det här dokumentet beskrivs azure app-tjänstens virtuella nätverksintegreringsfunktion och hur du konfigurerar den med appar i [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714). [Med Virtuella nätverk (Azure Virtual Networks)][VNETOverview] kan du placera många av dina Azure-resurser i ett icke-internetrdigerbart nätverk.  

Azure App-tjänsten har två varianter.

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

## <a name="enable-vnet-integration"></a>Aktivera VNet-integrering 

1. Gå till nätverksgränssnittet i App Service-portalen. Under VNet-integrering väljer du *"Klicka här för att konfigurera"*. 

1. Välj **Lägg till VNet**.  

   ![Välj VNet-integrering][1]

1. Listrutan innehåller alla virtuella nätverk för Resurshanteraren i din prenumeration i samma region och nedanför det är en lista över alla virtuella resurshanteraren-nätverk i alla andra regioner. Välj det virtuella nätverk som du vill integrera med.

   ![Välj det virtuella nätverket][2]

   * Om det virtuella nätverket finns i samma region skapar du antingen ett nytt undernät eller väljer ett tomt befintligt undernät. 

   * Om du vill välja ett virtuellt nätverk i en annan region måste du ha en virtuell nätverksgateway etablerad med punkt till plats aktiverat.

   * Om du vill integrera med ett klassiskt virtuella nätverk väljer du Klicka här för **att ansluta till ett klassiskt virtuella nätverk**i stället för att klicka på vnet-rullgardinsmenyn. Välj önskat klassiskt virtuella nätverk. Mål-virtuella nätverk måste redan ha en virtuell nätverksgateway etablerad med punkt till plats aktiverat.

    ![Välj klassiskt virtuella nätverk][3]
    
Under integrationen startas appen om.  När integrationen är klar ser du information om det virtuella nätverk som du är integrerad med. 

När din app är integrerad med ditt virtuella nätverk använder den samma DNS-server som ditt virtuella nätverk är konfigurerat med, såvida det inte är Azure DNS Private Zones. Du kan för närvarande inte använda VNet-integrering med Azure DNS Private Zones.

## <a name="regional-vnet-integration"></a>Regional VNet-integrering

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

### <a name="how-regional-vnet-integration-works"></a>Så här fungerar regional VNet-integrering

Appar i App-tjänsten finns på arbetsroller. Grundläggande och högre prisplaner är dedikerade värdplaner där det inte finns några andra kunders arbetsbelastningar som körs på samma arbetare. Regional VNet-integrering fungerar genom att montera virtuella gränssnitt med adresser i det delegerade undernätet. Eftersom från-adressen finns i ditt virtuella nätverk kan den komma åt det mesta i eller via ditt virtuella nätverk precis som en virtuell dator i ditt virtuella nätverk. Nätverksimplementeringen skiljer sig från att köra en virtuell dator i ditt virtuella nätverk och det är därför vissa nätverksfunktioner ännu inte är tillgängliga när du använder den här funktionen.

![Så här fungerar regional VNet-integration][5]

När regional VNet-integrering är aktiverad kommer appen fortfarande att ringa utgående samtal till internet via samma kanaler som vanligt. De utgående adresser som visas i appegenskapernas portal är fortfarande de adresser som används av appen. Vilka ändringar för din app är, samtal till tjänstslutpunktssäkra tjänster eller RFC 1918-adresser hamnar i ditt virtuella nätverk. Om WEBSITE_VNET_ROUTE_ALL är inställt på 1 kan all utgående trafik skickas till ditt virtuella nätverk. 

Funktionen stöder bara ett virtuellt gränssnitt per anställd.  Ett virtuellt gränssnitt per anställd innebär en regional VNet-integration per apptjänstplan. Alla appar i samma App Service-plan kan använda samma VNet-integrering, men om du behöver en app för att ansluta till ytterligare ett virtuella nätverk måste du skapa en annan App Service-plan. Det virtuella gränssnittet som används är inte en resurs som kunderna har direkt åtkomst till.

På grund av hur den här tekniken fungerar visas inte den trafik som används med VNet-integrering i Nätverksbevakare eller NSG-flödesloggar.  

## <a name="gateway-required-vnet-integration"></a>Gateway krävs VNet Integration

Gateway krävs VNet Integration stöder anslutning till ett VNet i en annan region, eller till en klassisk VNet. Gateway krävs VNet Integration: 

* Gör det möjligt för en app att ansluta till endast 1 VNet åt gången
* Gör att upp till fem virtuella nätverk kan integreras i en appserviceplan 
* Gör att samma virtuella nätverk kan användas av flera appar i en apptjänstplan utan att påverka det totala antalet som kan användas av en App Service-plan.  Om du har sex appar som använder samma virtuella nätverk i samma App Service-plan räknas det som 1 VNet som används. 
* Stöder ett serviceavtal på 99,9 % på grund av serviceavtalet på gatewayen
* Gör det möjligt för dina appar att använda den DNS som det virtuella nätverket är konfigurerat med
* Kräver en ruttbaserad gateway för virtuellt nätverk som konfigurerats med SSTP point-to-site VPN innan den kan anslutas till appen. 

Du kan inte använda gateway krävs VNet Integration:

* Med Linux-appar
* Med ett VNet kopplat till ExpressRoute 
* Så här kommer du åt skyddade resurser för tjänstslutpunkter
* Med en samexistensgateway som stöder både ExpressRoute och pekar på plats/plats till plats VPN

### <a name="set-up-a-gateway-in-your-vnet"></a>Konfigurera en gateway i ditt virtuella nätverk ###

Så här skapar du en gateway:

1. [Skapa ett gateway-undernät][creategatewaysubnet] i ditt virtuella nätverk.  

1. [Skapa VPN-gatewayen][creategateway]. Välj en ruttbaserad VPN-typ.

1. [Ange punkten till platsadresser][setp2saddresses]. Om gatewayen inte finns i den grundläggande SKU:n måste IKEV2 inaktiveras i punkt till platskonfiguration och SSTP måste väljas. Adressutrymmet för platsen måste finnas i RFC 1918-adressblocken, 10.0.0/8, 172.16.0.0/12, 192.168.0.0/16

Om du bara skapar gatewayen för användning med App Service VNet Integration, behöver du inte ladda upp ett certifikat. Det kan ta 30 minuter att skapa gatewayen. Du kan inte integrera din app med ditt virtuella nätverk förrän gatewayen har etablerats. 

### <a name="how-gateway-required-vnet-integration-works"></a>Så här fungerar gateway krävs VNet Integration

Gateway krävs VNet Integration byggd på toppen av punkt till plats VPN-teknik. Peka på plats VPN begränsar nätverksåtkomst till bara den virtuella datorn som är värd för appen. Appar är begränsade till att endast skicka trafik ut till Internet, via Hybridanslutningar eller via VNet-integrering. När appen konfigureras med portalen för att använda gateway krävs VNet Integration, en komplex förhandling hanteras för din räkning för att skapa och tilldela certifikat på gatewayen och programsidan. Slutresultatet är att de arbetare som används för att vara värd för dina appar kan ansluta direkt till den virtuella nätverksgatewayen i det valda virtuella nätverket. 

![Så här fungerar gateway krävs VNet Integration][6]

### <a name="accessing-on-premises-resources"></a>Komma åt lokala resurser

Appar kan komma åt lokala resurser genom att integrera med virtuella nätverk som har anslutningar från plats till plats. Om du använder den gateway som krävs VNet-integrering måste du uppdatera dina lokala VPN-gatewayvägar med dina punkt-till-plats-adressblock. När plats-till-plats-VPN först konfigureras bör skripten som används för att konfigurera det ställa in vägar korrekt. Om du lägger till point-to-site-adresserna när du har skapat vpn från plats till plats måste du uppdatera rutterna manuellt. Information om hur du gör som varierar per gateway och beskrivs inte här. Du kan inte ha BGP konfigurerat med en plats-till-plats-VPN-anslutning.

Det krävs ingen ytterligare konfiguration för att den regionala VNet-integrationsfunktionen ska nå via ditt virtuella nätverk och lokalt. Du behöver bara ansluta ditt virtuella nätverk till lokalt med ExpressRoute eller en plats-till-plats VPN. 

> [!NOTE]
> Den startmotor som krävs för VNet-integreringsfunktionen integrerar inte en app med ett virtuella nätverk som har en ExpressRoute Gateway. Även om ExpressRoute Gateway är konfigurerad i [samexistensläge][VPNERCoex] fungerar inte VNet-integreringen. Om du behöver komma åt resurser via en ExpressRoute-anslutning kan du använda den regionala VNet-integrationsfunktionen eller en [apptjänstmiljö][ASE]som körs i ditt virtuella nätverk. 
> 
> 

### <a name="peering"></a>Peering

Om du använder peering med den regionala VNet-integreringen behöver du inte göra någon ytterligare konfiguration. 

Om du använder den gateway som krävs VNet Integration med peering, måste du konfigurera några ytterligare objekt. Så här konfigurerar du peering så att den fungerar med appen:

1. Lägg till en peering-anslutning på det virtuella nätverk som appen ansluter till. När du lägger till peering-anslutningen aktiverar du **Tillåt virtuell nätverksåtkomst** och markerar **Tillåt vidarebefordrad trafik** och Tillåt **gatewaytransitering**.
1. Lägg till en peering-anslutning på det virtuella nätverket som peeras till det virtuella nätverk som du är ansluten till. När du lägger till peering-anslutningen på mål-virtuella nätverk aktiverar du **Tillåt virtuell nätverksåtkomst** och markerar **Tillåt vidarebefordrad trafik** och Tillåt **fjärrgateways**.
1. Gå till apptjänstplanen > nätverksgränssnittet > VNet-integrationsgränssnittet i portalen.  Välj det virtuella nätverk som appen ansluter till. Under routningsavsnittet lägger du till adressintervallet för det virtuella nätverk som är peer-med det virtuella nätverk som appen är ansluten till.  

## <a name="managing-vnet-integration"></a>Hantera VNet-integrering 

Att ansluta och koppla från med ett virtuella nätverk är på appnivå. Åtgärder som kan påverka VNet-integreringen mellan flera appar finns på apptjänstplansnivå. Från appen > Networking > VNet Integration portal, kan du få information om ditt virtuella nätverk. Du kan se liknande information på ASP-nivå i ASP-> Networking > VNet Integration portal.

Den enda åtgärd du kan ta i appvyn för din VNet-integrering är att koppla från din app från det virtuella nätverk som den för närvarande är ansluten till. Om du vill koppla från appen från ett virtuella nätverk väljer du **Koppla från**. Appen startas om när du kopplar från ett virtuella nätverk. Om du kopplar från det virtuella nätverket ändras inte. Undernätet eller gatewayen tas inte bort. Om du sedan vill ta bort ditt virtuella nätverk måste du först koppla från appen från det virtuella nätverket och ta bort resurserna i det, till exempel gateways. 

ASP VNet Integration UI visar dig alla VNet-integreringar som används av apparna i din ASP. För att se information på varje virtuella nätverk, klicka på det virtuella nätverk du är intresserad av. Det finns två åtgärder som du kan utföra här för gateway krävs VNet Integration.

* **Synkronisera nätverk**. Synkroniseringsnätverksåtgärden är endast för den gatewayberoende VNet-integrationsfunktionen. Genom att utföra en synkroniseringsnätverksåtgärd säkerställer du att certifikaten och nätverksinformationen är synkroniserade. Om du lägger till eller ändrar DNS för ditt virtuella nätverk måste du utföra en **synkroniseringsnätverksåtgärd.** Den här åtgärden startar om alla appar som använder det här virtuella nätverket.
* **Lägg till rutter** Om du lägger till rutter får du utgående trafik i ditt virtuella nätverk. 

**Gateway krävs VNet Integration Routing** De vägar som definieras i ditt virtuella nätverk används för att dirigera trafik till ditt virtuella nätverk från din app. Om du behöver skicka ytterligare utgående trafik till det virtuella nätverket kan du lägga till dessa adressblock här. Den här funktionen fungerar bara med gateway krävs VNet Integration. Flödestabeller påverkar inte apptrafiken när du använder gateway krävs VNet-integrering på det sätt som de gör med regional VNet-integrering.

**VNet-integrationscertifikat för gateway krävs** När gatewayen krävs VNet Integration aktiverat, det finns ett obligatoriskt utbyte av certifikat för att säkerställa säkerheten för anslutningen. Tillsammans med certifikaten är DNS-konfiguration, vägar och andra liknande saker som beskriver nätverket.
Om certifikat eller nätverksinformation ändras måste du klicka på "Synkronisera nätverk". När du klickar på "Synkronisera nätverk" orsakar du ett kort avbrott i anslutningen mellan appen och ditt virtuella nätverk. Appen startas inte om, men förlusten av anslutning kan leda till att webbplatsen inte fungerar som den ska. 

## <a name="pricing-details"></a>Prisinformation
Den regionala VNet-integrationsfunktionen har ingen extra kostnad för användning utöver ASP-prisnivåavgifterna.

Det finns tre relaterade avgifter för användningen av gateway krävs VNet Integration funktionen:

* ASP-prisnivåavgifter – Dina appar måste finnas i en standard-, Premium- eller PremiumV2-apptjänstplan. Du kan se mer information om dessa kostnader här: [App Service Prissättning][ASPricing]. 
* Kostnader för dataöverföring - Det finns en avgift för datafrigång, även om det virtuella nätverket finns i samma datacenter. Dessa avgifter beskrivs i [information om dataöverföringspriser][DataPricing]. 
* VPN Gateway kostnader - Det finns en kostnad för VNet gateway som krävs för punkt-till-plats VPN. Informationen finns på sidan [VPN Gateway Pricing.][VNETPricing]

## <a name="troubleshooting"></a>Felsökning

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="automation"></a>Automation

Det finns CLI-stöd för regional VNet Integration. Installera [Azure CLI][installCLI]för att komma åt följande kommandon . 

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

För gateway krävs VNet Integration, kan du integrera App Service med en Azure Virtual Network med PowerShell. Ett färdigt skript finns [i Ansluta en app i Azure App Service till ett Virtuellt Azure-nätverk](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3).


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
