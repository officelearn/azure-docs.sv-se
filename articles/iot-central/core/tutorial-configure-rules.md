---
title: 'Självstudie: Konfigurera regler och åtgärder i Azure IoT Central'
description: Den här självstudien visar hur du som byggare konfigurerar telemetribaserade regler och åtgärder i Azure IoT Central-programmet.
author: ankitscribbles
ms.author: ankitgup
ms.date: 11/13/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 9140114e7d31f24770bdcce9aae849b01aae9996
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2020
ms.locfileid: "76263685"
---
# <a name="tutorial-configure-rules-and-actions-for-your-device-in-azure-iot-central"></a>Självstudie: Konfigurera regler och åtgärder för enheten i Azure IoT Central

*Den här artikeln gäller för operatörer, kompilerare och administratörer.*

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

I den här självstudien skapar du en regel som skickar ett e-postmeddelande när temperaturen i en ansluten Luft Konditionerings enhet överskrider 70&deg; F.

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
>
> * Skapa en telemetribaserad regel
> * Lägga till en åtgärd

## <a name="prerequisites"></a>Krav

Innan du börjar bör du slutföra självstudien om att [definiera en ny enhetstyp i programmet](tutorial-define-device-type.md) för att skapa enhetsmallen **Ansluten luftkonditioneringsenhet** som du kommer att arbeta med.

## <a name="create-a-telemetry-based-rule"></a>Skapa en telemetribaserad regel

1. Om du vill lägga till en ny telemetri-baserad regel i programmet väljer du **enhets mal**lin i den vänstra rutan:

    ![Sidan Enhetsmallar](media/tutorial-configure-rules/templatespage1.png)

    Du ser enhetsmallen **Connected Air Conditioner (1.0.0)** som du skapade i föregående självstudie.

2. Om du vill anpassa din enhets mall väljer du den **anslutna Luft Konditionerings** mal len som du skapade i föregående självstudie.

3. Om du vill lägga till en telemetri-baserad regel i vyn **regler** väljer du **regler**, väljer **+ ny regel**och väljer sedan **telemetri**:

    ![Vyn Regler](media/tutorial-configure-rules/newrule.png)

4. Använd informationen i följande tabell för att definiera regeln:

    | Inställning                                      | Värde                             |
    | -------------------------------------------- | ------------------------------    |
    | Namn                                         | Temperaturmeddelande för luftkonditionering |
    | Aktivera regeln för alla enheter med den här mallen | På                                |
    | Villkor                                    | Temperatur är större än 70    |
    | Sammansättning                                  | Inget                              |

    ![Regelvillkor för temperatur](media/tutorial-configure-rules/temperaturerule.png)

    Välj sedan **Spara**.

## <a name="add-an-action"></a>Lägga till en åtgärd

När du definierar en regel kan du även definiera en åtgärd som ska köras när regelvillkoren uppfylls. I den här självstudien skapar du en regel med en åtgärd som skickar en e-postavisering.

1. För att lägga till en **åtgärd** **sparar** du regeln och rullar sedan ned på panelen **Konfigurera telemetriregel**. Välj **+** bredvid **Åtgärder** och välj sedan **E-post**:

    ![Regelåtgärd för temperatur](media/tutorial-configure-rules/addaction.png)

2. Använd informationen i följande tabell för att definiera åtgärden:

    | Inställning      | Värde                                               |
    | ------------ | --------------------------------------------------- |
    | Visningsnamn | Avisering om e-posttemperatur                             |
    | Till           | Din e-postadress                                  |
    | Anteckningar        | Temperaturen i luftkonditioneringsenheten har överskridit tröskelvärdet. |

    > [!NOTE]
    > För att ett e-postmeddelande ska kunna tas emot måste e-postadressen vara ett [användar-ID i programmet](howto-administer.md), och användaren måste har loggat in i programmet minst en gång.

    ![Temperaturåtgärd](media/tutorial-configure-rules/temperatureaction.png)

3. Välj **Spara**. Regeln finns på sidan **Regler**.

## <a name="test-the-rule"></a>Testa regeln

Strax efter att du har sparat regeln blir den aktiv. När villkoren som definierats i regeln uppfylls skickar programmet ett meddelande till den e-postadress som du angav i åtgärden.

> [!NOTE]
> När testet är färdigt stänger du av regeln för att sluta få aviseringar i inkorgen.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
>
> * Skapa en telemetribaserad regel
> * Lägga till en åtgärd

Nu när du har definierat en tröskelvärdesbaserad regel är nästa steg att [anpassa operatörens vyer](tutorial-customize-operator.md).

Mer information om olika typer av regler i Azure IoT Central och hur du parameteriserar regeldefinitionen finns här:

* [Skapa en telemetriregel och konfigurera meddelanden](howto-create-telemetry-rules.md).
* [Skapa en händelseregel och konfigurera meddelanden](howto-create-event-rules.md).

<!-- Next tutorials in the sequence -->