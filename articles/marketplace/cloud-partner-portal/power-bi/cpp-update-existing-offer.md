---
title: Uppdatera ett erbjudande för Power BI app | Azure Marketplace
description: Uppdatera ett Power BI app-erbjudande efter att det har publicerats på Microsoft AppSource Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: b210a945993457fa76703528178763fdc67e876b
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2020
ms.locfileid: "82141696"
---
# <a name="update-a-power-bi-app-offer"></a>Uppdatera ett erbjudande för Power BI app

>[!Important]
>Från och med 13 april 2020 börjar vi flytta hanteringen av dina Power BI app-erbjudanden till Partner Center. Efter migreringen kommer du att skapa och hantera dina erbjudanden i Partner Center. Följ anvisningarna i [Power BI översikt över skapande av appar](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-power-bi-app-offer) för att hantera dina migrerade erbjudanden.

Den här artikeln visar hur du uppdaterar ditt Microsoft Power BI app-erbjudande i [Cloud Partner Portal](https://cloudpartner.azure.com/) och sedan publicerar om erbjudandet. Här följer några vanliga orsaker till att uppdatera ett erbjudande:

- Uppdatera appens innehåll i Power BI och hämta en installations-URL från den nyligen paketerade appen
- Så här uppdaterar du erbjudandet för Azure Marketplace-metadata (försäljning, marknadsföring eller supportinformation och till gångar)
 
Använd portalens **Jämför** -och **Historik** funktioner för att hålla reda på ändringar som dessa.

## <a name="common-update-operations"></a>Vanliga uppdaterings åtgärder

Vissa attribut för en Power BI app-erbjudande kan inte ändras efter att erbjudandet har publicerats i AppSource. Otillåtna ändringar inkluderar **erbjudande-ID** och **utgivar-ID**. Men du kan fortfarande ändra en mängd olika egenskaper. Här är några vanliga ändringar.

### <a name="update-app-content-in-power-bi"></a>Uppdatera app-innehåll i Power BI

Power BI appar uppdateras ofta regelbundet med nytt innehåll, säkerhets korrigeringar, funktioner och så vidare. När en app ändras uppdaterar du dess installations-URL genom att följa dessa steg:

1.  Logga in på [Cloud Partner Portal](https://cloudpartner.azure.com/).
2.  Under **alla erbjudanden**hittar du det erbjudande du vill uppdatera.
3.  På fliken **teknisk information** anger du en ny installations-URL.
4.  Välj **publicera** för att starta arbets flödet för att publicera den nya program versionen till AppSource.


### <a name="update-the-offers-marketplace-metadata"></a>Uppdatera erbjudandets Marketplace-metadata

Ditt erbjudandes Marketplace-metadata innehåller företagets namn, logo typer och annan information. Följ dessa steg om du vill uppdatera metadata:

1.  Logga in på [Cloud Partner Portal](https://cloudpartner.azure.com/).
2.  Under **alla erbjudanden**hittar du det erbjudande du vill uppdatera.
3.  På fliken **information om butik** ändrar du metadata genom att följa anvisningarna i artikeln [Power BI information om app butik](./cpp-storefront-details-tab.md).
4.  Klicka på **publicera** för att starta arbets flödet för att publicera ändringarna.


## <a name="the-compare-feature"></a>Jämförelse funktionen

När du uppdaterar ett publicerat erbjudande kan du kontrol lera de ändringar du har gjort. Så här använder du funktionen **compare** :

1.  När som helst i redigerings processen väljer du knappen **Jämför** .

    ![Knappen Jämför](./media/compare-feature-button.png)

2.  Visa sida-vid-sida-versioner av marknadsförings till gångar och metadata.


## <a name="history-of-publishing-actions"></a>Historik för publicerings åtgärder

Om du vill visa publicerings historiken för ditt erbjudande öppnar du fliken **Historik** på vänster sida av Cloud Partner Portal. Här ser du en historik över tidsstämplade åtgärder på dina AppSource-erbjudanden.

## <a name="next-steps"></a>Nästa steg

I Cloud Partner Portal kan du regelbundet använda [säljare insikter](../../cloud-partner-portal-orig/si-getting-started.md) för att hitta värdefull information om dina Marketplace-kunder och-användning.  
