---
title: Kontrollera inkommande trafik till App Service - miljö i Azure
description: Läs mer om hur du konfigurerar Nätverkssäkerhetsregler för att styra inkommande trafik till en App Service Environment.
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
ms.custom: seodec18
ms.openlocfilehash: 84575dcb67845a074ce19cf9d819e1dda3f90e20
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62130797"
---
# <a name="how-to-control-inbound-traffic-to-an-app-service-environment"></a>Så här kontrollerar du inkommande trafik till App Service Environment
## <a name="overview"></a>Översikt
Du kan skapa en App Service Environment i **antingen** ett Azure Resource Manager-nätverk **eller** en klassisk distributionsmodell [virtuellt nätverk] [ virtualnetwork].  Ett nytt virtuellt nätverk och ett nytt undernät kan definieras när en App Service Environment har skapats.  Alternativt kan du skapa en App Service Environment i ett befintliga virtuellt nätverk och ett befintligt undernät.  Med en ändring som gjorts i juni 2016 kan ase-miljöer också distribueras till virtuella nätverk som använder offentliga adressintervall eller RFC1918 adressutrymmen (d.v.s. privata adresser).  Mer information om hur du skapar en App Service Environment finns [så här skapar du en App Service Environment][HowToCreateAnAppServiceEnvironment].

En App Service Environment måste alltid skapas i ett undernät eftersom ett undernät ger en nätverksgräns som kan användas för att låsa inkommande trafik bakom överordnade enheter och tjänster så att HTTP och HTTPS-trafik tillåts endast från specifika överordnade IP-adresser.

Inkommande och utgående nätverkstrafik på ett undernät kontrolleras med hjälp av en [nätverkssäkerhetsgrupp][NetworkSecurityGroups]. Kontrollera inkommande trafik kräver skapa Nätverkssäkerhetsregler i en grupp och sedan tilldela nätverkssäkerheten gruppen undernätet som innehåller App Service Environment.

När en nätverkssäkerhetsgrupp är kopplad till ett undernät, inkommande trafik till appar i App Service Environment är tillåtna/blockerade baserat på Tillåt och neka regler i nätverkssäkerhetsgruppen.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="inbound-network-ports-used-in-an-app-service-environment"></a>Inkommande nätverksportar som används i en App Service Environment
Innan du låsa inkommande trafik med en nätverkssäkerhetsgrupp är det viktigt att veta uppsättningen med obligatoriska och valfria nätverksportar som används av en App Service Environment.  Stänga av misstag av trafik till vissa portar kan leda till förlust av funktioner i en App Service Environment.

Här följer en lista över portar som används av en App Service Environment. Alla portar är **TCP**, om inget annat anges tydligt:

* 454:  **Krävs port** används av Azure-infrastrukturen för hantering och underhåll av App Service-miljöer via SSL.  Inte blockera trafik till den här porten.  Den här porten är alltid kopplad till det offentliga VIP för en ASE.
* 455:  **Krävs port** används av Azure-infrastrukturen för hantering och underhåll av App Service-miljöer via SSL.  Inte blockera trafik till den här porten.  Den här porten är alltid kopplad till det offentliga VIP för en ASE.
* 80:  Standardporten för inkommande HTTP-trafik till appar som körs i App Service-planer i en App Service Environment.  Den här porten är bunden till ILB-adressen på ase-miljön på en Apptjänstmiljö aktiverad ASE.
* 443: Standardporten för inkommande SSL-trafik till appar som körs i App Service-planer i en App Service Environment.  Den här porten är bunden till ILB-adressen på ase-miljön på en Apptjänstmiljö aktiverad ASE.
* 21:  Kontrollkanal för FTP.  Den här porten blockeras på ett säkert sätt om FTP inte används.  Den här porten kan vara kopplat till ILB-adressen på en Apptjänstmiljö aktiverad ASE för en ase-miljö.
* 990:  Kontrollkanal för FTPS.  Den här porten blockeras på ett säkert sätt om FTPS inte används.  Den här porten kan vara kopplat till ILB-adressen på en Apptjänstmiljö aktiverad ASE för en ase-miljö.
* 10001-10020: Datakanaler för FTP.  Precis som med kontrollkanal, blockeras portarna på ett säkert sätt om FTP inte används.  Den här porten kan vara kopplat till den ASE ILB-adress på en Apptjänstmiljö aktiverad ASE.
* 4016: Används för fjärrfelsökning med Visual Studio 2012.  Den här porten blockeras på ett säkert sätt om funktionen inte används.  Den här porten är bunden till ILB-adressen på ase-miljön på en Apptjänstmiljö aktiverad ASE.
* 4018: Används för fjärrfelsökning med Visual Studio 2013.  Den här porten blockeras på ett säkert sätt om funktionen inte används.  Den här porten är bunden till ILB-adressen på ase-miljön på en Apptjänstmiljö aktiverad ASE.
* 4020: Används för fjärrfelsökning med Visual Studio 2015.  Den här porten blockeras på ett säkert sätt om funktionen inte används.  Den här porten är bunden till ILB-adressen på ase-miljön på en Apptjänstmiljö aktiverad ASE.

