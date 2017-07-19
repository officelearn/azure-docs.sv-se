---
title: "Skapa din första funktion i Azure Portal | Microsoft Docs"
description: "Lär dig hur du skapar din första Azure-funktion för serverfri körning i Azure Portal."
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 96cf87b9-8db6-41a8-863a-abb828e3d06d
ms.service: functions
ms.devlang: multiple
ms.topic: hero-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 06/08/2017
ms.author: glenga
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 245ce9261332a3d36a36968f7c9dbc4611a019b2
ms.openlocfilehash: f00ca3b8a35c0c49277457bd42fe8a314520d5a5
ms.contentlocale: sv-se
ms.lasthandoff: 06/09/2017


---
# <a name="create-your-first-function-in-the-azure-portal"></a>Skapa din första funktion i Azure Portal

Med Azure Functions kan du köra kod i en serverfri miljö utan att först behöva skapa en virtuell dator eller publicera en webbapp. I det här ämnet får du lära dig att använda Functions till att skapa en ”hello world”-funktion i Azure Portal.

![Skapa en funktionsapp i Azure Portal](./media/functions-create-first-azure-function/function-app-in-portal-editor.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="create-a-function-app"></a>Skapa en funktionsapp

Du måste ha en funktionsapp som värd för körning av dina funktioner. I en funktionsapp kan du gruppera funktioner som en logisk enhet så att det blir enklare att hantera, distribuera och dela resurser. 

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

![Funktionsappen skapades.](./media/functions-create-first-azure-function/function-app-create-success.png)

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)]

Därefter skapar du en funktion i den nya funktionsappen.

## <a name="create-function"></a>Skapa en HTTP-utlöst funktion

1. Expandera den nya funktionsappen och klicka på knappen **+** bredvid **Funktioner**.

2.  På sidan **Kom igång snabbt** väljer du **WebHook + API**. Välj ett språk för funktionen och klicka på **Skapa den här funktionen**. 
   
    ![Snabbstart för funktioner i Azure Portal.](./media/functions-create-first-azure-function/function-app-quickstart-node-webhook.png)

En funktion skapas i ditt valda språk med hjälp av mallen för en HTTP-utlöst funktion. Du kan köra den nya funktionen genom att skicka en HTTP-begäran.

## <a name="test-the-function"></a>Testa funktionen

1. Klicka på den nya funktionen **</> Hämta funktions-URL**, välj **Standard (funktionsnyckel)** och klicka sedan på **Kopiera**. 

    ![Kopiera funktionswebbadressen från Azure Portal](./media/functions-create-first-azure-function/function-app-develop-tab-testing.png)

2. Klistra in webbadressen för HTTP-begäran i webbläsarens adressfält. Lägg till frågesträngen `&name=<yourname>` i webbadressen och kör din begäran. Nedan visas svaret på GET-begäran som returnerades av funktionen i webbläsaren:

    ![Funktionssvar i webbläsaren.](./media/functions-create-first-azure-function/function-app-browser-testing.png)

    Begäransadressen innehåller en nyckel som krävs för åtkomst till din funktion över HTTP.   

## <a name="view-the-function-logs"></a>Visa funktionsloggarna 

När din funktion körs skrivs spårningsinformation till loggarna. Om du vill visa spårningsinformationen från föregående körning återgår du till funktionen i portalen och klickar på uppåtpilen längst ned på skärmen så att **Loggar** expanderas. 

![Funktionsloggvisning i Azure Portal.](./media/functions-create-first-azure-function/function-view-logs.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [Clean up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

Du har nu skapat en funktionsapp med en enkel HTTP-utlöst funktion.  

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Mer information finns i [Azure Functions HTTP and webhook bindings](functions-bindings-http-webhook.md) (HTTP- och webhookbindningar i Azure Functions).




