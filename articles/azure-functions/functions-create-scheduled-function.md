---
title: Skapa en funktion som körs enligt ett schema i Azure | Microsoft Docs
description: Lär dig hur du skapar en funktion i Azure som körs enligt ett schema du definierar.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
ms.assetid: ba50ee47-58e0-4972-b67b-828f2dc48701
ms.service: functions
ms.devlang: multiple
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/28/2018
ms.author: glenga
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 9c961c06a01bba0b645088a19cd55e72a54396c1
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2018
---
# <a name="create-a-function-in-azure-that-is-triggered-by-a-timer"></a>Skapa en funktion i Azure som utlöses av en timer

Lär dig hur du använder Azure Functions för att skapa en funktion [utan server](https://azure.microsoft.com/overview/serverless-computing/) som körs enligt ett schema som du definierar.

![Skapa en funktionsapp i Azure Portal](./media/functions-create-scheduled-function/function-app-in-portal-editor.png)

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra den här kursen behöver du:

+ Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-an-azure-function-app"></a>Skapa en Azure Functions-app

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

![Funktionsappen skapades.](./media/functions-create-first-azure-function/function-app-create-success.png)

Därefter skapar du en funktion i den nya funktionsappen.

<a name="create-function"></a>

## <a name="create-a-timer-triggered-function"></a>Skapa en timerutlöst funktion

1. Expandera funktionsappen och klicka på knappen **+** bredvid **Funktioner**. Om det är den första funktionen i din funktionsapp väljer du **Anpassad funktion**. Detta visar en fullständig uppsättning med funktionsmallar.

    ![Sidan snabbstart för funktioner i Azure Portal](./media/functions-create-scheduled-function/add-first-function.png)

2. Skriv `timer` i sökfältet och välj sedan önskat språk för utlösarmallen för timern. 

    ![Välj mallen för den utlösta timerfunktionen.](./media/functions-create-scheduled-function/functions-create-timer-trigger.png)

3. Konfigurera den nya utlösaren med inställningar enligt tabellen under bilden.

    ![Skapa en timerutlöst funktion i Azure Portal.](./media/functions-create-scheduled-function/functions-create-timer-trigger-2.png)

    | Inställning | Föreslaget värde | Beskrivning |
    |---|---|---|
    | **Namn** | Standard | Det här är namnet på den timerutlösta funktionen. |
    | **[Schema](http://en.wikipedia.org/wiki/Cron#CRON_expression)** | 0 \*/1 \* \* \* \* | Ett [CRON-uttryck](http://en.wikipedia.org/wiki/Cron#CRON_expression) med sex fält som schemalägger att funktionen ska köras varje minut. |

2. Klicka på **Skapa**. En funktion skapas i valt språk som körs varje minut.

3. Kontrollera körningen genom att granska spårningsinformationen som skrivs till loggarna.

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

Du har skapat en funktion som körs enligt ett schema.

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Mer information timerutlösare finns i [Schedule code execution with Azure Functions](functions-bindings-timer.md) (Schemalägga kodkörning med Azure Functions).
