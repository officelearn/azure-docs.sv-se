---
title: Aktivera Azure Peering Service på en direkt peering med powershell
titleSuffix: Azure
description: Aktivera Azure Peering Service på en direkt peering med powershell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: c029b822d00ce545e3623a6212421a55b2d1971f
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686975"
---
# <a name="enable-azure-peering-service-on-a-direct-peering-by-using-powershell"></a>Aktivera Azure Peering Service på en direkt peering med powershell

I den här artikeln beskrivs hur du aktiverar Azure [Peering Service](overview-peering-service.md) på en Direct-peering med hjälp av PowerShell-cmdlets och Azure Resource Manager-distributionsmodellen.

Om du vill kan du slutföra den här guiden med hjälp av [Azure-portalen](howto-peering-service-portal.md).

## <a name="before-you-begin"></a>Innan du börjar
* Granska [förutsättningarna](prerequisites.md) innan du börjar konfigurera.
* Välj en direkt peering i din prenumeration som du vill aktivera Peering Service för. Om du inte har någon konverterar du en äldre direkt peering eller skapar en ny Direkt peering:
    * Om du vill konvertera en äldre Direct-peering följer du instruktionerna i [Konvertera en äldre Direkt peering till en Azure-resurs med hjälp av PowerShell](howto-legacy-direct-powershell.md).
    * Om du vill skapa en ny direkt peering följer du instruktionerna i [Skapa eller ändrar en direkt peering med hjälp av PowerShell](howto-direct-powershell.md).

### <a name="work-with-azure-powershell"></a>Arbeta med Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="enable-peering-service-on-a-direct-peering"></a>Aktivera Peering Service på en Direct-peering

### <a name="view-direct-peering"></a><a name= get></a>Visa direkt peering
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>Aktivera direkt peering för peering-tjänsten

När du har direkt peering i föregående steg aktiverar du den för peering-tjänsten.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-powershell.md)]

## <a name="modify-a-direct-peering-connection"></a>Ändra en direkt peering-anslutning

Om du behöver ändra anslutningsinställningar läser du avsnittet Ändra en direkt peering i [Skapa eller ändra en direkt peering med PowerShell](howto-direct-powershell.md).

## <a name="next-steps"></a>Nästa steg

* [Skapa eller ändra Exchange-peering med powershell](howto-exchange-powershell.md)
* [Konvertera en äldre Exchange-peering till en Azure-resurs med hjälp av PowerShell](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Ytterligare resurser
Du kan få detaljerade beskrivningar av alla parametrar genom att köra följande kommando:

```powershell
Get-Help Get-AzPeering -detailed
```

Vanliga frågor och svar finns i vanliga frågor och [svar om peering-tjänsten](service-faqs.md).