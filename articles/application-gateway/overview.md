---
title: Vad är Azure Application Gateway?
description: Lär dig hur du kan använda Azure Application Gateway för att hantera webbtrafik till din app.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.custom: mvc
ms.date: 03/04/2020
ms.author: victorh
ms.openlocfilehash: 4a4395801218409fe77d1081689ba80b495fcfad
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "78302584"
---
# <a name="what-is-azure-application-gateway"></a>Vad är Azure Application Gateway?

Azure Application Gateway är en lastbalanserare för webbtrafik som gör det möjligt för dig att hantera trafik till dina webbappar. Traditionella lastbalanserare fungerar med transportlagret (OSI lager 4 – TCP och UDP) och dirigera trafik baserat på källans IP-adress och port till en mål-IP-adress och -port.

Application Gateway kan göra routningsbeslut baserat på ytterligare attribut för en HTTP-begäran, till exempel URI-sökväg eller värdhuvuden. Du kan till exempel dirigera trafik baserat på inkommande URL. Så om `/images` finns i inkommande URL kan du dirigera trafik till en specifik uppsättning servrar (kallas även pool) som har konfigurerats för avbildningar. Om `/video` är i webbadressen dirigeras den trafiken till en annan pool som är optimerad för videor.

![imageURLroute](./media/application-gateway-url-route-overview/figure1-720.png)

Den här typen av routning kallas belastningsutjämning för programlager (OSI lager 7). Azure Application Gateway kan göra URL-baserad routning med mera.

>[!NOTE]
> Med Azure har du tillgång till en uppsättning fullständigt hanterade belastningsutjämningslösningar för dina scenarier. Om du behöver högpresterande, låg latens, Layer-4 belastningsutjämning, se [Vad är Azure Load Balancer?](../load-balancer/load-balancer-overview.md) Om du letar efter global DNS-belastningsutjämning läser du [Vad är Traffic Manager?](../traffic-manager/traffic-manager-overview.md) Dina heltäckande scenarier kan dra nytta av att kombinera dessa lösningar.
>
> En jämförelse med azure-belastningsutjämningsalternativ finns i [Översikt över belastningsutjämningsalternativ i Azure](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview).

## <a name="features"></a>Funktioner

Mer information om funktioner för Application Gateway finns i [Azure Application Gateway-funktioner](features.md).

## <a name="pricing-and-sla"></a>Prissättning och SLA

Information om prissättning för Programgateway finns i [prissättningen för Application Gateway](https://azure.microsoft.com/pricing/details/application-gateway/).

SLA för programgateway finns i [SLA för programgateway](https://azure.microsoft.com/support/legal/sla/application-gateway/v1_2/).

## <a name="next-steps"></a>Nästa steg

Beroende på dina krav och din miljö kan du skapa en testprogramgateway med antingen Azure-portalen, Azure PowerShell eller Azure CLI.

- [Snabbstart: Dirigera webbtrafik med Azure Application Gateway – Azure Portal](quick-create-portal.md)
- [Snabbstart: Dirigera webbtrafik med Azure Application Gateway – Azure PowerShell](quick-create-powershell.md)
- [Snabbstart: Dirigera webbtrafik med Azure Application Gateway – Azure CLI](quick-create-cli.md)
