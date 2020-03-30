---
title: Skapa eller ändra en direkt peering med PowerShell
titleSuffix: Azure
description: Skapa eller ändra en direkt peering med PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 69031550bdab1535213c78f81426fa76e8ea62ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774242"
---
# <a name="create-or-modify-a-direct-peering-using-powershell"></a>Skapa eller ändra en direkt peering med PowerShell

I den här artikeln beskrivs hur du skapar en Microsoft Direct-peering med hjälp av PowerShell-cmdlets och Resurshanterarens distributionsmodell. Den här artikeln visar också hur du kontrollerar status för resursen, uppdaterar den eller tar bort och avetableras.

Om du vill kan du slutföra den här guiden med hjälp av [portalen](howto-direct-portal.md).

## <a name="before-you-begin"></a>Innan du börjar
* Granska [förutsättningar](prerequisites.md) och [direkt peer-genomgång](walkthrough-direct-all.md) innan du börjar konfigurera.
* Om du redan har direkt peering med Microsoft, som inte konverteras till Azure-resurser, hänvisa till [Konvertera en äldre Direkt-peering till Azure-resurs med PowerShell](howto-legacy-direct-powershell.md)

### <a name="working-with-azure-powershell"></a>Arbeta med Azure PowerShell
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

* [Skapa eller ändra Exchange-peering med PowerShell](howto-exchange-powershell.md).
* [Konvertera en äldre Exchange-peering till Azure-resurs med PowerShell](howto-legacy-exchange-powershell.md).

## <a name="additional-resources"></a>Ytterligare resurser
Du kan få detaljerade beskrivningar av alla parametrar genom att köra följande kommando:

```powershell
Get-Help Get-AzPeering -detailed
```

Mer information finns på [vanliga frågor om internet peering](faqs.md)
