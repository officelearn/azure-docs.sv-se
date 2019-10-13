---
title: Använd lokal datagateway för Azure Virtual Network data källor | Microsoft Docs
description: Lär dig hur du konfigurerar en server att använda en gateway för data källor i VNet.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: d1993996c0f982624d6976b50871fa8c4592e2f6
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2019
ms.locfileid: "72301212"
---
# <a name="use-gateway-for-data-sources-on-an-azure-virtual-network-vnet"></a>Använda Gateway för data källor på en Azure-Virtual Network (VNet)

Den här artikeln beskriver **AlwaysUseGateway** -Server-egenskapen som används när data källor finns på ett [Azure-Virtual Network (VNet)](../virtual-network/virtual-networks-overview.md).

## <a name="server-access-to-vnet-data-sources"></a>Server åtkomst till VNet-datakällor

Om dina data källor nås via ett VNet måste Azure Analysis Services servern ansluta till dessa data källor som om de är lokala, i din egen miljö. Du kan konfigurera egenskapen **AlwaysUseGateway** Server för att ange att servern ska få åtkomst till alla data källor via en [lokal gateway](analysis-services-gateway.md). 

Azure SQL Database hanterade instans data källor körs i Azure VNet med en privat IP-adress. Om den offentliga slut punkten är aktive rad på instansen krävs ingen gateway. Om den offentliga slut punkten inte är aktive rad krävs en lokal datagateway och egenskapen AlwaysUseGateway måste ha värdet true.

> [!NOTE]
> Den här egenskapen gäller bara när en [lokal datagateway](analysis-services-gateway.md) har installerats och kon figurer ATS. Gatewayen kan finnas i det virtuella nätverket.

## <a name="configure-alwaysusegateway-property"></a>Konfigurera egenskapen AlwaysUseGateway

1. I SSMS > Server > **egenskaper** > **Allmänt**väljer du **Visa avancerade (alla) egenskaper**.
2. I **ASPaaS\AlwaysUseGateway**väljer du **Sant**.

    ![Använd alltid Gateway-egenskap](media/analysis-services-vnet-gateway/aas-ssms-always-property.png)


## <a name="see-also"></a>Se också
[Ansluta till lokala data källor](analysis-services-gateway.md)   
[Installera och konfigurera en lokal datagateway](analysis-services-gateway-install.md)   
[Azure-Virtual Network (VNET)](../virtual-network/virtual-networks-overview.md)   