## <a name="outbound-connectivity-and-dns-requirements"></a>Utgående anslutning och DNS-krav
För en App Service Environment ska fungera behövs också utgående åtkomst till olika slutpunkter. En fullständig lista över externa slutpunkter som används av en ASE är i avsnittet ”krävs nätverksanslutning” i den [nätverkskonfiguration för ExpressRoute](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) artikeln.

App Service kräver en giltig DNS-infrastruktur som konfigurerats för det virtuella nätverket.  Om DNS-konfigurationen har ändrats efter en App Service Environment har skapats av någon anledning, kan utvecklare tvinga en App Service Environment att den använder den nya DNS-konfigurationen.  Utlösa en löpande miljö omstart med hjälp av ”Restart”-ikonen finns högst upp på bladet för hantering i App Service Environment i den [Azure-portalen] [ NewPortal] leder till miljön att hämta nya DNS konfiguration.

Vi rekommenderar också att eventuella anpassade DNS-servrar på det virtuella nätverket konfigureras förbereds i förväg innan du skapar en App Service Environment.  Om DNS-konfiguration för ett virtuellt nätverk ändras medan en App Service Environment skapas, som resulterar i App Service Environment skapas processen misslyckas.  I en liknande vein om en anpassad DNS-server finns på den andra änden av en VPN-gateway och DNS-servern är inte kan nås eller otillgängliga misslyckas process för att skapa App Service Environment också.

## <a name="creating-a-network-security-group"></a>Skapa en Nätverkssäkerhetsgrupp
Fullständig information om hur nätverkssäkerhet grupper arbetar i följande [information][NetworkSecurityGroups].  Azure Service Management som exemplet nedan vidrör på visar för nätverkssäkerhetsgrupperna, med fokus på att konfigurera och använda en nätverkssäkerhetsgrupp för ett undernät som innehåller en App Service Environment.

