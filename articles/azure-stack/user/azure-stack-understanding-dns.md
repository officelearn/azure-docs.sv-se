---
title: 'Förstå IDN: er i Azure Stack | Microsoft Docs'
description: Förstå IDN-funktioner och egenskaper i Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-09/28/2018started-article
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: scottnap
ms.openlocfilehash: 837b96246191bd88ec380f122cf9ece85f822203
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2019
ms.locfileid: "54304541"
---
# <a name="introducing-idns-for-azure-stack"></a>Introduktion till IDN: er för Azure Stack

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

IDN: er är ett nätverk Azure Stack-funktion som gör det möjligt för dig att lösa externa DNS-namn (till exempel http://www.bing.com.) det också möjligt att registrera namn för internt virtuellt nätverk. Då kan lösa du virtuella datorer på samma virtuella nätverk genom att namnet i stället för IP-adress. Den här metoden tar bort behovet av att tillhandahålla anpassade DNS-serverposter. Mer information om DNS finns i den [översikt över Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview).

## <a name="what-does-idns-do"></a>Vad gör IDN: er?

Med IDN: er i Azure Stack får du följande funktioner utan att behöva ange anpassade DNS-serverposter:

- Delade tjänster för DNS-namnmatchning för klienternas arbetsbelastningar.
- Auktoritativ DNS-tjänst för namnmatchning och DNS-registrering i klientens virtuella nätverk.
- Recursive DNS-tjänsten för matchning av Internet namn från virtuella klientdatorer. Klienter som inte längre behöver ange anpassade DNS-poster för att matcha Internet-namn (till exempel www.bing.com.)

Du kan fortfarande ta med din egen DNS och använda anpassade DNS-servrar. Dock med IDN: er kan du kan matcha Internet-DNS-namn och ansluta till andra virtuella datorer i samma virtuella nätverk, behöver du inte skapa anpassade DNS-poster.

## <a name="what-doesnt-idns-do"></a>Vad inte IDN: er?

Vilka IDN: er tillåter inte att du kan utföra, är att skapa en DNS-post för ett namn som kan lösas från utanför det virtuella nätverket.

I Azure har du möjlighet att ange en DNS-Namnetiketten som är associerad med en offentlig IP-adress. Du kan välja etiketten (prefix), men Azure väljer suffix som baseras på den region där du skapar den offentliga IP-adressen.

![Exempel på en DNS-namnetikett](media/azure-stack-understanding-dns-in-tp2/image3.png)

Så som visas i föregående bild, Azure skapar en A-post i DNS för DNS-Namnetiketten som anges under zonen **westus.cloudapp.azure.com**. Prefixet och suffix kombineras för att skapa en [fullständigt kvalificerade domännamnet](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) (FQDN) som kan matchas från var som helst på Internet.

Azure Stack endast stöd för IDN: er för interna namnregistrering, så att den inte kan göra följande:

- Skapa en DNS-post under en befintlig värdbaserade DNS-zon (till exempel local.azurestack.external.)
- Skapa en DNS-zon (t.ex Contoso.com).
- Skapa en post under dina egna anpassade DNS-zon.
- Stöd för köp av domännamn.

## <a name="next-steps"></a>Nästa steg

[Med DNS i Azure Stack](azure-stack-dns.md)
