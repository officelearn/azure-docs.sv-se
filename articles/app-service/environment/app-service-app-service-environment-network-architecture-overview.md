---
title: Översikt över nätverksarkitekturen i App Service - miljöer i Azure
description: Översikt över arkitekturen i nätverket topologi ofApp Service-miljöer.
services: app-service
documentationcenter: ''
author: stefsch
manager: erikre
editor: ''
ms.assetid: 13d03a37-1fe2-4e3e-9d57-46dfb330ba52
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 0d7d4af46e54ad89e0d084cb15af13e56115e996
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53274181"
---
# <a name="network-architecture-overview-of-app-service-environments"></a>Översikt över nätverksarkitekturen i App Service-miljöer
## <a name="introduction"></a>Introduktion
App Service-miljöer skapas alltid i ett undernät till en [virtuellt nätverk] [ virtualnetwork] -appar som körs i en App Service Environment kan kommunicera med privata slutpunkter som finns i samma virtuella nätverkstopologi.  Eftersom kunder kan låsa delar av deras virtuell nätverksinfrastruktur, är det viktigt att förstå vilka typer av nätverk kommunikationsflöden som uppstår vid en App Service Environment.

## <a name="general-network-flow"></a>Allmänna Nätverksflödet
När en App Service Environment (ASE) använder en offentlig virtuell IP-adress (VIP) för appar, kommer all inkommande trafik på den offentliga VIP.  Detta omfattar HTTP och HTTPS-trafik för appar, samt annan trafik för FTP och felsökning fjärrfunktioner Azure hanteringsåtgärder.  En fullständig lista över specifika portar (obligatoriska och valfria) som är tillgängliga på det offentliga VIP finns i artikeln på [Kontrollera inkommande trafik] [ controllinginboundtraffic] till en App Service Environment. 

App Service-miljöer har också stöd för att köra apps som är bundna endast till ett virtuellt nätverk interna adressen, som även kallas ILB-adress (intern belastningsutjämnare).  Aktiverade ASE, HTTP och HTTPS-trafik för appar samt felsökning fjärranrop skannas ILB-adressen på en ILB.  För de vanligaste konfigurationerna för ILB ASE kommer också FTP/FTPS-trafik på ILB-adressen.  Men aktiverat Azure hanteringsåtgärder fortfarande flödar till portar 454/455 på offentlig VIP för en ILB ASE.

Diagrammet nedan visar en översikt över de olika nätverk för inkommande och utgående flödena för en App Service-miljö där apparna som är bundna till en offentlig virtuell IP-adress:

![Allmänt nätverk flöden][GeneralNetworkFlows]

En App Service Environment kan kommunicera med en mängd olika privata kunden slutpunkter.  Appar som körs i App Service Environment kan till exempel ansluta till databasservrar som körs på IaaS-datorer i samma virtuella nätverkstopologin.

> [!IMPORTANT]
> Titta på nätverksdiagrammet, distribueras ”andra Compute resurserna” i ett annat undernät från App Service Environment. Distribuera resurser i samma undernät med ASE blockerar anslutningen mellan ASE och resurserna (förutom för specifika intra-ASE routning). Distribuera till ett annat undernät i stället (i samma virtuella nätverk). App Service Environment kommer sedan att kunna ansluta. Det krävs ingen ytterligare konfiguration.
> 
> 

App Service-miljöer också kommunicera med Sql DB och Azure Storage resurser som krävs för att hantera och driva en App Service Environment.  Vissa av de Sql och Storage-resurser som en App Service Environment kommunicerar med finns i samma region som App Service Environment, medan andra finns i Azure-regioner.  Utgående anslutning till Internet måste därför alltid anges för en App Service Environment ska fungera korrekt. 

Nätverkssäkerhetsgrupper kan användas för att kontrollera inkommande trafik till undernätet eftersom en App Service Environment har distribuerats i ett undernät.  Mer information om hur du kontrollerar inkommande trafik till en App Service Environment finns följande [artikeln][controllinginboundtraffic].

Mer information om hur du tillåter utgående Internet-anslutning från en App Service-miljö finns i följande artikel om hur du arbetar med [Express Route][ExpressRoute].  Samma metod som beskrivs i artikeln gäller när du arbetar med plats-till-plats-anslutning med Tvingad tunneltrafik.

## <a name="outbound-network-addresses"></a>Utgående nätverksadresser
När en App Service Environment gör utgående samtal, är en IP-adress alltid kopplad till de utgående samtal.  Specifik IP-adress som används beror på om den slutpunkt som anropas är placerad i den virtuella nätverkstopologin eller utanför den virtuella nätverkstopologin.

