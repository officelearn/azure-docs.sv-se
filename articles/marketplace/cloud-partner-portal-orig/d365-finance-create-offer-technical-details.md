---
title: Hur du fyller i formuläret teknisk information
description: Beskriver hur du anger du värdena för den i formuläret teknisk information för en ny Dynamics 365 Business Central-app.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: dbc38fab5bd8e55f6dd280ecc46af1b1a5ae7ede
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64935073"
---
<a name="how-to-fill-out-the-technical-info-form"></a>Hur du fyller i formuläret teknisk information
===========================================

1.  I den **väljer programtyp** avsnittet, ladda upp din tilläggsfilen (.app) och eventuella tillägg paketfilerna ditt tillägg har ett beroende på.

    ![Programpaket Information](./media/d365-financials/image015.png)

-   **Tillägg paketfilen** --krävs – tilläggsfilen (.app).

-   **Beroende paketfilen** --krävs om appen har ett beroende på en annan app som publicerats i AppSource. Den här .app-filen som används av ett redan publicerat tillägg i AppSource, där den aktuella appen är beroende av. 

-   **Biblioteket paketfilen** -krävs om appen har ett beroende på en annan app som är *inte* publiceras i AppSource. Den här .app fil av en befintlig app, men en som inte har och inte publiceras i AppSource.

-   **Testa Appautomatisering** --krävs – den VS kodade testpaket som du måste skapa för automatiska tester av tillägg.

1. I den **ytterligare Information för tillägget** avsnittet, ladda upp ytterligare information för ditt tillägg. Den här informationen används under verifieringen.

   ![Ytterligare information för tillägget Programformulär](./media/d365-financials/image016.png)


-   **URL till dokumentation av produkten** --krävs - URL till dokumentation för tillägget.

-   **Användningsscenarier för nyckeln** --krävs – ett dokument som innehåller stegvis information för installation och användning för tillägget. Ett exempel finns i artikeln [användardokumentation för scenariot](https://docs.microsoft.com/dynamics-nav/compliance/apptest-userscenario/).

-   **Rikta versionen** --krävs – Välj versionen som du vill distribuera appen. Välj **aktuella** att distribuera på aktuellt i marknaden version. Välj **bredvid mindre** att distribuera med nästa mindre version som ska frisläppas. Välj **nästa större** att distribuera med nästa större version som ska frisläppas.

-   **Kräver Premium-SKU** --valfritt--Välj Premium knappen om appen kräver Premium-SKU. Service Management och tillverkning finns endast på premium. Detaljerad information om viktiga vs Premium finns i artikeln [ändra som funktionerna visas](https://docs.microsoft.com/dynamics365/financials/ui-experiences).

-   **Förklaring till Analysis kodfel** --Nepovinné--dokument som visar en lista över och motiverar någon kod som inte uppfyller kraven.

-   **Förklaring av påverkas kärnfunktioner** --Nepovinné--dokument som anges och beskrivs några grundläggande funktioner som är begränsad av tillägget.

-   **Testkonton** --Nepovinné--användarkonton för fjärrtjänster, webbplatser och annat som krävs för att slutföra test för slutpunkt till slutpunkt-användning.

-   **UX krav undantag** --Nepovinné--dokument som visar en lista över och justerar eventuella krav för upplevelse av användare som inte uppfylls av tillägget.

Nästa steg är att lägga till butiken information för ditt erbjudande.
