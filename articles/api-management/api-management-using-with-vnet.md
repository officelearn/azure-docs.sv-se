---
title: Använda Azure API Management med virtuella nätverk
description: Lär dig hur du konfigurerar en anslutning till ett virtuellt nätverk i Azure API Management och får tillgång till webbtjänster via den.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/09/2020
ms.author: apimpm
ms.openlocfilehash: 462a44f7766e0ec52ba7156d6de5ae5261e21376
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547369"
---
# <a name="how-to-use-azure-api-management-with-virtual-networks"></a>Använda Azure API Management med virtuella nätverk
Med virtuella Azure-nätverk (VNET) kan du placera valfria Azure-resurser i ett dirigerbart icke-Internetbaserat nätverk som du kontrollerar åtkomsten till. Dessa nätverk kan sedan anslutas till dina lokala nätverk med hjälp av olika VPN-tekniker. Om du vill veta mer om Azure Virtual Networks börja med informationen här: [Azure Virtual Network Overview](../virtual-network/virtual-networks-overview.md).

Azure API Management kan distribueras i det virtuella nätverket (VNET), så att den kan komma åt serverdtjänster i nätverket. Utvecklarportalen och API-gatewayen kan konfigureras så att de är tillgängliga antingen från Internet eller endast inom det virtuella nätverket.

> [!NOTE]
> API-importdokument-URL:en måste finnas på en allmänt tillgänglig internetadress.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Krav

Om du vill utföra stegen som beskrivs i den här artikeln måste du ha:

+ En aktiv Azure-prenumeration.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ En APIM-instans. Mer information finns i [Skapa en Azure API Management-instans](get-started-create-service-instance.md).

## <a name="enable-vnet-connection"></a><a name="enable-vpn"> </a>Aktivera VNET-anslutning

### <a name="enable-vnet-connectivity-using-the-azure-portal"></a>Aktivera VNET-anslutning med Azure-portalen

