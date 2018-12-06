---
title: Ansluta säkert till Serverdelsresurser från en App Service Environment
description: Läs mer om hur du ansluter på ett säkert sätt till serverdelsresurser från en App Service Environment.
services: app-service
documentationcenter: ''
author: stefsch
manager: erikre
editor: ''
ms.assetid: f82eb283-a6e7-4923-a00b-4b4ccf7c4b5b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2016
ms.author: stefsch
ms.openlocfilehash: 4fccfbb4cbc1b0db0c032d95ad66b497520aa619
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52960948"
---
# <a name="securely-connecting-to-backend-resources-from-an-app-service-environment"></a>Ansluta säkert till Serverdelsresurser från en App Service Environment
## <a name="overview"></a>Översikt
Eftersom en App Service Environment skapas alltid i **antingen** ett Azure Resource Manager-nätverk **eller** en klassisk distributionsmodell [virtuellt nätverk] [ virtualnetwork], utgående anslutningar från en App Service-miljö till andra backend-resurser kan flöda exklusivt över det virtuella nätverket.  Med en ny ändring som gjorts i juni 2016 kan ase-miljöer också distribueras till virtuella nätverk som använder offentliga adressintervall eller RFC1918 adressutrymmen (d.v.s. privata adresser).  

Exempelvis kan finnas det en SQL-Server som körs på ett kluster med virtuella datorer med port 1433 som är låsta.  Slutpunkten kan vara ACLd att endast tillåta åtkomst från andra resurser på samma virtuella nätverk.  

Ett annat exempel är känsliga slutpunkter kan köra lokalt och vara ansluten till Azure via antingen [plats-till-plats] [ SiteToSite] eller [Azure ExpressRoute] [ ExpressRoute] anslutningar.  Endast resurser i virtuella nätverk som är ansluten till plats-till-plats eller ExpressRoute-tunnlar kommer därför att kunna få åtkomst till lokala slutpunkter.

Appar som körs på en App Service Environment kommer att kunna ansluta säkert till olika servrar och resurser för alla dessa scenarier.  Utgående trafik från appar som körs i en App Service Environment till privata slutpunkter i samma virtuella nätverk (eller anslutna till samma virtuella nätverk), kommer endast flöde över det virtuella nätverket.  Utgående trafik till privata slutpunkter flödar inte via det offentliga Internet.

Ett villkor som gäller för utgående trafik från en App Service Environment till slutpunkterna inom ett virtuellt nätverk.  App Service-miljöer kan inte nå slutpunkter av virtuella datorer som finns i den **samma** undernät som App Service Environment.  Det får normalt inte vara ett problem så länge som App Service-miljöer har distribuerats i ett undernät som reserverats för exklusiv användning av bara App Service Environment.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="outbound-connectivity-and-dns-requirements"></a>Utgående anslutning och DNS-krav
För en App Service Environment ska fungera korrekt, kräver den utgående åtkomst till olika slutpunkter. En fullständig lista över externa slutpunkter som används av en ASE är i avsnittet ”krävs nätverksanslutning” i den [nätverkskonfiguration för ExpressRoute](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) artikeln.

App Service kräver en giltig DNS-infrastruktur som konfigurerats för det virtuella nätverket.  Om DNS-konfigurationen har ändrats efter en App Service Environment har skapats av någon anledning, kan utvecklare tvinga en App Service Environment att den använder den nya DNS-konfigurationen.  Utlösa en löpande miljö omstart med hjälp av ”Restart”-ikonen längst upp på bladet för hantering i App Service Environment i portalen kommer miljön att den använder den nya DNS-konfigurationen.

Vi rekommenderar också att eventuella anpassade DNS-servrar på det virtuella nätverket konfigureras förbereds i förväg innan du skapar en App Service Environment.  Om DNS-konfiguration för ett virtuellt nätverk ändras medan en App Service Environment skapas, som resulterar i App Service Environment skapas processen misslyckas.  I en liknande vein om en anpassad DNS-server finns på den andra änden av en VPN-gateway och DNS-servern är inte kan nås eller otillgängliga misslyckas process för att skapa App Service Environment också.

## <a name="connecting-to-a-sql-server"></a>Ansluta till en SQLServer
En vanlig SQL Server-konfiguration har en slutpunkt som lyssnar på port 1433:

![SQL Server-slutpunkt][SqlServerEndpoint]

Det finns två metoder för att begränsa trafik till den här slutpunkten:

* [Network Access Control List] [ NetworkAccessControlLists] (nätverks-ACL: er)
* [Nätverkssäkerhetsgrupper][NetworkSecurityGroups]

## <a name="restricting-access-with-a-network-acl"></a>Begränsa åtkomst med ett nätverk ACL
Port 1433 kan skyddas med hjälp av en access control list.  I exemplet nedan vitlistor klienten adresser kommer från i ett virtuellt nätverk och blockerar åtkomsten till alla andra klienter.

![Exempel på nätverket åtkomstkontroll listan][NetworkAccessControlListExample]

Alla program som körs i App Service Environment i samma virtuella nätverk som SQL Server kommer att kunna ansluta till SQL Server-instans med den **VNet interna** IP-adress för SQL Server-dator.  

Exempel anslutningssträngen nedan refererar till SQL-servern med dess privata IP-adress.

    Server=tcp:10.0.1.6;Database=MyDatabase;User ID=MyUser;Password=PasswordHere;provider=System.Data.SqlClient

Även om den virtuella datorn har en offentlig slutpunkt, kommer anslutningsförsök med hjälp av den offentliga IP-adressen att avvisas på grund av nätverket ACL. 

## <a name="restricting-access-with-a-network-security-group"></a>Begränsa åtkomst med en Nätverkssäkerhetsgrupp
En annan metod för att skydda åtkomsten är med en nätverkssäkerhetsgrupp.  Nätverkssäkerhetsgrupper kan användas till enskilda virtuella datorer eller till ett undernät som innehåller virtuella datorer.

Först måste en nätverkssäkerhetsgrupp skapas:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

Begränsa åtkomsten till endast VNet intern trafik är väldigt enkelt med en nätverkssäkerhetsgrupp.  Standardreglerna i en grupp kan du endast tillåta åtkomst från andra klienter i nätverk i samma virtuella nätverk.

Därför låsa åtkomsten till SQL Server är lika enkelt som att använda en nätverkssäkerhetsgrupp med dess standardregler för antingen virtuella datorer som kör SQL Server eller undernätet som innehåller de virtuella datorerna.

Exemplet nedan gäller en nätverkssäkerhetsgrupp för undernätet som innehåller:

    Get-AzureNetworkSecurityGroup -Name "testNSGExample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-1'

Slutresultatet är en uppsättning säkerhetsregler som blockera åtkomst till externa, samtidigt som VNet intern åtkomst:

![Standardsäkerhetsregler för nätverk][DefaultNetworkSecurityRules]

## <a name="getting-started"></a>Komma igång
Kom igång med App Service-miljöer, se [introduktion till App Service Environment][IntroToAppServiceEnvironment]

Mer information kring hur du styr inkommande trafik till App Service Environment finns i [Kontrollera inkommande trafik till en App Service Environment][ControlInboundASE]

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
