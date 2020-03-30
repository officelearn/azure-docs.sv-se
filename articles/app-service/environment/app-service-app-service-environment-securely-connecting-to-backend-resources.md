---
title: Ansluta till backend v1
description: Läs mer om hur du ansluter till backend-resurser från en App Service-miljö på ett säkert sätt. Det här dokumentet tillhandahålls endast för kunder som använder den äldre v1 ASE.
author: stefsch
ms.assetid: f82eb283-a6e7-4923-a00b-4b4ccf7c4b5b
ms.topic: article
ms.date: 10/04/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 03f773e286697a12188f238cf2f422a18a20054f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74687301"
---
# <a name="connect-securely-to-back-end-resources-from-an-app-service-environment"></a>Ansluta säkert till backend-resurser från en App Service-miljö
Eftersom en App Service-miljö alltid skapas **i** antingen ett virtuellt Azure Resource Manager-nätverk **eller** ett virtuellt klassiskt [distributionsmodellnätverk][virtualnetwork]kan utgående anslutningar från en App Service-miljö till andra serverdresurser flöda uteslutande över det virtuella nätverket.  Med en nyligen genomförd ändring som gjordes i juni 2016 kan ase-företag också distribueras i virtuella nätverk som använder antingen offentliga adressintervall eller RFC1918-adressutrymmen (dvs. privata adresser).  

Det kan till exempel finnas en SQL Server som körs på ett kluster av virtuella datorer med port 1433 låst.  Slutpunkten kan vara ACLd för att endast tillåta åtkomst från andra resurser i samma virtuella nätverk.  

Som ett annat exempel kan känsliga slutpunkter köras lokalt och anslutas till Azure via antingen [Site-to-Site-][SiteToSite] eller [Azure ExpressRoute-anslutningar.][ExpressRoute]  Därför kan endast resurser i virtuella nätverk som är anslutna till tunnlarna Plats till plats eller ExpressRoute komma åt lokala slutpunkter.

För alla dessa scenarier kan appar som körs i en App Service-miljö på ett säkert sätt ansluta till de olika servrarna och resurserna.  Utgående trafik från appar som körs i en App Service-miljö till privata slutpunkter i samma virtuella nätverk (eller ansluten till samma virtuella nätverk) flödar bara över det virtuella nätverket.  Utgående trafik till privata slutpunkter flödar inte över det offentliga Internet.

En varning gäller utgående trafik från en App Service-miljö till slutpunkter i ett virtuellt nätverk.  Apptjänstmiljöer kan inte nå slutpunkter för virtuella datorer som finns i **samma** undernät som App Service-miljön.  Detta bör normalt inte vara ett problem så länge App Service-miljöer distribueras till ett undernät som är reserverat för exklusiv användning av endast App Service-miljön.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="outbound-connectivity-and-dns-requirements"></a>Krav för utgående anslutning och DNS
För att en App Service-miljö ska fungera korrekt kräver den utgående åtkomst till olika slutpunkter. En fullständig lista över de externa slutpunkter som används av en ASE finns i avsnittet "Nödvändig nätverksanslutning" i artikeln [Nätverkskonfiguration för ExpressRoute.](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity)

App Service-miljöer kräver en giltig DNS-infrastruktur som konfigurerats för det virtuella nätverket.  Om DNS-konfigurationen av någon anledning ändras efter att en App Service-miljö har skapats, kan utvecklare tvinga en App Service-miljö att hämta den nya DNS-konfigurationen.  Om du utlöser en omstart av en rullande miljö med hjälp av ikonen "Starta om" högst upp på hanteringsbladet för App Service Environment i portalen kommer miljön att plocka upp den nya DNS-konfigurationen.

Vi rekommenderar också att alla anpassade DNS-servrar på det virtuella nätverket ställs in i förväg innan du skapar en App Service-miljö.  Om ett virtuellt nätverks DNS-konfiguration ändras medan en App Service-miljö skapas, resulterar det i att processen för att skapa App Service Environment misslyckas.  På liknande sätt, om det finns en anpassad DNS-server i andra änden av en VPN-gateway och DNS-servern inte kan nås eller inte är tillgänglig, misslyckas även skapandet av App Service Environment.

