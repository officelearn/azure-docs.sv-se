---
title: Konfigurera Azure ExpressRoute v1
description: Nätverkskonfiguration för App Service Environment för PowerApps med Azure ExpressRoute. Det här dokumentet tillhandahålls endast för kunder som använder den äldre v1 ASE.
author: stefsch
ms.assetid: 34b49178-2595-4d32-9b41-110c96dde6bf
ms.topic: article
ms.date: 10/14/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: fc11c6932d625b119ad933f5d4d128b4355530c5
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804443"
---
# <a name="network-configuration-details-for-app-service-environment-for-powerapps-with-azure-expressroute"></a>Information om nätverkskonfiguration för App Service Environment för PowerApps med Azure ExpressRoute

Kunder kan ansluta en [Azure ExpressRoute-krets][ExpressRoute] till sin virtuella nätverksinfrastruktur för att utöka sitt lokala nätverk till Azure. App Service Environment skapas i ett undernät till den [virtuella nätverksinfrastrukturen.][virtualnetwork] Appar som körs på App Service Environment upprättar säkra anslutningar till backend-resurser som endast är tillgängliga via ExpressRoute-anslutningen.  

App Service Environment kan skapas i följande scenarier:
- Virtuella nätverk i Azure Resource Manager.
- Klassiska virtuella nätverk för distributionsmodell.
- Virtuella nätverk som använder offentliga adressintervall eller RFC1918-adressutrymmen (det vill ha privata adresser). 

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="required-network-connectivity"></a>Nödvändig nätverksanslutning

App Service Environment har krav på nätverksanslutning som till en början kanske inte uppfylls i ett virtuellt nätverk som är anslutet till ExpressRoute.

App Service Environment kräver följande inställningar för nätverksanslutning för att fungera korrekt:

* Utgående nätverksanslutning till Azure Storage-slutpunkter över hela världen på både port 80 och port 443. Dessa slutpunkter finns i samma region som App Service Environment och även andra Azure-regioner. Azure Storage-slutpunkter matchas under följande DNS-domäner: table.core.windows.net, blob.core.windows.net, queue.core.windows.net och file.core.windows.net.  

* Utgående nätverksanslutning till Azure Files-tjänsten på port 445.

* Utgående nätverksanslutning till Azure SQL Database-slutpunkter som finns i samma region som App Service Environment. SLUTpunkter för SQL-databas matchas under domänen database.windows.net, vilket kräver öppen åtkomst till portarna 1433, 11000-11999 och 14000-14999. Mer information om SQL Database V12-portanvändning finns i [Portar utöver 1433 för ADO.NET 4.5](../../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md).

* Utgående nätverksanslutning till slutpunkterna för Azure-hanteringsplan (både Azure classic deployment model och Azure Resource Manager-slutpunkter). Anslutningen till dessa slutpunkter omfattar management.core.windows.net och management.azure.com domäner. 

* Utgående nätverksanslutning till ocsp.msocsp.com, mscrl.microsoft.com och crl.microsoft.com domäner. Anslutning till dessa domäner behövs för att stödja TLS-funktioner.

* DNS-konfigurationen för det virtuella nätverket måste kunna lösa alla slutpunkter och domäner som nämns i den här artikeln. Om slutpunkterna inte kan lösas misslyckas skapandet av App Service Environment. Alla befintliga App Service-miljöer markeras som felaktiga.

* Utgående åtkomst på port 53 krävs för kommunikation med DNS-servrar.

* Om det finns en anpassad DNS-server i andra änden av en VPN-gateway måste DNS-servern kunna nås från undernätet som innehåller App Service Environment. 

* Den utgående nätverkssökvägen kan inte färdas genom interna företagsproxyservrar och kan inte tvingas in lokalt. Dessa åtgärder ändrar den gällande NAT-adressen för utgående nätverkstrafik från App Service Environment. Ändringar i NAT-adressen för utgående nätverkstrafik i App Service Environment orsakar anslutningsfel till många av slutpunkterna. Det går inte att skapa App Service Environment. Alla befintliga App Service-miljöer markeras som felaktiga.

* Inkommande nätverksåtkomst till nödvändiga portar för App Service Environment måste tillåtas. Mer information finns [i Så här styr du inkommande trafik till App Service Environment][requiredports].

