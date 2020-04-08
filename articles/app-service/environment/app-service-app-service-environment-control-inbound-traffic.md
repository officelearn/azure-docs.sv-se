---
title: Kontroll inkommande trafik v1
description: Lär dig hur du styr inkommande trafik till en App Service-miljö. Det här dokumentet tillhandahålls endast för kunder som använder den äldre v1 ASE.
author: ccompy
ms.assetid: 4cc82439-8791-48a4-9485-de6d8e1d1a08
ms.topic: article
ms.date: 01/11/2017
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 857b2b00aadced567bc8ac191cdd9908f7bea7a3
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804409"
---
# <a name="how-to-control-inbound-traffic-to-an-app-service-environment"></a>Så här kontrollerar du inkommande trafik till en apptjänstmiljö
## <a name="overview"></a>Översikt
En App Service-miljö kan skapas i **antingen** ett virtuellt Azure Resource Manager-nätverk **eller** ett virtuellt klassiskt [distributionsmodellnätverk][virtualnetwork].  Ett nytt virtuellt nätverk och nytt undernät kan definieras när en App Service-miljö skapas.  Alternativt kan en App Service-miljö skapas i ett befintligt virtuellt nätverk och befintligt undernät.  Med en ändring som gjordes i juni 2016 kan ASE också distribueras till virtuella nätverk som använder antingen offentliga adressintervall eller RFC1918-adressutrymmen (dvs. privata adresser).  Mer information om hur du skapar en apptjänstmiljö finns i [Så här skapar du en apptjänstmiljö][HowToCreateAnAppServiceEnvironment].

En App Service-miljö måste alltid skapas i ett undernät eftersom ett undernät tillhandahåller en nätverksgräns som kan användas för att låsa inkommande trafik bakom uppströmsenheter och tjänster så att HTTP- och HTTPS-trafik endast accepteras från specifika IP-adresser uppströms.

Inkommande och utgående nätverkstrafik i ett undernät styrs med hjälp av en [nätverkssäkerhetsgrupp][NetworkSecurityGroups]. För att kontrollera inkommande trafik måste du skapa nätverkssäkerhetsregler i en nätverkssäkerhetsgrupp och sedan tilldela nätverkssäkerhetsgruppen undernätet som innehåller App Service-miljön.

När en nätverkssäkerhetsgrupp har tilldelats ett undernät tillåts/blockeras inkommande trafik till appar i App Service-miljön baserat på de tillåt och neka regler som definierats i nätverkssäkerhetsgruppen.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="inbound-network-ports-used-in-an-app-service-environment"></a>Inkommande nätverksportar som används i en apptjänstmiljö
Innan du låser inkommande nätverkstrafik med en nätverkssäkerhetsgrupp är det viktigt att känna till den uppsättning nödvändiga och valfria nätverksportar som används av en App Service-miljö.  Oavsiktligt stänga av trafik till vissa portar kan resultera i förlust av funktioner i en App Service Environment.

Följande är en lista över portar som används av en App Service Environment. Alla portar är **TCP**, om inte annat tydligt anges:

* 454: **Port som krävs** som används av Azure-infrastruktur för att hantera och underhålla App Service-miljöer via TLS.  Blockera inte trafik till den här porten.  Denna port är alltid bunden till den offentliga VIP av en ASE.
* 455: **Port som krävs** som används av Azure-infrastruktur för att hantera och underhålla App Service-miljöer via TLS.  Blockera inte trafik till den här porten.  Denna port är alltid bunden till den offentliga VIP av en ASE.
* 80: Standardport för inkommande HTTP-trafik till appar som körs i App Service Plans i en App Service-miljö.  På en ILB-aktiverad ASE är den här porten bunden till ILB-adressen för ASE.
* 443: Standardport för inkommande TLS-trafik till appar som körs i App Service Plans i en App Service-miljö.  På en ILB-aktiverad ASE är den här porten bunden till ILB-adressen för ASE.
* 21: Styrkanal för FTP.  Den här porten kan blockeras säkert om FTP inte används.  På en ILB-aktiverad ASE kan den här porten vara bunden till ILB-adressen för en ASE.
* 990: Styrkanal för FTPS.  Den här porten kan blockeras säkert om FTPS inte används.  På en ILB-aktiverad ASE kan den här porten vara bunden till ILB-adressen för en ASE.
* 10001-10020: Datakanaler för FTP.  Precis som med kontrollkanalen kan dessa portar blockeras säkert om FTP inte används.  På en ILB-aktiverad ASE kan den här porten vara bunden till ASE:s ILB-adress.
* 4016: Används för fjärrfelsökning med Visual Studio 2012.  Den här porten kan blockeras säkert om funktionen inte används.  På en ILB-aktiverad ASE är den här porten bunden till ILB-adressen för ASE.
* 4018: Används för fjärrfelsökning med Visual Studio 2013.  Den här porten kan blockeras säkert om funktionen inte används.  På en ILB-aktiverad ASE är den här porten bunden till ILB-adressen för ASE.
* 4020: Används för fjärrfelsökning med Visual Studio 2015.  Den här porten kan blockeras säkert om funktionen inte används.  På en ILB-aktiverad ASE är den här porten bunden till ILB-adressen för ASE.

