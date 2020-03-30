---
title: ta med fil
description: ta med fil
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/06/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 034e966d259f1ca5f22eec5935013de32c883b59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80056600"
---
Azure Functions-projektmallen i Visual Studio skapar ett projekt som du kan publicera till en funktionsapp i Azure. Du kan använda en funktionsapp för att gruppera funktioner som en logisk enhet för enklare hantering, distribution, skalning och delning av resurser.

1. Välj **Nytt** > **projekt**på **Arkiv-menyn** i Visual Studio .

1. I **Skapa ett nytt projekt**anger du *funktioner* i sökrutan och väljer sedan mallen **Azure Functions.**

1. Ange ett **projektnamn** för projektet i **Konfigurera det nya projektet**och välj sedan **Skapa**. Funktionsappens namn måste vara ett giltigt C#-namnområde. Du kan inte använda understreck, bindestreck eller andra icke-alfanumeriska tecken.

1. Använd värdena i följande tabell för inställningarna **för Nytt projekt – &lt;projektets namn:&gt; **

    | Inställning      | Värde  | Beskrivning                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Funktioner körtid** | **Azure-funktioner <br />v2 (.NET Core)** | Det här värdet skapar ett funktionsprojekt som använder körningen version 2.x av Azure Functions, som stöder .NET Core. Azure Functions 1.x har stöd för .NET Framework. Mer information finns i [översikt över Azure Functions runtime-versioner](../articles/azure-functions/functions-versions.md).   |
    | **Funktionsmall** | **HTTP-utlösare** | Det här värdet skapar en funktion som utlöses av en HTTP-begäran. |
    | **Lagringskonto**  | **Lagringsemulator** | Eftersom en Azure-funktion kräver ett lagringskonto tilldelas eller skapas ett när du publicerar projektet till Azure. En HTTP-utlösare använder inte en Azure Storage-kontoanslutningssträng. alla andra utlösartyper kräver en giltig Azure Storage-kontoanslutningssträng.  |
    | **Åtkomstbehörighet** | **Anonym** | Funktionen som skapats kan utlösas av alla klienter utan att en nyckel anges. Den här auktoriseringsinställningen gör det enkelt att testa den nya funktionen. Mer information om nycklar och auktorisering finns i [Auktoriseringsnycklar](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) och [HTTP- och webhook-bindningar](../articles/azure-functions/functions-bindings-http-webhook.md). |
    

    
    ![Projektinställningar för Azure Functions](./media/functions-vs-tools-create/functions-project-settings.png)

    Se till att du anger **åtkomsträttigheterna** till **Anonym**. Om du väljer standardnivå för **funktion**måste du presentera [funktionsnyckeln](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) i begäranden för att komma åt funktionsslutpunkten.

1. Välj **OK** om du vill skapa funktionen funktionsprojekt och HTTP-utlöst funktion.
