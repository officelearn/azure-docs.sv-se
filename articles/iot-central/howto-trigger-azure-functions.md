---
title: Utlösa Azure Functions med webhookar i Azure IoT Central
description: Skapa en funktionsapp som körs varje gång en regel utlöses i Azure IoT Central.
author: viv-liu
ms.author: viviali
ms.date: 02/20/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: c80b007c3c9c1a35540e690554603a5ae8f16d62
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58284637"
---
# <a name="trigger-azure-functions-using-webhooks-in-azure-iot-central"></a>Utlösa Azure Functions med webhookar i Azure IoT Central

*Det här avsnittet gäller builders och administratörer.*

Använd Azure Functions för att köra serverlös kod för webhook-utdata från IoT Central regler. Du behöver inte etablera en virtuell dator eller publicera en webbapp för att använda Azure Functions, men i stället kan du köra den här koden utan server. Använd Azure Functions för att omvandla webhook-nyttolasten innan de skickas till slutmålet, till exempel en SQL-databas eller en Event Grid.

## <a name="prerequisites"></a>Förutsättningar

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="how-to-connect-azure-functions"></a>Så här ansluter du Azure Functions

1. [Skapa en ny funktionsapp i Azure-portalen](https://ms.portal.azure.com/#create/Microsoft.FunctionApp).

    ![Skapa en ny funktionsapp i Azure portal](media/howto-trigger-azure-functions/createfunction.png)

2. Expandera funktionsappen och välj den **+ knappen** bredvid funktioner. Om den här funktionen är den första funktionen i din funktionsapp väljer **i portalen** som utvecklingsmiljö och välj **Fortsätt**.

    ![Välj anpassad funktion i funktionsappen](media/howto-trigger-azure-functions/customfunction.png)

3. Välj **Webhook + API** mallen och välj **skapa**. Det här avsnittet använder .NET-baserade Azure-funktion.

    ![Välj allmän webhook-utlösare](media/howto-trigger-azure-functions/genericwebhooktrigger.png)

4. I den nya funktionen väljer **<> / hämta Funktionswebbadress**och kopiera och spara värdet. Du kan använda det här värdet för att konfigurera webhooken.

    ![Hämta URL: en för funktionen](media/howto-trigger-azure-functions/getfunctionurl.png)

4. Navigera till den regel som du vill ansluta till din funktionsapp i IoT Central.

5. Lägg till en webhook-åtgärd. Ange en **visningsnamn** och klistra in funktionens URL som du kopierade tidigare i **Motringnings-URL för**.

    ![Ange funktions-URL i fältet Motringnings-URL](media/howto-trigger-azure-functions/configurewebhook.PNG)

6. Spara regeln. Nu anropar webhooken funktionsappen ska köras när regeln utlöses. Du kan välja i din funktionsapp **övervakaren** till funktionens anrop historik. Du kan använda App Insights eller klassiskt läge för att titta på historiken.

    ![Övervaka anrop historiken för funktionen](media/howto-trigger-azure-functions/monitorfunction.PNG)

Mer information finns i Azure Functions-artikeln om [skapar en funktion som utlöses av en allmän webhook](https://docs.microsoft.com/azure/azure-functions/functions-create-generic-webhook-triggered-function).

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig hur du ställer in och använda webhooks, föreslagna nästa steg är att utforska [att skapa arbetsflöden i Microsoft Flow](howto-add-microsoft-flow.md).
