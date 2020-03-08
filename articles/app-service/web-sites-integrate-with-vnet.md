---
title: Integrera app med Azure Virtual Network
description: Integrera appar i Azure App Service med virtuella Azure-nätverk.
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 02/27/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 89aa78e0d26598eacf436ca88cc6c5549f91d2fc
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78673213"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integrera din app med en Azure-Virtual Network
Det här dokumentet beskriver den Azure App Service funktionen för integrering av virtuella nätverk och hur du konfigurerar den med appar i [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714). Med [Azure Virtual Networks][VNETOverview] (virtuella nätverk) kan du placera många av dina Azure-resurser i ett dirigerbart nätverk som inte är Internet.  

Azure App Service har två varianter.

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

## <a name="enable-vnet-integration"></a>Aktivera VNet-integrering 

1. Gå till nätverks gränssnittet i App Service portalen. Under VNet-integrering väljer *du "Klicka här för att konfigurera"* . 

1. Välj **Lägg till VNet**.  

   ![Välj VNet-integrering][1]

1. Den nedrullningsbara listan innehåller alla Resource Manager-virtuella nätverk i din prenumeration i samma region och nedan finns en lista över alla Resource Manager-virtuella nätverk i alla andra regioner. Välj det virtuella nätverk som du vill integrera med.

   ![Välj VNet][2]

   * Om VNet finns i samma region skapar du antingen ett nytt undernät eller väljer ett tomt redan befintligt undernät. 

   * Om du vill välja ett VNet i en annan region måste du ha en Virtual Network Gateway etablerad med peka på plats aktive rad.

   * Om du vill integrera med ett klassiskt VNet i stället för att klicka på VNet-listruta väljer du **Klicka här för att ansluta till ett klassiskt VNet**. Välj önskat klassiskt VNet. Mål-VNet måste redan ha en Virtual Network Gateway etablerad med peka på plats aktive rad.

    ![Välj klassiskt VNet][3]
    
Under integrationen startas appen om.  När integrationen är klar visas information om det virtuella nätverk som du är integrerad med. 

När din app har integrerats med ditt VNet kommer den att använda samma DNS-server som ditt VNet har kon figurer ATS med, såvida det inte är Azure DNS Private Zones. Du kan för närvarande inte använda VNet-integrering med Azure DNS Private Zones.

## <a name="regional-vnet-integration"></a>Regional VNet-integrering

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

### <a name="how-regional-vnet-integration-works"></a>Så här fungerar regional VNet-integrering

Appar i App Service finns i arbets roller. De grundläggande och högre pris planerna är dedikerade värd planer där det inte finns några andra kund arbets belastningar som körs på samma arbetare. Regional VNet-integrering fungerar genom att montera virtuella gränssnitt med adresser i det delegerade under nätet. Eftersom från-adressen är i ditt VNet kan den komma åt de flesta saker i eller via ditt VNet precis som en virtuell dator i ditt VNet. Nätverks implementeringen skiljer sig från att köra en virtuell dator i ditt VNet och det är anledningen till att vissa nätverksfunktioner ännu inte är tillgängliga när du använder den här funktionen.

![Så här fungerar regional VNet-integrering][5]

När regional VNet-integrering är aktive rad kommer din app fortfarande att göra utgående samtal till Internet via samma kanaler som normalt. De utgående adresser som listas i app Properties-portalen är fortfarande de adresser som används av din app. Vilka ändringar av appen är, anrop till tjänstens slut punkts säkra tjänster eller RFC 1918-adresser går till ditt VNet. Om WEBSITE_VNET_ROUTE_ALL är inställt på 1 kan all utgående trafik skickas till ditt VNet. 

Funktionen stöder endast ett virtuellt gränssnitt per arbetare.  Ett virtuellt gränssnitt per arbetare innebär en regional VNet-integration per App Service plan. Alla appar i samma App Service plan kan använda samma VNet-integrering, men om du behöver en app för att ansluta till ytterligare ett VNet måste du skapa en annan App Service plan. Det virtuella gränssnittet som används är inte en resurs som kunder har direkt åtkomst till.

På grund av hur den här tekniken fungerar visas inte den trafik som används med VNet-integrering i Network Watcher-eller NSG flödes loggar.  

## <a name="gateway-required-vnet-integration"></a>Gateway krävs VNet-integrering

Gateway krävs VNet-integration stöder anslutning till ett VNet i en annan region eller till ett klassiskt VNet. Gateway krävs VNet-integrering: 

