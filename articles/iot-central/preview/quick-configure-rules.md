---
title: Snabb start – Konfigurera regler och åtgärder i Azure IoT Central
description: Den här snabb starten visar dig som ett verktyg för att konfigurera telemetri-baserade regler och åtgärder i ditt Azure IoT Central-program.
author: dominicbetts
ms.author: dobett
ms.date: 12/09/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 489a512ea5658aaeaccdba4b91eae7def14a589c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75434808"
---
# <a name="quickstart-configure-rules-and-actions-for-your-device-in-azure-iot-central-preview-features"></a>Snabb start: Konfigurera regler och åtgärder för din enhet i Azure IoT Central (för hands versions funktioner)

*Den här artikeln gäller för operatörer, kompilerare och administratörer.*

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

I den här snabb starten skapar du en regel som skickar ett e-postmeddelande när temperaturen i en miljö sensor enhet överskrider 90&deg; F.

## <a name="prerequisites"></a>Krav

Innan du börjar bör du slutföra de två föregående snabb starterna [skapa ett Azure IoT Central-program](./quick-deploy-iot-central.md) och [lägga till en simulerad enhet till ditt IoT Central-program](./quick-create-pnp-device.md) för att skapa en **miljö sensor** enhets mall att arbeta med.

## <a name="create-a-telemetry-based-rule"></a>Skapa en telemetribaserad regel

1. Om du vill lägga till en ny telemetri-baserad regel i programmet väljer du **regler**i det vänstra fönstret.

1. Om du vill skapa en ny regel väljer du **+ ny**.

1. Ange **miljö temperatur** som regel namn.

1. I avsnittet **mål enheter** väljer du **miljö sensorn** som enhets mal len. Med det här alternativet filtreras de enheter som regeln gäller för efter typ av enhets mal len. Du kan lägga till fler filter villkor genom att välja **+ filter**.

1. I avsnittet **villkor** definierar du vad som utlöser din regel. Använd följande information för att definiera ett villkor baserat på temperatur telemetri:

    | Field                                        | Värde                             |
    | -------------------------------------------- | ------------------------------    |
    | Mått                                  | Temperatur                       |
    | Operator                                     | är större än                   |
    | Värde                                        | 90                                |

    Om du vill lägga till fler villkor väljer du **+ villkor**.

    ![Skapa regel villkor](./media/quick-configure-rules/condition.png)

1. Om du vill lägga till en e-poståtgärd som ska köras när regeln utlöses väljer du **+ e-post**.

1. Använd informationen i följande tabell för att definiera din åtgärd:

    | Inställning   | Värde                                             |
    | --------- | ------------------------------------------------- |
    | Visningsnamn | E-poståtgärd för operatör                          |
    | Till        | Din e-postadress                                |
    | Anteckningar     | Miljö temperaturen överskred tröskelvärdet. |

    > [!NOTE]
    > För att ett e-postmeddelande ska kunna tas emot måste e-postadressen vara ett [användar-ID i programmet](howto-administer.md), och användaren måste har loggat in i programmet minst en gång.

    ![Åtgärden Skapa regel](./media/quick-configure-rules/action.png)

1. Välj **Spara**. Regeln finns på sidan **Regler**.

## <a name="test-the-rule"></a>Testa regeln

Strax efter att du har sparat regeln blir den aktiv. När villkoren som definierats i regeln uppfylls skickar programmet ett meddelande till den e-postadress som du angav i åtgärden.

> [!NOTE]
> När testet är färdigt stänger du av regeln för att sluta få aviseringar i inkorgen.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig att:

* Skapa en telemetribaserad regel
* Lägga till en åtgärd

Om du vill veta mer om övervakning av enheter som är anslutna till ditt program fortsätter du till snabb starten:

> [!div class="nextstepaction"]
> [Använd Azure-IoT central för att övervaka dina enheter](quick-monitor-devices.md).
