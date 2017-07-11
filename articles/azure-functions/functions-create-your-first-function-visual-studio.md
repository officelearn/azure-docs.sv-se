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
ms.date: 06/25/2017
ms.author: rachelap, glenga
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 64769458ad90f14c2f7a87b9a405b80616a478be
ms.contentlocale: sv-se
ms.lasthandoff: 06/28/2017


---
<a id="create-your-first-function-using-visual-studio" class="xliff"></a>

# Skapa din första funktion med Visual Studio 

Med Azure Functions kan du köra kod i en serverfri miljö utan att först behöva skapa en virtuell dator eller publicera en webbapp. 

I det här avsnittet lär du dig hur du använder Azure Functions Tools för Visual Studio 2017 för att skapa och testa en "hello world"-funktion lokalt. Du publicerar sedan funktionskoden till Azure.

![Azure Functions-kod i ett Visual Studio-projekt](./media/functions-create-your-first-function-visual-studio/functions-vstools-intro.png)

<a id="prerequisites" class="xliff"></a>

## Krav

För att slutföra den här självstudien installerar du:

* [Visual Studio 2017 Preview version 15.3](https://www.visualstudio.com/vs/preview/) (Förhandsversion 15.3 av Visual Studio 2017) inklusive arbetsbelastningen **Azure Development**.
    
    ![Installera Visual Studio 2017 med arbetsbelastningen Azure Development](./media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

<a id="install-azure-functions-tools-for-visual-studio-2017" class="xliff"></a>

## Installera Azure Functions Tools för Visual Studio 2017

Innan du börjar måste du ladda ned och installera Azure Functions Tools för Visual Studio 2017. Dessa verktyg kan endast användas med Förhandsversion 15.3 av Visual Studio 2017 eller en senare version. Om du redan har installerat Azure Functions Tools kan du hoppa över det här avsnittet.

[!INCLUDE [Install the Azure Functions Tools for Visual Studio](../../includes/functions-install-vstools.md)]   

<a id="create-an-azure-functions-project-in-visual-studio" class="xliff"></a>

## Skapa ett Azure Functions-projekt i Visual Studio

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

Nu när du har skapat projektet kan du skapa din första funktion.

<a id="create-the-function" class="xliff"></a>

## Skapa funktionen

I **Solution Explorer** högerklickar du på projektnoden och väljer **Lägg till** > **Nytt objekt**. Välj **Azure Function** och klicka på **Lägg till**.

Välj **HttpTrigger**, ange ett **Funktionsnamn**, välj **Anonym** för **Åtkomsträttigheter** och klicka på **Skapa**. Funktionen som skapas kan nås av en HTTP-begäran från alla klienter. 

![Skapa en ny Azure Function](./media/functions-create-your-first-function-visual-studio/functions-vstools-add-new-function-2.png)

Nu när du har skapat en HTTP-utlöst funktion kan du testa den på en lokal dator.

<a id="test-the-function-locally" class="xliff"></a>

## Testa funktionen lokalt

[!INCLUDE [Test the function locally](../../includes/functions-vstools-test.md)]

Kopiera URL:en för funktionen från dina Azure Functions-utdata.  

![Lokal Azure-körningsmiljö](./media/functions-create-your-first-function-visual-studio/functions-vstools-f5.png)

 Klistra in webbadressen för HTTP-begäran i webbläsarens adressfält. Lägg till frågesträngen `&name=<yourname>` i webbadressen och kör din begäran. Nedan visas svaret på den lokala GET-begäran som returnerades av funktionen i webbläsaren: 

![Svar för funktion-localhost i webbläsaren](./media/functions-create-your-first-function-visual-studio/functions-test-local-browser.png)

För att stoppa felsökningen klickar du på knappen **Stopp** i Visual Studio-verktygsfältet.

När du har kontrollerat att funktionen körs korrekt på den lokala datorn är det dags att publicera projektet på Azure.

<a id="publish-the-project-to-azure" class="xliff"></a>

## Publicera projektet på Azure

Du måste ha en funktionsapp i din Azure-prenumeration innan du kan publicera projektet. Du kan skapa en funktionsapp direkt från Visual Studio.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

<a id="test-your-function-in-azure" class="xliff"></a>

## Testa din funktion i Azure

Kopiera den grundläggande URL:en för funktionsappen från sidan Publicera profil. Ersätt `localhost:port`-delen av URL:en som du använde när du testade funktionen lokalt med den nya bas-URL:en. Lägg till frågesträngen `&name=<yourname>` i URL:en som tidigare och kör din begäran.

Den URL som anropar den HTTP-utlösta funktionen ser ut så här:

    http://<functionappname>.azurewebsites.net/api/<functionname>?name=<yourname> 

Klistra in den nya URL:en för HTTP-begäran i webbläsarens adressfält. Nedan visas svaret på fjärr-GET-begäran som returnerades av funktionen i webbläsaren: 

![Funktionssvar i webbläsaren](./media/functions-create-your-first-function-visual-studio/functions-test-remote-browser.png)
 
<a id="next-steps" class="xliff"></a>

## Nästa steg

Du har nu använt Visual Studio för att skapa en C#-funktionsapp med en enkel HTTP-utlöst funktion. 

[!INCLUDE [functions-quickstart-next-steps](../../includes/functions-quickstart-next-steps.md)]

Läs mer om lokal testning och felsökning med hjälp av Azure Functions Core Tools i [Code and test Azure Functions locally](functions-run-local.md) (Koda och testa Azure Functions lokalt). 


