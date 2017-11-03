---
title: "Hur du använder Azure API Management med virtuella nätverk"
description: "Lär dig mer om att skapa en anslutning till ett virtuellt nätverk i Azure API Management och åtkomst till webbtjänster genom den."
services: api-management
documentationcenter: 
author: antonba
manager: erikre
editor: 
ms.assetid: 64b58f7b-ca22-47dc-89c0-f6bb0af27a48
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: apimpm
ms.openlocfilehash: 9970452b62b31f28f8277580dd1075c306767d8b
ms.sourcegitcommit: 1131386137462a8a959abb0f8822d1b329a4e474
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2017
---
# <a name="how-to-use-azure-api-management-with-virtual-networks"></a>Hur du använder Azure API Management med virtuella nätverk
Virtuella Azure-nätverk (Vnet) kan du placera någon av dina Azure-resurser i ett routeable-internet-nätverk som du styr åtkomst till. Dessa nätverk kan sedan vara ansluten till ditt lokala nätverk med olika VPN-teknologier. Läs mer om Azure Virtual Networks startar med den här informationen: [Azure översikt över virtuella nätverk](../virtual-network/virtual-networks-overview.md).

Azure API Management kan distribueras i det virtuella nätverket (VNET), så att den har åtkomst till backend-tjänster i nätverket. Developer-portalen och API-gateway kan konfigureras för att vara tillgänglig från Internet eller i det virtuella nätverket.

> [!NOTE]
> Azure API Management stöder både klassiska och Azure Resource Manager Vnet.
>

## <a name="prerequisites"></a>Krav

Om du vill utföra stegen som beskrivs i den här artikeln, måste du ha:

