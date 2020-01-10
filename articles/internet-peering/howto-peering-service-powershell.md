---
title: Aktivera peering-tjänsten på en direkt peering med hjälp av PowerShell
titleSuffix: Azure
description: Aktivera peering-tjänsten på en direkt peering med hjälp av PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: ac843fa5440f7ba085d33cd897bcd4a1722f77ac
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774190"
---
# <a name="enable-peering-service-on-a-direct-peering-using-powershell"></a>Aktivera peering-tjänsten på en direkt peering med hjälp av PowerShell

Den här artikeln beskriver hur du aktiverar [peering-tjänsten](overview-peering-service.md) på en direkt peering med hjälp av PowerShell-cmdletar och distributions modellen för Resource Manager.

Om du vill kan du slutföra den här guiden med hjälp av [portalen](howto-peering-service-portal.md).

## <a name="before-you-begin"></a>Innan du börjar
* Granska [kraven](prerequisites.md) innan du påbörjar konfigurationen.
* Välj en direkt peering i din prenumeration som du vill aktivera peering-tjänsten på. Om du inte har någon kan du antingen konvertera en äldre direkt peering eller skapa en ny direkt peering.
    * Om du vill konvertera en äldre direkt peering följer du anvisningarna i [konvertera en äldre direkt peering till Azure-resurs med hjälp av PowerShell](howto-legacy-direct-powershell.md).
    * Om du vill skapa en ny direkt peering följer du anvisningarna i [skapa eller ändra en direkt peering med hjälp av PowerShell](howto-direct-powershell.md).

### <a name="working-with-azure-powershell"></a>Arbeta med Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="enable-peering-service-on-a-direct-peering"></a>Aktivera peering-tjänsten på en direkt peering

### <a name= get></a>Visa direkt peering
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

### <a name= get></a>Aktivera direkt peering för peering-tjänsten

När du har skaffat direkt peering i föregående steg aktiverar du det för peering-tjänsten.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-powershell.md)]

## <a name="modify-a-direct-peering-connection"></a>Ändra en direkt peering-anslutning

Om du behöver ändra anslutnings inställningarna kan du läsa avsnittet **ändra en direkt peering** i [skapa eller ändra en direkt peering med hjälp av PowerShell](howto-direct-powershell.md)

## <a name="next-steps"></a>Nästa steg

* [Skapa eller ändra Exchange-peering med PowerShell](howto-exchange-powershell.md)
* [Konvertera en äldre Exchange-peering till Azure-resurs med hjälp av PowerShell](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Ytterligare resurser
Du kan få detaljerade beskrivningar av alla parametrar genom att köra följande kommando:

```powershell
Get-Help Get-AzPeering -detailed
```

Vanliga frågor om [peering-tjänsten finns i vanliga frågor och svar om peering service](service-faqs.md).