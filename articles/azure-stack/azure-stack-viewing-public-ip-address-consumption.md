---
title: Visa offentlig IP-adress användning i Azure-stacken | Microsoft Docs
description: Administratörer kan visa förbrukningen av offentliga IP-adresser i en region
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 0f77be49-eafe-4886-8c58-a17061e8120f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/28/2018
ms.author: mabrigg
ms.openlocfilehash: 50bf01d6de6105d3041c6bb88e803f3d110f751d
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2018
ms.locfileid: "29742466"
---
# <a name="view-public-ip-address-consumption-in-azure-stack"></a>Visa offentlig IP-adress användning i Azure-stacken

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Som en moln-administratör kan visa du:
 - Antalet offentliga IP-adresser som har tilldelats till innehavare.
 - Antalet offentliga IP-adresser som är fortfarande tillgängliga för tilldelning.
 - Procentandelen av offentliga IP-adresser som har tilldelats på den platsen.

Den **offentliga IP-adresspooler användning** panelen visar antalet offentliga IP-adresser som används i offentliga IP-adresspooler. För varje IP-adress visas panelen användning för klienten IaaS VM instanser, fabric infrastrukturtjänster och offentliga IP-adress-resurser som uttryckligen har skapats av klienter.

Syftet med panelen är att ge Azure-stacken operatörer en uppfattning om antalet offentliga IP-adresser som används i den här platsen. Antalet hjälper administratörer att avgöra om de körs lågt på den här resursen.

Den **offentliga IP-adresser** menyalternativet **Klientresurser** visas endast de offentliga IP-adresser som har *skapats av klientorganisationer explicit*. Du hittar menyalternativet på den **resursproviders**, **nätverk** fönstret. Antalet **används** offentliga IP-adresser på det **offentliga IP-adresspooler användning** panelen alltid skiljer sig från (större än) numret på den **offentliga IP-adresser** panelen under  **Resurser för innehavare**.

## <a name="view-the-public-ip-address-usage-information"></a>Visa användningsinformation för den offentliga IP-adress
Så här visar det totala antalet offentliga IP-adresser som har förbrukats i region:

1. Välj i Azure Stack-administratörsportalen kan **fler tjänster**under **administrationsresurser**väljer **resursproviders**.
2. I listan över **Resursproviders**väljer **nätverket**.
3. Den **nätverk** fönstret den **offentliga IP-adresspooler användning** panelen i den **översikt** avsnitt.

![Nätverket Resursprovidern fönstret](media/azure-stack-viewing-public-ip-address-consumption/image01.png)

Den **används** representerar antalet tilldelade offentliga IP-adresser från offentliga IP-adresspooler. Den **lediga** nummer representerar antalet offentliga IP-adresser från offentliga IP-adresspooler som inte har tilldelats och är fortfarande tillgängliga. Den **% används** representerar antalet används eller tilldelade adresser som en procentandel av det totala antalet offentliga IP-adresser i offentliga IP-adresspooler på den platsen.

## <a name="view-the-public-ip-addresses-that-were-created-by-tenant-subscriptions"></a>Visa de offentliga IP-adresser som har skapats av klient prenumerationer
Välj **offentliga IP-adresser** under **Klientresurser**. Granska listan med offentliga IP-adresser som uttryckligen har skapats av klient prenumerationer i en viss region.

![Klient offentliga IP-adresser](media/azure-stack-viewing-public-ip-address-consumption/image02.png)

Du kan se att vissa offentliga IP-adresser som har tilldelats dynamiskt visas i listan. Men har inte en adress associerats med dem ännu. Adressresursen har skapats i den Nätverksresursprovidern, men har ännu inte i Network Controller.

Nätverksstyrenheten tilldelar inte en adress till resursen förrän det har bindningar till ett gränssnitt, nätverksgränssnittskortet (NIC), en belastningsutjämnare eller en virtuell nätverksgateway. När den offentliga IP-adressen binds till ett gränssnitt, allokerar en IP-adress för nätverkskortet. Adressen visas i den **adress** fältet.

## <a name="view-the-public-ip-address-information-summary-table"></a>Visa den offentliga IP-adress information Sammanfattningstabellen
I annat fall tilldelas offentliga IP-adresser som avgör om adressen som ska visas i en lista eller en annan.

| **Offentliga IP-adresstilldelning fall** | **Visas i Användningsöversikt** | **Visas i listan för klient offentliga IP-adresser** |
| --- | --- | --- |
| Dynamiska offentliga IP-adressen har ännu inte tilldelats ett nätverkskort eller läsa in belastningsutjämning (tillfällig) |Nej |Ja |
| Dynamiska offentliga IP-adressen till ett nätverkskort eller läsa in belastningsutjämning. |Ja |Ja |
| Statiska offentliga IP-adressen till en klient-NIC eller läsa in belastningsutjämning. |Ja |Ja |
| Statiska offentliga IP-adressen till en tjänstslutpunkt för fabric-infrastruktur. |Ja |Nej |
| Offentliga IP-adressen implicit för IaaS-VM-instanser och används för utgående NAT på det virtuella nätverket. Dessa skapas i bakgrunden när en klient skapar en VM-instans så att virtuella datorer kan skicka information till Internet. |Ja |Nej |

## <a name="next-steps"></a>Nästa steg
[Hantera Storage-konton i Azure-stacken](azure-stack-manage-storage-accounts.md)