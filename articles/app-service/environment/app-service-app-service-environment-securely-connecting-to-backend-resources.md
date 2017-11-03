---
title: "Ansluta på ett säkert sätt till Serverdelsresurser från en Apptjänst-miljö"
description: "Läs mer om hur du ansluter på ett säkert sätt till serverdelsresurser från en Apptjänst-miljö."
services: app-service
documentationcenter: 
author: stefsch
manager: erikre
editor: 
ms.assetid: f82eb283-a6e7-4923-a00b-4b4ccf7c4b5b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2016
ms.author: stefsch
ms.openlocfilehash: a43d88d64710b95dd753c19f02582f22accac8b6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="securely-connecting-to-backend-resources-from-an-app-service-environment"></a>Ansluta på ett säkert sätt till Serverdelsresurser från en Apptjänst-miljö
## <a name="overview"></a>Översikt
Eftersom en Apptjänst-miljö skapas alltid i **antingen** ett virtuellt nätverk i Azure Resource Manager **eller** klassiska distributionsmodellen [virtuellt nätverk][virtualnetwork], utgående anslutningar från en Apptjänst-miljö till andra serverdelsresurser kan flöda uteslutande över det virtuella nätverket.  Med en ändring görs i juni 2016 distribueras ASEs också till virtuella nätverk som använder offentliga-adressintervall eller RFC1918 adressutrymmen (d.v.s. privata adresser).  

Det kan exempelvis vara en SQL-Server som körs på ett kluster med virtuella datorer med port 1433 låst.  Slutpunkten kan vara ACLd att endast tillåta åtkomst från andra resurser på samma virtuella nätverk.  

Ett annat exempel är känsliga slutpunkter kan köra lokalt och vara ansluten till Azure via antingen [plats-till-plats] [ SiteToSite] eller [Azure ExpressRoute] [ ExpressRoute] anslutningar.  Därför kommer bara resurser i virtuella nätverk som är ansluten till plats-till-plats eller ExpressRoute tunnlar att kunna komma åt lokala slutpunkter.

Appar som körs på en Apptjänst-miljö kommer att kunna ansluta säkert till olika servrar och resurser för alla dessa scenarier.  Utgående trafik från appar som körs i en Apptjänst-miljö till privata slutpunkter i samma virtuella nätverk (eller anslutna till samma virtuella nätverk), kommer endast flödet över det virtuella nätverket.  Utgående trafik till privata slutpunkter flödar inte över offentligt Internet.

Ett villkor som gäller för utgående trafik från en Apptjänst-miljö till slutpunkter inom ett virtuellt nätverk.  Apptjänstmiljöer kan inte nå slutpunkter av virtuella datorer finns i den **samma** undernät som Apptjänst-miljön.  Det får normalt inte vara ett problem så länge Apptjänstmiljöer distribueras i ett undernät som reserverats för exklusiv användning av endast Apptjänst-miljön.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="outbound-connectivity-and-dns-requirements"></a>Utgående anslutning och DNS-krav
För en Apptjänstmiljö ska fungera korrekt, kräver den utgående åtkomst till olika slutpunkter. En fullständig lista över de externa slutpunkter som används av en ASE finns i avsnittet ”krävs nätverksanslutning” i den [nätverkskonfigurationen för ExpressRoute](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) artikel.

Apptjänstmiljöer kräver ett giltigt DNS-infrastruktur konfigurerad för det virtuella nätverket.  Om DNS-konfigurationen har ändrats efter en Apptjänst-miljö har skapats av någon anledning, kan utvecklare tvinga en Apptjänst-miljö för den nya DNS-konfigurationen.  Utlöser en rullande miljö omstart med hjälp av ikonen för ”starta om” som finns längst upp på bladet hantering av Apptjänst-miljö i portalen kommer miljö för att hämta den nya DNS-konfigurationen.

