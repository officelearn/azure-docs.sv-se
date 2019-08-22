---
title: Visa din faktura och konvertera utvärderings versionen för att betala per användning i Azure IoT Central-programmet | Microsoft Docs
description: Som administratör lär du dig att visa din faktura och konvertera från utvärderings versionen och betala per användning i ditt Azure IoT Central-program
author: v-krghan
ms.author: v-krghan
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: d27288718b7f224c78c9cf1f15d05198f628fd15
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2019
ms.locfileid: "69879648"
---
# <a name="view-your-bill-in-an-iot-central-application"></a>Visa din faktura i ett IoT Central program

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Den här artikeln beskriver hur du, som administratör, kan visa din faktura i Azure IoT Central-programmet i avsnittet Administration och även hur du kan konvertera utvärderings versionen till betala per användning.

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

Nu när du har lärt dig om hur du visar din faktura i Azure IoT Central programmet, är det föreslagna nästa steg att lära dig mer om hur du [anpassar program gränssnittet](howto-customize-ui-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) i Azure IoT Central.