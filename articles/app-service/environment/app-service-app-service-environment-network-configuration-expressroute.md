---
title: Information om nätverkskonfiguration för Azure ExpressRoute - App Service
description: Information om nätverkskonfiguration för App Service Environment för PowerApps i virtuella nätverk är anslutna till en Azure ExpressRoute-krets.
services: app-service
documentationcenter: ''
author: stefsch
manager: nirma
editor: ''
ms.assetid: 34b49178-2595-4d32-9b41-110c96dde6bf
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: a9af97bcd85833a140d6c668fe4c757c85d7447a
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2018
ms.locfileid: "53337180"
---
# <a name="network-configuration-details-for-app-service-environment-for-powerapps-with-azure-expressroute"></a>Information om nätverkskonfiguration för App Service Environment för PowerApps med Azure ExpressRoute

Kunder kan ansluta en [Azure ExpressRoute] [ ExpressRoute] kretsen till sina virtuella nätverksinfrastruktur kan utöka sina lokala nätverk till Azure. App Service Environment skapas i ett undernät för den [virtuellt nätverk] [ virtualnetwork] infrastruktur. Appar som körs på App Service Environment upprätta säkra anslutningar till backend-resurser som är tillgängliga endast via ExpressRoute-anslutning.  

App Service-miljö kan skapas i dessa scenarier:
- Azure Resource Manager-nätverk.
- Klassisk distribution modellen virtuella nätverk.
- Virtuella nätverk som använder offentliga adressintervall eller RFC1918-adressutrymmen (det vill säga privata adresser). 

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="required-network-connectivity"></a>Krävs nätverksanslutning

App Service Environment har anslutningskrav som ursprungligen inte kan vara uppfyllda i ett virtuellt nätverk som är anslutet till ExpressRoute.

App Service-miljö kräver följande nätverk anslutningsinställningarna ska fungera korrekt:

* Utgående nätverksanslutning till Azure Storage-slutpunkter i hela världen på både port 80 och port 443. Dessa slutpunkter finns i samma region som App Service-miljö och andra Azure-regioner. Azure Storage-slutpunkter lösa under följande DNS-domäner: table.core.windows.net, blob.core.windows.net, queue.core.windows.net och file.core.windows.net.  

* Utgående nätverksanslutning till Azure Files-tjänsten på port 445.

* Utgående nätverksanslutning till Azure SQL Database-slutpunkter som finns i samma region som App Service Environment. SQL Database-slutpunkter lösa under database.windows.net domänen, vilket kräver öppen åtkomst till portarna 1433, 11000 11999 och 14000 14999. Mer information om SQL Database V12 portanvändning finns [portar utöver 1433 för ADO.NET 4.5](../../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md).

* Utgående nätverksanslutning till Azure-Hanteringsplanet-slutpunkter (klassiska distributionsmodellen och Azure Resource Manager-slutpunkter). Anslutning till dessa slutpunkter innehåller management.core.windows.net och management.azure.com domäner. 

* Utgående nätverksanslutning till ocsp.msocsp.com och mscrl.microsoft.com crl.microsoft.com domäner. Anslutningen till dessa domäner krävs för att stödja SSL-funktioner.

* DNS-konfiguration för det virtuella nätverket måste kunna matcha alla slutpunkter och domäner som nämns i den här artikeln. Skapar App Service Environment misslyckas om slutpunkterna inte kan matchas. Alla befintliga App Service Environment markeras som felaktig.

* Utgående åtkomst på port 53 krävs för kommunikation med DNS-servrar.

* Om en anpassad DNS-server finns på den andra änden av en VPN-gateway, måste DNS-server kunna nås från det undernät som innehåller App Service Environment. 

* Utgående nätverkssökvägen kan inte skickas genom interna företagets proxyservrar och går inte att tvinga tunneltrafik lokalt. De här åtgärderna kan du ändra den effektiva NAT-adressen för utgående nätverkstrafik från App Service Environment. Ändringar till NAT-adressen för App Service Environment utgående nätverkstrafik orsaka anslutningsfel till många slutpunkter. Det går inte att skapa en App Service-miljö. Alla befintliga App Service Environment markeras som felaktig.

* Inkommande åtkomst till nödvändiga portar för App Service Environment måste tillåtas. Mer information finns i [Kontrollera inkommande trafik till App Service Environment][requiredports].