**Obs!** Nätverkssäkerhetsgrupper kan konfigureras med grafiskt den [Azure-portalen](https://portal.azure.com) eller via Azure PowerShell.

Nätverkssäkerhetsgrupper skapas första gången som en fristående enhet som är associerade med en prenumeration. Eftersom nätverkssäkerhetsgrupper skapas i en Azure-region, se till att nätverkssäkerhetsgruppen har skapats i samma region som App Service Environment.

Följande visar hur du skapar en nätverkssäkerhetsgrupp:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

När en nätverkssäkerhetsgrupp skapas har en eller flera Nätverkssäkerhetsregler lagts till den.  Eftersom uppsättningen regler kan ändras över tid, rekommenderar vi att du utrymme ut numrering schemat för regeln prioriteter som gör det enkelt att infoga ytterligare regler med tiden.

Exemplet nedan visar en regel som uttryckligen beviljar åtkomst till hanteringsportar som krävs av Azure-infrastrukturen för att hantera och underhålla en App Service Environment.  Observera att alla trafik flödar över SSL och skyddas av klientcertifikat, så att även om portarna är öppna de är tillgängligt som en enhet än Azure hanteringsinfrastruktur.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP


När du skyddar åtkomst till port 80 och 443 ”dölja” App Service Environment bakom överordnade enheter eller tjänster, måste du känna till överordnade IP-adressen.  Till exempel om du använder en brandvägg för webbaserade program (WAF), WAF har sin egen IP-adress (eller adresser) som används när trafik via proxy till en underordnad App Service Environment.  Du måste använda den här IP-adressen i den *SourceAddressPrefix* -parametern för en regel för säkerhet.

I exemplet nedan tillåts uttryckligen inkommande trafik från en specifik överordnade IP-adress.  Adressen *1.2.3.4* används som en platshållare för IP-adressen för en överordnad WAF.  Ändra värdet för att matcha den adress som används av din överordnade enhet eller tjänst.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTP" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTPS" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Om FTP-stöd är det önskade kan kan följande regler användas som en mall för att bevilja åtkomst till FTP-kontroll porten och data channel-portar.  Eftersom FTP är ett tillståndskänsliga protokoll, kan du kanske inte dirigera FTP-trafik via en traditionell HTTP/HTTPS brandvägg eller proxyserver enhet.  I det här fallet måste du ange den *SourceAddressPrefix* på ett annat värde – till exempel IP-adressintervall för utvecklare eller distribution datorer vilken FTP-klienter kör. 

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPCtrl" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '21' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPDataRange" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '10001-10020' -Protocol TCP

(**Obs:** portintervall för datakanal kan ändras under förhandsperioden.)

Om fjärrfelsökning med Visual Studio används visar följande regler hur du bevilja åtkomst.  Det finns en separat regel för varje version av Visual Studio som stöds eftersom varje version använder olika portar för fjärrfelsökning.  Precis som med FTP-åtkomst, kan felsökning fjärrtrafik inte flödar korrekt via en traditionell WAF eller proxy-enheten.  Den *SourceAddressPrefix* kan i stället tilldelas IP-adressintervallet för utvecklare datorer som kör Visual Studio.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2012" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4016' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2013" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4018' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2015" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4020' -Protocol TCP

## <a name="assigning-a-network-security-group-to-a-subnet"></a>Tilldela en Nätverkssäkerhetsgrupp till ett undernät
En nätverkssäkerhetsgrupp har en standardsäkerhetsregel som nekar åtkomst till alla extern trafik.  Resultatet från att kombinera Nätverkssäkerhetsregler som beskrivs ovan och standardsäkerhetsregel som blockerar inkommande trafik är att endast trafik från käll-adressintervall som är associerade med en *Tillåt* åtgärden kommer att kunna skicka trafik till appar som körs i en App Service Environment.

När en nätverkssäkerhetsgrupp har fyllts i med säkerhetsregler för, måste den tilldelas till undernätet som innehåller App Service Environment.  Kommandot tilldelning refererar till både namnet på det virtuella nätverket där App Service Environment finns, samt namnet på undernätet där App Service Environment har skapats.  

Exemplet nedan visar en nätverkssäkerhetsgrupp som tilldelas till ett undernät och virtuellt nätverk:

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

När grupptilldelningen network security lyckas (tilldelningen är en långvariga åtgärder och kan ta några minuter att slutföra), bara inkommande trafik matchar *Tillåt* regler når har appar i App Service Miljö.

I följande exempel visas hur du ta bort och därför ej associerar nätverkssäkerhetsgruppen från undernätet för fullständighetens skull:

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Remove-AzureNetworkSecurityGroupFromSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

## <a name="special-considerations-for-explicit-ip-ssl"></a>Att tänka på för explicita IP-SSL
Om en app har konfigurerats med en explicit IP SSL-adress (tillämpliga *endast* till ase-miljöer som har en offentlig VIP), istället för att använda standard-IP-adressen för App Service Environment, både HTTP och HTTPS-trafik flöden till undernätet via en olika uppsättningar av andra portar än portarna 80 och 443.

De enskilda två portar som används av varje IP SSL-adress kan hittas i portalens användargränssnitt från App Service Environment UX informationsblad.  Select "All settings" --> "IP addresses".  Bladet ”IP-adresser” innehåller en tabell med alla explicit konfigurerade IP SSL-adresser för App Service Environment, tillsammans med särskilda port-par som används för att dirigera HTTP och HTTPS-trafik som är associerade med varje IP SSL-adress.  Det är den här porten-par som ska användas för DestinationPortRange parametrar när du konfigurerar regler i en grupp.

När en app på en ASE är konfigurerad för att använda IP-SSL, externa kunder ser inte och behöver inte bekymra dig om den särskilda portmappning par.  Trafik till apparna som flödar normalt till den konfigurerade IP SSL-adressen.  Översättningen till särskilda port paret händer automatiskt internt under den sista delen av dirigera trafiken till undernätet som innehåller ASE. 

## <a name="getting-started"></a>Komma igång
Kom igång med App Service-miljöer, se [introduktion till App Service Environment][IntroToAppServiceEnvironment]

Mer information kring appar på ett säkert sätt ansluta till backend-resurs från en App Service Environment finns [på ett säkert sätt ansluta till serverdelsresurser från en App Service Environment][SecurelyConnecttoBackend]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[SecurelyConnecttoBackend]:  app-service-app-service-environment-securely-connecting-to-backend-resources.md
[NewPortal]:  https://portal.azure.com  

<!-- IMAGES -->

