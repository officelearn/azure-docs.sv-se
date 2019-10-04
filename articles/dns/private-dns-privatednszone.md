---
title: Vad är en Azure DNS privat zon
description: Översikt över en privat DNS-zon
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: 462c873221a4bdc622a9b118b6699a9719a5f88d
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/04/2019
ms.locfileid: "71961197"
---
# <a name="what-is-a-private-azure-dns-zone"></a>Vad är en privat Azure DNS zon

Azure Privat DNS ger en tillförlitlig och säker DNS-tjänst för att hantera och lösa domän namn i ett virtuellt nätverk utan att behöva lägga till en anpassad DNS-lösning. Genom att använda privata DNS-zoner kan du använda dina egna anpassade domän namn istället för de namn som tillhandahålls av Azure som är tillgängliga idag. 

De poster som finns i en privat DNS-zon kan inte matchas från Internet. DNS-matchning mot en privat DNS-zon fungerar bara från virtuella nätverk som är länkade till den.

Du kan länka en privat DNS-zon till ett eller flera virtuella nätverk genom att skapa [virtuella nätverks länkar](./private-dns-virtual-network-links.md).
Du kan också aktivera funktionen för [automatisk registrering](./private-dns-autoregistration.md) för att automatiskt hantera livs cykeln för DNS-poster för de virtuella datorer som distribueras i ett virtuellt nätverk.

## <a name="limits"></a>Begränsningar

För att förstå hur många privata DNS-zoner du kan skapa i en prenumeration och hur många post uppsättningar som stöds i en privat DNS-zon, se [Azure DNS gränser](https://docs.microsoft.com/azure/azure-subscription-service-limits#azure-dns-limits)

## <a name="restrictions"></a>Begränsningar

* Enkla, namngivna privata DNS-zoner stöds inte. Din privata DNS-zon måste ha två eller flera etiketter. Till exempel contoso.com har två etiketter avgränsade med en punkt. En privat DNS-zon kan ha maximalt 34 etiketter.
* Du kan inte skapa zon delegeringar (NS-poster) i en privat DNS-zon. Om du tänker använda en underordnad domän kan du direkt skapa domänen som en privat DNS-zon och länka den till det virtuella nätverket utan att konfigurera en namnserver-delegering från den överordnade zonen.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du skapar en privat zon i Azure DNS med [Azure PowerShell](./private-dns-getstarted-powershell.md) eller [Azure CLI](./private-dns-getstarted-cli.md).

* Läs om några vanliga [scenarier för privata](./private-dns-scenarios.md) zoner som kan realiseras med privata zoner i Azure DNS.

* Vanliga frågor och svar om privata zoner i Azure DNS, inklusive specifika funktioner som kan förväntas för vissa typer av åtgärder, finns i [privat DNS vanliga frågor](./dns-faq-private.md)och svar.
