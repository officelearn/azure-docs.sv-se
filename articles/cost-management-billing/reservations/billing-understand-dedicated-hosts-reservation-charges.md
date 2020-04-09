---
title: Förstå rabatten för reserverade Azure Dedicated Host-instanser
description: Lär dig hur rabatten för Azure Reserved VM Instances tillämpas på Azure Dedicated Host.
author: yashesvi
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: banders
ms.openlocfilehash: a39cd7aa2c15fedeaf69408d8c8ae8c6b0848fed
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80677390"
---
# <a name="how-the-azure-reservation-discount-is-applied-to-azure-dedicated-hosts"></a>Så tillämpas rabatten för Azure-reservation på Azure Dedicated Hosts

När du har köpt en reserverad Azure Dedicated Hosts-instans tillämpas reservationsrabatten automatiskt på reserverade värdar som matchar reservationens attribut och kvantitet. En reservation omfattar beräkningskostnaderna för dina reserverade värdar.

## <a name="how-reservation-discount-is-applied"></a>Så tillämpas reservationsrabatten

Reservationsrabatter *går förlorade om de inte används*. Om du inte har några matchande resurser för en viss timme förlorar du därmed en reservationskvantitet för den timmen. Det går inte att föra vidare oanvända reserverade timmar.

När du tar bort en reserverad värd tillämpas reservationsrabatten automatiskt på en annan matchande resurs i det angivna omfånget. Om det inte finns några matchande resurser i det angivna omfånget går de reserverade timmarna *förlorade*.

## <a name="reservation-discount-for-dedicated-hosts"></a>Reservationsrabatt för reserverade värdar

Med reserverade Azure Dedicated Hosts-instanser får du rabatt på kostnaden för datainfrastrukturen som används med dina reserverade värdar. Rabatten gäller dina reserverade instanser oavsett om de används av virtuella datorer eller inte. Reservationen täcker inte ytterligare kostnader som licensiering, nätverksanvändning eller lagringsanvändning av en virtuell dator som är distribuerad på den reserverade värden.

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss

Om du har frågor eller behöver hjälp kan du  [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

Du kan läsa mer om Azure-reservationer i följande artiklar:

- [Vad är Azure-reservationer?](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)

- [Använda Azure Dedicated Hosts](https://docs.microsoft.com/azure/virtual-machines/windows/dedicated-hosts)

- [Prissättning för reserverade värdar](https://azure.microsoft.com/pricing/details/virtual-machines/dedicated-host/)

- [Hantera reservationer för Azure](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)

- [Förstå reservationsanvändning för din Betala per användning-prenumeration](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)

- [Förstå reservationsanvändning för din Enterprise-registrering](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)

- [Förstå reservationsanvändning för CSP-prenumerationer](https://docs.microsoft.com/partner-center/azure-reservations)

