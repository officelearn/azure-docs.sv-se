---
title: Skapa din första funktion i Azure Portal
description: Lär dig hur du skapar din första Azure-funktion för serverfri körning i Azure Portal.
ms.assetid: 96cf87b9-8db6-41a8-863a-abb828e3d06d
ms.topic: quickstart
ms.date: 03/26/2020
ms.custom: mvc, devcenter, cc996988-fb4f-47
ms.openlocfilehash: 417ad96dc3dea25e322dbdb4d81c034a9b9c1e80
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656892"
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

1. Välj **Funktioner**i det vänstra menyn i fönstret **Funktioner** och välj sedan **Lägg till** på den övre menyn. 
 
1. Välj **Http-utlösare**i fönstret **Ny funktion** .

    ![Välj HTTP-utlösarfunktion](./media/functions-create-first-azure-function/function-app-select-http-trigger.png)

1. I fönstret **Ny funktion** accepterar du standardnamnet för **Ny funktion**eller anger ett nytt namn. 

1. Välj **Anonym** i listrutan **Auktoriseringsnivå** och välj sedan **Skapa funktion**.

    Azure skapar HTTP-utlösarfunktionen. Nu kan du köra den nya funktionen genom att skicka en HTTP-begäran.

## <a name="test-the-function"></a>Testa funktionen

1. I den nya HTTP-utlösarfunktionen väljer du **Kod + Test** på den vänstra menyn och väljer sedan Hämta **funktions-URL** på den övre menyn.

    ![Välj URL för funktionen Hämta](./media/functions-create-first-azure-function/function-app-select-get-function-url.png)

1. I dialogrutan **Hämta funktions-URL** väljer du **standard** i listrutan och väljer sedan ikonen **Kopiera till Urklipp.** 

    ![Kopiera funktionswebbadressen från Azure Portal](./media/functions-create-first-azure-function/function-app-develop-tab-testing.png)

1. Klistra in funktionens URL i adressfältet för din webbläsare. Lägg till frågesträngvärdet `?name=<your_name>` i slutet av den här URL:en och tryck på Retur för att köra begäran. 

    I följande exempel visas svaret i webbläsaren:

    ![Funktionssvar i webbläsaren.](./media/functions-create-first-azure-function/function-app-browser-testing.png)

    Begäransadressen innehåller en nyckel som krävs för åtkomst till din funktion över HTTP.

1. När din funktion körs skrivs spårningsinformation till loggarna. Om du vill se spårningsutdata går du tillbaka till sidan **Kod + Test** i portalen och expanderar pilen **Loggar** längst ned på sidan.

   ![Funktionsloggvisning i Azure Portal.](./media/functions-create-first-azure-function/function-view-logs.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

