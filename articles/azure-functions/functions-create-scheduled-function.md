---
title: "Skapa en funktion som körs enligt ett schema i Azure | Microsoft Docs"
description: "Lär dig hur du skapar en funktion i Azure som körs enligt ett schema du definierar."
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: ba50ee47-58e0-4972-b67b-828f2dc48701
ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/02/2017
ms.author: glenga
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: 1478a2eedad496d3113fef28920d10859d11b1ce
ms.contentlocale: sv-se
ms.lasthandoff: 05/12/2017


---
# <a name="create-a-function-in-azure-that-is-triggered-by-a-timer"></a>Skapa en funktion i Azure som utlöses av en timer

Lär dig hur du använder Azure Functions till att skapa en funktion som körs enligt ett schema du definierar.

![Skapa en funktionsapp i Azure Portal](./media/functions-create-scheduled-function/function-app-in-portal-editor.png)

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)]

## <a name="create-an-azure-function-app"></a>Skapa en Azure Functions-app

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

![Funktionsappen skapades.](./media/functions-create-first-azure-function/function-app-create-success.png)

Därefter skapar du en funktion i den nya funktionsappen.

<a name="create-function"></a>

## <a name="create-a-timer-triggered-function"></a>Skapa en timerutlöst funktion

1. Expandera din funktionsapp, klicka på knappen **+** bredvid **Funktioner** och klicka på mallen **TimerTrigger** för önskat språk. Använd inställningarna som anges i tabellen och klicka på **Skapa**:

| Inställning | Föreslaget värde | Beskrivning |
|---|---|---|
| **Namnge din funktion** | TimerTriggerCSharp1 | Det här är namnet på den timerutlösta funktionen. |
| **[Schema](http://en.wikipedia.org/wiki/Cron#CRON_expression)** | 0 \*/1 \* \* \* \* | Ett [CRON-uttryck](http://en.wikipedia.org/wiki/Cron#CRON_expression) med sex fält som schemalägger att funktionen ska köras varje minut. |

En funktion skapas i valt språk som körs varje minut.

1. Kontrollera körningen genom att granska spårningsinformationen som skrivs till loggarna.

![Funktionsloggvisning i Azure Portal.](./media/functions-create-scheduled-function/functions-timer-trigger-view-logs2.png)

Du kan nu ändra funktionens schema så att den körs mindre ofta, till exempel en gång i timmen. 

## <a name="update-the-timer-schedule"></a>Uppdatera timerschemat

1. Expandera funktionen och klicka på **Integrera**. Det är här du definierar in- och utdatabindningar för funktionen och även anger schemat. 

2. Ange det nya **Schema**-värdet `0 0 */1 * * *` och klicka sedan på **Spara**.  

![Funktioner, uppdatera timerschema i Azure Portal.](./media/functions-create-scheduled-function/functions-timer-trigger-change-schedule.png)

Du har nu en funktion som körs en gång i timmen. 

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

Du har skapat en funktion som körs enligt ett schema.

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Mer information timerutlösare finns i [Schedule code execution with Azure Functions](functions-bindings-timer.md) (Schemalägga kodkörning med Azure Functions).
