---
title: "Skapa en funktion i Azure som utlöses av en GitHub-webhook | Microsoft Docs"
description: "Använd Azure Functions för att skapa en funktion utan server som startas av en GitHub-webhook."
services: azure-functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 36ef34b8-3729-4940-86d2-cb8e176fcc06
ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/18/2017
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: d4354546f3342d65353a86a4cec7d02547ab92e7
ms.lasthandoff: 04/22/2017


---
# <a name="create-a-function-triggered-by-a-github-webhook"></a>Skapa en funktion som utlöses av en GitHub-webhook

Läs om hur du skapar en funktion som utlöses av en GitHub-webhook. 

![Skapa en funktionsapp i Azure Portal](./media/functions-create-github-webhook-triggered-function/function-app-in-portal-editor.png)

För det här avsnittet krävs de resurser som skapades i avsnittet [Create your first function from the Azure portal](functions-create-first-azure-function.md) (Skapa din första funktion i Azure Portal).

Du behöver också ett GitHub-konto. Du kan [registrera dig för ett kostnadsfritt GitHub-konto](https://github.com/join) om du inte redan har ett. 

Det bör ta mindre än fem minuter att slutföra alla steg i det här avsnittet.

## <a name="find-your-function-app"></a>Hitta din funktionsapp    

1. Logga in på [Azure-portalen](https://portal.azure.com/). 

2. I sökfältet längst upp i portalen skriver du namnet på din funktionsapp. Välj den sedan i listan.

## <a name="create-function"></a>Skapa en funktion som utlöses med en GitHub-webhook

1. I funktionsappen klickar du på knappen **+** bredvid **Funktioner**. Klicka sedan på **GitHubWebHook**-mallen för ditt språk och på **Skapa**.
   
    ![Skapa en funktion som utlöses med en GitHub-webhook i Azure Portal.](./media/functions-create-github-webhook-triggered-function/functions-create-github-webhook-trigger.png) 

2. Klicka på **</> Get function URL** (Hämta URL för funktion) och kopiera och spara värdena. Gör samma sak för **</> Get GitHub secret** (Hämta hemlighet för GitHub). Du behöver dessa värden när du konfigurerar webhooken i GitHub. 

    ![Granska funktionskoden](./media/functions-create-github-webhook-triggered-function/functions-copy-function-url-github-secret.png) 
         
Skapa sedan en webhook i GitHub-databasen. 

## <a name="configure-the-webhook"></a>Konfigurera webhooken
1. Navigera till en databas du äger i GitHub. Du kan också använda en databas som du har förgrenat.
 
2. Klicka på **Settings** (Inställningar), klicka på **Webhooks** och klicka sedan på **Add webhook**(Lägg till webhook).
   
    ![Lägga till en GitHub-webhook](./media/functions-create-github-webhook-triggered-function/functions-create-new-github-webhook-2.png)

3. Klistra in funktionens URL-adress och hemlighet till **Payload URL** (URL för nyttolast) och **Hemlighet** och välj **application/json** som **Innehållstyp**.

4. Klicka på **Let me select individual events** (Låt mig välja individuella händelser), välj **Ärendekommentar** och klicka på **Add webhook** (Lägg till webhook).
   
    ![Ställa in webhooksadressen och hemligheten](./media/functions-create-github-webhook-triggered-function/functions-create-new-github-webhook-3.png)

Nu har webhooken konfigurerats för att utlösa din funktion när en ny ärendekommentar läggs till. 

## <a name="test-the-function"></a>Testa funktionen
1. I din GitHub-databas öppnar du fliken **Issues** (Problem) i ett nytt webbläsarfönster.

2. I det nya fönstret klickar du på **Nytt ärende**, skriver en titel och klickar på **Submit new issue** (Skicka nytt ärende). 

2. Skriv en kommentar i ärendet och klicka på **Kommentar**. 

3. I det andra GitHub-fönstret klickar du på **Redigera** bredvid din nya webhook. Bläddra sedan ned till **Senaste leveranser** och verifiera att en webhook-begäran har bearbetats av din funktion. 
 
    ![Ställa in webhooksadressen och hemligheten](./media/functions-create-github-webhook-triggered-function/functions-github-webhook-triggered.png)

   Svaret från din funktion ska innehålla `New GitHub comment: <Your issue comment text>`.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]


