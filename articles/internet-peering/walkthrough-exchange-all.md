---
title: Genomgång av Exchange-peering
titleSuffix: Azure
description: Genomgång av Exchange-peering
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: bb761afa6d8953b441b6c9541c43b73031719494
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775178"
---
# <a name="exchange-peering-walkthrough"></a>Genomgång av Exchange-peering

I det här avsnittet beskrivs de steg du behöver följa för att konfigurera och hantera en Exchange-peering.

## <a name="create-an-exchange-peering"></a>Skapa en Exchange-peering
> [!div class="mx-imgBorder"]
> ![Arbetsflödes- och anslutningstillstånd för Exchange peering](./media/exchange-peering.png)

Följande steg måste följas för att etablera en Exchange-peering:
1. Granska [Microsofts peering-policy](https://peering.azurewebsites.net/peering) för att förstå kraven för Exchange-peering.
1. Hitta Microsofts peering-plats och peering-anläggnings-ID i [PeeringDB](https://www.peeringdb.com/net/694)
1. Begär Exchange-peering för en peering-plats med hjälp av instruktionerna i [Skapa och ändra en Exchange-peering med PowerShell-artikel](howto-exchange-powershell.md) för mer information.
1. När du har skickat in en peering-begäran granskar Microsoft begäran och kontaktar dig om det behövs.
1. När anslutningstillståndet har godkänts ändras det till Godkänd
1. Konfigurera BGP-session i slutet och meddela Microsoft
1. Vi kommer att etablera BGP-session med NEKA ALLA-principen och validera end-to-end.
1. Om det lyckas får du ett meddelande om att peering-anslutningstillståndet är aktivt.
1. Trafiken kommer då att tillåtas genom den nya peering.

Observera att anslutningstillstånd inte ska förväxlas med vanliga [BGP-sessionstillstånd.](https://en.wikipedia.org/wiki/Border_Gateway_Protocol)

## <a name="convert-a-legacy-exchange-peering-to-azure-resource"></a>Konvertera en äldre Exchange-peering till en Azure-resurs
Följande steg måste följas för att konvertera en äldre Exchange-peering till Azure-resurs:
1. Följ instruktionerna i [Konvertera en äldre Exchange-peering till Azure-resurs](howto-legacy-exchange-powershell.md)
1. När du har skickat in konverteringsbegäran granskar Microsoft begäran och kontaktar dig om det behövs.
1. När du har godkänts ser du din Exchange-peering med anslutningstillstånd som Aktiv.

## <a name="deprovision-exchange-peering"></a>Avetablering Exchange peering
Kontakta [Microsoft peering](mailto:peering@microsoft.com) för att avetablera Exchange-peering.

När en Exchange-peering är inställd för avetablering visas anslutningstillståndet som **PendingRemove**

> [!NOTE]
> Om du kör PowerShell-cmdlet för att ta bort Exchange-peering när anslutningstillståndet är EtableringStarted eller EtableringKomelt misslyckas åtgärden.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [Förutsättningar för att konfigurera peering med Microsoft](prerequisites.md).
