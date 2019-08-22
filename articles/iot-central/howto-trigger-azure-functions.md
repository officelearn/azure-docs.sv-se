---
title: Utlös Azure Functions att använda Webhooks i Azure IoT Central
description: Skapa en Function-app som körs varje gången en regel utlöses i Azure IoT Central.
author: viv-liu
ms.author: viviali
ms.date: 07/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: e7c0f0abdf4a96f4af904f76549bdebd62b803cd
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877326"
---
# <a name="trigger-azure-functions-using-webhooks-in-azure-iot-central"></a>Utlös Azure Functions att använda Webhooks i Azure IoT Central

*Det här avsnittet gäller för byggare och administratörer.*

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

Använd Azure Functions för att köra Server lös kod på webhook-utdata från IoT Centrals regler. Du behöver inte etablera en virtuell dator eller publicera en webbapp för att använda Azure Functions, men i stället kan du köra den här koden utan server. Använd Azure Functions för att transformera webhook-nyttolasten innan du skickar den till det slutliga målet, till exempel en SQL-databas eller Event Grid.

## <a name="prerequisites"></a>Förutsättningar

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="how-to-connect-azure-functions"></a>Så här ansluter du Azure Functions

1. [Skapa en ny function-app i Azure Portal](https://ms.portal.azure.com/#create/Microsoft.FunctionApp).

    ![Skapa en ny function-app i Azure Portal](media/howto-trigger-azure-functions/createfunction.png)

2. Expandera din Function-app och välj **+-knappen** bredvid functions. Om den här funktionen är den första i din Function-app väljer du **i portalen** som utvecklings miljö och väljer **Fortsätt**.

    ![Välj anpassad funktion i Function-appen](media/howto-trigger-azure-functions/customfunction.png)

3. Välj **webhook + API** -mall och välj **skapa**. I det här avsnittet används .NET-baserad Azure-funktion.

    ![Välj allmän webhook-utlösare](media/howto-trigger-azure-functions/genericwebhooktrigger.png)

4. I den nya funktionen väljer du **</> Hämta funktions webb adress**och kopierar och sparar sedan värdet. Du kan använda det här värdet för att konfigurera webhooken.

    ![Hämta funktionens URL](media/howto-trigger-azure-functions/getfunctionurl.png)

4. I IoT Central navigerar du till den regel som du vill ansluta till din Function-app.

5. Lägg till en webhook-åtgärd. Ange ett **visnings namn** och klistra in den funktions-URL som du kopierade tidigare i återanrops- **URL**.

    ![Ange funktions webb adress i fältet återanrops-URL](media/howto-trigger-azure-functions/configurewebhook.PNG)

6. Spara regeln. Nu när regeln utlöses anropar webhooken Function-appen för körning. I din Function-app kan du välja **övervakare** för att se funktionens anrops historik. Du kan använda App Insights eller den klassiska vyn för att titta på historiken.

    ![Övervaka anrops historiken för funktionen](media/howto-trigger-azure-functions/monitorfunction.PNG)

Mer information finns i Azure Functions artikel om hur du [skapar en funktion som utlöses av en allmän webhook](https://docs.microsoft.com/azure/azure-functions/functions-create-generic-webhook-triggered-function).

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig hur du konfigurerar och använder Webhooks är det föreslagna nästa steg att utforska [Bygg arbets flöden i Microsoft Flow](howto-add-microsoft-flow.md).
