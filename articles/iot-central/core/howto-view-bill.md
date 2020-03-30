---
title: Hantera din faktura och konvertera från den kostnadsfria prisplanen i Azure IoT Central-programmet | Microsoft-dokument
description: Som administratör kan du läsa om hur du hanterar din faktura och går från den kostnadsfria prisplanen till en standardprisplan i ditt Azure IoT Central-program
author: v-krghan
ms.author: v-krghan
ms.date: 12/09/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 888b9f31b8082ac124a773cc489293b335d7d255
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157539"
---
# <a name="manage-your-bill-in-an-iot-central-application"></a>Hantera din faktura i ett IoT Central-program

I den här artikeln beskrivs hur du som administratör kan hantera din faktura i Azure IoT Central-programmet i administrationsavsnittet. Du får lära dig hur du kan flytta ditt program från den kostnadsfria prisplanen till en standardprisplan, och även hur du uppgraderar eller nedgraderar din prisplan.

Om du vill komma åt och använda avsnittet **Administration** måste du vara i *rollen Administratör* eller ha en *anpassad användarroll* som gör att du kan visa fakturering för ett Azure IoT Central-program. Om du skapar ett Azure IoT Central-program tilldelas du automatiskt **administratörsrollen** för det programmet.

## <a name="move-from-free-to-standard-pricing-plan"></a>Flytta från kostnadsfri prisplan till standard

- Program som använder den kostnadsfria prisplanen är gratis i sju dagar innan de löper ut. För att undvika att förlora data kan du flytta dem till en standardprisplan när som helst innan de löper ut.
- Program som använder en standardprisplan debiteras per enhet, med de två första enheterna gratis, per program.

Läs mer om prissättning på [sidan med prisinformation för Azure IoT Central.](https://azure.microsoft.com/pricing/details/iot-central/)

I prisavsnittet kan du flytta programmet från det kostnadsfria till en standardprisplan.

Så här slutför du självbetjäningsprocessen:

1. Gå till sidan **Prissättning** i avsnittet **Administration.**

    ![Rättegång tillstånd](media/howto-view-bill/freetrialbilling.png)

1. Välj **Konvertera till en betald plan**.

    ![Konvertera rättegång](media/howto-view-bill/convert.png)

1. Välj lämplig Azure Active Directory och sedan azure-prenumerationen som ska användas för ditt program som använder en betald plan.

1. När du har **valt Konvertera**använder programmet nu en betald plan och du börjar bli fakturerad.

> [!Note]
> Som standard konverteras du till en *standard 2-prisplan.*

## <a name="how-to-change-your-application-pricing-plan"></a>Så här ändrar du din programprisplan

Program som använder en standardprisplan debiteras per enhet, med de två första enheterna gratis, per program.

I prisavsnittet kan du uppgradera eller nedgradera din Azure IoT-prisplan när som helst.

1. Gå till sidan **Prissättning** i avsnittet **Administration.**

    ![Rättegång tillstånd](media/howto-view-bill/pricing.png)

1. Välj **Planen** och klicka på **Spara** för att uppgradera eller nedgradera.

## <a name="view-your-bill"></a>Visa din faktura

1. Välj lämplig Azure Active Directory och sedan azure-prenumerationen som ska användas för ditt program som använder en betald plan.

1. När du har **valt Konvertera**använder programmet nu en betald plan och du börjar bli fakturerad.

> [!Note]
> Som standard konverteras du till en *standard 2-prisplan.*

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig om hur du hanterar din faktura i Azure IoT Central-programmet är det föreslagna nästa steget att lära dig mer om [Anpassa programgränssnittet](howto-customize-ui.md) i Azure IoT Central.