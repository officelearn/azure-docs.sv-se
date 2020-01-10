---
title: Skapa eller ändra en Exchange-peering med PowerShell
titleSuffix: Azure
description: Skapa eller ändra en Exchange-peering med PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 4fd7d345e5efbe6e4e86e5bb410e2df4dd917047
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774411"
---
# <a name="create-or-modify-an-exchange-peering-using-powershell"></a>Skapa eller ändra en Exchange-peering med PowerShell

Den här artikeln beskriver hur du skapar en Microsoft Exchange-peering med hjälp av PowerShell-cmdletar och distributions modellen för Resource Manager. Den här artikeln visar också hur du kontrollerar status för resursen, uppdaterar den eller tar bort och avetablerar den.

Om du vill kan du slutföra den här guiden med hjälp av [portalen](howto-exchange-portal.md).

## <a name="before-you-begin"></a>Innan du börjar
* Granska [krav](prerequisites.md) och [genom gång av Exchange-peering](walkthrough-exchange-all.md) innan du påbörjar konfigurationen.
* Om du redan har Exchange-peering med Microsoft, som inte konverteras till Azure-resurser, se [konvertera en äldre Exchange-peering till Azure-resurs med hjälp av PowerShell](howto-legacy-exchange-powershell.md)

### <a name="working-with-azure-powershell"></a>Arbeta med Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-and-provision-an-exchange-peering"></a>Skapa och etablera en Exchange-peering

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Logga in på ditt Azure-konto och välj din prenumeration
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name=exchange-location></a>Hämta listan över peering-platser som stöds för Exchange-peering
[!INCLUDE [exchange-location](./includes/exchange-powershell-create-location.md)]

### <a name=create></a>Skapa en Exchange-peering
[!INCLUDE [exchange-peering](./includes/exchange-powershell-create-connection.md)]

### <a name=get></a>Hämta Exchange-peering
[!INCLUDE [peering-exchange-get](./includes/exchange-powershell-get.md)]

## <a name="modify"></a>Ändra en Exchange-peering
[!INCLUDE [peering-exchange-modify](./includes/exchange-powershell-modify.md)]

## <a name=delete></a>Avetablera en Exchange-peering

[!INCLUDE [peering-exchange-delete](./includes/delete.md)]

## <a name="next-steps"></a>Nästa steg

* [Skapa eller ändra en direkt peering med hjälp av PowerShell](howto-direct-powershell.md)
* [Konvertera en äldre direkt peering till Azure-resurs med hjälp av PowerShell](howto-legacy-direct-powershell.md)

## <a name="additional-resources"></a>Ytterligare resurser
Du kan få detaljerade beskrivningar av alla parametrar genom att köra följande kommando:

```powershell
Get-Help Get-AzPeering -detailed
```

Mer information finns i [vanliga frågor och svar om Internet-peering](faqs.md)
