---
title: Konfigurera regler och åtgärder i Azure IoT Central | Microsoft Docs
description: Den här självstudien visar hur du som byggare konfigurerar telemetribaserade regler och åtgärder i Azure IoT Central-programmet.
author: ankitscribbles
ms.author: ankitgup
ms.date: 10/12/2018
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: d7269f61579ce1ffd9a686634effd153837a2f25
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2019
ms.locfileid: "55663000"
---
# <a name="tutorial-configure-rules-and-actions-for-your-device-in-azure-iot-central"></a>Självstudie: Konfigurera regler och åtgärder för enheten i Azure IoT Central

*Den här artikeln gäller för operatörer, kompilerare och administratörer.*

I den här självstudien skapar du en regel som skickar ett e-postmeddelande när temperaturen i en ansluten luftkonditioneringsenhet överskrider 90&deg; F.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en telemetribaserad regel
> * Lägga till en åtgärd

[!INCLUDE [iot-central-experimental-note](../../includes/iot-central-experimental-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du börjar bör du slutföra självstudien om att [definiera en ny enhetstyp i programmet](tutorial-define-device-type.md) för att skapa enhetsmallen **Ansluten luftkonditioneringsenhet** som du kommer att arbeta med.

## <a name="create-a-telemetry-based-rule"></a>Skapa en telemetribaserad regel

1. Om du vill lägga till en ny telemetribaserad regel i programmet går du till den vänstra navigeringsmenyn och väljer **Device Explorer**:

    ![Sidan Device Explorer](media/tutorial-configure-rules/explorerpage1.png)

    Du ser enhetsmallen **Ansluten luftkonditioneringsenhet (1.0.0)** och enheten **Ansluten luftkonditioneringsenhet-1** som du skapade i den föregående självstudien.

2. För att börja anpassa den anslutna luftkonditioneringsenheten väljer du den enhet som du skapade i föregående självstudien:

    ![Sidan Ansluten luftkonditioneringsenhet](media/tutorial-configure-rules/builderdevicelist1.png)

3. För att börja lägga till en regel i vyn **Regler** väljer du **Regler** och klickar sedan på **Redigera mall**:

    ![Vyn Regler](media/tutorial-configure-rules/builderedittemplate.png)

4. För att skapa en regel för tröskelbaserad telemetri väljer du **Ny regel** och sedan **Telemetri**.

    ![Redigera mall](media/tutorial-configure-rules/buildernewrule.png)

5. Använd informationen i följande tabell för att definiera regeln:

    | Inställning                                      | Värde                             |
    | -------------------------------------------- | ------------------------------    |
    | Namn                                         | Temperaturmeddelande för luftkonditionering |
    | Aktivera regeln för alla enheter med den här mallen | På                                |
    | Aktivera regeln på den här enheten                   | På                                |
    | Tillstånd                                    | Temperaturen är högre än 90    |
    | Sammansättning                                  | Ingen                              |

    ![Regelvillkor för temperatur](media/tutorial-configure-rules/buildertemperaturerule1.png)

## <a name="add-an-action"></a>Lägga till en åtgärd

När du definierar en regel kan du även definiera en åtgärd som ska köras när regelvillkoren uppfylls. I den här självstudien lägger du till en åtgärd som skickar ett e-postmeddelande när regeln utlöses.

1. För att lägga till en **Åtgärd** **sparar** du regeln och rullar sedan ned på panelen **Konfigurera telemetriregel** och väljer **+** bredvid **Åtgärder**. Välj sedan **E-post**:

    ![Regelåtgärd för temperatur](media/tutorial-configure-rules/builderaddaction1.png)

2. Använd informationen i följande tabell för att definiera åtgärden:

    | Inställning   | Värde                          |
    | --------- | ------------------------------ |
    | Till        | Din e-postadress             |
    | Anteckningar     | Temperaturen i luftkonditioneringsenheten har överskridit tröskelvärdet. |

    > [!NOTE]
    > För att ett e-postmeddelande ska kunna tas emot måste e-postadressen vara ett [användar-ID i programmet](howto-administer.md), och användaren måste har loggat in i programmet minst en gång.

    ![Temperaturåtgärd för Application Builder (Programverktyg)](media/tutorial-configure-rules/buildertemperatureaction.png)

3. Välj **Spara**. Regeln finns på sidan **Regler**:

    ![Regler för Application Builder (Programverktyg)](media/tutorial-configure-rules/builderrules1.png)

4. Välj **Klar** för att avsluta läget **Redigera mall**.
 

## <a name="test-the-rule"></a>Testa regeln

Strax efter att du har sparat regeln blir den aktiv. När villkoren som definierats i regeln uppfylls skickar programmet ett meddelande till den e-postadress som du angav i åtgärden.

![E-poståtgärd](media/tutorial-configure-rules/email.png)

> [!NOTE]
> När testet är klart stänger du av regeln för att sluta få aviseringar i inkorgen. 

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
> * Skapa en telemetribaserad regel
> * Lägga till en åtgärd

Nu när du har definierat en tröskelvärdesbaserad regel föreslår vi att du går vidare till steget [anpassa operatörens vyer](tutorial-customize-operator.md).

Mer information om olika typer av regler i Azure IoT Central och hur du parameteriserar regeldefinitionen finns här:
* [Skapa en telemetriregel och konfigurera meddelanden](howto-create-telemetry-rules.md).
* [Skapa en händelseregel och konfigurera meddelanden](howto-create-event-rules.md).

<!-- Next tutorials in the sequence -->
