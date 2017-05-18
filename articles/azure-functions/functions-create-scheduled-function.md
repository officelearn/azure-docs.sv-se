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
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 10461bddeb4d5adf4a6e4f65159ba2653dbef8a4
ms.contentlocale: sv-se
ms.lasthandoff: 05/10/2017


---
#  <a name="create-a-function-in-azure-that-is-triggered-by-a-timer"></a>Skapa en funktion i Azure som utlöses av en timer

Lär dig hur du använder Azure Functions till att skapa en funktion som körs enligt ett schema du definierar. 

![Skapa en funktionsapp i Azure Portal](./media/functions-create-scheduled-function/function-app-in-portal-editor.png)

Det bör ta mindre än fem minuter att slutföra alla steg i det här avsnittet.

## <a name="prerequisites"></a>Krav 

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

I det här avsnittet skapar du en timerutlöst funktion i din befintliga funktionsapp. 

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)] 

## <a name="create-function"></a>Skapa en timerutlöst funktion

1. Expandera din funktionsapp, klicka på knappen **+** bredvid **Funktioner** och klicka på mallen **TimerTrigger** för önskat språk. Använd inställningarna som anges i tabellen och klicka på **Skapa**:

    | Inställning      |  Föreslaget värde   | Beskrivning                              |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Namnge din funktion** | TimerTriggerCSharp1 | Det här är namnet på den timerutlösta funktionen.
    | **[Schema](http://en.wikipedia.org/wiki/Cron#CRON_expression)** | 0 */1 * * * * | Ett [CRON-uttryck](http://en.wikipedia.org/wiki/Cron#CRON_expression) med sex fält som schemalägger att funktionen ska köras varje minut. |

    En funktion skapas i valt språk som körs varje minut. 

4. Kontrollera körningen genom att granska spårningsinformationen som skrivs till loggarna. 

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




