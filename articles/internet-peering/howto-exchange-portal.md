---
title: Skapa eller ändra en Exchange-peering med hjälp av portalen
titleSuffix: Azure
description: Skapa eller ändra en Exchange-peering med hjälp av portalen
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 2c186decf68d167ab2c5ab7696c2dfb51d77a071
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774580"
---
# <a name="create-or-modify-an-exchange-peering-using-the-portal"></a>Skapa eller ändra en Exchange-peering med hjälp av portalen

I den här artikeln beskrivs hur du skapar en Microsoft Exchange-peering med hjälp av portalen. Den här artikeln visar också hur du kontrollerar status för resursen, uppdaterar den eller tar bort och avetableras.

Om du vill kan du slutföra den här guiden med [PowerShell](howto-exchange-powershell.md).

## <a name="before-you-begin"></a>Innan du börjar
* Granska [förutsättningar](prerequisites.md) och [Exchange peer-genomgång](walkthrough-exchange-all.md) innan du börjar konfigurationen.
* Om du redan har Exchange-peerings med Microsoft, som inte konverteras till Azure-resurser, hänvisa till [Konvertera en äldre Exchange-peering till Azure-resurs med hjälp av portalen](howto-legacy-exchange-portal.md)

## <a name="create-and-provision-an-exchange-peering"></a>Skapa och etablera en Exchange-peering

### <a name="sign-in-to-portal-and-select-your-subscription"></a>Logga in på portalen och välj din prenumeration
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-an-exchange-peering"></a><a name=create></a>Skapa en Exchange-peering

Du kan skapa en ny peering-begäran med hjälp av **peering-resurs.**

#### <a name="launch-resource-and-configure-basic-settings"></a>Starta resurs och konfigurera grundläggande inställningar
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

* [Skapa eller ändra en Direct-peering med hjälp av portalen](howto-direct-portal.md)
* [Konvertera en äldre Direct-peering till en Azure-resurs med hjälp av portalen](howto-legacy-direct-portal.md)

## <a name="additional-resources"></a>Ytterligare resurser

Mer information finns på [vanliga frågor om internet peering](faqs.md)
