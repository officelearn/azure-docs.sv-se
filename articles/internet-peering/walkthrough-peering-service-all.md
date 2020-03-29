---
title: Genomgång av Peering Service för partner
titleSuffix: Azure
description: Genomgång av Peering Service för partner
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: d626d98613bd5d988b599d0980c885d7f73bb958
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720256"
---
# <a name="peering-service-partner-walkthrough"></a>Genomgång av Peering Service för partner

I det här avsnittet beskrivs de steg som en leverantör måste följa för att aktivera en direkt peering för peering-tjänst.

## <a name="create-direct-peering-connection-for-peering-service"></a>Skapa direkt peering-anslutning för peering-tjänst
Tjänsteleverantörer kan utöka sin geografiska räckvidd genom att skapa ny direkt peering som stöder peering-tjänst. För att åstadkomma detta,
1. Bli peering-tjänstpartner om den inte redan är
1. Följ instruktionerna för att [skapa eller ändra en direkt peering med hjälp av portalen](howto-direct-portal.md). Se till att den uppfyller kraven på hög tillgänglighet.
1. Följ sedan stegen för att [aktivera peering-tjänsten på en direkt peering med hjälp av portalen](howto-peering-service-portal.md).

## <a name="use-legacy-direct-peering-connection-for-peering-service"></a>Använda äldre direkt peering-anslutning för peering-tjänst
Om du har äldre direkt peering som du vill använda för att stödja peering-tjänsten,
1. Bli peering-tjänstpartner om inte redan.
1. Följ instruktionerna för att [konvertera en äldre Direct-peering till Azure-resurs med hjälp av portalen](howto-legacy-direct-portal.md). Om det behövs, beställ ytterligare kretsar för att uppfylla kraven på hög tillgänglighet.
1. Följ sedan stegen för att [aktivera peering-tjänsten på en direkt peering med hjälp av portalen](howto-peering-service-portal.md).

## <a name="next-steps"></a>Nästa steg

* Läs mer om [peering-principen](https://peering.azurewebsites.net/peering).
* Om du vill veta mer om hur du konfigurerar Direkt peering med Microsoft följer du [Direktgenomspelning](walkthrough-direct-all.md).
* Om du vill veta mer om hur du konfigurerar Exchange-peering med Microsoft följer du [exchange-peering-genomgång](walkthrough-exchange-all.md).