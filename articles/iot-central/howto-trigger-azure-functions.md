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
ms.openlocfilehash: 8d638743b3cb07b0d060e754ecb76e74bd02719d
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/01/2019
ms.locfileid: "57215021"
---
# <a name="trigger-azure-functions-using-webhooks-in-azure-iot-central"></a>Utlösa Azure Functions med webhookar i Azure IoT Central

*Det här avsnittet gäller builders och administratörer.*

Använd Azure Functions för att köra serverlös kod för webhook-utdata från IoT Central regler. Du behöver inte etablera en virtuell dator eller publicera en webbapp för att använda Azure Functions, men i stället kan du köra den här koden serverlessly. Använd Azure Functions för att omvandla webhook-nyttolasten innan de skickas till slutmålet, till exempel en SQL-databas eller en Event Grid.

## <a name="prerequisites"></a>Förutsättningar

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="how-to-connect-azure-functions"></a>Så här ansluter du Azure Functions

1. [Skapa en ny funktionsapp i Azure Portal](https://ms.portal.azure.com/#create/Microsoft.FunctionApp).

    ![Skapa en ny funktionsapp i Azure Portal](media/howto-trigger-azure-functions/createfunction.png)

2. Expandera funktionsappen och klicka på den **+ knappen** bredvid funktioner. Om den här funktionen är den första funktionen i din funktionsapp väljer du **Anpassad funktion**. Detta visar en fullständig uppsättning med funktionsmallar.

    ![Välj anpassad funktion i funktionsappen](media/howto-trigger-azure-functions/customfunction.png)

3. I sökfältet skriver **”generic”** och välj sedan önskat språk för utlösarmallen för allmän webhook. I det här avsnittet används en C#-funktion. 

    ![Välj allmän webhook-utlösare](media/howto-trigger-azure-functions/genericwebhooktrigger.png)

4. Klicka på **</> Hämta funktionswebbadress** och kopiera och spara värdet. Du kan använda det här värdet för att konfigurera webhooken.

    ![Hämta URL: en för funktionen](media/howto-trigger-azure-functions/getfunctionurl.png)

4. Navigera till den regel som du vill ansluta till din funktionsapp i IoT Central.

5. Lägg till en webhook-åtgärd. Ange en **visningsnamn** och klistra in funktionens URL som du kopierade tidigare i **Motringnings-URL för**.

    ![Ange funktions-URL i fältet Motringnings-URL](media/howto-trigger-azure-functions/configurewebhook.PNG)

6. Spara regeln. Nu anropar webhooken funktionsappen ska köras när regeln utlöses. I din funktionsapp, kan du klicka på **övervakaren** till funktionens anrop historik. Du kan använda App Insights eller klassiskt läge för att titta på historiken.

    ![Övervaka anrop historiken för funktionen](media/howto-trigger-azure-functions/monitorfunction.PNG)

Mer information finns i Azure Functions-artikeln om [skapar en funktion som utlöses av en allmän webhook](https://docs.microsoft.com/azure/azure-functions/functions-create-generic-webhook-triggered-function).

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig hur du ställer in och använda webhooks, föreslagna nästa steg är att utforska [att skapa arbetsflöden i Microsoft Flow](howto-add-microsoft-flow.md).
