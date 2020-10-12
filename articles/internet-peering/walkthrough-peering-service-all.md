---
title: Genomgång av Peering Service för partner
titleSuffix: Azure
description: Genomgång av Peering Service för partner
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 75ec1a4ededfea4f9b40461d69b6e16d947e6919
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "84712487"
---
# <a name="peering-service-partner-walkthrough"></a>Genomgång av Peering Service för partner

I det här avsnittet beskrivs de steg som en provider måste följa för att aktivera en direkt peering för peering-tjänsten.

## <a name="create-direct-peering-connection-for-peering-service"></a>Skapa direkt peering-anslutning för peering-tjänsten
Tjänste leverantörer kan utöka sin geografiska räckvidd genom att skapa en ny direkt peering som stöder peering-tjänsten. För att åstadkomma detta
1. Bli en peering service-partner om den inte redan är
1. Följ instruktionerna för att [skapa eller ändra en direkt peering med hjälp av portalen](howto-direct-portal.md). Se till att det uppfyller kraven för hög tillgänglighet.
1. Följ sedan stegen för att [Aktivera peering-tjänsten på en direkt peering med hjälp av portalen](howto-peering-service-portal.md).

## <a name="use-legacy-direct-peering-connection-for-peering-service"></a>Använd äldre direkt peering-anslutning för peering-tjänsten
Om du har äldre direkt peering som du vill använda för att stödja peering-tjänsten
1. Bli en peering service-partner om du inte redan gjort det.
1. Följ instruktionerna för att [konvertera en äldre direkt peering till Azure-resursen med hjälp av portalen](howto-legacy-direct-portal.md). Om det behövs beställer du ytterligare kretsar för att uppfylla kraven på hög tillgänglighet.
1. Följ sedan stegen för att [Aktivera peering-tjänsten på en direkt peering med hjälp av portalen](howto-peering-service-portal.md).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [peering-principen](https://peering.azurewebsites.net/peering).
* Om du vill veta mer om hur du konfigurerar direkt peering med Microsoft följer du [genom gången av direkt peering](walkthrough-direct-all.md).
* Om du vill veta mer om hur du konfigurerar Exchange-peering med Microsoft följer du [genom gången av Exchange-peering](walkthrough-exchange-all.md).