---
title: Konvertera en äldre Direct-peering till en Azure-resurs med hjälp av Azure-portalen
titleSuffix: Azure
description: Konvertera en äldre Direct-peering till en Azure-resurs med hjälp av Azure-portalen
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: e49e4d5debe63b99039bbafbc14f7788367314f3
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678825"
---
# <a name="convert-a-legacy-direct-peering-to-an-azure-resource-by-using-the-azure-portal"></a>Konvertera en äldre Direct-peering till en Azure-resurs med hjälp av Azure-portalen

I den här artikeln beskrivs hur du konverterar en befintlig äldre Direct-peering till en Azure-resurs med hjälp av Azure-portalen.

Om du vill kan du slutföra den här guiden med [powershell](howto-legacy-direct-powershell.md).

## <a name="before-you-begin"></a>Innan du börjar
* Granska [förutsättningarna](prerequisites.md) och direkt [peer-genomgången](walkthrough-direct-all.md) innan du börjar konfigurera.


## <a name="convert-a-legacy-direct-peering-to-an-azure-resource"></a>Konvertera en äldre direkt peering till en Azure-resurs

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Logga in på portalen och välj din prenumeration
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="convert-a-legacy-direct-peering"></a><a name=create></a>Konvertera en äldre direkt peering

Du kan konvertera äldre peering-anslutningar med hjälp av **peering-resursen.**

#### <a name="launch-the-resource-and-configure-basic-settings"></a>Starta resursen och konfigurera grundläggande inställningar
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Konfigurera anslutningar och skicka
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration-legacy.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Verifiera direkt peer-peering
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="additional-resources"></a>Ytterligare resurser

Mer information finns i [Vanliga frågor och frågor om Internet-peering](faqs.md).

## <a name="next-steps"></a>Nästa steg

* [Skapa eller ändra en direkt peering med hjälp av portalen](howto-direct-portal.md)
