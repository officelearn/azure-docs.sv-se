---
title: Använd lokala datagateway för Azure Virtual Network datakällor | Microsoft Docs
description: Lär dig hur du konfigurerar en server för att använda en gateway för datakällor för virtuella nätverk.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 7e792114c61c6257f4f5be5bfa65474d595f0d36
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="use-gateway-for-data-sources-on-an-azure-virtual-network-vnet"></a>Använd gateway datakällor på ett Azure Virtual Network (VNet)

Den här artikeln beskriver den **AlwaysUseGateway** serveregenskapen för användning när datakällor som finns på en [Azure Virtual Network (VNet)](../virtual-network/virtual-networks-overview.md).

## <a name="server-access-to-vnet-data-sources"></a>Åtkomst till VNet-datakällor

Om dina datakällor kan nås via ett virtuellt nätverk, måste Azure Analysis Services-servern ansluta till dessa datakällor som om de finns lokalt, i din egen miljö. Du kan konfigurera den **AlwaysUseGateway** serveregenskapen för att ange den server för att komma åt alla data som datakällan via en [lokala gateway](analysis-services-gateway.md). 

> [!NOTE]
> Den här egenskapen är effektivt endast när en [lokala datagateway](analysis-services-gateway.md) har installerats och konfigurerats. Gatewayen kan vara för det virtuella nätverket.

## <a name="configure-alwaysusegateway-property"></a>Konfigurera AlwaysUseGateway egenskap

1. I SSMS > server > **egenskaper** > **allmänna**väljer **visa avancerade (alla) egenskaper**.
2. I den **ASPaaS\AlwaysUseGateway**väljer **SANT**.

    ![Använd alltid gateway-egenskap](media/analysis-services-vnet-gateway/aas-ssms-always-property.png)


## <a name="see-also"></a>Se också
[Ansluter till lokala datakällor](analysis-services-gateway.md)   
[Installera och konfigurera en gateway för lokala data](analysis-services-gateway-install.md)   
[Azure Virtual Network (VNET)](../virtual-network/virtual-networks-overview.md)   

