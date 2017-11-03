---
title: "Konfigurera en brandvägg för webbaserade program (Brandvägg) för Apptjänst-miljö"
description: "Lär dig hur du konfigurerar en brandvägg för webbaserade program framför din Apptjänst-miljö."
services: app-service\web
documentationcenter: 
author: naziml
manager: erikre
editor: jimbe
ms.assetid: a2101291-83ba-4169-98a2-2c0ed9a65e8d
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2016
ms.author: naziml
ms.openlocfilehash: 4c0e2d649f71d7797efbfe2c8e93ea0c844152df
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="configuring-a-web-application-firewall-waf-for-app-service-environment"></a>Konfigurera en brandvägg för webbaserade program (Brandvägg) för Apptjänst-miljö
## <a name="overview"></a>Översikt
Web application brandväggar som den [Barracuda Brandvägg för Azure](https://www.barracuda.com/programs/azure) som är tillgänglig på den [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/barracudanetworks/waf-byol/) skyddar dina webbprogram genom att kontrollera inkommande webbtrafik att blockera SQL injektionerna globala webbplatsskript, skadlig kod överföringar & DDoS-program och andra attacker. Den kontrollerar också svar från backend-webbservrar för Data går förlorade förebyggande (DLP). I kombination med isolering och ytterligare skalning som tillhandahålls av Apptjänstmiljöer, ger detta en perfekt miljö till värden kritiska web affärsprogram som måste klara skadliga begäranden och hög trafik.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="setup"></a>Konfiguration
För det här dokumentet som vi konfigurerar balanserade våra Apptjänstmiljö bakom flera belastningen instanser av Barracuda Brandvägg så att endast trafik från Brandvägg kan nå Apptjänst-miljön och det inte är tillgänglig från Perimeternätverket. Vi har även Azure Traffic Manager framför våra Barracuda Brandvägg instanser kan belastningsutjämna mellan Azure-datacenter och regioner. En hög nivå diagram över installationen skulle se ut vad som anges nedan.

![Arkitektur][Architecture] 

> Obs: med introduktionen av [ILB stöd för Apptjänst-miljö](app-service-environment-with-internal-load-balancer.md), kan du konfigurera ASE för att vara tillgänglig från Perimeternätverket och bara är tillgänglig för det privata nätverket. 
> 
> 

## <a name="configuring-your-app-service-environment"></a>Konfigurera din Apptjänst-miljö
Så här konfigurerar du en Apptjänst-miljö finns i [vår dokumentation](app-service-web-how-to-create-an-app-service-environment.md) om ämnet. När du har en Apptjänst-miljö skapas kan du skapa Webbappar, API Apps och [Mobilappar](../../app-service-mobile/app-service-mobile-value-prop.md) i den här miljön kommer alla skyddas bakom en Brandvägg som konfigureras i nästa avsnitt.

## <a name="configuring-your-barracuda-waf-cloud-service"></a>Konfigurera Barracuda Brandvägg Molntjänsten
Barracuda har en [detaljerad artikel](https://campus.barracuda.com/product/webapplicationfirewall/article/WAF/DeployWAFInAzure) om distribution av dess Brandvägg på en virtuell dator i Azure. Men eftersom vi vill redundans och inte inför en enskild felpunkt som du vill distribuera minst 2 Brandvägg instans virtuella datorer i samma molntjänst när följa dessa anvisningar.

### <a name="adding-endpoints-to-cloud-service"></a>Att lägga till slutpunkter molntjänst
När du har 2 eller mer Brandvägg VM-instanser i din molntjänst kan du använda den [Azure-portalen](https://portal.azure.com/) att lägga till HTTP och HTTPS-slutpunkter som används av programmet som visas i bilden nedan.

![Konfigurera slutpunkt][ConfigureEndpoint]

Om dina program använder slutpunkter, se till att lägga till dem i listan samt. 

### <a name="configuring-barracuda-waf-through-its-management-portal"></a>Konfigurera Barracuda Brandvägg via dess hanteringsportalen
Barracuda Brandvägg använder TCP-Port 8000 för konfigurationen med hjälp av dess hanteringsportalen. Eftersom vi har flera instanser av de virtuella datorerna Brandvägg behöver du upprepa de här stegen för varje VM-instans. 

> Obs: När du är klar med konfigurationen av Brandvägg, ta bort slutpunkten TCP/8000 från alla din Brandvägg virtuella datorer för att skydda din Brandvägg.
> 
> 

Lägg till management-slutpunkt som visas i bilden nedan för att konfigurera din Brandvägg Barracuda.

![Lägg till slutpunkt för hantering][AddManagementEndpoint]

Använda en webbläsare för att bläddra till management-slutpunkten på Molntjänsten. Om din molntjänst anropas test.cloudapp.net, skulle du åtkomst till den här slutpunkten genom att bläddra till http://test.cloudapp.net:8000. Du bör se en inloggningssida som nedan kan logga in med autentiseringsuppgifterna som du angav i installationsfasen Brandvägg VM.

![Hantering av inloggningssidan][ManagementLoginPage]

När du loggar in bör du se en instrumentpanel som det i bilden nedan visas grundläggande statistik om Brandvägg skyddet.

![Instrumentpanel för hantering][ManagementDashboard]

Klicka på fliken tjänster kan du konfigurera din Brandvägg för tjänster som skyddas. Mer information om hur du konfigurerar din Brandvägg Barracuda finns [deras dokumentation](https://techlib.barracuda.com/waf/getstarted1). I exemplet nedan en Azure-Webbapp har som betjänar trafik via HTTP och HTTPS konfigurerats.

![Hantering av lägga till tjänster][ManagementAddServices]

> Obs: Beroende på hur dina program är konfigurerade och vilka funktioner som används i din Apptjänst-miljö, behöver du vidarebefordrar trafik för TCP andra portar än 80 och 443, t.ex. Om du har IP SSL-inställningar för ett webbprogram. En lista över nätverksportar som används i Apptjänstmiljöer, referera till [kontroll för inkommande trafik dokumentationen](app-service-app-service-environment-control-inbound-traffic.md) nätverksportar avsnitt.
> 
> 

## <a name="configuring-microsoft-azure-traffic-manager-optional"></a>Konfigurera Microsoft Azure Traffic Manager (valfritt)
Om ditt program är tillgängligt i flera områden, och du vill läsa in balansera dem bakom [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md). Att göra så att du kan lägga till en slutpunkt i den [klassiska Azure-portalen](https://manage.azure.com) med Molntjänsten namn för din Brandvägg i Traffic Manager-profilen som visas i bilden nedan. 

![Traffic Manager-slutpunkt][TrafficManagerEndpoint]

Om ditt program kräver autentisering, se till att du har en resurs som inte kräver någon autentisering för Traffic Manager att pinga tillgängligheten för ditt program. Du kan konfigurera URL: en i avsnittet Konfigurera på den [klassiska Azure-portalen](https://manage.azure.com) enligt nedan.

![Konfigurera Traffic Manager][ConfigureTrafficManager]

För att vidarebefordra Traffic Manager-ping från din Brandvägg till ditt program, måste du installationen webbplats översättningar på Barracuda-Brandvägg att vidarebefordra trafik till tillämpningsprogrammet som visas i exemplet nedan.

![Webbplatsen översättningar][WebsiteTranslations]

## <a name="securing-traffic-to-app-service-environment-using-network-security-groups-nsg"></a>Skydda trafik till Apptjänst-miljö med Nätverkssäkerhetsgrupper (NSG)
Följ den [kontroll för inkommande trafik dokumentationen](app-service-app-service-environment-control-inbound-traffic.md) information om begränsar trafiken till din Apptjänst-miljö från Brandvägg med VIP-adressen för din tjänst i molnet. Här är ett exempel Powershell-kommando för att utföra den här aktiviteten för TCP-port 80.

    Get-AzureNetworkSecurityGroup -Name "RestrictWestUSAppAccess" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP Barracuda" -Type Inbound -Priority 201 -Action Allow -SourceAddressPrefix '191.0.0.1'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP

Ersätt SourceAddressPrefix med virtuella IP-adress (VIP) för din Brandvägg Molntjänsten.

> Obs: VIP för Molntjänsten ändras när du tar bort och återskapa Molntjänsten. Se till att uppdatera IP-adressen i nätverket resursgruppens namn när du gör. 
> 
> 

<!-- IMAGES -->
[Architecture]: ./media/app-service-app-service-environment-web-application-firewall/Architecture.png
[ConfigureEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/ConfigureEndpoint.png
[AddManagementEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/AddManagementEndpoint.png
[ManagementAddServices]: ./media/app-service-app-service-environment-web-application-firewall/ManagementAddServices.png
[ManagementDashboard]: ./media/app-service-app-service-environment-web-application-firewall/ManagementDashboard.png
[ManagementLoginPage]: ./media/app-service-app-service-environment-web-application-firewall/ManagementLoginPage.png
[TrafficManagerEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/TrafficManagerEndpoint.png
[ConfigureTrafficManager]: ./media/app-service-app-service-environment-web-application-firewall/ConfigureTrafficManager.png
[WebsiteTranslations]: ./media/app-service-app-service-environment-web-application-firewall/WebsiteTranslations.png
