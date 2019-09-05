---
title: Använda Azure API Management med virtuella nätverk
description: Lär dig hur du konfigurerar en anslutning till ett virtuellt nätverk i Azure API Management och får åtkomst till webb tjänster via det.
services: api-management
documentationcenter: ''
author: vlvinogr
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/01/2019
ms.author: apimpm
ms.openlocfilehash: b89f4d2264924983aac93fb266efd71e46bea6bf
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2019
ms.locfileid: "70305234"
---
# <a name="how-to-use-azure-api-management-with-virtual-networks"></a>Använda Azure API Management med virtuella nätverk
Med Azure Virtual Networks (virtuella nätverk) kan du placera någon av dina Azure-resurser i ett icke-Internet-dirigerbart nätverk som du styr åtkomsten till. Dessa nätverk kan sedan anslutas till dina lokala nätverk med hjälp av olika VPN-tekniker. Om du vill veta mer om virtuella Azure-nätverk börjar du med informationen här: [Översikt över Azure-Virtual Network](../virtual-network/virtual-networks-overview.md).

Azure API Management kan distribueras inuti det virtuella nätverket (VNET), så att det kan komma åt Server dels tjänster i nätverket. Developer-portalen och API-gatewayen kan konfigureras att vara tillgängliga antingen från Internet eller endast inom det virtuella nätverket.

> [!NOTE]
> URL: en för API-importens dokument måste finnas på en offentligt tillgänglig Internet adress.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Förutsättningar

För att utföra stegen som beskrivs i den här artikeln måste du ha:

+ En aktiv Azure-prenumeration.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ En APIM-instans. Mer information finns i [skapa en Azure API Management-instans](get-started-create-service-instance.md).

## <a name="enable-vpn"> </a>Aktivera VNET-anslutning

### <a name="enable-vnet-connectivity-using-the-azure-portal"></a>Aktivera VNET-anslutning med hjälp av Azure Portal

