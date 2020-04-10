---
title: Snabbstart – övervaka dina enheter i Azure IoT Central
description: Som operatör kan du lära dig hur du använder ditt Azure IoT Central-program för att övervaka dina enheter.
author: dominicbetts
ms.author: dobett
ms.date: 02/12/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 1dec52bbf1435cd7e363edf111f769d3e2cffb6a
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80998923"
---
# <a name="quickstart-use-azure-iot-central-to-monitor-your-devices"></a>Snabbstart: Använd Azure IoT Central för att övervaka dina enheter

*Den här artikeln gäller för operatörer, kompilerare och administratörer.*

Den här snabbstarten visar hur du som operatör använder ditt Microsoft Azure IoT Central-program för att övervaka dina enheter och ändra inställningar.

## <a name="prerequisites"></a>Krav

Innan du börjar bör du slutföra de tre tidigare snabbstarterna [Skapa ett Azure IoT Central-program](./quick-deploy-iot-central.md), [Lägg till en simulerad enhet i ditt IoT Central-program](./quick-create-simulated-device.md) och [Konfigurera regler och åtgärder för din enhet](quick-configure-rules.md).

## <a name="receive-a-notification"></a>Få ett meddelande

Azure IoT Central skickar meddelanden om enheter via e-post. Byggaren lade till en regel för att skicka ett meddelande när temperaturen i en ansluten enhetssensor överskred ett tröskelvärde. Kontrollera e-postmeddelanden som skickas till det konto som valdes ut av byggaren för att få meddelanden.

Öppna e-postmeddelandet som du fick i slutet av snabbstarten [Konfigurera regler och åtgärder för enheten.](quick-configure-rules.md) Välj länken till enheten i e-postmeddelandet:

![E-postavisering](media/quick-monitor-devices/email.png)

**Översiktsvyn** för den simulerade enhet som du skapade i föregående snabbstart öppnas i webbläsaren:

![Enheten som utlöste e-postmeddelandet](media/quick-monitor-devices/dashboard.png)

## <a name="investigate-an-issue"></a>Undersöka ett problem

Som operator kan du visa information om enheten i vyerna **Översikt**, **Om**och **Kommandon.** Byggaren skapade en **hantera enhetsvy** där du kan redigera enhetsinformation och ange enhetsegenskaper.

Diagrammet på instrumentpanelen visar en kurva över enhetens temperatur. Du bestämmer dig för att enhetens temperatur är för hög.

## <a name="remediate-an-issue"></a>Lösa ett problem

Om du vill göra en ändring på enheten använder du sidan **Hantera enhet.**

Byt **fläkthastighet** till 500 för att kyla enheten. Välj **Spara** för att uppdatera enheten. När enheten bekräftar att inställningarna ändras ändras egenskapens status till **synkroniserad:**

![Uppdatera inställningar](media/quick-monitor-devices/change-settings.png)

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig att:

* Få ett meddelande
* Undersöka ett problem
* Lösa ett problem

Nu när du vet nu att övervaka din enhet, är det föreslagna nästa steget att:

> [!div class="nextstepaction"]
> [Skapa och hantera en enhetsmall](howto-set-up-template.md).
