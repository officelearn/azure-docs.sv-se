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
ms.topic: tutorial
ms.date: 08/17/2016
ms.author: naziml
ms.custom: mvc
ms.openlocfilehash: bfe36ee5365e71db4280e8e2ccff6db8e552dd39
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2017
---
# <a name="configuring-a-web-application-firewall-waf-for-app-service-environment"></a>Konfigurera en brandvägg för webbaserade program (Brandvägg) för Apptjänst-miljö
## <a name="overview"></a>Översikt
Web application brandväggar som den [Barracuda Brandvägg för Azure](https://www.barracuda.com/programs/azure) som är tillgänglig på den [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/barracudanetworks/waf-byol/) skyddar dina webbprogram genom att kontrollera inkommande webbtrafik att blockera SQL injektionerna globala webbplatsskript, skadlig kod överföringar & DDoS-program och andra attacker. Den kontrollerar också svar från backend-webbservrar för Data går förlorade förebyggande (DLP). I kombination med isolering och ytterligare skalning som tillhandahålls av Apptjänstmiljöer, ger detta en perfekt miljö till värden kritiska web affärsprogram som måste klara skadliga begäranden och hög trafik.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="setup"></a>Konfiguration
För det här dokumentet konfigurerar vi Apptjänstmiljö bakom Utjämning av nätverksbelastning alla instanser av Barracuda Brandvägg så att endast trafik från Brandvägg kan nå Apptjänst-miljön och den inte är tillgänglig från DMZ: N. Vi har även Azure Traffic Manager framför Barracuda Brandvägg instanser kan belastningsutjämna mellan Azure-datacenter och regioner. Ett Översiktsdiagram över installationen ser ut som följande bild:

![Arkitektur][Architecture] 

> [!NOTE]
> Med introduktionen av [ILB stöd för Apptjänst-miljö](app-service-environment-with-internal-load-balancer.md), kan du konfigurera ASE för att vara tillgänglig från Perimeternätverket och bara är tillgänglig för det privata nätverket. 
> 
> 

## <a name="configuring-your-app-service-environment"></a>Konfigurera din Apptjänst-miljö
Om du vill konfigurera en Apptjänst-miljö, referera till [vår dokumentation](app-service-web-how-to-create-an-app-service-environment.md) om ämnet. När du har en Apptjänst-miljö skapas kan du skapa Webbappar, API Apps och [Mobilappar](../../app-service-mobile/app-service-mobile-value-prop.md) i den här miljön kommer alla skyddas bakom en Brandvägg som konfigureras i nästa avsnitt.

## <a name="configuring-your-barracuda-waf-cloud-service"></a>Konfigurera Barracuda Brandvägg Molntjänsten
Barracuda har en [detaljerad artikel](https://campus.barracuda.com/product/webapplicationfirewall/article/WAF/DeployWAFInAzure) om distribution av dess Brandvägg på en virtuell dator i Azure. Men eftersom vi vill redundans och inte inför en enskild felpunkt som du vill distribuera minst två Brandvägg instans virtuella datorer i samma molntjänst när följa dessa anvisningar.

### <a name="adding-endpoints-to-cloud-service"></a>Att lägga till slutpunkter molntjänst
När du har 2 eller mer Brandvägg VM-instanser i Molntjänsten, kan du använda den [Azure-portalen](https://portal.azure.com/) att lägga till HTTP och HTTPS-slutpunkter som används av programmet som visas i följande bild:

![Konfigurera slutpunkt][ConfigureEndpoint]

Om dina program använder slutpunkter, se till att lägga till dem i listan samt. 

### <a name="configuring-barracuda-waf-through-its-management-portal"></a>Konfigurera Barracuda Brandvägg via dess hanteringsportalen
Barracuda Brandvägg använder TCP-Port 8000 för konfigurationen med hjälp av dess hanteringsportalen. Om du har flera instanser av de virtuella datorerna Brandvägg kan behöva du upprepa de här stegen för varje VM-instans. 

> [!NOTE]
> När du är klar med konfigurationen av Brandvägg, kan du ta bort TCP/8000 slutpunkten från alla din Brandvägg virtuella datorer för att skydda din Brandvägg.
> 
> 

Lägg till management-slutpunkt som visas i följande bild för att konfigurera din Brandvägg Barracuda.

![Lägg till slutpunkt för hantering][AddManagementEndpoint]

Använda en webbläsare för att bläddra till management-slutpunkten på Molntjänsten. Om din molntjänst anropas test.cloudapp.net, skulle du åtkomst till den här slutpunkten genom att bläddra till http://test.cloudapp.net:8000. Du bör se en inloggningssida som på följande bild som du kan logga in med autentiseringsuppgifterna som du angav i installationsfasen Brandvägg VM.

![Hantering av inloggningssidan][ManagementLoginPage]

Du bör se en instrumentpanel som det i följande bild som visar grundläggande statistik om Brandvägg skydd när du loggar in.

![Instrumentpanel för hantering][ManagementDashboard]

Klicka på den **Services** fliken kan du konfigurera din Brandvägg för tjänster som skyddas. Mer information om hur du konfigurerar din Brandvägg Barracuda finns [deras dokumentation](https://techlib.barracuda.com/waf/getstarted1). I följande exempel visas har en Azure-Webbapp som betjänar trafik via HTTP och HTTPS konfigurerats.

![Hantering av lägga till tjänster][ManagementAddServices]

> [!NOTE]
> Beroende på hur dina program är konfigurerade och vilka funktioner som används i din Apptjänst-miljö kan behöver du vidarebefordra trafik för TCP andra portar än 80 och 443, till exempel om du har IP SSL-inställningar för ett webbprogram. En lista över nätverksportar som används i Apptjänstmiljöer finns [kontroll för inkommande trafik dokumentationen](app-service-app-service-environment-control-inbound-traffic.md) nätverksportar avsnitt.
> 
> 

## <a name="configuring-microsoft-azure-traffic-manager-optional"></a>Konfigurera Microsoft Azure Traffic Manager (valfritt)
Om ditt program är tillgängligt i flera områden, och du vill läsa in balansera dem bakom [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md). Om du vill göra det, kan du lägga till en slutpunkt i den [Azure-portalen](https://portal.azure.com) med Molntjänsten namn för din Brandvägg i Traffic Manager-profilen som visas i följande bild. 

![Traffic Manager-slutpunkt][TrafficManagerEndpoint]

Om ditt program kräver autentisering, se till att du har en resurs som inte kräver någon autentisering för Traffic Manager att pinga tillgängligheten för ditt program. Du kan konfigurera URL-Adressen på den **Configuration** sidan i den [Azure-portalen](https://portal.azure.com) som visas i följande bild:

![Konfigurera Traffic Manager][ConfigureTrafficManager]

För att vidarebefordra Traffic Manager-ping från din Brandvägg till ditt program, måste du konfigurera webbplatsen översättningar på din Brandvägg Barracuda att vidarebefordra trafik till tillämpningsprogrammet som visas i följande exempel:

![Webbplatsen översättningar][WebsiteTranslations]

## <a name="securing-traffic-to-app-service-environment-using-network-security-groups-nsg"></a>Skydda trafik till Apptjänst-miljö med Nätverkssäkerhetsgrupper (NSG)
Följ den [kontroll för inkommande trafik dokumentationen](app-service-app-service-environment-control-inbound-traffic.md) information om begränsar trafiken till din Apptjänst-miljö från Brandvägg med VIP-adressen för din tjänst i molnet. Här är ett exempel Powershell-kommando för att utföra den här aktiviteten för TCP-port 80.

    Get-AzureNetworkSecurityGroup -Name "RestrictWestUSAppAccess" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP Barracuda" -Type Inbound -Priority 201 -Action Allow -SourceAddressPrefix '191.0.0.1'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP

Ersätt SourceAddressPrefix med virtuella IP-adress (VIP) för din Brandvägg Molntjänsten.

> [!NOTE]
> VIP-Adressen för din molntjänst ändras när du tar bort och återskapa Molntjänsten. Se till att uppdatera IP-adressen i nätverket resursgruppens namn när du gör. 
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
