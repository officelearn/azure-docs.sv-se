---
title: Nätverksarkitektur v1
description: Arkitektonisk översikt över nätverkstopologi för App Service-miljöer. Det här dokumentet tillhandahålls endast för kunder som använder den äldre v1 ASE.
author: stefsch
ms.assetid: 13d03a37-1fe2-4e3e-9d57-46dfb330ba52
ms.topic: article
ms.date: 10/04/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: b1b866f3be789c59eea38c5c22b5557d557440be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243853"
---
# <a name="network-architecture-overview-of-app-service-environments"></a>Översikt över nätverksarkitektur för App Service-miljöer
App Service-miljöer skapas alltid i ett undernät till ett [virtuellt nätverk][virtualnetwork] - appar som körs i en App Service-miljö kan kommunicera med privata slutpunkter som finns i samma virtuella nätverkstopologi.  Eftersom kunder kan låsa delar av sin virtuella nätverksinfrastruktur är det viktigt att förstå vilka typer av nätverkskommunikationsflöden som uppstår med en App Service-miljö.

## <a name="general-network-flow"></a>Allmänt nätverksflöde
När en APP Service Environment (ASE) använder en offentlig virtuell IP-adress (VIP) för appar, anländer all inkommande trafik på den offentliga VIP.When an App Service Environment (ASE) uses a public virtual IP address (VIP) for apps, all inbound traffic arrives on that public VIP.  Detta inkluderar HTTP- och HTTPS-trafik för appar samt annan trafik för FTP, fjärrfelsökningsfunktioner och Azure-hanteringsåtgärder.  En fullständig lista över de specifika portar (både obligatoriska och valfria) som är tillgängliga på den offentliga VIP-webbplatsen finns i artikeln om [hur du kontrollerar inkommande trafik][controllinginboundtraffic] till en App Service-miljö. 

App servicemiljöer stöder också att appar körs som endast är bundna till en intern adress för ett virtuellt nätverk, även kallad en ILB-adress (intern belastningsutjämnare).  På en ILB-aktiverad ASE-, HTTP- och HTTPS-trafik för appar samt fjärrfelsökningsanrop anländer du till ILB-adressen.  För de vanligaste ILB-ASE-konfigurationerna kommer FTP/FTPS-trafiken också att anlända till ILB-adressen.  Azure-hanteringsåtgärder kommer dock fortfarande att flöda till portar 454/455 på den offentliga VIP-adressen för en ILB-aktiverad ASE.

Diagrammet nedan visar en översikt över de olika inkommande och utgående nätverksflödena för en App Service-miljö där apparna är bundna till en offentlig virtuell IP-adress:

![Allmänna nätverksflöden][GeneralNetworkFlows]

En App Service-miljö kan kommunicera med en mängd olika slutpunkter för privata kunder.  Appar som körs i App Service-miljön kan till exempel ansluta till databasservrar som körs på virtuella IaaS-datorer i samma virtuella nätverkstopologi.

> [!IMPORTANT]
> Om du tittar på nätverksdiagrammet distribueras "Andra beräkningsresurser" i ett annat undernät än App Service-miljön. Distribuera resurser i samma undernät med ASE blockerar anslutningen från ASE till dessa resurser (med undantag för specifik inom ASE-routning). Distribuera till ett annat undernät i stället (i samma virtuella nätverk). App servicemiljön kan sedan ansluta. Ingen ytterligare konfiguration är nödvändig.
> 
> 

App Service-miljöer kommunicerar också med Sql DB- och Azure Storage-resurser som krävs för att hantera och driva en App Service-miljö.  Vissa av de Sql- och Lagringsresurser som en App Service-miljö kommunicerar med finns i samma region som App Service-miljön, medan andra finns i fjärr-Azure-regioner.  Därför krävs alltid utgående anslutning till Internet för att en App Service-miljö ska fungera korrekt. 

Eftersom en App Service-miljö distribueras i ett undernät kan nätverkssäkerhetsgrupper användas för att styra inkommande trafik till undernätet.  Mer information om hur du kontrollerar inkommande trafik till en App Service-miljö finns i följande [artikel][controllinginboundtraffic].

Mer information om hur du tillåter utgående Internetanslutning från en App Service-miljö finns i följande artikel om hur du arbetar med [Express Route][ExpressRoute].  Samma metod som beskrivs i artikeln gäller när du arbetar med anslutning från plats till plats och användning av tvångstunneler.

## <a name="outbound-network-addresses"></a>Utgående nätverksadresser
När en apptjänstmiljö lämnar utgående samtal associeras alltid en IP-adress med de utgående samtalen.  Vilken IP-adress som används beror på om slutpunkten som anropas finns i den virtuella nätverkstopologin eller utanför den virtuella nätverkstopologin.

