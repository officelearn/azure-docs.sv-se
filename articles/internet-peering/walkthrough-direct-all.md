---
title: Genomgång av Direct-peering
titleSuffix: Azure
description: Genomgång av Direct-peering
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: d88fcfc4d3e073bf544f2ca0f4d01dbe305b45da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775516"
---
# <a name="direct-peering-walkthrough"></a>Genomgång av Direct-peering

I det här avsnittet beskrivs de steg du behöver följa för att konfigurera och hantera en direkt peering.

## <a name="create-a-direct-peering"></a>Skapa en direkt peering
> [!div class="mx-imgBorder"]
> ![Arbetsflödes- och anslutningstillstånd för direkt peering](./media/direct-peering.png)

Följande steg måste följas för att etablera en direkt peering:
1. Granska [Microsofts peering-policy](https://peering.azurewebsites.net/peering) för att förstå kraven för direkt peering.
1. Följ instruktionerna i [Skapa eller ändra en direkt peering](howto-direct-powershell.md) för att skicka en peering-begäran.
1. När du har skickat en peering-begäran kontaktar Microsoft din registrerade e-postadress för att tillhandahålla LOA (Letter Of Authorization) eller för annan information.
1. När peering-begäran har godkänts ändras anslutningstillståndet till EtableringStartad.
1. Du måste:
    1. komplett ledningar enligt LOA
    1. (valfritt) utföra länktest med 169.254.0.0/16
    1. konfigurera BGP-session och meddela oss.
1. Microsoft bestämmelser BGP session med neka alla princip och validera end-to-end.
1. Om det lyckas får du ett meddelande om att peering-anslutningstillståndet är aktivt.
1. Trafiken kommer då att tillåtas genom den nya peering.

Observera att anslutningstillstånd inte ska förväxlas med vanliga [BGP-sessionstillstånd.](https://en.wikipedia.org/wiki/Border_Gateway_Protocol)

## <a name="convert-a-legacy-direct-peering-to-azure-resource"></a>Konvertera en äldre Direct-peering till en Azure-resurs
Följande steg måste följas för att konvertera en äldre direct peering till Azure-resurs:
1. Följ instruktionerna i [Konvertera en äldre direkt peering till Azure-resurs](howto-legacy-direct-powershell.md)
1. När du har skickat in konverteringsbegäran granskar Microsoft begäran och kontaktar dig om det behövs.
1. När du har godkänts kommer du att se din Direkt peering med ett anslutningstillstånd som Aktiv.

## <a name="deprovision-direct-peering"></a>Avetablering Direkt peering
Kontakta [Microsofts peering-team](mailto:peering@microsoft.com) för att avetablera direkt peering.

När en direkt peering är inställd för avetablering visas anslutningstillståndet som **PendingRemove**

> [!NOTE]
> Om du kör PowerShell-cmdlet för att ta bort direct-peering när ConnectionState är EtableringStarted eller EtableringKomelt misslyckas åtgärden.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [Förutsättningar för att konfigurera peering med Microsoft](prerequisites.md).
