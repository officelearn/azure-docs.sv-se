---
title: "Ett program med Application Proxy tar för lång tid att läsa in | Microsoft Docs"
description: "Felsökning av problem med sidan belastningen prestanda med Azure AD Application Proxy"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: ce462c90746e6af0dc201686557121665b82b93d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="an-application-proxy-application-takes-too-long-to-load"></a>Ett program med Application Proxy tar för lång tid att läsa in

Den här artikeln hjälper dig att förstå varför ett program för Azure AD Application Proxy kan ta lång tid att läsa in och vad du kan göra för att lösa problemet.

## <a name="overview"></a>Översikt
Om dina program fungerar, men du ser en lång fördröjning, kan det finnas några mindre justeringar i nätverkets topologi som du kan överväga för att förbättra hastighet. En utvärdering av olika topologier finns i [nätverk överväganden dokumentet](https://docs.microsoft.com/azure/active-directory/application-proxy-network-topology-considerations).

Om dessa överväganden inte hjälper kan har vi tyvärr inte för närvarande ytterligare rekommendationer för prestandajustering. Eftersom tjänsten Application Proxy expanderar till flera datacenter som är närmast du, kanske du vill se bättre svarstid direkt. Om du vill se en fullständig lista över Azure-datacenter, kan du se den [svarstid testsida](http://www.azurespeed.com/Azure/Latency). 

Datacenter med Application Proxy-tjänsten finns med i [anslutningsverktyget portar Test](https://aadap-portcheck.connectorporttest.msappproxy.net/). 

## <a name="feedback-on-application-proxy-data-center-locations"></a>Feedback om Application Proxy data center platser 
Det kan finnas Azure-datacenter som ännu inte med Application Proxy men skulle leda till en bra svarstid förbättringar för dig. Data center plats < aadapfeedback@microsoft.com > så att vi kan använda din feedback för att planera som vi expandera.

Vi arbetar med vissa ytterligare funktioner som förbättra svarstiden för klienter som för närvarande finns långa fördröjningar och se till att dela dokumentationen när det är tillgängligt.

## <a name="next-steps"></a>Nästa steg
[Arbeta med befintliga lokala proxyservrar](application-proxy-working-with-proxy-servers.md)
