---
title: Konvertera en äldre Direct-peering till en Azure-resurs med hjälp av portalen
titleSuffix: Azure
description: Konvertera en äldre Direct-peering till en Azure-resurs med hjälp av portalen
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: a40c7bbc9f37135814b7bba3396d368faf97a166
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775204"
---
# <a name="convert-a-legacy-exchange-peering-to-azure-resource-using-the-portal"></a>Konvertera en äldre Exchange-peering till en Azure-resurs med hjälp av portalen

I den här artikeln beskrivs hur du konverterar en befintlig äldre Exchange-peering till Azure-resurs med hjälp av portalen.

Om du vill kan du slutföra den här guiden med [PowerShell](howto-legacy-exchange-powershell.md).

## <a name="before-you-begin"></a>Innan du börjar
* Granska [förutsättningar](prerequisites.md) och [Exchange peer-genomgång](walkthrough-exchange-all.md) innan du börjar konfigurationen.

## <a name="convert-a-legacy-exchange-peering-to-azure-resource"></a>Konvertera en äldre Exchange-peering till en Azure-resurs

### <a name="sign-in-to-portal-and-select-your-subscription"></a>Logga in på portalen och välj din prenumeration
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="convert-legacy-exchange-peering"></a><a name=create></a>Konvertera äldre Exchange-peering

Du kan konvertera äldre peering-anslutningar med **peering-resurs.**

#### <a name="launch-resource-and-configure-basic-settings"></a>Starta resurs och konfigurera grundläggande inställningar
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Konfigurera anslutningar och skicka
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration-legacy.md)]

### <a name="verify-exchange-peering"></a><a name=get></a>Verifiera Exchange-peering
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="additional-resources"></a>Ytterligare resurser

Mer information finns på [vanliga frågor om internet peering](faqs.md)

## <a name="next-steps"></a>Nästa steg

* [Skapa eller ändra en Exchange-peering med hjälp av portalen](howto-exchange-portal.md)
