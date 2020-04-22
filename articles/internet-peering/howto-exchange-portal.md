---
title: Skapa eller ändra en Exchange-peering med hjälp av Azure-portalen
titleSuffix: Azure
description: Skapa eller ändra en Exchange-peering med hjälp av Azure-portalen
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: e4f2ee72cbe17c094567aab5c7cc4720b02cde68
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680957"
---
# <a name="create-or-modify-an-exchange-peering-by-using-the-azure-portal"></a>Skapa eller ändra en Exchange-peering med hjälp av Azure-portalen

I den här artikeln beskrivs hur du skapar en Microsoft Exchange-peering med hjälp av Azure-portalen. Den här artikeln visar också hur du kontrollerar status för resursen, uppdaterar den eller tar bort och avetableras.

Om du vill kan du slutföra den här guiden med [powershell](howto-exchange-powershell.md).

## <a name="before-you-begin"></a>Innan du börjar
* Granska [förutsättningarna](prerequisites.md) och [genomgången Exchange-peering](walkthrough-exchange-all.md) innan du börjar konfigurera.
* Om du redan har Exchange-peerings med Microsoft som inte konverteras till Azure-resurser läser [du Konvertera en äldre Exchange-peering till en Azure-resurs med hjälp av portalen](howto-legacy-exchange-portal.md).

## <a name="create-and-provision-an-exchange-peering"></a>Skapa och etablera en Exchange-peering

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Logga in på portalen och välj din prenumeration
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-an-exchange-peering"></a><a name=create></a>Skapa en Exchange-peering

Du kan skapa en ny peering-begäran med hjälp av **peering-resursen.**

#### <a name="launch-the-resource-and-configure-basic-settings"></a>Starta resursen och konfigurera grundläggande inställningar
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Konfigurera anslutningar och skicka
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration.md)]

### <a name="verify-an-exchange-peering"></a><a name=get></a>Verifiera en Exchange-peering
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="modify-an-exchange-peering"></a><a name="modify"></a>Ändra en Exchange-peering
[!INCLUDE [peering-exchange-modify-portal](./includes/exchange-portal-modify.md)]

## <a name="deprovision-an-exchange-peering"></a><a name="delete"></a>Avetablera en Exchange-peering
[!INCLUDE [peering-exchange-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>Nästa steg

* [Skapa eller ändra en direkt peering med hjälp av portalen](howto-direct-portal.md)
* [Konvertera en äldre direkt peering till en Azure-resurs med hjälp av portalen](howto-legacy-direct-portal.md)

## <a name="additional-resources"></a>Ytterligare resurser

Mer information finns i [Vanliga frågor och frågor om Internet-peering](faqs.md).
