---
title: Använd lokal datagateway för datakällor för Azure Virtual Network | Microsoft Docs
description: Lär dig hur du konfigurerar en server för att använda en gateway för datakällor på virtuella nätverk.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 7e97bd50e3d37218e0f88f722387fd1a53167e27
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2019
ms.locfileid: "54187118"
---
# <a name="use-gateway-for-data-sources-on-an-azure-virtual-network-vnet"></a>Använda gateway för datakällor på Azure Virtual Network (VNet)

Den här artikeln beskriver den **AlwaysUseGateway** serveregenskap för användning när datakällor finns på en [Azure Virtual Network (VNet)](../virtual-network/virtual-networks-overview.md).

## <a name="server-access-to-vnet-data-sources"></a>Åtkomst till datakällor för virtuellt nätverk

Om dina datakällor kan nås via ett virtuellt nätverk, måste Azure Analysis Services-servern ansluta till dessa datakällor som om de är på plats, i din egen miljö. Du kan konfigurera den **AlwaysUseGateway** serveregenskap ange server för att komma åt alla data som datakällan via en [lokal gateway](analysis-services-gateway.md). 

> [!NOTE]
> Den här egenskapen är effektivt endast när en [lokal datagateway](analysis-services-gateway.md) har installerats och konfigurerats. Det kan vara på det virtuella nätverket.

## <a name="configure-alwaysusegateway-property"></a>Konfigurera AlwaysUseGateway egenskap

1. I SSMS > server > **egenskaper** > **Allmänt**väljer **visa avancerade (alla) egenskaper**.
2. I den **ASPaaS\AlwaysUseGateway**väljer **SANT**.

    ![Använd alltid gateway-egenskapen](media/analysis-services-vnet-gateway/aas-ssms-always-property.png)


## <a name="see-also"></a>Se också
[Ansluta till lokala datakällor](analysis-services-gateway.md)   
[Installera och konfigurera en lokal datagateway](analysis-services-gateway-install.md)   
[Azure-nätverk (VNET)](../virtual-network/virtual-networks-overview.md)   

