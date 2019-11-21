---
title: Create a function that runs on a schedule in Azure
description: Lär dig hur du skapar en funktion i Azure som körs enligt ett schema du definierar.
ms.assetid: ba50ee47-58e0-4972-b67b-828f2dc48701
ms.topic: quickstart
ms.date: 03/28/2018
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 8e89c3923daab15793707ff99dbbed6deeb6a0b0
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227176"
---
# <a name="create-a-function-in-azure-that-is-triggered-by-a-timer"></a>Skapa en funktion i Azure som utlöses av en timer

Learn how to use Azure Functions to create a [serverless](https://azure.microsoft.com/solutions/serverless/) function that runs based on a schedule that you define.

![Skapa en funktionsapp i Azure Portal](./media/functions-create-scheduled-function/function-app-in-portal-editor.png)

## <a name="prerequisites"></a>Krav

För att slutföra den här självstudien behöver du:

+ Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-an-azure-function-app"></a>Skapa en Azure Functions-app

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

![Funktionsappen skapades.](./media/functions-create-first-azure-function/function-app-create-success.png)

Därefter skapar du en funktion i den nya funktionsappen.

<a name="create-function"></a>

## <a name="create-a-timer-triggered-function"></a>Skapa en timerutlöst funktion

1. Expandera funktionsappen och klicka på knappen **+** bredvid **Funktioner**. Om det här är den första funktionen i din funktionsapp väljer du **I portalen** och sedan **Fortsätt**. Otherwise, go to step 3.

   ![Sidan snabbstart för funktioner i Azure Portal](./media/functions-create-scheduled-function/function-app-quickstart-choose-portal.png)

2. Välj **Fler mallar** och sedan **Slutför och visa mallar**.

    ![Functions-snabbstart, välj fler mallar](./media/functions-create-scheduled-function/add-first-function.png)

3. I sökfältet skriver du `timer` och konfigurerar den nya utlösaren med inställningarna enligt tabellen under bilden.

    ![Skapa en timerutlöst funktion i Azure Portal.](./media/functions-create-scheduled-function/functions-create-timer-trigger-2.png)

    | Inställning | Föreslaget värde | Beskrivning |
    |---|---|---|
    | **Namn** | Standard | Det här är namnet på den timerutlösta funktionen. |
    | **Schema** | 0 \*/1 \* \* \* \* | Ett [CRON-uttryck](functions-bindings-timer.md#ncrontab-expressions) med sex fält som schemalägger att funktionen ska köras varje minut. |

4. Klicka på **Skapa**. En funktion skapas i valt språk som körs varje minut.

5. Kontrollera körningen genom att granska spårningsinformationen som skrivs till loggarna.

    ![Funktionsloggvisning i Azure Portal.](./media/functions-create-scheduled-function/functions-timer-trigger-view-logs2.png)

Sedan ändrar du funktionens schema så att det körs en gång per timme i stället för varje minut.

## <a name="update-the-timer-schedule"></a>Uppdatera timerschemat

1. Expandera funktionen och klicka på **Integrera**. Det är här du definierar in- och utdatabindningar för funktionen och även anger schemat. 

2. Ange ett nytt **Schema**-värde för `0 0 */1 * * *` så att det körs varje timme och klicka sedan på **Spara**.  

![Funktioner, uppdatera timerschema i Azure Portal.](./media/functions-create-scheduled-function/functions-timer-trigger-change-schedule.png)

Du har nu en funktion som körs en gång i timmen. 

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

Du har skapat en funktion som körs enligt ett schema. Mer information timerutlösare finns i [Schedule code execution with Azure Functions](functions-bindings-timer.md) (Schemalägga kodkörning med Azure Functions).

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
