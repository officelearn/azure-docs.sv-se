---
title: Skapa din första funktion i Azure med Visual Studio | Microsoft Docs
description: Skapa och publicera en enkel HTTP-utlöst funktion till Azure med Azure Functions Tools för Visual Studio.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
keywords: azure functions, functions, event processing, compute, serverless architecture
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.service: functions
ms.devlang: multiple
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/13/2018
ms.author: glenga
ms.custom: mvc, devcenter
ms.openlocfilehash: a1f0a022b4620b15e2d76a127ed48e5472e4a5bb
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="create-your-first-function-using-visual-studio"></a>Skapa din första funktion med Visual Studio

Med Azure Functions kan du köra kod i en [serverfri](https://azure.microsoft.com/overview/serverless-computing/) miljö utan att först behöva skapa en virtuell dator eller publicera en webbapp.

I den här artikeln får du lära dig hur du använder Visual Studio 2017-verktyg med Azure Functions för att skapa och testa en ”Hello World”-funktion lokalt. Du publicerar sedan funktionskoden till Azure. De här verktygen är tillgängliga som en del av arbetsbelastningen Azure Development i Visual Studio 2017.

![Azure Functions-kod i ett Visual Studio-projekt](./media/functions-create-your-first-function-visual-studio/functions-vstools-intro.png)

Det här avsnittet innehåller [en video](#watch-the-video) som visar samma grundläggande steg.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra den här kursen behöver du:

* Installera [Visual Studio 2017 version 15.5](https://www.visualstudio.com/vs/) eller en senare version, inklusive arbetsbelastningen **Azure-utveckling**.

    ![Installera Visual Studio 2017 med arbetsbelastningen Azure Development](./media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

    Om du redan har installerat Visual Studio kontrollerar du att du har installerat eventuella väntande uppdateringar. 

* Om du har installerat arbetsbelastningen för Azure-utveckling med Visual Studio 2017 version 15.4 eller tidigare kan du också behöva [uppdatera Azure Functions-verktygen](functions-develop-vs.md#check-your-tools-version). 
    
[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 

## <a name="create-a-function-app-project"></a>Skapa ett funktionsapprojekt

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

Visual Studio skapar ett projekt och i det en klass som innehåller formaterad exempelkod för den valda typen. Attributet **FunctionName** i metoden anger namnet på funktionen. Attributet **HttpTrigger** anger att funktionen utlöses av en HTTP-förfrågan. Den formaterade exempelkoden skickar ett HTTP-svar som innehåller ett värde från förfrågan eller frågesträngen. Du kan lägga till bindningar för indata och utdata i en funktion genom att lägga till lämpliga attribut i metoden. Mer information finns i avsnittet [Triggers and bindings](functions-dotnet-class-library.md#triggers-and-bindings) (Utlösare och bindningar) i [Azure Functions C# developer reference](functions-dotnet-class-library.md) (Azure Functions C#-referens för utvecklare).

![Funktionens kodfil](./media/functions-create-your-first-function-visual-studio/functions-code-page.png)

Nu när du har skapat ditt funktionsprojekt och en HTTP-utlöst funktion kan du testa den på en lokal dator.

## <a name="test-the-function-locally"></a>Testa funktionen lokalt

Med Azure Functions Core Tools kan du köra ett Azure Functions-projekt på din lokala utvecklingsdator. Du uppmanas att installera de här verktygen första gången du startar en funktion från Visual Studio.  

1. Tryck på F5 för att testa funktionen. Acceptera begäran från Visual Studio för att ladda ned och installera Azure Functions Core (CLI)-verktyg. Du kan även behöva skapa ett brandväggsundantag så att verktygen kan hantera HTTP-förfrågningar.

2. Kopiera URL:en för funktionen från dina Azure Functions-utdata.  

    ![Lokal Azure-körningsmiljö](./media/functions-create-your-first-function-visual-studio/functions-vstools-f5.png)

3. Klistra in webbadressen för HTTP-begäran i webbläsarens adressfält. Lägg till frågesträngen `?name=<yourname>` i webbadressen och kör din begäran. Nedan visas svaret på den lokala GET-begäran som returnerades av funktionen i webbläsaren: 

    ![Svar för funktion-localhost i webbläsaren](./media/functions-create-your-first-function-visual-studio/functions-test-local-browser.png)

4. Tryck på SKIFT+F5 för att stoppa felsökningen.

När du har kontrollerat att funktionen körs korrekt på den lokala datorn är det dags att publicera projektet på Azure.

## <a name="publish-the-project-to-azure"></a>Publicera projektet på Azure

Du måste ha en funktionsapp i din Azure-prenumeration innan du kan publicera projektet. Du kan skapa en funktionsapp direkt från Visual Studio.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>Testa din funktion i Azure

1. Kopiera den grundläggande URL:en för funktionsappen från sidan Publicera profil. Ersätt `localhost:port`-delen av URL:en som du använde när du testade funktionen lokalt med den nya bas-URL:en. Lägg till frågesträngen `?name=<yourname>` i URL:en som tidigare och kör din begäran.

    Den URL som anropar din HTTP-utlösta funktion ska ha följande format:

        http://<functionappname>.azurewebsites.net/api/<functionname>?name=<yourname> 

2. Klistra in den nya URL:en för HTTP-begäran i webbläsarens adressfält. Nedan visas svaret på fjärr-GET-begäran som returnerades av funktionen i webbläsaren: 

    ![Funktionssvar i webbläsaren](./media/functions-create-your-first-function-visual-studio/functions-test-remote-browser.png)

## <a name="watch-the-video"></a>Titta på videon

> [!VIDEO https://www.youtube-nocookie.com/embed/DrhG-Rdm80k]

## <a name="next-steps"></a>Nästa steg

Du har nu använt Visual Studio till att skapa och publicera en C#-funktionsapp med en enkel HTTP-utlöst funktion. 

+ Information om hur du konfigurerar ditt projekt för att ge stöd för andra typer av utlösare och bindningar finns i [Configure the project for local development](functions-develop-vs.md#configure-the-project-for-local-development) (Konfigurera projektet för lokal utveckling) i avsnittet [Azure Functions Tools for Visual Studio](functions-develop-vs.md) (Azure Functions Tools för Visual Studio).
+ Läs mer om lokal testning och felsökning med hjälp av Azure Functions Core Tools i [Code and test Azure Functions locally](functions-run-local.md) (Koda och testa Azure Functions lokalt). 
+ Mer information om hur du utvecklar fungerar som .NET-klassbibliotek finns i [Using .NET class libraries with Azure Functions](functions-dotnet-class-library.md) (Använda .NET-klassbibliotek med Azure Functions). 

