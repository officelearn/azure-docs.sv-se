---
title: "Visa offentlig IP-adress användning i Azure-stacken | Microsoft Docs"
description: "Administratörer kan visa förbrukningen av offentliga IP-adresser i en region"
services: azure-stack
documentationcenter: 
author: ScottNapolitan
manager: darmour
editor: 
ms.assetid: 0f77be49-eafe-4886-8c58-a17061e8120f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: scottnap
ms.openlocfilehash: 7651565eebf6272f307a4ce4790ca19b41bfa826
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="view-public-ip-address-consumption-in-azure-stack"></a>Visa offentlig IP-adress användning i Azure-stacken

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Du kan visa antalet offentliga IP-adresser som har allokerats till klienter, antal offentliga IP-adresser som är fortfarande tillgängliga för tilldelning och procentandelen av offentliga IP-adresser som har tilldelats på den platsen som en moln-administratör.

Den **offentliga IP-adresspooler användning** panelen visar det totala antalet offentliga IP-adresser som har förbrukats över alla offentliga IP-adresspooler på infrastrukturen, om de har använts för klienten IaaS VM instanser infrastruktur tjänster eller offentlig IP-resurser som uttryckligen har skapats av klienter.

Syftet med den här panelen är att ge Azure-stacken administratörer en uppfattning om det totala antalet offentliga IP-adresser som har förbrukats på den här platsen. Detta hjälper administratörer att avgöra om de körs lågt på den här resursen.

På den **resursproviders**, **nätverk** bladet den **offentliga IP-adresser** menyalternativet **Klientresurser** visas endast de offentligt IP-adresser som har *skapats av klientorganisationer explicit*. Således antalet **används** offentliga IP-adresser på det **offentliga IP-adresspooler användning** panelen alltid skiljer sig från (större än) numret på den **offentliga IP-adresser** panelen under **klient resurser**.

## <a name="view-the-public-ip-address-usage-information"></a>Visa användningsinformation för den offentliga IP-adress
Så här visar det totala antalet offentliga IP-adresser som har förbrukats i region:

1. Klicka på Azure Stack-administratörsportalen kan **fler tjänster**under **administrationsresurser**, klickar du på **resursproviders**.
2. I listan över **Resursproviders**väljer **nätverket**.
3. Den **nätverk** bladet visar den **offentliga IP-adresspooler användning** panelen i den **översikt** avsnitt.

![Resursprovidern nätverksblad](media/azure-stack-viewing-public-ip-address-consumption/image01.png)

Tänk på att den **används** representerar antalet offentliga IP-adresser från alla offentliga IP-adresspooler på den platsen som har tilldelats. Den **lediga** nummer representerar antalet offentliga IP-adresser från alla offentliga IP-adresspooler som inte har tilldelats och är fortfarande tillgängliga. Den **% används** representerar antalet används eller tilldelade adresser som en procentandel av det totala antalet offentliga IP-adresser i alla offentliga IP-adresspooler på den platsen.

## <a name="view-the-public-ip-addresses-that-were-created-by-tenant-subscriptions"></a>Visa de offentliga IP-adresser som har skapats av klient prenumerationer
Klicka för att visa en lista över offentliga IP-adresser som uttryckligen har skapats av klient prenumerationer i en viss region **offentliga IP-adresser** under **Klientresurser**.

![Klient offentliga IP-adresser](media/azure-stack-viewing-public-ip-address-consumption/image02.png)

Du kan se att vissa offentliga IP-adresser som har tilldelats dynamiskt visas i listan, men inte har en adress som är kopplade till dem ännu. Det beror på att adressresursen har skapats i den Nätverksresursprovidern, men inte i nätverksstyrenheten ännu.

Nätverksstyrenheten tilldelar inte en adress till den här resursen förrän den faktiskt är bunden till ett gränssnitt, nätverksgränssnittskortet (NIC), en belastningsutjämnare eller en virtuell nätverksgateway. När den offentliga IP-adressen är bunden till ett gränssnitt, nätverksstyrenheten allokerar en IP-adress till den och den visas i den **adress** fältet.

## <a name="view-the-public-ip-address-information-summary-table"></a>Visa den offentliga IP-adress information Sammanfattningstabellen
Det finns ett antal olika fall där offentliga IP-adresser tilldelas som avgör om adressen som ska visas i en lista eller en annan.

| **Offentliga IP-adresstilldelning fall** | **Visas i Användningsöversikt** | **Visas i listan för klient offentliga IP-adresser** |
| --- | --- | --- |
| Dynamiska offentliga IP-adressen har ännu inte tilldelats ett nätverkskort eller läsa in belastningsutjämning (tillfällig) |Nej |Ja |
| Dynamiska offentliga IP-adressen till ett nätverkskort eller läsa in belastningsutjämning. |Ja |Ja |
| Statiska offentliga IP-adressen till en klient-NIC eller läsa in belastningsutjämning. |Ja |Ja |
| Statiska offentliga IP-adressen till en tjänstslutpunkt för fabric-infrastruktur. |Ja |Nej |
| Offentliga IP-adressen implicit för IaaS-VM-instanser och används för utgående NAT på det virtuella nätverket. Dessa skapas i bakgrunden när en klient skapar en VM-instans så att virtuella datorer kan skicka information till Internet. |Ja |Nej |

## <a name="next-steps"></a>Nästa steg
[Hantera Storage-konton i Azure-stacken](azure-stack-manage-storage-accounts.md)