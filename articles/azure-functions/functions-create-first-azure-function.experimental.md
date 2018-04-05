---
title: Skapa din första funktion i Azure Portal | Microsoft Docs
description: Lär dig hur du skapar din första Azure-funktion för serverfri körning i Azure Portal.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
ms.assetid: 96cf87b9-8db6-41a8-863a-abb828e3d06d
ms.service: functions
ms.devlang: multiple
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/05/2018
ms.author: glenga
ms.custom: mvc, devcenter
ms.openlocfilehash: 31437360396e220de24789fdff9d2602af385053
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2018
---
# <a name="create-your-first-function-in-the-azure-portal"></a>Skapa din första funktion i Azure Portal

Med Azure Functions kan du köra kod i en [serverfri](https://azure.microsoft.com/overview/serverless-computing/) miljö utan att först behöva skapa en virtuell dator eller publicera en webbapp. I det här ämnet får du lära dig att använda Functions till att skapa en ”hello world”-funktion i Azure Portal.

![Skapa en funktionsapp i Azure Portal](./media/functions-create-first-azure-function/function-app-in-portal-editor.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure-portalen på <http://portal.azure.com> med ditt Azure-konto.

## <a name="create-a-function-app"></a>Skapa en funktionsapp

Du måste ha en funktionsapp som värd för körning av dina funktioner. I en funktionsapp kan du gruppera funktioner som en logisk enhet så att det blir enklare att hantera, distribuera och dela resurser. 

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal-experiment.md)]

Därefter skapar du en funktion i den nya funktionsappen.

## <a name="create-function"></a>Skapa en HTTP-utlöst funktion

1. Expandera den nya funktionsappen och klicka på knappen **+** bredvid **Funktioner**.

2.  På sidan **Kom igång snabbt** väljer du **WebHook + API**. **Välj ett språk** för funktionen och klicka på **Skapa den här funktionen**. 
   
    ![Snabbstart för funktioner i Azure Portal.](./media/functions-create-first-azure-function/function-app-quickstart-node-webhook.png)

En funktion skapas i ditt valda språk med hjälp av mallen för en HTTP-utlöst funktion. Det här avsnittet visar en C#-skriptfunktion i portalen, men du kan skapa en funktion i alla [språk som stöds](supported-languages.md). 

Nu kan du köra den nya funktionen genom att skicka en HTTP-begäran.

## <a name="test-the-function"></a>Testa funktionen

1. I den nya funktionen klickar du på **</> Hämta funktionswebbadress** längst upp till höger och väljer **Standard (funktionsnyckel)**. Sedan klickar du på **Kopiera**. 

    ![Kopiera funktionswebbadressen från Azure Portal](./media/functions-create-first-azure-function/function-app-develop-tab-testing.png)

2. Klistra in funktionens URL i adressfältet för din webbläsare. Lägg till frågesträngvärdet `&name=<yourname>` i slutet av den här webbadressen och tryck på knappen `Enter` på tangentbordet för att utföra begäran. Du bör se svaret som returnerades av funktionen som visas i webbläsaren.  

    Det här exemplet visar svaret i webbläsaren Edge (andra webbläsare kan även visa XML):

    ![Funktionssvar i webbläsaren.](./media/functions-create-first-azure-function/function-app-browser-testing.png)

    Begäransadressen innehåller en nyckel som krävs för åtkomst till din funktion över HTTP.   

3. När din funktion körs skrivs spårningsinformation till loggarna. Om du vill visa spårningsinformationen från föregående körning återgår du till funktionen i portalen och klickar på pilen längst ned på skärmen så att **Loggar** expanderas. 

   ![Funktionsloggvisning i Azure Portal.](./media/functions-create-first-azure-function/function-view-logs.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

Du har nu skapat en funktionsapp med en enkel HTTP-utlöst funktion.  

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Mer information finns i [Azure Functions HTTP and webhook bindings](functions-bindings-http-webhook.md) (HTTP- och webhookbindningar i Azure Functions).