* Gör det möjligt för en app att ansluta till endast ett VNet i taget
* Gör det möjligt att integrera upp till fem virtuella nätverk i en App Service plan 
* Tillåter att samma VNet används av flera appar i en App Service plan utan att det totala antalet som kan användas av en App Service plan påverkas.  Om du har sex appar som använder samma VNet i samma App Service plan räknas det som 1 VNet som används. 
* Har stöd för 99,9% SLA på grund av SLA på gatewayen
* Gör att dina appar kan använda den DNS som VNet har kon figurer ATS med
* Kräver en Virtual Network Route-baserad gateway som kon figurer ATS med SSTP punkt-till-plats-VPN innan den kan anslutas till appen. 

Du kan inte använda Gateway krävs VNet-integrering:

* Med Linux-appar
* Med ett virtuellt nätverk som är anslutet till ExpressRoute 
* Få åtkomst till tjänst slut punkter säkrade resurser
* Med en gateway för samexistens som stöder både ExpressRoute och pekar på plats-/plats-till-plats-VPN

### <a name="set-up-a-gateway-in-your-vnet"></a>Konfigurera en gateway i ditt VNet ###

Så här skapar du en gateway:

1. [Skapa ett Gateway-undernät][creategatewaysubnet] i ditt VNet.  

1. [Skapa VPN-gatewayen][creategateway]. Välj en Route-baserad VPN-typ.

1. [Ange platsen för plats adresser][setp2saddresses]. Om gatewayen inte finns i Basic SKU måste IKEV2 vara inaktiverat i plats-till-plats-konfigurationen och SSTP måste väljas. Platsens adress utrymme måste vara i RFC 1918-Adressblock, 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16

Om du bara skapar en gateway för användning med App Service VNet-integrering behöver du inte ladda upp ett certifikat. Det kan ta 30 minuter att skapa en gateway. Du kommer inte att kunna integrera din app med ditt VNet förrän gatewayen har tillhandahållits. 

### <a name="how-gateway-required-vnet-integration-works"></a>Hur Gateway kräver att VNet-integrering fungerar

Gateway krävs VNet-integrering som byggts ovanpå punkt-till-plats-VPN-teknik. Peka på plats-VPN: er begränsar nätverks åtkomsten till bara den virtuella dator som är värd för appen. Appar är begränsade till att bara skicka trafik ut till Internet, via Hybridanslutningar eller genom VNet-integrering. När din app har kon figurer ATS med portalen för att använda Gateway krävs VNet-integrering, hanteras en komplex förhandling för din räkning för att skapa och tilldela certifikat på gatewayen och på program sidan. Slut resultatet är att de anställda som används som värd för dina appar kan ansluta direkt till den virtuella Nätverksgatewayen i det valda virtuella nätverket. 

![Hur Gateway kräver att VNet-integrering fungerar][6]

### <a name="accessing-on-premises-resources"></a>Åtkomst till lokala resurser

Appar kan komma åt lokala resurser genom att integrera med virtuella nätverk som har plats-till-plats-anslutningar. Om du använder gatewayen som krävs VNet-integrering måste du uppdatera dina lokala VPN gateway-vägar med punkt-till-plats-Adressblock. När plats-till-plats-VPN först konfigureras bör de skript som används för att konfigurera den Konfigurera vägar korrekt. Om du lägger till punkt-till-plats-adresser när du har skapat din plats-till-plats-VPN måste du uppdatera vägarna manuellt. Information om hur du gör det varierar per gateway och beskrivs inte här. Du kan inte ha BGP konfigurerat med en plats-till-plats-VPN-anslutning.

Det krävs ingen ytterligare konfiguration för den regionala VNet-integrerings funktionen för att komma ut på ditt VNet och lokalt. Du behöver bara ansluta ditt VNet till lokalt med ExpressRoute eller VPN för plats till plats. 

> [!NOTE]
> Den gateway som krävs för funktionen VNet-integrering integrerar inte en app med ett VNet som har en ExpressRoute-Gateway. Även om ExpressRoute-gatewayen har kon figurer ATS i [läget för samexistens][VPNERCoex] fungerar inte VNet-integreringen. Om du behöver åtkomst till resurser via en ExpressRoute-anslutning kan du använda funktionen regional VNet-integrering eller en [App Service-miljön][ASE]som körs i ditt VNet. 
> 
> 

### <a name="peering"></a>Peering

Om du använder peering med den regionala VNet-integrationen behöver du inte göra någon ytterligare konfiguration. 

Om du använder gatewayen som krävs VNet-integrering med peering måste du konfigurera ytterligare några objekt. Konfigurera peering så att den fungerar med din app:

