---
title: Hämta den aktuella POP IP-listan för Azure CDN| Microsoft-dokument
description: Läs om hur du hämtar den aktuella POP-listan.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2019
ms.author: magattus
ms.custom: ''
ms.openlocfilehash: 95b85aa11d99ddd48c90c8d9fa28789e79ee979f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72299248"
---
# <a name="retrieve-the-current-pop-ip-list-for-azure-cdn"></a>Hämta den aktuella POP IP-listan för Azure CDN

## <a name="retrieve-the-current-verizon-pop-ip-list-for-azure-cdn"></a>Hämta den aktuella Verizon POP IP-listan för Azure CDN

Du kan använda REST API för att hämta uppsättningen AV IPs för Verizons point of presence (POP) servrar. Dessa POP-servrar gör förfrågningar till ursprungsservrar som är associerade med CDN-slutpunkter (Azure Content Delivery Network) på en Verizon-profil (**Azure CDN Standard från Verizon** eller Azure **CDN Premium från Verizon**). Observera att den här uppsättningen IP-adresser skiljer sig från de IP-adresser som en klient skulle se när du gör förfrågningar till POP. 

Syntaxen för REST API-åtgärden för att hämta POP-listan finns i [Edge-noder - lista](https://docs.microsoft.com/rest/api/cdn/edgenodes/list).

## <a name="retrieve-the-current-microsoft-pop-ip-list-for-azure-cdn"></a>Hämta den aktuella Microsoft POP IP-listan för Azure CDN

Om du vill låsa programmet för att endast acceptera trafik från Azure CDN från Microsoft måste du konfigurera IP-åtkomstkontrollant för din backend. Du kan också begränsa uppsättningen godkända värden för rubriken "X-Forwarded-Host" som skickas av Azure CDN från Microsoft. Dessa steg beskrivs nedan:

Konfigurera IP ACLing för dina serverenheter för att acceptera trafik från Azure CDN från Microsofts serverd IP-adressutrymme och Azures infrastrukturtjänster. 

* Azure CDN från Microsofts IPv4-ip-utrymme för säkerhetskopiering: 147.243.0.0/16
* Azure CDN från Microsofts IP-utrymme för IPv6-backend: 2a01:111:2050::/44

IP-intervall och tjänsttaggar för Microsoft-tjänster hittar du [här](https://www.microsoft.com/download/details.aspx?id=56519)


## <a name="typical-use-case"></a>Vanligt användningsfall

Av säkerhetsskäl kan du använda den här IP-listan för att framtvinga att begäranden till din ursprungsserver endast görs från en giltig Verizon POP. Om någon till exempel upptäckte värdnamnet eller IP-adressen för en CDN-slutpunkts ursprungsserver kan man göra begäranden direkt till ursprungsservern och därmed kringgå skalnings- och säkerhetsfunktionerna som tillhandahålls av Azure CDN. Genom att ange IPs i den returnerade listan som de enda tillåtna IPs på en ursprungsserver kan det här scenariot förhindras. Om du vill vara säkra på att du har den senaste POP-listan hämtar du den minst en gång om dagen. 

## <a name="next-steps"></a>Nästa steg

Information om REST API finns i [Azure CDN REST API](https://docs.microsoft.com/rest/api/cdn/).
