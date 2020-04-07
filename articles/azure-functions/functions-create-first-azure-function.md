---
title: Skapa din första funktion i Azure Portal
description: Lär dig hur du skapar din första Azure-funktion för serverfri körning i Azure Portal.
ms.assetid: 96cf87b9-8db6-41a8-863a-abb828e3d06d
ms.topic: how-to
ms.date: 03/26/2020
ms.custom: mvc, devcenter, cc996988-fb4f-47
ms.openlocfilehash: a96d2ede80b4c57e7b85048379a4bfb66cacfd52
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754858"
---
# <a name="create-your-first-function-in-the-azure-portal"></a>Skapa din första funktion i Azure Portal

Med Azure Functions kan du köra koden i en serverlös miljö utan att först behöva skapa en virtuell dator (VM) eller publicera ett webbprogram. I den här artikeln får du lära dig hur du använder Azure Functions för att skapa en HTTP-utlösad "hello world"-funktion i Azure-portalen.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Om du är C#-utvecklare kan du [överväga att skapa din första funktion i Visual Studio 2019](functions-create-your-first-function-visual-studio.md) i stället för i portalen. 

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com) med ditt Azure-konto.

## <a name="create-a-function-app"></a>Skapa en funktionsapp

Du måste ha en funktionsapp som värd för körning av dina funktioner. Med en funktionsapp kan du gruppera funktioner som en logisk enhet för enklare hantering, distribution, skalning och delning av resurser.

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Skapa sedan en funktion i den nya funktionsappen.

## <a name="create-an-http-triggered-function"></a><a name="create-function"></a>Skapa en HTTP-utlöst funktion

1. Expandera din nya funktionsapp, **+** välj knappen bredvid **Funktioner,** välj **I-portalen**och välj sedan **Fortsätt**.

    ![Funktioner quickstart för att välja en plattform.](./media/functions-create-first-azure-function/function-app-quickstart-choose-portal.png)

1. Välj **WebHook + API**och välj sedan **Skapa**.

    ![Snabbstart för funktioner i Azure Portal.](./media/functions-create-first-azure-function/function-app-quickstart-node-webhook.png)

   En funktion skapas med hjälp av en språkspecifik mall för en HTTP-utlöst funktion.

Nu kan du köra den nya funktionen genom att skicka en HTTP-begäran.

## <a name="test-the-function"></a>Testa funktionen

1. I den nya funktionen väljer du **</> Hämta funktionsadress** längst upp till höger. 

1. I dialogrutan **Hämta funktions-URL** väljer du **standard (funktionstangent)** i listrutan och väljer sedan **Kopiera**. 

    ![Kopiera funktionswebbadressen från Azure Portal](./media/functions-create-first-azure-function/function-app-develop-tab-testing.png)

1. Klistra in funktionens URL i adressfältet för din webbläsare. Lägg till frågesträngvärdet `&name=<your_name>` i slutet av den här URL:en och tryck på Retur för att köra begäran. 

    I följande exempel visas svaret i webbläsaren:

    ![Funktionssvar i webbläsaren.](./media/functions-create-first-azure-function/function-app-browser-testing.png)

    Begäransadressen innehåller en nyckel som krävs för åtkomst till din funktion över HTTP.

1. När din funktion körs skrivs spårningsinformation till loggarna. Om du vill visa spårningsutdata från föregående körning går du tillbaka till funktionen i portalen och väljer pilen längst ned på skärmen för att expandera **loggarna**.

   ![Funktionsloggvisning i Azure Portal.](./media/functions-create-first-azure-function/function-view-logs.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