## <a name="outbound-connectivity-and-dns-requirements"></a>Krav för utgående anslutning och DNS
För att en App Service-miljö ska fungera korrekt kräver den också utgående åtkomst till olika slutpunkter. En fullständig lista över de externa slutpunkter som används av en ASE finns i avsnittet "Nödvändig nätverksanslutning" i artikeln [Nätverkskonfiguration för ExpressRoute.](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity)

App Service-miljöer kräver en giltig DNS-infrastruktur som konfigurerats för det virtuella nätverket.  Om DNS-konfigurationen av någon anledning ändras efter att en App Service-miljö har skapats, kan utvecklare tvinga en App Service-miljö att hämta den nya DNS-konfigurationen.  Om du utlöser en omstart av en rullande miljö med hjälp av ikonen "Starta om" högst upp i hanteringsbladet för App Service Environment i [Azure-portalen][NewPortal] kommer miljön att plocka upp den nya DNS-konfigurationen.

Vi rekommenderar också att alla anpassade DNS-servrar på det virtuella nätverket ställs in i förväg innan du skapar en App Service-miljö.  Om ett virtuellt nätverks DNS-konfiguration ändras medan en App Service-miljö skapas, resulterar det i att processen för att skapa App Service Environment misslyckas.  På liknande sätt, om det finns en anpassad DNS-server i andra änden av en VPN-gateway och DNS-servern inte kan nås eller inte är tillgänglig, misslyckas även skapandet av App Service Environment.

## <a name="creating-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp
Fullständig information om hur nätverkssäkerhetsgrupper fungerar finns i följande [information][NetworkSecurityGroups].  Exemplet Azure Service Management nedan berör höjdpunkter i nätverkssäkerhetsgrupper, med fokus på att konfigurera och tillämpa en nätverkssäkerhetsgrupp på ett undernät som innehåller en App Service-miljö.

