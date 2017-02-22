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
ms.date: 02/02/2016
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: ab0b218a99ab3ff98edfa075eabbd3eb2c2bd1d4
ms.openlocfilehash: eacba057de5065acc3baa610a76a9ae135d6060a


---
# <a name="create-your-first-azure-function"></a>Skapa din första Azure-funktion
## <a name="overview"></a>Översikt
Azure Functions är en händelsedriven beräkna-på-begäran-upplevelse som utökar den befintliga Azure-programplattformen med funktioner för att implementera kod som utlöses av händelser i andra Azure-tjänster, SaaS-produkter och lokala system. Med Azure Functions skalas dina program baserat på efterfrågan och du betalar bara för de resurser som du använder. Med Azure Functions kan du skapa schemalagda eller utlösta kodenheter som implementerats i olika programmeringsspråk. Läs mer om Azure Functions i [Översikt över Azure Functions](functions-overview.md).

Det här ämnet visar hur du använder Azure Functions-snabbstarten i portalen för att skapa en enkel "hello world"-funktion i JavaScript som anropas av en HTTP-utlösare. Du kan också titta på en kort video om du vill se hur dessa steg utförs i portalen.

## <a name="watch-the-video"></a>Titta på videon
Följande videoklipp visar hur du utför de grundläggande stegen i den här självstudiekursen. 

> [!VIDEO https://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Create-your-first-Azure-Function-simple/player]
> 
> 

## <a name="create-a-function-from-the-quickstart"></a>Skapa en funktion från snabbstarten
En funktionsapp är värd för körningen av dina funktioner i Azure. Följ dessa steg för att skapa en funktionsapp med den nya funktionen. Funktionsappen skapas med en standardkonfiguration. Ett exempel på hur du uttryckligen skapar funktionsappen finns i [Snabbstartsguide för de andra Azure-funktionerna](functions-create-first-azure-function-azure-portal.md).

Innan du kan skapa din första funktion måste du ha ett aktivt Azure-konto. Om du inte redan har ett Azure-konto, [finns kostnadsfria konton tillgängliga](https://azure.microsoft.com/free/).

1. Gå till [Azure Functions-portalen](https://functions.azure.com/signin) och logga in med ditt Azure-konto.
2. Skriv ett unikt **namn** för din nya funktionsapp eller acceptera det genererade namnet, välj **Region** och klicka på **Skapa + Kom igång**. Observera att du måste ange ett giltigt namn som endast får innehålla bokstäver, siffror och bindestreck. Understreck (**_**) är inte tillåtna.
3. På fliken **Snabbstart** klickar du på **WebHook + API**och**JavaScript**, klicka sedan på **Skapa en funktion**. En ny fördefinierad JavaScript-funktion skapas. 
   
    ![](./media/functions-create-first-azure-function/function-app-quickstart-node-webhook.png)
4. (Valfritt) I det här läget i Snabbstart kan du välja att få en snabb genomgång av funktionerna i Azure Functions i portalen. När du har slutfört eller hoppat över genomgången kan du testa den nya funktionen med hjälp av HTTP-utlösaren.

## <a name="test-the-function"></a>Testa funktionen
[!INCLUDE [Functions quickstart test](../../includes/functions-quickstart-test.md)]

## <a name="next-steps"></a>Nästa steg
[!INCLUDE [Functions quickstart next steps](../../includes/functions-quickstart-next-steps.md)]

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]




<!--HONumber=Feb17_HO1-->


