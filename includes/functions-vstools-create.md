---
title: ta med fil
description: ta med fil
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
origin.date: 03/05/2019
ms.date: 03/25/2019
ms.author: v-junlch
ms.custom: include file
ms.openlocfilehash: 6c430f22a9d4fa0fad95bcaa41675545fffd91ec
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60403870"
---
Med Azure Functions-projektmallen i Visual Studio skapas ett projekt som kan publiceras till en funktionsapp i Azure. Du kan använda en funktionsapp som gruppen ska fungera som en logisk enhet för hantering, distribution och dela resurser.

1. I Visual Studio på den **filen** menyn och välj **New** > **projekt**.

2. I den **nytt projekt** dialogrutan **installerad** > **Visual C#**   >  **molnet**  >  **Azure Functions**. Ange ett namn för projektet och välj **OK**. Funktionsappens namn måste vara ett giltigt C#-namnområde. Du kan inte använda understreck, bindestreck eller andra icke-alfanumeriska tecken.

    ![Dialogrutan Nytt projekt att skapa en funktion i Visual Studio](./media/functions-vstools-create/functions-vs-new-project.png)

3. Använd inställningarna som anges i tabellen under bilden.

    ![Dialogrutan ny funktion i Visual Studio](./media/functions-vstools-create/functions-vs-new-function.png) 

    | Inställning      | Föreslaget värde  | Beskrivning                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Version** | Azure Functions 2.x <br />(.NET Core) | Den här inställningen skapar en function-projekt som använder version 2.x-körningen i Azure Functions, som har stöd för .NET Core. Azure Functions 1.x har stöd för .NET Framework. Mer information finns i [Target Azure Functions runtime-versionen](../articles/azure-functions/functions-versions.md).   |
    | **Mall** | HTTP-utlösare | Den här inställningen skapar en funktion som utlöses av en HTTP-begäran. |
    | **Lagringskonto**  | Storage-emulatorn | En HTTP-utlösare använda inte anslutningen för Azure Storage-konto. Alla andra typer av utlösare kräver en giltig anslutningssträng för Storage-kontot. |
    | **Åtkomstbehörighet** | Anonym | Funktionen som skapats kan utlösas av alla klienter utan att en nyckel anges. Den här auktoriseringsinställningen gör det enkelt att testa den nya funktionen. Mer information om nycklar och auktorisering finns i avsnittet om [auktoriseringsnycklar](../articles/azure-functions/functions-bindings-http-webhook.md#authorization-keys) i avsnittet om [HTTP- och webhook-bindningar](../articles/azure-functions/functions-bindings-http-webhook.md). |
    
    > [!NOTE]
    > Se till att ange den **åtkomstbehörigheter** till `Anonymous`. Om du väljer vilken `Function`, så måste du presentera den [funktionsnyckel](../articles/azure-functions/functions-bindings-http-webhook.md#authorization-keys) i förfrågningar om åtkomst till din funktion-slutpunkt.
    
4. Välj **OK** att skapa function-projekt och HTTP-utlöst funktion.

