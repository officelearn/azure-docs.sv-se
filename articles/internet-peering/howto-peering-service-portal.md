---
title: Aktivera peering-tjänsten på en direkt peering med hjälp av portalen
titleSuffix: Azure
description: Aktivera peering-tjänsten på en direkt peering med hjälp av portalen
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: d6b67c42ef8a5ba5ae98894775d1f56cee39ba8b
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774983"
---
# <a name="enable-peering-service-on-a-direct-peering-using-the-portal"></a>Aktivera peering-tjänsten på en direkt peering med hjälp av portalen

I den här artikeln beskrivs hur du aktiverar [peering-tjänsten](overview-peering-service.md) på en direkt peering med hjälp av portalen.

Om du vill kan du slutföra den här guiden med hjälp av [PowerShell](howto-peering-service-powershell.md).

## <a name="before-you-begin"></a>Innan du börjar
* Granska [kraven](prerequisites.md) innan du påbörjar konfigurationen.
* Välj en direkt peering i din prenumeration som du vill aktivera peering-tjänsten på. Om du inte har någon kan du antingen konvertera en äldre direkt peering eller skapa en ny direkt peering.
    * Om du vill konvertera en äldre direkt peering följer du anvisningarna i [konvertera en äldre direkt peering till Azure-resursen med hjälp av portalen](howto-legacy-direct-portal.md).
    * Om du vill skapa en ny direkt peering följer du anvisningarna i [skapa eller ändra en direkt peering med hjälp av portalen](howto-direct-portal.md).

## <a name="enable-peering-service-on-a-direct-peering"></a>Aktivera peering-tjänsten på en direkt peering

### <a name= get></a>Visa direkt peering
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

### <a name= get></a>Aktivera direkt peering för peering-tjänsten

När du har öppnat direkt peering i föregående steg aktiverar du den för peering-tjänsten.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-portal.md)]

## <a name="modify-a-direct-peering-connection"></a>Ändra en direkt peering-anslutning

Om du behöver ändra anslutnings inställningarna läser du avsnittet **ändra ett direkt peering** i [skapa eller ändra en direkt peering med hjälp av portalen](howto-direct-portal.md)

## <a name="next-steps"></a>Nästa steg

* [Skapa eller ändra Exchange-peering med hjälp av portalen](howto-exchange-portal.md)
* [Konvertera en äldre Exchange-peering till Azure-resursen med hjälp av portalen](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Ytterligare resurser

Vanliga frågor om [peering-tjänsten finns i vanliga frågor och svar om peering service](service-faqs.md).