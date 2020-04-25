---
title: Teknisk information för ett erbjudande om Power BI app | Azure Marketplace
description: Konfigurera tekniska informations fält för ett Power BI app-erbjudande för Microsoft AppSource Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: b012ac012c7de3c6428ecbe2d2b922a2a9aa0523
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2020
ms.locfileid: "82141779"
---
# <a name="power-bi-apps-technical-info-tab"></a>Fliken teknisk information för Power BI Apps

>[!Important]
>Från och med 13 april 2020 börjar vi flytta hanteringen av dina Power BI app-erbjudanden till Partner Center. Efter migreringen kommer du att skapa och hantera dina erbjudanden i Partner Center. Följ anvisningarna i [Power BI översikt över skapande av appar](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-power-bi-app-offer) för att hantera dina migrerade erbjudanden.

På sidan **nytt erbjudande** använder du fliken **teknisk information** för att tillhandahålla Power BI installations paketets URL och annan information som du behöver för att verifiera det nya erbjudandet.  För den första versionen är alla Power BI-appar kostnads fria och tillgängliga för nedladdning från AppSource. Därför kan du inte definiera lagerhållnings enheter (SKU: er) för den här erbjudande typen.

![Fliken teknisk information](./media/technical-info-tab.png)


## <a name="technical-info-fields"></a>Fält för teknisk information 

Fyll i fälten som beskrivs i följande tabell på fliken **teknisk information** . En asterisk (*) i slutet av en fält etikett innebär att fältet är obligatoriskt.

|        Field          |  Beskrivning                                                                 |
|    ---------------    |  ----------------------------------------------------------------------------|
| **Installations-URL\***     | Power BI skapar den här URL: en när du publicerar appen och marknadsför den till produktion.  Mer information finns i [publicera appar med instrument paneler och rapporter i Power BI](https://docs.microsoft.com/power-bi/service-create-distribute-apps).  |
|  **Verifierings anvisningar**  |  Om du vill kan du lägga till instruktioner (upp till 3 000 tecken) för att hjälpa Microsoft validerings teamet att konfigurera, ansluta och testa din app. Inkludera vanliga konfigurations inställningar, konton, parametrar eller annan information som kan användas för att testa alternativet Anslut data. Den här informationen visas bara för validerings teamet och används endast i validerings syfte.  |
| **Skapas den här appen som ett Power BI innehålls paket?** | För närvarande används det här fältet endast internt. Låt standardinställningen vara **Nej**. Om du ändrar inställningen till **Ja**kan du stoppa publicerings processen.  |  
|  |  |


## <a name="next-steps"></a>Nästa steg

På fliken [information om butik](./cpp-storefront-details-tab.md) anger du marknadsföring och juridisk information för din app.

