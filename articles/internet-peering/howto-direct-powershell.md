---
title: Skapa eller ändra en direkt peering med powershell
titleSuffix: Azure
description: Skapa eller ändra en direkt peering med powershell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 7639499aaef8d479c2552849b2124e709c46fd36
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680794"
---
# <a name="create-or-modify-a-direct-peering-by-using-powershell"></a>Skapa eller ändra en direkt peering med powershell

I den här artikeln beskrivs hur du skapar en Microsoft Direct-peering med hjälp av PowerShell-cmdlets och Azure Resource Manager-distributionsmodellen. Den här artikeln visar också hur du kontrollerar status för resursen, uppdaterar den eller tar bort och avetableras.

Om du vill kan du slutföra den här guiden med hjälp av [Azure-portalen](howto-direct-portal.md).

## <a name="before-you-begin"></a>Innan du börjar
* Granska [förutsättningarna](prerequisites.md) och direkt [peer-genomgången](walkthrough-direct-all.md) innan du börjar konfigurera.
* Om du redan har direkt peering-anslutningar med Microsoft som inte konverteras till Azure-resurser läser [du Konvertera en äldre Direkt-peering till en Azure-resurs med hjälp av PowerShell](howto-legacy-direct-powershell.md).

### <a name="work-with-azure-powershell"></a>Arbeta med Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-and-provision-a-direct-peering"></a>Skapa och etablera en direkt peering

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Logga in på ditt Azure-konto och välj din prenumeration
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-the-list-of-supported-peering-locations-for-direct-peering"></a><a name=direct-location></a>Hämta listan över peering-platser som stöds för direkt peering
[!INCLUDE [direct-location](./includes/direct-powershell-create-location.md)]

### <a name="create-a-direct-peering"></a><a name=create></a>Skapa en direkt peering
[!INCLUDE [direct-peering](./includes/direct-powershell-create-connection.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Verifiera direkt peer-peering
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

## <a name="modify-a-direct-peering"></a><a name="modify"></a>Ändra en direkt peering
[!INCLUDE [peering-direct-modify](./includes/direct-powershell-modify.md)]

## <a name="deprovision-a-direct-peering"></a><a name="delete"></a>Avetablera en direkt peering
[!INCLUDE [peering-direct-delete](./includes/delete.md)]

## <a name="next-steps"></a>Nästa steg

* [Skapa eller ändra Exchange-peering med powershell](howto-exchange-powershell.md)
* [Konvertera en äldre Exchange-peering till en Azure-resurs med hjälp av PowerShell](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Ytterligare resurser
Du kan få detaljerade beskrivningar av alla parametrar genom att köra följande kommando:

```powershell
Get-Help Get-AzPeering -detailed
```

Mer information finns i [Vanliga frågor och frågor om Internet-peering](faqs.md).
