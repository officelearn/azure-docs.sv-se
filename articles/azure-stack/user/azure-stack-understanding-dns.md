---
title: "Förstå DNS i Azure-stacken | Microsoft Docs"
description: "Förstå DNS-funktioner och funktioner i Azure-stacken"
services: azure-stack
documentationcenter: 
author: ScottNapolitan
manager: darmour
editor: 
ms.assetid: 60f5ac85-be19-49ac-a7c1-f290d682b5de
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: scottnap
ms.openlocfilehash: 381947a5c936b2bbcae1cf61cdd36eb1653be30b
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2018
---
# <a name="introducing-idns-for-azure-stack"></a>Introduktion till IDN: er för Azure-Stack

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

IDN: er är en funktion i Azure-stacken som hjälper dig att lösa externa DNS-namn (till exempel http://www.bing.com).
Du kan också registrera interna virtuella nätverksnamn. På så sätt, kan du åtgärda virtuella datorer på samma virtuella nätverk namn i stället för IP-adress, utan att ange anpassade poster i DNS-server.

Det är något som har alltid varit det i Azure, men den är tillgänglig i Windows Server 2016 och Azure-stacken för.

## <a name="what-does-idns-do"></a>Vad är IDN: er?
Med IDN: er i Azure-stacken får du följande funktioner utan att ange anpassade poster i DNS-server:

* Delade tjänster för DNS-namnmatchning för klienternas arbetsbelastningar.
* Auktoritära DNS-tjänsten för namnmatchning och DNS-registrering i innehavarens virtuella nätverk.
* Recursive DNS-tjänsten för matchning av Internet namn från innehavaren virtuella datorer. Klienterna behöver inte längre ange anpassade DNS-poster för att lösa Internet-namn (till exempel www.bing.com).

Du kan fortfarande sätta egna DNS och använda anpassade DNS-servrar om du vill. Men nu om du bara vill kunna matcha Internet-DNS-namn och kunna ansluta till andra virtuella datorer i samma virtuella nätverk, behöver du inte ange något och den fungerar bara.

## <a name="what-does-idns-not-do"></a>Vad är IDN inte?
Vilka IDN tillåter inte att göra är att skapa en DNS-post för ett namn som kan lösas från utanför det virtuella nätverket.

I Azure har möjlighet att ange ett DNS-namnetikett som kan associeras med en offentlig IP-adress. Du kan välja etiketten (prefix), men Azure väljer suffix som baseras på den region där du skapar den offentliga IP-adressen.

![Skärmbild av DNS-namnetikett](media/azure-stack-understanding-dns-in-tp2/image3.png)

I bilden ovan Azure skapar en A-post i DNS för DNS-namnetikett som anges under zonen **westus.cloudapp.azure.com**. Prefixet och suffixet tillsammans utgöra ett fullständigt kvalificerat domännamn (FQDN) som kan matchas från var som helst på Internet.

Azure-stacken stöder bara IDn för interna namnregistrering, så det går inte att göra följande:

* Skapa en DNS-post i en befintlig värdbaserade DNS-zon (till exempel local.azurestack.external).
* Skapa en DNS-zon (t.ex Contoso.com).
* Skapa en post under dina egna anpassade DNS-zon.
* Stöd för köp av domännamn.

