---
title: Övervaka dina enheter i Azure IoT Central | Microsoft Docs
description: Som operatör kan du övervaka dina enheter med hjälp av Azure IoT Central-programmet.
author: dominicbetts
ms.author: dobett
ms.date: 10/24/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: de83315f38766bc1e2f37ac82452bb023930f241
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2019
ms.locfileid: "73893776"
---
# <a name="quickstart-use-azure-iot-central-to-monitor-your-devices-preview-features"></a>Snabb start: Använd Azure IoT Central för att övervaka dina enheter (för hands versions funktioner)

*Den här artikeln gäller för operatörer, kompilerare och administratörer.*

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Den här snabb starten visar dig, som en operatör, hur du använder ditt Microsoft Azure IoT Central-program för att övervaka dina enheter och ändra inställningar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du börjar bör du slutföra de tre föregående snabb starterna [skapa ett Azure IoT Central-program](./quick-deploy-iot-central.md), [lägga till en simulerad enhet i IoT Central programmet](./quick-create-pnp-device.md) och [Konfigurera regler och åtgärder för enheten](quick-configure-rules.md).

## <a name="receive-a-notification"></a>Få ett meddelande

Azure IoT Central skickar meddelanden om enheter via e-post. Verktyget lade till en regel för att skicka ett meddelande när temperaturen i en ansluten miljö sensor enhet har överskridit ett tröskelvärde. Kontrollera e-postmeddelanden som skickas till det konto som valdes ut av byggaren för att få meddelanden.

Öppna e-postmeddelandet som du fick i slutet av [Konfigurera reglerna och åtgärderna för](quick-configure-rules.md) snabb starten av enheten. I e-postmeddelandet väljer du länken till enheten:

![E-postavisering](media/quick-monitor-devices/email.png)

Vyn **instrument panel** för den simulerade enheten som du skapade i föregående snabb starts fönster öppnas i webbläsaren:

![Enheten som utlöste e-postmeddelandet](media/quick-monitor-devices/dashboard.png)

## <a name="investigate-an-issue"></a>Undersöka ett problem

Som operatör kan du Visa information om enheten på sidorna **Översikt**, **Egenskaper för miljö sensor**och **kommandon** . Verktyget anpassar sidan Egenskaper för **instrument panel** och **miljö sensor** för att visa viktig information om en ansluten miljö sensor enhet.

Välj vyn **Översikt** för att se information om enheten.

Diagrammet på instrumentpanelen visar en kurva över enhetens temperatur. Du bestämmer att enhetens temperatur är för hög.

## <a name="remediate-an-issue"></a>Lösa ett problem

Om du vill göra en ändring av enheten använder du sidan **Egenskaper för miljö sensor** .

Välj **Egenskaper för miljö sensor**. Ändra **ljus styrke nivån** till 10. Välj **Spara** för att uppdatera enheten. När enheten bekräftar inställningarna ändras statusen för egenskapen till **synkroniserad**:

![Uppdatera inställningar](media/quick-monitor-devices/change-settings.png)

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig att:

* Få ett meddelande
* Undersöka ett problem
* Lösa ett problem

Nu när du vet att du nu kan övervaka enheten är det föreslagna nästa steg att:

> [!div class="nextstepaction"]
> [Bygg och hantera en enhets mall](howto-set-up-template.md).
