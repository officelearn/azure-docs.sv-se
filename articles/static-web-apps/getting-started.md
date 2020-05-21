---
title: 'Snabb start: skapa din första statiska webbapp med Azures statiska Web Apps'
description: Lär dig att bygga en Azure static Web Apps-instans med ditt prioriterade klient dels ramverk.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 8de6e9cff8149423f4b00b07e3113be6606181b5
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2020
ms.locfileid: "83714244"
---
# <a name="quickstart-building-your-first-static-web-app"></a>Snabb start: skapa din första statiska webbapp

Azures statiska Web Apps publicerar webbplatser till en produktions miljö genom att bygga appar från en GitHub-lagringsplats. I den här snabb starten skapar du ett webb program med hjälp av det önskade front-end-ramverket från en GitHub-lagringsplats.

Om du inte har en Azure-prenumeration kan du [skapa ett kostnads fritt utvärderings konto](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Krav

- [GitHub](https://github.com) -konto
- [Azure](https://portal.azure.com) -konto

## <a name="create-a-repository"></a>Skapa en lagrings plats

Den här artikeln använder GitHub för att göra det enkelt för dig att skapa en ny lagrings plats. Funktionerna för mallar startappar har skapats med olika klient dels ramverk.

# <a name="angular"></a>[Angular](#tab/angular)

- Gå till följande plats för att skapa en ny lagrings plats
  - https://github.com/staticwebdev/angular-basic/generate
- Namnge din lagrings plats **först statisk-webb-app**

# <a name="react"></a>[Reagera](#tab/react)

- Gå till följande plats för att skapa en ny lagrings plats
  - https://github.com/staticwebdev/react-basic/generate
- Namnge din lagrings plats **först statisk-webb-app**

# <a name="vue"></a>[Vue](#tab/vue)

- Gå till följande plats för att skapa en ny lagrings plats
  - https://github.com/staticwebdev/vue-basic/generate
- Namnge din lagrings plats **först statisk-webb-app**

# <a name="no-framework"></a>[Inget ramverk](#tab/vanilla-javascript)

- Gå till följande plats för att skapa en ny lagrings plats
  - https://github.com/staticwebdev/vanilla-basic/generate
- Namnge din lagrings plats **först statisk-webb-app**

> [!NOTE]
> Azures statiska Web Apps kräver minst en HTML-fil för att skapa en webbapp. Lagrings platsen som du skapar i det här steget innehåller en enskild _index. html-_ fil.

---

Klicka på knappen **skapa databas från mall** .

:::image type="content" source="media/getting-started/create-template.png" alt-text="Skapa lagrings plats från mall":::

## <a name="create-a-static-web-app"></a>Skapa en statisk webbapp

Nu när du har skapat databasen kan du skapa en statisk webbapp från Azure Portal.

- Navigera till [Azure Portal](https://portal.azure.com)
- Klicka på **skapa en resurs**
- Sök efter **statiska Web Apps**
- Klicka på **statisk Web Apps (förhands granskning)**
- Klicka på **skapa**

### <a name="basics"></a>Grundläggande inställningar

Börja med att konfigurera din nya app och länka den till en GitHub-lagringsplats.

:::image type="content" source="media/getting-started/basics-tab.png" alt-text="Fliken Grundläggande":::

- Välj din _Azure-prenumeration_
- Välj eller skapa en ny _resurs grupp_
- Namnge appen **mitt-första-statiskt-Web-App**.
  - Giltiga tecken är `a-z` (Skift läges okänsligt), `0-9` och `-` .
- Välj en _region_ som är närmast dig
- Välj den **kostnads fria** _SKU: n_
- Klicka på knappen **Logga in med GitHub** och autentisera med GitHub

När du har loggat in med GitHub anger du lagrings plats informationen.

:::image type="content" source="media/getting-started/repository-details.png" alt-text="Lagrings information":::

- Välj önskad _organisation_
- Välj **min-första-webb-statisk-app** från List rutan _databas_
- Välj **original** i list rutan _gren_
- Klicka på **Nästa: bygg >** om du vill redigera build-konfigurationen

:::image type="content" source="media/getting-started/next-build-button.png" alt-text="Nästa build-knapp":::

### <a name="build"></a>Utveckla

Lägg sedan till konfigurations information som är speciell för ditt prioriterade front-end-ramverk.

# <a name="angular"></a>[Angular](#tab/angular)

- Ange **/** i rutan _app location_
- Rensa standardvärdet från rutan _API-plats_
- Ange **förd/vinkel-Basic** i rutan _app artefakt plats_

# <a name="react"></a>[Reagera](#tab/react)

- Ange **/** i rutan _app location_
- Rensa standardvärdet från rutan _API-plats_
- Ange **build** i rutan _plats för app-artefakt_

# <a name="vue"></a>[Vue](#tab/vue)

- Ange **/** i rutan _app location_
- Rensa standardvärdet från rutan _API-plats_
- Ange **Dist** i rutan _plats för app-artefakt_

# <a name="no-framework"></a>[Inget ramverk](#tab/vanilla-javascript)

- Ange **/** i rutan _app location_
- Rensa standardvärdet från rutan _API-plats_
- Rensa rutan Standardvärde från _appens artefakt plats_

---

Klicka på knappen **Granska + skapa**.

:::image type="content" source="media/getting-started/review-create.png" alt-text="Knappen granska skapande":::

Om du vill ändra dessa värden när du har skapat appen kan du redigera [arbets flödes filen](github-actions-workflow.md).

### <a name="review--create"></a>Granska + skapa

När begäran har validerats kan du fortsätta att skapa programmet.

Klicka på knappen **skapa**

:::image type="content" source="media/getting-started/create-button.png" alt-text="Knappen Skapa":::

När resursen har skapats klickar du på knappen **gå till resurs**

:::image type="content" source="media/getting-started/resource-button.png" alt-text="Knappen gå till resurs":::

## <a name="view-the-website"></a>Visa webbplatsen

Det finns två aspekter av att distribuera en statisk app. De första etablerar de underliggande Azure-resurserna som utgör din app. Det andra är ett arbets flöde för GitHub åtgärder som skapar och publicerar ditt program.

Innan du kan navigera till den nya statiska platsen måste distributions versionen först slutföras.

I fönstret Översikt över statisk Web Apps visas en serie länkar som hjälper dig att interagera med din webbapp.

:::image type="content" source="media/getting-started/overview-window.png" alt-text="Översikts fönster":::

1. Klicka på banderollen som säger "Klicka här om du vill kontrol lera status för dina GitHub-åtgärder körs" tar dig till de GitHub åtgärder som körs mot din lagrings plats. När du har kontrollerat att distributions jobbet är klart kan du gå till webbplatsen via den genererade URL: en.

2. När GitHub-åtgärds arbets flödet är klart kan du klicka på _URL_ -länken för att öppna webbplatsen på ny flik.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta att använda det här programmet kan du ta bort den statiska Azure-Web Apps-instansen genom följande steg:

1. Öppna [Azure Portal](https://portal.azure.com)
1. Sök efter **min-First-Web-static-app** från det övre Sök fältet
1. Klicka på appens namn
1. Klicka på knappen **ta bort**
1. Bekräfta borttagnings åtgärden genom att klicka på **Ja**

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lägga till ett API](add-api.md)
