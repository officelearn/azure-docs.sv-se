---
title: Så här kontrollerar du inkommande trafik till en Apptjänst-miljö
description: Lär dig mer om hur du konfigurerar Nätverkssäkerhetsregler för att styra inkommande trafik till en Apptjänst-miljö.
services: app-service
documentationcenter: ''
author: ccompy
manager: erikre
editor: ''
ms.assetid: 4cc82439-8791-48a4-9485-de6d8e1d1a08
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2017
ms.author: stefsch
ms.openlocfilehash: ed72bf3202d6cb2d2161bc0df693d3e6a1fc58ef
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23836926"
---
# <a name="how-to-control-inbound-traffic-to-an-app-service-environment"></a>Så här kontrollerar du inkommande trafik till en Apptjänst-miljö
## <a name="overview"></a>Översikt
En Apptjänst-miljö kan skapas i **antingen** ett virtuellt nätverk i Azure Resource Manager **eller** klassiska distributionsmodellen [virtuellt nätverk][virtualnetwork].  Ett nytt virtuellt nätverk och ett nytt undernät kan definieras när en Apptjänst-miljö skapas.  Alternativt kan en Apptjänst-miljö skapas i ett befintligt virtuellt nätverk och befintlig undernät.  Med en ändring som gjorts i juni 2016 distribueras ASEs också till virtuella nätverk som använder offentliga-adressintervall eller RFC1918 adressutrymmen (d.v.s. privata adresser).  Mer information om hur du skapar en Apptjänst-miljö finns [så här skapar du en Apptjänst-miljö][HowToCreateAnAppServiceEnvironment].

En Apptjänst-miljö måste alltid skapas i ett undernät eftersom ett undernät ger en nätverksgräns som kan användas för att låsa inkommande trafik bakom överordnade enheter och tjänster så att HTTP och HTTPS-trafik tillåts endast från specifika överordnade IP-adresser.

Inkommande och utgående nätverkstrafik på ett undernät kontrolleras med en [nätverkssäkerhetsgruppen][NetworkSecurityGroups]. Kontrollera inkommande trafik kräver skapar Nätverkssäkerhetsregler i en nätverkssäkerhetsgrupp och sedan tilldela nätverkssäkerheten gruppen undernät som innehåller Apptjänst-miljön.

När en nätverkssäkerhetsgrupp tilldelas till ett undernät, inkommande trafik till appar i Apptjänst-miljön tillåtet/blockeras baserat på Tillåt och neka regler som definierats i nätverkssäkerhetsgruppen.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="inbound-network-ports-used-in-an-app-service-environment"></a>Inkommande nätverksportar som används i en Apptjänst-miljö
Innan du låsa inkommande nätverkstrafik med en säkerhetsgrupp för nätverk är det viktigt att veta uppsättning obligatoriska och valfria nätverksportar som används av en Apptjänst-miljö.  Stänger av misstag av trafik till vissa portar kan resultera i förlust av funktioner i en Apptjänst-miljö.

Följande är en lista över portar som används av en Apptjänst-miljö. Alla portar **TCP**, såvida inte annat anges tydligt:

* 454: **krävs port** används av Azure-infrastrukturen för att hantera och underhålla Apptjänstmiljöer via SSL.  Inte blockera trafik till den här porten.  Den här porten är alltid bunden till en ASE offentliga VIP.
* 455: **krävs port** används av Azure-infrastrukturen för att hantera och underhålla Apptjänstmiljöer via SSL.  Inte blockera trafik till den här porten.  Den här porten är alltid bunden till en ASE offentliga VIP.
* 80: standardport för inkommande HTTP-trafik till appar som körs i App Service-planer i en Apptjänst-miljö.  Den här porten är bunden till ILB-adressen för ASE på en ILB-aktiverade ASE.
* 443: standardport för inkommande SSL-trafik till appar som körs i App Service-planer i en Apptjänst-miljö.  Den här porten är bunden till ILB-adressen för ASE på en ILB-aktiverade ASE.
* 21: kontrollkanal för FTP.  Den här porten blockeras på ett säkert sätt om FTP inte används.  Den här porten kan vara kopplat till ILB-adress på en ILB-aktiverade ASE för en ASE.
* 990: kontrollkanal för FTPS.  Den här porten blockeras på ett säkert sätt om FTPS inte används.  Den här porten kan vara kopplat till ILB-adress på en ILB-aktiverade ASE för en ASE.
* 10001 10020: datakanaler för FTP.  Precis som med kontrollkanalen, blockeras portarna på ett säkert sätt om FTP inte används.  Den här porten kan vara kopplat till den ASE ILB-adress på en ILB-aktiverade ASE.
* 4016: används för fjärrfelsökning med Visual Studio 2012.  Den här porten blockeras på ett säkert sätt om funktionen inte används.  Den här porten är bunden till ILB-adressen för ASE på en ILB-aktiverade ASE.
* 4018: används för fjärrfelsökning med Visual Studio 2013.  Den här porten blockeras på ett säkert sätt om funktionen inte används.  Den här porten är bunden till ILB-adressen för ASE på en ILB-aktiverade ASE.
* 4020: används för fjärrfelsökning med Visual Studio 2015.  Den här porten blockeras på ett säkert sätt om funktionen inte används.  Den här porten är bunden till ILB-adressen för ASE på en ILB-aktiverade ASE.

