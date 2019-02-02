---
title: Uppdatera ett befintligt Power BI-appen erbjudande – Azure Marketplace | Microsoft Docs
description: Uppdatera ett erbjudande med Power BI-appen när den har publicerats på Microsoft AppSource Marketplace.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: pbutlerm
ms.openlocfilehash: 635e2e71bb952aaee761df6a1d5d87c46db531f6
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2019
ms.locfileid: "55666711"
---
# <a name="update-an-existing-power-bi-app-offer"></a>Uppdatera ett befintligt erbjudande för Power BI-appen

Den här artikeln vägleder dig genom de olika delarna för att uppdatera erbjudandet Power BI-appen i den [Cloud Partner Portal](https://cloudpartner.azure.com/) och sedan publicera erbjudandet.  Det finns vanliga orsaker till att du kan uppdatera ditt erbjudande, inklusive:

- Uppdatera appens innehåll i Power BI och får en ny installera URL från nyligen paketerad app
- Uppdaterar marketplace-metadata för erbjudandet: försäljning, marknadsföring, eller supportinformation och tillgångar
 
För att hjälpa dig på de här ändringarna, portalen erbjuder den **jämför** och **historik** funktioner.


## <a name="unpermitted-changes-to-offer"></a>Unpermitted ändringar att erbjuda

Det finns vissa attribut för ett erbjudande för Power BI-appen som inte kan ändras när erbjudandet är aktiv i AppSource, främst **erbjudande-ID** och **Publicerings-ID**.


## <a name="common-update-operations"></a>Vanliga åtgärder för uppdatering

Även om det finns en mängd olika egenskaper som du kan ändra på ett erbjudande för Power BI-appen, är följande åtgärder vanliga.


### <a name="update-app-content-in-power-bi"></a>Uppdatera appinnehåll i Power BI

Det är vanligt för appen i Power BI uppdateras regelbundet med nytt innehåll, säkerhetsuppdateringar, extrafunktioner och så vidare. I sådana fall som du vill uppdatera URL: en till den nya appar innehåll installationen med hjälp av följande steg:

1.  Logga in på den [Cloud Partner Portal](https://cloudpartner.azure.com/).
2.  Under **alla erbjudanden**, hitta erbjudandet att uppdatera.
3.  I den **teknisk information** ange en ny installer-URL.
4.  Klicka på **publicera** att starta arbetsflödet för att publicera din nya app-versionen på AppSource.


### <a name="update-offer-marketplace-metadata"></a>Metadata för marketplace-erbjudande

Använd följande steg för att uppdatera metadata för marketplace, företagsnamn, logotyper, etc., som är associerade med ditt erbjudande:

1.  Logga in på den [Cloud Partner Portal](https://cloudpartner.azure.com/).
2.  Under **alla erbjudanden**, erbjudandet som du vill uppdatera.
3.  Gå till den **Storefront information** fliken och följ sedan instruktionerna i den [fliken Power BI-appar Storefront information](./cpp-storefront-details-tab.md) att göra ändringar i metadata.
4.  Klicka på **publicera** att starta arbetsflödet för att publicera dina ändringar.


## <a name="compare-feature"></a>Jämför funktioner

När du gör ändringar på ett redan publicerat erbjudande, kan du använda den **jämför** funktionen för att granska de ändringar som har gjorts. Använda den här funktionen:

1.  När som helst i redigeringsprocessen klickar du på den **jämför** knappen för ditt erbjudande.

    ![Jämför funktionen knappen](./media/compare-feature-button.png)

2.  Visa sida-vid-sida-versioner av marknadsföring tillgångar och metadata.


## <a name="history-of-publishing-actions"></a>Historik för publiceringen av åtgärder

Om du vill visa alla historiska publiceringsaktivitet, klickar du på den **historik** flik i den vänstra menyraden för partnerportalen i molnet. Här kommer du att kunna visa tidsstämplad åtgärder som har vidtagits under livslängden för dina AppSource-erbjudanden.


## <a name="next-steps"></a>Nästa steg

Du bör regelbundet använder den [försäljning Insights](../../cloud-partner-portal-orig/si-getting-started.md) funktion i den [partnerportalen i molnet](https://cloudpartner.azure.com/#insights) att ge insikter om din marketplace för kunder och användning.  