+ En aktiv Azure-prenumeration.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ En APIM-instans. Mer information finns i [skapa en instans av Azure API Management](get-started-create-service-instance.md).
+ VNET-anslutningen finns i Premium- och Developer nivåerna endast. Växla till en av dessa nivåer genom att följa anvisningarna i den [uppgradera och skala](upgrade-and-scale.md#upgrade-and-scale) avsnittet.

## <a name="enable-vpn"></a>Aktivera VNET-anslutning

### <a name="enable-vnet-connectivity-using-the-azure-portal"></a>Aktivera VNET-anslutning med hjälp av Azure portal

1. Navigera till din APIM instans i den [Azure-portalen](https://portal.azure.com/).
2. Välj **anpassade domäner och SSL**.
3. Konfigurera API Management-instans som ska distribueras i ett virtuellt nätverk.

    ![Virtuellt nätverk-menyn i API Management][api-management-using-vnet-menu]
4. Välj typ av åtkomst:
    
    * **Externa**: API Management gateway och developer portal är tillgänglig från internet via en extern belastningsutjämnare. Gatewayen kan komma åt resurser i det virtuella nätverket.
    
    ![Offentlig peering][api-management-vnet-public]
    
    * **Internt**: API Management gateway och developer portal är enbart tillgänglig från det virtuella nätverket via en intern belastningsutjämnare. Gatewayen kan komma åt resurser i det virtuella nätverket.
    
    ![Privat peering][api-management-vnet-private]`

    Nu visas en lista över alla regioner där API Management-tjänsten har etablerats. Välj en VNET och undernät för varje region. I listan fylls med både klassiska och Resource Manager virtuella nätverk som är tillgängliga i din Azure-prenumerationer som har konfigurerats i den region som du konfigurerar.
    
    > [!NOTE]
    > **Tjänstslutpunkten** i ovanstående diagram innehåller Gateway-proxyn, Publisher Portal, Developer-portalen, GIT och direkt hantering av slutpunkten.
    > **Hanteringsslutpunkten** i ovanstående diagram är den slutpunkt som värd för tjänsten för att hantera konfiguration via Azure portal och Powershell.
    > Tänk också på, att, även om diagrammet visar IP-adresser för olika, API Management-tjänsten **endast** svarar på dess konfigurerade värdnamn.
    
    > [!IMPORTANT]
    > När du distribuerar en Azure API Management-instans till en Resource Manager-VNET, måste tjänsten vara i ett dedikerat undernät som innehåller några resurser förutom Azure API Management-instanser. Om ett försök görs att distribuera en Azure API Management-instans till en Resource Manager VNET-undernät som innehåller andra resurser, distributionen misslyckas.
    >

    ![Välj VPN][api-management-setup-vpn-select]

5. Klicka på **spara** överst på skärmen.

> [!NOTE]
> VIP-adressen för API Management-instansen kommer att ändras varje gång virtuella nätverk är aktiverad eller inaktiverad.  
> VIP-adressen ändras samtidigt när API Management flyttas från **externa** till **internt** eller tvärtom
>

> [!IMPORTANT]
> Om du ta bort API-hantering från ett VNET eller ändrar en distribuerad i kan tidigare VNET förbli låsta för upp till fyra timmar. Under denna tid går det inte att ta bort VNET eller distribuera en ny resurs till den.

## <a name="enable-vnet-powershell"></a>Aktivera VNET-anslutningen med hjälp av PowerShell-cmdlets
Du kan också aktivera VNET-anslutning med hjälp av PowerShell-cmdlets

* **Skapa en API Management-tjänst i ett VNET**: Använd cmdlet [ny AzureRmApiManagement](/powershell/module/azurerm.apimanagement/new-azurermapimanagement) att skapa en Azure API Management-tjänst i ett virtuellt nätverk.

* **Distribuera en befintlig API Management-tjänst i ett VNET**: Använd cmdlet [uppdatering AzureRmApiManagementDeployment](/powershell/module/azurerm.apimanagement/update-azurermapimanagementdeployment) att flytta en befintlig Azure API Management-tjänst i ett virtuellt nätverk.

## <a name="connect-vnet"></a>Ansluta till en webbtjänst som finns inom ett virtuellt nätverk
När din API Management-tjänst är ansluten till VNET, är åtkomst till backend-tjänster i den inte annorlunda än att komma åt offentliga tjänster. Skriv i den lokala IP-adressen eller värdnamnet (om en DNS-server är konfigurerad för VNET) för webbtjänsten i den **-webbtjänstens URL** fältet när du skapar ett nytt API eller redigerar en befintlig.

![Lägg till API från VPN][api-management-setup-vpn-add-api]

## <a name="network-configuration-issues"></a>Vanliga nätverkskonfigurationen
Nedan följer en lista över vanliga problem som kan uppstå vid distribution av API Management-tjänsten till ett virtuellt nätverk.

* **Anpassade DNS-serverinstallation**: I API Management-tjänsten är beroende av flera Azure-tjänster. När API-hantering finns i ett VNET med en anpassad DNS-server, måste den matcha värdnamn för de Azure-tjänsterna. Följ [detta](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) vägledning om anpassade DNS-inställningarna. Se tabellen portar och andra krav som referens.

> [!IMPORTANT]
> Det rekommenderas att, om du använder en anpassad DNS-servrar för VNET kan du konfigurera som **innan** distribuera en API Management-tjänsten till den. Annars måste du uppdatera API Management-tjänsten varje gång du ändrar DNS-servrar (s) genom att köra den [gäller åtgärden för konfiguration](https://docs.microsoft.com/en-us/rest/api/apimanagement/ApiManagementService/ApplyNetworkConfigurationUpdates)

* **Portar som behövs för API Management**: inkommande och utgående trafik i undernätet som API Management har distribuerats kan kontrolleras med [Nätverkssäkerhetsgruppen][Network Security Group]. Om någon av dessa portar är otillgängliga API Management kanske inte fungerar korrekt och kan inte komma åt. Med en eller flera av de här portarna blockeras är ett annat vanligt felkonfiguration problem när du använder API-hantering med ett VNET.

När en instans för API Management-tjänsten är värd för ett virtuellt nätverk, används portarna i följande tabell.

| Källan / målet portar | Riktning | Transportprotokoll | Källan / målet | Syfte (*) | Typ för virtuella nätverk |
| --- | --- | --- | --- | --- | --- |
| * / 80, 443 |Inkommande |TCP |INTERNET / VIRTUAL_NETWORK|Klientkommunikation till API-hantering|Extern |
| * / 3443 |Inkommande |TCP |INTERNET / VIRTUAL_NETWORK|Hanteringsslutpunkten för Azure-portalen och Powershell |internt |
| * / 80, 443 |Utgående |TCP |VIRTUAL_NETWORK / INTERNET|**Åtkomst till Azure Storage-slutpunkter** |Externa och interna |
| * / 1433 |Utgående |TCP |VIRTUAL_NETWORK / INTERNET|**Åtkomst till Azure SQL-slutpunkter** |Externa och interna |
| * / 11000 - 11999 |Utgående |TCP |VIRTUAL_NETWORK / INTERNET|**Åtkomst till V12 Azure SQL** |Externa och interna |
| * / 14000 - 14999 |Utgående |TCP |VIRTUAL_NETWORK / INTERNET|**Åtkomst till V12 Azure SQL** |Externa och interna |
| * / 5671 |Utgående |AMQP |VIRTUAL_NETWORK / INTERNET|Beroende för inloggning till Event Hub-principen och övervakningsagent |Externa och interna |
| * / 445 |Utgående |TCP |VIRTUAL_NETWORK / INTERNET|Beroende på Azure-filresursen för GIT |Externa och interna |
| * / 6381 - 6383 |Inkommande och utgående |TCP |VIRTUAL_NETWORK / VIRTUAL_NETWORK|Åtkomst till Redis-cacheinstanser mellan RoleInstances |Externa och interna |
| * / * | Inkommande |TCP |AZURE_LOAD_BALANCER / VIRTUAL_NETWORK| Azure-infrastrukturen belastningsutjämnare |Externa och interna |

>[!IMPORTANT]
> * Portar som de *syfte* är **fetstil** krävs för API Management-tjänsten distribueras. De andra portarna blockeras kommer men försämras i möjligheten att använda och övervaka tjänsten körs.

* **SSL-funktionen**: att bygga kedja för SSL-certifikat och validering API Management-tjänsten måste utgående nätverksanslutning till ocsp.msocsp.com, mscrl.microsoft.com och crl.microsoft.com. Detta beroende är inte krävs, om något av de certifikat som du överför till API Management innehåller hela kedjan rot-CA: N.

* **DNS-åtkomst**: utgående åtkomst på port 53 krävs för kommunikation med DNS-servrar. Om en anpassad DNS-server finns på den andra änden av en VPN-gateway, måste DNS-servern vara nåbar från det undernät som är värd för API-hantering.

* **Mått och hälsoövervakning**: utgående nätverksanslutning till Azure-övervakning slutpunkter som löser under följande domäner: global.metrics.nsatc.net, shoebox2.metrics.nsatc.net, prod3.metrics.nsatc.net.

* **Expressinstallationen väg**: en gemensam konfiguration av customer är att definiera egna standardvägen (0.0.0.0/0) som tvingar utgående Internet-trafiken flöda lokalt i stället. Den här trafikflöde bryts utan undantag anslutningen till Azure API Management eftersom utgående trafik är antingen blockerade lokalt eller NAT skulle med ett okänt uppsättning adresser som inte längre att fungera med olika Azure-slutpunkter. Lösningen är att definiera en (eller flera) användardefinierade vägar ([udr: er][UDRs]) i undernät som innehåller Azure API Management. En UDR definierar undernät-specifika vägar som ska användas i stället för standardväg.
  Om möjligt bör du använder följande konfiguration:
 * ExpressRoute-konfigurationen annonserar 0.0.0.0/0 och som standard kraft tunnlar all utgående trafik lokalt.
 * UDR tillämpad på undernätet som innehåller Azure API Management definierar 0.0.0.0/0 med ett nexthop-typ för Internet.
 Den kombinerade effekten av dessa steg är att undernätverksnivån UDR företräde framför den ExpressRoute Tvingad tunneltrafik tillse utgående Internetåtkomst från Azure API Management.

>[!WARNING]  
>Azure API Management stöds inte med ExpressRoute-konfigurationer som **felaktigt cross-annonsera vägar från offentlig peering sökvägen att privat peering sökväg**. ExpressRoute-konfigurationer som har konfigurerats, offentlig peering får vägannonser från Microsoft för ett stort antal Microsoft Azure IP-adressintervall. Om dessa adressintervall felaktigt cross-annonseras i privat peering sökväg, är slutresultatet att alla utgående paket från Azure API Management-instans undernät felaktigt kraft tunnlar kundens lokala nätverkets infrastruktur. Det här nätverket flödet bryts Azure API Management. Lösning på problemet är att stoppa mellan reklam vägar från offentlig peering sökvägen att privat peering sökväg.


## <a name="troubleshooting"></a>Felsökning
* **Inledande installationen**: när den initiala distributionen av API Management-tjänsten i ett undernät inte lyckas är det bäst att först distribuera en virtuell dator i samma undernät. Nästa fjärrskrivbord till den virtuella datorn och kontrollera att det finns en anslutning till en av varje resurs nedan i din azure-prenumeration 
    * Azure Storage-blob
    * Azure SQL Database

 > [!IMPORTANT]
 > När du har validerat anslutningen, se till att ta bort alla resurser som har distribuerats i undernätet, innan du distribuerar API-hantering till undernätet.

* **Inkrementella uppdateringar**: när du gör ändringar i nätverket referera till [NetworkStatus API](https://docs.microsoft.com/en-us/rest/api/apimanagement/networkstatus), för att verifiera att API Management-tjänsten inte har tillgång till någon av de viktiga resurser som den är beroende av. Statusen ska uppdateras var 15: e minut.

* **Resursnavigeringslänkar**: vid distribution i Resource Manager style vnet subnet API Management reserverar undernät, genom att skapa en resurs navigering länk. Om undernätet innehåller redan en resurs från en annan leverantör, distributionen ska **misslyckas**. När du flyttar en API Management-tjänst till ett annat undernät eller ta bort den på samma sätt tar vi bort resurs navigering länken. 

## <a name="routing"></a> Routning
+ Utjämning av nätverksbelastning offentlig IP-adress (VIP) reserveras för att ge åtkomst till alla slutpunkter.
+ En IP-adress från ett intervall med IP-undernät (DIP) används för att komma åt resurser inom vnet och en offentlig IP-adress (VIP) används för att få åtkomst till resurser utanför vnet.
+ Belastningsutjämnade offentlig IP adress finns på bladet översikt/Essentials i Azure-portalen.

## <a name="limitations"></a>Begränsningar
* Ett undernät som innehåller API Management-instanser får inte innehålla andra Azure-resurs-typer.
* Undernätet och API Management-tjänsten måste vara i samma prenumeration.
* Ett undernät som innehåller API Management-instanser kan inte flyttas mellan prenumerationer.
* För flera regioner API Management-distributioner konfigurerad i internt virtuellt nätverk läge, ansvarar användare för att hantera belastningsbalansering över flera regioner som de äger routning.


## <a name="related-content"></a>Relaterat innehåll
* [Ansluta ett virtuellt nätverk till backend med hjälp av Vpn-Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti)
* [Ansluta ett virtuellt nätverk från olika distributionsmodeller](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [Hur du använder API-Inspector för att spåra anropar i Azure API Management](api-management-howto-api-inspector.md)

[api-management-using-vnet-menu]: ./media/api-management-using-with-vnet/api-management-menu-vnet.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-type.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-using-with-vnet/api-management-using-vnet-add-api.png
[api-management-vnet-private]: ./media/api-management-using-with-vnet/api-management-vnet-private.png
[api-management-vnet-public]: ./media/api-management-using-with-vnet/api-management-vnet-public.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[UDRs]: ../virtual-network/virtual-networks-udr-overview.md
[Network Security Group]: ../virtual-network/virtual-networks-nsg.md