## <a name="outbound-connectivity-and-dns-requirements"></a>Utgående anslutning och DNS-krav
För en Apptjänstmiljö ska fungera korrekt kräver också utgående åtkomst till olika slutpunkter. En fullständig lista över de externa slutpunkter som används av en ASE finns i avsnittet ”krävs nätverksanslutning” i den [nätverkskonfigurationen för ExpressRoute](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) artikel.

Apptjänstmiljöer kräver ett giltigt DNS-infrastruktur konfigurerad för det virtuella nätverket.  Om DNS-konfigurationen har ändrats efter en Apptjänst-miljö har skapats av någon anledning, kan utvecklare tvinga en Apptjänst-miljö för den nya DNS-konfigurationen.  Utlöser en rullande miljö omstart med hjälp av ikonen ”Restart” som finns längst upp på bladet hantering av Apptjänst-miljö i det [Azure-portalen] [ NewPortal] kommer miljö för att hämta den nya DNS-konfigurationen.

Vi rekommenderar också att alla anpassade DNS-servrar för det virtuella nätverket konfigureras i förväg innan du skapar en Apptjänst-miljö.  Om DNS-konfiguration för ett virtuellt nätverk har ändrats medan en Apptjänst-miljö skapas, vilket leder till att Apptjänstmiljö skapa processen misslyckas.  I en liknande vein om en anpassad DNS-server finns på den andra änden av en VPN-gateway och DNS-servern är felaktig eller inte tillgänglig, misslyckas Apptjänst-miljö skapas också.

## <a name="creating-a-network-security-group"></a>Skapa en Nätverkssäkerhetsgrupp
Mer information om hur nätverkssäkerhet grupper arbetar finns följande [information][NetworkSecurityGroups].  Azure Service Management som exemplet nedan vidrör på visar för nätverkssäkerhetsgrupper med fokus på Konfigurera och tillämpa en nätverkssäkerhetsgrupp till ett undernät som innehåller en Apptjänst-miljö.

