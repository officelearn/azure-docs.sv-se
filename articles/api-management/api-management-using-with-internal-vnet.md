---
title: Använda Azure API Management med interna virtuella nätverk
titleSuffix: Azure API Management
description: Lär dig hur du konfigurerar och konfigurerar Azure API Management i ett internt virtuellt nätverk
services: api-management
documentationcenter: ''
author: vladvino
manager: kjoshi
editor: ''
ms.assetid: dac28ccf-2550-45a5-89cf-192d87369bc3
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/31/2019
ms.author: apimpm
ms.openlocfilehash: 6054c595bca26dc2a0432c53369a60a61e3efde0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841871"
---
# <a name="using-azure-api-management-service-with-an-internal-virtual-network"></a>Använda Azure API Management-tjänsten med ett internt virtuellt nätverk
Med Azure Virtual Networks kan Azure API Management hantera API:er som inte är tillgängliga på internet. Ett antal VPN-tekniker finns tillgängliga för att göra anslutningen. API Management kan distribueras i två huvudlägen i ett virtuellt nätverk:
* Extern
* Intern

När API Management distribueras i internt virtuellt nätverksläge visas alla tjänstslutpunkter (proxygatewayen, utvecklarportalen, direkthanteringen och Git) bara i ett virtuellt nätverk som du styr åtkomsten till. Ingen av tjänstslutpunkterna registreras på den offentliga DNS-servern.

