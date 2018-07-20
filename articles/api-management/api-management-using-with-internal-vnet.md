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
ms.openlocfilehash: 6b6fd7395f7aff303f4950fb07bd0472cf7057a2
ms.sourcegitcommit: 727a0d5b3301fe20f20b7de698e5225633191b06
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2018
ms.locfileid: "39145748"
---
# <a name="using-azure-api-management-service-with-an-internal-virtual-network"></a>Med Azure API Management-tjänsten med ett internt virtuellt nätverk
Med Azure Virtual Networks, kan Azure API Management hantera API: er som är inte tillgänglig på internet. Ett antal VPN-tekniker är tillgängliga för att upprätta anslutningen. API Management kan distribueras i två huvudlägen i ett virtuellt nätverk:
* Extern
* Intern


När API Management distribuerar i läget för internt virtuellt nätverk, visas bara alla tjänstslutpunkter (gateway, Developer-portalen, Azure-portalen, direkthantering och Git) i ett virtuellt nätverk som du styr åtkomst till. Ingen av Tjänsteslutpunkter är registrerade på offentliga DNS-servern.

Du kan använda API Management i interna läge, för att åstadkomma följande scenarier:
* Gör API: er som finns i ditt privata datacenter på ett säkert sätt komma åt av tredje part utanför den med hjälp av plats-till-plats eller Azure ExpressRoute VPN-anslutningar.
* Aktivera hybridmolnscenarierna genom att exponera dina molnbaserade API: er och lokala API: er via en gemensam gateway.
* Hantera dina API: er som finns i flera geografiska platser med hjälp av en enda gateway-slutpunkt. 


## <a name="prerequisites"></a>Förutsättningar

Om du vill utföra stegen som beskrivs i den här artikeln, måste du ha:

+ **En aktiv Azure-prenumeration**.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ **En Azure API Management-instans**. Mer information finns i [skapa en Azure API Management-instans](get-started-create-service-instance.md).

## <a name="enable-vpn"> </a>Skapa en API Management i ett internt virtuellt nätverk
API Management-tjänsten i ett internt virtuellt nätverk finns bakom en intern belastningsutjämnare (ILB).

### <a name="enable-a-virtual-network-connection-using-the-azure-portal"></a>Aktivera en virtuell nätverksanslutning med Azure portal

