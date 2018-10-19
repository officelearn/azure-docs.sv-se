---
title: Använd lokal datagateway för datakällor för Azure Virtual Network | Microsoft Docs
description: Lär dig hur du konfigurerar en server för att använda en gateway för datakällor på virtuella nätverk.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 62f6413854ecdfc257655fd864167d8e24d35e06
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2018
ms.locfileid: "49427328"
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