**Anm.:** Nätverkssäkerhetsgrupper kan konfigureras grafiskt med Hjälp av [Azure Portal](https://portal.azure.com) eller via Azure PowerShell.

Nätverkssäkerhetsgrupper skapas först som en fristående enhet som är associerad med en prenumeration. Eftersom nätverkssäkerhetsgrupper skapas i en Azure-region kontrollerar du att nätverkssäkerhetsgruppen skapas i samma region som App Service-miljön.

Följande visar att skapa en nätverkssäkerhetsgrupp:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

När en nätverkssäkerhetsgrupp har skapats läggs en eller flera nätverkssäkerhetsregler till i den.  Eftersom uppsättningen regler kan ändras med tiden, rekommenderas att utrymmet ut numreringsystemet som används för regelprioriteringar för att göra det enkelt att infoga ytterligare regler över tiden.

Exemplet nedan visar en regel som uttryckligen ger åtkomst till hanteringsportar som behövs av Azure-infrastrukturen för att hantera och underhålla en App Service-miljö.  Observera att all hanteringstrafik flödar över TLS och är skyddad av klientcertifikat, så även om portarna öppnas är de otillgängliga av någon annan enhet än Azure-hanteringsinfrastruktur.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP


När du låser åtkomsten till port 80 och 443 för att "dölja" en App Service-miljö bakom uppströmsenheter eller -tjänster måste du känna till IP-adressen uppströms.  Om du till exempel använder en brandvägg för webbprogram (WAF) har WAF en egen IP-adress (eller adresser) som den använder när trafik proxyförstörnad till en apptjänstmiljö nedströms.  Du måste använda den här IP-adressen i parametern *SourceAddressPrefix* för en nätverkssäkerhetsregel.

I exemplet nedan tillåts inkommande trafik från en specifik IP-adress uppströms uttryckligen.  Adressen *1.2.3.4* används som platshållare för IP-adressen för en uppströms WAF.  Ändra värdet så att det matchar den adress som används av din uppströmsenhet eller tjänst.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTP" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTPS" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Om FTP-stöd önskas kan följande regler användas som mall för att bevilja åtkomst till FTP-kontrollporten och datakanalportarna.  Eftersom FTP är ett tillståndskänsligt protokoll kanske du inte kan dirigera FTP-trafik via en traditionell HTTP/HTTPS-brandvägg eller proxyenhet.  I det här fallet måste du ställa in *SourceAddressPrefix* till ett annat värde - till exempel IP-adressintervallet för utvecklare eller distributionsdatorer som FTP-klienter körs på. 

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPCtrl" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '21' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPDataRange" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '10001-10020' -Protocol TCP

(**Obs:** datakanalportintervallet kan ändras under förhandsgranskningsperioden.)

Om fjärrfelsökning med Visual Studio används visar följande regler hur du beviljar åtkomst.  Det finns en separat regel för varje version av Visual Studio som stöds eftersom varje version använder en annan port för fjärrfelsökning.  Precis som med FTP-åtkomst kan det hända att fjärrfelsökningstrafik inte flödar korrekt via en traditionell WAF- eller proxyenhet.  *SourceAddressPrefix* kan i stället ställas in på IP-adressintervallet för utvecklardatorer som kör Visual Studio.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2012" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4016' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2013" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4018' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2015" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4020' -Protocol TCP

## <a name="assigning-a-network-security-group-to-a-subnet"></a>Tilldela en nätverkssäkerhetsgrupp till ett undernät
En nätverkssäkerhetsgrupp har en standardsäkerhetsregel som nekar åtkomst till all extern trafik.  Resultatet av att kombinera de nätverkssäkerhetsregler som beskrivs ovan och standardsäkerhetsregeln som blockerar inkommande trafik är att endast trafik från källadressintervall som är associerade med åtgärden *Tillåt* kan skicka trafik till appar som körs i en apptjänstmiljö.

När en nätverkssäkerhetsgrupp har fyllts i med säkerhetsregler måste den tilldelas till undernätet som innehåller App Service-miljön.  Tilldelningskommandot refererar både till namnet på det virtuella nätverk där App Service-miljön finns, samt namnet på undernätet där App Service Environment skapades.  

Exemplet nedan visar en nätverkssäkerhetsgrupp som tilldelas ett undernät och ett virtuellt nätverk:

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

När nätverkssäkerhetsgrupptilldelningen lyckas (tilldelningen är en tidskrävande verksamhet och kan ta några minuter att slutföra) kommer endast inkommande trafikmatchning *Tillåt* regler att nå appar i App Service-miljön.

För fullständighet visar följande exempel hur du tar bort och därmed dis-associerar nätverkssäkerhetsgruppen från undernätet:

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Remove-AzureNetworkSecurityGroupFromSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

## <a name="special-considerations-for-explicit-ip-ssl"></a>Särskilda överväganden för explicit IP-SSL
Om en app är konfigurerad med en explicit IP-SSL-adress (gäller *endast* för ASE:er som har en offentlig VIP) i stället för att använda standard-IP-adressen för App Service-miljön, flödar både HTTP- och HTTPS-trafik in i undernätet över en annan uppsättning portar än portar 80 och 443.

Det enskilda paret portar som används av varje IP-SSL-adress finns i portalanvändargränssnittet från App Service-miljöns informations-UX-blad.  Välj "Alla inställningar" --> "IP-adresser".  Bladet "IP-adresser" visar en tabell över alla uttryckligen konfigurerade IP-SSL-adresser för App Service-miljön, tillsammans med det speciella portpar som används för att dirigera HTTP- och HTTPS-trafik som är associerad med varje IP-SSL-adress.  Det är det här portparet som måste användas för DestinationPortRange-parametrarna när du konfigurerar regler i en nätverkssäkerhetsgrupp.

När en app på en ASE är konfigurerad för att använda IP-SSL, kommer externa kunder inte att se och behöver inte oroa sig för den speciella portparmappningen.  Trafiken till apparna flödar normalt till den konfigurerade IP-SSL-adressen.  Översättningen till specialportparet sker automatiskt internt under den sista delen av routningstrafiken till undernätet som innehåller ASE. 

## <a name="getting-started"></a>Komma igång
Mer om du vill komma igång med App Service-miljöer finns i [Introduktion till App Service Environment][IntroToAppServiceEnvironment]

Mer information om appar som ansluter till serveringsresurser från en App Service-miljö finns i [Säker anslutning till Server-och-serverningsresurser från en App Service-miljö][SecurelyConnecttoBackend]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[SecurelyConnecttoBackend]:  app-service-app-service-environment-securely-connecting-to-backend-resources.md
[NewPortal]:  https://portal.azure.com  

<!-- IMAGES -->

