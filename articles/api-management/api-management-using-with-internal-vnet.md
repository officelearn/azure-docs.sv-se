---
title: Hur du använder Azure API Management med interna virtuella nätverk | Microsoft Docs
description: Lär dig hur du skapar och konfigurerar Azure API Management på ett internt virtuellt nätverk
services: api-management
documentationcenter: ''
author: vladvino
manager: kjoshi
editor: ''
ms.assetid: dac28ccf-2550-45a5-89cf-192d87369bc3
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: apimpm
ms.openlocfilehash: b2b690978c2d67dbf26b74ecd38a408cece91566
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
ms.locfileid: "32151237"
---
# <a name="using-azure-api-management-service-with-an-internal-virtual-network"></a>Med hjälp av Azure API Management-tjänsten med ett internt virtuellt nätverk
Med Azure Virtual Networks hantera Azure API Management API: er som inte är tillgänglig på internet. Ett antal VPN-tekniker är tillgängliga för att ansluta. API-hantering kan distribueras i två huvudsakliga lägen i ett virtuellt nätverk:
* Extern
* Intern


När API Management distribuerar i internt virtuellt nätverk läge, visas bara alla Tjänsteslutpunkter (gateway, Developer-portalen, Azure-portalen, direkthantering och Git) i ett virtuellt nätverk som du styr åtkomst till. Inget av Tjänsteslutpunkter registreras på den offentliga DNS-servern.

Du kan använda API Management i internt läge för att få följande scenarier:
* Se API: er som finns i ditt privata datacenter på ett säkert sätt komma åt av tredje part utanför den med hjälp av plats-till-plats eller Azure ExpressRoute VPN-anslutningar.
* Aktivera hybrid cloud scenarier genom att exponera dina molnbaserade API: er och lokala API: er via en vanliga gateway.
* Hantera dina API: er som finns i flera geografiska platser med hjälp av en enda gateway-slutpunkt. 


## <a name="prerequisites"></a>Förutsättningar

Om du vill utföra stegen som beskrivs i den här artikeln, måste du ha:

+ **En aktiv Azure-prenumeration**.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ **En instans av Azure API Management**. Mer information finns i [skapa en instans av Azure API Management](get-started-create-service-instance.md).

## <a name="enable-vpn"> </a>Skapa en API-hantering i ett internt virtuellt nätverk
API Management-tjänst i ett internt virtuellt nätverk ligger bakom en intern belastningsutjämnare (ILB).

### <a name="enable-a-virtual-network-connection-using-the-azure-portal"></a>Aktivera en virtuell nätverksanslutning som använder Azure portal

