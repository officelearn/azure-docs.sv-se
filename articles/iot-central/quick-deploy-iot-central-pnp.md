---
title: Skapa ett Azure IoT Central-program | Microsoft Docs
description: Skapa ett nytt Azure IoT Central-program. Skapa en utvärderingsversion eller betala per användning-program med en mall för program.
author: viv-liu
ms.author: viviali
ms.date: 06/07/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 9d242c48068e96498a811f52dbc599abd32bc936
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/05/2019
ms.locfileid: "70383011"
---
# <a name="create-an-azure-iot-central-application-preview-features"></a>Skapa ett Azure IoT Central-program (för hands versions funktioner)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Den här snabb starten visar hur du skapar ett Azure IoT Central-program som använder för hands versions funktioner som IoT Plug and Play.

> [!WARNING]
> IoT Plug and Play-funktionerna i Azure IoT Central finns för närvarande i en offentlig för hands version. Använd inte ett IoT-Plug and Play aktiverat IoT Central program för produktions arbets belastningar. För produktions miljöer används ett IoT Central-program som skapats från en aktuell, allmänt tillgänglig program mal len.

## <a name="create-an-application"></a>Skapa ett program

Gå till webbplatsen för [Azure IoT Central Application Manager](https://aka.ms/iotcentral) . Logga sedan in med ett personligt Microsoft-konto eller arbets-eller skol konto.

Börja skapa ett nytt Azure IoT Central-program genom att välja **Nytt program**. Med den här länken går du till sidan **skapa ett program** .

![Sidan skapa en program-Azure IoT Central](media/quick-deploy-iot-central-pnp/iotcentralcreate.png)

Så här skapar du ett nytt Azure IoT Central-program som innehåller för hands versions funktioner som IoT Plug and Play:

1. Välj en betalningsplan:
   - **Utvärderings** program är kostnads fria i sju dagar innan de upphör att gälla. De kan konverteras till betala per användning när som helst innan de upphör att gälla. Om du skapar ett **utvärderings** program måste du ange din kontakt information och välja om du vill få information och tips från Microsoft.
   - **Betala** per användning-program debiteras per enhet och de första fem Enheterna är kostnads fria. Om du skapar ett program **enligt principen betala per** användning måste du välja din *katalog*, Azure- *prenumeration*och *region*:
      - *Directory* är Azure Active Directory (AD) för att skapa ditt program. Den innehåller användaridentiteter, autentiseringsuppgifter och övrig organisatorisk information. Om du inte har en Azure AD skapas en åt dig när du skapar en Azure-prenumeration.
      - Med en *Azure-prenumeration* kan du skapa instanser av Azure-tjänster. IoT Central etablerar resurser i din prenumeration. Om du inte har någon Azure-prenumeration kan du skapa en på [Azures registreringssida](https://aka.ms/createazuresubscription). När du har skapat Azure-prenumerationen kan du gå tillbaka till sidan **Skapa program**. Din nya prenumeration visas i listrutan **Azure-prenumeration**.
      - *Region* är den fysiska platsen där du vill skapa ditt program. Under den offentliga för hands versionen är de enda tillgängliga regionerna för ett för **hands versions program** **Nord Europa** och **centrala USA**.

      Läs mer om prissättning på [sidan med prisinformation för Azure IoT Central.](https://azure.microsoft.com/pricing/details/iot-central/)

1. Välj ett eget programnamn, exempelvis **Contoso IoT**. Du får ett unikt URL-prefix från Azure IoT Central. Du kan ändra URL-prefixet till något som är enklare att komma ihåg.

1. Välj program mal len för för **hands versionen** . En programmall kan innehålla fördefinierade objekt, till exempel mallar för enheten och instrumentpaneler som hjälper dig att komma igång.

1. Välj **Skapa** längst ned på sidan.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du ett IoT Central-program som använder för hands versions funktionerna. Det föreslagna nästa steg är att:

> [!div class="nextstepaction"]
> [Definiera en ny enhets typ i ditt Azure IoT Central-program](./tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
