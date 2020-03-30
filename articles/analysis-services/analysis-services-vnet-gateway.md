---
title: Konfigurera Azure Analysis Services för VNet-datakällor | Microsoft-dokument
description: Lär dig hur du konfigurerar en Azure Analysis Services-server för att använda en gateway för datakällor på Azure Virtual Network (VNet).
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 384f4c2ee3021678c530aa5c98eaf4dbfd661e95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73572264"
---
# <a name="use-gateway-for-data-sources-on-an-azure-virtual-network-vnet"></a>Använda gateway för datakällor på ett virtuellt Azure-nätverk (VNet)

I den här artikeln beskrivs serveregenskapen Azure Analysis Services **AlwaysUseGateway** för användning när datakällor finns på ett [Virtuellt Azure-nätverk (VNet).](../virtual-network/virtual-networks-overview.md)

## <a name="server-access-to-vnet-data-sources"></a>Serveråtkomst till VNet-datakällor

Om dina datakällor nås via ett virtuella nätverk måste din Azure Analysis Services-server ansluta till dessa datakällor som om de är lokala, i din egen miljö. Du kan konfigurera egenskapen **AlwaysUseGateway-server** för att ange servern för att komma åt alla datakällor via en [lokal gateway](analysis-services-gateway.md). 

Azure SQL Database Managed Instance datakällor körs i Azure VNet med en privat IP-adress. Om den offentliga slutpunkten är aktiverad på instansen krävs ingen gateway. Om den offentliga slutpunkten inte är aktiverad krävs en lokal datagateway och egenskapen AlwaysUseGateway måste vara true.

> [!NOTE]
> Den här egenskapen gäller endast när en [lokal datagateway](analysis-services-gateway.md) installeras och konfigureras. Gatewayen kan finnas på det virtuella nätverket.

## <a name="configure-alwaysusegateway-property"></a>Konfigurera egenskapen AlwaysUseGateway

1. I SSMS > server > **Egenskaper** > **Allmänt**väljer du Visa avancerade **egenskaper (alla).**
2. I **ASPaaS\AlwaysUseGateway**väljer du **sant**.

    ![Använd alltid gateway-egenskap](media/analysis-services-vnet-gateway/aas-ssms-always-property.png)


## <a name="see-also"></a>Se även
[Ansluta till lokala datakällor](analysis-services-gateway.md)   
[Installera och konfigurera en lokal datagateway](analysis-services-gateway-install.md)   
[Virtuellt Azure-nätverk (VNET)](../virtual-network/virtual-networks-overview.md)   

