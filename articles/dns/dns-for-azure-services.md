---
title: Använda Azure DNS med andra Azure-tjänster | Microsoft Docs
description: Om du använder Azure DNS kan matcha namn för andra Azure-tjänster
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
ms.openlocfilehash: 5d9886e16aa1921963f3d91d0fbd4da9287f7e54
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46989152"
---
# <a name="how-azure-dns-works-with-other-azure-services"></a>Så här fungerar Azure DNS med andra Azure-tjänster

Azure DNS är en värdbaserade DNS-hantering och namnet namnmatchningstjänst. På så sätt kan du skapa offentliga DNS-namn för andra program och tjänster som du har distribuerat i Azure. Skapa ett namn för en Azure-tjänst i din anpassade domän är lika enkelt som att lägga till en post med rätt typ för din tjänst.

* Du kan skapa en DNS CNAME-post som mappar till DNS-namn som Azure skapade för din tjänst för dynamiskt allokerade IP-adresser. DNS-standarden hindrar dig från att använda en CNAME-post i zonens apex, men du kan använda en aliasresurspost i stället. Mer information finns i [självstudie: Konfigurera en aliaspost för att referera till en Azure offentlig IP-adress](tutorial-alias-pip.md).
* För statiskt tilldelad IP-adresser, kan du skapa en DNS A-post med ett namn, inklusive en *domän utan www* namn på zonens apex.

I följande tabell visas de typer av stöds poster som kan användas för olika Azure-tjänster. Som du kan se den här tabellen kan stöd Azure DNS endast för DNS-poster för nätverksresurser för webbservergrupper på Internet. Azure DNS kan inte användas för namnmatchning av interna, privata adresser.

| Azure-tjänst | Nätverksgränssnitt | Beskrivning |
| --- | --- | --- |
| Application Gateway |[Klientdelen offentlig IP-adress](dns-custom-domain.md#public-ip-address) |Du kan skapa en DNS-A- eller CNAME-post. |
| Lastbalanserare |[Klientdelen offentlig IP-adress](dns-custom-domain.md#public-ip-address)  |Du kan skapa en DNS-A- eller CNAME-post. Belastningsutjämnaren kan ha en offentlig IPv6-IP-adress som tilldelas dynamiskt. Därför måste du skapa en CNAME-post för en IPv6-adress. |
| Traffic Manager |Offentliga namn |Du kan skapa ett aliaspost som mappar till trafficmanager.net-namnet som tilldelats Traffic Manager-profilen. Mer information finns i [självstudie: Konfigurera en aliaspost för att stödja apex-domännamn med Traffic Manager](tutorial-alias-tm.md). |
| Molntjänst |[Offentlig IP-adress](dns-custom-domain.md#public-ip-address) |Du kan skapa en DNS A-post för statiskt tilldelad IP-adresser. För dynamiskt allokerade IP-adresser måste du skapa en CNAME-post som mappar till den *cloudapp.net* namn.|
| App Service | [Extern IP-adress](dns-custom-domain.md#app-service-web-apps) |Du kan skapa en DNS A-post för den externa IP-adresser. I annat fall måste du skapa en CNAME-post som mappar till azurewebsites.net-namn. Mer information finns i [mappa ett anpassat domännamn till en Azure-app](../app-service/app-service-web-tutorial-custom-domain.md) |
| Resource Manager-VM |[Offentlig IP-adress](dns-custom-domain.md#public-ip-address) |Resource Manager-VM kan ha offentliga IP-adresser. En virtuell dator med en offentlig IP-adress kan också vara bakom en belastningsutjämnare. Du kan skapa en DNS A eller CNAME-aliaspost för den offentliga adressen. Den här anpassade namn kan användas för att kringgå VIP i belastningsutjämnaren. |
| Klassiska virtuella datorer |[Offentlig IP-adress](dns-custom-domain.md#public-ip-address) |Klassiska virtuella datorer skapas med hjälp av PowerShell eller CLI kan konfigureras med en dynamisk eller statisk (reserverad) virtuell adress. Du kan skapa ett DNS CNAME eller en post respektive. |
