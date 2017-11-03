---
title: "Azure DNS med andra Azure-tjänster | Microsoft Docs"
description: "Förstå hur du använder Azure DNS för att matcha namn för andra Azure-tjänster"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
tags: azure dns
ms.assetid: e9b5eb94-7984-4640-9930-564bb9e82b78
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 09/21/2016
ms.author: gwallace
ms.openlocfilehash: a286508fe445208b6bb348d07434b5722cc3f11e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-azure-dns-works-with-other-azure-services"></a>Hur Azure DNS fungerar med andra Azure-tjänster

Azure DNS är en DNS-hanterings- och name resolution värdtjänst. På så sätt kan du skapa den offentliga DNS-namn för andra program och tjänster som du har distribuerat i Azure. Skapa ett namn för en Azure-tjänst i din anpassade domän är lika enkelt som att lägga till en post av rätt typ för din tjänst.

* För dynamiskt allokerade IP-adresser måste du skapa en DNS CNAME-post som mappar till DNS-namn som Azure som skapats för din tjänst. DNS-standarden att du använder en CNAME-post för zonens apex.
* Du kan skapa en DNS A-post med hjälp av ett namn för statiskt tilldelade IP-adresser, inklusive en *asbestgaller domän* namn på zonens apex.

I följande tabell beskrivs de typer av stöds poster som kan användas för olika Azure-tjänster. Som du ser i den här tabellen stöder Azure DNS bara DNS-poster för nätverksresurser mot Internet. Azure DNS kan inte användas för namnmatchning av interna, privata adresser.

| Azure-tjänst | Nätverksgränssnitt | Beskrivning |
| --- | --- | --- |
| Application Gateway |[Frontend offentlig IP-adress](dns-custom-domain.md#public-ip-address) |Du kan skapa en DNS A- eller CNAME-post. |
| Belastningsutjämnare |[Frontend offentlig IP-adress](dns-custom-domain.md#public-ip-address)  |Du kan skapa en DNS A- eller CNAME-post. Belastningsutjämnaren kan ha en IPv6-offentlig IP-adress som tilldelas dynamiskt. Därför måste du skapa en CNAME-post för en IPv6-adress. |
| Traffic Manager |Offentligt namn |Du kan bara skapa en CNAME-post som mappar till trafficmanager.net namnet som tilldelats Traffic Manager-profilen. Mer information finns i [hur Traffic Manager fungerar](../traffic-manager/traffic-manager-overview.md#traffic-manager-example). |
| Molntjänsten |[Offentlig IP-adress](dns-custom-domain.md#public-ip-address) |Du kan skapa en DNS A-post för statiskt tilldelade IP-adresser. För dynamiskt allokerade IP-adresser måste du skapa en CNAME-post som mappar till den *cloudapp.net* namn. Den här regeln gäller för virtuella datorer skapas i den klassiska portalen eftersom de har distribuerats som en tjänst i molnet. Mer information finns i [konfigurera ett anpassat domännamn i Cloud Services](../cloud-services/cloud-services-custom-domain-name-portal.md). |
| App Service | [Extern IP](dns-custom-domain.md#app-service-web-apps) |Du kan skapa en DNS A-post för den externa IP-adresser. I annat fall måste du skapa en CNAME-post som mappar till azurewebsites.net namn. Mer information finns i [mappa ett anpassat domännamn till en Azure-app](../app-service/app-service-web-tutorial-custom-domain.md) |
| Hanteraren för virtuella datorer |[Offentlig IP-adress](dns-custom-domain.md#public-ip-address) |Hanteraren för virtuella datorer kan ha offentliga IP-adresser. En virtuell dator med en offentlig IP-adress kan också vara bakom en belastningsutjämnare. Du kan skapa en DNS A- eller CNAME-post för den offentliga adressen. Den här anpassade namn kan användas för att kringgå VIP i belastningsutjämnaren. |
| Klassiska virtuella datorer |[Offentlig IP-adress](dns-custom-domain.md#public-ip-address) |Klassiska virtuella datorer skapas med hjälp av PowerShell eller CLI kan konfigureras med en dynamisk eller statisk (reserverad) virtuell adress. Du kan skapa ett DNS CNAME eller en post respektive. |
