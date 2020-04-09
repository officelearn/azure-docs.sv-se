---
title: Uppdatera ett Power BI-apperbjudande | Azure Marketplace
description: Uppdatera ett Power BI-apperbjudande när det har publicerats på Microsoft AppSource Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: ee8f67a41d339e6e5f65548867c5458739857d50
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80981264"
---
# <a name="update-a-power-bi-app-offer"></a>Uppdatera ett Power BI-apperbjudande

>[!Important]
>Från och med den 13 april 2020 börjar vi flytta hanteringen av dina Power BI-appar till Partner Center. Efter migreringen skapar och hanterar du dina erbjudanden i Partner center. Följ instruktionerna i [översikten över hur du skapar Power BI-appar](https://aka.ms/AzureCreatePBIServiceApp) för att hantera dina migrerade erbjudanden.

I den här artikeln beskrivs hur du uppdaterar ditt Microsoft Power BI-apperbjudande i [Cloud Partner Portal](https://cloudpartner.azure.com/) och sedan publicerar erbjudandet igen. Här är några vanliga orsaker till att uppdatera ett erbjudande:

- Så här uppdaterar du appens innehåll i Power BI och hämtar en installations-URL från den nyligen paketerade appen
- Så här uppdaterar du erbjudandets Azure Marketplace-metadata (försäljning, marknadsföring eller supportinformation och resurser)
 
Använd portalens **funktioner Jämför** och **historik** för att hålla reda på ändringar som dessa.

## <a name="common-update-operations"></a>Vanliga uppdateringsåtgärder

Vissa attribut för ett Power BI App-erbjudande kan inte ändras när erbjudandet har publicerats i AppSource. Otillåtna ändringar inkluderar **erbjudande-ID** och **Publisher-ID**. Men du kan fortfarande ändra ett brett spektrum av egenskaper. Här är några vanliga förändringar.

### <a name="update-app-content-in-power-bi"></a>Uppdatera appinnehåll i Power BI

Power BI-appar uppdateras ofta regelbundet med nytt innehåll, säkerhetskorrigeringar, funktioner och så vidare. När en app ändras uppdaterar du installations-URL:en genom att följa dessa steg:

1.  Logga in på [Molnpartnerportalen](https://cloudpartner.azure.com/).
2.  Under **Alla erbjudanden**hittar du det erbjudande du vill uppdatera.
3.  Ange en ny installations-URL på fliken **Teknisk information.**
4.  Välj **Publicera om** du vill starta arbetsflödet om du vill publicera den nya appversionen i AppSource.


### <a name="update-the-offers-marketplace-metadata"></a>Uppdatera erbjudandets Marketplace-metadata

Marketplace-metadata innehåller ditt företagsnamn, logotyper och annan information. Så här uppdaterar du metadata:

1.  Logga in på [Molnpartnerportalen](https://cloudpartner.azure.com/).
2.  Under **Alla erbjudanden**hittar du det erbjudande du vill uppdatera.
3.  På fliken **Information om Store** ändrar du metadata genom att följa instruktionerna i artikeln Fliken Power [BI-app Butiksuppgifter](./cpp-storefront-details-tab.md).
4.  Välj **Publicera om** du vill starta arbetsflödet om du vill publicera ändringarna.


## <a name="the-compare-feature"></a>Funktionen Jämför

När du uppdaterar ett publicerat erbjudande kan du kontrollera de ändringar du har gjort. Så här använder du **funktionen Jämför:**

1.  Välj knappen Jämför för erbjudandet **när** som helst i redigeringsprocessen.

    ![Knappen Jämför](./media/compare-feature-button.png)

2.  Visa versioner av marknadsföringsresurser och metadata sida vid sida.


## <a name="history-of-publishing-actions"></a>Historia av publicerande handlingar

Om du vill visa erbjudandets publiceringshistorik öppnar du fliken **Historik** till vänster i Cloud Partner Portal. Här ser du en historik över tidsstämplade åtgärder på dina AppSource-erbjudanden.

## <a name="next-steps"></a>Nästa steg

I Cloud Partner Portal använder du regelbundet [Säljarstatistik](../../cloud-partner-portal-orig/si-getting-started.md) för att hitta användbar information om dina Marketplace-kunder och -användning.  
