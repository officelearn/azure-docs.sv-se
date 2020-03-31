---
title: Aktivera peering-tjänst på en direkt peering med PowerShell
titleSuffix: Azure
description: Aktivera peering-tjänst på en direkt peering med PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: ac843fa5440f7ba085d33cd897bcd4a1722f77ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774190"
---
# <a name="enable-peering-service-on-a-direct-peering-using-powershell"></a>Aktivera peering-tjänst på en direkt peering med PowerShell

I den här artikeln beskrivs hur du aktiverar [peering-tjänst](overview-peering-service.md) på en direkt peering med hjälp av PowerShell-cmdlets och Resurshanterarens distributionsmodell.

Om du vill kan du slutföra den här guiden med hjälp av [portalen](howto-peering-service-portal.md).

## <a name="before-you-begin"></a>Innan du börjar
* Granska [förutsättningar](prerequisites.md) innan du börjar konfigurera.
* Välj en direkt peering i din prenumeration som du vill aktivera peering-tjänst på. Om du inte har en konverterar du en äldre direkt peering eller skapar en ny Direkt peering.
    * Om du vill konvertera en äldre Direct-peering följer du instruktionerna i [Konvertera en äldre Direct-peering till Azure-resurs med PowerShell](howto-legacy-direct-powershell.md).
    * Om du vill skapa en ny direkt peering följer du instruktionerna i [Skapa eller ändrar en direkt peering med PowerShell](howto-direct-powershell.md).

### <a name="working-with-azure-powershell"></a>Arbeta med Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="enable-peering-service-on-a-direct-peering"></a>Aktivera Peering Service på en Direct-peering

### <a name="view-direct-peering"></a><a name= get></a>Visa direkt peering
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>Aktivera direkt peering för peering-tjänsten

När du har fått direkt peering i föregående steg aktiverar du den för peering-tjänsten.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-powershell.md)]

## <a name="modify-a-direct-peering-connection"></a>Ändra en direkt peering-anslutning

Om du behöver ändra anslutningsinställningarna läser du **Ändra ett direkt peering-avsnitt** i [Skapa eller ändrar en direkt peering med PowerShell](howto-direct-powershell.md)

## <a name="next-steps"></a>Nästa steg

* [Skapa eller ändra Exchange-peering med PowerShell](howto-exchange-powershell.md)
* [Konvertera en äldre Exchange-peering till Azure-resurs med PowerShell](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Ytterligare resurser
Du kan få detaljerade beskrivningar av alla parametrar genom att köra följande kommando:

```powershell
Get-Help Get-AzPeering -detailed
```

Vanliga frågor och svar finns i Vanliga frågor och svar om [peering-tjänsten](service-faqs.md).