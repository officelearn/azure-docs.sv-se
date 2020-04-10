---
title: Snabbstart – Konfigurera regler och åtgärder i Azure IoT Central
description: Den här snabbstarten visar hur du som byggare konfigurerar telemetribaserade regler och åtgärder i ditt Azure IoT Central-program.
author: dominicbetts
ms.author: dobett
ms.date: 02/12/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 66c3bd8650d1194d5d753c1dc967ec8e870c8748
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80998970"
---
# <a name="quickstart-configure-rules-and-actions-for-your-device-in-azure-iot-central"></a>Snabbstart: Konfigurera regler och åtgärder för din enhet i Azure IoT Central

*Den här artikeln gäller för operatörer, kompilerare och administratörer.*

I den här snabbstarten skapar du en regel som skickar ett e-postmeddelande när temperaturen som rapporteras av en enhetssensor överstiger 90&deg; F.

## <a name="prerequisites"></a>Krav

Innan du börjar bör du slutföra de två tidigare snabbstarterna [Skapa ett Azure IoT Central-program](./quick-deploy-iot-central.md) och [Lägg till en simulerad enhet i ditt IoT Central-program](./quick-create-simulated-device.md) för att skapa **MXChip IoT** DevKit-enhetsmallen som du kan arbeta med.

## <a name="create-a-telemetry-based-rule"></a>Skapa en telemetribaserad regel

1. Om du vill lägga till en ny telemetribaserad regel i ditt program väljer du **Regler**i den vänstra rutan .

1. Om du vill skapa **+** en ny regel väljer du .

1. Ange **miljötemperatur** som regelnamn.

1. I avsnittet **Målenheter** väljer du **MXChip IoT DevKit** som enhetsmall. Det här alternativet filtrerar de enheter som regeln gäller för efter enhetsmalltyp. Du kan lägga till fler filtervillkor genom att välja **+ Filter**.

1. I avsnittet **Villkor** definierar du vad som utlöser regeln. Använd följande information för att definiera ett villkor baserat på temperaturtelemetri:

    | Field        | Värde            |
    | ------------ | ---------------- |
    | Mått  | Temperatur      |
    | Operator     | är större än  |
    | Värde        | 90               |

    Om du vill lägga till fler villkor väljer du **+ Villkor**.

    ![Skapa regelvillkor](./media/quick-configure-rules/condition.png)

1. Om du vill lägga till en e-poståtgärd som ska köras när regeln utlöses väljer du **+ E-post**.

1. Använd informationen i följande tabell för att definiera åtgärden och välj sedan **Klar:**

    | Inställning   | Värde                                             |
    | --------- | ------------------------------------------------- |
    | Visningsnamn | E-poståtgärd för operatör                          |
    | Till        | Din e-postadress                                |
    | Anteckningar     | Miljötemperaturen överskred tröskeln. |

    > [!NOTE]
    > För att ett e-postmeddelande ska kunna tas emot måste e-postadressen vara ett [användar-ID i programmet](howto-administer.md), och användaren måste har loggat in i programmet minst en gång.

    ![Skapa regelåtgärd](./media/quick-configure-rules/action.png)

1. Välj **Spara**. Regeln visas på sidan **Regler.**

## <a name="test-the-rule"></a>Testa regeln

Strax efter att du har sparat regeln blir den aktiv. När villkoren som definierats i regeln uppfylls skickar programmet ett meddelande till den e-postadress som du angav i åtgärden.

> [!NOTE]
> När testet är färdigt stänger du av regeln för att sluta få aviseringar i inkorgen.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig att:

* Skapa en telemetribaserad regel
* Lägga till en åtgärd

Om du vill veta mer om hur du övervakar enheter som är anslutna till ditt program fortsätter du till snabbstarten:

> [!div class="nextstepaction"]
> [Använd Azure IoT Central för att övervaka dina enheter](quick-monitor-devices.md).