Kontrollera att en giltig DNS-infrastruktur konfigurerad och underhållen för det virtuella nätverket för att uppfylla kraven för DNS. Om DNS-konfigurationen har ändrats efter App Service Environment har skapats, kan utvecklare tvinga App Service Environment att den använder den nya DNS-konfigurationen. Du kan utlösa en löpande miljö omstart med hjälp av den **starta om** ikonen under App Service Environment management i [Azure-portalen] [NewPortal]. Omstarten gör miljön att den använder den nya DNS-konfigurationen.

För att uppfylla kraven för inkommande åtkomst, konfigurera en [nätverkssäkerhetsgrupp (NSG)][NetworkSecurityGroups] på undernätet för App Service Environment. NSG: N tillåter den nödvändiga åtkomsten [Kontrollera inkommande trafik till App Service Environment][requiredports].

## <a name="outbound-network-connectivity"></a>Utgående nätverksanslutning

Som standard annonserar en nyligen skapade ExpressRoute-krets en standardväg som tillåter utgående internet-anslutning. App Service-miljö kan använda den här konfigurationen för att ansluta till andra Azure-slutpunkter.

En vanlig kund-konfiguration är att definiera egna standardväg (0.0.0.0/0), vilket tvingar utgående Internettrafik till flow lokalt. Det här flödet i nätverkstrafiken delar utan undantag App Service Environment. Den utgående trafiken är antingen blockerade lokalt eller NAT skulle med ett okänt uppsättning adresser som inte längre att fungera med olika Azure-slutpunkter.

Lösningen är att definiera en (eller mer) användardefinierade vägar (Udr) i det undernät som innehåller App Service Environment. En UDR definierar undernät-specifika vägar som respekteras i stället för standardväg.

Använd om möjligt följande konfiguration:

* ExpressRoute-konfigurationen annonserar 0.0.0.0/0. Som standard tunnlar configuration tvinga all utgående trafik lokalt.
* Den användardefinierade vägen som tillämpas på undernät som innehåller App Service Environment definierar 0.0.0.0/0 med ett nexthop-typ för internet. Ett exempel på den här konfigurationen beskrivs senare i den här artikeln.

Den kombinerade effekten av den här konfigurationen är att på undernätsnivå UDR har företräde framför ExpressRoute Tvingad tunneltrafik. Utgående Internetåtkomst från App Service Environment är korrekt.

> [!IMPORTANT]
> Vägarna som definieras i en UDR måste vara tillräckligt specifika för att få företräde framför eventuella vägar som annonseras av ExpressRoute-konfigurationen. I exemplet som beskrivs i nästa avsnitt används det breda adressintervallet 0.0.0.0/0. Det här intervallet åsidosättas av misstag av vägannonseringar som använder mer specifika adressintervall.
> 
> App Service Environment stöds inte med ExpressRoute-konfigurationer som korsannonserar vägar från den offentliga peering-sökvägen till den privata peering-sökvägen. ExpressRoute-konfigurationer som har offentlig peering har konfigurerats för att få vägannonseringar från Microsoft för ett stort antal Microsoft Azure IP-adressintervall. Om dessa adressintervallen korsannonseras på den privata peering-sökvägen, dirigeras alla utgående nätverkspaket från undernätet i App Service Environment tvingande till kundens lokala nätverksinfrastruktur. Det här nätverksflödet stöds inte för närvarande med App Service Environment. En lösning är att stoppa korsannonsering vägar från den offentliga peering-sökvägen till den privata peering-sökvägen.
> 
> 

Bakgrundsinformation om användardefinierade vägar finns i [trafikdirigering i virtuella nätverk][UDROverview].  

Information om hur du skapar och konfigurerar användardefinierade vägar finns i [dirigera nätverkstrafik med en routningstabell med hjälp av PowerShell] [UDRHowTo].

## <a name="udr-configuration"></a>Konfigurationen för UDR

Det här avsnittet visas en exempelkonfiguration UDR för App Service Environment.

### <a name="prerequisites"></a>Förutsättningar

* Installera Azure PowerShell från [nedladdningsbara filer för Azure page] [AzureDownloads]. Välj en nedladdning med ett datum i juni 2015 eller senare. Under **kommandoradsverktyg** > **Windows PowerShell**väljer **installera** att installera de senaste PowerShell-cmdletarna.

* Skapa ett unikt undernät för exklusiv användning av App Service Environment. Unikt undernät säkerställer att udr: er som tillämpas på undernät öppna utgående trafiken för App Service Environment endast.