1. Bläddra till din Azure API Management-instans i den [Azure-portalen](https://portal.azure.com/).
2. Välj **för virtuella nätverk**.
3. Konfigurera API Management-instans som ska distribueras i det virtuella nätverket.

    ![Menyn för att skapa ett Azure API Management i ett internt virtuellt nätverk][api-management-using-internal-vnet-menu]

4. Välj **Spara**.

När distributionen lyckas, bör du se interna virtuella IP-adressen för din tjänst på instrumentpanelen.

![API Management-instrumentpanel med ett internt virtuellt nätverk som har konfigurerats][api-management-internal-vnet-dashboard]

### <a name="enable-a-virtual-network-connection-by-using-powershell-cmdlets"></a>Aktivera virtuella nätverk med hjälp av PowerShell-cmdlets
Du kan också aktivera anslutningar för virtuella nätverk med PowerShell-cmdlets.

* Skapa en API Management-tjänst i ett virtuellt nätverk: Använd cmdlet [ny AzureRmApiManagement](/powershell/module/azurerm.apimanagement/new-azurermapimanagement) att skapa en Azure API Management-tjänst i ett virtuellt nätverk och konfigurera den interna virtuella nätverk.

* Distribuera en befintlig API Management-tjänst i ett virtuellt nätverk: Använd cmdlet [uppdatering AzureRmApiManagementDeployment](/powershell/module/azurerm.apimanagement/update-azurermapimanagementdeployment) att flytta en befintlig API Management-tjänst i ett virtuellt nätverk och konfigurera den att använda den interna typen av virtuellt nätverk.

## <a name="apim-dns-configuration"></a>DNS-konfiguration
När API-hantering är i läget för externa virtuella nätverk kan hanteras DNS i Azure. Du måste hantera egna routning för internt virtuellt läge.

> [!NOTE]
> API Management-tjänsten lyssnar inte på begäranden som kommer från IP-adresser. Den bara svarar på förfrågningar om att värdnamnet som konfigurerats på dess slutpunkter. Dessa slutpunkter är gateway, Azure-portalen och Developer-portalen, direkthantering slutpunkt och Git.

### <a name="access-on-default-host-names"></a>Åtkomst på standard-värdnamn
När du skapar en API Management-tjänsten med namnet ”contoso” till exempel konfigureras att följande Tjänsteslutpunkter som standard:

   * Gateway eller proxy: contoso.azure api.net

   * Azure-portalen och Developer-portalen: contoso.portal.azure api.net

   * Direkthantering slutpunkt: contoso.management.azure api.net

   * Git: contoso.scm.azure-api.net

Om du vill få åtkomst till dessa slutpunkter för API Management-tjänsten måste skapa du en virtuell dator i ett undernät som är anslutna till det virtuella nätverket som API Management har distribuerats. Under förutsättning att den interna virtuella IP-adressen för din tjänst är 10.0.0.5, kan du mappa hosts-filen % SystemDrive%\drivers\etc\hosts, enligt följande:

   * 10.0.0.5 contoso.azure-api.net

   * 10.0.0.5 contoso.portal.azure-api.net

   * 10.0.0.5 contoso.management.azure-api.net

   * 10.0.0.5 contoso.scm.azure-api.net

Du kan sedan komma åt alla Tjänsteslutpunkter från den virtuella datorn som du skapade. Om du använder en anpassad DNS-server i ett virtuellt nätverk kan du också skapa en DNS-poster och få åtkomst till dessa slutpunkter från valfri plats i det virtuella nätverket. 

### <a name="access-on-custom-domain-names"></a>Åtkomst på anpassade domännamn

   1. Om du inte vill att få åtkomst till API Management-tjänsten med standard-värdnamn, kan du ställa in anpassade domännamn för alla dina slutpunkter som visas i följande bild: 

   ![Konfigurera en anpassad domän för API Management][api-management-custom-domain-name]

   2. Du kan skapa poster i DNS-servern för att få åtkomst till de slutpunkter som endast är tillgänglig från det virtuella nätverket.

## <a name="routing"> </a> Routning
+ En belastningsutjämnad privata virtuella IP-adress från intervallet undernät kommer reserverad och används för att komma åt Tjänsteslutpunkter API Management inom vnet.
+ Även reserveras belastningsutjämnade offentlig IP-adress (VIP) för att ge åtkomst till management-tjänsteslutpunkt bara via port 3443.
+ En IP-adress från ett intervall med IP-undernät (DIP) används för att komma åt resurser inom vnet och en offentlig IP-adress (VIP) används för att få åtkomst till resurser utanför vnet.
+ Belastningsutjämnade offentliga och privata IP-adresser finns på bladet översikt/Essentials i Azure-portalen.

## <a name="related-content"> </a>Relaterat innehåll
Mer information finns i följande artiklar:
* [Vanliga problem med nätverket konfiguration när du konfigurerar Azure API Management i ett virtuellt nätverk][Common network configuration problems]
* [Virtuella nätverk och svar](../virtual-network/virtual-networks-faq.md)
* [Skapa en post i DNS](https://msdn.microsoft.com/library/bb727018.aspx)

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-menu.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/api-management-custom-domain-name.png

[Create API Management service]: get-started-create-service-instance.md
[Common network configuration problems]: api-management-using-with-vnet.md#network-configuration-issues

