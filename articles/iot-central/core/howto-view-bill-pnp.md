---
title: Hantera din faktura och konvertera utvärderings versionen så att du betalar per användning i Azure IoT Central-programmet | Microsoft Docs
description: Som administratör lär du dig att hantera din faktura och konvertera från utvärderings versionen till ditt Azure IoT Central-program
author: v-krghan
ms.author: v-krghan
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: a0d12d762b675141035bebb9308a25cc71360c5c
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2019
ms.locfileid: "72949864"
---
# <a name="manage-your-bill-in-an-iot-central-application"></a>Hantera din faktura i ett IoT Central program

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Den här artikeln beskriver hur du, som administratör, kan hantera din faktura i Azure IoT Central-programmet i avsnittet Administration och hur du kan konvertera utvärderings versionen till betala per användning.

För att få åtkomst till och använda avsnittet **Administration** måste du ha rollen **administratör** för ett Azure IoT Central-program. Om du skapar ett Azure IoT Central-program tilldelas du automatiskt rollen som **administratör** för programmet.

## <a name="view-your-bill"></a>Visa din faktura

Om du vill visa din faktura går du till sidan **fakturering** i avsnittet **Administration** . Sidan Azure-fakturering öppnas på en ny flik där du kan se fakturan för var och en av dina Azure IoT Central-program.

## <a name="convert-your-trial-to-pay-as-you-go"></a>Konvertera din utvärderings version till "betala per användning"

- **Utvärderings** program är kostnads fria i sju dagar innan de upphör att gälla. De kan konverteras till betala per användning när som helst innan de upphör att gälla.
- **Betala** per användning-program debiteras per enhet och de första fem Enheterna är kostnads fria per prenumeration.

Läs mer om prissättning på [sidan med prisinformation för Azure IoT Central.](https://azure.microsoft.com/pricing/details/iot-central/)

I fakturerings avsnittet kan du konvertera dina utvärderings program till "betala per användning".

Följ dessa steg för att slutföra självbetjänings processen:

1. Gå till **fakturerings** sidan i avsnittet **Administration** .

    ![Utvärderings tillstånd](media/howto-view-bill-pnp/freetrialbilling.png)

1. Välj **konvertera för att betala per**användning.

    ![Konvertera utvärderings version](media/howto-view-bill-pnp/convert.png)

1. Välj lämplig Azure Active Directory och sedan den Azure-prenumeration som ska användas för ditt program enligt principen betala per användning.

1. När du har valt **konvertera**är ditt program nu ett program som du betalar per användning och du börjar debiteras.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du hanterar din faktura i Azure IoT Central-programmet är det föreslagna nästa steg att lära dig mer om hur du [anpassar program gränssnittet](howto-customize-ui-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) i Azure IoT Central.