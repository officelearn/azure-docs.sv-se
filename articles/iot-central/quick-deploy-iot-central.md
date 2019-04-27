---
title: Skapa ett Azure IoT Central-program | Microsoft Docs
description: Skapa ett nytt Azure IoT Central-program. Skapa en utvärderingsversion eller betala per användning-program med en mall för program.
author: viv-liu
ms.author: viviali
ms.date: 02/20/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 5df798f0f5f9588b1f2cd792e99d6409e565337e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60746406"
---
# <a name="create-an-azure-iot-central-application"></a>Skapa ett Azure IoT Central-program

Som _byggare_ använder du Azure IoT Central-användargränssnittet till att definiera ditt Microsoft Azure IoT Central-program. Den här snabbstarten visar hur du skapar ett Azure IoT Central-program som innehåller ett exempel på en _enhetsmall_ och simulerade _enheter_.

Gå till sidan [Application Manager](https://aka.ms/iotcentral) (Programhanterare) i Azure IoT Central. Du måste logga in med ett arbets- eller skolkonto för Microsoft.

Börja skapa ett nytt Azure IoT Central-program genom att välja **Nytt program**. Då kommer du till sidan **Skapa program**.

![Sidan Skapa program i Azure IoT Central](media/quick-deploy-iot-central/iotcentralcreate.png)

Skapa ett nytt Azure IoT Central-program:

1. Välj en betalningsplan:
   - **Utvärderingsversioner** är kostnadsfria i 7 dagar innan de upphör att gälla. De kan konverteras till betala per användning när som helst innan de upphör att gälla.
   - **Betala per användning**-program debiteras per enhet, och de fem första enheterna är kostnadsfria.

     Läs mer om prissättning på [sidan med prisinformation för Azure IoT Central.](https://azure.microsoft.com/pricing/details/iot-central/)

1. Välj ett eget programnamn, exempelvis **Contoso IoT**. Du får ett unikt URL-prefix från Azure IoT Central. Du kan ändra URL-prefixet till något som är enklare att komma ihåg.

1. Välj en programmall. En programmall kan innehålla fördefinierade objekt, till exempel mallar för enheten och instrumentpaneler som hjälper dig att komma igång.

    | Programmall | Beskrivning |
    | -------------------- | ----------- |
    | Exemplet Contoso       | Skapar ett program som innehåller en mall för enheter som redan har skapats för en kylande varuautomat. Använd den här mallen för att börja utforska Azure IoT Central. |
    | Exemplet Devkits       | Skapar ett program med enhetsmallar där du kan ansluta en MXChip- eller Raspberry Pi-enhet. Använd den här mallen om du är en enhetsutvecklare som experimenterar med någon av dessa enheter. |
    | Anpassat program   | Skapar ett tomt program som du kan fylla med dina egna enhetsmallar och enheter. |

1. Om du skapar ett program efter principen **betala per användning** måste du välja din *katalog*, *Azure-prenumeration* och *region*. 

   - *Directory* är den Azure Active Directory (AD) att skapa ditt program. Den innehåller användaridentiteter, autentiseringsuppgifter och övrig organisatorisk information. Om du inte har en Azure AD, skapas en åt dig när du skapar en Azure-prenumeration.

   - Med en *Azure-prenumeration* kan du skapa instanser av Azure-tjänster. IoT Central etablerar resurser i din prenumeration. Om du inte har någon Azure-prenumeration kan du skapa en på [Azures registreringssida](https://aka.ms/createazuresubscription). När du har skapat Azure-prenumerationen kan du gå tillbaka till sidan **Skapa program**. Din nya prenumeration visas i listrutan **Azure-prenumeration**.

   - *Region* är den fysiska platsen där du vill skapa ditt program. Normalt bör du välja den region som är fysiskt närmast dina enheter för att få bästa möjliga prestanda. Du kan se regionerna där Azure IoT Central är tillgängligt på sidan [Produkttillgänglighet per region](https://azure.microsoft.com/regions/services/).

     > [!Note]
     > När du väljer en region kan du inte flytta ditt program till en annan region senare.

1. Välj **Skapa**.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har skapat du ett IoT Central-program. Här är nästa föreslagna steg:

> [!div class="nextstepaction"]
> [Ta en rundtur om IoT Central](overview-iot-central-tour.md)
