---
title: Skapa ett Azure IoT Central-program | Microsoft Docs
description: Skapa ett nytt Azure IoT Central-program. Skapa en utvärderingsversion eller betala per användning-program med en mall för program.
author: viv-liu
ms.author: viviali
ms.date: 08/02/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: corywink
ms.openlocfilehash: c83e64b0b49ba853765de22e5a7df5a0d06d1686
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2019
ms.locfileid: "71719139"
---
# <a name="create-an-azure-iot-central-application"></a>Skapa ett Azure IoT Central-program

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

Som _byggare_ använder du Azure IoT Central-användargränssnittet till att definiera ditt Microsoft Azure IoT Central-program. Den här snabb starten visar hur du skapar ett Azure IoT Central-program som innehåller en exempel _enhets mall_.

## <a name="create-an-application"></a>Skapa ett program

Gå till webbplatsen för [Azure IoT Central Application Manager](https://aka.ms/iotcentral) . Logga sedan in med ett Microsoft personal-, arbets-eller skol konto.

Börja skapa ett nytt Azure IoT Central-program genom att välja **Nytt program**. Med den här länken går du till sidan **skapa ett program** .

![Sidan skapa en program-Azure IoT Central](media/quick-deploy-iot-central/iotcentralcreate.png)

Skapa ett nytt Azure IoT Central-program:

1. Välj en betalningsplan:
   - **Utvärderings** program är kostnads fria i sju dagar innan de upphör att gälla. De kan konverteras till " **betala per** användning" när som helst innan de upphör att gälla. Om du skapar ett **utvärderings** program måste du ange din kontakt information och välja om du vill få information och tips från Microsoft.
   - **Betala** per användning-program debiteras per enhet och de första fem Enheterna är kostnads fria. Om du skapar ett program **enligt principen betala per** användning måste du välja din *katalog*, *Azure-prenumeration*och *region*:
        - *Directory* är Azure Active Directory (AD) för att skapa ditt program. Den innehåller användaridentiteter, autentiseringsuppgifter och övrig organisatorisk information. Om du inte har en Azure AD skapas en åt dig när du skapar en Azure-prenumeration.
        - Med en *Azure-prenumeration* kan du skapa instanser av Azure-tjänster. IoT Central etablerar resurser i din prenumeration. Om du inte har någon Azure-prenumeration kan du skapa en på [Azures registreringssida](https://aka.ms/createazuresubscription). När du har skapat Azure-prenumerationen går du tillbaka till sidan **skapa ett program** . Din nya prenumeration visas i listrutan **Azure-prenumeration**.
        - *Region* är den fysiska plats eller det [geografiskt](https://azure.microsoft.com/global-infrastructure/geographies/) område där du vill skapa ditt program. Normalt bör du välja den region som är fysiskt närmast dina enheter för att få optimala prestanda. Du kan se de regioner där Azure IoT Central är tillgängligt på sidan [produkter som är tillgängliga efter region](https://azure.microsoft.com/global-infrastructure/services/?products=iot-central) . När du har valt en region kan du inte flytta programmet till en annan region senare.

        Läs mer om prissättning på [sidan med prisinformation för Azure IoT Central.](https://azure.microsoft.com/pricing/details/iot-central/)

1. Välj en program mal len. En programmall kan innehålla fördefinierade objekt, till exempel mallar för enheten och instrumentpaneler som hjälper dig att komma igång.

    | Programmall | Beskrivning |
    | -------------------- | ----------- |
    | Contoso-exempel       | Skapar ett program som innehåller en mall för enheter som redan har skapats för en kylande varuautomat. Använd den här mallen för att börja utforska Azure IoT Central. |
    | Exemplet Devkits       | Skapar ett program med enhetsmallar där du kan ansluta en MXChip- eller Raspberry Pi-enhet. Använd den här mallen om du är en enhetsutvecklare som experimenterar med någon av dessa enheter. |
    | Anpassat program   | Skapar ett tomt program som du kan fylla med dina egna enhetsmallar och enheter. |

1. Azure IoT Central föreslår automatiskt ett program namn baserat på den program mall som du har valt. Du kan acceptera namnet eller ange ett eget vänligt program namn, till exempel **contoso IoT**. Azure IoT Central genererar också ett unikt URL-prefix åt dig, baserat på programmets namn. Du är kostnads fri att ändra detta URL-prefix till något mer minnes värt om du vill.

1. Fyll i ytterligare information som krävs för den betalnings plan som du valde tidigare i steg 1.

1. Välj **Skapa** längst ned på sidan.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har skapat du ett IoT Central-program. Här är nästa föreslagna steg:

> [!div class="nextstepaction"]
> [Definiera en ny enhets typ i ditt Azure IoT Central-program](./tutorial-define-device-type.md)