## <a name="connecting-to-a-sql-server"></a>Ansluta till en SQL Server
En vanlig SQL Server-konfiguration har en slutpunkt som lyssnar på port 1433:

![Slutpunkt för SQL Server][SqlServerEndpoint]

Det finns två metoder för att begränsa trafiken till den här slutpunkten:

* [Kontrolllistor för nätverksåtkomst][NetworkAccessControlLists] (nät-ACL:er)
* [Säkerhetsgrupper för nätverk][NetworkSecurityGroups]

## <a name="restricting-access-with-a-network-acl"></a>Begränsa åtkomst med en ACL-nätverk
Port 1433 kan säkras med hjälp av en kontrolllista för nätverksåtkomst.  Exemplet nedan vitlistar klientadresser som kommer inifrån ett virtuellt nätverk och blockerar åtkomst till alla andra klienter.

![Exempel på kontrolllista för nätverksåtkomst][NetworkAccessControlListExample]

Alla program som körs i App Service Environment i samma virtuella nätverk som SQL Server kan ansluta till SQL Server-instansen med den **interna** IP-adressen för SQL Server för den virtuella SQL Server-datorn.  

Exempelanslutningssträngen nedan refererar till SQL Server med hjälp av sin privata IP-adress.

    Server=tcp:10.0.1.6;Database=MyDatabase;User ID=MyUser;Password=PasswordHere;provider=System.Data.SqlClient

Även om den virtuella datorn har en offentlig slutpunkt också, kommer anslutningsförsök med den offentliga IP-adressen att avvisas på grund av nätverket ACL. 

## <a name="restricting-access-with-a-network-security-group"></a>Begränsa åtkomst med en nätverkssäkerhetsgrupp
En alternativ metod för att skydda åtkomsten är med en nätverkssäkerhetsgrupp.  Nätverkssäkerhetsgrupper kan tillämpas på enskilda virtuella datorer eller på ett undernät som innehåller virtuella datorer.

Först måste en nätverkssäkerhetsgrupp skapas:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

Att begränsa åtkomsten till endast intern VNet-trafik är mycket enkelt med en nätverkssäkerhetsgrupp.  Standardreglerna i en nätverkssäkerhetsgrupp tillåter endast åtkomst från andra nätverksklienter i samma virtuella nätverk.

Därför är det lika enkelt att låsa åtkomsten till SQL Server som att tillämpa en nätverkssäkerhetsgrupp med standardreglerna på antingen de virtuella datorer som kör SQL Server eller undernätet som innehåller de virtuella datorerna.

Exemplet nedan tillämpar en nätverkssäkerhetsgrupp på det innehållande undernätet:

    Get-AzureNetworkSecurityGroup -Name "testNSGExample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-1'

Slutresultatet är en uppsättning säkerhetsregler som blockerar extern åtkomst, samtidigt som VNet-intern åtkomst tillåts:

![Standardregler för nätverkssäkerhet][DefaultNetworkSecurityRules]

## <a name="getting-started"></a>Komma igång
Mer om du vill komma igång med App Service-miljöer finns i [Introduktion till App Service Environment][IntroToAppServiceEnvironment]

Mer information om hur du kontrollerar inkommande trafik till apptjänstmiljön finns [i Kontrollera inkommande trafik till en App Service-miljö][ControlInboundASE]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[ControlInboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[SiteToSite]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-multi-site
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/
[NetworkAccessControlLists]: https://azure.microsoft.com/documentation/articles/virtual-networks-acl/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[ControlInboundASE]:  app-service-app-service-environment-control-inbound-traffic.md

<!-- IMAGES -->
[SqlServerEndpoint]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/SqlServerEndpoint01.png
[NetworkAccessControlListExample]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/NetworkAcl01.png
[DefaultNetworkSecurityRules]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/DefaultNetworkSecurityRules01.png 
