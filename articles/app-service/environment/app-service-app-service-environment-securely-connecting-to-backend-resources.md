---
title: Anslut till Server delen v1
description: Lär dig mer om hur du ansluter säkert till Server dels resurser från en App Service-miljön. Detta dokument tillhandahålls endast för kunder som använder den äldre v1-ASE.
author: stefsch
ms.assetid: f82eb283-a6e7-4923-a00b-4b4ccf7c4b5b
ms.topic: article
ms.date: 10/04/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 03f773e286697a12188f238cf2f422a18a20054f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "74687301"
---
# <a name="connect-securely-to-back-end-resources-from-an-app-service-environment"></a>Anslut säkert till Server dels resurser från en App Service-miljö
Eftersom en App Service-miljön alltid skapas **antingen** i ett Azure Resource Manager virtuellt nätverk **eller** i ett klassiskt [virtuellt nätverk][virtualnetwork]för distributions modellen, kan utgående anslutningar från en app service-miljön till andra server dels resurser flöda exklusivt över det virtuella nätverket.  Med en nyligen genomförd ändring i juni 2016 kan ASE också distribueras till virtuella nätverk som använder antingen offentliga adress intervall eller RFC1918 adress utrymmen (dvs. privata adresser).  

Det kan till exempel finnas en SQL Server som körs på ett kluster med virtuella datorer med port 1433 låst.  Slut punkten kan vara ACLd att endast tillåta åtkomst från andra resurser i samma virtuella nätverk.  

Som ett annat exempel kan känsliga slut punkter köras lokalt och vara anslutna till Azure via antingen [plats-till-plats-][SiteToSite] eller [Azure ExpressRoute][ExpressRoute] -anslutningar.  Det innebär att endast resurser i virtuella nätverk som är anslutna till plats-till-plats-eller ExpressRoute-tunnlar kan komma åt lokala slut punkter.

För alla dessa scenarier kommer appar som körs på en App Service-miljön att kunna ansluta säkert till de olika servrarna och resurserna.  Utgående trafik från appar som körs i en App Service-miljön till privata slut punkter i samma virtuella nätverk (eller anslutna till samma virtuella nätverk) kommer bara att flöda över det virtuella nätverket.  Utgående trafik till privata slut punkter kommer inte att flöda över det offentliga Internet.

En av dessa varningar gäller utgående trafik från en App Service-miljön till slut punkter inom ett virtuellt nätverk.  App Service miljöer kan inte komma åt slut punkter för virtuella datorer som finns i **samma** undernät som App Service-miljön.  Detta bör normalt inte vara ett problem så länge App Service miljöer distribueras till ett undernät som är reserverat för exklusiv användning enbart av App Service-miljön.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="outbound-connectivity-and-dns-requirements"></a>Krav för utgående anslutning och DNS
För att en App Service-miljön ska fungera korrekt krävs utgående åtkomst till olika slut punkter. En fullständig lista över de externa slut punkter som används av en ASE finns i avsnittet "nödvändig nätverks anslutning" i artikeln [nätverks konfiguration för ExpressRoute](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) .

App Service miljöer kräver en giltig DNS-infrastruktur som kon figurer ATS för det virtuella nätverket.  Om DNS-konfigurationen ändras efter att en App Service-miljön har skapats, kan utvecklare tvinga en App Service-miljön att hämta den nya DNS-konfigurationen.  Om du startar om en rullande miljö startar du om med hjälp av ikonen "starta om" längst upp på bladet App Service-miljön hantering i portalen, kommer miljön att hämta den nya DNS-konfigurationen.

Vi rekommenderar också att alla anpassade DNS-servrar i VNet konfigureras före tiden innan du skapar en App Service-miljön.  Om ett virtuellt nätverks DNS-konfiguration ändras medan ett App Service-miljön skapas, leder det till att processen för App Service-miljön skapande Miss lyckas.  Om en anpassad DNS-Server finns i den andra änden av en VPN-gateway och DNS-servern inte går att komma åt eller inte är tillgänglig, kommer App Service-miljön skapande processen också att Miss Vein.

## <a name="connecting-to-a-sql-server"></a>Ansluta till en SQL Server
En vanlig SQL Server konfiguration har en slut punkt som lyssnar på port 1433:

![SQL Server slut punkt][SqlServerEndpoint]

Det finns två metoder för att begränsa trafik till den här slut punkten:

* [Nätverks Access Control listor][NetworkAccessControlLists] (nätverks-ACL: er)
* [Nätverks säkerhets grupper][NetworkSecurityGroups]

## <a name="restricting-access-with-a-network-acl"></a>Begränsa åtkomst med en nätverks-ACL
Port 1433 kan skyddas med hjälp av en lista över nätverks åtkomst kontroll.  Exemplet nedan whitelists klient adresser som härstammar från i ett virtuellt nätverk och blockerar åtkomsten till alla andra klienter.

![Exempel på nätverks Access Control listan][NetworkAccessControlListExample]

Alla program som körs i App Service-miljön i samma virtuella nätverk som SQL Server kommer att kunna ansluta till SQL Server-instansen med den **interna** virtuella IP-adressen för den SQL Server virtuella datorn.  

Anslutnings strängen nedan refererar till SQL Server med hjälp av dess privata IP-adress.

    Server=tcp:10.0.1.6;Database=MyDatabase;User ID=MyUser;Password=PasswordHere;provider=System.Data.SqlClient

Även om den virtuella datorn har en offentlig slut punkt, kommer anslutnings försök som använder den offentliga IP-adressen att avvisas på grund av nätverks åtkomst kontrol listan. 

## <a name="restricting-access-with-a-network-security-group"></a>Begränsa åtkomsten med en nätverks säkerhets grupp
En annan metod för att skydda åtkomsten är med en nätverks säkerhets grupp.  Nätverks säkerhets grupper kan tillämpas på enskilda virtuella datorer eller till ett undernät som innehåller virtuella datorer.

Först måste du skapa en nätverks säkerhets grupp:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

Att begränsa åtkomsten till enbart VNet intern trafik är mycket enkelt med en nätverks säkerhets grupp.  Standard reglerna i en nätverks säkerhets grupp tillåter endast åtkomst från andra nätverks klienter i samma virtuella nätverk.

Därför är det lika enkelt att låsa åtkomsten till SQL Server som att tillämpa en nätverks säkerhets grupp med standard reglerna på antingen de virtuella datorerna som kör SQL Server eller det undernät som innehåller de virtuella datorerna.

Exemplet nedan tillämpar en nätverks säkerhets grupp för det innehåll ande under nätet:

    Get-AzureNetworkSecurityGroup -Name "testNSGExample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-1'

Slut resultatet är en uppsättning säkerhets regler som blockerar extern åtkomst och möjliggör intern VNet-åtkomst:

![Standard regler för nätverks säkerhet][DefaultNetworkSecurityRules]

## <a name="getting-started"></a>Komma igång
Information om hur du kommer igång med App Service miljöer finns i [Introduktion till App Service-miljön][IntroToAppServiceEnvironment]

Mer information om hur du kontrollerar inkommande trafik till App Service-miljön finns i [styra inkommande trafik till en app service-miljön][ControlInboundASE]

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
