---
title: Övervaka dina enheter i Azure IoT Central | Microsoft Docs
description: Som operatör kan du övervaka dina enheter med hjälp av Azure IoT Central-programmet.
author: dominicbetts
ms.author: dobett
ms.date: 08/06/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: cf50fd80bdbce5895bd1da39700d1c6e4cdcc230
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878119"
---
# <a name="tutorial-use-azure-iot-central-to-monitor-your-devices-preview-features"></a>Självstudier: Använd Azure IoT Central för att övervaka dina enheter (för hands versions funktioner)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

I den här självstudien lär du dig att som operatör använda Microsoft Azure IoT Central-programmet för att övervaka enheter och ändra inställningar.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Få ett meddelande
> * Undersöka ett problem
> * Lösa ett problem

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar bör byggaren skapa Azure IoT Central-programmet genom att slutföra de tre självstudierna för byggare:

* [Definiera en ny enhetstyp](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
* [Lägg till en enhet](tutorial-add-device-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
* [Konfigurera regler och åtgärder för din enhet](tutorial-configure-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)

## <a name="receive-a-notification"></a>Få ett meddelande

Azure IoT Central skickar meddelanden om enheter via e-post. Verktyget lade till en regel för att skicka ett meddelande när temperaturen i en ansluten miljö sensor enhet har överskridit ett tröskelvärde. Kontrollera e-postmeddelanden som skickas till det konto som valdes ut av byggaren för att få meddelanden.

Öppna det e-postmeddelande som du fick i slutet av självstudien om att [konfigurera regler och åtgärder för din enhet](tutorial-configure-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json). I e-postmeddelandet väljer du länken till enheten:

![E-postavisering](media/tutorial-monitor-devices-pnp/email.png)

Vyn **instrument panel** för den simulerade enheten som du skapade i de föregående självstudierna öppnas i webbläsaren:

![Enheten som utlöste e-postmeddelandet](media/tutorial-monitor-devices-pnp/dashboard.png)

## <a name="investigate-an-issue"></a>Undersöka ett problem

Som operatör kan du Visa information om enheten på **instrument panelen**, **Egenskaper för miljö sensor**och kommandon på sidan **kommandon** . Verktyget anpassar sidan Egenskaper för **instrument panel** och **miljö sensor** för att visa viktig information om en ansluten miljö sensor enhet.

Välj **Instrumentpanelen** om du vill se information om enheten.

Diagrammet på instrumentpanelen visar en kurva över enhetens temperatur. Du kan också se aktuell mål temperatur för enheten i **enhetens egenskaps** panel. Du anser att måltemperaturen är för hög.

## <a name="remediate-an-issue"></a>Lösa ett problem

Om du vill göra en ändring av enheten använder du sidan **Egenskaper för miljö sensor** :

1. Välj **Egenskaper för miljö sensor**. Ändra **ljus styrke nivån** till 10. Välj **Spara** för att uppdatera enheten. När enheten bekräftar inställningarna ändras statusen för egenskapen till **synkroniserad**:

    ![Uppdatera inställningar](media/tutorial-monitor-devices-pnp/change-settings.png)

2. Välj **instrument panel** och verifiera det nya egenskap svärdet:

    ![Uppdaterad enhetsinstrumentpanel](media/tutorial-monitor-devices-pnp/new-settings.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

* Få ett meddelande
* Undersöka ett problem
* Lösa ett problem

Nu när du vet att du nu kan övervaka enheten är det föreslagna nästa steg att:

> [!div class="nextstepaction"]
> [Konfigurera regler och åtgärder för din enhet](tutorial-configure-rules.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).