Vi rekommenderar också att alla anpassade DNS-servrar för det virtuella nätverket konfigureras i förväg innan du skapar en Apptjänst-miljö.  Om DNS-konfiguration för ett virtuellt nätverk har ändrats medan en Apptjänst-miljö skapas, vilket leder till att Apptjänstmiljö skapa processen misslyckas.  I en liknande vein om en anpassad DNS-server finns på den andra änden av en VPN-gateway och DNS-servern är felaktig eller inte tillgänglig, misslyckas Apptjänst-miljö skapas också.

## <a name="connecting-to-a-sql-server"></a>Ansluter till en SQLServer
En vanlig SQL Server-konfiguration har en slutpunkt som lyssnade på port 1433:

![Slutpunkten för SQL Server][SqlServerEndpoint]

Det finns två tillvägagångssätt för att begränsa trafik till den här slutpunkten:

* [Network Access Control List] [ NetworkAccessControlLists] (Network ACL: er)
* [Nätverkssäkerhetsgrupper][NetworkSecurityGroups]

## <a name="restricting-access-with-a-network-acl"></a>Begränsa åtkomst till ett nätverk ACL
Port 1433 kan skyddas med hjälp av en lista över åtkomstkontroll.  I exemplet nedan whitelists klienten adresserar kommer från inuti ett virtuellt nätverk och blockerar åtkomst till alla andra klienter.

![Exempel på nätverket Access Control][NetworkAccessControlListExample]

Alla program som körs i Apptjänst-miljö i samma virtuella nätverk som SQL-servern kommer att kunna ansluta till SQL Server-instans använder den **VNet interna** IP-adress för den virtuella datorn för SQL Server.  

Anslutningssträngen exemplet nedan refererar till SQL Server med dess privata IP-adress.

    Server=tcp:10.0.1.6;Database=MyDatabase;User ID=MyUser;Password=PasswordHere;provider=System.Data.SqlClient

Även om den virtuella datorn har en offentlig slutpunkt, avvisas anslutningsförsök med hjälp av den offentliga IP-adressen på grund av nätverkets ACL. 

## <a name="restricting-access-with-a-network-security-group"></a>Begränsa åtkomst med en Nätverkssäkerhetsgrupp
Det är en alternativ metod för att skydda åtkomsten med en nätverkssäkerhetsgrupp.  Nätverkssäkerhetsgrupper kan användas till enskilda virtuella datorer eller till ett undernät som innehåller virtuella datorer.

Först måste en nätverkssäkerhetsgrupp skapas:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

Begränsa åtkomsten till endast VNet intern trafik är mycket enkelt med en nätverkssäkerhetsgrupp.  Standardregler i en nätverkssäkerhetsgrupp Tillåt endast åtkomst från andra klienter i nätverk i samma virtuella nätverk.

Detta innebär att låsa åtkomsten till SQL Server är så enkelt som att tillämpa en nätverkssäkerhetsgrupp med dess standardregler på antingen virtuella datorer som kör SQL Server eller undernät som innehåller de virtuella datorerna.

Exemplet nedan gäller en nätverkssäkerhetsgrupp som innehåller undernätet:

    Get-AzureNetworkSecurityGroup -Name "testNSGExample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-1'

Slutresultatet är en uppsättning säkerhetsregler som blockerar extern åtkomst samtidigt VNet intern åtkomst:

![Standard Nätverkssäkerhetsregler][DefaultNetworkSecurityRules]

## <a name="getting-started"></a>Komma igång
Kom igång med Apptjänstmiljöer finns [introduktion till Apptjänst-miljö][IntroToAppServiceEnvironment]

Mer information kring styra inkommande trafik till din Apptjänst-miljö finns [styra inkommande trafik till en Apptjänst-miljö][ControlInboundASE]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[ControlInboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[NetworkAccessControlLists]: https://azure.microsoft.com/documentation/articles/virtual-networks-acl/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[ControlInboundASE]:  app-service-app-service-environment-control-inbound-traffic.md

<!-- IMAGES -->
[SqlServerEndpoint]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/SqlServerEndpoint01.png
[NetworkAccessControlListExample]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/NetworkAcl01.png
[DefaultNetworkSecurityRules]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/DefaultNetworkSecurityRules01.png 