Om den slutpunkt som anropas är **utanför** av den virtuella nätverkstopologin sedan den utgående adressen (även kallat den utgående NAT-adressen) som används är offentlig VIP för App Service Environment.  Den här adressen kan hittas i användargränssnittet för portalen för App Service Environment i egenskapsbladet.

![Utgående IP-adress][OutboundIPAddress]

Den här adressen kan också fastställas för ase-miljöer som bara har en offentlig VIP genom att skapa en app i App Service Environment och utför sedan en *nslookup* på appens adress. Den resulterande IP-adressen är såväl offentliga VIP-Adressen, som App Service Environment utgående NAT-adress.

Om den slutpunkt som anropas är **inuti** av den virtuella nätverkstopologin, den utgående adressen för den anropande appen kommer att interna IP-adressen för den enskilda beräkningsresurs som kör appen.  Men det är inte en beständig mappning av virtuellt nätverk interna IP-adresser till appar.  Appar kan flytta runt i olika beräkningsresurser och pool med tillgängliga databearbetning som resurser i en App Service Environment kan ändra på grund av skalningsåtgärder.

Men eftersom en App Service Environment finns alltid i ett undernät, är du säkert att den interna IP-adressen för en beräkningsresurs som kör en app ska alltid ligga inom CIDR-intervall för undernätet.  Därför när detaljerade ACL: er eller nätverkssäkerhetsgrupper används för att säkra åtkomst till andra slutpunkter i det virtuella nätverket, måste undernätsintervall som innehåller App Service Environment beviljas åtkomst.

I följande diagram visas de här koncepten i detalj:

![Utgående nätverksadresser][OutboundNetworkAddresses]

I ovanstående diagram:

* Eftersom offentlig VIP för App Service Environment är 192.23.1.2, som är den utgående IP-adressen som används för att göra anrop till ”Internet” slutpunkter.
* CIDR-intervall för som innehåller undernätet för App Service Environment är 10.0.1.0/26.  Andra slutpunkter i samma virtuella nätverksinfrastruktur visas anrop från appar som kommer från någonstans inom den här adressintervall.

## <a name="calls-between-app-service-environments"></a>Anrop mellan App Service-miljöer
Ett mer komplext scenario kan inträffa om du distribuerar flera App Service-miljöer i samma virtuella nätverk och kontrollera utgående samtal från en App Service-miljö till en annan App Service Environment.  Dessa typer av mellan App Service Environment anrop kommer också att behandlas som ”Internet”-anrop.

Följande diagram visar ett exempel på en arkitektur med appar på en App Service Environment (t.ex.) ”Ytterdörren” webbappar) anropa appar på en andra App Service Environment (t.ex. interna backend-API-appar inte avsedd att vara tillgänglig från Internet). 

![Anrop mellan App Service-miljöer][CallsBetweenAppServiceEnvironments] 

I exemplet ovan har App Service Environment ”ASE en” 192.23.1.2 utgående IP-adressen.  Om en app som körs på den här behandlas App Service Environment gör ett utgående anrop till en app som körs på en andra App Service Environment (”ASE två”) finns i samma virtuella nätverk, utgående anropet kommer att som ett ”Internet”-anrop.  Därmed visas nätverkstrafik som kommer på andra App Service Environment som kommer från 192.23.1.2 (dvs. inte adressintervallet i undernätet för den första App Service Environment).

Även om anrop mellan olika App Service-miljöer behandlas som ”Internet”-anrop, när båda App Service-miljöer finns i samma Azure-region nätverkstrafiken kommer förblir i regionala Azure-nätverket och inte fysiskt flöde över den offentliga Internet.  Du kan därför använda en nätverkssäkerhetsgrupp på undernätet för andra App Service Environment att endast tillåta inkommande anrop från den första App Service Environment (vars utgående IP-adress är 192.23.1.2), vilket säkerställer säker kommunikation mellan appen Service-miljöer.

## <a name="additional-links-and-information"></a>Ytterligare länkar och Information
Information om inkommande portar som används av App Service-miljöer och är tillgängliga att använda nätverkssäkerhetsgrupper för att kontrollera inkommande trafik [här][controllinginboundtraffic].

Information om hur du använder användaren definierats vägar för att bevilja utgående Internet-åtkomst till App Service-miljöer finns i den här [artikeln][ExpressRoute]. 

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/services/virtual-network/
[controllinginboundtraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[ExpressRoute]:  app-service-app-service-environment-network-configuration-expressroute.md

<!-- IMAGES -->
[GeneralNetworkFlows]: ./media/app-service-app-service-environment-network-architecture-overview/NetworkOverview-1.png
[OutboundIPAddress]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundIPAddress-1.png
[OutboundNetworkAddresses]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundNetworkAddresses-1.png
[CallsBetweenAppServiceEnvironments]: ./media/app-service-app-service-environment-network-architecture-overview/CallsBetweenEnvironments-1.png

