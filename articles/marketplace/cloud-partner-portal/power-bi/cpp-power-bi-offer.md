---
title: Erbjudande för Power BI app | Azure Marketplace
description: Publicera en Power BI-app på Microsoft AppSource Marketplace.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: pabutler
ms.openlocfilehash: d1974133562c0bb8b8006ea2ef323e6797643b67
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826078"
---
# <a name="power-bi-app-offer"></a>Erbjudande för Power BI-appen

|              |                                |
|--------------|--------------------------------|
| Den här artikeln beskriver hur du publicerar en Power BI-app på Microsoft [AppSource Marketplace](https://appsource.microsoft.com/).  En Power BI app-paket kan anpassas Power BI innehåll, inklusive data uppsättningar, rapporter och instrument paneler. Du kan sedan distribuera appen till andra Power BI klienter via AppSource, utföra de justeringar och anpassningar som tillåts av utvecklaren och ansluta den till dina egna data. | ![Power BI ikon](./media/powerbi-icon.png) |


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