1. Gå till [Azure-portalen](https://portal.azure.com) för att hitta din API-hanteringsinstans. Sök efter och välj **API Management-tjänster**.

2. Välj din API Management-instans.

3. Välj **Virtuellt nätverk**.
4. Konfigurera API Management-instansen som ska distribueras i ett virtuellt nätverk.

    ![Menyn Virtuellt nätverk för API Management][api-management-using-vnet-menu]
5. Välj önskad åtkomsttyp:

    * **Av**: Detta är standard. API Management distribueras inte till ett virtuellt nätverk.

    * **Extern**: API Management gateway och utvecklarportal är tillgängliga från det offentliga internet via en extern belastningsutjämnare. Gatewayen kan komma åt resurser i det virtuella nätverket.

        ![Offentlig peering][api-management-vnet-public]

    * **Internt**: API Management gateway och utvecklarportal är endast tillgängliga inifrån det virtuella nätverket via en intern belastningsutjämnare. Gatewayen kan komma åt resurser i det virtuella nätverket.

        ![Privat peering][api-management-vnet-private]

6. Om du har valt **Externt** eller **Internt**visas en lista över alla regioner där API Management-tjänsten har etablerats. Välj en **plats**och välj sedan dess **virtuella nätverk** och **undernät**. Den virtuella nätverkslistan fylls med både klassiska och Resource Manager virtuella nätverk som är tillgängliga i dina Azure-prenumerationer som har konfigurerats i den region som du konfigurerar.

    > [!IMPORTANT]
    > När du distribuerar en Azure API Management-instans till ett Resource Manager-VNET måste tjänsten finnas i ett dedikerat undernät som inte innehåller några andra resurser förutom Azure API Management-instanser. Om ett försök görs att distribuera en Azure API Management-instans till ett Resurshanterarens VNET-undernät som innehåller andra resurser, misslyckas distributionen.

    Välj därefter **Tillämpa**. Sidan **Virtuellt nätverk** i API Management-instansen uppdateras med dina nya virtuella nätverks- och undernätsval.

    ![Välj VPN][api-management-setup-vpn-select]

7. I det övre navigeringsfältet väljer du **Spara**och väljer sedan **Använd nätverkskonfiguration**.

> [!NOTE]
> VIP-adressen för API Management-instansen ändras varje gång VNET aktiveras eller inaktiveras.
> VIP-adressen ändras också när API Management flyttas från **extern** till **intern**eller vice versa.
>

> [!IMPORTANT]
> Om du tar bort API Management från ett VNET eller ändrar det som distribueras i, kan det tidigare använda virtuella nätverket förbli låst i upp till sex timmar. Under den här perioden kommer det inte att vara möjligt att ta bort det virtuella nätverket eller distribuera en ny resurs till det. Det här problemet gäller för klienter som använder api-version 2018-01-01 och tidigare. Klienter som använder api-version 2019-01-01 och senare frigörs VNET så snart den associerade API Management-tjänsten har tagits bort.

## <a name="enable-vnet-connection-using-powershell-cmdlets"></a><a name="enable-vnet-powershell"> </a>Aktivera VNET-anslutning med PowerShell-cmdlets
Du kan också aktivera VNET-anslutning med PowerShell-cmdlets.

* **Skapa en API Management-tjänst i ett VNET:** Använd cmdlet [New-AzApiManagement](/powershell/module/az.apimanagement/new-azapimanagement) för att skapa en Azure API Management-tjänst i ett VNET.

* **Distribuera en befintlig API Management-tjänst i ett virtuellt nätverk:** Använd cmdlet [Update-AzApiManagementRegion](/powershell/module/az.apimanagement/update-azapimanagementregion) för att flytta en befintlig Azure API Management-tjänst i ett virtuellt nätverk.

## <a name="connect-to-a-web-service-hosted-within-a-virtual-network"></a><a name="connect-vnet"> </a>Ansluta till en webbtjänst som finns i ett virtuellt nätverk
När din API Management-tjänst är ansluten till VNET är det inte annorlunda att komma åt serverdtjänster inom den än att komma åt offentliga tjänster. Skriv bara in den lokala IP-adressen eller värdnamnet (om en DNS-server har konfigurerats för webbtjänsten) i **URL-fältet** för webbtjänster när du skapar ett nytt API eller redigerar ett befintligt.

![Lägg till API från VPN][api-management-setup-vpn-add-api]

## <a name="common-network-configuration-issues"></a><a name="network-configuration-issues"> </a>Vanliga problem vid konfiguration av nätverk
Nedan följer en lista över vanliga felkonfigurationsproblem som kan uppstå när API Management-tjänsten distribueras till ett virtuellt nätverk.

* **Anpassad DNS-serverkonfiguration:** API-hanteringstjänsten är beroende av flera Azure-tjänster. När API Management finns i ett VNET med en anpassad DNS-server måste den matcha värdnamnen för dessa Azure-tjänster. Följ [den här](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) vägledningen om anpassade DNS-inställningar. Se porttabellen nedan och andra nätverkskrav som referens.

> [!IMPORTANT]
> Om du planerar att använda en anpassad DNS-server för VNET bör du konfigurera det **innan** du distribuerar en API Management-tjänst till det. Annars måste du uppdatera API Management-tjänsten varje gång du ändrar DNS-servern genom att köra [åtgärden Använd nätverkskonfiguration](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/ApiManagementService/ApplyNetworkConfigurationUpdates)

* **Portar som krävs för API Management:** Inkommande och utgående trafik till undernätet där API Management distribueras kan styras med hjälp av [Nätverkssäkerhetsgrupp][Network Security Group]. Om någon av dessa portar inte är tillgängliga kanske API Management inte fungerar korrekt och kan bli otillgänglig. Att ha en eller flera av dessa portar blockerade är ett annat vanligt felkonfigurationsproblem när du använder API Management med ett VNET.

<a name="required-ports"> </a> När en API Management-tjänstinstans finns i ett VNET används portarna i följande tabell.

| Källa / Destinationsport(er) | Riktning          | Transportprotokoll |   [Tjänsttaggar](../virtual-network/security-overview.md#service-tags) <br> Källa / Destination   | Syfte (*)                                                 | Typ av virtuellt nätverk |
|------------------------------|--------------------|--------------------|---------------------------------------|-------------------------------------------------------------|----------------------|
| * / [80], 443                  | Inkommande            | TCP                | INTERNET / VIRTUAL_NETWORK            | Klientkommunikation till API Management                      | Extern             |
| * / 3443                     | Inkommande            | TCP                | ApiManagement / VIRTUAL_NETWORK       | Hanteringsslutpunkt för Azure-portalen och Powershell         | Extern & Internt  |
| * / 443                  | Utgående           | TCP                | VIRTUAL_NETWORK / Förvaring             | **Beroende av Azure Storage**                             | Extern & Internt  |
| * / 443                  | Utgående           | TCP                | VIRTUAL_NETWORK / AzureActiveDirectory | [Azure Active Directory](api-management-howto-aad.md) (i förekommande fall)                   | Extern & Internt  |
| * / 1433                     | Utgående           | TCP                | VIRTUAL_NETWORK / SQL                 | **Åtkomst till Azure SQL-slutpunkter**                           | Extern & Internt  |
| * / 5671, 5672, 443          | Utgående           | TCP                | VIRTUAL_NETWORK / EventHub            | Beroende för [princip för logg till händelsehubb](api-management-howto-log-event-hubs.md) och övervakningsagent | Extern & Internt  |
| * / 445                      | Utgående           | TCP                | VIRTUAL_NETWORK / Förvaring             | Beroende av Azure File Share för [GIT](api-management-configuration-repository-git.md)                      | Extern & Internt  |
| * / 1886                     | Utgående           | TCP                | VIRTUAL_NETWORK / AzureCloud            | Behövs för att publicera hälsostatus till Resurshälsa          | Extern & Internt  |
| * / 443                     | Utgående           | TCP                | VIRTUAL_NETWORK / AzureMonitor         | Publicera [diagnostikloggar och mått](api-management-howto-use-azure-monitor.md)                       | Extern & Internt  |
| * / 25                       | Utgående           | TCP                | VIRTUAL_NETWORK / INTERNET            | Ansluta till SMTP Relay för att skicka e-post                    | Extern & Internt  |
| * / 587                      | Utgående           | TCP                | VIRTUAL_NETWORK / INTERNET            | Ansluta till SMTP Relay för att skicka e-post                    | Extern & Internt  |
| * / 25028                    | Utgående           | TCP                | VIRTUAL_NETWORK / INTERNET            | Ansluta till SMTP Relay för att skicka e-post                    | Extern & Internt  |
| * / 6381 - 6383              | Inkommande & utgående | TCP                | VIRTUAL_NETWORK / VIRTUAL_NETWORK     | Få tillgång till Redis-tjänst för [hastighetsbegränsningsprinciper](api-management-access-restriction-policies.md#LimitCallRateByKey) mellan datorer         | Extern & Internt  |
| * / *                        | Inkommande            | TCP                | AZURE_LOAD_BALANCER / VIRTUAL_NETWORK | Belastningsutjämnad i Azure Infrastructure                          | Extern & Internt  |

>[!IMPORTANT]
> De portar som *syftet* är **fetstil** krävs för att API Management-tjänsten ska kunna distribueras. Om du blockerar de andra portarna försämras dock möjligheten att använda och övervaka tjänsten som körs.

+ **TLS-funktioner**: För att aktivera TLS/SSL-certifikatkedjebyggnad och validering behöver API Management-tjänsten Utgående nätverksanslutning till ocsp.msocsp.com, mscrl.microsoft.com och crl.microsoft.com. Det här beroendet krävs inte, om något certifikat som du överför till API Management innehåller hela kedjan till ca-roten.

+ **DNS Access**: Utgående åtkomst på port 53 krävs för kommunikation med DNS-servrar. Om det finns en anpassad DNS-server i andra änden av en VPN-gateway måste DNS-servern kunna nås från api-hanteringen av undernätet.

+ **Mått och hälsoövervakning:** Utgående nätverksanslutning till Azure Monitoring-slutpunkter, som matchar under följande domäner:

+ **Regional tjänsttaggar**": NSG-regler som tillåter utgående anslutning till lagrings-, SQL- och EventHubs-tjänsttaggar kan använda de regionala versionerna av dessa taggar som motsvarar den region som innehåller API Management-instansen (till exempel Storage.WestUS för en API Management-instans i regionen Västra USA). I distributioner med flera regioner bör NSG i varje region tillåta trafik till tjänsttaggarna för den regionen.

    | Azure-miljö | Slutpunkter                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Azure, offentlig      | <ul><li>gcs.prod.monitoring.core.windows.net(**ny)**</li><li>prod.warmpath.msftcloudes.com( att**vara föråldrad)**</li><li>shoebox2.metrics.microsoftmetrics.com(**ny)**</li><li>shoebox2.metrics.nsatc.net(**att vara föråldrad)**</li><li>prod3.metrics.microsoftmetrics.com(**ny)**</li><li>prod3.metrics.nsatc.net( att**vara föråldrad)**</li><li>prod3-black.prod3.metrics.microsoftmetrics.com(**ny)**</li><li>prod3-black.prod3.metrics.nsatc.net( att**vara föråldrad)**</li><li>prod3-red.prod3.metrics.microsoftmetrics.com(**ny)**</li><li>prod3-red.prod3.metrics.nsatc.net( att**vara föråldrad)**</li><li>prod.warm.ingestion.msftcloudes.com</li><li>`azure region`.warm.ingestion.msftcloudes.com var `East US 2` är eastus2.warm.ingestion.msftcloudes.com</li></ul> |
    | Azure Government  | <ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>shoebox2.metrics.microsoftmetrics.com(**ny)**</li><li>shoebox2.metrics.nsatc.net(**att vara föråldrad)**</li><li>prod3.metrics.microsoftmetrics.com(**ny)**</li><li>prod3.metrics.nsatc.net( att**vara föråldrad)**</li><li>prod5.prod.microsoftmetrics.com</li></ul>                                                                                                                                                                                                                                                |
    | Azure Kina 21Vianet     | <ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>shoebox2.metrics.microsoftmetrics.com(**ny)**</li><li>shoebox2.metrics.nsatc.net(**att vara föråldrad)**</li><li>prod3.metrics.microsoftmetrics.com(**ny)**</li><li>prod3.metrics.nsatc.net( att**vara föråldrad)**</li><li>prod5.prod.microsoftmetrics.com</li></ul>                                                                                                                                                                                                                                                |

>[!IMPORTANT]
> Ändringen av kluster ovan med DNS-zonen **.nsatc.net** till **.microsoftmetrics.com** är oftast en DNS-ändring. IP-adressen för klustret ändras inte.

+ **SMTP Relay:** Utgående nätverksanslutning för SMTP Relay, som `smtpi-co1.msn.com` `smtpi-ch1.msn.com`löser `smtpi-db3.msn.com` `smtpi-sin.msn.com` under värd-, , och`ies.global.microsoft.com`

+ **Utvecklarportalen CAPTCHA**: Utgående nätverksanslutning för utvecklarportalens CAPTCHA, `client.hip.live.com` `partner.hip.live.com`som försvinner under värdarna och .

+ **Azure portal Diagnostics**: För att aktivera flödet av diagnostikloggar från Azure-portalen när `dc.services.visualstudio.com` du använder API Management-tillägget inifrån ett virtuellt nätverk krävs utgående åtkomst till på port 443. Detta hjälper till att felsöka problem som du kan ställas inför när du använder tillägg.

+ **Tvinga tunneltrafik till on-prem-brandvägg med hjälp av Express Route eller Network Virtual Appliance**: En gemensam kundkonfiguration är att definiera sin egen standardväg (0.0.0.0/0) som tvingar all trafik från API Management-delegerat undernät att flöda genom en lokal brandvägg eller till en virtuell nätverksinstallation. Det här trafikflödet bryter alltid anslutningen med Azure API Management eftersom den utgående trafiken antingen blockeras lokalt eller NAT'd till en oigenkännlig uppsättning adresser som inte längre fungerar med olika Azure-slutpunkter. Lösningen kräver att du gör ett par saker:

  * Aktivera tjänstslutpunkter i undernätet där API Management-tjänsten distribueras. [Tjänstslutpunkter][ServiceEndpoints] måste aktiveras för Azure Sql, Azure Storage, Azure EventHub och Azure ServiceBus. Om du aktiverar slutpunkter direkt från API Management-delegerat undernät till dessa tjänster kan de använda Microsoft Azure-stamnätet som ger optimal routning för tjänsttrafik. Om du använder tjänstslutpunkter med en tvingad tunnel Api Management tvingas inte den ovannämnda Azure-tjänstens trafik tunnel. Den andra API Management-tjänstberoendetrafiken tvingas tunnelförs och kan inte gå förlorad eller api-hanteringstjänsten fungerar inte korrekt.
    
  * All trafik på kontrollplanet från Internet till hanteringsslutpunkten för din API Management-tjänst dirigeras via en specifik uppsättning inkommande IPs som tillhandahålls av API Management. När trafiken är force tunneled svaren kommer inte symmetriskt mappa tillbaka till dessa inkommande käll-IPs. För att övervinna begränsningen måste vi lägga till följande användardefinierade vägar[(UDRs)][UDRs]för att styra trafiken tillbaka till Azure genom att ange destinationen för dessa värdvägar till "Internet". Uppsättningen inkommande IP-adresser för kontrollPlanstrafik dokumenteras [IP-adresser för kontrollplan](#control-plane-ips)

  * För andra API Management-tjänstberoenden som är tvingande tunnel, bör det finnas ett sätt att lösa värdnamnet och nå ut till slutpunkten. Dessa inkluderar
      - Mått och hälsoövervakning
      - Diagnostik i Azure Portal
      - SMTP-relä
      - Utvecklarportalen CAPTCHA

## <a name="troubleshooting"></a><a name="troubleshooting"> </a>Felsökning
* **Inledande installation:** När den första distributionen av API Management-tjänsten i ett undernät inte lyckas, rekommenderas att först distribuera en virtuell dator till samma undernät. Nästa fjärrskrivbord till den virtuella datorn och verifiera att det finns anslutning till en av varje resurs nedan i din azure-prenumeration
    * Azure Storage-blob
    * Azure SQL Database
    * Azure-lagringstabell

  > [!IMPORTANT]
  > När du har validerat anslutningen måste du ta bort alla resurser som distribueras i undernätet innan du distribuerar API-hantering till undernätet.

* **Inkrementella uppdateringar**: När du gör ändringar i nätverket, se [NetworkStatus API](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/networkstatus), för att kontrollera att API Management-tjänsten inte har förlorat åtkomst till någon av de kritiska resurserna, vilket den är beroende av. Anslutningsstatusen bör uppdateras var 15:e minut.

* **Resursnavigeringslänkar:** När du distribuerar till Resource Manager-format vnet-undernät reserverar API Management undernätet genom att skapa en resursnavigeringslänk. Om undernätet redan innehåller en resurs från en annan provider **misslyckas**distributionen . När du flyttar en API Management-tjänst till ett annat undernät eller tar bort den tar vi också bort den resursnavigeringslänken.

## <a name="subnet-size-requirement"></a><a name="subnet-size"> </a> Krav på undernätsstorlek
Azure reserverar vissa IP-adresser i varje undernät och dessa adresser kan inte användas. De första och sista IP-adresserna för undernäten är reserverade för protokollkonformance, tillsammans med ytterligare tre adresser som används för Azure-tjänster. Mer information finns i [Finns det några begränsningar för att använda IP-adresser i dessa undernät?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Förutom de IP-adresser som används av Azure VNET-infrastrukturen använder varje Api Management-instans i undernätet två IP-adresser per enhet Premium SKU eller en IP-adress för Utvecklar-SKU. Varje instans reserverar ytterligare en IP-adress för den externa belastningsutjämnaren. När du distribuerar till internt virtuellt nätverk krävs ytterligare en IP-adress för den interna belastningsutjämnaren.

Med tanke på beräkningen över den minsta storleken på undernätet, där API Management kan distribueras är / 29 som ger tre användbara IP-adresser.

Varje ytterligare skalningsenhet för API Management kräver ytterligare två IP-adresser.

## <a name="routing"></a><a name="routing"> </a> Routning
+ En belastningsbalanserad offentlig IP-adress (VIP) kommer att reserveras för att ge åtkomst till alla tjänstslutpunkter.
+ En IP-adress från ett IP-intervall (SUBNET) kommer att användas för att komma åt resurser inom det virtuella nätverket och en offentlig IP-adress (VIP) kommer att användas för att komma åt resurser utanför det virtuella nätverket.
+ Belastningsbalanserad offentlig IP-adress finns på bladet Översikt/Essentials i Azure-portalen.

## <a name="limitations"></a><a name="limitations"> </a>Begränsningar
* Ett undernät som innehåller API Management-instanser kan inte innehålla andra Azure-resurstyper.
* Undernätet och API Management-tjänsten måste finnas i samma prenumeration.
* Ett undernät som innehåller API Management-instanser kan inte flyttas mellan prenumerationer.
* För API Management-distributioner med flera regioner som konfigurerats i internt virtuellt nätverksläge är användarna ansvariga för att hantera belastningsutjämningen i flera regioner, eftersom de äger routningen.
* Anslutning från en resurs i ett globalt peered VNET i en annan region till API Management-tjänsten i internt läge fungerar inte på grund av plattformsbegränsning. Mer information finns [i Resurser i ett virtuellt nätverk kan inte kommunicera med Azures interna belastningsutjämnare i peer-virtuellt nätverk](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)

## <a name="control-plane-ip-addresses"></a><a name="control-plane-ips"> </a> Styra plan-IP-adresser

IP-adresserna delas av **Azure Environment**. När ip-adress som markerats med **Global** tillåts tillåts måste IP-adressen vitlistas tillsammans med den **regionspecifika** IP-adressen.

| **Azure-miljö**|   **Regionen**|  **IP-adress**|
|-----------------|-------------------------|---------------|
| Azure, offentlig| Södra centrala USA (Global)| 104.214.19.224|
| Azure, offentlig| Norra centrala USA (Global)| 52.162.110.80|
| Azure, offentlig| USA, västra centrala| 52.253.135.58|
| Azure, offentlig| Sydkorea, centrala| 40.82.157.167|
| Azure, offentlig| Storbritannien, västra| 51.137.136.0|
| Azure, offentlig| Japan, västra| 40.81.185.8|
| Azure, offentlig| USA, norra centrala| 40.81.47.216|
| Azure, offentlig| Storbritannien, södra| 51.145.56.125|
| Azure, offentlig| Indien, västra| 40.81.89.24|
| Azure, offentlig| USA, östra| 52.224.186.99|
| Azure, offentlig| Europa, västra| 51.145.179.78|
| Azure, offentlig| Japan, östra| 52.140.238.179|
| Azure, offentlig| Frankrike, centrala| 40.66.60.111|
| Azure, offentlig| Kanada, östra| 52.139.80.117|
| Azure, offentlig| Uae Norra| 20.46.144.85|
| Azure, offentlig| Brasilien, södra| 191.233.24.179|
| Azure, offentlig| Sydostasien| 40.90.185.46|
| Azure, offentlig| Sydafrika North| 102.133.130.197|
| Azure, offentlig| Kanada, centrala| 52.139.20.34|
| Azure, offentlig| Sydkorea, södra| 40.80.232.185|
| Azure, offentlig| Indien, centrala| 13.71.49.1|
| Azure, offentlig| USA, västra| 13.64.39.16|
| Azure, offentlig| Australien, sydöstra| 20.40.160.107|
| Azure, offentlig| Australien, centrala| 20.37.52.67|
| Azure, offentlig| Indien, södra| 20.44.33.246|
| Azure, offentlig| USA, centrala| 13.86.102.66|
| Azure, offentlig| Australien, östra| 20.40.125.155|
| Azure, offentlig| USA, västra 2| 51.143.127.203|
| Azure, offentlig| Östra USA 2 EUAP| 52.253.229.253|
| Azure, offentlig| Centrala USA EUAP| 52.253.159.160|
| Azure, offentlig| USA, södra centrala| 20.188.77.119|
| Azure, offentlig| USA, östra 2| 20.44.72.3|
| Azure, offentlig| Europa, norra| 52.142.95.35|
| Azure, offentlig| Asien, östra| 52.139.152.27|
| Azure, offentlig| Frankrike, södra| 20.39.80.2|
| Azure, offentlig| Schweiz Väst| 51.107.96.8|
| Azure, offentlig| Australien, centrala 2| 20.39.99.81|
| Azure, offentlig| Centrala Förenade Arabemiraten| 20.37.81.41|
| Azure, offentlig| Schweiz Norr| 51.107.0.91|
| Azure, offentlig| Sydafrika Väst| 102.133.0.79|
| Azure, offentlig| Tyskland Västra Central| 51.116.96.0|
| Azure, offentlig| Tyskland Norra| 51.116.0.0|
| Azure, offentlig| Norge Öst| 51.120.2.185|
| Azure, offentlig| Norge Väst| 51.120.130.134|
| Azure Kina 21Vianet| Kina North (Global)| 139.217.51.16|
| Azure Kina 21Vianet| Kina Öst (Global)| 139.217.171.176|
| Azure Kina 21Vianet| Kina, norra| 40.125.137.220|
| Azure Kina 21Vianet| Kina, östra| 40.126.120.30|
| Azure Kina 21Vianet| Kina Norra 2| 40.73.41.178|
| Azure Kina 21Vianet| Kina Öst 2| 40.73.104.4|
| Azure Government| USGov Virginia (Globalt)| 52.127.42.160|
| Azure Government| USGov Texas (Global)| 52.127.34.192|
| Azure Government| USGov Virginia| 52.227.222.92|
| Azure Government| USGov Iowa| 13.73.72.21|
| Azure Government| USGov Arizona| 52.244.32.39|
| Azure Government| USGov Texas| 52.243.154.118|
| Azure Government| USDoD Central| 52.182.32.132|
| Azure Government| USDoD Öst| 52.181.32.192|

## <a name="related-content"></a><a name="related-content"> </a>Relaterat innehåll
* [Ansluta ett virtuellt nätverk till servering med Vpn Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti)
* [Ansluta ett virtuellt nätverk från olika distributionsmodeller](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [Så här använder du API-inspektören för att spåra anrop i Azure API Management](api-management-howto-api-inspector.md)
* [Vanliga frågor och svar om virtuellt nätverk](../virtual-network/virtual-networks-faq.md)
* [Tjänsttaggar](../virtual-network/security-overview.md#service-tags)

[api-management-using-vnet-menu]: ./media/api-management-using-with-vnet/api-management-menu-vnet.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-using-with-vnet/api-management-using-vnet-add-api.png
[api-management-vnet-private]: ./media/api-management-using-with-vnet/api-management-vnet-internal.png
[api-management-vnet-public]: ./media/api-management-using-with-vnet/api-management-vnet-external.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[UDRs]: ../virtual-network/virtual-networks-udr-overview.md
[Network Security Group]: ../virtual-network/security-overview.md
[ServiceEndpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md
[ServiceTags]: ../virtual-network/security-overview.md#service-tags