> [!NOTE]
> Eftersom det inte finns några DNS-poster för tjänstslutpunkterna är dessa slutpunkter inte tillgängliga förrän [DNS har konfigurerats](#apim-dns-configuration) för det virtuella nätverket.

Med API Management i internt läge kan du uppnå följande scenarier:

* Gör API:er som finns i ditt privata datacenter säkert tillgängliga för tredje part utanför det med hjälp av plats-till-plats- eller Azure ExpressRoute VPN-anslutningar.
* Aktivera hybridmolnscenarier genom att exponera dina molnbaserade API:er och lokala API:er via en gemensam gateway.
* Hantera dina API:er som finns på flera geografiska platser med hjälp av en slutpunkt för en enda gateway.

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Krav

Om du vill utföra stegen som beskrivs i den här artikeln måste du ha:

+ **En aktiv Azure-prenumeration**.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ **En Azure API Management-instans**. Mer information finns i [Skapa en Azure API Management-instans](get-started-create-service-instance.md).
+ När en API Management-tjänst distribueras i ett virtuellt nätverk används en [lista över portar](./api-management-using-with-vnet.md#required-ports) och måste öppnas. 

## <a name="creating-an-api-management-in-an-internal-virtual-network"></a><a name="enable-vpn"> </a>Skapa en API-hantering i ett internt virtuellt nätverk
API Management-tjänsten i ett internt virtuellt nätverk finns bakom en [intern belastningsutjämnare (klassisk)](https://docs.microsoft.com/azure/load-balancer/load-balancer-get-started-ilb-classic-cloud). Detta är det enda tillgängliga alternativet och kan inte ändras.

### <a name="enable-a-virtual-network-connection-using-the-azure-portal"></a>Aktivera en virtuell nätverksanslutning med Azure-portalen

1. Bläddra till din Azure API Management-instans i [Azure-portalen](https://portal.azure.com/).
2. Välj **Virtuellt nätverk**.
3. Konfigurera API Management-instansen som ska distribueras i det virtuella nätverket.

    ![Meny för att konfigurera en Azure API Management i ett internt virtuellt nätverk][api-management-using-internal-vnet-menu]

4. Välj **Spara**.

När distributionen lyckas bör du se **privat** virtuell IP-adress och **offentlig** virtuell IP-adress för din API Management-tjänst på översiktsbladet. Den **privata** virtuella IP-adressen är en belastningsbalanserad IP-adress `gateway`inifrån API Management-delegerade undernät över vilken , `portal` `management` och `scm` slutpunkter kan nås. Den **offentliga** virtuella IP-adressen används **endast** för att styra flygtrafiken till `management` slutpunkten över port 3443 och kan låsas till [ApiManagement-servicetag.][ServiceTags]

![API Management-instrumentpanel med ett internt virtuellt nätverk konfigurerat][api-management-internal-vnet-dashboard]

> [!NOTE]
> Testkonsolen som är tillgänglig på Azure Portal fungerar inte för **intern** VNET-distribuerad tjänst, eftersom gateway-url:en inte är registrerad på den offentliga DNS:en. Du bör i stället använda testkonsolen som finns på **utvecklarportalen**.

### <a name="enable-a-virtual-network-connection-by-using-powershell-cmdlets"></a>Aktivera en virtuell nätverksanslutning med PowerShell-cmdlets

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Du kan också aktivera anslutning till virtuella nätverk med powershell-cmdlets.

* Skapa en API Management-tjänst i ett virtuellt nätverk: Använd cmdlet [New-AzApiManagement](/powershell/module/az.apimanagement/new-azapimanagement) för att skapa en Azure API Management-tjänst i ett virtuellt nätverk och konfigurera den så att den använder den interna virtuella nätverkstypen.

* Uppdatera en befintlig distribution av en API Management-tjänst i ett virtuellt nätverk: Använd cmdlet [Update-AzApiManagementRegion](/powershell/module/az.apimanagement/update-azapimanagementregion) för att flytta en befintlig API Management-tjänst i ett virtuellt nätverk och konfigurera den så att den använder den interna virtuella nätverkstypen.

## <a name="dns-configuration"></a><a name="apim-dns-configuration"></a>DNS-konfiguration
När API Management är i externt virtuellt nätverksläge hanteras DNS av Azure. För internt virtuellt nätverksläge måste du hantera din egen routning.

> [!NOTE]
> API Management-tjänsten lyssnar inte på begäranden som kommer från IP-adresser. Den svarar bara på begäranden på värdnamnet som konfigurerats på dess tjänstslutpunkter. Dessa slutpunkter inkluderar gateway, Azure-portalen och utvecklarportalen, slutpunkten för direkt hantering och Git.

### <a name="access-on-default-host-names"></a>Åtkomst på standardvärden
När du skapar en API Management-tjänst, som heter "contosointernalvnet", konfigureras till exempel följande tjänstslutpunkter som standard:

   * Gateway eller proxy: contosointernalvnet.azure-api.net

   * Utvecklarportalen: contosointernalvnet.portal.azure-api.net

   * Den nya utvecklarportalen: contosointernalvnet.developer.azure-api.net

   * Slutpunkt för direkt hantering: contosointernalvnet.management.azure-api.net

   * Git: contosointernalvnet.scm.azure-api.net

Om du vill komma åt dessa API Management-tjänstslutpunkter kan du skapa en virtuell dator i ett undernät som är anslutet till det virtuella nätverket där API Management distribueras. Om du antar att den interna virtuella IP-adressen för tjänsten är 10.1.0.5 kan du mappa hosts-filen %SystemDrive%\drivers\etc\hosts enligt följande:

   * 10.1.0.5 contosointernalvnet.azure-api.net

   * 10.1.0,5 contosointernalvnet.portal.azure-api.net

   * 10.1.0.5 contosointernalvnet.developer.azure-api.net

   * 10.1.0.5 contosointernalvnet.management.azure-api.net

   * 10.1.0,5 contosointernalvnet.scm.azure-api.net

Du kan sedan komma åt alla tjänstslutpunkter från den virtuella datorn som du skapade.
Om du använder en anpassad DNS-server i ett virtuellt nätverk kan du också skapa en DNS-post och komma åt dessa slutpunkter var som helst i det virtuella nätverket.

### <a name="access-on-custom-domain-names"></a>Åtkomst till anpassade domännamn

1. Om du inte vill komma åt API Management-tjänsten med standardvärdena kan du ställa in anpassade domännamn för alla tjänstslutpunkter som visas i följande bild:

   ![Konfigurera en anpassad domän för API Management][api-management-custom-domain-name]

2. Sedan kan du skapa poster i DNS-servern för att komma åt slutpunkter som bara är tillgängliga från det virtuella nätverket.

## <a name="routing"></a><a name="routing"> </a> Routning

* En belastningsbalanserad *privat* virtuell IP-adress från undernätsintervallet reserveras och användas för att komma åt API Management-tjänstslutpunkterna inifrån det virtuella nätverket. Den här *privata* IP-adressen finns på bladet Översikt för tjänsten i Azure-portalen. Den här adressen måste registreras hos DE DNS-servrar som används av det virtuella nätverket.
* En belastningsbalanserad *offentlig* IP-adress (VIP) kommer också att reserveras för att ge åtkomst till slutpunkten för hanteringstjänsten över port 3443. Den här *offentliga* IP-adressen finns på bladet Översikt för tjänsten i Azure-portalen. Den *offentliga* IP-adressen används endast för `management` kontrollplanstrafik till slutpunkten över port 3443 och kan låsas till [ApiManagement][ServiceTags] servicetag.
* IP-adresser från undernäts-IP-intervallet (DIP) tilldelas varje virtuell dator i tjänsten och används för att komma åt resurser inom det virtuella nätverket. En offentlig IP-adress (VIP) kommer att användas för att komma åt resurser utanför det virtuella nätverket. Om IP-begränsningslistor används för att skydda resurser i det virtuella nätverket måste hela intervallet för undernätet där API Management-tjänsten distribueras anges för att bevilja eller begränsa åtkomsten från tjänsten.
* Belastningsbalanserade offentliga och privata IP-adresser finns på bladet Översikt i Azure-portalen.
* IP-adresserna som tilldelats för offentlig och privat åtkomst kan ändras om tjänsten tas bort från och sedan läggs tillbaka till det virtuella nätverket. Om detta inträffar kan det vara nödvändigt att uppdatera DNS-registreringar, routningsregler och IP-begränsningslistor i det virtuella nätverket.

## <a name="related-content"></a><a name="related-content"> </a>Relaterat innehåll
Mer information finns i följande artiklar:
* [Vanliga problem med nätverkskonfigurationen när du konfigurerar Azure API Management i ett virtuellt nätverk][Common network configuration problems]
* [Vanliga frågor om virtuella nätverk](../virtual-network/virtual-networks-faq.md)
* [Skapa en post i DNS](/previous-versions/windows/it-pro/windows-2000-server/bb727018(v=technet.10))

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/api-management-using-with-internal-vnet.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/api-management-custom-domain-name.png

[Create API Management service]: get-started-create-service-instance.md
[Common network configuration problems]: api-management-using-with-vnet.md#network-configuration-issues

[ServiceTags]: ../virtual-network/security-overview.md#service-tags

