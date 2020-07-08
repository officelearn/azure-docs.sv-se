---
title: Aktivera Azure peering-tjänsten på en direkt peering med hjälp av PowerShell
titleSuffix: Azure
description: Aktivera Azure peering-tjänsten på en direkt peering med hjälp av PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 579af2d5cbe0f3dcdbdf749894d5c400112f37cd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84710804"
---
# <a name="enable-azure-peering-service-on-a-direct-peering-by-using-powershell"></a>Aktivera Azure peering-tjänsten på en direkt peering med hjälp av PowerShell

Den här artikeln beskriver hur du aktiverar Azure [peering-tjänsten](overview-peering-service.md) på en direkt peering med hjälp av PowerShell-cmdletar och Azure Resource Manager distributions modell.

Om du vill kan du slutföra den här guiden med hjälp av Azure [Portal](howto-peering-service-portal.md).

## <a name="before-you-begin"></a>Innan du börjar
* Granska [kraven](prerequisites.md) innan du påbörjar konfigurationen.
* Välj en direkt peering i din prenumeration som du vill aktivera peering-tjänsten för. Om du inte har någon kan du antingen konvertera en äldre direkt peering eller skapa en ny direkt peering:
    * Om du vill konvertera en äldre direkt peering följer du anvisningarna i [konvertera en äldre direkt peering till en Azure-resurs med hjälp av PowerShell](howto-legacy-direct-powershell.md).
    * Om du vill skapa en ny direkt peering följer du anvisningarna i [skapa eller ändra en direkt peering med hjälp av PowerShell](howto-direct-powershell.md).

### <a name="work-with-azure-powershell"></a>Arbeta med Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="enable-peering-service-on-a-direct-peering"></a>Aktivera Peering Service på en Direct-peering

### <a name="view-direct-peering"></a><a name= get></a>Visa direkt peering
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>Aktivera direkt peering för peering-tjänsten

När du har direkt peering i föregående steg aktiverar du det för peering-tjänsten.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-powershell.md)]

## <a name="modify-a-direct-peering-connection"></a>Ändra en direkt peering-anslutning

Om du behöver ändra anslutnings inställningarna läser du avsnittet "ändra en direkt peering" i [skapa eller ändra en direkt peering med hjälp av PowerShell](howto-direct-powershell.md).

## <a name="next-steps"></a>Nästa steg

* [Skapa eller ändra Exchange-peering med hjälp av PowerShell](howto-exchange-powershell.md)
* [Konvertera en äldre Exchange-peering till en Azure-resurs med hjälp av PowerShell](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Ytterligare resurser
Du kan få detaljerade beskrivningar av alla parametrar genom att köra följande kommando:

```powershell
Get-Help Get-AzPeering -detailed
```

Vanliga frågor om [peering-tjänsten finns i vanliga frågor och svar om peering-tjänsten](service-faqs.md).