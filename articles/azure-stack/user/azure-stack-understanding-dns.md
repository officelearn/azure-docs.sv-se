---
title: 'Förstå IDN: er i Azure-stacken | Microsoft Docs'
description: Förstå IDN-funktioner och möjligheter i Azure-stacken
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/21/2018
ms.author: mabrigg
ms.reviewer: scottnap
ms.openlocfilehash: 9123160f42adea57c28dff265bd5b5dbbcbb7918
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34724266"
---
# <a name="introducing-idns-for-azure-stack"></a>Introduktion till IDN: er för Azure-Stack

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

IDN: er är en Azure-stacken nätverk funktion som gör det möjligt för dig att lösa externa DNS-namn (till exempel http://www.bing.com.) det ger dig möjlighet att registrera internt virtuellt nätverksnamn. På så sätt, kan du åtgärda virtuella datorer på samma virtuella nätverk namn i stället för IP-adress. Den här metoden tar bort behovet av att tillhandahålla anpassade poster i DNS-server. Mer information om DNS finns i [översikt över Azure DNS](https://docs.microsoft.com/en-us/azure/dns/dns-overview).

## <a name="what-does-idns-do"></a>Vad är IDN: er?

Med IDN: er i Azure-stacken får du följande funktioner utan att ange anpassade poster i DNS-server:

- Delade tjänster för DNS-namnmatchning för klienternas arbetsbelastningar.
- Auktoritära DNS-tjänsten för namnmatchning och DNS-registrering i innehavarens virtuella nätverk.
- Recursive DNS-tjänsten för matchning av Internet namn från innehavaren virtuella datorer. Klienter som inte längre behöver ange anpassade DNS-poster för att lösa Internet-namn (till exempel www.bing.com.)

Du kan fortfarande sätta egna DNS och använda anpassade DNS-servrar. Emellertid med IDN: er kan du matcha Internet-DNS-namn och ansluta till andra virtuella datorer i samma virtuella nätverk, behöver du inte skapar anpassade DNS-poster.

## <a name="what-doesnt-idns-do"></a>Vad inte IDN: er?

Vilka IDN tillåter inte att göra, är att skapa en DNS-post för ett namn som kan lösas från utanför det virtuella nätverket.

I Azure har möjlighet att ange ett DNS-namnetikett som är associerad med en offentlig IP-adress. Du kan välja etiketten (prefix), men Azure väljer suffix som baseras på den region där du skapar den offentliga IP-adressen.

![Exempel på en DNS-namnetikett](media/azure-stack-understanding-dns-in-tp2/image3.png)

Eftersom föregående bild visas Azure skapar en A-post i DNS för DNS-namnetikett som anges under zonen **westus.cloudapp.azure.com**. Prefixet och suffixet kombineras för att skapa en [fullständigt kvalificerade domännamnet](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) (FQDN) som kan matchas från var som helst på Internet.

Azure-stacken stöder bara IDn för interna namnregistrering, så det går inte att göra följande:

- Skapa en DNS-post i en befintlig värdbaserade DNS-zon (till exempel local.azurestack.external.)
- Skapa en DNS-zon (t.ex Contoso.com).
- Skapa en post under dina egna anpassade DNS-zon.
- Stöd för köp av domännamn.

## <a name="next-steps"></a>Nästa steg

[Med hjälp av DNS i Azure-stacken](azure-stack-dns.md)