> [!IMPORTANT]
> Distribuera App Service Environment endast när du har slutfört konfigurationen. Stegen Kontrollera utgående nätverksanslutning är tillgängliga innan du försöker distribuera App Service Environment.

### <a name="step-1-create-a-route-table"></a>Steg 1: Skapa en routningstabell

Skapa en routningstabell med namnet **DirectInternetRouteTable** i Azure för västra USA-regionen, som visas i det här kodfragmentet:

`New-AzureRouteTable -Name 'DirectInternetRouteTable' -Location uswest`

### <a name="step-2-create-routes-in-the-table"></a>Steg 2: Skapa vägar i tabellen

Lägg till vägar i routningstabellen för att aktivera utgående Internetåtkomst.  

Konfigurera utgående åtkomst till internet. Definiera en väg för 0.0.0.0/0 som visas i det här kodfragmentet:

`Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 0' -AddressPrefix 0.0.0.0/0 -NextHopType Internet`

0.0.0.0/0 är en bred adressintervall. Intervallet åsidosätts av adressintervallen som annonseras av ExpressRoute och som är mer specifika. En UDR med en väg med 0.0.0.0/0 bör användas tillsammans med en ExpressRoute-konfiguration som annonserar endast 0.0.0.0/0. 

Alternativt kan du hämta en aktuella, heltäckande lista över CIDR-intervall som används av Azure. XML-filen för alla Azure-IP-adressintervall kan hämtas från [Microsoft Download Center] [DownloadCenterAddressRanges].  

> [!NOTE]
>
> Azure IP-adressintervall ändras med tiden. Användardefinierade vägar måste periodiska manuella uppdateringar för att synkronisera.
>
> En enda UDR har en standard övre gräns på 100 vägar. Du måste ”summarize” Azure IP-adressintervall ska rymmas inom gränsen på 100-route. UDR-användardefinierade vägar måste vara mer specifik än vägar som annonseras av ExpressRoute-anslutningen.
> 

### <a name="step-3-associate-the-table-to-the-subnet"></a>Steg 3: Koppla tabellen till undernätet

Associera routningstabellen för undernätet där du planerar att distribuera App Service Environment. Det här kommandot associerar den **DirectInternetRouteTable** -tabellen till den **ASESubnet** undernät som innehåller App Service Environment.

`Set-AzureSubnetRouteTable -VirtualNetworkName 'YourVirtualNetworkNameHere' -SubnetName 'ASESubnet' -RouteTableName 'DirectInternetRouteTable'`

### <a name="step-4-test-and-confirm-the-route"></a>Steg 4: Testa och kontrollera flödet

När routningstabellen är bunden till undernätet, testa och bekräfta vägen.

Distribuera en virtuell dator i undernätet och bekräfta dessa villkor:

* Utgående trafik till Azure och icke-Azure-slutpunkter som beskrivs i den här artikeln har **inte** flödar nedåt ExpressRoute-kretsen. Om utgående trafik från undernätet har tvingande tunneltrafik lokalt, skapar App Service Environment alltid att misslyckas.
* DNS-sökning för de slutpunkter som beskrivs i den här artikeln som alla lösa korrekt. 

När du har slutfört konfigurationsåtgärderna och bekräfta vägen kan du ta bort den virtuella datorn. Undernätet måste vara ”Töm” när App Service Environment skapas.

Nu är du redo att distribuera App Service Environment!

## <a name="next-steps"></a>Nästa steg

Om du vill komma igång med App Service Environment för PowerApps, finns i [introduktion till App Service Environment] [IntroToAppServiceEnvironment].

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/services/virtual-network/
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/
[requiredports]: app-service-app-service-environment-control-inbound-traffic.md
[networkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[UDROverview]: https://azure.microsoft.com/documentation/articles/virtual-networks-udr-overview/
<!-- Old link -- [UDRHowTo]: http://azure.microsoft.com/documentation/articles/virtual-networks-udr-how-to/ --> [UDRHowTo]: https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-powershell [HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md [AzureDownloads]: https://azure.microsoft.com/downloads/ [DownloadCenterAddressRanges]: https://www.microsoft.com/download/details.aspx?id=41653  
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/ [IntroToAppServiceEnvironment]: app-service-app-service-environment-intro.md [NewPortal]:  https://portal.azure.com


<!-- IMAGES -->
