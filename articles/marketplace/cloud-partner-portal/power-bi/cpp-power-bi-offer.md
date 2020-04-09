---
title: Power BI App erbjudande | Azure Marketplace
description: Så här publicerar du en Power BI-app på Microsoft AppSource marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: dc433fb2ee7888a20af16e01d76d4678cf12b01b
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985583"
---
# <a name="power-bi-app-offer"></a>Erbjudande för Power BI-appen

>[!Important]
>Från och med den 13 april 2020 börjar vi flytta hanteringen av dina Power BI-appar till Partner Center. Efter migreringen skapar och hanterar du dina erbjudanden i Partner center. Följ instruktionerna i [översikten över hur du skapar Power BI-appar](https://aka.ms/AzurePBIAppCreationOverview) för att hantera dina migrerade erbjudanden.

|              |                                |
|--------------|--------------------------------|
| I den här artikeln beskrivs hur du publicerar en Power BI-app på [Microsoft AppSource marketplace](https://appsource.microsoft.com/).  Ett Power BI-appaket kan anpassas till Power BI-innehåll, inklusive datauppsättningar, rapporter och instrumentpaneler. Du kan sedan distribuera appen till andra Power BI-klienter via AppSource, utföra de justeringar och anpassningar som tillåts av utvecklaren och ansluta den till dina egna data. | ![Power BI-ikon](./media/powerbi-icon.png) |


Denna artikel är uppdelad i tre huvuddelar:

-   [Förutsättningar](./cpp-prerequisites.md). De tekniska kraven och affärskraven för att skapa och publicera ett Power BI App-erbjudande.
-   [Skapa ett Power BI-apperbjudande](./cpp-create-offer.md). Så här skapar du en Power BI-app-erbjudandepost med hjälp av [Cloud Partner Portal](https://cloudpartner.azure.com).
-   [Publicera ett Power BI-apperbjudande](./cpp-publish-offer.md). Så här skickar du ett nytt erbjudande till AppSource för publicering och hur du uppdaterar ett befintligt erbjudande.


## <a name="publishing-steps"></a>Publiceringssteg

Här är stegen på hög nivå för att publicera ett Power BI App-erbjudande:

![Power BI App erbjuder publiceringssteg](media/publishing-steps.png)

Här är Power BI App erbjudande publiceringsprocessen:

1. Skapa ett mallprogram i Power BI. Den här åtgärden genererar en url för paketinstallation, som representerar den primära tekniska tillgången för erbjudandet. Också vid denna tid, främja testpaketet till förproduktion. Mer information finns i [Vad är Power BI-mallappar?](https://docs.microsoft.com/power-bi/service-template-apps-overview). 
2. Samla in eller skapa erbjudandets marknadsföringsmaterial, inklusive: officiellt namn, beskrivning, logotyper, etc. 
3. Samla in eller skapa erbjudandets juridiska och supportdokument: *användarvillkor,* *sekretesspolicy,* *supportpolicy,* användarhjälp, etc.
4. Skapa erbjudandet: använd Cloud Partner Portal för att konfigurera information om erbjudandet, inklusive erbjudandebeskrivning, marknadsföringsmaterial, juridisk information, supportinformation och tillgångsspecifikationer.  När erbjudandet har angetts fullständigt skickar du det för publicering.
5. Övervaka publiceringsprocessen i Cloud Partner Portal.  Under det här steget testar, validerar och certifierar AppSource-introduktionsteamet ditt program. 
6. När appen har certifierats granskar du den i sin testmiljö och släpper den. 
7. Power BI-appen visas på AppSource (den "går live").
8. I Power BI marknadsför du förproduktionspaketet till produktion. Mer information finns i [Hantera versionen av mallappen](https://docs.microsoft.com/power-bi/service-template-apps-create#manage-the-template-app-release).


## <a name="next-steps"></a>Nästa steg

Innan du skapar ditt Power BI App-erbjudande och publicerar det på AppSource måste du uppfylla [kraven](./cpp-prerequisites.md) för att publicera en Power BI-app till AppSource.
