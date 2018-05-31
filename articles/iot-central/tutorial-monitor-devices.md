---
title: Övervaka dina enheter i Azure IoT Central | Microsoft Docs
description: Som operatör kan du övervaka dina enheter med hjälp av Azure IoT Central-programmet.
services: iot-central
author: tanmaybhagwat
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: tutorial
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: a07c9e3c28fadaead8bfaaebe4d1ee06ac66a99e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34201395"
---
# <a name="use-azure-iot-central-to-monitor-your-devices"></a>Använda Azure IoT Central för att övervaka dina enheter

I den här självstudien lär du dig att som operatör använda Microsoft Azure IoT Central-programmet för att övervaka enheter och ändra inställningar.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Få ett meddelande
> * Undersöka ett problem
> * Lösa ett problem

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du börjar bör byggaren skapa Azure IoT Central-programmet genom att slutföra de tre självstudierna för byggare:

* [Definiera en ny enhetstyp](tutorial-define-device-type.md)
* [Konfigurera regler och åtgärder för din enhet](tutorial-configure-rules.md)
* [Anpassa operatörsvyer](tutorial-customize-operator.md)

## <a name="receive-a-notification"></a>Få ett meddelande

Azure IoT Central skickar meddelanden om enheter via e-post. Byggaren lade till en regel för att skicka ett meddelande när temperaturen i en ansluten luftkonditioneringsenhet överskrider ett tröskelvärde. Kontrollera e-postmeddelanden som skickas till det konto som valdes ut av byggaren för att få meddelanden.

Öppna det e-postmeddelande som du fick i slutet av självstudien om att [konfigurera regler och åtgärder för din enhet](tutorial-configure-rules.md). I e-postmeddelandet väljer du **Klicka här för att öppna enheten**:

![Regler för Application Builder (Programverktyg)](media/tutorial-monitor-devices/email.png)

Sidan **Enhet** för den simulerade enheten **Ansluten luftkonditioneringsenhet-1** som du skapade i den föregående självstudien öppnas i webbläsaren:

![Enheten som utlöste e-postmeddelandet](media/tutorial-monitor-devices/sourcedevice.png)

## <a name="investigate-an-issue"></a>Undersöka ett problem

Som operatör kan du visa information om enheten på sidorna **Mått**, **Inställningar**, **Egenskaper**, **Regler** och **Instrumentpanelen**. Byggaren anpassade **Instrumentpanelen** till att visa viktig information om en ansluten luftkonditioneringsenhet.

* Välj **Instrumentpanelen** om du vill se information om enheten:

    ![Instrumentpanel för enhet](media/tutorial-monitor-devices/initial_screen.png)

    Diagrammet på instrumentpanelen visar en kurva över enhetens temperatur. Du kan även se den aktuella måltemperaturen för enheten i panelen **Ange måltemperatur**. Du anser att måltemperaturen är för hög.

## <a name="remediate-an-issue"></a>Lösa ett problem

För att ändra enhetens måltemperatur använder du sidan **Inställningar**:

1. Välj **Inställningar**. Ändra **Ange temperatur** till 100. Välj **Uppdatera** för att skicka den nya måltemperaturen till enheten. När enheten har bekräftat de ändrade inställningarna ändras status för inställningens värde till **synkroniserad**:

    ![Uppdatera inställningar](media/tutorial-monitor-devices/change_settings.png)

1. Välj **Instrumentpanelen** och bekräfta inställningens nya värde:

    ![Uppdaterad enhetsinstrumentpanel](media/tutorial-monitor-devices/new_settings.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="nextstepaction"]
> * Få ett meddelande
> * Undersöka ett problem
> * Lösa ett problem

Nu när du har övervakat enheten föreslår vi att ditt nästa steg blir att [Lägga till en enhet](tutorial-add-device.md).
