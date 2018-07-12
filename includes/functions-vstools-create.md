---
title: ta med fil
description: ta med fil
services: functions
author: ggailey777
ms.service: functions
ms.topic: include
ms.date: 05/22/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 16bda26a80611b29fdb100736cfc48978e63f75a
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38739231"
---
Med Azure Functions-projektmallen i Visual Studio skapas ett projekt som kan publiceras till en funktionsapp i Azure. Med en funktionsapp kan du gruppera funktioner som en logisk enhet så att det blir enklare att hantera, distribuera och dela resurser.

1. Välj **Nytt** > **Projekt** från **Arkiv**-menyn i Visual Studio.

2. I dialogrutan **Nytt projekt** väljer du **Installerat**, expanderar **Visual C#** > **Cloud**, väljer **Azure Functions**, skriver ett **namn** för projektet och klickar sedan på **OK**. Funktionsappens namn måste vara ett giltigt C#-namnområde. Du kan inte använda understreck, bindestreck eller andra icke-alfanumeriska tecken.

    ![Dialogrutan Nytt projekt för att skapa en funktion i Visual Studio](./media/functions-vstools-create/functions-vstools-add-new-project.png)

3. Använd inställningarna som anges i tabellen under bilden.

    ![Dialogrutan Ny funktion i Visual Studio](./media/functions-vstools-create/functions-vstools-add-new-function.png) 

    | Inställning      | Föreslaget värde  | Beskrivning                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Version** | Azure Functions v1 <br />(.NET Framework) | Med dessa inställningar skapas ett funktionsprojekt som använder körningsversion 1 av Azure Functions. Körningsversion 2, som stöder .NET Core, finns för närvarande endast som förhandsversion. Läs mer i informationen om att [köra rätt körningsversion av Azure Functions](../articles/azure-functions/functions-versions.md).   |
    | **Mall** | HTTP-utlösare | Den här inställningen skapar en funktion som utlöses av en HTTP-begäran. |
    | **Lagringskonto**  | Lagringsemulator | HTTP-utlösare använder inte Storage-kontoanslutningen. Alla andra typer av utlösare kräver en giltig anslutningssträng för Storage-kontot. |
    | **Åtkomstbehörighet** | Anonym | Funktionen som skapats kan utlösas av alla klienter utan att en nyckel anges. Den här auktoriseringsinställningen gör det enkelt att testa den nya funktionen. Mer information om nycklar och auktorisering finns i avsnittet om [auktoriseringsnycklar](../articles/azure-functions/functions-bindings-http-webhook.md#authorization-keys) i avsnittet om [HTTP- och webhook-bindningar](../articles/azure-functions/functions-bindings-http-webhook.md). |
4. Klicka på **OK** för att skapa funktionsprojektet och den HTTP-utlösta funktionen.

