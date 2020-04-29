---
title: Konvertera en äldre Exchange-peering till en Azure-resurs med hjälp av Azure Portal
titleSuffix: Azure
description: Konvertera en äldre Exchange-peering till en Azure-resurs med hjälp av Azure Portal
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 87a7a6bca608f1748d3b659eabdc3e941b537377
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81678562"
---
# <a name="convert-a-legacy-exchange-peering-to-an-azure-resource-by-using-the-azure-portal"></a>Konvertera en äldre Exchange-peering till en Azure-resurs med hjälp av Azure Portal

Den här artikeln beskriver hur du konverterar en befintlig äldre Exchange-peering till en Azure-resurs med hjälp av Azure Portal.

Om du vill kan du slutföra den här guiden med hjälp av [PowerShell](howto-legacy-exchange-powershell.md).

## <a name="before-you-begin"></a>Innan du börjar
* Granska [kraven](prerequisites.md) och [genom gången av Exchange-peering](walkthrough-exchange-all.md) innan du påbörjar konfigurationen.

## <a name="convert-a-legacy-exchange-peering-to-an-azure-resource"></a>Konvertera en äldre Exchange-peering till en Azure-resurs

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Logga in på portalen och välj din prenumeration
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="convert-legacy-exchange-peering"></a><a name=create></a>Konvertera äldre Exchange-peering

Du kan konvertera äldre peering-anslutningar med hjälp av **peering** -resursen.

#### <a name="launch-the-resource-and-configure-basic-settings"></a>Starta resursen och konfigurera grundläggande inställningar
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Konfigurera anslutningar och skicka
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration-legacy.md)]

### <a name="verify-exchange-peering"></a><a name=get></a>Verifiera Exchange-peering
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="additional-resources"></a>Ytterligare resurser

Mer information finns i [vanliga frågor och svar om Internet-peering](faqs.md).

## <a name="next-steps"></a>Nästa steg

* [Skapa eller ändra en Exchange-peering med hjälp av portalen](howto-exchange-portal.md)