1. Navigera till din APIM-instans i [Azure Portal](https://portal.azure.com/).
2. Välj **Virtual Network**.
3. Konfigurera API Management-instansen som ska distribueras i ett virtuellt nätverk.

    ![Menyn virtuellt nätverk för API Management][api-management-using-vnet-menu]
4. Välj önskad åtkomst typ:

   * **Av**: det här är standardinställningen. API Management distribueras inte till ett virtuellt nätverk.

   * **Externt**: API Management Gateway-och Developer-portalen kan nås från det offentliga Internet via en extern belastningsutjämnare. Gatewayen har åtkomst till resurser i det virtuella nätverket.

     ![Offentlig peering][api-management-vnet-public]

   * **Internt**: API Management Gateway-och Developer-portalen är bara tillgängliga i det virtuella nätverket via en intern belastningsutjämnare. Gatewayen har åtkomst till resurser i det virtuella nätverket.

     ![Privat peering][api-management-vnet-private]

     Nu visas en lista över alla regioner där API Managements tjänsten har tillhandahållits. Välj ett VNET och undernät för varje region. Listan fylls i med både klassiska och virtuella Resource Manager-nätverk tillgängliga i Azure-prenumerationer som konfigureras i den region som du konfigurerar.

     > [!IMPORTANT]
     > När du distribuerar en Azure API Management-instans till ett virtuellt Resource Manager-nätverk måste tjänsten finnas i ett dedikerat undernät som inte innehåller några andra resurser förutom Azure API Management-instanser. Om ett försök görs att distribuera en Azure API Management-instans till ett VNET-undernät i Resource Manager som innehåller andra resurser, kommer distributionen att Miss lyckas.
     >

     ![Välj VPN][api-management-setup-vpn-select]

5. Klicka på **Spara** i det övre navigerings fältet.
6. Klicka på **tillämpa nätverks konfiguration** i det övre navigerings fältet.

> [!NOTE]
> VIP-adressen för API Management-instansen ändras varje gång VNET aktive ras eller inaktive ras.
> VIP-adressen kommer också att ändras när API Management flyttas från **extern** till **intern** eller omvänt
>

> [!IMPORTANT]
> Om du tar bort API Management från ett VNET eller ändrar den som det distribueras i, kan det tidigare använda VNET förbli låst i upp till två timmar. Under den här perioden går det inte att ta bort det virtuella nätverket eller distribuera en ny resurs till den.

## <a name="enable-vnet-powershell"> </a>Aktivera VNET-anslutning med PowerShell-cmdletar
Du kan också aktivera VNET-anslutning med hjälp av PowerShell-cmdletar

* **Skapa en API Management tjänst i ett VNet**: Använd cmdleten [New-AzApiManagement](/powershell/module/az.apimanagement/new-azapimanagement) för att skapa en Azure API Management-tjänst i ett VNet.

* **Distribuera en befintlig API Management tjänst i ett VNet**: Använd cmdlet [Update-AzApiManagementRegion](/powershell/module/az.apimanagement/update-azapimanagementregion) för att flytta en befintlig Azure API Management-tjänst i en Virtual Network.

## <a name="connect-vnet"> </a>Ansluta till en webb tjänst som finns i ett virtuellt nätverk
När din API Management-tjänst är ansluten till det virtuella nätverket, är åtkomst till backend-tjänster i den inte något annat än att komma åt offentliga tjänster. Skriv bara in den lokala IP-adressen eller värd namnet (om en DNS-server har kon figurer ATS för VNET) för webb tjänsten i fältet **URL för webb tjänst** när du skapar ett nytt API eller redigerar en befintlig.

![Lägg till API från VPN][api-management-setup-vpn-add-api]

## <a name="network-configuration-issues"> </a>Vanliga problem med nätverks konfiguration
Nedan följer en lista över vanliga fel konfigurations problem som kan uppstå när du distribuerar API Management tjänst till en Virtual Network.

* **Installation av anpassad DNS-Server**: Den API Management tjänsten är beroende av flera Azure-tjänster. När API Management finns i ett VNET med en anpassad DNS-server måste du matcha värd namnen för dessa Azure-tjänster. Följ [den här](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) vägledningen om anpassad DNS-konfiguration. Se tabellen portar nedan och andra nätverks krav för referens.

> [!IMPORTANT]
> Om du planerar att använda en anpassad DNS-server (er) för det virtuella nätverket bör du konfigurera den **innan** du distribuerar en API Management tjänst till den. Annars måste du uppdatera API Managements tjänsten varje gång du ändrar DNS-servrarna genom att köra [åtgärden tillämpa nätverks konfiguration](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/ApiManagementService/ApplyNetworkConfigurationUpdates)

* **Portar som krävs för API Management**: Inkommande och utgående trafik till under nätet där API Management distribueras kan kontrol leras med hjälp av [nätverks säkerhets gruppen][Network Security Group]. Om någon av dessa portar är otillgänglig kanske API Management inte fungerar korrekt och kan bli oåtkomlig. En eller flera av dessa portar blockeras är ett annat vanligt problem med fel konfiguration vid användning av API Management med ett VNET.

<a name="required-ports"></a> När en API Management tjänst instans finns i ett VNet används portarna i följande tabell.

| Käll-/mål Port (er) | Direction          | Transport protokoll |   [Service märken](../virtual-network/security-overview.md#service-tags) <br> Källa/mål   | Syfte (*)                                                 | Virtual Network typ |
|------------------------------|--------------------|--------------------|---------------------------------------|-------------------------------------------------------------|----------------------|
| * / 80, 443                  | Inkommande            | TCP                | INTERNET/VIRTUAL_NETWORK            | Klient kommunikation till API Management                      | Extern             |
| * / 3443                     | Inkommande            | TCP                | API Management/VIRTUAL_NETWORK       | Hanterings slut punkt för Azure Portal och PowerShell         | Externt & internt  |
| * / 80, 443                  | Utgående           | TCP                | VIRTUAL_NETWORK/lagring             | **Beroende av Azure Storage**                             | Externt & internt  |
| * / 80, 443                  | Utgående           | TCP                | VIRTUAL_NETWORK/AzureActiveDirectory | Azure Active Directory (i förekommande fall)                   | Externt & internt  |
| * / 1433                     | Utgående           | TCP                | VIRTUAL_NETWORK/SQL                 | **Åtkomst till Azure SQL-slutpunkter**                           | Externt & internt  |
| */5672                     | Utgående           | TCP                | VIRTUAL_NETWORK/EventHub            | Beroende för logg till Event Hub-princip och övervaknings agent | Externt & internt  |
| * / 445                      | Utgående           | TCP                | VIRTUAL_NETWORK/lagring             | Beroende av Azure-filresurs för GIT                      | Externt & internt  |
| */1886                     | Utgående           | TCP                | VIRTUAL_NETWORK/INTERNET            | Krävs för att publicera hälso status till Resource Health          | Externt & internt  |
| */443                     | Utgående           | TCP                | VIRTUAL_NETWORK / AzureMonitor         | Publicera diagnostikloggar och mått                        | Externt & internt  |
| */25                       | Utgående           | TCP                | VIRTUAL_NETWORK/INTERNET            | Ansluta till SMTP-relä för att skicka e-post                    | Externt & internt  |
| */587                      | Utgående           | TCP                | VIRTUAL_NETWORK/INTERNET            | Ansluta till SMTP-relä för att skicka e-post                    | Externt & internt  |
| * / 25028                    | Utgående           | TCP                | VIRTUAL_NETWORK/INTERNET            | Ansluta till SMTP-relä för att skicka e-post                    | Externt & internt  |
| * / 6381 - 6383              | Inkommande & utgående | TCP                | VIRTUAL_NETWORK / VIRTUAL_NETWORK     | Få åtkomst till Azure cache för Redis-instanser mellan RoleInstances          | Externt & internt  |
| * / \*                        | Inkommande            | TCP                | AZURE_LOAD_BALANCER / VIRTUAL_NETWORK | Azure-infrastruktur Load Balancer                          | Externt & internt  |

>[!IMPORTANT]
> Portarna för vilka *syftet* är **Bold** krävs för att API Management tjänsten ska kunna distribueras korrekt. Om du blockerar de andra portarna kan du dock orsaka försämring i möjligheten att använda och övervaka den aktiva tjänsten.

+ **SSL-funktioner**: Om du vill aktivera skapande och verifiering av SSL-certifikat måste den API Management tjänsten behöva utgående nätverks anslutning till ocsp.msocsp.com, mscrl.microsoft.com och crl.microsoft.com. Beroendet krävs inte, om något certifikat som du överför till API Management innehåller hela kedjan till CA-roten.

+ **DNS-åtkomst**: Utgående åtkomst på port 53 krävs för kommunikation med DNS-servrar. Om en anpassad DNS-Server finns i den andra änden av en VPN-gateway, måste DNS-servern gå att komma åt från under nätets värd API Management.

+ **Mått och hälso övervakning**: Utgående nätverks anslutning till Azures övervaknings slut punkter, vilket löser problemet under följande domäner:

    | Azure Environment | Slutpunkter                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Azure Public      | <ul><li>prod.warmpath.msftcloudes.com</li><li>shoebox2.metrics.nsatc.net</li><li>prod3.metrics.nsatc.net</li><li>prod3-black.prod3.metrics.nsatc.net</li><li>prod3-red.prod3.metrics.nsatc.net</li><li>prod.warm.ingestion.msftcloudes.com</li><li>`azure region`. Warm.ingestion.msftcloudes.com där `East US 2` är eastus2.Warm.ingestion.msftcloudes.com</li></ul> |
    | Azure Government  | <ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>shoebox2.metrics.nsatc.net</li><li>prod3.metrics.nsatc.net</li></ul>                                                                                                                                                                                                                                                |
    | Azure Kina       | <ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>shoebox2.metrics.nsatc.net</li><li>prod3.metrics.nsatc.net</li></ul>                                                                                                                                                                                                                                                |

+ **SMTP-relä**: Utgående nätverks anslutning för SMTP-reläet, som löses `smtpi-co1.msn.com`under värden, `smtpi-ch1.msn.com`, `smtpi-db3.msn.com` `smtpi-sin.msn.com` och`ies.global.microsoft.com`

+ **CAPTCHA för Developer-portalen**: Utgående nätverks anslutning för Developer-portalens CAPTCHA, som löses under värdarna `client.hip.live.com` och. `partner.hip.live.com`

+ **Azure Portal diagnostik**: Om du vill aktivera flödet av diagnostikloggar från Azure Portal när du använder API Management tillägget inifrån en Virtual Network, krävs utgående åtkomst `dc.services.visualstudio.com` till på port 443. Detta hjälper till att felsöka problem som kan uppstå när du använder tillägget.

+ **Tvinga tunnel trafik till lokal-brandvägg med Express Route eller virtuell nätverks**installation: En vanlig kund konfiguration är att definiera sin egen standard väg (0.0.0.0/0) som tvingar all trafik från det API Management delegerade under nätet att flöda genom en lokal brand vägg eller till en virtuell nätverks installation. Detta trafikflöde avbryter anslutningen till Azure API Management eftersom utgående trafik antingen blockeras lokalt eller NAT till en okänd uppsättning adresser som inte längre fungerar med olika Azure-slutpunkter. Lösningen kräver att du gör några saker:

  * Aktivera tjänst slut punkter i det undernät där API Managements tjänsten har distribuerats. [Tjänst slut punkter][ServiceEndpoints] måste aktive ras för Azure Sql, Azure Storage, Azure EventHub och Azure Service Bus. Genom att aktivera slut punkter direkt från API Management delegerade under nätet till dessa tjänster kan de använda det Microsoft Azure stamnät nätverket som tillhandahåller optimal routning för tjänst trafik. Om du använder tjänstens slut punkter med en Tvingad tunnel-API Management är ovanstående Azure-tjänstens trafik inte Tvingad tunnel trafik. Den andra API Management tjänst beroende trafik tvingas till tunnel trafik och går inte att förlora eller också fungerar inte tjänsten API Management.
    
  * All kontroll Plans trafik från Internet till hanterings slut punkten för API Managements tjänsten dirigeras via en speciell uppsättning inkommande IP-adresser som API Management. När trafiken är Tvingad tunnel trafik mappas svaren inte symmetriskt till dessa inkommande käll-IP-adresser. För att undvika begränsningen måste vi lägga till följande användardefinierade vägar ([UDR][UDRs]) för att styra trafiken tillbaka till Azure genom att ställa in målet för dessa värd vägar till "Internet". Uppsättningen inkommande IP-adresser för kontroll Plans trafik är följande:
    
     | Azure Environment | IP-adresser för hantering                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Azure Public      | 13.84.189.17/32, 13.85.22.63/32, 23.96.224.175/32, 23.101.166.38/32, 52.162.110.80/32, 104.214.19.224/32, 13.64.39.16/32, 40.81.47.216/32, 51.145.179.78/32, 52.142.95.35/32, 40.90.185.46/32, 20.40.125.155/32, 52.159.16.255/32, 40.82.157.167/32, 51.137.136.0/32, 40.81.185.8/32, 40.81.47.216/32, 51.145.56.125/32, 40.81.89.24/32, 52.224.186.99/32, 51.145.179.78/32, 52.140.238.179/32, 40.66.60.111/32, 52.139.80.117/32, 20.46.144.85/32, 191.233.24.179/32, 40.90.185.46/32, 102.133.130.197/32, 52.139.20.34/32, 40.80.232.185/32, 13.71.49.1/32, 13.64.39.16/32, 20.40.160.107/32, 20.37.52.67/32, 20.44.33.246/32, 13.86.102.66/32, 20.40.125.155/32, 51.143.127.203/32, 52.253.225.124/32, 52.253.159.160/32, 20.188.77.119/32, 20.44.72.3/32, 52.142.95.35/32, 52.139.152.27/32, 20.39.80.2/32, 51.107.96.8/32, 20.39.99.81/32, 20.37.81.41/32, 51.107.0.91/32, 102.133.0.79/32, 51.116.96.0/32, 51.116.0.0/32 |
    | Azure Government  | 52.127.42.160/32, 52.127.34.192/32 |
    | Azure Kina       | 139.217.51.16/32, 139.217.171.176/32 |

  * För andra API Management tjänst beroenden som tvingas tunnel trafik bör det finnas ett sätt att matcha värd namnet och kontakta slut punkten. Dessa inkluderar
      - Mått och hälso övervakning
      - Azure Portal diagnostik
      - SMTP-relä
      - CAPTCHA för Developer-portalen

## <a name="troubleshooting"> </a>Fel sökning
* **Första installationen**: När den första distributionen av API Management tjänst i ett undernät inte lyckas, bör du först distribuera en virtuell dator till samma undernät. Nästa fjärr skrivbord i den virtuella datorn och kontrol lera att det finns en anslutning till en av varje resurs nedan i din Azure-prenumeration
    * Azure Storage BLOB
    * Azure SQL Database
    * Azure Storage tabell

  > [!IMPORTANT]
  > När du har verifierat anslutningen ser du till att ta bort alla resurser som har distribuerats i under nätet innan du distribuerar API Management till under nätet.

* **Stegvisa uppdateringar**: När du gör ändringar i nätverket kan du läsa [NetworkStatus-API: et](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/networkstatus)för att kontrol lera att tjänsten API Management inte har förlorat åtkomst till någon av de kritiska resurserna som den är beroende av. Anslutnings statusen måste uppdateras var 15: e minut.

* **Länkar till resurs navigering**: När du distribuerar till Resource Manager-formatet VNet-undernät kan API Management reserverar under nätet genom att skapa en resurs navigerings länk. Om under nätet redan innehåller en resurs från en annan provider, kommer distributionen att **Miss**förfalla. När du flyttar en API Management-tjänst till ett annat undernät eller tar bort den, kommer vi att ta bort resurs navigerings länken.

## <a name="subnet-size"></a> Krav för under näts storlek
Azure reserverar vissa IP-adresser i varje undernät och de här adresserna kan inte användas. De första och sista IP-adresserna i under näten är reserverade för protokoll överensstämmelse, tillsammans med tre fler adresser som används för Azure-tjänster. Mer information finns i finns [det några begränsningar för att använda IP-adresser i dessa undernät?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Förutom de IP-adresser som används av Azure VNET-infrastrukturen använder varje API Management-instans i under nätet två IP-adresser per enhet av Premium-SKU eller en IP-adress för Developer-SKU: n. Varje instans reserverar en ytterligare IP-adress för den externa belastningsutjämnaren. När du distribuerar till ett internt virtuellt nätverk kräver det ytterligare en IP-adress för den interna belastningsutjämnaren.

Beräkningen ovanför under nätets minsta storlek, där API Management kan distribueras är/29, vilket ger tre IP-adresser.

## <a name="routing"></a> Routning
+ En belastningsutjämnad offentlig IP-adress (VIP) är reserverad för att ge åtkomst till alla tjänst slut punkter.
+ En IP-adress från ett undernäts IP-intervall (DIP) används för att komma åt resurser i VNet och en offentlig IP-adress (VIP) kommer att användas för att få åtkomst till resurser utanför VNet.
+ Belastningsutjämnad offentlig IP-adress hittar du på bladet översikt/Essentials i Azure Portal.

## <a name="limitations"> </a>Begränsningar
* Ett undernät som innehåller API Management instanser får inte innehålla några andra typer av Azure-resurser.
* Under nätet och tjänsten API Management måste finnas i samma prenumeration.
* Ett undernät som innehåller API Management instanser kan inte flyttas mellan prenumerationer.
* För API Management distributioner i flera regioner som kon figurer ATS i internt virtuellt nätverks läge ansvarar användarna för att hantera belastnings utjämning över flera regioner, när de äger routningen.
* Anslutning från en resurs i ett globalt peer-nätverk i en annan region till API Management tjänsten i det interna läget fungerar inte på grund av en plattforms begränsning. Mer information finns i [resurser i ett virtuellt nätverk kan inte kommunicera med Azures interna belastningsutjämnare i peer-kopplat virtuellt nätverk](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)


## <a name="related-content"> </a>Relaterat innehåll
* [Ansluta en Virtual Network till Server delen med VPN-gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti)
* [Ansluta en Virtual Network från olika distributions modeller](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [Så här använder du API-kontrollen för att spåra anrop i Azure API Management](api-management-howto-api-inspector.md)
* [Virtual Network vanliga frågor och svar](../virtual-network/virtual-networks-faq.md)
* [Service märken](../virtual-network/security-overview.md#service-tags)

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
