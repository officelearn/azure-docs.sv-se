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
ms.date: 05/31/2017
ms.author: glenga
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 07584294e4ae592a026c0d5890686eaf0b99431f
ms.openlocfilehash: accd5c55e7adafd5a387bf420660b808335192e6
ms.contentlocale: sv-se
ms.lasthandoff: 06/01/2017

---
# <a name="create-a-function-triggered-by-a-github-webhook"></a>Skapa en funktion som utlöses av en GitHub-webhook

Lär dig hur du skapar en funktion som utlöses av en HTTP-webhookbegäran med en GitHub-specifik nyttolast.

![Github-webhookutlöst funktion i Azure Portal](./media/functions-create-github-webhook-triggered-function/function-app-in-portal-editor.png)

## <a name="prerequisites"></a>Krav

+ Ett GitHub-konto med minst ett projekt.
+ En Azure-prenumeration. Om du inte har ett konto kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)]

## <a name="create-an-azure-function-app"></a>Skapa en Azure Functions-app

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

![Funktionsappen skapades.](./media/functions-create-first-azure-function/function-app-create-success.png)

Därefter skapar du en funktion i den nya funktionsappen.

<a name="create-function"></a>

## <a name="create-a-github-webhook-triggered-function"></a>Skapa en webhook-utlöst GitHub-funktion

1. Expandera funktionsappen och klicka på knappen **+** bredvid **Funktioner**. Om det är den första funktionen i din funktionsapp väljer du **Anpassad funktion**. Detta visar en fullständig uppsättning med funktionsmallar.

    ![Sidan snabbstart för funktioner i Azure Portal](./media/functions-create-github-webhook-triggered-function/add-first-function.png)

2. Välj **GitHubWebHook**-mallen för språket. **Namnge funktionen** och klicka på **Skapa**.

     ![Skapa en funktion som utlöses med en GitHub-webhook i Azure Portal](./media/functions-create-github-webhook-triggered-function/functions-create-github-webhook-trigger.png) 

3. Klicka på **</> Hämta funktionswebbadress** och kopiera och spara värdena. Gör samma sak för **</> Hämta GitHub-hemlighet**. Du behöver dessa värden när du konfigurerar webhooken i GitHub.

    ![Granska funktionskoden](./media/functions-create-github-webhook-triggered-function/functions-copy-function-url-github-secret.png)

Skapa sedan en webhook i GitHub-lagringsplatsen.

## <a name="configure-the-webhook"></a>Konfigurera webhooken

1. Navigera till en lagringsplats du äger i GitHub. Du kan också använda en lagringsplats du har förgrenat. Om du behöver förgrena en lagringsplats använder du <https://github.com/Azure-Samples/functions-quickstart>.

1. Klicka på **Inställningar**, klicka på **Webhooks** och klicka sedan på **Lägg till webhook**.

    ![Lägga till en GitHub-webhook](./media/functions-create-github-webhook-triggered-function/functions-create-new-github-webhook-2.png)

1. Använd inställningarna i tabellen och klicka på **Lägg till webhook**.

    ![Ställa in webhooksadressen och hemligheten](./media/functions-create-github-webhook-triggered-function/functions-create-new-github-webhook-3.png)

| Inställning | Föreslaget värde | Beskrivning |
|---|---|---|
| **Payload URL** (Webbadress för nyttolast) | Kopierat värde | Använd värdet som returnerades med **</> Hämta funktionswebbadress**. |
| **Hemlighet**   | Kopierat värde | Använd värdet som returnerades med **</> Hämta GitHub-hemlighet**. |
| **Innehållstyp** | application/json | Funktionen förväntar sig en JSON-nyttolast. |
| Händelseutlösare | Låt mig välja enskilda händelser | Vi vill bara utlösa för händelser med ärendekommentarer.  |
| | Ärendekommentar |  |

Nu har webhooken konfigurerats för att utlösa din funktion när en ny ärendekommentar läggs till.

## <a name="test-the-function"></a>Testa funktionen

1. I din GitHub-lagringsplats öppnar du fliken **Ärenden** i ett nytt webbläsarfönster.

1. I det nya fönstret klickar du på **Nytt ärende**, skriver en titel och klickar på **Submit new issue** (Skicka nytt ärende).

1. Skriv en kommentar i ärendet och klicka på **Kommentar**.

    ![Lägg till en GitHub-ärendekommentar.](./media/functions-create-github-webhook-triggered-function/functions-github-webhook-add-comment.png)

1. Gå tillbaka till portalen och öppna loggarna. Du bör se en spårningspost med den nya kommentartexten.

     ![Se kommentartexten i loggarna.](./media/functions-create-github-webhook-triggered-function/function-app-view-logs.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

Du har skapat en funktion som körs när en begäran tas emot från en GitHub-webhook. 
[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)] Mer information om webhookutlösare finns i [Azure Functions HTTP and webhook bindings](functions-bindings-http-webhook.md) (HTTP- och webhookbindningar i Azure Functions).
