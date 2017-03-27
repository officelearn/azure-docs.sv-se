---
title: "Skapa din första Azure-funktion | Microsoft Docs"
description: "Skapa din första Azure-funktion, ett program utan server, på mindre än två minuter."
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 4a1669e7-233e-4ea2-9b83-b8624f2dbe59
ms.service: functions
ms.devlang: multiple
ms.topic: hero-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/14/2016
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: a797910e286cd2aacf5a8aa6c509e2b0f5f60276
ms.lasthandoff: 03/17/2017


---
# <a name="create-your-first-azure-function"></a>Skapa din första Azure-funktion

Det här ämnet visar hur du använder Azure Functions-snabbstart i portalen för att skapa en enkel "hello world"-funktion som anropas av en HTTP-begäran. Läs mer om Azure Functions i [Översikt över Azure Functions](functions-overview.md).

Innan du börjar måste du ha ett Azure-konto. Det finns [kostnadsfria konton](https://azure.microsoft.com/free/). Du kan också [prova Azure Functions](https://azure.microsoft.com/try/app-service/functions/) utan Azure-registrering.

## <a name="create-a-function-from-the-portal-quickstart"></a>Skapa en funktion från snabbstarten

1. Gå till [Azure Functions-portalen](https://functions.azure.com/signin) och logga in med ditt Azure-konto. 
 
2. Skriv ett unikt **Namn** för din nya funktionsapp eller acceptera namnet som genererats automatiskt, välj **Region** och klicka på **Skapa + Kom igång**. Namn får endast innehålla bokstäver, siffror och bindestreck. Understreck (**_**) är inte tillåtna.

3. På fliken **Snabbstart** klickar du på **WebHook + API**, väljer ett språk för funktionen och klickar sedan på **Skapa en funktion**. En ny fördefinierad funktion skapas på det språk som du har valt. 
   
    ![](./media/functions-create-first-azure-function/function-app-quickstart-node-webhook.png)

4. (Valfritt) I det här läget i Snabbstart kan du välja att få en snabb genomgång av funktionerna i Azure Functions i portalen. När du har slutfört eller hoppat över genomgången kan du testa den nya funktionen genom att skicka en HTTP-begäran.

## <a name="test-the-function"></a>Testa funktionen
[!INCLUDE [Functions quickstart test](../../includes/functions-quickstart-test.md)]

## <a name="watch-the-video"></a>Titta på videon
Följande videoklipp visar hur du utför de grundläggande stegen i den här självstudiekursen. 

> [!VIDEO https://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Create-your-first-Azure-Function-simple/player]
> 


## <a name="next-steps"></a>Nästa steg
[!INCLUDE [Functions quickstart next steps](../../includes/functions-quickstart-next-steps.md)]

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]


