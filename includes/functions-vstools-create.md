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
ms.openlocfilehash: a27d44a7684b4ad3d39d7fba50ca52e08e932971
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769784"
---
Med Azure Functions-projektmallen i Visual Studio skapas ett projekt som kan publiceras till en funktionsapp i Azure. Du kan använda en Function-app för att gruppera funktioner som en logisk enhet för enklare hantering, distribution, skalning och delning av resurser.

1. I Visual Studio väljer du **nytt** > **projekt**på **Arkiv** -menyn.

1. I dialog rutan **skapa ett nytt projekt** söker du efter `functions`, väljer **Azure Functions** mal len och väljer **Nästa**.

1. Ange ett namn för projektet och välj **skapa**. Funktionsappens namn måste vara ett giltigt C#-namnområde. Du kan inte använda understreck, bindestreck eller andra icke-alfanumeriska tecken.

1. Använd följande alternativ i **skapa ett nytt Azure Functions-program**:

    + **Azure Functions v2 (.NET Core)**
    + **HTTP-utlösare**
    + **Lagrings konto**: **Storage-emulator**
    + **Autentiseringsnivå**: **Anonym** 

    | Alternativ      | Föreslaget värde  | Beskrivning                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Functions-körning** | **Azure Functions 2. x <br />(.NET Core)** | Med den här inställningen skapas ett Function-projekt som använder version 2. x-körningen för Azure Functions, som har stöd för .NET Core. Azure Functions 1.x har stöd för .NET Framework. Mer information finns i [mål Azure Functions runtime-version](../articles/azure-functions/functions-versions.md).   |
    | **Funktions mal len** | **HTTP-utlösare** | Den här inställningen skapar en funktion som utlöses av en HTTP-begäran. |
    | **Lagringskonto**  | **Storage-emulator** | En HTTP-utlösare använder inte Azure Storage konto anslutningen. Alla andra typer av utlösare kräver en giltig anslutningssträng för Storage-kontot. Eftersom-funktioner kräver ett lagrings konto, tilldelas eller skapas en när du publicerar projektet till Azure. |
    | **Auktorisationsnivå** | **Antal** | Funktionen som skapats kan utlösas av alla klienter utan att en nyckel anges. Den här auktoriseringsinställningen gör det enkelt att testa den nya funktionen. Mer information om nycklar och auktorisering finns i avsnittet om [auktoriseringsnycklar](../articles/azure-functions/functions-bindings-http-webhook.md#authorization-keys) i avsnittet om [HTTP- och webhook-bindningar](../articles/azure-functions/functions-bindings-http-webhook.md). |
    
    > [!NOTE]
    > Se till att du ställer in **behörighets nivån** på `Anonymous`. Om du väljer standard nivån `Function`måste du presentera [funktions nyckeln](../articles/azure-functions/functions-bindings-http-webhook.md#authorization-keys) i begär Anden för att få åtkomst till funktions slut punkten.
    
4. Välj **skapa** för att skapa funktionen Project och http-utlöst funktion.
