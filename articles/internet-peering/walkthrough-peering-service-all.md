---
title: Genom gång av peering service partner
titleSuffix: Azure
description: Genom gång av peering service partner
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 28aa9f921de64fcc838935cf65b64a63280ddf16
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774632"
---
# <a name="peering-service-partner-walkthrough"></a>Genom gång av peering service partner

I det här avsnittet beskrivs de steg som en provider måste följa för att aktivera en direkt peering för peering-tjänsten.

## <a name="create-direct-peering-connection-for-peering-service"></a>Skapa direkt peering-anslutning för peering-tjänsten
Tjänste leverantörer kan utöka sin geografiska räckvidd genom att skapa en ny direkt peering som stöder peering-tjänsten. För att åstadkomma detta
1. Bli en peering service-partner om den inte redan är
1. Följ instruktionerna för att [skapa eller ändra en direkt peering med hjälp av portalen](howto-direct-portal.md). Se till att det uppfyller kraven för hög tillgänglighet.
1. Följ sedan stegen för att [Aktivera peering-tjänsten på en direkt peering med hjälp av portalen](howto-peering-service-portal.md).

## <a name="use-legacy-direct-peering-conection-for-peering-service"></a>Använd äldre anslutnings för direkt peering för peering-tjänsten
Om du har äldre direkt peering som du vill använda för att stödja peering-tjänsten
1. Bli en peering service-partner om du inte redan gjort det.
1. Följ instruktionerna för att [konvertera en äldre direkt peering till Azure-resursen med hjälp av portalen](howto-legacy-direct-portal.md). Om det behövs beställer du ytterligare kretsar för att uppfylla kraven på hög tillgänglighet.
1. Följ sedan stegen för att [Aktivera peering-tjänsten på en direkt peering med hjälp av portalen](howto-peering-service-portal.md).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [peering-principen](https://peering.azurewebsites.net/peering).
* Om du vill veta mer om hur du konfigurerar direkt peering med Microsoft följer du [genom gången av direkt peering](walkthrough-direct-all.md).
* Om du vill veta mer om hur du konfigurerar Exchange-peering med Microsoft följer du [genom gången av Exchange-peering](walkthrough-exchange-all.md).