För att uppfylla DNS-kraven kontrollerar du att en giltig DNS-infrastruktur är konfigurerad och underhållen för det virtuella nätverket. Om DNS-konfigurationen ändras efter att App Service Environment har skapats kan utvecklare tvinga App Service Environment att hämta den nya DNS-konfigurationen. Du kan utlösa en rullande miljö omstart genom att använda **ikonen Starta om** under App Service Environment management i [Azure-portalen][NewPortal]. Omstarten gör att miljön hämtar den nya DNS-konfigurationen.

Konfigurera en [nätverkssäkerhetsgrupp (NSG)][NetworkSecurityGroups] i undernätet för apptjänstmiljö för att uppfylla kraven för inkommande nätverksåtkomst. NSG ger den åtkomst som krävs [för att styra inkommande trafik till App Service Environment][requiredports].

## <a name="outbound-network-connectivity"></a>Utgående nätverksanslutning

Som standard annonserar en nyskapade ExpressRoute-krets en standardväg som tillåter utgående internetanslutning. App Service Environment kan använda den här konfigurationen för att ansluta till andra Azure-slutpunkter.

En gemensam kundkonfiguration är att definiera sin egen standardväg (0.0.0.0/0), vilket tvingar utgående internettrafik att flöda lokalt. Det här trafikflödet bryter alltid App Service Environment. Den utgående trafiken blockeras antingen lokalt eller NAT'd till en oigenkännlig uppsättning adresser som inte längre fungerar med olika Azure-slutpunkter.

Lösningen är att definiera en (eller flera) användardefinierade vägar (UDRs) i undernätet som innehåller App Service Environment. En UDR definierar undernätsspecifika vägar som uppfylls i stället för standardvägen.

Använd om möjligt följande konfiguration:

* ExpressRoute-konfigurationen annonserar 0.0.0.0/0. Som standard finns konfigurationskraftstunnlar all utgående trafik lokalt.
* Den UDR som används på undernätet som innehåller App Service Environment definierar 0.0.0.0/0 med nästa hoptyp av internet. Ett exempel på den här konfigurationen beskrivs senare i den här artikeln.

Den kombinerade effekten av den här konfigurationen är att UDR på undernätsnivå har företräde framför ExpressRoute-krafttunnelen. Utgående internet från App Service Environment garanteras.

> [!IMPORTANT]
> De vägar som definieras i en UDR måste vara tillräckligt specifika för att ha företräde framför alla vägar som annonseras av ExpressRoute-konfigurationen. I exemplet som beskrivs i nästa avsnitt används det breda adressintervallet 0.0.0.0/0. Det här intervallet kan av misstag åsidosättas av ruttannonser som använder mer specifika adressintervall.
> 
> App Service Environment stöds inte med ExpressRoute-konfigurationer som korsannonserar vägar från den offentliga peering-sökvägen till den privata peering-sökvägen. ExpressRoute-konfigurationer som har konfigurerad offentlig peering tar emot vägannonser från Microsoft för en stor uppsättning Microsoft Azure IP-adressintervall. Om dessa adressintervall korsannonseras på den privata peering-sökvägen, tvingas alla utgående nätverkspaket från undernätet för App Service-miljön tunnel till kundens lokala nätverksinfrastruktur. Det här nätverksflödet stöds för närvarande inte med App Service Environment. En lösning är att stoppa korsannonseringsvägar från den offentliga peering-vägen till den privata peering-vägen.
> 
> 

Bakgrundsinformation om användardefinierade vägar finns i [Routning av virtuell nätverkstrafik][UDROverview].  

Mer information om hur du skapar och konfigurerar användardefinierade vägar finns i [Dirigera nätverkstrafik med en vägtabell med hjälp av PowerShell][UDRHowTo].

## <a name="udr-configuration"></a>UDR-konfiguration

Det här avsnittet visar ett exempel på UDR-konfiguration för App Service Environment.

### <a name="prerequisites"></a>Krav

* Installera Azure PowerShell från [sidan Azure Downloads][AzureDownloads]. Välj en nedladdning med ett datum i juni 2015 eller senare. Under **Kommandoradsverktyg** > **Windows PowerShell**väljer du **Installera** för att installera de senaste PowerShell-cmdlets.

* Skapa ett unikt undernät som endast kan användas av App Service Environment. Det unika undernätet säkerställer att de UDR som tillämpas på undernätet endast är öppna utgående trafik för App Service Environment.