Om slutpunkten som anropas ligger **utanför** den virtuella nätverkstopologin är den utgående adressen (aka den utgående NAT-adressen) som används den offentliga VIP-posten för App Service-miljön.  Den här adressen finns i portalanvändargränssnittet för bladet App Service i Egenskaper.

![Utgående IP-adress][OutboundIPAddress]

Den här adressen kan också bestämmas för ASE:er som bara har en offentlig VIP genom att skapa en app i apptjänstmiljön och sedan utföra en *nslookup* på appens adress. Den resulterande IP-adressen är både den offentliga VIP, liksom App Service Environment utgående NAT-adress.

Om slutpunkten som anropas finns **i** den virtuella nätverkstopologin, kommer den utgående adressen för den anropande appen att vara den interna IP-adressen för den enskilda beräkningsresursen som kör appen.  Det finns dock inte en beständig mappning av interna IP-adresser för virtuella nätverk till appar.  Appar kan flytta runt mellan olika beräkningsresurser och poolen med tillgängliga beräkningsresurser i en App Service-miljö kan ändras på grund av skalningsåtgärder.

Men eftersom en App Service-miljö alltid finns i ett undernät, är du garanterad att den interna IP-adressen för en beräkningsresurs som kör en app alltid kommer att ligga inom CIDR-intervallet för undernätet.  När detaljerade ACL:er eller nätverkssäkerhetsgrupper används för att skydda åtkomst till andra slutpunkter i det virtuella nätverket måste därför undernätsområdet som innehåller App Service-miljön beviljas åtkomst.

Följande diagram visar dessa begrepp mer i detalj:

![Utgående nätverksadresser][OutboundNetworkAddresses]

I diagrammet ovan:

* Eftersom den offentliga VIP i App Service Environment är 192.23.1.2, det är den utgående IP-adress som används när du ringer samtal till "Internet" slutpunkter.
* CIDR-intervallet för det innehållande undernätet för App Service-miljön är 10.0.1.0/26.  Andra slutpunkter inom samma virtuella nätverksinfrastruktur ser samtal från appar som kommer från någonstans inom det här adressintervallet.

## <a name="calls-between-app-service-environments"></a>Samtal mellan apptjänstmiljöer
Ett mer komplext scenario kan uppstå om du distribuerar flera App Service-miljöer i samma virtuella nätverk och ringer utgående samtal från en App Service-miljö till en annan App Service-miljö.  Dessa typer av cross App Service Environment-samtal kommer också att behandlas som "Internet"-samtal.

Följande diagram visar ett exempel på en arkitektur i lager med appar på en App Service Environment (t.ex. webbappar för ytterdörren) som anropar appar på en andra App Service Environment (t.ex. interna serverdels-API-appar som inte är avsedda att vara tillgängliga från Internet). 

![Samtal mellan apptjänstmiljöer][CallsBetweenAppServiceEnvironments] 

I exemplet ovan har App Service Environment "ASE One" en utgående IP-adress på 192.23.1.2.  Om en app som körs i den här App Service-miljön ringer ett utgående samtal till en app som körs på en andra App Service-miljö ("ASE Two") som finns i samma virtuella nätverk, behandlas det utgående samtalet som ett "Internet"-samtal.  Det innebär att nätverkstrafiken som anländer till den andra App Service-miljön visas som ursprung från 192.23.1.2 (dvs. inte undernätsadressintervallet för den första App Service-miljön).

Även om anrop mellan olika App Service-miljöer behandlas som "Internet"-anrop, kommer nätverkstrafiken att finnas i det regionala Azure-nätverket när båda App Service-miljöerna finns i samma Azure-region och kommer inte att flöda fysiskt över offentliga Internet.  Därför kan du använda en nätverkssäkerhetsgrupp i undernätet i den andra App Service-miljön för att endast tillåta inkommande samtal från den första App Service-miljön (vars utgående IP-adress är 192.23.1.2), vilket säkerställer säker kommunikation mellan Appen Servicemiljöer.

## <a name="additional-links-and-information"></a>Ytterligare länkar och information
Information om inkommande portar som används av App Service-miljöer och använda nätverkssäkerhetsgrupper för att styra inkommande trafik finns [här][controllinginboundtraffic].

Information om hur du använder användardefinierade vägar för att bevilja utgående Internet-åtkomst till App Service-miljöer finns i den här [artikeln][ExpressRoute]. 

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/services/virtual-network/
[controllinginboundtraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[ExpressRoute]:  app-service-app-service-environment-network-configuration-expressroute.md

<!-- IMAGES -->
[GeneralNetworkFlows]: ./media/app-service-app-service-environment-network-architecture-overview/NetworkOverview-1.png
[OutboundIPAddress]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundIPAddress-1.png
[OutboundNetworkAddresses]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundNetworkAddresses-1.png
[CallsBetweenAppServiceEnvironments]: ./media/app-service-app-service-environment-network-architecture-overview/CallsBetweenEnvironments-1.png

