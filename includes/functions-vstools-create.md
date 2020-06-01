---
title: ta med fil
description: ta med fil
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/26/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 4e56063891a92ef06e3e14651e26f0b73d280e79
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/31/2020
ms.locfileid: "84231526"
---
Azure Functions projekt mal len i Visual Studio skapar ett projekt som du kan publicera till en Function-app i Azure. Du kan använda en Function-app för att gruppera funktioner som en logisk enhet för enklare hantering, distribution, skalning och delning av resurser.

1. Från Visual Studio-menyn väljer du **fil**  >  **nytt**  >  **projekt**.

1. I **skapa ett nytt projekt**, ange *funktioner* i sökrutan, Välj mallen **Azure Functions** och välj sedan **Nästa**.

1. I **Konfigurera ditt nya projekt**anger du ett **projekt namn** för projektet och väljer sedan **skapa**. Funktionsappens namn måste vara ett giltigt C#-namnområde. Du kan inte använda understreck, bindestreck eller andra icke-alfanumeriska tecken.

1. Använd värdena i följande tabell för inställningarna **skapa en ny Azure Function-applikation** :

    | Inställning      | Värde  | Beskrivning                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Functions-körning** | **Azure Functions v3 <br /> (.net Core)** | Det här värdet skapar ett funktions projekt som använder version 3. x-körningen av Azure Functions, som stöder .NET Core 3. x. Azure Functions 1.x har stöd för .NET Framework. Mer information finns i [Översikt över Azure Functions körnings versioner](../articles/azure-functions/functions-versions.md).   |
    | **Funktions mal len** | **HTTP-utlösare** | Det här värdet skapar en funktion som utlöses av en HTTP-begäran. |
    | **Lagrings konto**  | **Lagringsemulator** | Eftersom en Azure-funktion kräver ett lagrings konto, tilldelas eller skapas en när du publicerar projektet till Azure. En HTTP-utlösare använder inte en anslutnings sträng för Azure Storage konto. alla andra utlösare kräver en giltig anslutnings sträng för Azure Storage konto.  |
    | **Åtkomstbehörighet** | **Anonym** | Funktionen som skapats kan utlösas av alla klienter utan att en nyckel anges. Den här auktoriseringsinställningen gör det enkelt att testa den nya funktionen. Mer information om nycklar och auktorisering finns i [auktoriseringsarkiv](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) och [http-och webhook-bindningar](../articles/azure-functions/functions-bindings-http-webhook.md). |
    
    
    ![Azure Functions projekt inställningar](./media/functions-vs-tools-create/functions-project-settings.png)

    Se till att du ställer in **åtkomst behörighet** till **Anonym**. Om du väljer standard **funktions**nivån måste du presentera [funktions nyckeln](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) i begär Anden för att få åtkomst till funktions slut punkten.

1. Välj **skapa** för att skapa funktionen Project och funktionen http-utlösare.
