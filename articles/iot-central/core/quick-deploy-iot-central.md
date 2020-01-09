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
ms.openlocfilehash: cb9968d3bcc30fe8e0f0023bcf7101cde5e4a196
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453901"
---
# <a name="create-an-azure-iot-central-application"></a>Skapa ett Azure IoT Central-program

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Som _byggare_ använder du Azure IoT Central-användargränssnittet till att definiera ditt Microsoft Azure IoT Central-program. Den här snabb starten visar hur du skapar ett Azure IoT Central-program som innehåller en exempel _enhets mall_. Det program som du skapar använder inte några för hands versions funktioner.

## <a name="create-an-application"></a>Skapa ett program

Gå till webbplatsen för [Azure IoT Central build](https://aka.ms/iotcentral) . Logga sedan in med ett Microsoft personal-, arbets-eller skol konto.

Välj **skapa**om du vill börja skapa ett Azure IoT Central-program utan att Visa aktiverade funktioner. Med den här länken går du till sidan för att **bygga IoT-program** .

![Sidan Azure IoT Central build](media/quick-deploy-iot-central/iotcentralcreate.png)

Välj sedan **anpassad app**.

Så här skapar du ett nytt Azure IoT Central-program:

1. Azure IoT Central föreslår automatiskt ett program namn baserat på den program mall som du har valt. Du kan acceptera namnet eller ange ett eget vänligt program namn, till exempel **contoso IoT**. Azure IoT Central genererar också en unik URL åt dig, baserat på programmets namn. Du är kostnads fri att ändra detta URL-prefix till något mer minnes värt om du vill.

1. Välj den **äldre program** mal len som inte använder för hands versions funktioner.

    | Programmall | Beskrivning |
    | -------------------- | ----------- |
    | Äldre program   | Skapar ett tomt program som du kan fylla med dina egna enhetsmallar och enheter. |

1. Välj en betalningsplan:
   - **7 dagars kostnads fria utvärderings versioner** är kostnads fria i sju dagar innan de upphör att gälla. De kan konverteras till " **betala per** användning" när som helst innan de upphör att gälla. Om du skapar ett **utvärderings** program måste du ange din kontakt information och välja om du vill få information och tips från Microsoft.
   - **Betala** per användning-program debiteras per enhet och de första fem Enheterna är kostnads fria. Om du skapar ett program **enligt principen betala per** användning måste du välja *katalog*, *Azure-prenumeration*och *plats*:
        - *Directory* är Azure Active Directory (AD) för att skapa ditt program. Den innehåller användaridentiteter, autentiseringsuppgifter och övrig organisatorisk information. Om du inte har en Azure AD skapas en åt dig när du skapar en Azure-prenumeration.
        - Med en *Azure-prenumeration* kan du skapa instanser av Azure-tjänster. IoT Central etablerar resurser i din prenumeration. Om du inte har någon Azure-prenumeration kan du skapa en på [Azures registreringssida](https://aka.ms/createazuresubscription). När du har skapat Azure-prenumerationen går du tillbaka till sidan **skapa ett program** . Din nya prenumeration visas i listrutan **Azure-prenumeration**.
        - *Plats* är den [geografi](https://azure.microsoft.com/global-infrastructure/geographies/) där du vill skapa ditt program. Normalt bör du välja den plats som är fysiskt närmast dina enheter för att få optimala prestanda. Azure IoT Central är för närvarande tillgängligt i **USA**, **Australien**, **Asien och Stillahavsområdet**eller i **Europa**.  När du har valt en plats kan du inte flytta programmet till en annan plats senare.

        Läs mer om prissättning på [sidan med prisinformation för Azure IoT Central.](https://azure.microsoft.com/pricing/details/iot-central/)

1. Fyll i ytterligare information som krävs för den betalnings plan som du valde tidigare i steg 1.

1. Välj **Skapa** längst ned på sidan.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har skapat du ett IoT Central-program. Här är nästa föreslagna steg:

> [!div class="nextstepaction"]
> [Definiera en ny enhets typ i ditt Azure IoT Central-program](./tutorial-define-device-type.md)
