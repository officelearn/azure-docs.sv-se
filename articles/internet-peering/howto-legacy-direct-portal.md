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
ms.openlocfilehash: 9900414d38bd597d08a80d15e908228c06ce06ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775061"
---
# <a name="convert-a-legacy-direct-peering-to-azure-resource-using-the-portal"></a>Konvertera en äldre Direct-peering till en Azure-resurs med hjälp av portalen

I den här artikeln beskrivs hur du konverterar en befintlig äldre Direct-peering till Azure-resurs med hjälp av portalen.

Om du vill kan du slutföra den här guiden med [PowerShell](howto-legacy-direct-powershell.md).

## <a name="before-you-begin"></a>Innan du börjar
* Granska [förutsättningar](prerequisites.md) och [direkt peer-genomgång](walkthrough-direct-all.md) innan du börjar konfigurera.


## <a name="convert-legacy-direct-peering-to-azure-resource"></a>Konvertera äldre direkt peering till Azure-resurs

### <a name="sign-in-to-portal-and-select-your-subscription"></a>Logga in på portalen och välj din prenumeration
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="convert-legacy-direct-peering"></a><a name=create></a>Konvertera äldre direkt peering

Du kan konvertera äldre peering-anslutningar med **peering-resurs.**

#### <a name="launch-resource-and-configure-basic-settings"></a>Starta resurs och konfigurera grundläggande inställningar
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Konfigurera anslutningar och skicka
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration-legacy.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Verifiera direkt peer-peering
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="additional-resources"></a>Ytterligare resurser

Mer information finns på [vanliga frågor om internet peering](faqs.md)

## <a name="next-steps"></a>Nästa steg

* [Skapa eller ändra en direkt peering med hjälp av portalen](howto-direct-portal.md).
