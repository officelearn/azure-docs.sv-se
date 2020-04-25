---
title: Erbjudande för Power BI app | Azure Marketplace
description: Publicera en Power BI-app på Microsoft AppSource Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: 1745868085a4ca573e2d6b5f78bea5f18b9e23c2
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2020
ms.locfileid: "82147701"
---
# <a name="power-bi-app-offer"></a>Erbjudande för Power BI-appen

>[!Important]
>Från och med 13 april 2020 börjar vi flytta hanteringen av dina Power BI app-erbjudanden till Partner Center. Efter migreringen kommer du att skapa och hantera dina erbjudanden i Partner Center. Följ anvisningarna i [Power BI översikt över skapande av appar](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-power-bi-app-offer) för att hantera dina migrerade erbjudanden.

|              |                                |
|--------------|--------------------------------|
| Den här artikeln beskriver hur du publicerar en Power BI-app på Microsoft [AppSource Marketplace](https://appsource.microsoft.com/).  En Power BI app-paket kan anpassas Power BI innehåll, inklusive data uppsättningar, rapporter och instrument paneler. Du kan sedan distribuera appen till andra Power BI klienter via AppSource, utföra de justeringar och anpassningar som tillåts av utvecklaren och ansluta den till dina egna data. | ![Power BI-ikon](./media/powerbi-icon.png) |


Den här artikeln är uppdelad i tre huvud delar:

-   [Krav](./cpp-prerequisites.md). De tekniska och affärs kraven för att skapa och publicera ett Power BI app-erbjudande.
-   [Skapa ett erbjudande för Power BI app](./cpp-create-offer.md). Så här skapar du en post för Power BI app-erbjudande med hjälp av [Cloud Partner Portal](https://cloudpartner.azure.com).
-   [Publicera ett erbjudande för Power BI app](./cpp-publish-offer.md). Så här skickar du ett nytt erbjudande till AppSource för publicering och hur du uppdaterar ett befintligt erbjudande.


## <a name="publishing-steps"></a>Publicerings steg

Här följer de övergripande stegen för att publicera ett Power BI app-erbjudande:

![Publicerings steg för Power BI app-erbjudandet](media/publishing-steps.png)

Här är publicerings processen för den Power BI appens erbjudande:

1. Skapa ett mall-program i Power BI. Den här åtgärden genererar en paket installations-URL, som representerar den primära tekniska till gången för erbjudandet. Nu kan du befordra test paketet till för produktion. Mer information finns i [Vad är Power BI Template Apps?](https://docs.microsoft.com/power-bi/service-template-apps-overview). 
2. Samla in eller skapa erbjudandets marknadsförings material, inklusive: officiellt namn, beskrivning, logo typer osv. 
3. Samla in eller skapa erbjudandena juridiska och stöd dokument: användnings *villkor*, *sekretess policy*, *support princip*, användar hjälp osv.
4. Skapa erbjudandet: Använd Cloud Partner Portal för att konfigurera information om erbjudandet, inklusive erbjudande beskrivning, marknadsförings material, juridisk information, supportinformation och till gångs uppgifter.  När erbjudandet har angetts fullständigt skickar du det för publicering.
5. Övervaka publicerings processen i Cloud Partner Portal.  Under det här steget testar AppSource onboarding-teamet, validerar och certifierar ditt program. 
6. När appen har certifierats, granskar du den i dess test miljö och släpper den. 
7. Power BI-appen visas på AppSource (den är Live).
8. I Power BI höjer du för produktions paketet till produktion. Mer information finns i [Hantera mallens app-version](https://docs.microsoft.com/power-bi/service-template-apps-create#manage-the-template-app-release).


## <a name="next-steps"></a>Nästa steg

Innan du skapar Power BI app-erbjudandet och publicerar det till AppSource måste du uppfylla [kraven](./cpp-prerequisites.md) för att publicera en Power BI-app till AppSource.
