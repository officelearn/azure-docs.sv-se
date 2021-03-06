---
author: vhorne
ms.service: dns
ms.topic: include
ms.date: 11/25/2018
ms.author: victorh
ms.openlocfilehash: 74031a8dbc9b64d6a09533789eed1296ff334d47
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "67188004"
---
En DNS-zon används som värd åt DNS-posterna för en viss domän. Om du vill låta Azure DNS vara värd för din domän så måste du skapa en DNS-zon för det domännamnet. Varje DNS-post för din domän skapas sedan i den här DNS-zonen.

Domänen contoso.com kan t.ex. innehålla flera DNS-poster, som mail.contoso.com (för en e-postserver) och www.contoso.com (för en webbplats).

När du skapar en DNS-zon i Azure DNS:

* Namnet på zonen måste vara unikt inom resursgruppen och zonen får inte redan finnas. Annars misslyckas åtgärden.
* Samma zonnamn kan återanvändas i en annan resursgrupp eller Azure-prenumeration.
* Om flera zoner som delar samma namn, tilldelas varje instans sin egen namnserveradress. Endast en uppsättning adresser kan konfigureras hos domänamnsregistratorn.

> [!NOTE]
> Du behöver inte äga ett domännamn för att kunna skapa en DNS-zon med det domännamnet i Azure DNS. Du måste dock äga domänen för att kunna konfigurera Azure DNS-namnservrarna som rätt namnservrar för domännamnet hos domännamnsregistratorn.
> 
> Mer information finns i [delegera en domän till Azure DNS](../articles/dns/dns-domain-delegation.md).