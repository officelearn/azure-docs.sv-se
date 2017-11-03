---
title: "Översikt över nätverksarkitekturen i App Service-miljöer"
description: "Översikt över arkitekturen i nätverket topologi ofApp miljöer."
services: app-service
documentationcenter: 
author: stefsch
manager: erikre
editor: 
ms.assetid: 13d03a37-1fe2-4e3e-9d57-46dfb330ba52
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2016
ms.author: stefsch
ms.openlocfilehash: 3362a55524da42914681db06b8d2c0da8df773d8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="network-architecture-overview-of-app-service-environments"></a>Översikt över nätverksarkitekturen i App Service-miljöer
## <a name="introduction"></a>Introduktion
Apptjänstmiljöer skapas alltid i ett undernät för en [virtuellt nätverk] [ virtualnetwork] -appar som körs i en Apptjänst-miljö kan kommunicera med privata slutpunkter i samma virtuella nätverkstopologin.  Eftersom kunder kan låsa delar av deras virtuella nätverksinfrastruktur, är det viktigt att förstå vilka typer av nätverksflöden för kommunikation som sker med en Apptjänst-miljö.

## <a name="general-network-flow"></a>Allmänna flödet för nätverk
När en App Service miljö (ASE) använder en offentlig virtuella IP-adress (VIP) för appar, kommer all inkommande trafik på den offentliga VIP.  Det inkluderar HTTP och HTTPS-trafik för appar, samt annan trafik för FTP, felsökning fjärrfunktioner och Azure hanteringsåtgärder.  En fullständig lista över specifika portar (obligatoriska och valfria) som är tillgängliga på det offentliga VIP finns i artikeln på [styra inkommande trafik] [ controllinginboundtraffic] till en Apptjänst-miljö. 

Apptjänstmiljöer stöder också körs appar som är bundna endast till en intern adress virtuellt nätverk, kallas även en adress för ILB (intern belastningsutjämnare).  På en ILB inkommer aktiverad ASE, HTTP och HTTPS-trafik för appar samt felsökning fjärranrop till ILB-adress.  För de vanligaste ILB ASE konfigurationer kommer också FTP-/ FTPS-trafik på ILB-adress.  Däremot aktiverad Azure hanteringsåtgärder flödar fortfarande till portar 454/455 på en ILB offentliga VIP ASE.

Diagrammet nedan visar en översikt över olika inkommande och utgående nätverksflöden för en Apptjänst-miljö där apparna som är bundna till en offentliga virtuella IP-adress:

![Allmänna nätverksflöden][GeneralNetworkFlows]

En Apptjänst-miljö kan kommunicera med en mängd olika privata kunden slutpunkter.  Appar som körs i Apptjänst-miljön kan till exempel ansluta till databasservrar som körs på IaaS-virtuella datorer i samma virtuella nätverkstopologin.

> [!IMPORTANT]
> Titta på nätverksdiagrammet distribueras ”andra Compute resurserna” i ett annat undernät från Apptjänst-miljön. Distribuera resurser i samma undernät med ASE blockerar anslutningen mellan ASE och resurserna (förutom för specifika intra-ASE routning). Distribuera till ett annat undernät i stället (i samma virtuella nätverk). Apptjänst-miljön kommer sedan att kunna ansluta. Det krävs ingen ytterligare konfiguration.
> 
> 

Apptjänstmiljöer också kommunicera med Sql-databas och Azure Storage resurser som krävs för att hantera och driva en Apptjänst-miljö.  Sql- och resurser som en Apptjänst-miljö kommunicerar med finns i samma region som Apptjänst-miljön, medan andra finns i Azure-regioner.  Därför krävs alltid utgående anslutning till Internet för en Apptjänstmiljö ska fungera korrekt. 

Eftersom en Apptjänst-miljö har distribuerats i ett undernät, användas nätverkssäkerhetsgrupper för att styra inkommande trafik till undernätet.  Mer information om hur du styr inkommande trafik till en Apptjänst-miljö, finns följande [artikel][controllinginboundtraffic].

Mer information om hur du tillåter utgående Internetanslutning från en Apptjänst-miljö, finns i följande artikel om hur du arbetar med [Expressroute][ExpressRoute].  Samma metod som beskrivs i artikeln gäller när du arbetar med plats-till-plats-anslutning med Tvingad tunneltrafik.

## <a name="outbound-network-addresses"></a>Utgående nätverksadresser
När en Apptjänst-miljö gör utgående samtal, är alltid en IP-adress kopplad till de utgående samtal.  Specifik IP-adress som används beror på om slutpunkten som anropas finns i virtuella nätverkets topologi eller utanför virtuella nätverkets topologi.

Om slutpunkten som anropas är **utanför** av den virtuella nätverkstopologin sedan utgående adressen (aka utgående NAT-adress) som används är det offentliga VIP för Apptjänst-miljön.  Den här adressen kan hittas i portalens användargränssnitt för Apptjänst-miljön i egenskapsbladet.

