---
title: ta med fil
description: ta med fil
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/05/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 17b7626f79d7d356e3e8f3440e4a6526f2df776d
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68593822"
---
Med Azure Functions-projektmallen i Visual Studio skapas ett projekt som kan publiceras till en funktionsapp i Azure. Du kan använda en Function-app för att gruppera funktioner som en logisk enhet för hantering, distribution och delning av resurser.

1. I Visual Studio väljer du **nytt** > **projekt**på **Arkiv** -menyn.

1. I dialog rutan **skapa ett nytt projekt** söker du efter `functions`, väljer mallen **Azure Functions** och väljer **Nästa**.

1. Ange ett namn för projektet och välj **skapa**. Funktionsappens namn måste vara ett giltigt C#-namnområde. Du kan inte använda understreck, bindestreck eller andra icke-alfanumeriska tecken.

1. Använd följande alternativ i **skapa ett nytt Azure Functions-program**:

    + **Azure Functions v2 (.net Core)** 1
    + **HTTP-utlösare**
    + **Lagrings konto**: **Storage-emulator**
    + **Autentiseringsnivå**: **Antal** 

    | Alternativ      | Föreslaget värde  | Beskrivning                      |
    | ------------ |  ------- |----------------------------------------- |
    | Functions-körning | **Azure Functions 2. x <br />(.net Core)** | Med den här inställningen skapas ett Function-projekt som använder version 2. x-körningen för Azure Functions, som har stöd för .NET Core. Azure Functions 1.x har stöd för .NET Framework. Mer information finns i [mål Azure Functions runtime-version](../articles/azure-functions/functions-versions.md).   |
    | Funktions mal len | **HTTP-utlösare** | Den här inställningen skapar en funktion som utlöses av en HTTP-begäran. |
    | **Lagringskonto**  | **Storage-emulator** | En HTTP-utlösare använder inte Azure Storage konto anslutningen. Alla andra typer av utlösare kräver en giltig anslutningssträng för Storage-kontot. Eftersom-funktioner kräver ett lagrings konto, tilldelas eller skapas en när du publicerar projektet till Azure. |
    | **Auktorisationsnivå** | **Antal** | Funktionen som skapats kan utlösas av alla klienter utan att en nyckel anges. Den här auktoriseringsinställningen gör det enkelt att testa den nya funktionen. Mer information om nycklar och auktorisering finns i avsnittet om [auktoriseringsnycklar](../articles/azure-functions/functions-bindings-http-webhook.md#authorization-keys) i avsnittet om [HTTP- och webhook-bindningar](../articles/azure-functions/functions-bindings-http-webhook.md). |
    
    > [!NOTE]
    > Se till att du ställer in behörighets `Anonymous` **nivån** på. Om du väljer standard nivån för `Function`, måste du presentera [funktions nyckeln](../articles/azure-functions/functions-bindings-http-webhook.md#authorization-keys) i begär Anden för att få åtkomst till funktions slut punkten.
    
4. Välj **skapa** för att skapa funktionen Project och http-utlöst funktion.
