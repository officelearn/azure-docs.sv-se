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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774411"
---
# <a name="create-or-modify-an-exchange-peering-using-powershell"></a>Skapa eller ändra en Exchange-peering med PowerShell

I den här artikeln beskrivs hur du skapar en Microsoft Exchange-peering med hjälp av PowerShell-cmdlets och Resurshanterarens distributionsmodell. Den här artikeln visar också hur du kontrollerar status för resursen, uppdaterar den eller tar bort och avetableras.

Om du vill kan du slutföra den här guiden med hjälp av [portalen](howto-exchange-portal.md).

## <a name="before-you-begin"></a>Innan du börjar
* Granska [förutsättningar](prerequisites.md) och [Exchange peer-genomgång](walkthrough-exchange-all.md) innan du börjar konfigurationen.
* Om du redan har Exchange-peerings med Microsoft, som inte konverteras till Azure-resurser, hänvisa till [Konvertera en äldre Exchange-peering till Azure-resurs med PowerShell](howto-legacy-exchange-powershell.md)

### <a name="working-with-azure-powershell"></a>Arbeta med Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-and-provision-an-exchange-peering"></a>Skapa och etablera en Exchange-peering

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Logga in på ditt Azure-konto och välj din prenumeration
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-the-list-of-supported-peering-locations-for-exchange-peering"></a><a name=exchange-location></a>Hämta listan över peering-platser som stöds för Exchange-peering
[!INCLUDE [exchange-location](./includes/exchange-powershell-create-location.md)]

### <a name="create-an-exchange-peering"></a><a name=create></a>Skapa en Exchange-peering
[!INCLUDE [exchange-peering](./includes/exchange-powershell-create-connection.md)]

### <a name="get-exchange-peering"></a><a name=get></a>Hämta Exchange-peering
[!INCLUDE [peering-exchange-get](./includes/exchange-powershell-get.md)]

## <a name="modify-an-exchange-peering"></a><a name="modify"></a>Ändra en Exchange-peering
[!INCLUDE [peering-exchange-modify](./includes/exchange-powershell-modify.md)]

## <a name="deprovision-an-exchange-peering"></a><a name=delete></a>Avetablera en Exchange-peering

[!INCLUDE [peering-exchange-delete](./includes/delete.md)]

## <a name="next-steps"></a>Nästa steg

* [Skapa eller ändra en direkt peering med PowerShell](howto-direct-powershell.md)
* [Konvertera en äldre Direkt peering till Azure-resurs med PowerShell](howto-legacy-direct-powershell.md)

## <a name="additional-resources"></a>Ytterligare resurser
Du kan få detaljerade beskrivningar av alla parametrar genom att köra följande kommando:

```powershell
Get-Help Get-AzPeering -detailed
```

Mer information finns på [vanliga frågor om internet peering](faqs.md)