1. Bläddra till din Azure API Management-instans i den [Azure-portalen](https://portal.azure.com/).
2. Välj **virtuellt nätverk**.
3. Konfigurera API Management-instans som ska distribueras i det virtuella nätverket.

    ![Menyn för att konfigurera en Azure API Management i ett internt virtuellt nätverk][api-management-using-internal-vnet-menu]

4. Välj **Spara**.

När distributionen är klar bör du se interna virtuella IP-adressen för din tjänst på instrumentpanelen.

![API Management-instrumentpanel med ett internt virtuellt nätverk som har konfigurerats][api-management-internal-vnet-dashboard]

> [!NOTE]
> Testkonsol som är tillgängliga på Azure Portal fungerar inte för **internt** VNET har distribuerat-tjänsten, eftersom Gateway-URL: en inte är registrerad på det offentliga DNS. I stället bör du använda Testkonsol som tillhandahålls på den **utvecklarportalen**.

### <a name="enable-a-virtual-network-connection-by-using-powershell-cmdlets"></a>Aktivera anslutning till ett virtuellt nätverk med hjälp av PowerShell-cmdletar
Du kan också aktivera anslutningar för virtuella nätverk med hjälp av PowerShell-cmdletar.

* Skapa en API Management-tjänst i ett virtuellt nätverk: Använd cmdlet [New-AzureRmApiManagement](/powershell/module/azurerm.apimanagement/new-azurermapimanagement) att skapa en Azure API Management-tjänsten i ett virtuellt nätverk och konfigurera den att använda typen internt virtuellt nätverk.

* Distribuera en befintlig API Management-tjänst i ett virtuellt nätverk: Använd cmdlet [uppdatering AzureRmApiManagementDeployment](/powershell/module/azurerm.apimanagement/update-azurermapimanagementdeployment) att flytta en befintlig API Management-tjänst i ett virtuellt nätverk och konfigurera den att använda den interna typ av virtuellt nätverk.

## <a name="apim-dns-configuration"></a>DNS-konfiguration
När API Management är i läget för externt virtuellt nätverk, hanteras DNS av Azure. Du måste hantera din egen routning för internt virtuellt nätverk.

> [!NOTE]
> API Management-tjänsten lyssnar inte på förfrågningar som kommer från IP-adresser. Det är bara svarar på förfrågningar till värdnamn som konfigurerats på dess tjänstslutpunkter. De här slutpunkterna är gateway, Azure-portalen och Developer-portalen, direkthantering slutpunkt och Git.

### <a name="access-on-default-host-names"></a>Åtkomst på standard-värdnamn
När du skapar en API Management-tjänsten med namnet ”contoso” exempelvis konfigureras följande tjänstslutpunkter som standard:

   * Gateway eller proxy: contoso.azure-api.net

   * Azure-portalen och Developer-portalen: contoso.portal.azure api.net

   * Direkthantering slutpunkt: contoso.management.azure api.net

   * Git: contoso.scm.azure api.net

Om du vill få åtkomst till dessa slutpunkter för API Management-tjänsten måste skapa du en virtuell dator i ett undernät som är anslutna till det virtuella nätverket där API Management har distribuerats. Under förutsättning att den interna virtuella IP-adressen för din tjänst är 10.0.0.5, kan du mappa hosts-filen % SystemDrive%\drivers\etc\hosts, enligt följande:

   * 10.0.0.5 contoso.azure-api.net

   * 10.0.0.5 contoso.portal.azure api.net

   * 10.0.0.5 contoso.management.azure api.net

   * 10.0.0.5 contoso.scm.azure api.net

Du kan sedan komma åt alla tjänstslutpunkter från den virtuella datorn som du skapade. Om du använder en anpassad DNS-server i ett virtuellt nätverk kan du också skapa en DNS-poster och åtkomst till dessa slutpunkter från valfri plats i det virtuella nätverket. 

### <a name="access-on-custom-domain-names"></a>Åtkomst på anpassade domännamn

   1. Om du inte vill få åtkomst till API Management-tjänsten med standard-värdnamn, kan du ställa in anpassade domännamn för alla dina tjänstslutpunkter som visas i följande bild: 

   ![Konfigurera en anpassad domän för API Management][api-management-custom-domain-name]

   2. Du kan sedan skapa poster i DNS-servern för att få åtkomst till de slutpunkter som endast är tillgängliga från i det virtuella nätverket.

## <a name="routing"> </a> Routning
+ Belastningsutjämnade privat virtuell IP-adress från undernätets intervall kommer reserveras och används för att få åtkomst till API Management-tjänstslutpunkter från det virtuella nätverket.
+ Också kommer att reservera en belastningsutjämnad offentlig IP-adress (VIP) för att ge åtkomst till hanteringsslutpunkten service endast via port 3443.
+ En IP-adress från ett undernät på en IP-adressintervall (DIP) används för att komma åt resurser i det virtuella nätverket och en offentlig IP-adress (VIP) som används för att få åtkomst till resurser utanför det virtuella nätverket.
+ Belastningsutjämnade offentliga och privata IP-adresser finns på bladet översikt/Essentials i Azure-portalen.

## <a name="related-content"> </a>Relaterat innehåll
Mer information finns i följande artiklar:
* [Vanliga konfigurationsproblem i nätverket när du konfigurerar Azure API Management i ett virtuellt nätverk][Common network configuration problems]
* [Vanliga frågor och svar för virtuellt nätverk](../virtual-network/virtual-networks-faq.md)
* [Skapa en post i DNS](https://msdn.microsoft.com/library/bb727018.aspx)

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-menu.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/api-management-custom-domain-name.png

[Create API Management service]: get-started-create-service-instance.md
[Common network configuration problems]: api-management-using-with-vnet.md#network-configuration-issues