> [!IMPORTANT]
> Distribuera endast App Service Environment när du har slutfört konfigurationsstegen. Stegen säkerställer att utgående nätverksanslutning är tillgänglig innan du försöker distribuera App Service Environment.

### <a name="step-1-create-a-route-table"></a>Steg 1: Skapa en rutttabell

Skapa en vägtabell med namnet **DirectInternetRouteTable** i regionen Västra USA Azure, som visas i det här kodavsnittet:

`New-AzureRouteTable -Name 'DirectInternetRouteTable' -Location uswest`

### <a name="step-2-create-routes-in-the-table"></a>Steg 2: Skapa rutter i tabellen

Lägg till vägar till rutttabellen för att aktivera utgående internetåtkomst.  

Konfigurera utgående åtkomst till internet. Definiera en väg för 0.0.0.0/0 som visas i det här kodavsnittet:

`Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 0' -AddressPrefix 0.0.0.0/0 -NextHopType Internet`

0.0.0.0/0 är ett brett adressintervall. Intervallet åsidosätts av adressintervall som annonseras av ExpressRoute och som är mer specifika. En UDR med en 0.0.0.0/0-rutt bör användas tillsammans med en ExpressRoute-konfiguration som endast annonserar 0.0.0.0/0. 

Som ett alternativ kan du hämta en aktuell, omfattande lista över CIDR-intervall som används av Azure. XML-filen för alla Azure IP-adressintervall är tillgänglig från [Microsoft Download Center][DownloadCenterAddressRanges].  

> [!NOTE]
>
> Azure IP-adressintervallen ändras med tiden. Användardefinierade vägar behöver regelbundna manuella uppdateringar för att synkroniseras.
>
> En enda UDR har en övre standardgräns på 100 vägar. Du måste "sammanfatta" Azure IP-adressintervall för att passa inom 100-route-gränsen. UDR-definierade rutter måste vara mer specifika än rutter som annonseras av din ExpressRoute-anslutning.
> 

### <a name="step-3-associate-the-table-to-the-subnet"></a>Steg 3: Koppla tabellen till undernätet

Associera flödestabellen till undernätet där du tänker distribuera App Service Environment. Det här kommandot associerar **directinternetroutetable-tabellen** till **ASESubnet-undernätet** som innehåller App Service Environment.

`Set-AzureSubnetRouteTable -VirtualNetworkName 'YourVirtualNetworkNameHere' -SubnetName 'ASESubnet' -RouteTableName 'DirectInternetRouteTable'`

### <a name="step-4-test-and-confirm-the-route"></a>Steg 4: Testa och bekräfta rutten

När flödestabellen är bunden till undernätet testar och bekräftar du rutten.

Distribuera en virtuell dator i undernätet och bekräfta följande villkor:

* Utgående trafik till Azure- och icke-Azure-slutpunkter som beskrivs i den här artikeln rinner **inte** nedför ExpressRoute-kretsen. Om utgående trafik från undernätet tvingar tunnel lokalt misslyckas alltid skapandet av App Service Environment.
* DNS-sökning för slutpunkterna som beskrivs i den här artikeln löser sig korrekt. 

När du har slutfört konfigurationsstegen och bekräftat vägen tar du bort den virtuella datorn. Undernätet måste vara "tomt" när App Service Environment skapas.

Nu är du redo att distribuera App Service Environment!

## <a name="next-steps"></a>Nästa steg

Mer om du vill komma igång med App Service Environment för PowerApps finns i [Introduktion till App Service Environment][IntroToAppServiceEnvironment].

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/services/virtual-network/ 
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/ 
[requiredports]: app-service-app-service-environment-control-inbound-traffic.md 
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/ 
[UDROverview]: https://azure.microsoft.com/documentation/articles/virtual-networks-udr-overview/ 
<!-- Old link -- [UDRHowTo]: https://azure.microsoft.com/documentation/articles/virtual-networks-udr-how-to/ -->

[UDRHowTo]: https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-powershell 
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md 
[AzureDownloads]: https://azure.microsoft.com/downloads/ 
[DownloadCenterAddressRanges]: https://www.microsoft.com/download/details.aspx?id=41653 
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/ 
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md 
[NewPortal]:  https://portal.azure.com 


<!-- IMAGES -->
