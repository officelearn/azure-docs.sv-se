---
title: Konfigurera regler och åtgärder i Azure IoT Central | Microsoft Docs
description: Den här självstudien visar hur du som byggare konfigurerar telemetribaserade regler och åtgärder i Azure IoT Central-programmet.
author: dominicbetts
ms.author: dobett
ms.date: 08/06/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: c2aa6edfe7ce9b670ea1015e2da72f3ecc48c9ec
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878154"
---
# <a name="tutorial-configure-rules-and-actions-for-your-device-in-azure-iot-central-preview-features"></a>Självstudier: Konfigurera regler och åtgärder för din enhet i Azure IoT Central (för hands versions funktioner)

*Den här artikeln gäller för operatörer, kompilerare och administratörer.*

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

I den här självstudien skapar du en regel som skickar ett e-postmeddelande när temperaturen i en miljö sensor&deg; enhet överskrider 90 F.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en telemetribaserad regel
> * Lägga till en åtgärd

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar bör du gå igenom själv studie kursen [definiera en ny enhets typ i program](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) självstudien för att skapa miljösensorns enhets mall att fungera med.

## <a name="create-a-telemetry-based-rule"></a>Skapa en telemetribaserad regel

1. Om du vill lägga till en ny telemetri-baserad regel i programmet väljer du **regler**i den vänstra navigerings menyn.

1. Om du vill skapa en ny regel väljer du **+ ny**. Välj sedan **telemetri**.

1. Ange **miljö temperatur** som regel namn.

1. I avsnittet **omfattning** väljer du **miljö sensorn** som enhets mal len. Omfattningen som de enheter som den här regeln gäller för. Du kan lägga till fler filter villkor med hjälp av **+ filter**.

1. I avsnittet **villkor** definierar du vad som utlöser din regel. Använd följande information för att definiera ett villkor baserat på temperatur telemetri:

    | Fält                                        | Value                             |
    | -------------------------------------------- | ------------------------------    |
    | Mått                                  | Temperatur                       |
    | Operator                                     | är större än                   |
    | Value                                        | 90                                |

    Om du vill lägga till fler villkor väljer du **+ villkor**.

    ![Skapa regel villkor](./media/tutorial-configure-rules-pnp/condition.png)

1. Om du vill lägga till en åtgärd som ska köras när regeln utlöses väljer du **+ åtgärd**och sedan **e-post**.

1. Använd informationen i följande tabell för att definiera din åtgärd:

    | Inställning   | Value                                             |
    | --------- | ------------------------------------------------- |
    | Till        | Din e-postadress                                |
    | Anteckningar     | Miljö temperaturen överskred tröskelvärdet. |

    > [!NOTE]
    > För att ett e-postmeddelande ska kunna tas emot måste e-postadressen vara ett [användar-ID i programmet](howto-administer-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json), och användaren måste har loggat in i programmet minst en gång.

    ![Åtgärden Skapa regel](./media/tutorial-configure-rules-pnp/action.png)

1. Välj **Spara**. Regeln finns på sidan **Regler**.

## <a name="test-the-rule"></a>Testa regeln

Strax efter att du har sparat regeln blir den aktiv. När villkoren som definierats i regeln uppfylls skickar programmet ett meddelande till den e-postadress som du angav i åtgärden.

> [!NOTE]
> När testet är färdigt stänger du av regeln för att sluta få aviseringar i inkorgen.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

* Skapa en telemetribaserad regel
* Lägga till en åtgärd

Nu när du har definierat en tröskel-baserad regel är det föreslagna nästa steg att:

> [!div class="nextstepaction"]
> [Skapa en händelseregel och konfigurera meddelanden](howto-create-event-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).
