---
title: "Skapa din första funktion i Azure med Visual Studio | Microsoft Docs"
description: "Skapa och publicera en enkel HTTP-utlöst funktion till Azure med Azure Functions Tools för Visual Studio."
services: functions
documentationcenter: na
author: rachelappel
manager: erikre
editor: 
tags: 
keywords: azure functions, functions, event processing, compute, serverless architecture
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.service: functions
ms.devlang: multiple
ms.topic: hero-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 07/05/2017
ms.author: glenga
ms.translationtype: HT
ms.sourcegitcommit: c30998a77071242d985737e55a7dc2c0bf70b947
ms.openlocfilehash: 4a6b706b63c4e1b0df3c46bce4ff6877efca4ead
ms.contentlocale: sv-se
ms.lasthandoff: 08/02/2017

---
# <a name="create-your-first-function-using-visual-studio"></a>Skapa din första funktion med Visual Studio

Med Azure Functions kan du köra kod i en serverfri miljö utan att först behöva skapa en virtuell dator eller publicera en webbapp.

> [!IMPORTANT]
> Det här avsnittet använder vi en förhandsversion av Visual Studio för att kunna slutföra dessa steg. Se till att du har installerat [förhandsversionen av Visual Studio 2017 version 15.3](https://www.visualstudio.com/vs/preview/) innan du fortsätter.

I det här avsnittet lär du dig hur du använder Azure Functions Tools för Visual Studio 2017 för att skapa och testa en "hello world"-funktion lokalt. Du publicerar sedan funktionskoden till Azure.

![Azure Functions-kod i ett Visual Studio-projekt](./media/functions-create-your-first-function-visual-studio/functions-vstools-intro.png)

## <a name="prerequisites"></a>Krav

För att slutföra den här självstudien installerar du:

* [Visual Studio 2017 Preview version 15.3](https://www.visualstudio.com/vs/preview/) (Förhandsversion 15.3 av Visual Studio 2017) inklusive arbetsbelastningen **Azure Development**.

    ![Installera Visual Studio 2017 med arbetsbelastningen Azure Development](./media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="install-azure-functions-tools-for-visual-studio-2017"></a>Installera Azure Functions Tools för Visual Studio 2017

Innan du börjar måste du ladda ned och installera Azure Functions Tools för Visual Studio 2017. Dessa verktyg kan endast användas med Förhandsversion 15.3 av Visual Studio 2017 eller en senare version. Om du redan har installerat Azure Functions Tools kan du hoppa över det här avsnittet.

[!INCLUDE [Install the Azure Functions Tools for Visual Studio](../../includes/functions-install-vstools.md)]   

## <a name="create-an-azure-functions-project-in-visual-studio"></a>Skapa ett Azure Functions-projekt i Visual Studio

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

Nu när du har skapat projektet kan du skapa din första funktion.

## <a name="create-the-function"></a>Skapa funktionen

1. I **Solution Explorer** högerklickar du på projektnoden och väljer **Lägg till** > **Nytt objekt**. Välj **Azure Function** och klicka på **Lägg till**.

2. Välj **HttpTrigger**, ange ett **Funktionsnamn**, välj **Anonym** för **Åtkomsträttigheter** och klicka på **Skapa**. Funktionen som skapas kan nås av en HTTP-begäran från alla klienter. 

    ![Skapa en ny Azure Function](./media/functions-create-your-first-function-visual-studio/functions-vstools-add-new-function-2.png)

Nu när du har skapat en HTTP-utlöst funktion kan du testa den på en lokal dator.

## <a name="test-the-function-locally"></a>Testa funktionen lokalt

Med Azure Functions Core Tools kan du köra Azure Functions-projekt på din lokala utvecklingsdator. Du uppmanas att installera de här verktygen första gången du startar en funktion från Visual Studio.  

1. Tryck på F5 för att testa funktionen. Acceptera begäran från Visual Studio för att ladda ned och installera Azure Functions Core (CLI)-verktyg.  Du kan även behöva skapa ett brandväggsundantag så att verktygen kan hantera HTTP-förfrågningar.

2. Kopiera URL:en för funktionen från dina Azure Functions-utdata.  

    ![Lokal Azure-körningsmiljö](./media/functions-create-your-first-function-visual-studio/functions-vstools-f5.png)

3. Klistra in webbadressen för HTTP-begäran i webbläsarens adressfält. Lägg till frågesträngen `&name=<yourname>` i webbadressen och kör din begäran. Nedan visas svaret på den lokala GET-begäran som returnerades av funktionen i webbläsaren: 

    ![Svar för funktion-localhost i webbläsaren](./media/functions-create-your-first-function-visual-studio/functions-test-local-browser.png)

4. För att stoppa felsökningen klickar du på knappen **Stopp** i Visual Studio-verktygsfältet.

När du har kontrollerat att funktionen körs korrekt på den lokala datorn är det dags att publicera projektet på Azure.

## <a name="publish-the-project-to-azure"></a>Publicera projektet på Azure

Du måste ha en funktionsapp i din Azure-prenumeration innan du kan publicera projektet. Du kan skapa en funktionsapp direkt från Visual Studio.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>Testa din funktion i Azure

1. Kopiera den grundläggande URL:en för funktionsappen från sidan Publicera profil. Ersätt `localhost:port`-delen av URL:en som du använde när du testade funktionen lokalt med den nya bas-URL:en. Lägg till frågesträngen `&name=<yourname>` i URL:en som tidigare och kör din begäran.

    Den URL som anropar den HTTP-utlösta funktionen ser ut så här:

        http://<functionappname>.azurewebsites.net/api/<functionname>?name=<yourname> 

2. Klistra in den nya URL:en för HTTP-begäran i webbläsarens adressfält. Nedan visas svaret på fjärr-GET-begäran som returnerades av funktionen i webbläsaren: 

    ![Funktionssvar i webbläsaren](./media/functions-create-your-first-function-visual-studio/functions-test-remote-browser.png)
 
## <a name="next-steps"></a>Nästa steg

Du har nu använt Visual Studio för att skapa en C#-funktionsapp med en enkel HTTP-utlöst funktion. 

+ Information om hur du konfigurerar ditt projekt för att ge stöd för andra typer av utlösare och bindningar finns i [Configure the project for local development](functions-develop-vs.md#configure-the-project-for-local-development) (Konfigurera projektet för lokal utveckling) i avsnittet [Azure Functions Tools for Visual Studio](functions-develop-vs.md) (Azure Functions Tools för Visual Studio).
+ Läs mer om lokal testning och felsökning med hjälp av Azure Functions Core Tools i [Code and test Azure Functions locally](functions-run-local.md) (Koda och testa Azure Functions lokalt). 
+ Mer information om hur du utvecklar fungerar som .NET-klassbibliotek finns i [Using .NET class libraries with Azure Functions](functions-dotnet-class-library.md) (Använda .NET-klassbibliotek med Azure Functions). 


