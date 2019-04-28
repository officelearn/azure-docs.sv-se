---
title: Använda Azure DNS med andra Azure-tjänster | Microsoft Docs
description: Förstå hur du använder Azure DNS för namnmatchning för andra Azure-tjänster
services: dns
documentationcenter: na
author: vhorne
manager: jeconnoc
editor: ''
tags: azure dns
ms.assetid: e9b5eb94-7984-4640-9930-564bb9e82b78
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 09/21/2016
ms.author: victorh
ms.openlocfilehash: dcf209d2036d2686bea0b51380db3cd2473d04a6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61293204"
---
# <a name="how-azure-dns-works-with-other-azure-services"></a>Så här fungerar Azure DNS med andra Azure-tjänster

Azure DNS är en värdbaserade DNS-hantering och namnet namnmatchningstjänst. Du kan använda den för att skapa den offentliga DNS-namn för andra program och tjänster som du distribuerar i Azure. Det är enkelt att skapa ett namn för en Azure-tjänst i din anpassade domän. Du bara lägga till en post med rätt typ för din tjänst.

* Du kan skapa en DNS CNAME-post som mappar till DNS-namn som Azure skapade för din tjänst för dynamiskt allokerade IP-adresser. DNS-standarden hindrar dig från att använda en CNAME-post i zonens apex. Du kan använda en aliaspost i stället. Mer information finns i [Självstudie: Konfigurera en aliaspost för att referera till en Azure offentlig IP-adress](tutorial-alias-pip.md).
* För statiskt tilldelad IP-adresser, du kan skapa en DNS A-post med hjälp av ett namn, som innehåller en *domän utan www* namn på zonens apex.

I följande tabell visas de typer av stöds poster som du kan använda för olika Azure-tjänster. Så som visas i tabellen, stöder Azure DNS DNS-poster för nätverksresurser för webbservergrupper på Internet. Azure DNS kan inte användas för namnmatchning av interna, privata adresser.

| Azure-tjänst | Nätverksgränssnitt | Beskrivning |
| --- | --- | --- |
| Azure Application Gateway |[Klientdelen offentlig IP-adress](dns-custom-domain.md#public-ip-address) |Du kan skapa en DNS-A- eller CNAME-post. |
| Azure Load Balancer |[Klientdelen offentlig IP-adress](dns-custom-domain.md#public-ip-address) |Du kan skapa en DNS-A- eller CNAME-post. Belastningsutjämnaren kan ha en IPv6-offentliga IP-adress som tilldelas dynamiskt. Skapa en CNAME-post för en IPv6-adress. |
| Azure Traffic Manager |Offentliga namn |Du kan skapa ett aliaspost som mappar till trafficmanager.net-namnet som tilldelats Traffic Manager-profilen. Mer information finns i [Självstudie: Konfigurera en aliaspost för att stödja apex-domännamn med Traffic Manager](tutorial-alias-tm.md). |
| Azure Cloud Services |[Offentlig IP-adress](dns-custom-domain.md#public-ip-address) |Du kan skapa en DNS A-post för statiskt tilldelad IP-adresser. För dynamiskt allokerade IP-adresser måste du skapa en CNAME-post som mappar till den *cloudapp.net* namn.|
| Azure App Service | [Extern IP-adress](dns-custom-domain.md#app-service-web-apps) |Du kan skapa en DNS A-post för den externa IP-adresser. I annat fall måste du skapa en CNAME-post som mappar till azurewebsites.net-namn. Mer information finns i [mappa ett anpassat domännamn till en Azure-app](../app-service/app-service-web-tutorial-custom-domain.md). |
| Azure Resource Manager-VM |[Offentlig IP-adress](dns-custom-domain.md#public-ip-address) |Resource Manager-VM kan ha offentliga IP-adresser. En virtuell dator med en offentlig IP-adress kan vara bakom en belastningsutjämnare. Du kan skapa en DNS A eller CNAME-aliaspost för den offentliga adressen. Du kan använda den här anpassade namn för att kringgå VIP i belastningsutjämnaren. |
| Klassiska virtuella datorer |[Offentlig IP-adress](dns-custom-domain.md#public-ip-address) |Klassiska virtuella datorer som skapas med hjälp av PowerShell eller CLI kan konfigureras med en dynamisk eller statisk (reserverad) virtuell adress. Du kan skapa ett DNS CNAME eller en A-post, respektive. |
