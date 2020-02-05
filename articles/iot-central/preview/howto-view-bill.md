---
title: Hantera din faktura och konvertera från den kostnads fria pris planen i Azure IoT Central-programmet | Microsoft Docs
description: Som administratör kan du lära dig hur du hanterar din faktura och flyttar från den kostnads fria pris Planen till en standard pris plan i Azure IoT Central-programmet
author: v-krghan
ms.author: v-krghan
ms.date: 12/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: adc7b59eebf513042667c0c585c05307aeab07b2
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990897"
---
# <a name="manage-your-bill-in-an-iot-central-application"></a>Hantera din faktura i ett IoT Central program

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Den här artikeln beskriver hur du, som administratör, kan hantera din faktura i Azure IoT Central-programmet i avsnittet Administration. Du får lära dig hur du kan flytta ditt program från den kostnads fria pris Planen till en standard pris plan och även uppgradera eller nedgradera pris planen.

För att få åtkomst till och använda avsnittet **Administration** , måste du vara i *Administratörs* rollen eller ha en *anpassad användar roll* som gör att du kan visa fakturering för ett Azure IoT Central-program. Om du skapar ett Azure IoT Central-program tilldelas du automatiskt rollen som **administratör** för programmet.

## <a name="move-from-free-to-standard-pricing-plan"></a>Flytta från kostnads fri till standard pris plan

- Program som använder den kostnads fria pris Planen är kostnads fria i sju dagar innan de upphör att gälla. För att undvika att förlora data kan du när som helst flytta dem till en standard pris plan innan de upphör att gälla.

I avsnittet prissättning kan du flytta ditt program från kostnads fritt till en standard pris plan.

Följ dessa steg för att slutföra självbetjänings processen:

1. Gå till sidan med **priser** i avsnittet **Administration** .

    ![Utvärderings tillstånd](media/howto-view-bill/freetrialbilling.png)

1. Välj **konvertera till en betald plan**.

    ![Konvertera utvärderings version](media/howto-view-bill/convert.png)

1. Välj lämplig Azure Active Directory och sedan Azure-prenumerationen som ska användas för ditt program som använder en betald plan.

1. När du har valt **konvertera**använder programmet nu en betald plan och du börjar debiteras.

> [!Note]
> Som standard konverteras du till en pris sättnings plan med *Standard 2* .

## <a name="how-to-change-your-application-pricing-plan"></a>Ändra din program pris plan

- Program som använder en standard pris plan debiteras per enhet, med de två första enheterna kostnads fria, per program.

I avsnittet prissättning kan du när som helst uppgradera eller nedgradera din Azure IoT-pris plan.

1. Gå till sidan med **priser** i avsnittet **Administration** .

    ![Utvärderings tillstånd](media/howto-view-bill/pricing.png)

1. Välj **planen** och klicka på **Spara** för att uppgradera eller nedgradera.

Läs mer om prissättning på [sidan med prisinformation för Azure IoT Central.](https://azure.microsoft.com/pricing/details/iot-central/)

## <a name="view-your-bill"></a>Visa din faktura

Om du vill visa din faktura går du till sidan **prissättning** i avsnittet **Administration** . Sidan med priser för Azure öppnas på en ny flik där du kan se fakturan för var och en av dina Azure IoT Central-program.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du hanterar din faktura i Azure IoT Central-programmet är det föreslagna nästa steg att lära dig mer om hur du [anpassar program gränssnittet](howto-customize-ui.md) i Azure IoT Central.