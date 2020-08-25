---
title: 'Snabb start: skapa din första statiska webbapp med Azures statiska Web Apps med hjälp av Azure Portal'
description: Lär dig att bygga en statisk Azure Web Apps-instans med Azure Portal.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 08/13/2020
ms.author: cshoe
ms.openlocfilehash: e0b78c5e053c5668fbebd8ebaac91a90aa2b364f
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2020
ms.locfileid: "88752929"
---
# <a name="quickstart-building-your-first-static-web-app-in-the-azure-portal"></a>Snabb start: skapa din första statiska webbapp i Azure Portal

Azures statiska Web Apps publicerar en webbplats i en produktions miljö genom att bygga appar från en GitHub-lagringsplats. I den här snabb starten distribuerar du ett webb program till Azures statiska webb program med hjälp av portalen.

Om du inte har en Azure-prenumeration kan du [skapa ett kostnads fritt utvärderings konto](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Förutsättningar

- [GitHub](https://github.com) -konto
- [Azure](https://portal.azure.com) -konto

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

## <a name="create-a-static-web-app"></a>Skapa en statisk webbapp

Nu när du har skapat databasen kan du skapa en statisk webbapp från Azure Portal.

1. Navigera till [Azure Portal](https://portal.azure.com)
1. Välj **skapa en resurs**
1. Sök efter **Statiska webbappar**
1. Välj **statisk Web Apps (förhands granskning)**
1. Välj **Skapa**

På fliken _grundläggande_ börjar du med att konfigurera din nya app och länka den till en GitHub-lagringsplats.

:::image type="content" source="media/getting-started-portal/basics-tab.png" alt-text="Fliken Grundläggande":::

1. Välj din _Azure-prenumeration_
1. Välj eller skapa en ny _resurs grupp_
1. Namnge appen **mitt-första-statiskt-Web-App**.
      1. Giltiga tecken är `a-z` (skiftlägesokänsligt), `0-9` och `-`.
1. Välj en _region_ som är närmast dig
1. Välj den **kostnads fria** _SKU: n_
1. Välj knappen **Logga in med GitHub** och autentisera med GitHub

När du har loggat in med GitHub anger du lagrings plats informationen.

:::image type="content" source="media/getting-started-portal/repository-details.png" alt-text="Lagrings information":::

1. Välj önskad _organisation_
1. Välj **min-första-webb-statisk-app** från List rutan _databas_
1. Välj **original** i list rutan _gren_
1. Välj **Nästa: build >** -knappen för att redigera build-konfigurationen

:::image type="content" source="media/getting-started-portal/next-build-button.png" alt-text="Nästa build-knapp":::

> [!NOTE]
> Om du inte ser några databaser kan du behöva auktorisera Azures statiska Web Apps i GitHub. Bläddra till GitHub-lagringsplatsen och gå till **inställningar > program > auktoriserade OAuth-appar**, Välj **Azure static Web Apps**och välj sedan **bevilja**. Du måste vara ägare till organisationen för att kunna bevilja behörighet för organisations databaser.

1. På fliken _skapa_ lägger du till konfigurations information som är unik för det önskade front-end-ramverket.

    # <a name="no-framework"></a>[Inget ramverk](#tab/vanilla-javascript)

    - Rensa standardvärdet i rutan _app location_
    - Rensa standardvärdet från rutan _API-plats_
    - Rensa rutan Standardvärde från _appens artefakt plats_

    # <a name="angular"></a>[Angular](#tab/angular)

    - Rensa standardvärdet i rutan _app location_
    - Rensa standardvärdet från rutan _API-plats_
    - Skriv **förd/vinkel-Basic** i rutan _app artefakt plats_

    # <a name="react"></a>[React](#tab/react)

    - Rensa standardvärdet i rutan _app location_
    - Rensa standardvärdet från rutan _API-plats_
    - Skriv **build** i rutan _plats för app-artefakt_

    # <a name="vue"></a>[Vue](#tab/vue)

    - Rensa standardvärdet i rutan _app location_
    - Rensa standardvärdet från rutan _API-plats_
    - Skriv **Dist** i rutan _plats för app-artefakt_

    ---

1. Välj **Granska + skapa**.

    :::image type="content" source="media/getting-started-portal/review-create.png" alt-text="Knappen granska skapande":::

    > [!NOTE]
    > Du kan redigera [arbets flödes filen](github-actions-workflow.md) om du vill ändra dessa värden när du har skapat appen.

1. Välj **Skapa**.

    :::image type="content" source="media/getting-started-portal/create-button.png" alt-text="Knappen Skapa":::

1. Välj **Gå till resurs**.

    :::image type="content" source="media/getting-started-portal/resource-button.png" alt-text="Knappen Gå till resurs":::

[!INCLUDE [view website](../../includes/static-web-apps-get-started-view-website.md)]

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta att använda det här programmet kan du ta bort den statiska Azure-Web Apps-instansen genom följande steg:

1. Öppna [Azure-portalen](https://portal.azure.com)
1. Sök efter **min-First-Web-static-app** från det övre Sök fältet
1. Välj på appens namn
1. Välj på knappen **ta bort**
1. Välj **Ja** för att bekräfta borttagnings åtgärden

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lägga till ett API](add-api.md)
