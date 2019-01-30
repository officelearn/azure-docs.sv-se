---
title: Visa förbrukning för offentlig IP-adress i Azure Stack | Microsoft Docs
description: Administratörer kan visa förbrukningen av offentliga IP-adresser i en region
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
ms.date: 01/14/2019
ms.author: mabrigg
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: d97674940f0af91bf50af87cfe96fda9644d469b
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55242060"
---
# <a name="view-public-ip-address-consumption-in-azure-stack"></a>Visa förbrukning för offentlig IP-adress i Azure Stack

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

Som en molnadministratör kan du visa:
 - Hur många offentliga IP-adresser som har tilldelats till klienter.
 - Hur många offentliga IP-adresser som är fortfarande tillgängligt för allokering.
 - Procentandelen av offentliga IP-adresser som har allokerats på den platsen.

Den **offentliga IP-adresspooler användning** panel visar antalet offentliga IP-adresser som förbrukas över offentliga IP-adresspooler. För varje IP-adress visas panelen användning för klient IaaS VM-instanser, fabric infrastrukturtjänster och offentliga IP-adressresurser som uttryckligen har skapats av klienter.

Syftet med panelen är att ge Azure Stack-operatörer en uppfattning om hur många offentliga IP-adresser som används i den här platsen. Hur många hjälper administratörer att avgöra om de har snart slut på den här resursen.

Den **offentliga IP-adresser** menyalternativet under **Klientresurser** visar en lista över de offentliga IP-adresser som har *uttryckligen skapade av innehavarna*. Du kan hitta menyalternativet på den **resursprovidrar**, **nätverk** fönstret. Antalet **används** offentliga IP-adresser på den **offentliga IP-adresspooler användning** panelen är alltid detsamma (större än) antalet på den **offentliga IP-adresser** panelen under  **Klient-resurser**.

## <a name="view-the-public-ip-address-usage-information"></a>Visa den offentliga användningsinformation för IP-adress

Så här visar det totala antalet offentliga IP-adresser som har förbrukats i regionen:

1. Välj i Azure Stack-administratörsportalen kan **alla tjänster**. Sedan, under den **ADMINISTRATION** kategori väljer **nätverk**.
1. Den **nätverk** fönstret visar den **offentliga IP-adresspooler användning** panelen i den **översikt** avsnittet.

![Nätverk Resursprovidern fönstret](media/azure-stack-viewing-public-ip-address-consumption/image01.png)

Den **används** talet representerar antalet tilldelade IP-adresser från offentliga IP-adresspooler. Den **kostnadsfri** talet anger hur många offentliga IP-adresser från offentliga IP-adresspooler som inte har tilldelats och är fortfarande tillgängliga. Den **överföringskö** tal som representerar antalet används eller tilldelade adresser som en procentandel av det totala antalet offentliga IP-adresser i offentliga IP-adresspooler på den platsen.

## <a name="view-the-public-ip-addresses-that-were-created-by-tenant-subscriptions"></a>Visa de offentliga IP-adresser som har skapats av klient-prenumerationer

Välj **offentliga IP-adresser** under **Klientresurser**. Granska listan över offentliga IP-adresser som uttryckligen har skapats av klient prenumerationer i en viss region.

![Offentliga IP-adresser för klient](media/azure-stack-viewing-public-ip-address-consumption/image02.png)

Du kanske märker att vissa offentliga IP-adresser som har tilldelats dynamiskt visas i listan. Men har inte en adress associerats med dem ännu. -Adressresurs har skapats i den Provider för nätverksresurser, men har ännu inte i Network Controller.

Nätverksstyrenheten tilldelar inte en adress till resursen tills det har bindningar till ett gränssnitt, ett nätverkskort (NIC), en belastningsutjämnare eller en virtuell nätverksgateway. När den offentliga IP-adressen Binder till ett gränssnitt, allokerar en IP-adress för nätverkskortet. Adressen visas i den **adress** fält.

## <a name="view-the-public-ip-address-information-summary-table"></a>Visa den offentliga IP-adress information sammanfattningstabell

I annat fall kan tilldelas offentliga IP-adresser som avgör om adressen visas i en lista eller en annan.

| **Offentliga IP-adress tilldelning ärende** | **Visas i Användningsöversikt** | **Visas i offentliga IP-adresser klientlista** |
| --- | --- | --- |
| Dynamiska offentliga IP-adress har ännu inte tilldelats till ett NIC eller belastningsutjämnare (tillfällig) |Nej |Ja |
| Dynamiska offentliga IP-adressen till ett NIC eller belastningsutjämnare. |Ja |Ja |
| Statisk offentlig IP-adress tilldelad till en klient-NIC eller belastningsutjämnare. |Ja |Ja |
| Statisk offentlig IP-adress tilldelas en tjänstslutpunkt för fabric-infrastruktur. |Ja |Nej |
| Offentlig IP-adress implicit skapas för IaaS VM-instanser och används för utgående NAT i det virtuella nätverket. Dessa skapas i bakgrunden när en klient skapar en VM-instans så att virtuella datorer kan skicka information till Internet. |Ja |Nej |

## <a name="next-steps"></a>Nästa steg

[Hantera Lagringskonton i Azure Stack](azure-stack-manage-storage-accounts.md)