**Obs:** nätverkssäkerhetsgrupper kan konfigureras med grafiskt den [Azure Portal](https://portal.azure.com) eller via Azure PowerShell.

Nätverkssäkerhetsgrupper skapas först som en fristående enhet som är associerad med en prenumeration. Se till att nätverkssäkerhetsgruppen skapas i samma region som Apptjänst-miljön eftersom nätverkssäkerhetsgrupper skapas i en Azure-region.

Följande visar hur du skapar en nätverkssäkerhetsgrupp:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

När du har skapat en nätverkssäkerhetsgrupp läggs en eller flera Nätverkssäkerhetsregler till den.  Eftersom uppsättningen regler kan förändras över tid, rekommenderas att sprida ut numrering schemat som används för prioriteter för att göra det enkelt att infoga ytterligare regler över tid.

Exemplet nedan visar en regel som uttryckligen beviljar åtkomst till management-portar som krävs av Azure-infrastrukturen att hantera och underhålla en Apptjänst-miljö.  Observera att alla hantering av trafik som flödar över SSL och skyddas av klientcertifikat, så att även om portarna är öppna de är tillgängligt som en annan entitet än Azure hanteringsinfrastruktur.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP


När låsa åtkomst till port 80 och 443 ”dölja” en Apptjänst-miljö bakom överordnade enheter eller tjänster behöver du känna till överordnade IP-adressen.  Till exempel om du använder en brandvägg för webbaserade program (Brandvägg) på Brandvägg har sin egen IP-adress (eller adresserna) som används när proxyanslutning trafik till en underordnad Apptjänstmiljö.  Du måste använda den här IP-adressen på den *SourceAddressPrefix* parametern för en nätverkssäkerhetsregeln.

Inkommande trafik från en specifik överordnade IP-adress är uttryckligen tillåtet i exemplet nedan.  Adressen *1.2.3.4* används som platshållare för IP-adressen för en överordnad Brandvägg.  Ändra värdet så att den matchar den adress som används av din överordnade enhet eller tjänst.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTP" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTPS" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Eventuellt FTP-stöd kan följande regler användas som en mall för att bevilja åtkomst till FTP-kontrollen porten och data channel-portar.  Eftersom FTP är ett tillståndskänslig protokoll, kan du kanske inte skicka FTP-trafik via en traditionell HTTP/HTTPS brandvägg eller proxyserver enhet.  I det här fallet måste du ange den *SourceAddressPrefix* till ett annat värde - till exempel IP-adressintervall utvecklare eller distribution datorer som kör på vilken FTP-klienter. 

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPCtrl" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '21' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPDataRange" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '10001-10020' -Protocol TCP

(**Obs:** portintervall för datakanal kan ändras i förhandsversionen.)

Om fjärrfelsökning med Visual Studio används visar följande regler hur du bevilja åtkomst.  Det finns en separat regel för varje version av Visual Studio som stöds eftersom varje version använder en annan port för fjärrfelsökning.  Precis som med FTP-åtkomst kan remote felsökning inte trafiken korrekt via en traditionell Brandvägg eller proxyserver enhet.  Den *SourceAddressPrefix* kan anges i stället till IP-adressintervall developer-datorer som kör Visual Studio.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2012" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4016' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2013" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4018' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2015" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4020' -Protocol TCP

## <a name="assigning-a-network-security-group-to-a-subnet"></a>Tilldela en Nätverkssäkerhetsgrupp till ett undernät
En nätverkssäkerhetsgrupp har en standardsäkerhetsregel som nekar åtkomst till alla externa trafiken.  Resultatet från att kombinera Nätverkssäkerhetsregler som beskrivs ovan och standardsäkerhetsregel som blockerar inkommande trafik, är att endast trafik från käll-adressintervallen som är associerade med en *Tillåt* åtgärden kommer att kunna skicka trafik till appar som körs i en Apptjänst-miljö.

När en nätverkssäkerhetsgrupp fylls med säkerhetsregler måste den tilldelas det undernät som innehåller Apptjänst-miljön.  Kommandot tilldelningen referenser både namnet på det virtuella nätverket där Apptjänst-miljön finns, samt namnet på det undernät där Apptjänst-miljön har skapats.  

Exemplet nedan visar en nätverkssäkerhetsgrupp som tilldelas till ett undernät och virtuella nätverk:

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

När grupptilldelningen network security lyckas (tilldelningen är en långvariga åtgärder och kan ta några minuter att slutföra) bara inkommande trafik matchar *Tillåt* regler ska komma åt appar i Apptjänst-miljön.

I följande exempel visas hur du tar bort och därför dis-associerar nätverkssäkerhetsgruppen från undernätet för fullständighetens skull:

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Remove-AzureNetworkSecurityGroupFromSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

## <a name="special-considerations-for-explicit-ip-ssl"></a>Speciella överväganden vid Explicit IP-SSL
Om en app har konfigurerats med en explicit SSL-IP-adress (tillämpliga *endast* till ASEs som har en offentlig VIP), istället för att använda standard IP-adressen för Apptjänst-miljön, både HTTP och HTTPS-trafik flödar till undernätet via en annan uppsättning andra portar än portarna 80 och 443.

Enskilda paret med portar som används av varje SSL-IP-adress kan hittas i portalens användargränssnitt från den Apptjänstmiljö UX informationsbladet.  Välj ”alla inställningar”--> ”IP-adresser”.  ”IP-adresser” bladet visar en tabell med alla uttryckligen konfigurerade SSL-IP-adresser för Apptjänst-miljön, tillsammans med särskilda port-par som används för att dirigera HTTP och HTTPS-trafik som är associerade med varje SSL-IP-adress.  Det är här porten två som ska användas för parametrarna DestinationPortRange när du konfigurerar regler i en nätverkssäkerhetsgrupp.

När en app på en ASE är konfigurerad för att använda IP-SSL, visas inte externa kunder och behöver inte bry dig om särskilda port par mappningen.  Trafik till apparna flödar normalt till den konfigurerade SSL-IP-adressen.  Översättning till särskilda port paret sker automatiskt internt under den sista delen av dirigera trafiken till undernätet som innehåller ASE. 

## <a name="getting-started"></a>Komma igång
Kom igång med Apptjänstmiljöer finns [introduktion till Apptjänst-miljö][IntroToAppServiceEnvironment]

Mer information kring appar som ansluter till backend-resursen på ett säkert sätt från en Apptjänst-miljö finns [säker anslutning till serverdelsresurser från en Apptjänst-miljö][SecurelyConnecttoBackend]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[SecurelyConnecttoBackend]:  app-service-app-service-environment-securely-connecting-to-backend-resources.md
[NewPortal]:  https://portal.azure.com  

<!-- IMAGES -->

