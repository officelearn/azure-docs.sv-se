---
title: Snabb start – övervaka dina enheter i Azure IoT Central
description: Som operatör lär du dig att använda ditt Azure IoT Central-program för att övervaka dina enheter.
author: dominicbetts
ms.author: dobett
ms.date: 02/12/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: e56b733a567c706c2a15f2c30ed93a47c244db11
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2020
ms.locfileid: "77168699"
---
# <a name="quickstart-use-azure-iot-central-to-monitor-your-devices"></a>Snabb start: Använd Azure IoT Central för att övervaka dina enheter

*Den här artikeln gäller för operatörer, kompilerare och administratörer.*

Den här snabb starten visar dig, som en operatör, hur du använder ditt Microsoft Azure IoT Central-program för att övervaka dina enheter och ändra inställningar.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar bör du slutföra de tre föregående snabb starterna [skapa ett Azure IoT Central-program](./quick-deploy-iot-central.md), [lägga till en simulerad enhet i IoT Central programmet](./quick-create-pnp-device.md) och [Konfigurera regler och åtgärder för enheten](quick-configure-rules.md).

## <a name="receive-a-notification"></a>Få ett meddelande

Azure IoT Central skickar meddelanden om enheter via e-post. Verktyget lade till en regel för att skicka ett meddelande när temperaturen i en ansluten enhets sensor har överskridit ett tröskelvärde. Kontrollera e-postmeddelanden som skickas till det konto som valdes ut av byggaren för att få meddelanden.

Öppna e-postmeddelandet som du fick i slutet av [Konfigurera reglerna och åtgärderna för](quick-configure-rules.md) snabb starten av enheten. I e-postmeddelandet väljer du länken till enheten:

![E-postavisering](media/quick-monitor-devices/email.png)

**Översikts** visningen för den simulerade enheten som du skapade i föregående snabb starter öppnas i webbläsaren:

![Enheten som utlöste e-postmeddelandet](media/quick-monitor-devices/dashboard.png)

## <a name="investigate-an-issue"></a>Undersöka ett problem

Som operatör kan du Visa information om enheten i vyerna **Översikt**, **om**och **kommandon** . Verktyget skapade en **hanterad enhets** vy där du kan redigera enhets information och ange enhets egenskaper.

Diagrammet på instrumentpanelen visar en kurva över enhetens temperatur. Du bestämmer att enhetens temperatur är för hög.

## <a name="remediate-an-issue"></a>Lösa ett problem

Använd sidan **Hantera enhet** om du vill göra en ändring i enheten.

Ändra **fläkt hastigheten** till 500 för att kyla enheten. Välj **Spara** för att uppdatera enheten. När enheten bekräftar inställningarna ändras statusen för egenskapen till **synkroniserad**:

![Uppdatera inställningar](media/quick-monitor-devices/change-settings.png)

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig att:

* Få ett meddelande
* Undersöka ett problem
* Lösa ett problem

Nu när du vet att du nu kan övervaka enheten är det föreslagna nästa steg att:

> [!div class="nextstepaction"]
> [Bygg och hantera en enhets mall](howto-set-up-template.md).
