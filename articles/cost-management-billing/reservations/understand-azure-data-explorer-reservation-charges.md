---
title: Förstå hur reservationsrabatten tillämpas för Azure Data Explorer
description: Lär dig hur reservationsrabatten tillämpas för Azure Data Explorer-påläggsmätare.
author: avneraa
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/12/2020
ms.openlocfilehash: b222e4ff519fbe2fbb4fa58b39555ac9fead0917
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352923"
---
# <a name="understand-how-the-reservation-discount-is-applied-to-azure-data-explorer"></a>Förstå hur reservationsrabatten tillämpas för Azure Data Explorer

När du har köpt en reserverad Azure Data Explorer-kapacitet tillämpas reservationsrabatten automatiskt på Azure Data Explorer-resurser som matchar reservationens attribut och kvantitet. En reservation omfattar påläggsavgifter för Azure Data Explorer. Den omfattar inte beräkning, nätverk, lagring eller någon annan Azure-resurs som används för att driva Azure Data Explorer-klustret. Reservationer dessa resurser bör köpas separat.

## <a name="how-reservation-discount-is-applied"></a>Så tillämpas reservationsrabatten

Reservationsrabatter *går förlorade om de inte används*. Om du inte har några matchande resurser för en viss timme förlorar du därmed en reservationskvantitet för den timmen. Det går inte att föra vidare oanvända reserverade timmar.

När du avslutar en resurs tillämpas reservationsrabatten automatiskt på en annan matchande resurs i det angivna omfånget. Om det inte finns några matchande resurser i det angivna omfånget går de reserverade timmarna *förlorade*.

## <a name="reservation-discount-applied-to-azure-data-explorer-clusters"></a>Reservationsrabatt som tillämpas på Azure Data Explorer-kluster

En reservationsrabatt tillämpas på Azure Data Explorer-påläggsförbrukningen timme för timme. För Azure Data Explorer-resurser som inte körs hela timmen tillämpas reservationsrabatten automatiskt för andra Azure Data Explorer-resurser som matchar reservationsattributen. Rabatten kan tillämpas på Azure Data Explorer-resurser som körs samtidigt. Om du inte har några Azure Data Explorer-resurser som körs timmen ut och som matchar reservationsattributen får du inte valuta för reservationsrabatten under den timmen.

> [!NOTE]
> * Vi **rekommenderar starkt** att köpa [reservationskapacitet](../../virtual-machines/prepay-reserved-vm-instances.md) för de virtuella datorer som används för Azure Data Explorer-klustret för att maximera besparingarna för reserverad kapacitet.
> * Med reservationsköpet tillämpas rabatterna på alla regioner.

## <a name="examples"></a>Exempel

I följande exempel visas hur rabatten för reserverad Azure Data Explorer-kapacitet tillämpas beroende på det antal påläggsenheter som du har köpt och när de körs.
Exempel för motorklusterstorlek: **2 virtuella D11_v2-datorer**: dina totala på begäran-avgifter är för fyra enheter Azure Data Explorer-påläggsmätare per timme.

**Scenario 1**

Du köper en reserverad Azure Data Explorer-kapacitet för 8 Azure Data Explorer-påläggsenheter. Du kör ett motorkluster med två virtuella D13_v2-datorer med totalt 16 kärnor som debiteras för 16 enheter Azure Data Explorer-påläggsenheter per timme och matchar resten av attributen för reservationen. Du debiteras Betala per användning-priset för åtta kärnor Azure Data Explorer-beräkningsanvändning och får reservationsrabatten för en timmes påläggsenhetsanvändning för åttakärnig Azure Data Explorer.

För resten av dessa exempel förutsätter vi att den reserverade Azure Data Explorer-kapacitet som du köper är avsedd för ett 16-kärnigt Azure Data Explorer-kluster och att resten av reservationens attribut matchar det Azure Data Explorer-kluster som körs.

**Scenario 2**

Du kör två Azure Data Explorer-motorkluster med åtta kärnor i en timme i två olika regioner. Reservationsrabatten för 16 kärnor tillämpas för båda klustren och för de 16 enheter Azure Data Explorer-påläggsenheter de förbrukar.

**Scenario 3**

Du kör ett 16-kärnigt Azure Data Explorer-motorkluster från kl. 13:00 till kl. 13:30. Du kör ett annat 16-kärnigt Azure Data Explorer-motorkluster från kl. 13:30 till kl. 14:00. Båda omfattas av reservationsrabatten.

**Scenario 4**

Du kör ett 16-kärnigt Azure Data Explorer-motorkluster från kl. 13:00 till kl. 13:45. Du kör ett annat 16-kärnigt Azure Data Explorer-motorkluster från kl. 13:30 till kl. 14:00. Du debiteras Betala per användning-priset för överlappningen på 15 minuter. Reservationsrabatten gäller för Azure Data Explorer-påläggsanvändningen för resten av tiden.

Information om hur du förstår och visar tillämpningen av dina Azure-reservationer i användningsrapporter på fakturor finns i [Förstå användning av Azure-reservationer](understand-reserved-instance-usage-ea.md).

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

Du kan läsa mer om Azure-reservationer i följande artiklar:

* [Förskottsbetala Azure Data Explorer-beräkningsresurser med reserverad Azure Data Explorer-kapacitet](/azure/data-explorer/pricing-reserved-capacity)  
* [Vad är reservationer för Azure](save-compute-costs-reservations.md)  
* [Hantera Azure-reservationer](manage-reserved-vm-instance.md)  
* [Förstå reservationsanvändning för din Betala per användning-prenumeration](understand-reserved-instance-usage.md)
* [Förstå reservationsanvändning för din Enterprise-registrering](understand-reserved-instance-usage-ea.md)
* [Förstå reservationsanvändning för CSP-prenumerationer](/partner-center/azure-reservations)