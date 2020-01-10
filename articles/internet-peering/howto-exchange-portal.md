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
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774580"
---
# <a name="create-or-modify-an-exchange-peering-using-the-portal"></a>Skapa eller ändra en Exchange-peering med hjälp av portalen

Den här artikeln beskriver hur du skapar en Microsoft Exchange-peering med hjälp av portalen. Den här artikeln visar också hur du kontrollerar status för resursen, uppdaterar den eller tar bort och avetablerar den.

Om du vill kan du slutföra den här guiden med hjälp av [PowerShell](howto-exchange-powershell.md).

## <a name="before-you-begin"></a>Innan du börjar
* Granska [krav](prerequisites.md) och [genom gång av Exchange-peering](walkthrough-exchange-all.md) innan du påbörjar konfigurationen.
* Om du redan har Exchange-peering med Microsoft, som inte konverteras till Azure-resurser, se [konvertera en äldre Exchange-peering till Azure-resurs med hjälp av portalen](howto-legacy-exchange-portal.md)

## <a name="create-and-provision-an-exchange-peering"></a>Skapa och etablera en Exchange-peering

### <a name="sign-in-to-portal-and-select-your-subscription"></a>Logga in på portalen och välj din prenumeration
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name=create></a>Skapa en Exchange-peering

Du kan skapa en ny peering-begäran genom att använda **peering** -resurs.

#### <a name="launch-resource-and-configure-basic-settings"></a>Starta resurs och konfigurera grundläggande inställningar
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Konfigurera anslutningar och skicka
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration.md)]

### <a name=get></a>Verifiera en Exchange-peering
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="modify"></a>Ändra en Exchange-peering
[!INCLUDE [peering-exchange-modify-portal](./includes/exchange-portal-modify.md)]

## <a name="delete"></a>Avetablera en Exchange-peering
[!INCLUDE [peering-exchange-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>Nästa steg

* [Skapa eller ändra en direkt peering med hjälp av portalen](howto-direct-portal.md)
* [Konvertera en äldre direkt peering till Azure-resurs med hjälp av portalen](howto-legacy-direct-portal.md)

## <a name="additional-resources"></a>Ytterligare resurser

Mer information finns i [vanliga frågor och svar om Internet-peering](faqs.md)