![Utgående IP-adress][OutboundIPAddress]

Den här adressen kan även fastställas för ASEs som bara har en offentlig VIP genom att skapa en app i Apptjänst-miljön och utför sedan en *nslookup* på appens adress. Den resulterande IP-adressen är både offentliga VIP samt den Apptjänstmiljö utgående NAT-adress.

Om slutpunkten som anropas är **i** för den virtuella nätverkstopologin utgående adressen till den anropande appen kommer att vara interna IP-adressen för den enskilda beräkningsresurser som kör appen.  Men det är inte en beständig mappning av virtuellt nätverk interna IP-adresser till appar.  Appar kan flytta över olika beräkningsresurser och pool med tillgängliga beräknings resurser i en Apptjänst-miljö kan ändras på grund av skalning åtgärder.

Eftersom en Apptjänst-miljö finns alltid i ett undernät kan garanteras du dock att interna IP-adressen för en beräkningsresurser som kör en app alltid ska ligga inom CIDR-intervall för undernätet.  Därför när detaljerade ACL: er och nätverkssäkerhetsgrupper används för att skydda åtkomst till andra slutpunkter i det virtuella nätverket, måste undernätsintervall som innehåller Apptjänst-miljön beviljas åtkomst.

Följande diagram visar dessa koncept i detalj:

![Utgående nätverksadresser][OutboundNetworkAddresses]

I ovanstående diagram:

* Eftersom det offentliga VIP för Apptjänst-miljön är 192.23.1.2, som är den utgående IP-adress som används när du anropar ”Internet” slutpunkter.
* CIDR-intervall som innehåller undernätet för Apptjänst-miljön är 10.0.1.0/26.  Slutpunkter inom samma infrastruktur för virtuellt nätverk ser anrop från appar som härrör från någonstans i den här adressintervallet.

## <a name="calls-between-app-service-environments"></a>Anrop mellan Apptjänstmiljöer
En mer komplex situationen kan uppstå om du distribuerar flera Apptjänstmiljöer i samma virtuella nätverk och utgående anrop från en Apptjänst-miljö till en annan Apptjänstmiljö.  Dessa typer av mellan Apptjänstmiljö anrop kommer också att behandlas som ”Internet” anrop.

Följande diagram visar ett exempel på en skiktad arkitektur med appar på en Apptjänst-miljö (t.ex.) ”Dörren” web apps) anropa appar på en andra Apptjänst-miljö (t.ex. interna backend-API-appar inte avsedd att vara tillgänglig från Internet). 

![Anrop mellan Apptjänstmiljöer][CallsBetweenAppServiceEnvironments] 

I exemplet ovan har Apptjänstmiljö ”ASE en” 192.23.1.2 utgående IP-adressen.  Om en app som körs på den här behandlas Apptjänst-miljön gör ett utgående anrop till en app som körs på en andra Apptjänst-miljö (”ASE två”) som finns i samma virtuella nätverk, utgående anropet kommer att som ett anrop för ”Internet”.  Därför visas den nätverkstrafik som inkommer på andra Apptjänstmiljö som härrör från 192.23.1.2 (d.v.s. inte undernät adressintervall första Apptjänst-miljön).

Även om anrop mellan olika Apptjänstmiljöer behandlas som ”Internet” anrop, när båda Apptjänstmiljöer befinner sig i samma Azure-region nätverkstrafiken finns kvar i regionala Azure-nätverk och kommer inte fysiskt flödar över offentligt Internet.  Du kan därför använda en nätverkssäkerhetsgrupp på undernätet för andra Apptjänst-miljön att endast tillåta inkommande samtal från den första Apptjänst-miljö (vars utgående IP-adress är 192.23.1.2), därför att säkerställa säker kommunikation mellan App Service-miljöer.

## <a name="additional-links-and-information"></a>Information och ytterligare länkar
Information om inkommande portar som används av Apptjänstmiljöer och använder nätverkssäkerhetsgrupper för att styra inkommande trafik finns [här][controllinginboundtraffic].

Information om hur du använder användaren definierat vägar att bevilja utgående Internetåtkomst till Apptjänstmiljöer är tillgänglig i det här [artikel][ExpressRoute]. 

<!-- LINKS -->
[virtualnetwork]: http://azure.microsoft.com/services/virtual-network/
[controllinginboundtraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[ExpressRoute]:  app-service-app-service-environment-network-configuration-expressroute.md

<!-- IMAGES -->
[GeneralNetworkFlows]: ./media/app-service-app-service-environment-network-architecture-overview/NetworkOverview-1.png
[OutboundIPAddress]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundIPAddress-1.png
[OutboundNetworkAddresses]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundNetworkAddresses-1.png
[CallsBetweenAppServiceEnvironments]: ./media/app-service-app-service-environment-network-architecture-overview/CallsBetweenEnvironments-1.png

