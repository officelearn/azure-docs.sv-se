---
title: Skapa eller ändra en direkt peering med hjälp av Azure-portalen
titleSuffix: Azure
description: Skapa eller ändra en direkt peering med hjälp av Azure-portalen
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: dcd6aaf584691005dd071a7aba5958070f598978
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681082"
---
# <a name="create-or-modify-a-direct-peering-by-using-the-azure-portal"></a>Skapa eller ändra en direkt peering med hjälp av Azure-portalen

I den här artikeln beskrivs hur du skapar en Microsoft Direct-peering med hjälp av Azure-portalen. Den här artikeln visar också hur du kontrollerar status för resursen, uppdaterar den eller tar bort och avetableras.

Om du vill kan du slutföra den här guiden med hjälp av Azure [PowerShell](howto-direct-powershell.md).

## <a name="before-you-begin"></a>Innan du börjar
* Granska [förutsättningarna](prerequisites.md) och direkt [peer-genomgången](walkthrough-direct-all.md) innan du börjar konfigurera.
* Om du redan har direkt peering-anslutningar med Microsoft som inte konverteras till Azure-resurser läser [du Konvertera en äldre Direkt-peering till en Azure-resurs med hjälp av portalen](howto-legacy-direct-portal.md).

## <a name="create-and-provision-a-direct-peering"></a>Skapa och etablera en direkt peering

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Logga in på portalen och välj din prenumeration
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-a-direct-peering"></a><a name=create></a>Skapa en direkt peering

Du kan skapa en ny peering-begäran med hjälp av **peering-resursen.**

#### <a name="launch-resource-and-configure-basic-settings"></a>Starta resurs och konfigurera grundläggande inställningar
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Konfigurera anslutningar och skicka
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Verifiera direkt peer-peering
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="modify-a-direct-peering"></a><a name="modify"></a>Ändra en direkt peering
[!INCLUDE [peering-direct-modify-portal](./includes/direct-portal-modify.md)]

## <a name="deprovision-a-direct-peering"></a><a name="delete"></a>Avetablera en direkt peering
[!INCLUDE [peering-direct-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>Nästa steg

* [Skapa eller ändra Exchange-peering med hjälp av portalen](howto-exchange-portal.md)
* [Konvertera en äldre Exchange-peering till en Azure-resurs med hjälp av portalen](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Ytterligare resurser

Mer information finns i [Vanliga frågor och frågor om Internet-peering](faqs.md).