1. Lägg till en peering-anslutning på det virtuella nätverk som din App ansluter till. När du lägger till peering-anslutningen aktiverar du **Tillåt åtkomst till virtuellt nätverk** och markerar **Tillåt vidarebefordrad trafik** och **Tillåt Gateway-överföring**.
1. Lägg till en peering-anslutning på det virtuella nätverk som peer-kopplas till det virtuella nätverk som du är ansluten till. När du lägger till peering-anslutningen på målets VNet aktiverar du **Tillåt åtkomst till virtuellt nätverk** och markerar **Tillåt vidarebefordrad trafik** och **Tillåt fjärrgatewayer**.
1. Gå till App Service plan > Networking > VNet integration UI i portalen.  Välj det VNet som appen ansluter till. Under avsnittet routning lägger du till adress intervallet för det virtuella nätverk som peer-kopplas med det virtuella nätverk som appen är ansluten till.  

## <a name="managing-vnet-integration"></a>Hantera VNet-integrering 

Anslutning och från koppling med ett VNet finns på en app-nivå. Åtgärder som kan påverka VNet-integration över flera appar finns på App Service plan nivå. Från appen > nätverk > VNet-integrering kan du få information om ditt VNet. Du kan se liknande information på ASP-nivå i ASP-> nätverk > VNet integrations Portal.

Den enda åtgärd du kan utföra i vyn app för din VNet-integrering är att koppla från din app från det virtuella nätverk som den är ansluten till. Om du vill koppla från din app från ett VNet väljer du **Koppla från**. Din app kommer att startas om när du kopplar från ett virtuellt nätverk. Om du kopplar från, ändras inte ditt VNet. Under nätet eller gatewayen tas inte bort. Om du sedan vill ta bort ditt VNet måste du först koppla från appen från VNet och ta bort resurserna i den, till exempel gatewayer. 

ASP-gränssnittet för VNet-integrering visar alla VNet-integreringar som används av apparna i ASP. Om du vill se information om varje VNet klickar du på det virtuella nätverk som du är intresse rad av. Det finns två åtgärder som du kan utföra här för gateway krävs VNet-integrering.

* **Synkronisera nätverk**. Den synkroniserade nätverks åtgärden gäller endast för den gateway-beroende funktionen för VNet-integrering. Att utföra en synkroniserad nätverks åtgärd garanterar att dina certifikat och nätverksinformation är synkroniserade. Om du lägger till eller ändrar DNS för ditt VNet måste du utföra en **synkroniserad nätverks** åtgärd. Den här åtgärden startar om alla appar som använder det här virtuella nätverket.
* **Lägg till vägar** Om du lägger till vägar kommer utgående trafik att bedrivas i ditt VNet. 

**Gateway krävs VNet-integrering routning** Vägarna som definieras i ditt VNet används för att dirigera trafik till ditt VNet från din app. Om du behöver skicka ytterligare utgående trafik till VNet kan du lägga till dessa adress block här. Den här funktionen fungerar bara med Gateway krävs VNet-integrering. Routningstabeller påverkar inte din app-trafik när du använder Gateway krävs VNet-integration på det sätt som de gör med regional VNet-integration.

**Gateway krävs VNet-integrerings certifikat** När gatewayen kräver att VNet-integrering är aktiverat, finns det ett nödvändigt utbyte av certifikat för att säkerställa anslutningens säkerhet. Tillsammans med certifikaten är DNS-konfigurationen, vägarna och andra liknande saker som beskriver nätverket.
Om certifikat eller nätverksinformation har ändrats måste du klicka på synkronisera nätverk. När du klickar på "synkronisera nätverk" orsakar du ett kort avbrott i anslutningen mellan appen och ditt VNet. När din app inte startas om kan det leda till att din webbplats inte fungerar korrekt. 

## <a name="pricing-details"></a>Prisinformation
Funktionen för regional VNet-integrering har ingen extra kostnad för användning utöver pris nivån för ASP.

Det finns tre relaterade kostnader för att använda gatewayen som krävs VNet-integrerings funktion:

* Pris nivå avgifter för ASP – dina appar måste vara i en standard-, Premium-eller PremiumV2 App Service-plan. Du kan se mer information om dessa kostnader här: [App Service prissättning][ASPricing]. 
* Kostnader för data överföring – det finns en avgift för utgående data, även om VNet finns i samma data Center. Dessa avgifter beskrivs i [dataöverföring pris information][DataPricing]. 
* VPN Gateway kostnader – det finns en kostnad för den VNet-gateway som krävs för punkt-till-plats-VPN. Informationen finns på sidan med [VPN gateway priser][VNETPricing] .

## <a name="troubleshooting"></a>Felsökning

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="automation"></a>Automation

Det finns CLI-stöd för regional VNet-integrering. [Installera Azure CLI][installCLI]för att få åtkomst till följande kommandon. 

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

För gateway krävs VNet-integrering, kan du integrera App Service med en Azure-Virtual Network med hjälp av PowerShell. Ett skript som är klart att köra finns i [ansluta en app i Azure App Service till en Azure-Virtual Network](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3).


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
