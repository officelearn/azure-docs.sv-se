---
title: Använda Azure DNS med andra Azure-tjänster
description: I den här utbildningssökvägen kommer du igång med hur du använder Azure DNS för att matcha namn för andra Azure-tjänster
services: dns
documentationcenter: na
author: rohinkoul
manager: kumudD
tags: azure dns
ms.assetid: e9b5eb94-7984-4640-9930-564bb9e82b78
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 09/21/2016
ms.author: rohink
ms.openlocfilehash: fa2c1ced6405c967ca33562d6215b304b8507e5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76937243"
---
# <a name="how-azure-dns-works-with-other-azure-services"></a>Så här fungerar Azure DNS med andra Azure-tjänster

Azure DNS är en värd-TV-hantering och namnmatchningstjänst. Du kan använda den för att skapa offentliga DNS-namn för andra program och tjänster som du distribuerar i Azure. Det är enkelt att skapa ett namn för en Azure-tjänst i din anpassade domän. Du lägger bara till en post av rätt typ för din tjänst.

* För dynamiskt allokerade IP-adresser kan du skapa en DNS CNAME-post som mappar till DET DNS-namn som Azure skapade för din tjänst. DNS-standarder hindrar dig från att använda en CNAME-post för zonsponsen. Du kan använda en aliaspost i stället. Mer information finns i [Självstudiekurs: Konfigurera en aliaspost för att referera till en offentlig Azure-IP-adress](tutorial-alias-pip.md).
* För statiskt allokerade IP-adresser kan du skapa en DNS A-post med vilket namn som helst, vilket innehåller ett *naket domännamn* i zonapexen.

I följande tabell beskrivs de posttyper som stöds som du kan använda för olika Azure-tjänster. Som tabellen visar stöder Azure DNS endast DNS-poster för Internet-vända nätverksresurser. Azure DNS kan inte användas för namnmatchning av interna, privata adresser.

| Azure-tjänst | Nätverksgränssnitt | Beskrivning |
| --- | --- | --- |
| Azure Application Gateway |[Front-end offentlig IP](dns-custom-domain.md#public-ip-address) |Du kan skapa en DNS A- eller CNAME-post. |
| Azure Load Balancer |[Front-end offentlig IP](dns-custom-domain.md#public-ip-address) |Du kan skapa en DNS A- eller CNAME-post. Belastningsutjämnaren kan ha en IPv6-offentlig IP-adress som har tilldelats dynamiskt. Skapa en CNAME-post för en IPv6-adress. |
| Azure Traffic Manager |Offentligt namn |Du kan skapa en aliaspost som mappar till det trafficmanager.net namn som tilldelats traffic manager-profilen. Mer information finns i [Självstudiekurs: Konfigurera en aliaspost för att stödja apex-domännamn med Traffic Manager](tutorial-alias-tm.md). |
| Azure Cloud Services |[Offentlig IP](dns-custom-domain.md#public-ip-address) |För statiskt allokerade IP-adresser kan du skapa en DNS A-post. För dynamiskt allokerade IP-adresser måste du skapa en CNAME-post som mappar till *cloudapp.net* namn.|
| Azure Apptjänst | [Extern IP](dns-custom-domain.md#app-service-web-apps) |För externa IP-adresser kan du skapa en DNS A-post. Annars måste du skapa en CNAME-post som mappar till azurewebsites.net namn. Mer information finns i [Mappa ett anpassat domännamn till en Azure-app](../app-service/app-service-web-tutorial-custom-domain.md). |
| Virtuella datorer i Azure Resource Manager |[Offentlig IP](dns-custom-domain.md#public-ip-address) |Virtuella datorer i Resource Manager kan ha offentliga IP-adresser. En virtuell dator med en offentlig IP-adress kan också ligga bakom en belastningsutjämnare. Du kan skapa en DNS A-, CNAME- eller aliaspost för den offentliga adressen. Du kan använda det här anpassade namnet för att kringgå VIP på belastningsutjämnaren. |
| Klassiska virtuella datorer |[Offentlig IP](dns-custom-domain.md#public-ip-address) |Klassiska virtuella datorer som skapas med PowerShell eller CLI kan konfigureras med en dynamisk eller statisk (reserverad) virtuell adress. Du kan skapa ett DNS CNAME eller en A-post. |
