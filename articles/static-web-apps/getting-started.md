---
title: 'Snabb start: skapa din första statiska webbapp med Azures statiska Web Apps'
description: Lär dig att bygga en Azure-statisk Web Apps webbplats.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 08/13/2020
ms.author: cshoe
ms.openlocfilehash: db3836e6171d187539b8615efcb5ab782c368020
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2020
ms.locfileid: "88752647"
---
# <a name="quickstart-building-your-first-static-web-app"></a>Snabb start: skapa din första statiska webbapp

Azures statiska Web Apps publicerar en webbplats i en produktions miljö genom att bygga appar från en GitHub-lagringsplats. I den här snabb starten distribuerar du ett webb program till Azures statiska webbappar med Visual Studio Code-tillägget.

Om du inte har en Azure-prenumeration kan du [skapa ett kostnads fritt utvärderings konto](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Förutsättningar

- [GitHub](https://github.com) -konto
- [Azure](https://portal.azure.com) -konto
- [Visual Studio Code](https://code.visualstudio.com)
- [Azure static Web Apps-tillägg för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps)

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

[!INCLUDE [clone the repository](../../includes/static-web-apps-get-started-clone-repo.md)]

Öppna sedan Visual Studio Code och gå till **fil > Öppna mapp** för att öppna den lagrings plats som du precis har klonat till din dator i redigeraren.

## <a name="create-a-static-web-app"></a>Skapa en statisk webbapp

1. I Visual Studio Code väljer du Azure-logotypen i aktivitets fältet för att öppna fönstret Azure-tillägg.

    :::image type="content" source="media/getting-started/extension-azure-logo.png" alt-text="Azure-logotyp":::

    > [!NOTE]
    > Azure-och GitHub-inloggning krävs. Om du inte redan har loggat in på Azure och GitHub från Visual Studio Code uppmanas du att logga in till båda under skapandet.

1. Placera musen över den _statiska Web Apps_ etiketten och välj **plus tecknet**.

    :::image type="content" source="media/getting-started/extension-create-button.png" alt-text="Programnamn":::

1. Kommandot palett visas längst upp i redigeraren och du blir ombedd att namnge ditt program.

    Skriv in **den första statiska webb-appen** och tryck på **RETUR**.

    :::image type="content" source="media/getting-started/extension-create-app.png" alt-text="Skapa en statisk webbapp":::

1. Välj **huvud** grenen och tryck på **RETUR**.

    :::image type="content" source="media/getting-started/extension-branch.png" alt-text="Namn på gren":::

1. Välj **/** som plats för program koden och tryck på **RETUR**.

    :::image type="content" source="media/getting-started/extension-app-location.png" alt-text="Plats för program kod":::

1. Tillägget söker efter platsen för API: et i ditt program. Den här artikeln implementerar inte ett API.

    Välj **hoppa över för tillfället** och tryck på **RETUR**.

    :::image type="content" source="media/getting-started/extension-api-location.png" alt-text="API-plats":::

1. Välj den plats där filerna skapas för produktion i din app.

    # <a name="no-framework"></a>[Inget ramverk](#tab/vanilla-javascript)

    Avmarkera rutan och tryck på **RETUR**.

    :::image type="content" source="media/getting-started/extension-artifact-no-framework.png" alt-text="Sökväg till app-filer":::

    # <a name="angular"></a>[Angular](#tab/angular)

    Skriv **förd/vinkel-Basic** och tryck på **RETUR**.

    :::image type="content" source="media/getting-started/extension-artifact-angular.png" alt-text="Sökväg till vinkel app-filer":::

    # <a name="react"></a>[React](#tab/react)

    Skriv **build** och tryck på **RETUR**.

    :::image type="content" source="media/getting-started/extension-artifact-react.png" alt-text="Sökväg för att reagera på App-filer":::

    # <a name="vue"></a>[Vue](#tab/vue)

    Skriv in **Dist** och tryck på **RETUR**.

    :::image type="content" source="media/getting-started/extension-artifact-vue.png" alt-text="Sökväg till Vue-app Files":::

    ---

1. Välj en plats närmast dig och tryck på **RETUR**.

    :::image type="content" source="media/getting-started/extension-location.png" alt-text="Resursplats":::

1. När appen har skapats visas ett bekräftelse meddelande i Visual Studio Code.

    :::image type="content" source="media/getting-started/extension-confirmation.png" alt-text="Bekräftelse har skapats":::

1. I fönstret Visual Studio Code Explorer går du tillbaka till avsnittet _statisk Web Apps_ och högerklickar på **produktion** och väljer **Öppna i portalen** för att Visa appen i Azure Portal.

    :::image type="content" source="media/getting-started/extension-open-in-portal.png" alt-text="Öppna Portal":::

[!INCLUDE [view website](../../includes/static-web-apps-get-started-view-website.md)]

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta att använda det här programmet kan du ta bort den statiska Azure-Web Apps-instansen via tillägget.

I fönstret Visual Studio Code Explorer går du tillbaka till avsnittet för _statisk Web Apps_ och högerklickar på **min-första-statisk-Web-App** och väljer **ta bort**.

:::image type="content" source="media/getting-started/extension-delete.png" alt-text="Ta bort app":::

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lägga till ett API](add-api.md)
