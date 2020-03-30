---
title: Vad är en privat Azure DNS-zon
description: Översikt över en privat DNS-zon
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: a951bc07c4a8ed42b1c116332d13674656bbaafd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646803"
---
# <a name="what-is-a-private-azure-dns-zone"></a>Vad är en privat Azure DNS-zon

Azure Private DNS tillhandahåller en tillförlitlig och säker DNS-tjänst för att hantera och matcha domännamn i ett virtuellt nätverk utan att du behöver lägga till en anpassad DNS-lösning. Genom att använda privata DNS-zoner kan du använda dina egna egna domännamn i stället för de Azure-angivna namn som finns tillgängliga idag. 

Posterna i en privat DNS-zon kan inte lösas från Internet. DNS-matchning mot en privat DNS-zon fungerar bara från virtuella nätverk som är länkade till den.

Du kan länka en privat DNS-zon till ett eller flera virtuella nätverk genom att skapa [virtuella nätverkslänkar](./private-dns-virtual-network-links.md).
Du kan också aktivera funktionen [för automatisk registrering](./private-dns-autoregistration.md) för att automatiskt hantera livscykeln för DNS-posterna för de virtuella datorer som distribueras i ett virtuellt nätverk.

## <a name="limits"></a>Begränsningar

För att förstå hur många privata DNS-zoner du kan skapa i en prenumeration och hur många postuppsättningar som stöds i en privat DNS-zon, se [Azure DNS-gränser](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-dns-limits)

## <a name="restrictions"></a>Begränsningar

* En enda märkta privata DNS-zoner stöds inte. Din privata DNS-zon måste ha två eller flera etiketter. Till exempel har contoso.com två etiketter åtskilda av en punkt. En privat DNS-zon kan ha högst 34 etiketter.
* Du kan inte skapa zondelegering (NS-poster) i en privat DNS-zon. Om du tänker använda en underordnad domän kan du direkt skapa domänen som en privat DNS-zon och länka den till virtuellt nätverk utan att ställa in en namnserverdelegering från den överordnade zonen.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du skapar en privat zon i Azure DNS med hjälp av [Azure PowerShell](./private-dns-getstarted-powershell.md) eller [Azure CLI](./private-dns-getstarted-cli.md).

* Läs om några vanliga [scenarier för privata zoner](./private-dns-scenarios.md) som kan realiseras med privata zoner i Azure DNS.

* Vanliga frågor och svar om privata zoner i Azure DNS, inklusive specifika beteenden som du kan förvänta dig för vissa typer av åtgärder, finns i [Vanliga frågor och svar om privata DNS.](./dns-faq-private.md)
